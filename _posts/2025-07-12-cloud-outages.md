---
layout: default
author: Fulton Byrne
title: "Thinking about Cloud Outages"
---

I recently realized I subconciously hold a naïve belief that I wanted to correct: "the clouds never or rarely go out". In reality the clouds _do_ suffer major downtime. When the clouds go out, conversations online center on reliability and resilience. Sometimes to an _extreme_ degree. This made me curious to correct my own bias, and understand the problem of cloud outages more deeply to see if I could identify cost-effective strategies to outcompete complex and expensive strategies such as failover.


# Why is this problem important?

Imagine an online shop. Every second down means sales lost. A large retailer may lose significant revenue during a 2 hour window. Conversely, a retailer who continues to operate, when their competitors do not, gains a significant advantage. I recommend reading industry articles covering the cost of downtime:

1. [_Atlassian: Calculating the Cost of Downtime_](https://www.atlassian.com/incident-management/kpis/cost-of-downtime)
2. [_N2W: Solutions - Disaster Recovery_](https://n2ws.com/solutions/disaster-recovery)
3. [_Solarwinds Pingdom: Average Cost of Downtime per Industry_](https://www.pingdom.com/outages/average-cost-of-downtime-per-industry/)
4. [_Splunk: The Hidden Costs of Downtime_](https://www.splunk.com/en_us/campaigns/the-hidden-costs-of-downtime.html)

Practicing good media literacy one must note that most of these articles come from companies attempting to _sell_ a solution. A generally very _expensive_ solution. At the same time the data provides powerful context that systems engineers should acknowledge.

# What they want to sell.
Generally, these companies leverage uncertainty to sell a very complex and expensive solution: multi-region and even multi-cloud failover. Regional failover aligns well with the [3-2-1 backup rule](https://www.veeam.com/blog/321-backup-rule.html) specifically the _1 offsite backup_. Most clouds do provide the necessary functionality to make regional failover accessible. What would such a failover look like?

1. (Prerequisite) Design network architecture to support multiple regions in one network.
2. Stop processing in region A.
3. Spin up new instances in region B using a backup.
5. Reroute DNS to region B.
4. Spin up new application instances in region B.

However, when Cloud APIs go down this well planned recovery plan may fail. How to change DNS when the cloud DNS API returns 503? Additionally, the 3-2-1 offsite backup rule mainly protects against ransomware attacks and not temporary outages.

Making matters worse the complexity of failover routines may actually create a much more severe incident such as when GitHub suffered a _24 hour_ outage due to a 43 second network partition that triggered a failover ([_GitHub: October 21 post-incident analysis_](https://github.blog/news-insights/company-news/oct21-post-incident-analysis/)).

## Cross Cloud Failover
I plan to move quickly through this topic, but crosscloud failover faces all of the challenges of regional failover with much higher cost and complexity. However, many database providers offer multi-cloud clusters:

1. [_Red Panda - High availability deployment: multi-region stretch clusters in RedPanda_](https://www.redpanda.com/blog/multi-region-stretch-clusters)
2. [_MongoDB Atlas - What is Multi-Cloud?_](https://www.mongodb.com/resources/basics/multicloud)
3. [_CockRoach Labs - What is a multi-cloud database, and how to deploy one_](https://www.cockroachlabs.com/blog/multi-cloud-deployment/)

Most "multi-cloud" material overlook a critical point: databases generally require a "majority" of nodes to elect a primary or at least `(n/2) + 1`. So if one provisions 5 nodes and put 3 in cloud A and 2 in cloud B then if cloud A goes down the cluster goes read only. This arrangement creates a 50/50 chance of withstanding a cloud provider failure. Supporting _seamless_ cross cloud failover requires an _odd_ number of cloud providers. Several of these articles hint at requiring an odd number of cloud providers and MongoDB's documentation points to an odd number of cloud providers as a key requirement to withstanding a cloud failure:

> Point of Failure: Cloud Provider
> Minimum of one node set in all three cloud providers. More than one node per region. (_MongoDB Atlas - Configure High Availability and Workload Isolation_](https://www.mongodb.com/docs/atlas/cluster-config/multi-cloud-distribution/#configure-high-availability-and-workload-isolation))

Given the need to support managing infrastructure across _at least_ 3 clouds and the ability to seamlessly transition traffic between each cloud, expect exorbitant upfront investment and ongoing maintenance costs. Companies still _do_ pursue multi-cloud and this provides them an advantage, but I assume they heavily studied the cost versus value tradeoff before committing to such a large investment.

# Analyzing Cloud Incidents

A savvy architect seeking to protect against cloud failures and aware of the ["Double Diamond Design Process"](https://www.designcouncil.org.uk/our-resources/the-double-diamond/) would first dive into understanding the problem that triggers the desire for a particular solution. I also want to correct my bias around cloud outages so inspecting the status pages of different clouds should provide good data to generate good insights into the problem.

While [AWS](https://health.aws.amazon.com/health/status), [Azure](https://azure.status.microsoft/en-us/status/), and [Oracle](https://ocistatus.oraclecloud.com/#/) all provide status pages, only Google provides a [JSON feed](https://status.cloud.google.com/incidents.json) that allows customers to analyze incidents. While the JSON feed allows for greater scrutiny of Google Cloud incidents, I commend Google for making this information so accessible to customers. Every cloud should follow suit as the information allows customers to architect better against cloud failures.

Between July 2024 and June 2025 Google Cloud experienced 95 incidents (roughly 7 per month!). An unexpected number to correcy my bias. If one compares the number against their personal experience the incident count probably feels _wrong_. I encourage examining the data which will reveal good insights: most incidents relate to particular "2nd level" services and not _core_ GCP infrastructure such as networking, compute, and storage. Many incidents also related to trivial matters such as opening support tickets. So incidents happen frequently, but rarely impact the wider user base.

My analysis indicated only _three_ incidents during that time period would impact a service using only _core_ infrastructure. These generally affect provisioning new network, compute, or storage. The longest of which, affecting storage, lasted ~8 hours. This provokes the questions: does your service depend on storage and what impact would an inability to provision more storage cause? Would you do better to spend continuously on cross cloud failover maintenance or experience short term business impacts.

Take [Redpanda's response to the June 12th Google Cloud Outage](https://www.redpanda.com/blog/gcp-outage-june-redpanda-cloud). I carefully read the post many times to understand the response and the key insight is that the Redpanda team simply waited out the incident. Redpanda's main effort centered around establishing communication with their customers. Yet from a technical perspective the Redpanda team rode the incident out successfully. Why? Because on top of Redpanda's ["cell-based"](https://www.redpanda.com/blog/gcp-outage-june-redpanda-cloud#strengths-that-played-in-our-favor) design, databases generally scale slowly and overprovision resources to handle spike loads. Meaning: Redpanda and their customer base did not _depend_ on the cloud provider for the duration of the incident. The article does mention certain customers suffered during the incident because they required new nodes, but is cross cloud failover the answer? Perhaps simply running more replicas and monitoring disk usage would suffice.

# Conclusion

Examining data allows us to generate insights that lead to more practical improvements for the majority of incidents. The very interesting insight from this example is that one should not only examine their service's dependencies, but also how _often_ a service depends on the dependency. I suggest service teams list their dependencies and identify how long their service could survive an outage. 

What then would an incident requiring cross cloud failover look like? Perhaps when Google deleted the infrastructure and data for an [entire pension fund](https://www.axios.com/2024/05/30/google-cloud-pension-error). Then keeping data in another cloud would provide significant value. Yet, Google eventually restored the pension fund's data so perhaps a simple solution existed.

# Further Reading
The topic of failover _implementation_ is pretty interesting so I wanted collect some articles regarding different implementations.

1. [_Netflix: Active-Active for Multi-Regional Resiliency_](https://netflixtechblog.com/active-active-for-multi-regional-resiliency-c47719f6685b)
2. [_Uber: Engineering Failover Handling in Uber's Mobile Networking Infrastructure_](https://www.uber.com/blog/eng-failover-handling/)
3. [_Stack Overflow: Failing over without falling over_](https://stackoverflow.blog/2020/10/23/adrian-cockcroft-aws-failover-chaos-engineering-fault-tolerance-distaster-recovery/)
4. [_Amazing CTO: Our Fetish with Failover and Redundancy_](https://www.amazingcto.com/our-fetisch-with-failover-and-high-availability/)
5. [_Increment - The process: implementing Yelp's failover strategy_](https://increment.com/reliability/yelp-traffic-failover-strategy/)
6. [_Shopify: An Introduction to DNS Traffic Management_](https://shopify.engineering/introduction-dns-traffic-management)
