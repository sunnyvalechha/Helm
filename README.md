Helm:

* Helm is a package manager for Kubernetes that allows us to install any controller on Kubernetes. Example: Prometheus, Grafana, ArgoCD, or any application like nginx, httpd. It can be installed through Helm.
* We can install, Uninstall, Update, or package an application as a Helm chart so that someone else can also be able to installation the same application through Helm.
* Kubernetes should be installed on the system, but not mandatory.

Helm Repository:

To install any package in our system, we need a repository, just like yum or apt-get. Same way, we must have a Helm repository that has all the charts. A chart is a bundle or package of the application.

Bitnami is a popular maintainer of Helm repository: https://bitnami.com/

**Note**: Before installing helm, install any kubernetes distribution

Eks Install:

vim kube-eksctl-awscli.sh

        apt install unzip -y
        curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
        unzip awscliv2.zip
        sudo ./aws/install
        aws --version
        
        #!/bin/bash
        set -e
        uname -m  # Should print x86_64
        mkdir -p kubectlbinary && cd kubectlbinary
        curl -O https://s3.us-west-2.amazonaws.com/amazon-eks/1.33.5/2025-09-19/bin/linux/amd64/kubectl
        chmod +x ./kubectl
        mkdir -p $HOME/bin
        cp ./kubectl $HOME/bin/kubectl
        export PATH=$HOME/bin:$PATH
        grep -qxF 'export PATH=$HOME/bin:$PATH' ~/.bashrc || echo 'export PATH=$HOME/bin:$PATH' >> ~/.bashrc
        kubectl version --client
        
        # for ARM systems, set ARCH to: `arm64`, `armv6` or `armv7`
        ARCH=amd64
        PLATFORM=$(uname -s)_$ARCH
        curl -sLO "https://github.com/eksctl-io/eksctl/releases/latest/download/eksctl_$PLATFORM.tar.gz"
        # (Optional) Verify checksum
        curl -sL "https://github.com/eksctl-io/eksctl/releases/latest/download/eksctl_checksums.txt" | grep $PLATFORM | sha256sum --check
        tar -xzf eksctl_$PLATFORM.tar.gz -C /tmp && rm eksctl_$PLATFORM.tar.gz
        sudo install -m 0755 /tmp/eksctl /usr/local/bin && rm /tmp/eksctl
        eksctl version

        source ~/.bashrc
        
        eksctl create cluster --name sunny-eks --region=ap-south-1 --node-type t2.micro --nodes-min 1 --nodes-max 1
        
        eksctl utils associate-iam-oidc-provider --region=ap-south-1 --cluster sunny-eks --approve

Minikube Install:

        apt-get update -y
        apt-get install docker.io -y
        sudo usermod -aG docker ubuntu
        curl -LO https://github.com/kubernetes/minikube/releases/latest/download/minikube-linux-amd64
        sudo install minikube-linux-amd64 /usr/local/bin/minikube && rm minikube-linux-amd64
        minikube start
        alias kubectl="minikube kubectl --"

        minikube start --driver=docker        # Run as a regular user
        alias kubectl="minikube kubectl --"        # Setting alias

Install helm on ubuntu:
    
    curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-4
    chmod 700 get_helm.sh
    ./get_helm.sh

