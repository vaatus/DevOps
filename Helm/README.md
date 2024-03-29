Helm is Package manager for Kubernetes.

### Resources

- [Helm Docs](https://helm.sh/)
- [ Helm for beginers](https://www.youtube.com/watch?v=KeRrvCrF8zc)
- [ Helm in Depth ](https://www.youtube.com/watch?v=gbUBTTXuQwI&list=PLLYW3zEOaqlKYku0piyzzLFGpR9VpPvXR)


### Using a Helm Chart

- Once we install the helm to the system, we have to add a chart repository, like prometheus, Ingress-nginx and [more](https://artifacthub.io/packages/search?kind=0)

Eg:

```bash
helm repo add <name> <repo url>
helm repo add kubernetes-dashboard https://kubernetes.github.io/dashboard/
```

- To check list of charts we can install

```bash
helm search repo kubernetes-dashboard
```

- To install a chart 

```
helm install <name> kubernetes-dashboard/kubernetes-dashboard
```

