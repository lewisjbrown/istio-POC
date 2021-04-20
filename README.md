# istio-POC

## Getting Started

Download Istio and add binary to $PATH:

See https://istio.io/latest/docs/setup/install/helm/ for reference. 

```
$ curl -L https://istio.io/downloadIstio | sh -

$ cd istio-1.9.3

$ export PATH=$PWD/bin:$PATH

```

Download and install desired version of Helm:

See https://helm.sh/docs/intro/install/ for reference.

```
$ wget https://get.helm.sh/helm-v3.5.4-linux-amd64.tar.gz

$ tar -zxvf helm-v3.5.4-linux-amd64.tar.gz

$ mv linux-amd64/helm /usr/local/bin/helm

```

Create a namespace istio-system for Istio components:

```
$ kubectl create namespace istio-system
```

Install the Istio base chart which contains cluster-wide resources used by the Istio control plane:

```
$ helm install istio-base manifests/charts/base -n istio-system
```

Install the Istio discovery chart which deploys the istiod service:

```
$ helm install istiod manifests/charts/istio-control/istio-discovery -n istio-system
```

Verify installation:

```
$ kubectl get pods -n istio-system
```

Create namespace

```
$ kubectl create ns foo
```

Create deployments using the sample deployments in the downloaded istio directory:
```
$ kubectl apply -f <(istioctl kube-inject -f samples/httpbin/httpbin.yaml) -n foo

$ kubectl apply -f <(istioctl kube-inject -f samples/sleep/sleep.yaml) -n foo
```

When using mutual TLS, the proxy injects the X-Forwarded-Client-Cert header to the upstream request to the backend. That header’s presence is evidence that mutual TLS is used. So, we can test for mutual TLS using the following command:

```
$ kubectl exec "$(kubectl get pod -l app=sleep -n foo -o jsonpath={.items..metadata.name})" -c sleep -n foo -- curl -s http://httpbin.foo:8000/headers -s | grep X-Forwarded-Client-Cert | sed 's/Hash=[a-z0-9]*;/Hash=<redacted>;/'

```

If mutual TLS is present then the X-Forwarded-Client-Cert should be returned. 
