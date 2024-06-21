# Games on K8s
Various web-based games deployed on Kubernetes. 

The containers are deployed using straightforward deployment manifests and are served by ClusterIP type services. External traffic to the cluster is handled by an Nginx Ingress controller, which is exposed through a routable IP address provided by the MetalLB load balancer.

## Requirements

- MetalLB for bare-metal Load Balancing. Installation manifests included in `manifests/metallb` for reference.
- Nginx Ingress controller (Helm installation provided below).

## Deployment
Create the namespace
```
kubectl apply -f namespace
```

Install Nginx Ingress chart
```
helm repo add ingress-nginx http://kubernetes.github.io/ingress-nginx
helm install gamesingress ingress-nginx/ingress-nginx -n games-ns --values helm/ingress-chart-values.yaml
```

Apply the manifests
```
kubectl apply -f deploy-games.yaml -f services.yaml -f ingress-resources.yaml  -f hpa.yaml
```

Retrieve the Ingress `EXTERNAL-IP` address provided by MetalLB
```
kubectl get svc -n games-ns
```

Configure hostname resolution:
- If you're using DNS, add an A record for the hostnames defined in the `ingress-resource.yaml`.
- Or you can edit your local `/etc/hosts` file to add the IP/hostname mapping. 

For example:
```
# The IP is the ingress' IP  retrieved in the previous step
10.10.50.90     snake.example.com
10.10.50.90     flappybird.example.com
```
