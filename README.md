# Run kPow for Apache Kafka in Kubernetes

This is the Helm Chart for [kPow for Apache Kafka®](https://kpow.io).  

[Helm](https://helm.sh) is the package manager for Kubernetes.

The Operatr IO Helm repository is published at https://charts.operatr.io. 

View the kPow Helm Chart on [ArtifactHUB](https://artifacthub.io/packages/helm/operatr-io/kpow).

## Prerequisites

This Helm chart uses the latest [operatr/kpow](https://hub.docker.com/r/operatr/kpow) container from Dockerhub. 

To run the Dockerhub container requires a license. Start a [free 30-day trial](https://kpow.io/try) of kPow today.

See [kPow on the AWS Marketplace](https://docs.kpow.io/installation/aws-marketplace) to have kPow billed automatically to your AWS account, no license required.   

## Installation

You need to setup your Kubernetes environment before you can install kPow. 

The following examples demonstrate installing kPow in [Amazon EKS](https://aws.amazon.com/eks/).

### Configure Kubernetes/EKS 

```bash
aws eks --region <your-aws-region> update-kubeconfig --name <your-eks-cluster-name>

Updated context arn:aws:eks:<your-aws-region>:123123123:cluster/<your-eks-cluster-name> in /your/.kube/config
```

#### Confirm Kubernetes Cluster Availability

```bash
$ kubectl get svc

NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   12.345.6.7   <none>        443/TCP   28h
```

### Run kPow in Kubernetes

#### Configure the Operatr IO Helm Repository

Add the Operatr IO Helm Repository in order to use the kPow Helm Chart.

```
helm repo add operatr-io https://charts.operatr.io
```

#### Pull the kPow Chart

```
helm pull operatr-io/kpow --untar --untardir .
```

#### Update kPow Configuration

Update [./kpow/templates/kpow-config.yaml](charts/kpow/templates/kpow-config.yaml) with your environment configuration.

See the [kPow Documentation](https://docs.kpow.io) for a full list of configuration options.

The minimum information required by kPow to operate is:

* License parameters
* Kafka Bootstrap URL

#### Start a kPow Instance

```bash
$ helm install my-kpow ./kpow

NAME: my-kpow
LAST DEPLOYED: Mon May 31 16:09:57 2021
NAMESPACE: default
STATUS: deployed
REVISION: 1
NOTES:
1. Get the application URL by running these commands:
  export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/name=kpow,app.kubernetes.io/instance=my-kpow" -o jsonpath="{.items[0].metadata.name}")
  echo "Visit http://127.0.0.1:3000 to use your application"
  kubectl --namespace default port-forward $POD_NAME 3000:3000
```
  
#### Access the kPow UI

Follow the instructions in the notes to configure port forwarding.

```bash
export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/name=kpow,app.kubernetes.io/instance=my-kpow" -o jsonpath="{.items[0].metadata.name}")
echo "Visit http://127.0.0.1:3000 to use your application"
kubectl --namespace default port-forward $POD_NAME 3000:3000
```

kPow is now available on [http://127.0.0.1:3000](http://127.0.0.1:3000).

#### Check the kPow Pod

```bash
$ kubectl describe pods

Name:           kpow-688bbc74-lrm6x
Namespace:      default
Priority:       0
Node:           <none>
Labels:         app.kubernetes.io/instance=kpow
                app.kubernetes.io/name=kpow
                pod-template-hash=688bbc74
Annotations:    kubernetes.io/psp: eks.privileged
Status:         Pending
...
```

#### View the kPow Pod Logs

```bash
kubectl logs kpow-688bbc74-lrm6x

11:36:49.111 INFO  [main] operatr.system ? start kPow
...
...
```

#### Remove kPow

```bash
$ helm delete kpow
```

### Get Help!

If you have any issues or errors, please contact support@operatr.io.