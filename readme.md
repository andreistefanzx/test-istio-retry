
## Description

Test istio egress retry policy.
Note: Istio control plane is already installed.

We'll try reach http://httpstat.us/503 (external service) from inside cluster.

```
# apply istio config
kubectl apply -f serviceEntry_httpstat.yaml

# create curl pod and make request to external service httpstat.us
kubectl run curl --image=radial/busyboxplus:curl -it
curl -sSL -I http://httpstat.us/503

# monitor logs on istio-proxy sidecar container and istio egressgateway
kubectl -n istio-system logs -l app=istio-egressgateway -f
kubectl logs curl -c istio-proxy -f
```