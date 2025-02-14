---
title: AWS Load Balancer Controller - External DNS & Service
description: Learn AWS Load Balancer Controller - External DNS & Kubernetes Service
---

- Using a Kubernetes Service of type LoadBalancer with ExternalDNS in Amazon EKS has specific use cases, especially when you want to expose services directly to the internet (or externally) and automate DNS record management. Here's a breakdown of its use case and how it works:
Use Case
The primary use case for using a Kubernetes Service of type LoadBalancer with ExternalDNS in EKS is direct service exposure with automated DNS management. This setup is useful when:
Direct Access to Services:
You want external users or systems to access a specific Kubernetes service directly without routing through an Ingress.
For example, exposing a database or an application API directly.
Simplified DNS Management:
You want DNS records (e.g., in Route 53) to be automatically created and updated for the external IP or hostname of the LoadBalancer service.
Single Service Exposure:
When you don’t need complex routing rules (like those provided by Ingress) and only need one service exposed.
Multi-Cluster or Multi-Service Setup:
If you have multiple clusters or services that need their own DNS records, ExternalDNS simplifies managing these records.
How It Works
Kubernetes Service of Type LoadBalancer:
When you create a Kubernetes Service of type LoadBalancer, the AWS Load Balancer Controller provisions an AWS Elastic Load Balancer (ELB) for that service.
The ELB provides an external IP or hostname that can be used to access the service.
ExternalDNS Integration:
ExternalDNS watches for LoadBalancer services in your cluster.
It reads annotations like external-dns.alpha.kubernetes.io/hostname in the service manifest to determine which DNS records to create.
It then creates or updates DNS records in Route 53, mapping the specified hostname (e.g., api.example.com) to the ELB's external IP or DNS name.

## Step-01: Introduction
- We will create a Kubernetes Service of `type: LoadBalancer`
- We will annotate that Service with external DNS hostname `external-dns.alpha.kubernetes.io/hostname: externaldns-k8s-service-demo101.stacksimplify.com` which will register the DNS in Route53 for that respective load balancer

## Step-02: 02-Nginx-App1-LoadBalancer-Service.yml
```yaml
apiVersion: v1
kind: Service
metadata:
  name: app1-nginx-loadbalancer-service
  labels:
    app: app1-nginx
  annotations:
    external-dns.alpha.kubernetes.io/hostname: externaldns-k8s-service-demo101.stacksimplify.com
spec:
  type: LoadBalancer
  selector:
    app: app1-nginx
  ports:
    - port: 80
      targetPort: 80  
```
## Step-03: Deploy & Verify

### Deploy & Verify
```t
# Deploy kube-manifests
kubectl apply -f kube-manifests/

# Verify Apps
kubectl get deploy
kubectl get pods

# Verify Service
kubectl get svc
```
### Verify Load Balancer 
- Go to EC2 -> Load Balancers -> Verify Load Balancer Settings

### Verify External DNS Log
```t
# Verify External DNS logs
kubectl logs -f $(kubectl get po | egrep -o 'external-dns[A-Za-z0-9-]+')
```
### Verify Route53
- Go to Services -> Route53
- You should see **Record Sets** added for `externaldns-k8s-service-demo101.stacksimplify.com`


## Step-04: Access Application using newly registered DNS Name
### Perform nslookup tests before accessing Application
- Test if our new DNS entries registered and resolving to an IP Address
```t
# nslookup commands
nslookup externaldns-k8s-service-demo101.stacksimplify.com
```
### Access Application using DNS domain
```t
# HTTP URL
http://externaldns-k8s-service-demo101.stacksimplify.com/app1/index.html
```

## Step-05: Clean Up
```t
# Delete Manifests
kubectl delete -f kube-manifests/

## Verify Route53 Record Set to ensure our DNS records got deleted
- Go to Route53 -> Hosted Zones -> Records 
- The below records should be deleted automatically
  - externaldns-k8s-service-demo101.stacksimplify.com
```


## References
- https://github.com/kubernetes-sigs/external-dns/blob/master/docs/tutorials/alb-ingress.md
- https://github.com/kubernetes-sigs/external-dns/blob/master/docs/tutorials/aws.md
