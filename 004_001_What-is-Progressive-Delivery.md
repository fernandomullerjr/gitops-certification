


# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# Git push

git status
eval $(ssh-agent -s)
ssh-add /home/fernando/.ssh/chave-debian10-github
git add .
git commit -m "Aula 004 - 001 - What is Progressive Delivery"
git push
git status


# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# What is Progressive Delivery

Progressive Delivery is the practice of deploying an application in a gradual manner allowing for minimum downtime and easy rollbacks. There are several forms of progressive delivery such as blue/green, canary, a/b and feature flags.
Blue Green Deployments

Blue/Green deployments are one of the simplest ways to minimize deployment downtime. Blue/Green deployments are not specific to Kubernetes and can be used even for traditional applications that reside on Virtual Machines.

    In the beginning, all users of the application are routed to the current version (shown as blue color). A key point is that all traffic passes from a load balancer
    A new version is deployed (shown as green color). This version does not receive any live traffic so all users are still served by the previous/stable version
    Developers can internally test the new color and verify its correctness. If it is valid, traffic is switched to that new version
    If everything goes well, the old version is discarded completely. We are back to the initial state (and order of colors does not matter).


The major benefit of this pattern is that if at any point in time the new version has issues, all users can be switched back to the previous version (via the load balancer). Switching the load balancer is much faster than redeploying a new version, resulting in minimum disruption for existing users.

There are several variations of this pattern. In some cases, the old color is never destroyed but keeps running in the background. You can also keep even older versions online (maybe with a smaller footprint) allowing for easy switching to any previous application revision.
Canary Deployments
Blue/Green deployments are great for minimizing downtime after a deployment, but they are not perfect. If your new version has a hidden issue that manifests itself only after some time (i.e. it is not detected by your smoke tests), then all your users will be affected because the traffic switch is all or nothing.

An improved deployment method is canary deployments. This functions similar to blue/green, but instead of switching 100% of live traffic all at once to the new version, you can instead move only a subset of users.

    In the beginning, all users of the application are routed to the current version (shown as blue color). A key point is that all traffic passes from a load balancer.
    A new version is deployed (shown as green color). This version gets only a very small amount of live traffic (for example 10%).
    Developers can test internally and monitor their metrics to verify the new release. If they are confident, they can redirect more traffic to the new version (for example 33%).
    If everything goes well, the old version is discarded completely. All traffic is now redirected to the new version. We are back to the initial state (and order of colors does not matter).
    The major benefit of this pattern is that if at any point in time the new version has issues, only a small subset of live users are affected. And like blue/green deployments, performing a rollback is as easy as resetting the load balancer to send no traffic to the canary version. Switching the load balancer is much faster than redeploying a new version, resulting in minimum disruption for existing users.
    There are several variations of this pattern. The amount of live traffic that you send to the canary at each step as well as the number of steps are user configurable. A simple approach would have just two steps (10%, 100%) while a more complex one could move traffic in a gradual way (10%, 30%, 60%, 90%, 100%).


Note that canary deployments are more advanced than blue/green ones and are also more complex to set up. The load balancer is now much smarter as it can handle two streams of traffic at the same time with different destinations of different weights. You also need a way (usually an API) to instruct the load balancer to change the weight distribution of the traffic streams. If you are just getting started with progressive delivery, we suggest you master blue/green deployments first, before adopting canary ones.
