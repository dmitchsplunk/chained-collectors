# chained-collectors

This example shows how to deploy two OpenTelemetry collectors in a Kubernetes environment. The first collector is the Splunk distribution of the OpenTelemetry collector, and it's deployed via Helm as a Daemonset. It receives metrics, traces, and logs, and includes various processors. It exports traces and metrics to Splunk Observability Cloud, and exports logs to the second collector. 

The second collector is referred to as the "upstream" collector, as it uses the OpenTelemetry Collector Helm Chart <https://opentelemetry.io/docs/kubernetes/helm/collector/> rather than the Splunk distribution. It's deployed as a Kubernetes deployment rather than a daemonset. It receives logs and exports them to AWS CloudWatch Logs.  The second collector is necessary to export logs to CloudWatch, since the Splunk distribution doesn't include this exporter. 

To deploy this example: 

1) Edit configmap.yaml and secret.yaml to input the specific parameters for your environment.  Add your Splunk Observability Cloud access token to the splunk-otel-values.yaml file. 

2) Use the following commands to create the config map and secrets:  

kubectl apply -f ./configmap.yaml
kubectl apply -f ./secret.yaml

3) Install the Helm repositories: 

helm repo add open-telemetry https://open-telemetry.github.io/opentelemetry-helm-charts
helm repo add splunk-otel-collector-chart https://signalfx.github.io/splunk-otel-collector-chart
helm repo update

4) Install the upstream collector:

helm install upstream-opentelemetry-collector open-telemetry/opentelemetry-collector \
   -f ./upstream-collector-values.yaml

5) Install the Splunk OpenTelemetry collector:

helm install --generate-name splunk-otel-collector-chart/splunk-otel-collector -f ./splunk-otel-values.yaml
