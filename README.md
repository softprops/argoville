# install tools

```sh
brew install helm
brew install argocd
```

# install argo cd

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



open http://localhost:8080 in your browser

the default username will be `admin`. run the following to get the password

```sh
kubectl -n argocd get secret argocd-initial-admin-secret \
    -o jsonpath="{.data.password}" \
    | base64 -d \
    | pbcopy
```

Alternatively create an argo app via

```sh
argocd app create argoville \
--repo https://github.com/softprops/argoville.git \
--path helm \
--values values-dev.yaml \
--dest-server https://kubernetes.default.svc \
--dest-namespace dev
```

ubectl port-forward service/argodemo -n dev 8082:80
