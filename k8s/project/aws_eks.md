# EKS

```shell
aws configure
```
aws command로 생성한 IAM user의 access key 등록
(IAM User 별도로 생성해야 함)

```shell
aws eks update-kubeconfig --region ap-northeast-2 --name sns-cluster
```