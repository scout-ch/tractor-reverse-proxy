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