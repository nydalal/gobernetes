<img src="assets/gobernetes-logo-text.jpeg" align="right" width="300" heigh="auto">

# gobernetes

Collection of miscellaneous [helper tools](#tools), [must-read articles](#articles), [documentations](#documentation), [k8s manifests](#kubernetes-components-included) & [useful commands](#useful-commands). 

<br><br><br><br>

## Tools
* [Kustomize](https://kubernetes.io/blog/2018/05/29/introducing-kustomize-template-free-configuration-customization-for-kubernetes/) (now part of kubectl since 1.14)
* [kubectx + kubens](https://github.com/ahmetb/kubectx) - switch between clusters and namespaces
* [kube-fzf](https://github.com/arunvelsriram/kube-fzf) - findpod/tailpod/execpod/describepod with [fzf](https://github.com/junegunn/fzf)
* [kubetail](https://github.com/johanhaleby/kubetail) - tail k8s logs
* [rakkess](https://github.com/corneliusweig/rakkess) - show an access matrix
* [rbac-lookup](https://github.com/reactiveops/rbac-lookup) - find k8s roles and cluster roles
* [kube-capacity](https://github.com/robscott/kube-capacity) - provide an overview of the resource requests/limits
* [ketall](https://github.com/corneliusweig/ketall) - show really all k8s resources
* [kubediff](https://github.com/weaveworks/kubediff) - show differences between running state and version controlled configuration.
* [kubefwd](https://github.com/txn2/kubefwd) - bulk port forwarding k8s services for local dev
* [kube-ps1](https://github.com/jonmosco/kube-ps1) - k8s prompt info for bash and zsh
* [pop eye](https://github.com/derailed/popeye) - k8s cluster resource sanitizer
* [kubectl cheat sheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet)
* [Fish auto completion for kubectl](https://gist.github.com/Aracki/cf422173371d2118ae94bb6821f074e0)

## Articles:
* K8s components - good introduction material
	* [Understanding kubernetes networking: pods](https://medium.com/google-cloud/understanding-kubernetes-networking-pods-7117dd28727)
	* [Understanding kubernetes networking: services](https://medium.com/google-cloud/understanding-kubernetes-networking-services-f0cb48e4cc82)
	* [Understanding kubernetes networking: ingress](https://medium.com/google-cloud/understanding-kubernetes-networking-ingress-1bc341c84078)
	* [Kubernetes NodePort vs LoadBalancer vs Ingress? When should I use what?](https://medium.com/google-cloud/kubernetes-nodeport-vs-loadbalancer-vs-ingress-when-should-i-use-what-922f010849e0)
* About networking:
	* [IP, subnets, CIDR](https://www.digitalocean.com/community/tutorials/understanding-ip-addresses-subnets-and-cidr-notation-for-networking)
	* [Network interfaces and protocols](https://www.digitalocean.com/community/tutorials/an-introduction-to-networking-terminology-interfaces-and-protocols)
* Best practices:
	* [The 12 Factor App](https://12factor.net/) - methodology for building modern software 
	* [12 Fractured Apps](https://medium.com/@kelseyhightower/12-fractured-apps-1080c73d481c) - Kelsey Hightower’s view on how 12FA and Docker can be killer combo
	* [Modernizing Applications for Kubernetes](https://dev.to/digitalocean/modernizing-applications-for-kubernetes-1hon) - DigitalOcean guide
	* [Configuration Best Practices](https://kubernetes.io/docs/concepts/configuration/overview/#general-configuration-tips) - official kubernetes guide
* [Kubernetes the hard way](https://github.com/kelseyhightower/kubernetes-the-hard-way) - Kelsey Hightower’s guide on how to bootstrap k8s the hard way on GCP with no scripts.
* Great [article](https://medium.com/spire-labs/utilizing-kubernetes-liveness-and-readiness-probes-to-automatically-recover-from-failure-2fe0314f2b2e) about utilizing k8s liveness and readiness probes to automatically recover from failure
* Great [article](https://docs.bitnami.com/kubernetes/how-to/configure-rbac-in-your-kubernetes-cluster/) about configuring *RBAC*
* [Pain(less) NGINX Ingress](https://danielfm.me/posts/painless-nginx-ingress.html) - Daniel Martins about Nginx Ingress outages and config reloading

## Documentation:
Personal documentation on how I set things up:
* [Bootstrap AWS infrastructure](docs/01-bootstrap-aws-infrastructure.md)
* [Bootstrap K8s cluster with kubeadm](docs/02-bootstrap-k8s-cluster.md)
* [Set up GlusterFS](docs/03-set-up-glusterfs.md)
* [Set up Ingress + TLS termination with cert-manager](docs/04-set-up-ingress.md)
* [Set up monitoring with Prometheus and Grafana](docs/05-set-up-monitoring.md)
* [Set up private docker registry](docs/06-set-up-private-registry.md)

## Kubernetes components included:
`k8s_manifests/` folder contains all the manifests for: 
* Simple web server application written in Go - [go-ws](https://github.com/Aracki/go-ws)
* MongoDB
* Ingress Controllers - in order for the Ingress resources to work _(read this [Bare metal considerations](https://kubernetes.github.io/ingress-nginx/deploy/baremetal/#bare-metal-considerations))_
   * [Nginx](https://github.com/kubernetes/ingress-nginx)
   * [Traefik](https://docs.traefik.io/user-guide/kubernetes/)
* [Monitoring stack](https://github.com/coreos/prometheus-operator)
   * Prometheus   
   * Alert Manager
   * Grafana
* CI/CD stack
   * [Jenkins](k8s_manifests/jenkins)
   * [Keel](k8s_manifests/keel)
* [cert-manager](https://github.com/jetstack/cert-manager/) - for automated provisioning TLS certs for both staging/production environments
* Kubernetes [Secrets](https://kubernetes.io/docs/concepts/configuration/secret/) & [ConfigMaps](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/#create-a-configmap) for storing secret and non-secret configurations

## Useful commands
* List all resources in a namespace: `kubectl api-resources --verbs=list --namespaced -o name | xargs -n 1 kubectl get --show-kind --ignore-not-found -n <namespace>`
* Gets IPs of pods: `kubectl get pods --selector=app=go-ws -o jsonpath='{.items[*].status.podIP}'`
* List all containers in k8s cluster: `kubectl get pods -o jsonpath={.items[*].spec.containers[*].name} --all-namespaces`
* Activate these with **kube-fzf**:
	* `findpod` + `describepod` + `execpod` + `tailpod`
* Kill pod forcefully:
	* `kubectl delete pods <pod> --grace-period=0 --force`
	* `kubectl patch pod <pod> -p '{"metadata":{"finalizers":null}}'` 
* Replace resource forcefully: `kubectl replace --force -f go-web-server.yml`
* Print the supported API versions/resources: 
	* `kubectl api-versions`
	* `kubectl api-resources` 
* Overwriting the existing labels: `kubectl label --overwrite pods foo status=unhealthy`
* Show the default values for kubelet: `kubeadm config print-default --api-objects KubeletConfiguration`
* Update existing ConfigMap based on a file: 
	```
	kubectl create configmap traefik-conf --from-file=traefik.toml --dry-run -o yaml | kubectl replace configmap traefik-conf -f - -n traefik
	``` 
