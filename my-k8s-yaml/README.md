# my-k8s-project

A minimal, working example wired end-to-end: one Namespace, one
ServiceAccount + Role + RoleBinding, one ConfigMap + Secret, one PVC, one
Deployment (using all of the above), and one Service exposing it.

## Apply everything, in order

```bash
kubectl apply -f 01-organization/
kubectl apply -f 02-security/
kubectl apply -f 03-configuration/
kubectl apply -f 04-storage/
kubectl apply -f 05-workloads/
kubectl apply -f 06-networking/
```

Or recursively in one shot — Kubernetes will retry objects whose
dependencies aren't ready yet, but applying in order avoids avoidable
failures on the first pass:

```bash
kubectl apply -R -f .
```

## Verify

```bash
kubectl get all -n my-app
kubectl describe deployment my-app -n my-app
```

## Clean up

```bash
kubectl delete namespace my-app
```

Deleting the Namespace deletes everything inside it — that's the point of
putting it all in one Namespace for a self-contained example.
