
Associate the custom `entando-scc` SCC with the `build-bot` service account

```bash
oc adm policy add-scc-to-user entando-scc -z build-bot
```

Associate the `privileged` SCC with the `build-bot` service account

```bash
oc adm policy add-scc-to-user privileged -z build-bot
```