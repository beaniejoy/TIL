# kube cluster config

로컬에서 kube cluster 설정 방법

```shell
$ kubectl config set-credentials [credential_user_name] --token=[token]
$ kubectl config set-cluster [cluster_name] --server [control_plane_node_url]
$ kubectl config set-context [context_name] --cluster=[cluster_name] --user=[credential_user_name]
$ kubectl config use-context [context_name]
```
- set cluster시 `--insecure-skip-tls-verify=true` option 추가 가능

```shell
$ kubectl config current-context
```
현재 kube cluster context가 어떤 것인지 확인 가능
