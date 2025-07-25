**Install helm on ubuntu:**

  curl https://baltocdn.com/helm/signing.asc | gpg --dearmor | sudo tee /usr/share/keyrings/helm.gpg > /dev/null
  sudo apt-get install apt-transport-https --yes
  echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/helm.gpg] https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
  sudo apt-get update
  sudo apt-get install helm

Q: Use of Helm?
A: Helm is a package manager for Kubernetes that allows us to install any controller on Kubernetes. Example: Prometheus, Grafana, ArgoCD, or any application like nginx, httpd. It can be installed through Helm.

* We can install, Uninstall, Update, or package an application as a Helm chart so that someone else can also be able to installation the same application through Helm.
* Kubernetes should be installed on the system, but not mandatory.

Q: Repository in Helm?
A: To install any package in our system, we need a repository, just like a repository for yum or apt-get. Same way, we must have a Helm repository that has all the charts. A chart is a bundle or package of the application.

* Bitnami is a popular maintainer of Helm repository: https://bitnami.com/

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


    






  

  
  





