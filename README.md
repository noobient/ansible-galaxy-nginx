# noobient.nginx

## Synopsys

This role installs nginx and configures hosts.

## Parameters

| Name | Required | Example | Description |
|---|---|---|---|
| `domain` | yes | `foo.com` | Domain to host. |
| `mode` | yes | `wordpress` | Hosting mode. Possible values are `dirlist`, `php`, `proxy`, `redirect`, `static`, `wordpress`. |
| `path` | no | `/var/www/html/noobient.com` | Document root. Defaults to `/var/www/html/<domain>` for `php`, `static`, and `wordpress`, ignored otherwise. |
| `www_mode` | no | `redirect` | Possible values are `redirect` and `serve`, to redirect `www.<domain>` requests to `<domain>` or serve them as is, respectively. Defaults to `redirect`. Ignored when `mode` is set to `redirect`. |
| `new_domain` | no | `foobar.com` | New domain to redirect to. Mandatory for `redirect`, ignored otherwise. |
| `ssl_disabled` | no | `true` | `true` or `false`. Defaults to `false`. |
| `ssl_key` | no | `/etc/acme/noobient.com/noobient.com.key` | Full path to SSL key file. Defaults to `/etc/acme/<domain>/<domain>.key`. Ignored if `ssl_disabled` is `true`. |
| `ssl_cert` | no | `/etc/acme/noobient.com/fullchain.cer` | Full path to SSL full chain file. Defaults to `/etc/acme/<domain>/fulllchain.cer`. Ignored if `ssl_disabled` is `true`. |
| `host_port` | no | `8888` | Listen on custom port. Defaults to `80` when `ssl_disabled` is `true`, or `443` otherwise. |
| `proxy_port` | no | `9999` | Port of the app being proxied, when `mode` is set to `proxy`, ignored otherwise. Defaults to `8080`. |

## Examples

```yml
# Barebones
- include_role:
    name: noobient.nginx
  vars:
    domain: foo.com
    mode: wordpress

# Serve static pages both on foo.com and www.foo.com, with custom content path
- include_role:
    name: noobient.nginx
  vars:
    domain: foo.com
    mode: static
    path: /data/content/foo.com
    www_mode: serve

# Redirect foo.com and www.foo.com to bar.com without SSL
- include_role:
    name: noobient.nginx
  vars:
    domain: foo.com
    mode: redirect
    new_domain: bar.com
    ssl_disabled: true

# Proxy the app running on port 9999, use custom SSL paths, and serve on port 8888
- include_role:
    name: noobient.nginx
  vars:
    domain: foo.com
    mode: proxy
    ssl_key: /opt/acme/privkey.pem
    ssl_cert: /opt/acme/fullchain.pem
    host_port: 8888
    proxy_port: 9999
```

## Return Values

N/A

## Support

| Platform | Support | Status |
|---|---|---|
| Linter | ✅ | [![Lint](https://github.com/noobient/ansible-galaxy-nginx/actions/workflows/lint.yml/badge.svg)](https://github.com/noobient/ansible-galaxy-nginx/actions/workflows/lint.yml) |
| AlmaLinux 8 | ✅ | [![AlmaLinux 8](https://github.com/noobient/ansible-galaxy-nginx/actions/workflows/almalinux-8.yml/badge.svg)](https://github.com/noobient/ansible-galaxy-nginx/actions/workflows/almalinux-8.yml) |
| AlmaLinux 9 | ✅ | [![AlmaLinux 9](https://github.com/noobient/ansible-galaxy-nginx/actions/workflows/almalinux-9.yml/badge.svg)](https://github.com/noobient/ansible-galaxy-nginx/actions/workflows/almalinux-9.yml) |
| Fedora 38 | ✅ | [![Fedora 38](https://github.com/noobient/ansible-galaxy-nginx/actions/workflows/fedora-38.yml/badge.svg)](https://github.com/noobient/ansible-galaxy-nginx/actions/workflows/fedora-38.yml) |
| Fedora 39 | ✅ | [![Fedora 39](https://github.com/noobient/ansible-galaxy-nginx/actions/workflows/fedora-39.yml/badge.svg)](https://github.com/noobient/ansible-galaxy-nginx/actions/workflows/fedora-39.yml) |
| Ubuntu 18.04 | ✅ | [![Ubuntu 18.04](https://github.com/noobient/ansible-galaxy-nginx/actions/workflows/ubuntu-18.04.yml/badge.svg)](https://github.com/noobient/ansible-galaxy-nginx/actions/workflows/ubuntu-18.04.yml) |
| Ubuntu 20.04 | ✅ | [![Ubuntu 20.04](https://github.com/noobient/ansible-galaxy-nginx/actions/workflows/ubuntu-20.04.yml/badge.svg)](https://github.com/noobient/ansible-galaxy-nginx/actions/workflows/ubuntu-20.04.yml) |
| Ubuntu 22.04 | ✅ | [![Ubuntu 22.04](https://github.com/noobient/ansible-galaxy-nginx/actions/workflows/ubuntu-22.04.yml/badge.svg)](https://github.com/noobient/ansible-galaxy-nginx/actions/workflows/ubuntu-22.04.yml) |
