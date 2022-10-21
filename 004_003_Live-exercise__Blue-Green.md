

# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# Git push

git status
eval $(ssh-agent -s)
ssh-add /home/fernando/.ssh/chave-debian10-github
git add .
git commit -m "Aula 004 - 003 - Live exercise Blue/Green"
git push
git status


# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# 09 Blue/Green deployments

Blue/Green deployments with Argo Rollouts

Welcome

Install the Argo Rollouts controller

The first deployment

Blue/Green deployments









# Progressive delivery with Argo Rollouts

The example application is at https://github.com/codefresh-contrib/gitops-certification-examples/tree/main/blue-green-app.
Objectives

In this track, this is what you'll learn:

    Installation of the Argo Rollouts controller
    How to convert a Deployment to a Rollout
    How to perform blue/green deployments
    How to monitor the state of the deployment

Please wait while we boot the VM for you and start Kubernetes.