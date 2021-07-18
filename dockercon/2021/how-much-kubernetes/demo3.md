# Demo 3: Deployments

You don't often create Pods directly because that isn't flexible - you can't update Pods to release application updates, and you can only scale them by manually deploying new Pods.

Instead you'll use a [controller](https://kubernetes.io/docs/concepts/architecture/controller/) - a Kubernetes object which manages other objects. The controller you'll use most for Pods is the Deployment, which has features to support upgrades and scale.

Deployments use a template to create Pods, and a label selector to identify the Pods they own.

## Create a Deployment for the whoami app

Delete the existing whoami Pod:

```
kubectl delete pod whoami
```

This Deployment wraps the same Pod spec, with the same labels:

- [whoami-v1.yaml](deployments/whoami-v1.yaml)

```
kubectl apply -f deployments/whoami-v1.yaml

kubectl get pods -l app=whoami -o wide
```

> Deployments apply their own naming system when they create Pods, they end with a random string

Deployments are first-class objects, you work with them in Kubectl in the usual way. 

```
kubectl get deployments
```

The new Pod has the same labels, so the Service still works:

```
curl http://localhost:8080
```

## Updating the application

Application updates usually mean a change to the Pod spec - a new container image, or a configuraion change. You can't change the spec of a running Pod, but you can change the Pod spec in a Deployment. It makes the change by starting up new Pods and terminating the old ones.

- [whoami-v2.yaml](deployments/whoami-v2.yaml) changes a configuration setting for the app and runs multiple replicas

```
# open a new terminal to monitor the Pods:
kubectl get po -l app=whoami --watch

# apply the change:
kubectl apply -f deployments/whoami-v2.yaml
```

When the rollout completes:

```
kubectl get po -l app=whoami -o wide
```

The new version of the app returns a more concise response:

```
curl http://localhost:8080 # x3
```