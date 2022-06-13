# GitOps App of Apps

To use this repo, please make sure that you have `oc` CLI installed
(ideally, have `kustomize` installed as well).

To apply the app of apps, please run:

`oc apply -k cluster-config-manager/base`

## manifests
There are two parts in the `/manifests`: argo CD apps and appProj defined
in `/argocd` and configuration files in `/configs` for the OpenShift cluster.

### argocd
[sealed-secrets](https://github.com/bitnami-labs/sealed-secrets) is used for encrypting all the secrets that are used for each application.
All the argocd `Application` (in /apps) are managed by a root app `cluster-config-manager`. If you want to add more applications to 
the root, you can modify the [kustomization.yaml](https://github.com/StinkyBenji/fluentocp-gitops/blob/main/manifests/argocd/project/cluster-apps/base/kustomization.yaml)
of the `appProj` cluster-apps (defined in [cluster-apps/base](https://github.com/StinkyBenji/fluentocp-gitops/tree/main/manifests/argocd/project/cluster-apps/base))



### configs

1. `/oauth/overlays`: The GitLab idp is configured to use https://gitlab.consulting.redhat.com as an identity provider. The client secret (`gitlab-secret.yaml`) is encrypte using `sealed-secrets`.

2. `/cert-manager/base`: The cert-manager operator is used for managing the API certificates
and the ingress certificate of OpenShift cluster. The certificate issuer is Letsencrypt. As the client
provided a Hetzner server, the [ACNE webhook for using cert-manager with Hetzner API](https://github.com/vadimkim/cert-manager-webhook-hetzner) was
configured (see [the folder](https://github.com/StinkyBenji/fluentocp-gitops/tree/main/manifests/argocd/apps/cert-manager-webhook/base/apps)).
The API token to access the Hetzner API is encryped using `sealed-secrets` and is defined in the `hetzner-secret.yaml`.
Both API and ingress certificates are defined in the `api-certificate.yaml` and `ingress-certificate.yaml`, separately.

3. `/alertmanager`: The configuration of alertmanager for sending notifications to an external receiver (Gchat) is encryped and defined in the `alertmanager-config.yaml`.
The webhook receiver used an open source application [calert](https://github.com/mr-karan/calert). Detailed configuration is shown [here](https://github.com/StinkyBenji/fluentocp-gitops/tree/main/manifests/argocd/apps/calert/base/apps)