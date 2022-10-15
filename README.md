# gitops-certification


# About GitOps

GitOps is a set of best practices that make Git the source of truth for everything (and not just the application source code). With GitOps you describe your whole platform (infrastructure and applications) in a declarative format and use Git for storage, history, and auditing of your deployments. Discarding custom deployment scripts and adopting a GitOps approach can help you avoid failed deployments, configuration drift, and fragile releases.
Welcome
How to use the worklabs
About GitOps Champions
What is GitOps
GitOps Use Cases
GitOps pros and cons
Quiz: GitOps theory


# ArgoCD basics

ArgoCD is a platform that follows GitOps specification for Kubernetes deployments. It allows you to describe your applications in a declarative format, it can eliminate configuration drift, and coupled with other Argo projects, it can change the way you work with Kubernetes.
Introduction to Argo CD
Argo CD installation
Live exercise - Argo CD installation
Creating an application
Syncing an application
Live exercise: Create an application
Quiz: Argo CD basics


# Using ArgoCD

Day 2 operations with Argo CD
The reconciliation loop
Live exercise: Sync both ways
Application Health
Sync Strategies
Live exercise: Sync strategies
Secret Management
Live exercise: Secrets with GitOps
Declarative setup
Live exercise: Declarative applications
Deploying with Helm
Live exercise: Helm deployments
Deploying with Kustomize
Live exercise: Kustomize deployments
Quiz: Argo CD Usage


# Progressive Delivery

Progressive delivery is a way to release your applications gradually to a subset of your users allowing for easy rollbacks. Two of the most common forms of Progressive Delivery are blue/green deployments and canary deployments. You will see how to use Argo Rollouts for Progressive Delivery.
What is Progressive Delivery
Introduction to Argo Rollouts
Blue/Green with Argo Rollouts
Live exercise: Blue/Green
Canaries with Argo Rollouts
Live exercise: Canaries
Automated rollbacks with metrics
Using Argo Rollouts with Argo CD
Live exercise: Canaries the GitOps way
Quiz: Progressive Delivery



# Summary and examination

Summary of what you have learned and a brief look at the next course "GitOps as Scale"