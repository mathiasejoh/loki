# loki
Testing loki
Deploy Loki to send fluentbit logs
And visible in grafana 
on local kind cluster
Enable Grafana used to visualize the logs  and fluentbit used to get the logs

helm repo add grafana https://grafana.github.io/helm-charts	
helm repo update
helm search repo grafana


helm show values grafana/loki-stack > loki-fluentbit-grafana.values.yaml
helm upgrade --install loki-stack grafana/loki-stack -n=loki --values loki-fluentbit-grafana.values.yaml --create-namespace
Promtail should be set to false else it will be deployed 
Loki enabled with persistence set to true 
Grafana is enabled
Fluentbit enabled



kubectl port-forward -n loki loki-stack-grafana-5b6f9cb464-26lr5 80 3000
why 3000.
the pod has two containers and the grafana container is on
 Port:           3000/TCP
http://127.0.0.1:3000/
Or use LoadBalancer if in AWS 

kubectl get secret -n loki 
kubectl get secret loki-stack-grafana -n loki -o yaml
to get the admin pw to logon to grafana
echo "pw" | base64 -d ; echo

echo "username" | base64 -d

From grafana 
Explore and run query to view logs 
