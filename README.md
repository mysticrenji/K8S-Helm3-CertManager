# K3S-Helm3-CertManager

## K3s Installation on the device - Server -
curl -sLS https://get.k3s.io | sh - </br>
curl -sLS https://get.k3s.io | INSTALL_K3S_EXEC="server -tls-san 192.168.1.200 -write-kubeconfig-mode 644" sh - </br>

## Helm 3 installation on the the device

curl https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash </br>
kubectl create namespace cert-manager </br>

## Applying Cert-manager manifest on the device V0.14
kubectl apply -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.14/deploy/manifests/00-crds.yaml </br>
helm install cert-manager jetstack/cert-manager -n cert-manager --version v0.14.1  </br>

## Fixing known issues in Cert-manager V0.14
kubectl delete mutatingwebhookconfiguration.admissionregistration.k8s.io cert-manager-webhook </br>
kubectl delete validatingwebhookconfigurations.admissionregistration.k8s.io cert-manager-webhook </br>

namespace="not-cert-manager" </br>
curl -s -L "https://github.com/jetstack/cert-manager/releases/download/v0.14.1/cert-manager.crds.yaml" 2>&1 | sed -e "s/namespace: cert-manager/namespace: ${namespace}/" -e "s/cert-manager.io\/inject-ca-from-secret: cert-manager\/cert-manager-webhook-tls/cert-manager.io\/inject-ca-from-secret: ${namespace}\/${namespace}-cert-manager-webhook-tls/" |  kubectl apply --validate=false -f -
