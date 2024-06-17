# Deployment for jenkins from containers (Docker)

## Tools used
- Docker
- Jenkins

## Running
- Execute docker compose file with the next command:
```
    docker compose up -d
```

## Reverse proxy with NGINX
- To make it available from nginx we got create a server block on our configuration file where are our sites deployment
```
    nano /etc/nginx/sites-available/<file>
````
- Copy this code in nginx configuration file with the appropriate port established on docker compose file 
```
    upstream jenkins {
        keepalive 32; # keepalive connections
        server 127.0.0.1:8080; # jenkins ip and port
    }

    # Required for Jenkins websocket agents
        map $http_upgrade $connection_upgrade {
        default upgrade;
        '' close;
    }

    server {
        ...

        location /<context_path> { # Same as prefix value in docker compose file
            sendfile off;
            proxy_pass         http://jenkins;
            proxy_redirect     default;
            proxy_http_version 1.1;

            # Required for Jenkins websocket agents
            proxy_set_header   Connection        $connection_upgrade;
            proxy_set_header   Upgrade           $http_upgrade;

            proxy_set_header   Host              $http_host;
            proxy_set_header   X-Real-IP         $remote_addr;
            proxy_set_header   X-Forwarded-For   $proxy_add_x_forwarded_for;
            proxy_set_header   X-Forwarded-Proto $scheme;
            proxy_max_temp_file_size 0;

            #this is the maximum upload size
            client_max_body_size       10m;
            client_body_buffer_size    128k;

            proxy_connect_timeout      90;
            proxy_send_timeout         90;
            proxy_read_timeout         90;
            proxy_request_buffering    off; # Required for HTTP CLI commands
        }

    }

```