# Kubeflow on Spot instances 

[![forthebadge](https://forthebadge.com/images/badges/powered-by-responsibility.svg)](https://forthebadge.com)
[![forthebadge](https://forthebadge.com/images/badges/60-percent-of-the-time-works-every-time.svg)](https://forthebadge.com)
[![forthebadge](https://forthebadge.com/images/badges/powered-by-oxygen.svg)](https://forthebadge.com)

[![forthebadge](https://forthebadge.com/images/badges/built-with-love.svg)](https://forthebadge.com)
[![forthebadge](https://forthebadge.com/images/badges/for-you.svg)](https://forthebadge.com)


Config files for setting up Multitenant Kubeflow on AWS with spot instances
Repo contains supporting code for the following blog <link coming soon=>

Check the blog for detailed instructions

## By the end of this tutorial, you will have:
- An EKS cluster with Kubernetes 1.14 on AWS
- Autoscaling with Nodegroup autodiscovery enabled
- GPU nodes
	-  With scale-down-to-zero at no workload
	- Spot Instance purchase enabled by default
- Kubeflow 1.0.1 running on the cluster with only GPU requesting resources running on GPU nodes

# TLDR;
```bash
# setup environment
export ENVIRONMENT=staging
export AWS_PROFILE=<your profile>
source envs/$ENVIRONMENT/variables.sh
# Create cluster
eksctl create cluster -f envs/$ENVIRONMENT/cluster-spec.yml
kubectl cluster-info # to check if the cluster is connected
# set executable
chmod a+x *.sh
# Deploy Kubeflow
./deploy_kubeflow.sh
```

## Prequisites
### AWS
- [CLI Programmatic Access Keys](https://www.youtube.com/watch?v=l9kkdRiDFQw)
	- Keys to manipulate resources on AWS
### CLI
- [eksctl](https://docs.aws.amazon.com/eks/latest/userguide/getting-started-eksctl.html)
	- To create the cluster
-  [aws-cli](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html)
	- eksctl dependency
- [aws-iam-authenticator](https://docs.aws.amazon.com/eks/latest/userguide/install-aws-iam-authenticator.html)
	- eksctl dependency
- [kubectl](https://docs.aws.amazon.com/eks/latest/userguide/install-kubectl.html)
	- To manage the kubernetes cluster
- [helm3](https://helm.sh/docs/intro/install/)
	- To deploy helm charts

## Cluster Spec
The cluster that gets spun up will have the following specs:
- **ng-1**
	- m5a.2xlarge
	- min nodes: 0
	- max nodes: 3
	- vol: 100 GB
- **ng-2**
	- m5a.2xlarge
	- min: 0
	- max: 10
	- vol: 20 GB
- **1-gpu-spot-p2-xlarge**
	- p2.xlarge
	- min nodes: 0
	- max nodes: 10
	- max price: $1.2
- **1-gpu-spot-p3-2xlarge**
	- p3.2xlarge
	- min nodes: 0
	- max nodes: 10
	- max price: $1.2
- **4-gpu-spot-p3-8xlarge**
	- p3.8xlarge
	- min nodes: 0
	- max nodes: 4
	- max price: OnDemand
- **8-gpu-spot-p3dn-24xlarge** -- *Disabled by default*
	- p3dn.24xlarge
	- min nodes: 0
	- max nodes: 1
	- max price: $11
