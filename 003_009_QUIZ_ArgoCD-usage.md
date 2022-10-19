


# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# Git push

git status
eval $(ssh-agent -s)
ssh-add /home/fernando/.ssh/chave-debian10-github
git add .
git commit -m "Aula 003 - QUIZ ArgoCD usage"
git push
git status


# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# QUIZ


# Is the following statement true or false?
If you have enabled the "auto-sync" option in an Argo CD application and something is changed manually in the cluster, then Argo CD will automatically discard the change.

True

False
    Correct! Unless you have also setup the "self-heal" option, Argo CD will never discard manual changes






# Is the following statement true or false?
If you have enabled the "auto-sync" option in an Argo CD application and you delete a resource in Git, then Argo CD will automatically delete that resource from the cluster as well.

True

False
    Correct! Unless you have also setup the "auto-prune" option, Argo CD will never remove resources from the live cluster





# What is the proper way to handle application secrets via GitOps?

Encrypt them and store them in Git. Then decrypt them during runtime.
    Correct! That is the proper way to handle secrets with GitOps

Put them in configmaps and use kubectl exec to pass the values directly to a pod

Store them in the values file of a Helm chart and commit them to Git

Put them in the Kubernetes Secret resource and make them part of a Helm chart.





# If you use Bitnami Sealed Secrets, then where does encryption and decryption take place?

Encryption happens in the Argo CD CLI. Decryption happens in the Argo CD Web interface

Both encryption and decryption are handled by the Sealed Secrets controller.

Encryption happens via the kubeseal executable. Decryption happens via the Sealed Secrets controller.
    Correct!

Encryption happens by the Sealed Secrets controller. Decryption happens via the Argo CD controller







# You have just logged in the Argo CD UI and created an application using a Git repository that holds your Helm chart. You sync the application, and everything is fine. What is the next step that you should take?

Delete the application from the Argo CD UI and create it again using the Argo CD CLI.

Convert the Helm chart to Kustomize files.

Create a declarative file of the application and other resources (e.g. Argo CD project) used and store them in Git.
    Correct! This way you are following GitOps all the way.

Go into the latest manifest inside the Argo CD Web interface and update the container image to a new version.





# You just created a Helm application using the Argo CD web interface. Now you go the command line and you enter helm list. To your surprise nothing is printed.

The helm command will never work no matter what you do in Argo CD
    Correct! Argo CD uses Helm only as a templating mechanism.

You need to first sync the application using the Argo CD UI before the helm command can work.

You need to enable "auto-sync" in the Argo CD UI before the helm command can work

You need to enable both the self-heal and the "auto-sync" option in the Argo CD UI for the helm command to work




# What kind of applications can Argo CD deploy?

ArgoCD can only deploy plain Kubernetes manifests.

ArgoCD can only deploy Helm applications

ArgoCD can only deploy Kustomize applications

ArgoCD can deploy all of the above
    Correct!