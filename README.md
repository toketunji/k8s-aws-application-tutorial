# Deploying a Spring Boot application in Kubernetes, on AWS

This tutorial will deploy a sample application in a Kubernetes cluster on AWS.

The application is simple, but have some different parts:
- A Spring Boot application exposing a REST API, to scale on multiple nodes. The application comes from this post [Implementing HAL hypermedia REST API using Spring HATEOAS](https://opencredo.com/hal-hypermedia-api-spring-hateoas/).
- A data store, a standalone H2 server.
- Application's environment-specific configuration and secrets injected using a K8s Secret
- Persistent data stored on a separate EBS volume, so no data loss when the Pod restart.

## Requirements

For running this tutorial you need:

- An AWS account (although most of the infrastructure might be included in the free-tier, some component will not, and running the cluster will cost a few bucks).
- A DockerHub account (this is free)

You also need the following software on your machine:

- Docker (tested with v1.9.1)
- Kubernetes (tested with v1.3.5)
- AWS CLI (also requires Python)
- Maven (to compile the sample Spring Boot application)

## Labs

All steps are entirely manual. Some Kubernetes specs files must be manually modified before using them.

- [docs/build-images.md](Build application and Docker images and publish them to public DocherHub repositories).
- (optional) [docs/local-docker.md](Run containers locally, in Docker)
- [docs/k8s-cluster-aws.md](Create Kubernetes cluster on AWS)
- [docs/launch-application-components.md](Lauch application components in Kubernetes)
- [docs/experiment-persistent-data-volume.md](Experiment: No data is lost when db Pod restart)
- [docs/experiment-update-image.md](Experiment: Update application image)
- [docs/scale-nodes.md](Experiment: Scale up Nodes (Minions))

Please, also read some [docs/notes-about-security.md](note about security).

## Cleanup

To shut down the Kubernetes cluster, we use the provided script:
```
$ kube-down.sh
```

Alternatively, you may keep the cluster running but remove all the component we created:
```
$ kubectl delete deployment frontend h2server
deployment "frontend" deleted
deployment "h2server" deleted

$ kubectl delete svc frontend h2server h2server-ui
service "frontend" deleted
service "h2server" deleted
service "h2server-ui" deleted

$ kubectl delete secret sample-app-cfg
secret "sample-app-cfg" deleted
```

Also, don't forget to remove the EBS Volume we created for H2 data:
```
$ aws ec2 delete-volume --volume-id <volume-id>
```
