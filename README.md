# Nginx Ingress on GKE kubernetes cluster

[![forthebadge](https://forthebadge.com/images/badges/made-with-go.svg)](https://forthebadge.com)

We will deploy 2 pods and dispach traffic with the NGINX Ingress Controller for Kubernetes

Requirements

- A Cluster Already Online
- Helm on your local machine

## Step 1 : Initialize Helm , Install Tiller and give Tiller some rights

Helm is the package manager for Kubernetes and the best way to find, share, and use software built for Kubernetes.

```
kubectl apply -f rbac-config.yaml
```

```
helm init --service-account tiller --upgrade
```

## Step 2 : Install Nginx Ingress with Helm

In Kubernetes, Ingress is an object that allows access to your Kubernetes services from outside the Kubernetes cluster. You configure access by creating a collection of rules that defines which inbound connections reach which services.

![index](https://github.com/Just1B/Kubernetes_Nginx_Ingress/raw/master/screens/ingress.png)

```
helm install stable/nginx-ingress --name nginx-ingress --namespace nginx-ingress --set rbac.create=true --set controller.service.externalTrafficPolicy=Local
```

Next we will need the Ingress `EXTERNAL-IP`

```
kubectl --namespace nginx-ingress get services
```

Now you can point 2 subdomains ( TYPE A ) on the `EXTERNAL-IP`

Propagation can be long so wait for it 😴

## Step 3 : Deploy 2 Differents services

We will deploy 2 `hashicorp/http-echo` server an use the ingress to point on subdomains.

First create the fruits namespace

```
kubectl create namespace fruits
```

Deploy the http-echo servers

```
kubectl apply -f apple.yml
```

```
kubectl apply -f banana.yml
```

Now, edit `fruits.yml` with your custom domains and deploy the fruits ingress

```
kubectl apply -f fruits.yml
```

And voila !

## Clear the Stack

You can delete the `fruits` and `nginx-ingress` Namespaces

# Licence

The MIT License

Copyright (c) 2019 JUST1B

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
