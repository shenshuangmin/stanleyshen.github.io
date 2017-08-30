# Concourse
There are countless CI options, transitioning from one CI system to another one can be a huge investment depending on the size of your project.

Part of this resk stems from the projects being coupled to the intricacies of theier existing CI software, and learning the new one. Or it's simply the number of potential variables to accidentally change when switching so many builds over.

Concourse's principles reduce the risk of switching to and from Concourse, by encouraging practices that decouple your project from your CI, and keeping all configuration in declarative files that can be checked into version control.

##Installing
There are ways to install Concourse, we can refer to [https://concourse.ci/installing.html](https://concourse.ci/installing.html) for detail information.

* Local VM with Vagrant
* Standalone Binary
* Docker repository with docker-compose
* Clusters with BOSH

## Concourse Architecture
Concourse is a fairly simple distributed system built up from the following components. 

* ATC: web UI and build scheduler
	* The ATC is the heart of Concourse. It runs the web UI and API and is responsible for all pipeline scheduling. It connects to PostgreSQL, which it uses to store pipeline data. (building logs)  
* TSA: worker registration and forwarding
	* TSA is a custom-built SSH server that is used solely for securely registering workers with ATC. The TSA only supports two commands: `register-worker` and `forward-worker`
	* The `register-worker` is used to register a worker directly with the ATC. This should be used if the worker is running in the same(private) networks as the ATC.
	* THE `forward-worker` is used to reverse-tunnel a worker's addresses through the TSA and register the forwarded connection with the ATC. This allows workers running in arbitrary networks to register securely, so long as they can reach the TSA. The TSA default listens on port `2222` and is usually colocated with the ATC and sitting behind a load balancer.  
* Workers: container runtime & cache management
	* Workers are machines running [Garden](https://github.com/cloudfoundry-incubator/garden) and [Baggageclaim](https://github.com/concourse/baggageclaim) servers and registering themselves via the TSA.
	* Workers have no important state configured on their machines, as `everything runs in a container` and thus shouldn't care about what packages are installed on the host.
	* Each worker register itself with Concourse cluster via the TSA.
	* Workers by default listen on port `7777` for Garden and `7788` for Baggageclaim. If they are within a private network reachable by the ATC, they'll probably bind on all addresses `0.0.0.0` and register themselves directly. Otherwise they should bind on `127.0.0.1` and forward themselves through the TSA.

	
## Using Concourse

### 1. Hello world, pipeline
hello.yml

```
jobs:
- name: hello-world
  plan:
  - task: say-hello
    config:
      platform: linux
      image_resource:
        type: docker-image
        source: {repository: ubuntu}
      run:
        path: echo
        args: ["Hello, world!"]
```

#### Tasks
The smallest configurable unit in a Concourse pipeline is a single task. A task can be thought of a function from `inputs` to `outputs` that can either succeed or fail.

Going a bit further, ideally tasks are pure functions: given the same set of input, it should either always succeed with same outputs or always fail. This is entirely up to your script's level of discipline, however. Flaky tests or dependiencies on the internet are the most common source of impurity.

task.yml

```
---
platform: linux

image_resource:
  type: docker-image
  source:
    repository: ruby
    tag: '2.1'

inputs:
- name: my-app

run:
  path: my-app/scripts/test
```
#### Build plan
A build plan is a sequence of steps to execute. These steps may fetch down or update Resources, or execute Tasks.

A new build of the job is scheduled whenever get steps with trigger: true have new versions available.








#### Jobs

#### Pipeline
Together, jobs and resources form a pipeline.

![https://cdn.infoq.com/statics_s1_20170411-0445/resource/news/2016/04/concourse/en/resources/ConcoursePipeline.png](https://cdn.infoq.com/statics_s1_20170411-0445/resource/news/2016/04/concourse/en/resources/ConcoursePipeline.png)

