
## Install patched version of istio 1.1

Ensure you have the certificates added to your local Helm home directory. See https://kyma-project.io/docs/components/security#details-tls-in-tiller-add-certificates-to-helm-home

1. Uninstall the istio helm charts
```
helm delete istio-init --tls
helm delete istio --tls
```

2. Clone istio patch provided by James Ropper

```
git clone https://github.com/jroper/istio.git
cd istio
git checkout 1.1-with-port-exclusions
```

3. Install istio-init helm chart

```
helm template install/kubernetes/helm/istio-init \
  --name istio-init --namespace istio-system | kubectl apply -f -
```

4. Install istio helm chart

```
helm template install/kubernetes/helm/istio --name istio \
  --namespace istio-system \
  --values install/kubernetes/helm/istio/values-istio-demo.yaml \
  --set global.hub=docker.io/jamesroper \
  --set global.tag=1.1-with-outbound-port-exclusions \
  | kubectl apply -f -
```

## Install Demo App

1. Change to the namespace you want to use for deployment 

```
kubectl config set-context $(kubectl config current-context) --namespace=stage
```

2. Increase the ResourceQuota limits (or remove it)

```
kubectl patch ResourceQuota kyma-default --patch "$(cat ./patch-resourcequota.yaml)"
```

3. Install Demo App

```
kubectl apply -f kubernetes/
```

