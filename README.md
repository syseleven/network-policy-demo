# Cilium Network Policy Demo


### Prerequisites:

You will need a running Kubernetes Cluster without a CNI installation. Or a cluster with cillium CNI running. If you already have a working cilium setup please skip the first step. 


* Install Cilium 

```
helm upgrade --install -n kube-system -f  cilium-values.yaml cilium cilium/cilium
```

* View the cilium endpoints

```
kubectl get ciliumendpoints.cilium.io -A
```

* Test Networking with the netshoot pod

```
kubectl run tmp-shell --rm -i --tty --image nicolaka/netshoot
```

* Deploy and the cluster wide default deny policy 

```
kubectl create -f network-polices/default-deny-policy.yaml
```

* Test Networking again with the netshoot pod

```
kubectl run tmp-shell --rm -i --tty --image nicolaka/netshoot
```

* observe blocked traffic with hubble cli

```
cilium hubble port-forward
hubble observe --verdict DROPPED -f
```

* observe blocked traffic with hubble ui

```
cilium hubble ui
```

* Curl the webapp

```
curl -IL app.example.com
```

* Start deploying network polices. Start with the nginx ingress policy

```
kubectl create -f network-polices/nginx-ingress.yaml
```

* Curl the Nginx Ingress LB Service again

```
curl MY-LB-IP
curl -IL app.example.com
curl -IL blog.example.com
```


* Observe blocked traffic again with hubble cli

```
cilium hubble port-forward
hubble observe --verdict DROPPED -f
```

* Start deploying network polices for the webapp

```
kubectl create -f network-polices/webapp.yaml
```

* Start deploying network polices for the cert-manager and external-dns

```
kubectl create -f network-polices/external-dns.yaml
kubectl create -f network-polices/cert-manager.yaml
```

* Observe blocked traffic again with hubble cli only wordpress traffic is blocked

```
cilium hubble port-forward
hubble observe --verdict DROPPED -f
```

* curl wordpress

```
curl -IL blog.example.com
```


* Deploy network polices wordpress

```
kubectl create -f network-polices/wordpress.yaml
```

* curl wordpress again

```
curl -IL blog.example.com
```

* Observe blocked traffic again with hubble cli there should be no more blocked traffic

```
cilium hubble port-forward
hubble observe --verdict DROPPED -f
```