Commands:

    helm repo add bitnami https://charts.bitnami.com/bitnami    # add repo
    helm repo list                               # get repo name
    helm list                                    # get installed package list
    helm search repo | grep mysql                # get specific package
    helm search repo bitnami                     # show all packages in the bitnami repo
    helm search repo mysql                       # list all packages specific to mysql
    helm repo update                             # helm repo update
    helm install <any-name> <package-name>
    helm install sun-nginx bitnami/nginx         # install a package
    helm install -n team2 mysql bitnami/mysql    # install package in a namespace
    helm list -n team2                           # list package in a namespace
    helm uninstall nginx -n team2                # uninstall package in a namespace
    helm list or helm ls                         # list installed package
    helm install -n team2 mydb bitnami/mysql --set auth.rootPassword=test1234        # give password to db user, but this method is not recomended
    vim values.yaml
    auth:
          rootPassword: "test134"
    helm install -n team2 mydb bitnami/mysql --values values.yaml                # recomended way
    kubectl get pods                            # get installed package as a pod
    helm ls --output=yaml                       # get output as yaml
    helm upgrade grafana bitnami/grafana --namespace default --version 12.1.8        # upgrade chart
    helm history grafana
    helm status grafana                        # get overall status of the chart's pod
    

    

    helm install my-release bitnami/<chart>    
    helm search repo bitnami | grep prometheus
    helm install nginxv1 bitnami/nginx
    helm install prometheusv1 bitnami/prometheus
     after installing Prometheus charts, we should get prom alert manager and prom server

Q: What if any chart is not available in Bitnami?
A: We can get through Google > "install aws load balancer controller with helm" >> https://docs.aws.amazon.com/eks/latest/userguide/lbc-helm.html

Step 2: Install AWS Load Balancer Controller

    helm repo add eks https://aws.github.io/eks-charts

    helm repo list
    helm repo update eks

    helm search repo eks    # lists the charts are available in eks

    helm search repo eks | grep -i load

    helm install aws-load-balancer-controller eks/aws-load-balancer-controller \
    -n kube-system \
    --set clusterName=<your-cluster-name> \
    --set serviceAccount.create=false \
    --set serviceAccount.name=aws-load-balancer-controller

**List the installed charts**

    helm list

**Uninstall chart**

    helm uninstall nginxv1


    mkdir -p best-commer/{payments,shipping}
    cd best-commer/
    helm create payments
    helm create shipping

Note: The Above commands will create "Chart.yaml, values.yaml, templates, charts" inside a folder

* Chart.yml - this acts as metadata for charts.
* templates - put the deployments.yml file or stateful sets or daemonsets file, service account, and config maps.

    cd best-commer/payments/templates/
    vim deployment.yaml    # Delete all contents of the deployment.yaml & paste below yml


  apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: {{ .Release.Name }}-{{ .Chart.Name }}
    labels:
      app: {{ .Chart.Name }}
  spec:
    replicas: 1
    selector:
      matchLabels:
        app: {{ .Chart.Name }}
    template:
      metadata:
        labels:
          app: {{ .Chart.Name }}
      spec:
        containers:
          - name: {{ .Chart.Name }}
            image: {{ .Values.image.repository }}:{{ .Values.image.tag }}
            command: ['sh', '-c', 'echo {{ .Values.appMessage }}; sleep 3600']
            imagePullPolicy: {{ .Values.image.pullPolicy }}


* This code is the same as deployment.yml, which we have deleted above, but notice this file has a lot of variables.

  cd /home/ubuntu/best-commer/payments
  vim values.yaml    # Delete all contents of the deployment.yaml & paste below yml
    image:
      repository: busybox
      tag: latest
      pullpolicy: IfNotPresent
    appMessage: "Payments Service"

<img width="301" height="104" alt="image" src="https://github.com/user-attachments/assets/b7ef104b-40f9-4e9c-9cea-a60f0175df4f" />

* Observe that the values.yml file has a variable that deployment.yml requires

  vim Chart.yaml  # update app version

<img width="835" height="367" alt="image" src="https://github.com/user-attachments/assets/bfbc57d2-6ede-4143-a34e-1bc3820cb1ff" />

* Once the shipping folder is also update with required details, now bundle them into a chart.


  helm package payments

<img width="809" height="67" alt="image" src="https://github.com/user-attachments/assets/9896ae55-4f95-405a-a1f9-b60e26237b64" />

* It will create a zip file also called as charts.

  "helm repo index ."

* It will create an index file
* Using this index file, we can add this repo to a Nexus or GitHub with the "helm repo add" command


    






  

  
  





