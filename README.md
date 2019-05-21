ansible-nginx-role
=========

Role to manage nginx and vhosts for Debian.

Requirements
------------

None

ToDo's
------------

- Check existence of vhost.ssl certificates before applying vhost configuration, as this leads to nginx not able to start.

Role Variables
--------------
```YAML
##
# Nginx setup configuration
##

# Nginx package should be installed
nginx_enabled: true

# Latest will install and update to latest versions, present will not update
nginx_packages_mode: latest # present|latest

nginx_worker_processes: auto
nginx_pid: /run/nginx.pid
nginx_modules_dir: /etc/nginx/modules-enabled

nginx_events_worker_connections: 1024
nginx_multi_accept: "off"

#
# Nginx basic settings
#
nginx_http_sendfile: 'on'
nginx_http_tcp_nopush: 'on'
nginx_http_tcp_nodelay: 'on'
nginx_http_keepalive_timeout: 65
nginx_http_types_hash_max_size: 2048

nginx_http_server_tokens: 'on'
nginx_http_server_names_hash_bucket_size: 64
nginx_http_server_name_in_redirect: 'on'

nginx_http_default_type: application/octet-stream

#
# Nginx ssl settings
#
nginx_http_ssl_protocols: 'TLSv1 TLSv1.1 TLSv1.2'
nginx_http_ssl_prefer_server_ciphers: 'on'
nginx_http_ssl_ciphers: 'AES128+EECDH:AES128+EDH:AES256+EECDH:AES256+EDH:!aNULL'
nginx_http_ssl_session_cache: 'shared:SSL:10m'
nginx_http_ssl_stapling: 'on'
nginx_http_resolver: 8.8.4.4 8.8.8.8 valid=300s
nginx_resolver_timeout: 10s

#
# Nginx logging settings
#
nginx_http_access_log: /var/log/nginx/access.log
nginx_http_error_log: /var/log/nginx/error.log

#
# Nginx gzip settings
#
nginx_http_gzip: 'on'
nginx_http_gzip_disable: '"msie6"'

#
# Nginx basic configuration
#

# Example for extra options prior to the http block of the nginx.conf
nginx_extra_conf_options: |
  env VARIABLE;
  include /etc/nginx/main.d/*.conf;

# Example for extra options near the end of the http block of the nginx.conf
nginx_extra_http_options: |
  #
  # Additional Settings
  #
  some_addition_stuff withSomeValue

#
# Nginx vhost configuration
#
nginx_manage_vhosts: false
nginx_clear_vhosts_first: false

# Removal of the default vhost shipped with nginx
nginx_remove_default_vhost: false

#Example for managed vhost list
nginx_vhosts:
  - example_project:
      filename: example.conf
      state: present # absent
      
      # Redirection to https
      redirect:
        listen: 1.2.3.4:80
        server_name: example.tld
        target: https://example.tld
      
      # Basic setup
      listen: 1.2.3.4:443
      server_name: abc.tld
      access_log: /var/log/nginx/access.tld.example.log
      error_log: /var/log/nginx/error.tld.example.log
      root: /home/user/project/public
      try_files: '$uri /$uri /index.php$is_args$args'
      index: index.php
      
      # SSL configuration
      ssl:
        enabled: 'off'
        cert: /etc/letsencrypt/exmple.tld/fullchain.pem
        key: /etc/letsencrypt/exmple.tld/privkey.pem
      
      # Locations
      locations:
        - default:
            location: \
            try_files: $uri /$uri /index.php$is_args$args;
        - project:
            location: '~ \.php$'
            fastcgi:
              pass: unix:/var/run/php.sock
              index: index.php
              include_conf: fastcgi.conf;
            additional_conf: |
              fastcgi_read_timeout 3600;
              fastcgi_param APPLICATION_ENV development;
              
      # Addition configuration
      additional_conf: ""
```

Dependencies
------------

None

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: servers
      roles:
         - { role: methorz.ansible-nginx-role }

License
-------

BSD

Author Information
------------------

https://twitter.com/methor_z
