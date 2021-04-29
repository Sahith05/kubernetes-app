Configure the AWS Cloud9 environment
Launch the AWS Cloud9 IDE. In a new terminal session, follow the instructions to configure the AWS Cloud9 environment.
1. Install and configure Kubectl
Install kubectland aws-iam-authenticatoron the AWS Cloud9 Ubuntu machine:
	apt install awscli
curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl

chmod +x ./kubectl

sudo mv ./kubectl /usr/local/bin/kubectl


curl -o aws-iam-authenticator https://amazon-eks.s3.us-west-2.amazonaws.com/1.19.6/2021-01-05/bin/linux/amd64/aws-iam-authenticator
chmod +x ./aws-iam-authenticator
mkdir -p $HOME/bin && cp ./aws-iam-authenticator $HOME/bin/aws-iam-authenticator && export PATH=$HOME/bin:$PATH

echo 'export PATH=$HOME/bin:$PATH' >> ~/.bashrc

aws-iam-authenticator  help

2. Upgrade awscli
	aws configure  	# Provide aws access key and Secret Access Key
	aws --version
pip install awscli --upgrade --user

3. Install eksctl

	curl --silent --location "https://github.com/weaveworks/eksctl/releases/download/latest_release/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
	sudo mv /tmp/eksctl /usr/local/bin

4. Install Helm
	curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3
	chmod 700 get_helm.sh
	./get_helm.sh


Create Amazon EKS clusters
	

1. Create the Production Amazon EKS cluster
	eksctl create cluster --name=demo-project --nodes=1 --region=us-east-2 --write-kubeconfig=false




## Robot Shop Install

Install Stan's Robot Shop on to your K8s cluster using the helm chart, see the [README](helm/README.md) for details of the various options.

```shell
$ cd helm
$ helm install --name robot-shop --namespace robot-shop .
```

## Quotas and Scaling

You can apply resource quotas to the namespace where you installed Stan's Robot Shop.

```shell
$ kubectl -n robot-shop apply -f resource-quota.yaml
```

The quotas and usage are shown in the Instana Kubernetes dashboards.

Optinally you can also run the `autoscale.sh` script to configure automatic scaling of the deployments. You will need to edit the script if you did not deploy to the `robot-shop` namespace. Varying the load on the application will cause Kubernetes to scale up/down the various deployments.

## Istio

Stan's Robot Shop will run on Kubernetes with Istio service mesh. Configure Istio ingress.

```shell
$ kubectl -n robot-shop apply -f Istio/gateway.yaml
```

Now use the exposed Istio gateway to access Robot Shop.

```shell
$ kubectl -n istio-system get svc istio-ingressgateway
```

The above will display the IP address of the Istio gateway.

**NOTE** The Instana agent only works with later versions of Istio.










Cleanup
	eksctl delete cluster --name=demo-project --region=us-east-2
