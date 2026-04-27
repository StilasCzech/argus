## Why ingress-nginx instead of Traefik

k3s ships with Traefik as default ingress. We replaced it with ingress-nginx because:
- ingress-nginx is the most common ingress controller in production K8s clusters
- Wider community, more documented edge cases
- Traefik is fine, just less ubiquitous

Trade-off: lost Traefik's automatic dashboard, but we'll get equivalent visibility from kube-prometheus-stack later.