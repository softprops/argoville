# argoville

an exploration in arggocd (not to be confused with argo workflows org argo events)


## install local deps tools

Install docker/kube as wish I use [Docker Desktop](https://www.docker.com/products/docker-desktop/) with [Kubernetes enabled](https://docs.docker.com/desktop/kubernetes/)

Install cli tools

```sh
brew install helm
brew install argocd
```

## install argo cd

create a namespace and [install](https://argo-cd.readthedocs.io/en/stable/getting_started/#1-install-argo-cd) it

```sh
kubectl apply -f - <<EOF
apiVersion: v1
kind: Namespace
metadata:
  name: argo
EOF
```

```sh
kubectl apply -n argocd \
    -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

## port forward

```sh
kubectl get services -n argocd --watch
kubectl port-forward service/argocd-server -n argocd 8080:443
```

authenticate the `argocd` cli

```sh
argocd login 127.0.0.1:8080
```

the default username will be `admin`. run the following to get the password

```sh
kubectl -n argocd get secret argocd-initial-admin-secret \
    -o jsonpath="{.data.password}" \
    | base64 -d \
    | pbcopy
```

## create app

open http://localhost:8080 in your browser and create app

Alternatively create an argo app via the `argocd` cli

```sh
argocd app create argoville \
    --repo https://github.com/softprops/argoville.git \
    --path helm \
    --values values-dev.yaml \
    --dest-server https://kubernetes.default.svc \
    --dest-namespace dev \
    --sync-policy automated \
    --self-heal \
    --auto-prune
```


```sh
kubectl get all --namespace dev
```

port forward demo app

```sh
kubectl port-forward service/argodemo -n dev 8082:80
```

clean up

```sh
argocd app delete argoville -y
```

```sh
kubectl delete ns argo
```

\-fin
