


# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# Git push

git status
eval $(ssh-agent -s)
ssh-add /home/fernando/.ssh/chave-debian10-github
git add .
git commit -m "Aula 004 - 004 - Canaries with Argo Rollouts"
git push
git status


# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# Canaries with Argo Rollouts
Argo Rollouts supports the basic canary pattern described in the previous section, and also offers a wealth of customization options. One of the most important additions is the ability to “test” the upcoming version by introducing a “preview” Kubernetes service, in addition to the service used for live traffic. This preview service can be used by the team that performs the deployment to verify the new version as it gets used by the subset of live users.

Here is the initial state of a deployment. The example uses 4 pods (shown as 22nqx, nqksq, 8bzwh and jtdcc in the diagram).
There are now three Kubernetes services. The rollout-canary-all-traffic service is capturing all live traffic from actual users of the application (internet traffic coming from 20.37.135.240). There is a secondary service called rollout-canary-active. This will always point to the stable/previous version of the software. Finally the third service is called rollout-canary-preview, and this will only route traffic to the canary/new versions. Under normal circumstances all 3 services point to the same version.

Once a deployment starts, a new “version” is created. In the example we have 1 new pod that represents the next version of the application to be deployed (shown as 9wx8w at the top of the diagram).
The important point here is the fact that the service used by live users (called rollout-canary-all-traffic) is routing traffic to both the canary and the previous version. It is not visible in the diagram, but only 10% of traffic is sent to the single pod that hosts the new version, while 90% of traffic goes to the 4 pods of the old version.

The rollout-canary-preview service goes only to the canary pod. You can use this service to examine metrics from the canary or even give it to users that always want to try the new version first (e.g. your internal developers). On the other hand, the rollout-canary-active service always goes to the stable version. You can use that for users who never want to try the new version first or for verifying how something worked in the previous version.

If everything goes well, and you are happy with how the canary works, we can redirect some more traffic to it.
We are now sending 33% of live traffic to the canary (the traffic weights are not visible in the picture). To accommodate for the extra traffic, the canary version now has 2 pods instead of one. This is also another great feature of Argo Rollouts. The amount of pods you have in the canary is completely unrelated to the amount of traffic that you send to it. You can have all possible combinations that you can think of (e.g. 10% of traffic to 5 pods, or 50% of traffic to 3 pods and so on). It all depends on the resources used by your application.

It makes sense of course to gradually increase the number of pods in the canary as you shift more traffic to it.

Having the old version around is a great failsafe, as one can abort the deployment process and switch back all active users to the old deployment in the fastest way possible by simply telling the load balancer to move 100% of traffic back to the previous version.
Two more pods are launched for the canary (for a total of 4), and finally we can shift 100% of live traffic to it. After some time, the old version is scaled down completely (to preserve resources). We are now back to the same configuration as the initial state, and the next deployment will follow the same sequence of events.