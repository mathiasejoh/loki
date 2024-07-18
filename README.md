# loki
Testing loki
Deploy Loki to send fluentbit logs
And visible in grafana 
on local kind cluster
Enable Grafana used to visualize the logs  and fluentbit used to get the logs
https://github.com/grafana/loki/tree/main/production/helm
Note that the chart for fluentbit is deprecated



helm repo add grafana https://grafana.github.io/helm-charts	
helm repo update
helm search repo grafana


helm show values grafana/loki-stack > loki-fluentbit-grafana.values.yaml
helm upgrade --install loki-stack grafana/loki-stack -n=loki --values loki-fluentbit-grafana.values.yaml --create-namespace
##########


Use this for cdc deploy 
helm upgrade --install loki-stack grafana/loki-stack -f cdc-values.yaml --namespace=cdc-loki  --create-namespace
########## you will fine complete values yaml here if you need to add any values. https://github.com/grafana/loki/blob/main/production/helm/loki/values.yaml
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


Deploy a sample application with labels and check if you will see it in grafana loki
kubectl apply -f sample-app.yaml

To send logs to AWS use cdc-loki-s3-values.yaml
create secret... do not push secret yaml to githup

helm upgrade --install loki-stack grafana/loki-stack -f cdc-loki-s3-values.yaml --namespace=loki0
Check S3 in aws to see logs 

Monitor loki-stack po for statefulset to see how logs are shipped to S3
The message should look like below

level=error ts=2024-07-18T18:28:27.485228002Z caller=flush.go:146 org_id=fake msg="failed to flush user" err="store put chunk: RequestError: send request failed\ncaused by: Put \"https://s3.us-west-2.amazonaws.com/vprofile-cicdt/fake/ff7f5f75e0368036%3A190c7124838%3A190c716532d%3Aae7c4317\": dial tcp: lookup s3.us-west-2.amazonaws.com on 10.96.0.10:53: server misbehaving"

