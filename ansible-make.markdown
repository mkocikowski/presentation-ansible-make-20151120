# about

a/b deployments with ansible and make

mkocikowski@gmail.com
http://uspto.io

# what I will do

- quickly:
    - define a/b deployment
    - describe tools (ansible, make)
    - describe the sample project
    - describe the problem
    - describe the solution
- longly:
    - demonstrate the solution (code)

# what is a/b deployment

- two analogous environments
- only one of them receiving prod traffic
- if 'a' is live, deploy to 'b'
- when ready, switch traffic from 'a' to 'b'

# why ansible

- no agent / no 'server'
- idempotent wrappers around ssh calls
- essentially imperative (order matters)
- ...though there are role dependencies

- good at failing (& resuming) gracefuly
- runs jobs in parallel

# why not just ansible

see next slide: why gnu make

# why gnu make

- good for local shell commands
- declarative (dependencies matter)
- cleanup on failure
- already use it for builds, tests
- "infrastructure as code"

- good at failing (& resuming) gracefuly
- runs jobs in parallel

# my project (overview)

http://uspto.io
  
- uspto.io shows who owned what patents when
- ~250GB of semi-structured data
- 75ms 95 percentile for analytic queries
- Google Cloud, Nginx, Elasticsearch, Go

# my project (architecture)

- multiple nodes, some on-demand:
    - expensive ETL process
    - results indexed into Elasticsearch
    - go API + Nginx in front of Elastic

# the way I approach problems

- why
- what
- how

# why (the problem)

- deploy new data without performance hit
- deploy new code with no interrupts
- be able to 'test in production'
- be able to 'rollback'
- save time, money

# what

- use a/b deployments
- have no 'idle' machines
- decouple ET from L
- automate (always the answer, haha)

# how

- everything behind layer 3 load balancer
- orchestration with GNU Make
- provisioning with gcloud cli
- configuration with Ansible

# aside: the 'elevator test'

- elevators are very robust systems:
    - multiple inputs (buttons)
    - inputs accepted in any order
    - at any cadence (simultaneous, repeat)
    - regardless of state (rest, in transit)
    - despite timeouts (hold doors open)

# steps for my a/b deploy

- create nodes (make)
- configure (ansible)
+ build and deploy code (ansible)
+ smoke test the system (make)
- switch a/b (make)

# how I use ansible

- no tower, no galaxy
- simple roles for a specific os version
- strictly for configuration management
- ansible vault (thinking about git crypt)
- roles; --tags, --skip-tags
- static hosts file (names, not IPs)
- use --limit for a subset of hosts

# examples

- 3 examples
- all part of the same Makefile
- shown out of order to make more sense
- ...but doesn't matter because declarative ;)

# example 1: build & deploy api

see code example #1

- make builds api if necessary
- ansible deploys, bounces service

# example 2: create an enviroment

see code example #2

- make provisions
- ansible configures

# example 3: do the a/b switch

see code example #3

- create an environment (example #2)
- build & deploy api (example #1)
- smoke test
- do the switch

# conclusion

- could have done it all with ansible
- but would have needed to *really* want to
- local actions clunky (for me)
- but could use GCE modules
