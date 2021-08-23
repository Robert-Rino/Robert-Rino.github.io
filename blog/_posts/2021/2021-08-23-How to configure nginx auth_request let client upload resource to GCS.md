---
layout: post
title: "How to configure nginx auth_request let client upload resource to GCS."
excerpt:
last_modified_at: 2021-08-23
tags:
  - GCS
  - nginx
---

# Goal

Client can upload file like `PUT /api/picture` directly to GCS, the blob object never go through API server, it only provide authorization check.

# Pre-requests
- GCS Signed URL : Make us possible to dynamically make client has temporary access to upload file to GCS.
- API server: Make authorization check and generate signed url for upload.
- Nginx Frontend load balancer: To route client upload request directly to GCS signed url.

---

You would need configure your nginx.conf like below, so that you can simply

`curl :8000/users/user_abc.jpg --data-binary @data/avatar.jpg` to GCS.

```nginx
http {
  upstream api {
    server                    web:8000;
    keepalive                 4;
  }
upstream storage.googleapis.com {
    server                    storage.googleapis.com:443;
    keepalive                 4;
  }
server {
    listen                    80;
include                   mime.types;
    default_type              application/octet-stream;
location = .api {
      internal;
      proxy_pass                http://api$request_uri;
      proxy_method              $request_method;
      proxy_pass_request_body   off;
      proxy_set_header          Content-Length "";
    }
location ~* '^/users/(?<user_id>[\w]+)\.(?<format>(jpg))$' {
      if ( $request_method = "PUT" ) {
        rewrite ^(.*)$ PUT|users/$user_id/$item last;
      }
proxy_pass http://api;
    }
location ~* '^PUT|/users/(?<user_id>[\w]+)\.(?<format>(jpg))$' {
      internal;
      resolver             8.8.8.8 127.0.0.11;
auth_request         .api;
      auth_request_set     $upload_url $upstream_http_location;
proxy_pass           $upload_url;
      proxy_set_header     Content-Type image/jpeg;
    }
  }
}
```