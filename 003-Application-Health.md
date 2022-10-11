
# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# Git push
git status
eval $(ssh-agent -s)
ssh-add /home/fernando/.ssh/chave-debian10-github
git add .
git commit -m "Aula 03 - Application Health"
git push
git status


# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# Application Health
Apart from the synced/out-of-sync status, Argo CD also keeps track of the service health for each deployed application. The health status is shown in the UI and can also be retrieved by the CLI.

The possible values are:

    “Healthy” -> Resource is 100% healthy
    “Progressing” -> Resource is not healthy but still has a chance to reach healthy state
    “Suspended” -> Resource is suspended or paused. The typical example is a cron job
    “Missing” -> Resource is not present in the cluster
    “Degraded” -> Resource status indicates failure or resource could not reach healthy state in time
    “Unknown” -> Health assessment failed and actual health status is unknown


The decision whether an application is healthy or not depends on the type of underlying Kubernetes resources.

For the built-in Kubernetes resources, the rules are the following:

    Deployments, ReplicaSets, StatefulSets, and Daemon sets are considered “healthy” if observed generation is equal to desired generation.
    Number of updated replicas equals the number of desired replicas.


For a service of type Loadbalancer or Ingress, the resource is healthy if the status.loadBalancer.ingress list is non-empty, with at least one value for hostname or IP.

For custom Kubernetes resources, health is defined in Lua scripts. Some examples of resources that have custom health definitions are:

    Argo Rollout (and associated Analysis and Experiments)
    Bitnami Sealed secrets
    Cert Manager
    Elastic Search
    Jaeger
    Kafka
    CrossPlane provider


You can see a list of all custom health checks at https://github.com/argoproj/argo-cd/tree/master/resource_customizations. You can add your own health check for a custom resource by implementing the check in Lua which is a self-contained scripting language.

Here is a simple example of the health check from Argo Rollouts Experiments that simply maps the experiment status to Argo CD health status.
<https://github.com/argoproj/argo-cd/blob/master/resource_customizations/argoproj.io/Experiment/health.lua>

~~~~lua
hs = {}
if obj.status ~= nil then
    if obj.status.phase == "Pending" then
        hs.status = "Progressing"
        hs.message = "Experiment is pending"
    end
    if obj.status.phase == "Running" then
        hs.status = "Progressing"
        hs.message = "Experiment is running"
    end
    if obj.status.phase == "Successful" then
        hs.status = "Healthy"
        hs.message = "Experiment is successful"
    end
    if obj.status.phase == "Failed" then
        hs.status = "Degraded"
        hs.message = "Experiment has failed"
    end
    if obj.status.phase == "Error" then
        hs.status = "Degraded"
        hs.message = "Experiment had an error"
    end
    return hs
end
~~~~

hs.status = "Progressing"
hs.message = "Waiting for experiment to finish: status has not been reconciled."
return hs

You can write your own health check in a similar manner for your custom resource, if it is not already supported by Argo CD.

Note that having a custom health check just provides a better experience with Argo CD. You can still deploy your custom application even without a health check, and it will never show as Healthy in the Argo CD dashboards.

Argo CD health checks are completely independent from Kubernetes health probes.