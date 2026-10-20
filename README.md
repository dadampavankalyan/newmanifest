# Corrected Kubernetes manifests for bankapp

Images:
- `dadampavankalyan/k8sproject:appimage` — PHP/Apache app, port 80
- `dadampavankalyan/k8sproject:dbimage` — MySQL database, port 3306

## Apply
```bash
kubectl apply -f 00-namespace.yml
kubectl apply -f 01-configmap.yml
kubectl apply -f 02-secret.yml
kubectl apply -f 03-db-service.yml
kubectl apply -f 04-statefulset.yml
kubectl apply -f 05-deployment.yml
kubectl apply -f 06-app-service.yml
kubectl apply -f 07-ingress.yml
kubectl apply -f 08-hpa.yml
kubectl apply -f 09-pdb.yml
kubectl apply -f 10-resource-quota.yml
kubectl apply -f 11-network-policy.yml
```

Or apply all at once after extracting this folder:
```bash
kubectl apply -f .
```

## Important assumptions
- The database image is based on MySQL and listens on TCP 3306.
- The app image serves HTTP on port 80.
- The app's current `config.php` hardcodes `mysqldb`, `root`, `admin123`, and `customers`, as shown in the supplied application source. Kubernetes environment variables do not override PHP hardcoded values. If `dbimage` is based on the provided `database/Dockerfile`, it already sets `MYSQL_ROOT_PASSWORD=admin123` and runs `init.sql` to create `customers`.
- The MySQL PVC uses the cluster's default StorageClass. Check `kubectl get storageclass`; if there is no default StorageClass, set `storageClassName` explicitly in `volumeClaimTemplates`.
- Ingress requires an NGINX Ingress Controller. For EKS, install/configure one or use a LoadBalancer Service instead.
- The NetworkPolicy is enforced only if the cluster's CNI supports NetworkPolicy.
- The HPA requires Metrics Server.
