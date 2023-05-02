
Associate the custom `pipelines-scc-userid-1000` SCC with the `build-bot` service account

```bash
oc adm policy add-scc-to-user pipelines-scc-userid-1000 -z build-bot
```
