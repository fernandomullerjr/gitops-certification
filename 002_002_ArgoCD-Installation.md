Argo CD Installation
While Argo CD can be used to deploy and manage other applications, do not forget that it is also an application by itself. There are many ways to install it on a Kubernetes cluster and in most cases you need to look at your company policy regarding other Kubernetes applications that are critical for your infrastructure.

There are several questions that you need to answer before making a decision about installation:

    Will your Argo CD installation manage only the cluster on which it is installed? Will it manage other clusters? Both?
    Do you want a High Availability (HA) installation or not?
    Do you want to use plain manifests or are you looking for a Helm chart?
    How will you manage Argo CD upgrades? With an external system? Or do you wish for Argo CD to manage itself?


For quick demos and experimentation, you can deploy ArgoCD by directly using the manifests


kubectl create namespace argocd


kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

For a production setup we suggest you use Autopilot, a companion project that not only installs Argo CD, but commits all configuration to git so Argo CD can manage itself using GitOps.

For more traditional installation options, the manifests directory offers different modes of installation with or without High Availability. The installs marked as “namespace” allow you to install Argo CD on a single namespace without cluster-wide privileges. This is best when you want to install Argo CD on one cluster but have it manage other external clusters.

A community helm chart is also available at https://github.com/argoproj/argo-helm/tree/master/charts/argo-cd
Regarding configuration options, Argo CD can be customized a lot after the initial installation, but the absolute essentials are:

    Decide how to expose Argo CD API/UI to your users.
    Decide how users are going to authenticate to Argo CD.


Exposing your Argo CD to users will be a different process depending on your organization, but in most cases, you should make the Argo CD UI available to external traffic via a Kubernetes ingress.

The setup process is not specific to Argo CD, and you should already be familiar with ingresses for other Kubernetes applications. You can follow the official documentation for setting up Argo CD with popular Ingress options.

Once the external URL is ready, you need to decide how users will access the Argo CD UI. There are mainly two approaches:

    Use a small number of local users. Authentication is handled by Argo CD itself. Best for very small companies (e.g. 2-5 people).
    Use an SSO provider. Authentication is handled by the provider. Ideal for companies and large organizations


You can read all the details at https://argo-cd.readthedocs.io/en/stable/operator-manual/user-management/
