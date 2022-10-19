

# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# Git push

git status
eval $(ssh-agent -s)
ssh-add /home/fernando/.ssh/chave-debian10-github
git add .
git commit -m "Aula 004 - 002 - Introduction to Argo Rollouts"
git push
git status


# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# Introduction to Argo Rollouts

Argo Rollouts is a progressive delivery controller created for Kubernetes. It allows you to deploy your application with minimal/zero downtime by adopting a gradual way of deploying instead of taking an “all at once” approach.

Argo Rollouts supercharges your Kubernetes cluster and in addition to the rolling updates you can now do:

    Blue/green deployments
    Canary deployments
    A/B tests
    Automatic rollbacks
    Integrated Metric analysis


Let’s see how this works in practice.










# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# Argo Rollouts - Kubernetes Progressive Delivery Controller¶
<https://argoproj.github.io/argo-rollouts/>

What is Argo Rollouts?¶

Argo Rollouts is a Kubernetes controller and set of CRDs which provide advanced deployment capabilities such as blue-green, canary, canary analysis, experimentation, and progressive delivery features to Kubernetes.

Argo Rollouts (optionally) integrates with ingress controllers and service meshes, leveraging their traffic shaping abilities to gradually shift traffic to the new version during an update. Additionally, Rollouts can query and interpret metrics from various providers to verify key KPIs and drive automated promotion or rollback during an update.

Here is a demonstration video (click to watch on Youtube):

Argo Rollouts Demo
Why Argo Rollouts?¶

The native Kubernetes Deployment Object supports the RollingUpdate strategy which provides a basic set of safety guarantees (readiness probes) during an update. However the rolling update strategy faces many limitations:

    Few controls over the speed of the rollout
    Inability to control traffic flow to the new version
    Readiness probes are unsuitable for deeper, stress, or one-time checks
    No ability to query external metrics to verify an update
    Can halt the progression, but unable to automatically abort and rollback the update

For these reasons, in large scale high-volume production environments, a rolling update is often considered too risky of an update procedure since it provides no control over the blast radius, may rollout too aggressively, and provides no automated rollback upon failures.
Controller Features¶

    Blue-Green update strategy
    Canary update strategy
    Fine-grained, weighted traffic shifting
    Automated rollbacks and promotions
    Manual judgement
    Customizable metric queries and analysis of business KPIs
    Ingress controller integration: NGINX, ALB
    Service Mesh integration: Istio, Linkerd, SMI
    Simultaneous usage of multiple providers: SMI + NGINX, Istio + ALB, etc.
    Metric provider integration: Prometheus, Wavefront, Kayenta, Web, Kubernetes Jobs, Datadog, New Relic, Graphite, InfluxDB

Quick Start¶

kubectl create namespace argo-rollouts
kubectl apply -n argo-rollouts -f https://github.com/argoproj/argo-rollouts/releases/latest/download/install.yaml

Follow the full getting started guide to walk through creating and then updating a rollout object.
How does it work?¶

Similar to the deployment object, the Argo Rollouts controller will manage the creation, scaling, and deletion of ReplicaSets. These ReplicaSets are defined by the spec.template field inside the Rollout resource, which uses the same pod template as the deployment object.

When the spec.template is changed, that signals to the Argo Rollouts controller that a new ReplicaSet will be introduced. The controller will use the strategy set within the spec.strategy field in order to determine how the rollout will progress from the old ReplicaSet to the new ReplicaSet. Once that new ReplicaSet is scaled up (and optionally passes an Analysis), the controller will mark it as "stable".

If another change occurs in the spec.template during a transition from a stable ReplicaSet to a new ReplicaSet (i.e. you change the application version in the middle of a rollout), then the previously new ReplicaSet will be scaled down, and the controller will try to progress the ReplicasSet that reflects the updated spec.template field. There is more information on the behaviors of each strategy in the spec section.
Use cases of Argo Rollouts¶

    A user wants to run last-minute functional tests on the new version before it starts to serve production traffic. With the BlueGreen strategy, Argo Rollouts allows users to specify a preview service and an active service. The Rollout will configure the preview service to send traffic to the new version while the active service continues to receive production traffic. Once a user is satisfied, they can promote the preview service to be the new active service. (example)

    Before a new version starts receiving live traffic, a generic set of steps need to be executed beforehand. With the BlueGreen Strategy, the user can bring up the new version without it receiving traffic from the active service. Once those steps finish executing, the rollout can cut over traffic to the new version.

    A user wants to give a small percentage of the production traffic to a new version of their application for a couple of hours. Afterward, they want to scale down the new version and look at some metrics to determine if the new version is performant compared to the old version. Then they will decide if they want to roll out the new version for all of the production traffic or stick with the current version. With the canary strategy, the rollout can scale up a ReplicaSet with the new version to receive a specified percentage of traffic, wait for a specified amount of time, set the percentage back to 0, and then wait to rollout out to service all of the traffic once the user is satisfied. (example)

    A user wants to slowly give the new version more production traffic. They start by giving it a small percentage of the live traffic and wait a while before giving the new version more traffic. Eventually, the new version will receive all the production traffic. With the canary strategy, the user specifies the percentages they want the new version to receive and the amount of time to wait between percentages. (example)

    A user wants to use the normal Rolling Update strategy from the deployment. If a user uses the canary strategy with no steps, the rollout will use the max surge and max unavailable values to roll to the new version. (example)

Examples¶

You can see more examples of Rollouts at:

    The example directory
    The Argo Rollouts Demo application
