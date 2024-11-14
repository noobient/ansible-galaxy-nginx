# noobient.nginx

## Synopsys

This role installs nginx and configures hosts.

## Parameters

| Name | Required | Example | Description |
|---|---|---|---|
| `nginx_batch` | no | `<list>` | Supply the below parameters as a list, see examples. |
| `domain` | yes | `foo.com` | Domain to host. |
| `mode` | yes | `wordpress` | Hosting mode. Possible values are `dirlist`, `php`, `proxy`, `redirect`, `static`, `wordpress`. |
| `path` | no | `/var/www/html/noobient.com` | Document root. Defaults to `/var/www/html/<domain>` for `php`, `static`, and `wordpress`, ignored otherwise. |
| `www_mode` | no | `redirect` | Possible values are `redirect` and `serve`, to redirect `www.<domain>` requests to `<domain>` or serve them as is, respectively. Defaults to `redirect`. Ignored when `mode` is set to `redirect`. |
| `new_domain` | no | `foobar.com` | New domain to redirect to. Mandatory for `redirect`, ignored otherwise. |
| `ssl_selfsigned` | no | `true` | `true` or `false`. If `true`, generates a self-signed certificate with 1 year validity. Defaults to `false`. |
| `ssl_key` | no | `/etc/acme/noobient.com/noobient.com.key` | Full path to SSL key file. Defaults to `/etc/acme/<domain>/<domain>.key`. Ignored if `ssl_selfsigned` is `true`. |
| `ssl_cert` | no | `/etc/acme/noobient.com/fullchain.cer` | Full path to SSL full chain file. Defaults to `/etc/acme/<domain>/fulllchain.cer`. Ignored if `ssl_selfsigned` is `true`. |
| `host_port` | no | `8888` | Listen on custom port. Defaults to `443`. |
| `proxy_port` | no | `9999` | Port of the app being proxied, when `mode` is set to `proxy`, ignored otherwise. Defaults to `8080`. |

## Examples

### Single Mode

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
    ssl_selfsigned: true

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

### Batch Mode

Use in tasks is straightforward, just pass the same arguments as a list:

```yml
- include_role:
    name: noobient.nginx
  vars:
    nginx_batch:
      - { domain: foo.com, mode: static, path: /data/content/foo.com }
      - { domain: bar.com, mode: php, www_mode: serve }
```

Use via playbooks is also possible:

```yml
# hosts
[webservers]
websrv1.contoso.com
websrv2.contoso.com

# host_vars/websrv1.contoso.com.yml:
nginx_batch:
  - { domain: foo1.com, mode: static, path: /data/content/foo1.com }
  - { domain: bar1.com, mode: php, www_mode: serve }

# host_vars/websrv2.contoso.com.yml:
# Yes, a list can also contain just 1 element
nginx_batch:
  - { domain: bar2.com, mode: wordpress, host_port: 4567 }

# webstuff.yml
- hosts: webservers
  become: yes
  roles:
    - your_own_role
    - your_other_own_role
    - noobient.nginx
```

For whatever reason, you might want to use a different variable name internally.
In this case, simply map your variable in your playbook. Note that in Ansible,
when you pass variables to roles in playbooks, you also need to prepend the role
name with `role: `. You can use any internal variable name you want, and define
the passed variable wherever you see fit, e.g., `group_vars`, `host_vars`, etc.
All you have to ensure is you pass it to the role as `nginx_batch`.

```yml
# host_vars/websrv1.contoso.com.yml:
random_variable_name:
  - { domain: foo1.com, mode: static, path: /data/content/foo1.com }
  - { domain: bar1.com, mode: php, www_mode: serve }

# host_vars/websrv2.contoso.com.yml:
random_variable_name:
  - { domain: bar2.com, mode: wordpress, host_port: 4567 }

# webstuff.yml
- hosts: webservers
  become: yes
  roles:
    - your_own_role
    - your_other_own_role
    - role: noobient.nginx
      nginx_batch: "{{ random_variable_name }}"
```

## Return Values

N/A

## Support

| Platform | Support | Status |
|---|---|---|
| Linter | ✅ | [![Lint](https://github.com/noobient/ansible-galaxy-nginx/actions/workflows/lint.yml/badge.svg)](https://github.com/noobient/ansible-galaxy-nginx/actions/workflows/lint.yml) |
| AlmaLinux 8 | ✅ | [![AlmaLinux 8](https://github.com/noobient/ansible-galaxy-nginx/actions/workflows/almalinux-8.yml/badge.svg)](https://github.com/noobient/ansible-galaxy-nginx/actions/workflows/almalinux-8.yml) |
| AlmaLinux 9 | ✅ | [![AlmaLinux 9](https://github.com/noobient/ansible-galaxy-nginx/actions/workflows/almalinux-9.yml/badge.svg)](https://github.com/noobient/ansible-galaxy-nginx/actions/workflows/almalinux-9.yml) |
| Fedora 40 | ✅ | [![Fedora 40](https://github.com/noobient/ansible-galaxy-nginx/actions/workflows/fedora-40.yml/badge.svg)](https://github.com/noobient/ansible-galaxy-nginx/actions/workflows/fedora-40.yml) |
| Fedora 41 | ✅ | [![Fedora 41](https://github.com/noobient/ansible-galaxy-nginx/actions/workflows/fedora-41.yml/badge.svg)](https://github.com/noobient/ansible-galaxy-nginx/actions/workflows/fedora-41.yml) |
| Ubuntu 20.04 | ✅ | [![Ubuntu 20.04](https://github.com/noobient/ansible-galaxy-nginx/actions/workflows/ubuntu-20.04.yml/badge.svg)](https://github.com/noobient/ansible-galaxy-nginx/actions/workflows/ubuntu-20.04.yml) |
| Ubuntu 22.04 | ✅ | [![Ubuntu 22.04](https://github.com/noobient/ansible-galaxy-nginx/actions/workflows/ubuntu-22.04.yml/badge.svg)](https://github.com/noobient/ansible-galaxy-nginx/actions/workflows/ubuntu-22.04.yml) |
| Ubuntu 24.04 | ✅ | [![Ubuntu 24.04](https://github.com/noobient/ansible-galaxy-nginx/actions/workflows/ubuntu-24.04.yml/badge.svg)](https://github.com/noobient/ansible-galaxy-nginx/actions/workflows/ubuntu-24.04.yml) |
