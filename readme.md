
## Description

Test istio egress retry policy.
Prerequisites: Istio control plane already installed.

We'll try reach http://httpstat.us/503 URI (external service) from inside existing k8s cluster with istio installed.
Any request to http://httpstat.us/503 URI will get a 503 response.

Because retries is set to 0 `attempts: 0` inside the VirtualService, the istio egressgateway will not have any retries for 503 responses.

curl ====> istio-proxy ====> egressgateway ====> http://httpstat.us/503
1xReq      1xReq             1xReq               1xReq  

By default, the retry mechanishm is configured with 2 retries (`attempts: 2`) which means the external service will receive a total of 3 requests (1 initial request plus 2 retries).

curl ====> istio-proxy ====> egressgateway ====> http://httpstat.us/503
1xReq      1xReq             1xReq + 2 RetReq    1xReq + 2 RetReq 

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