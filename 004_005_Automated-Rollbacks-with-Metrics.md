
# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# Git push

git status
eval $(ssh-agent -s)
ssh-add /home/fernando/.ssh/chave-debian10-github
git add .
git commit -m "Aula 004 - 005 - Automated Rollbacks with Metrics"
git push
git status


# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# Automated Rollbacks with Metrics

While you can use canaries with simple pauses between the different stages, Argo Rollouts offers the powerful capability to look at application metrics and decide automatically if the deployment should continue or not.

The idea behind this approach is to completely automate canary deployments. Instead of having a human running manual smoke tests, or looking at graphs, you can set different thresholds that define if a deployment is “successful” or not.

Here is the flow of the whole process:
Argo Rollouts already supports several metric providers such as Prometheus, DataDog, NewRelic, Cloudwatch, etc.

It is also possible to use a custom URL instead of a metric provider that will decide if a deployment should continue or not.

To take advantage of metric evaluation, Argo Rollouts introduces the concept of Analysis. An Analysis is deployed along with your Rollout and defines the thresholds for metrics.

Here is an example:

~~~~yaml
apiVersion: argoproj.io/v1alpha1
kind: AnalysisTemplate
metadata:
  name: success-rate
spec:
  args:
  - name: service-name
  metrics:
  - name: success-rate
    interval: 2m
    count: 2
    # NOTE: prometheus queries return results in the form of a vector.
    # So it is common to access the index 0 of the returned array to obtain the value
    successCondition: result[0] >= 0.95
    provider:
      prometheus:
        address: http://prom-release-prometheus-server.prom.svc.cluster.local:80
        query: sum(response_status{app="{{args.service-name}}",role="canary",status=~"2.*"})/sum(response_status{app="{{args.service-name}}",role="canary"}
~~~~

This prometheus analysis says that :

    A metric of response status (200 responses vs total) will be fetched for Prometheus.
    If this results in more than 0.95, the deployment will continue with success.
    If the result is less than 0.95, then the canary will fail and the whole deployment will be rolled back automatically.