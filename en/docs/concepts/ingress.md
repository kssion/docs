# Ingress

!!! tip "Dynamic configuration"
    Ingress supports dynamic configuration via [Web API](/en/tutorials/api/overview/).

!!! note "Limitation"
    Ingress currently can only be used in [reverse proxy](/en/tutorials/reverse-proxy-advanced/).

Ingress consists of a set of rules, each rule is a mapping from a hostname to a service endpoint, and the entry point traffic is routed and load-balanced through Ingress in the reverse proxy.

The hostname in the rule also supports domain name wildcards, and the service endpoint must be a legal UUID.

## Wildcard Hostname

The hostname in the Ingress rule supports the wildcard format starting with `.` or `*`.

For example `.example.org` or `*.example.org` matches example.org, and subdomains like abc.example.org，def.abc.example.org, etc.

When querying, it will first look for the exact match, if not found, then look for the wildcard item, if not found again, then look for the upper-level domain name wildcard in turn.

For example: abc.example.org, the mapping value of abc.example.org will be searched first (exact match), if not found, the .abc.example.org wildcard item will be searched, and if not, the .example.org and .org wildcard items will be searched in turn.

## Data Source

Ingress can configure multiple data sources, currently supported data sources are: inline, file, redis.

#### Inline

An inline data source means setting the data directly in the configuration file via the `rules` options.

```yaml
ingresses:
- name: ingress-0
  rules:
  - hostname: example.com
    endpoint: 4d21094e-b74c-4916-86c1-d9fa36ea677b
  - hostname: example.org
    endpoint: ac74d9dd-3125-442a-a7c1-f9e49e05faca
```

### File

Specify an external file as the data source. Specify the file path via the `file.path` property.

```yaml
ingresses:
- name: ingress-0
  file:
    path: /path/to/file
```

The file format is mapping items separated by lines, each line is an hostname-endpoint pair separated by spaces, and the part starting with `#` is the comment information.

```text
# hostname endpoint

example.com  4d21094e-b74c-4916-86c1-d9fa36ea677b
example.org  ac74d9dd-3125-442a-a7c1-f9e49e05faca
```

### Redis

Specify the redis service as the data source, and the redis data type can be [Hash](https://redis.io/docs/data-types/hashes/) or [Set](https://redis.io/docs/data-types/sets/).

```yaml
ingresses:
- name: ingress-0
  redis:
    addr: 127.0.0.1:6379
	db: 1
	password: 123456
	key: gost:ingresses:ingress-0
	type: hash
```

`addr` (string, required)
:    redis addr.

`db` (int, default=0)
:    database name.

`password` (string)
:    password.

`key` (string, default=gost)
:    redis key.

`type` (string, default=hash)
:    data type: `hash` or `set`.

```redis
> HGETALL gost:ingresses:ingress-0
1) "example.com"
2) "4d21094e-b74c-4916-86c1-d9fa36ea677b"
3) "example.org"
4) "ac74d9dd-3125-442a-a7c1-f9e49e05faca"
```

### HTTP

指定HTTP服务作为数据源。对于所请求的URL，HTTP返回200状态码则认为有效，返回的数据格式与文件数据源相同。
Specify an HTTP service as the data source. For the requested URL, if the HTTP status code is 200, it is considered valid, and the returned data format is the same as that of the file data source.

```yaml
ingresses:
- name: ingress-0
  http:
    url: http://127.0.0.1:8000
    timeout: 10s
```

`url` (string, required)
:    request URL.

`timeout` (duration, default=0)
:    request timeout.

## Priority

When configuring multiple data sources at the same time, the priority from high to low is: HTTP, redis, file, inline.

## Hot Reload

File, redis, HTTP data sources support hot reloading. Enable hot loading by setting the `reload` option, which specifies the period for synchronizing the data source data.

```yaml hl_lines="3"
ingresses:
- name: ingress-0
  reload: 10s
  file:
    path: /path/to/file
  redis:
    addr: 127.0.0.1:6379
	db: 1
	password: 123456
	key: gost:ingresses:ingress-0
  http:
    url: http://127.0.0.1:8000
    timeout: 10s
```