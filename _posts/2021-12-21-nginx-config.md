---
title: nginx config
author: ianpowell
date: 2021-12-21 13:40:00 +0000
categories: [linux]
tags: [nginx]
---

## Nginx.conf

```nginx
# For more information on configuration, see:
#   * Official English Documentation: http://nginx.org/en/docs/
#   * Official Russian Documentation: http://nginx.org/ru/docs/
user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log;
pid /run/nginx.pid;
# Load dynamic modules. See /usr/share/doc/nginx/README.dynamic.
include /usr/share/nginx/modules/*.conf;
events {
    worker_connections 1024;
}
http {
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
    access_log  /var/log/nginx/access.log  main;
    error_log  /var/log/nginx/error.log;
    sendfile            on;
    tcp_nopush          on;
    tcp_nodelay         on;
    keepalive_timeout   65;
    types_hash_max_size 2048;
    include             /etc/nginx/mime.types;
    default_type        application/octet-stream;
    # Load modular configuration files from the /etc/nginx/conf.d directory.
    # See http://nginx.org/en/docs/ngx_core_module.html#include
    # for more information.
    include /etc/nginx/conf.d/*.conf;
    include /nas/nginx/sites-available/*.conf;
}
```

## Default.site.conf
```nginx
    server {
        listen       80 default_server;
        listen       [::]:80 default_server;
        server_name  {server domain name};
        root         /usr/share/nginx/html;
        rewrite      ^\/redirect\/mobile-app$ https://a.app.qq.com/o/simple.jsp?pkgname=package.names redirect;
        rewrite      ^\/redirect\/hs241-install-video$ /video-library redirect;
        add_header   X-XSS-Protection '1; mode=block';
        add_header   X-Content-Type-Options 'nosniff';
        add_header   Strict-Transport-Security 'max-age=31536000; includeSubDomains';
        add_header   X-Frame-Options 'SAMEORIGIN';
        add_header   Report-To '{"group":"default","max_age":10886400,"endpoints":[{"url":"https://7pz13sb7.uriports.com/reports"}],"include_subdomains":true}';
        add_header   NEL '{"report_to":"default","max_age":2592000,"include_subdomains":true,"failure_fraction":1.0}';
        add_header   Content-Security-Policy "default-src 'self'; script-src 'self' 'unsafe-inline' 'unsafe-eval' assets.server.com js.hs-scripts.com js.hsforms.net forms.hsforms.com hm.baidu.com js.hscollectedforms.net js.usemessages.com js.hs-banner.com js.hs-analytics.net js.hsleadflows.net *.alipayobjects.com https://*.hotjar.com https://*.hotjar.io *.baidu.com www.googletagmanager.com www.google-analytics.com fe-resource.cdn.bcebos.com ; script-src-elem 'self' 'unsafe-inline' 'unsafe-hashes' 'unsafe-eval' assets.server.com js.hs-scripts.com js.hsleadflows.net *.baidu.com sofire.bdstatic.com js.hscollectedforms.net js.usemessages.com *.hs-analytics.net js.hs-banner.com js.hsforms.net forms.hsforms.com static.hotjar.com script.hotjar.com www.googletagmanager.com *.google-analytics.com www.google.com www.gstatic.com fe-resource.cdn.bcebos.com; script-src-attr 'self' 'unsafe-inline' 'unsafe-eval'; style-src 'self' 'unsafe-inline' assets.server.com fonts.googleapis.com; style-src-elem 'self' fonts.googleapis.com 'unsafe-inline' assets.server.com sgoutong.baidu.com; style-src-attr 'self' 'unsafe-inline'; img-src 'self' data: *.baidu.com *.hubspot.com forms.hsforms.com f.hubspotusercontent00.net android-webview-video-poster: f.hubspotusercontent10.net *.server.com https://*.hotjar.com https://*.hotjar.io www.google-analytics.com www.googletagmanager.com www.gstatic.com exceptions.hs-embed-reporting.com; font-src 'self' fonts.gstatic.com *.server.com https://*.hotjar.com https://*.hotjar.io; connect-src 'self' *.hubspot.com *.baidu.com forms.hsforms.com https://*.hotjar.com:* https://*.hotjar.io wss://*.hotjar.com www.google-analytics.com videojet.fact-finder.co.uk; media-src 'self' vod.server.com; object-src 'self'; child-src; frame-src 'self' forms.hsforms.com *.hubspot.com https://*.hotjar.com https://*.hotjar.io www.google.com; worker-src; frame-ancestors 'self' app.hubspot.com *.baidu.com; form-action 'self' http://*.server.com forms.hsforms.com forms.hubspot.com *.alipay.com *.alipaydev.com; base-uri 'self'; manifest-src 'self'; report-uri https://7pz13sb7.uriports.com/reports/enforce; report-to default";
        
        # add_header   Content-Security-Policy-Report-Only "default-src 'self'; script-src 'self' 'unsafe-inline' 'unsafe-eval' assets.server.com js.hs-scripts.com js.hsforms.net forms.hsforms.com hm.baidu.com js.hscollectedforms.net js.usemessages.com js.hs-banner.com js.hs-analytics.net *.alipayobjects.com www.google-analytics.com js.hsleadflows.net; script-src-elem 'self' 'unsafe-inline' 'unsafe-hashes' 'unsafe-eval' assets.server.com js.hs-scripts.com hm.baidu.com js.hscollectedforms.net js.usemessages.com *.hs-analytics.net js.hs-banner.com js.hsforms.net forms.hsforms.com; script-src-attr 'self' 'unsafe-inline' 'unsafe-eval'; style-src 'self' 'unsafe-inline' assets.server.com fonts.googleapis.com; style-src-elem 'self' 'unsafe-inline' assets.server.com; style-src-attr 'self' 'unsafe-inline'; img-src 'self' assets.server.com data: hm.baidu.com *.hubspot.com forms.hsforms.com android-webview-video-poster: f.hubspotusercontent10.net sgoutong.baidu.com; font-src 'self' assets.server.com; connect-src 'self' *.hubspot.com hm.baidu.com forms.hsforms.com; media-src 'self' vod.server.com; object-src 'self'; prefetch-src 'self'; child-src; frame-src 'self' forms.hsforms.com app.hubspot.com; worker-src; frame-ancestors 'self'; form-action 'self' http://www.server.com forms.hsforms.com openapi.alipay.com mclient.alipay.com; base-uri 'self'; manifest-src 'self'; plugin-types application/pdf; report-uri https://7pz13sb7.uriports.com/reports/report; report-to default";
        add_header   Expect-CT 'max-age=86400,enforce,report-uri="https://7pz13sb7.uriports.com/reports/enforce"';
        add_header   Referrer-Policy 'strict-origin-when-cross-origin';
        add_header   X-Permitted-Cross-Domain-Policies 'none';
        # Increase max file upload to limit of kestrel
        client_max_body_size 28M;
        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;
        location / {
            proxy_pass      http://localhost:5001;
            proxy_http_version  1.1;
            proxy_set_header    Upgrade             $http_upgrade;
            proxy_set_header    Connection          keep-alive;
            proxy_set_header    Host $host;            
            proxy_set_header    X-Real-IP           $remote_addr;
            proxy_set_header    X-Forwarded-For     $http_x_forwarded_for;
            proxy_set_header    X-Forwarded-Proto   $http_x_forwarded_proto;
            proxy_cache_bypass  $http_upgrade;
            proxy_buffering     off;
        }
        error_page 404 /404.html;
            location = /40x.html {
        }
        error_page 500 502 503 504 /50x.html;
            location = /50x.html {
        }
    }
# Settings for a TLS enabled server.
# ----------------------------------------------------------------------
# SSL Needed to run scheduled tasks - Do not remove or comment out 
# ----------------------------------------------------------------------
    server {
        listen       443 ssl http2 default_server;
        listen       [::]:443 ssl http2 default_server;
        server_name  {server domain name};
        root         /usr/share/nginx/html;
        ssl_certificate           "/etc/pki/nginx/server.crt";
        ssl_certificate_key       "/etc/pki/nginx/STAR_server_com.key";
        ssl_protocols             TLSv1.1 TLSv1.2;
        ssl_prefer_server_ciphers on;
        ssl_ciphers               "EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH";
        ssl_ecdh_curve            secp384r1;
        ssl_session_cache         shared:SSL:10m;
        ssl_session_tickets       off;
        add_header X-Frame-Options DENY;
        add_header X-Content-Type-Options nosniff;
        add_header X-Frame-Options "SAMEORIGIN";
        
        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;
        location / {
            proxy_pass      http://localhost:5001;
            proxy_http_version  1.1;
            proxy_set_header    Upgrade         $http_upgrade;
            proxy_set_header    Connection      keep-alive;
            proxy_set_header    Host            $host;
            proxy_set_header    X-Real-IP       $remote_addr;
            proxy_set_header    X-Forwarded-For     $proxy_add_x_forwarded_for;
            proxy_set_header    X-Forwarded-Proto   $scheme;
            proxy_set_header    X-Forwarded-Host    $host;
            proxy_set_header    X-Forwarded-Port    $server_port;
            proxy_cache_bypass  $http_upgrade;
        }
        error_page 404 /404.html;
            location = /40x.html {
        }
        error_page 500 502 503 504 /50x.html;
            location = /50x.html {
        }
    }
```