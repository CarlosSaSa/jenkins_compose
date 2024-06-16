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
- Copy this code with the appropriate port established on docker compose file 
```
    location <site> {
        proxy_pass http://localhost:<port>;  # Change the port 
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
```