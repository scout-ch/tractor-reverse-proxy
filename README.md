# 🚜 Tractor Reverse Proxy

This repository is a setup to augment a Jelastic Docker native environment with a reverse proxy.

> [!NOTE]  
> This is also useful if you want the Portainer UI to be accessible via a subdomain, e.g. `portainer.yourdomain.com`. Check the docker labels in the `docker-compose.yml` file.

## Usage

Setup a new stack in Portainer, selecting a repository deployment with this repo.  
Then setup the following environment variables:
- `PORTAINER_HOSTNAME`: The hostname of the portainer instance, e.g. `portainer.yourdomain.com`
- `ACME_EMAIL`: The email address to use for Let's Encrypt certificate generation.

## Note about networks

The compose file in this repository creates a new network called `reverse-proxy`. You then need to join the portainer service to this network through the UI.

The `default` network is required to allow the proxy to obtain TLS certificates.

## Explain usage of webhooks vs polling

You can either set up the stack to poll for changes in the repository, or you can set up a webhook in the repository to trigger a new deployment. See the following links:
- https://docs.portainer.io/user/docker/stacks/webhooks
- https://docs.github.com/en/webhooks/using-webhooks/creating-webhooks (the default settings are fine. Portainer just needs the url to be called, it doesn't care about the payload)