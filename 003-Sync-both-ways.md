
# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# Git push
git status
eval $(ssh-agent -s)
ssh-add /home/fernando/.ssh/chave-debian10-github
git add .
git commit -m "Aula 03 Sync both ways"
git push
git status


# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# 03 Sync both ways

Learn how Argo CD works in both directions

Welcome

First deployment

Deploy a new version

Detecting cluster changes









You need to have a GitHub account for this exercise.

The example application is at https://github.com/codefresh-contrib/gitops-certification-examples/tree/main/simple-app. Fork this repository in your own account
Objectives

In this track, this is what you'll learn:

    How to change the application in Git and redeploy
    How Argo CD detects changes between the cluster and Git
    How to make changes in the cluster and see the diff

Please wait while we boot the VM for you and start Kubernetes.



- Efetuado o Fork:
<https://github.com/fernandomullerjr/gitops-certification-examples>