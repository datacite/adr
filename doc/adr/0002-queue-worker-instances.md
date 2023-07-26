# 0002-queue-worker-instances

## Status

Accepted

## Date

2023-07-26

## Context

The application instances of the client-api i.e. [Lupo](https://github.com/datacite/lupo) handle everything from website requests to queue processing. 
This can cause an issue where the processing of large amounts of queue messages can affect the stability of the instances serving web requests.

## Decision

The goal of this change is to have the web worker seperate to the queue workers. 
The web workers will now only handle web requests and push new messages onto the message queues as required.
The queue workers will be new instances except will have no web traffic pushed to them and will only be processing messages from the queue system.

The code base does not change aside from a new environment variable to be able to disable queue message processing, this allows instances to be run in a state where they will not run the shorokyun queue processing worker.

On AWS ECS via Terraform in Mastino a new Container instance will be spun up that does not receive web requests via the load balancer. Existing containers will have the queue processing disabled.

See also: [datacite/datacite#1848](https://github.com/datacite/datacite#1848)

## Consequences

### Positive

The web workers should be able to continue to serve requests even if the queue-workers are overloaded.
It also allows potential to scale queue workers only and leave web workers alone, potentially reducing costs as well as traffic will be more easily shaped.

### Negative

Slight increase in complexity of understanding which parts of the services do what.

ADR Number: 0002

