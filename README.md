# 🚜 Tractor Reverse Proxy

This repository is a setup to augment a Jelastic Docker native environment with a reverse proxy.

> [!NOTE]  
> This is also useful if you want the Portainer UI to be accessible via a subdomain, e.g. `portainer.yourdomain.com`. Check the docker labels in the `docker-compose.yml` file.

## Usage

1. Create a new network called `reverse-proxy` in the Portainer UI. Under Advanced configuration, set it to be an "isolated network".
2. Setup a new stack in Portainer, selecting a repository deployment with this repo.  
    Setup the following environment variables:
    - `PORTAINER_HOSTNAME`: The hostname of the portainer instance, e.g. `portainer.yourdomain.com`
    - `ACME_EMAIL`: The email address to use for Let's Encrypt certificate generation.
3. Join the portainer container to the reverse-proxy network through the UI.

> [!TIP]  
> The `default` network is required to allow the proxy to obtain TLS certificates, as the reverse-proxy network is isolated.  
> Accessing the proxy, happens through the mapped ports that are exposed in the `docker-compose.yml` file.

## Webhooks vs polling

You can either set up the stack to poll for changes in the repository, or you can set up a webhook in the repository to trigger a new deployment. See the following links:
- https://docs.portainer.io/user/docker/stacks/webhooks
- https://docs.github.com/en/webhooks/using-webhooks/creating-webhooks (the default settings are fine. Portainer just needs the url to be called, it doesn't care about the payload)

Webhooks are more efficient, but required edit access to the repository.  
Polling is easier to set up, but is less efficient and has a delay. You can always trigger a pull manually in the Portainer UI though.

## Exposing services

1. Set up DNS. It is important that the domain is ready before first deploying the service, as Let's Encrypt will try to verify the domain.
    DNS setup varies based on your domain and DNS provider. Generally you will need to create a CNAME record pointing to the portainer instance. In case of tractor this is: `portainer.tractor.scout.ch`.
2. Add the following labels to the service in the `docker-compose.yml` file or in the Portainer UI:
    ```yaml
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.my-service.rule=Host(`my-service.yourdomain.com`)"
      - "traefik.http.routers.my-service.entrypoints=https"
      - "traefik.http.services.my-service.loadbalancer.server.port=80"
    ```
    Make sure to replace `my-service` with the name of your service and `my-service.yourdomain.com` with your domain, as well as specifying the correct port.
3. The last step is to add the container to the `reverse-proxy` network. Depending on how you set up your service, you do that in the `docker-compose.yml` file or in the Portainer UI.
    In the docker compose file, add the following to the service:
    ```yaml
    networks:
      - reverse-proxy
      - default # optional if you don't need outgoing internet access for the service
    ```
    In the Portainer UI, you can add the container to the network in the container settings.
    > [!WARNING]  
    > This does not work currently if you are not an admin in the Portainer instance.

