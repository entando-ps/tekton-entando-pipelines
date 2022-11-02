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
- A git repository containing the Entando bundle's source code 
- A secret containing credentials to access to your docker registry to push or pull images
- A secret containing credentials or ssh key to access to your VCS server

## Installation

### Required steps

1. Clone this repostitory (https://github.com/entando-ps/tekton-entando-pipelines.git)
2. Edit the `build-bot` secret, located at `./common/secrets` directory to match with your VCS's credentials
3. Edit the `container-registry-secret`, located at `./common/secrets` directory to match with your docker registry's credentials
4. Edit the `build-bot` service account, located at `./common/serviceaccounts` directory to match with your target namespace
5. Edit the `role-api-access` role, located at `./common/serviceaccounts` directory to match with your target namespace
6. Edit the `rolebinging-api-access` role-binding, located at `./common/serviceaccounts` directory to match with your target namespace 
7. Start applying all the Tekton resources:

```bash
# Set the correct OCP project
oc project [your target project]

# Install ClusterTasks
oc apply -f ClusterTasks/

# Install all the resources in common/**/** path
for r in common/**/*.yaml; do
  oc apply -f $r;
done

# Install the entando-bundle pipeline
oc apply -f pipelines/
```

### Optional Steps

If you want to have your pipeline started automatically after every commit to your bundle git repo you need to 
configure Tekton Triggers (https://tekton.dev/docs/triggers/).

In this repository you can find an example done with Gitlab inside the `./triggers` directory.

## Execute the firs pipeline

In this repo you can find a working `pipelinerun` example with all the needed parameters. The pipelinerun is located
at `./pipelineruns` directory and is listed here:

```yaml
apiVersion: tekton.dev/v1beta1
kind: PipelineRun
metadata:
  generateName: simple-bundle-
spec:
  serviceAccountName: build-bot
  pipelineRef:
    name: ent-bundle-project
  params:
    - name: ENTANDO_APP_NAME (1)
      value: "ent"
    - name: REPO_URL (2)
      value: git@github.com:cecchisandrone/entando-codemotion-bundle.git
    - name: SUBDIR_PATH (3)
      value: "simple-bundle"
    - name: BRANCH_NAME (4)
      value: main
    - name: CLEAN_WORKSPACE (5)
      value: "true"
    - name: TLSVERIFY (6)
      value: "false"
    - name: CONTEXT_DIR (7)
      value: "./simple-bundle"
    - name: DOCKER_ORG (8)
      value: "p_masala"
    - name: DOCKER_REGISTRY (9)
      value: "quay.io"
  workspaces:
    - name: source
      persistentVolumeClaim:
        claimName: source-pvc
    - name: local-maven-repo
      persistentVolumeClaim:
        claimName: maven-repo-pvc
```
1. `ENTANDO_APP_NAME` parameter is needed to tell `ent` which is the Entando App name to point to retrieve the Keycloak token from.
2. `REPO_URL` is the Bundle's repository which needs to be <u>specified in the SSH form</u>.
3. `SUBDIR_PATH` parameter is used to download the bundle's repository under a specific directory. In this case `simple-bundle`.
4. `BRANCH_NAME` is the branch bundle name to download the code from.
5. `CLEAN_WORKSPACE` parameter is used to delete the previous download code and needs to be set to `true`.
6. `TLSVERIFY` parameter is used when you are using a self-signed TLS certificate.
7. `CONTEXT_DIR` parameter needs to match with the `subdir_path`. Is the path from where the commands will be executed.
8. `DOCKER_ORG` parameter is needed by `ent` and `buildah` steps to generate the correct docker images.
9. `DOCKER_REGISTRY` parameter is needed by `ent` and `buildah` steps to generate the correct docker images.


