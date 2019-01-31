# Varnish Docker images for Docksal

- Varnish 4.1
- Varnish 5.2

This image(s) is part of the [Docksal](http://docksal.io) image library.


## Features

- BigPipe support


## Environmental variables

- `VARNISH_PORT` - port Varnish binds to, default: `80`
- `VARNISH_BACKEND_HOST` - backed-end IP/host, default: `web`
- `VARNISH_BACKEND_PORT` - backed-end port, default: `80`
- `VARNISH_CACHE_SIZE` - cache size, default: `64M`
- `VARNISH_VARNISHD_PARAMS` - extra parameters for `varnishd`.
- `VARNISH_VARNISHNCSA_PARAMS` - parameters for `varnishncsa` (logging).
- `VARNISH_SECRET` - allow the secret to be set for varnish.
- `VARNISH_CACHE_TAGS_HEADER` - Varnish cache header name for BAN's, default: `Cache-Tags`


## Cache flushing

### PURGE

Cache for a specific URL can be flushed using the `PURGE` HTTP method, example:

```
curl -X PURGE http://varnish.tests.docksal/node/1
```

### BAN

Cache for a group of URLs can be flushed using the `BAN` HTTP method by passing a list of tags via a specific header.

Using `BAN` requires the application to first tag all responses with cache tags.
These tags are then used to ban pages from Varnish cache (usually handled by the application using a module/library).

By default, `Cache-Tags` is used as the header to pass cache tags.
The header name can be overriden via the `VARNISH_CACHE_TAGS_HEADER` environment variable.

Depending your application environment `VARNISH_CACHE_TAGS_HEADER` may need to be set to:

- `Purge-Cache-Tags` for use with Drupal's [Purge module](https://www.drupal.org/project/purge)
- `X-Acquia-Purge-Tags` for use with Drupal's [Acquia Purge module](https://www.drupal.org/project/acquia_purge)
- `X-Cache-Tags` for [Symfony FOSHttpCache](https://foshttpcache.readthedocs.io/en/stable/response-tagging.html#tags)

Using `BAN` to manually flush cache by tag:

```
curl -X BAN http://varnish.tests.docksal/ -H "Cache-Tags: node:1"
```


## VCL

To provide a custom VCL config mount it at `/opt/default.vcl`.

When used with Docksal, custom VCL is automatically loaded from `.docksal/etc/varnish/default.vcl` if one exists 
in the project codebase.
