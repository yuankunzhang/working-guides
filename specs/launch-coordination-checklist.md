# Launch Coordination Checklist

## Architecture

* Architecture sketch, types of servers, types of requests from clients
* Programmatic client requests

## Machines and Datacenters

* Machines and bandwidth, datacenters, N+2 redundancy, network QoS
* New domain names, DNS load balancing

## Volume Estimates, Capacity, and Performance

* HTTP traffic and bandwidth estimates, launch "spike," traffic mix, 6 months out
* Load test, end-to-end test, capacity per datacenter at max latency
* Impact on other services we care most abou†
* Storage capacity

## System Reliability and Failover

* What happens when:
  * Machine dies, rack fails, or cluster goes offline
  * Network fails between two datacenters
* For each type of server that talks to other servers (its backends):
  * How to detect when backends die, and what to do when they die
  * How to terminate or restart without affecting clients or users
  * Load balancing, rate-limiting, timeout, retry and error handling behavior
* Data backup/restore, disaster recovery

## Monitoring and Server Management

* Monitoring internal state, monitoring end-to-end behavior, managing alerts
* Monitoring the monitoring
* Financially important alerts and logs
* Tips for running servers within cluster environment
* Don't crash mail servers by sending yourself email alerts in your own server code

## Security

* Security design review, security code audit, spam risk, authentication, SSL
* Prelaunch visibility/access control, various types of blacklists

## Automation and Manual Tasks

* Methods and change control to update servers, data, and configs
* Release process, repeatable builds, canaries under live traffic, staged rollouts

## Growth Issues

* Spare capacity, 10x growth, growth alerts
* Scalability bottlenecks, linear scaling, scaling with hardware, changes needed
* Caching, data sharding/resharding

## External Dependencies

* Third-party systems, monitoring, networking, traffic volume, launch spikes
* Graceful degradation, how to avoid accidentally overrunning third-party services
* Playing nice with syndicated partners, mail systems, services within Google

## Schedule and Rollout Planning

* Hard deadlines, external events, Mondays or Fridays
* Standard operating procedures for this service, for other services