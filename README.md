Helm:
* Helm is a package manager for Kubernetes that allows us to install any controller on Kubernetes. Example: Prometheus, Grafana, ArgoCD, or any application like nginx, httpd. It can be installed through Helm.
* We can install, Uninstall, Update, or package an application as a Helm chart so that someone else can also be able to installation the same application through Helm.
* Kubernetes should be installed on the system, but not mandatory.

Helm Repository:
To install any package in our system, we need a repository, just like yum or apt-get. Same way, we must have a Helm repository that has all the charts. A chart is a bundle or package of the application.

Bitnami is a popular maintainer of Helm repository: https://bitnami.com/

Install helm on ubuntu:
    curl https://baltocdn.com/helm/signing.asc | gpg --dearmor | sudo tee /usr/share/keyrings/helm.gpg > /dev/null
    sudo apt-get install apt-transport-https --yes
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/helm.gpg] https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
    sudo apt-get update
    sudo apt-get install helm
Commands:
    helm repo add bitnami https://charts.bitnami.com/bitnami
    helm search repo bitnami
    helm install my-release bitnami/<chart>
    helm search repo bitnami | grep prometheus
    helm install nginxv1 bitnami/nginx
    helm install prometheusv1 bitnami/prometheus
    kubectl get pods        # after installing Prometheus charts, we should get prom alert manager and prom server

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


    






  

  
  





