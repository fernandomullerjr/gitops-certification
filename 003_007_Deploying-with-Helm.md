# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# Git push

git status
eval $(ssh-agent -s)
ssh-add /home/fernando/.ssh/chave-debian10-github
git add .
git commit -m "Aula 003 - 007 - Deploying with Helm"
git push
git status


# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
#  Deploying with Helm

Helm is a package manager for Kubernetes. It’s a convenient way for packaging collections of YAML files with a Helm chart for the Kubernetes application and allowing distribution with a Helm repository.

When using Helm, there are some powerful commands you will find useful.

To install a new package:

helm install release_name and name_of_chart_you_want_to_install

The chart install performs the Kubernetes deployment and service creation of the application. The chart is essentially a collection of files used to describe a set of Kubernetes resources, and Helm manages the creation of these resources.

To confirm the deployment and view the currently deployed release:

helm list

or

helm ls

To view revision numbers for a particular release:

helm history release_name

To execute a rollback:

helm rollback release_name revision_id

To upgrade a release:

helm upgrade release_name chart_name

To learn more details about these commands, visit Helm’s official documentation.

Something important to note is that Argo CD provides native support for Helm, meaning you can directly connect a packaged Helm chart and Argo CD will monitor it for new versions. When this takes place, the Helm chart will no longer function as a Helm chart and instead, is rendered with the Helm template when Argo is installed, using the Argo CD application manifest.

Argo CD then deploys and monitors the application components until both states are identical. The application is no longer a Helm application and is now recognized as an Argo app that can only operated by Argo CD. Hence if you execute the helm list command, you should no longer be able to view your helm release because the Helm metadata no longer exists.

Here’s an example of the command output. As you can see, the Argo CD application is NOT detected as a Helm application:
