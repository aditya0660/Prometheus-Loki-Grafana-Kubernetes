install helm in ubuntu

curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash

helm version

++++++++++++++++++++++++++++++++++++++++++++++++++++++

install CSI driver for ec2 instance

helm repo add aws-ebs-csi-driver https://kubernetes-sigs.github.io/aws-ebs-csi-driver
helm repo update

IAM Permissions

{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "ec2:CreateVolume",
        "ec2:AttachVolume",
        "ec2:DetachVolume",
        "ec2:DeleteVolume",
        "ec2:DescribeVolume*",
        "ec2:DescribeInstances"
      ],
      "Resource": "*"
    }
  ]
}

+++++++++++++++++++++++++++++++++++++++++++++++++++++++

helm upgrade --install aws-ebs-csi-driver aws-ebs-csi-driver/aws-ebs-csi-driver \
  --namespace kube-system \
  --set controller.serviceAccount.create=true \
  --set controller.serviceAccount.name=ebs-csi-controller-sa \
  --set storageClasses[0].name=ebs-sc \
  --set storageClasses[0].volumeBindingMode=WaitForFirstConsumer \
  --set storageClasses[0].reclaimPolicy=Delete \
  --set storageClasses[0].allowVolumeExpansion=true \
  --set storageClasses[0].parameters.type=gp2

+++++++++++++++++++++++++++++++++++++++++++++++++++++++

Install Metric server for ec2 instance kubernetes cluster

kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml

kubectl patch deployment metrics-server -n kube-system \
  --type='json' \
  -p='[{"op": "add", "path": "/spec/template/spec/containers/0/args/-", "value":"--kubelet-insecure-tls"}]'


kubectl get deployment metrics-server -n kube-system
kubectl get apiservices | grep metrics
