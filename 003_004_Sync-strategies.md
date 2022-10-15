
# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# Git push
git status
eval $(ssh-agent -s)
ssh-add /home/fernando/.ssh/chave-debian10-github
git add .
git commit -m "Aula 03 - Sync Strategies"
git push
git status


# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# Sync Strategies

In the previous sections we created an Argo CD application and chose a manual sync strategy for demonstration purposes. However, Argo CD has many more options when it comes to syncing.

There are 3 parameters that you can change when defining the sync strategy:

    Manual or automatic sync.
    Auto-pruning of resources - this is only applicable for automatic sync.
    Self-Heal of cluster - this is only applicable for automatic sync.


Manual or automatic sync defines what Argo CD does when it finds a new version of your application in Git. If set to automatic, Argo CD will apply the changes then update/create new resources in the cluster. If set to manual, Argo CD will detect the change but will not change anything in the cluster.

Auto-pruning defines what Argo CD does when you remove/delete files from Git. If it is enabled, Argo CD will also remove the respective resources in the cluster as well. If disabled, Argo CD will never delete anything from the cluster.

Self-heal defines what Argo CD does when you make changes directly to the cluster (via kubectl or any other way). Note that doing manual changes in the cluster is not recommended if you want to follow GitOps principles (as all changes should pass from Git). If enabled, then Argo CD will discard the extra changes and bring the cluster back to the state described in Git.

This means that you have several combinations of all the settings as shown in the following table:
Policy
	A
	 B	C
	 D	E
Sync Strategy
	 Manual	Auto
	Auto
	Auto
	Auto
Auto-prune
	N/A
	Disabled
	Enabled
	Disabled
	Enabled
Self-heal
	N/A
	Disabled
	Disabled
	 Enabled	Enabled
Policy A (nothing is done by Argo CD) is probably how you should start adopting Argo CD , especially if you want to apply GitOps on an existing project. This is your chance to see how Argo CD works, without actually affecting your deployments.

Policy B (auto-sync) is the first step towards following GitOps with its automation capabilities. As soon as any change happens in Git, your cluster will auto-update. Disabling auto-prune means that you must still delete resources manually, and disabled self-heal means that you can still make manual changes to the cluster (if you want to have a migration period).

Policies C and D are orthogonal and can act as stepping stones to Policy E, which is the one you should look up to. 
Under that policy, everything is automated both ways. Changes in Git are also reflected automatically to the cluster (including removals of resources), and manual changes in the cluster are simply discarded.

Adopting GitOps in its purest form may require organizational changes or a reexamination of policies and your organization may not be ready at this point. While correcting drift automatically will help organizations get the most out of GitOps, it may take some time to be ready.


