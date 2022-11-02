### Generate secrets to pushing to external registry

Set the required env vars to simplify the process:

1. **This example if for quay.io**
```bash
export CONTAINER_REGISTRY_SERVER='quay.io' 
export CONTAINER_REGISTRY_USER='<your registry user>'
export CONTAINER_REGISTRY_PASSWORD='<your registry user password>'
```

2. **Create the required secret**
```bash
oc create secret -n [your namespace] docker-registry quay-registry-secret \
  --docker-server=$CONTAINER_REGISTRY_SERVER \
  --docker-username=$CONTAINER_REGISTRY_USER \
  --docker-password=$CONTAINER_REGISTRY_PASSWORD
```

3. **Add the secret to the `build-bot` service account**
```bash
apiVersion: v1
kind: ServiceAccount
metadata:
  name: build-bot
secrets:
  - name: ssh-key
  - name: quay-registry-secret
```
