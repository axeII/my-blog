---
title: "Stop Oracle Cloud powering off your VM on free tier."
date: 2023-04-28T00:09:53+02:00
draft: true
---

Oracle Cloud Infrastructure (OCI) Free Tier is an excellent option for developers and small businesses to test and deploy applications on the cloud without incurring significant costs. However, the Free Tier has specific resource limits and usage restrictions that you should be aware of, especially when it comes to virtual machines (VMs).

## 1) The problem

It all beginnings with an email that I had received from Oracle:

{{< figure src="__GHOST_URL__/content/images/2023/04/image.png" >}}

Oracle has implemented a feature that automatically shuts down any VM that is not using enough processing power or have been idle for a specific period. So if you are like me and host there VM, then yes Oracle can shut it down anytime they feel your VM is "idle". Here are the conditions for reclaiming your VM:

* CPU utilization for the 95th percentile is less than 15%
* Network utilization is less than 15%
* Memory utilization is less than 15%  _(applies to [A1 shapes](https://docs.oracle.com/en-us/iaas/Content/FreeTier/freetier_topic-Always_Free_Resources.htm#Details_of_the_Always_Free_Compute_instance__a1_flex) only)_

If you machine produces less CPU power then specified, Oracle will reclaim your VM by powering it off. I haven't found any information that Oracle would also reclaim storage so yours (and mine) data should be safe for now.

{{< figure src="__GHOST_URL__/content/images/2023/04/7jtacm.jpg" >}}

## 2) Solution?

This sucks, but there is a simple hack to avoid unexpected shutdowns. One option is to deploy on your Oracle VM simple script. First you need to install `stress` utility like this:

```bash
apt-get install stress
```

Example is for ubuntu systems but for any different OS look over [here](https://command-not-found.com/stress). Then create simple bash script like this:

```bash
#!/bin/bash
sleep $(( RANDOM % 3000 ))

# This script generates random stress for 5 times
for i in {1..5}
do
    echo "Running stress test $i"
    stress -c $(( $RANDOM % 2 + 1 )) -t $(( $RANDOM % 60 + 1 ))
done
```

Make it executable and deploy crontab job that for every hour:

```bash
* * * * /path/to/script.sh
```

With this hack you should be fine. I am myself testing this as well and so far I don't have any issues. 🤞
