Make sure you have the following Docker images in your private registry
=======
(the version tags may vary):
```
	  1 docker.io/envoyproxy/envoy:v1.28.0
      5 ghcr.io/projectcontour/contour:v1.27.0
      1 quay.io/jetstack/cert-manager-cainjector:v1.11.0
      1 quay.io/jetstack/cert-manager-controller:v1.11.0
      1 quay.io/jetstack/cert-manager-webhook:v1.11.0
      2 registry.k8s.io/coredns/coredns:v1.10.1
      3 registry.tanzu.vmware.com/pivotal-gemfire/vmware-gemfire:10.0.1
      4 registry.tanzu.vmware.com/tanzu-gemfire-for-kubernetes/gemfire-controller:2.3.0
	  
	  -- if using Carvel installation method: 
	  
	  2 ghcr.io/carvel-dev/kapp-controller@sha256:3f99338bf7fc577686321817d4015e67f48396efd20bfcf67a4e1b7eca2d259a
      1 ghcr.io/carvel-dev/secretgen-controller@sha256:200e683e070e5be1e3c6aef6b9720d2490e1b61c35e58d751546afac5183e888
```	
Download Contour Gateway Provisioner's YAML	
====
From `https://projectcontour.io/quickstart/contour-gateway-provisioner.yaml`

Change the following
====
Add `--contour-image` and `--envoy-image` arguments to the `contour-gateway-provisioner` resource as:
```
---
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    control-plane: contour-gateway-provisioner
  name: contour-gateway-provisioner
  namespace: projectcontour
spec:
  replicas: 1
  selector:
    matchLabels:
      control-plane: contour-gateway-provisioner
  template:
    metadata:
      labels:
        control-plane: contour-gateway-provisioner
    spec:
      containers:
      - args:
        - gateway-provisioner
        - --metrics-addr=127.0.0.1:8080
        - --enable-leader-election
        - --contour-image=YOUR_CONTOUR_IMAGE_URL
        - --envoy-image=YOUR_ENVOY_IMAGE_URL
        command: ["contour"]
        image: YOUR_CONTOUR_IMAGE_URL
```

Apply the above file
===
`kubectl apply -n YOUR_NAMESPACE -f contour-gateway-provisioner.yaml`