# Entando Bundle Tekton pipeline for OCP 4.8 

## Official documentation (READ before following these instructions)

- https://tekton.dev/docs/pipelines/install/
- https://cloud.redhat.com/blog/introducing-openshift-pipelines
- https://developer.entando.com/v7.1/tutorials/getting-started/openshift-install-by-operator.html
- https://developer.entando.com/v7.1/tutorials/


## Prerequisites

- To be able to install these Tekton resources you need to be a member of the Cluster-Admin role.
- OCP 4.8 with Openshift Pipelines (1.52) already installed and configured
- Access to your API OCP cluster to execute commands with `oc`
- An instance of Entando 7.x already installed to the target namespace
- An Entando Bundle
- A git repository containing the Entando bundle's code 
- A secret containing credentials to access to your docker registry to push or pull images
- A secret containing credentials or ssh key to access to your VCS server

## Installation

1. Clone this repostitory ()
