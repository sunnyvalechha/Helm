**Install helm on ubuntu:**

  curl https://baltocdn.com/helm/signing.asc | gpg --dearmor | sudo tee /usr/share/keyrings/helm.gpg > /dev/null
  sudo apt-get install apt-transport-https --yes
  echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/helm.gpg] https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
  sudo apt-get update
  sudo apt-get install helm

Q: Use of Helm?
A: Helm is a package manager for Kubernetes which allow us to install any controller on Kubernetes. Example, prometheus, Grafana, ArgoCD or any application like nginx, httpd. It can be installed through Helm.

* We can Install, Uninstall, Update or package a application as helm chart so that someone else also be able to installation the same application through helm.
* Kubenetes should installed on system but not manadatory.

Q: Repository in Helm?
A: To install any package in our system we must need a repository just like a repository for yum or apt-get. Same way we must have Helm repository which has all the charts. Chart is a bundle or package of the application.

* Bitnami is a popular maintainers of helm repository: https://bitnami.com/

  helm repo add bitnami https://charts.bitnami.com/bitnami
  helm search repo bitnami
  helm install my-release bitnami/<chart>
  helm search repo bitnami | grep prometheus
  





