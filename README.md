# 🚜 Tractor Portainer Proxy

This repository is a helper setup to augment a Jelastic Docker native environment with a Portainer proxy. This is useful if you want the Portainer UI to be accessible via a subdomain, e.g. `portainer.yourdomain.com`.

> [!NOTE]  
> This setup will **not** proxy containers you deploy through portainer, only the portainer UI itself.

## Usage

Setup a new stack in Portainer, selecting a repository deployment with this repo.  
Then setup the following environment variables:
- `PORTAINER_HOSTNAME`: The hostname of the portainer instance, e.g. `portainer.yourdomain.com`
- `ACME_EMAIL`: The email address to use for Let's Encrypt certificate generation.

## Note about networks

The compose file in this repository creates a new network called `portainer-proxy`. You then need to join the portainer service to this network through the UI.

The `default` network is required to allow caddy to obtain TLS certificates.