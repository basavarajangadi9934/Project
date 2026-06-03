# Project
Deploying Application onto kind cluster, using Jenkins CI and Argocd Pipeline

Installing Jenkins On kind:
---------------------------------------------------------------
1. kubectl create namespace jenkins
2. helm repo add jenkins https://charts.jenkins.io
helm repo update
helm install jenkins jenkins/jenkins -n jenkins
3. kubectl exec --namespace jenkins -it svc/jenkins -c jenkins \
  -- cat /run/secrets/additional/chart-admin-password
4. kubectl port-forward svc/jenkins -n jenkins 8080:8080
5. kubectl port-forward svc/jenkins -n jenkins 8080:8080
