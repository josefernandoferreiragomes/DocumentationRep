# Kubernetes Commands Quick Reference

## Basic Commands
- **kubectl version**: Show the Kubernetes version.
- **kubectl cluster-info**: Display cluster information.
- **kubectl get nodes**: List all nodes in the cluster.
- **kubectl get pods**: List all pods in the default namespace.
- **kubectl get services**: List all services in the default namespace.
- **kubectl get deployments**: List all deployments in the default namespace.
- **kubectl describe [resource] [name]**: Show detailed information about a resource.
- **kubectl logs [pod-name]**: Print the logs for a pod.
- **kubectl exec -it [pod-name] -- /bin/bash**: Access a pod's container in interactive mode.

## Managing Resources
- **kubectl create -f [file]**: Create resources from a file.
- **kubectl apply -f [file]**: Apply changes to resources from a file.
- **kubectl delete -f [file]**: Delete resources from a file.
- **kubectl scale --replicas=[count] deployment/[name]**: Scale a deployment to a specified number of replicas.
- **kubectl rollout status deployment/[name]**: Check the status of a deployment rollout.
- **kubectl set image deployment/[name] [container-name]=[new-image]**: Update the image of a deployment's container.

## Namespaces
- **kubectl get namespaces**: List all namespaces.
- **kubectl create namespace [name]**: Create a new namespace.
- **kubectl delete namespace [name]**: Delete a namespace.

## Configurations
- **kubectl config view**: Show merged kubeconfig settings.
- **kubectl config use-context [context-name]**: Set the current context.
- **kubectl config set-context [context-name] --namespace=[namespace]**: Set the default namespace for a context.

## k3d Specific Commands
- **k3d cluster create [name]**: Create a new k3d cluster.
- **k3d cluster delete [name]**: Delete a k3d cluster.
- **k3d cluster list**: List all k3d clusters.
- **k3d node create [name] --cluster [cluster-name]**: Create a new node in a k3d cluster.
- **k3d node delete [name] --cluster [cluster-name]**: Delete a node from a k3d cluster.
- **k3d node list --cluster [cluster-name]**: List all nodes in a k3d cluster.

## Miscellaneous
- **kubectl get events**: List all events in the default namespace.
- **kubectl top nodes**: Show resource usage of nodes.
- **kubectl top pods**: Show resource usage of pods.
- **kubectl port-forward [pod-name] [local-port]:[pod-port]**: Forward one or more local ports to a pod.
- **kubectl cp [pod-name]:[path] [local-path]**: Copy files from a pod to the local machine.

## Sources:
Official site:
  https://kubernetes.io/docs/reference/kubectl/quick-reference/
