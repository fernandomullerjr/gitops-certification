



# What is Argo CD?

A manager for Helm and Kustomize templates

A certified distribution of Kubernetes

A GitOps Agent
    Correct. Argo CD is a GitOps Agent that implements the GitOps principles

A solution for Kubernetes workflows




# Which other Argo products does Argo CD need to function correctly?

Argo CD also needs Argo Workflows and Argo Events

Argo CD also needs Argo Rollouts

ArgoCD only needs Argo Workflows. Argo Event and Argo Rollouts are optional

None. Argo CD is a standalone project. It works great with the other Argo projects, but it does not depend on them.
    Correct! The same sentence is true for all other Argo projects.





# How does ArgoCD interact with clusters?

You need to install ArgoCD on every cluster that you wish to deploy

You only need one ArgoCD instance on one cluster. You can use the ArgoCD CLI for other clusters

You need one ArgoCD instance for every 5 Kubernetes clusters that you wish to deploy to

You can have any combination of clusters and ArgoCD instances. ArgoCD can deploy applications on the cluster it is installed on, or other external clusters that are authenticated correctly
    Correct! You can link additional clusters to  your Argo CD instance




# How can you install ArgoCD on your cluster?

You must use a Kubernetes manifest from Git

You must use the official Helm chart

You must use a friendly installer such as Argo Autopilot

You can use any of the above including other community methods
    Correct! Your organization might have different requirements according to your use case.




# What is the relationship between the ArgoCD Web interface and the Argo CD Command line executable?

The ArgoCD UI is only for viewing installed applications. You must use the CLI to actually deploy an application.

The ArgoCD UI is only for managers and team leaders. Developers must use the ArgoCD CLI for their needs

The Argo CD UI and the CLI can be used interchangeably according to your needs.
    Correct! Most actions should be available to both interaction methods

The ArgoCD UI can only install applications on the same cluster ArgoCD runs on. Only the ArgoCD CLI can deploy to other clusters.