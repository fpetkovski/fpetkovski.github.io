---
layout: post
title: New Relic alerts as Kubernetes Custom Resources
tags: [kubernetes, new relic, alerting]
# image: '/images/posts/kubernetes-logo.jpeg'
---

Defining alerts through a UI sucks. 

Even though it might be convenient to *just* start clicking buttons on a web interface, over time, manually created alerts suffer the same issues as creating any piece of infrastructure through a web console:
* lack of peer review 
* configuration drift
* hard to ensure cross-environment parity
* hard to re-use alerting rules
* hard to make system-wide alerting changes

and the list goes on.

While most vendors in the monitoring/observability space do provide some means of defining monitors and alerts as code, many of these methods rely on using a particular piece of technology through which you implement alert management. 

The most acceptable solutions are probably the various terraform modules available for vendors like DataDog or NewRelic, but the biggest problem with terraform is the fact that it runs client-side and therefore makes secrets management a risky business. At Personio we rely heavily on New Relic which requires you to use an Admin API Key for defining alerts (ridiculous, I know). Having API keys with high level of access spread across tens of hundreds of services just for the sake of defining alerts seems to be a huge limitation of using terraform for this purpose.

### Alerts as Code
With the rapid increase in the number of microservices being developed at the company, we saw the need to have some sort of an alerting strategy so that we do not have to re-invent the wheel whenever a new service pops up. What we noticed that we lack at the very least are some baseline alerts that come with a new service once it is added to the system. In addition to these defaults, we also wanted each service to be able to either add new alerts, or even change or remove some of the default ones.

Since we use Kubernetes extensively and the developers are good at deploying their services through Kubernetes manifests, we developed an operator that manages New Relic alerts defined as [Kubernetes Custom Resources](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/). This way, all the alerts for a service could live in the same repository as the service itself. In addition, we could package pre-made alerts for each type of service depending on the language and distribute them either through service templates or some other mechanism.

Finally, the code is fully open-sourced on [Github](https://github.com/fpetkovski/newrelic-alert-manager) and you can try out the operator yourselves.