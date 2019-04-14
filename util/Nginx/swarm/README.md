# Nginx swarm variation

This variation assumes you have another proxy in front of nginx.
Nginx will be used to apply some settings like mime types, and headers for browser security.
However you are responsible for load balancing and HTTPS.

Additionally, no entrypoint.sh is used here.
Nginx has been tested extensively at being capable of running as root and dropping to user level for requests.

## Usage

```
# Deployable as a docker swarm stack
version: '3.4'
services:
  nginx:
    image: bitwarden/nginx-swarm:latest
    healthcheck:
      # This assumes you're sticking to port 8080 in the vhost config.
      test: wget --quiet --tries=1 --spider http://localhost:8080/ || exit 1
      interval: 15s
      timeout: 11s
      retries: 3
    configs:
      # Configs are easier to scale and update in swarms compared to sharing volumes.
      - source: bitwarden-nginx-default.1.conf
        target: /etc/nginx/conf.d/default.conf
    deploy:
      # It's OK to scale this as you like.
      mode: replicated
      replicas: 1
      restart_policy:
        condition: any
        window: 5s
        delay: 5s
      update_config:
        # Start first, because these run fine concurrently.
        order: start-first

  # The remaining bitwarden services...
```

From there on, the port you've defined in your `default.conf` can be used by your next load balancing application.
