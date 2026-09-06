wget https://get.helm.sh/helm-v3.14.0-linux-arm64.tar.gz

# 1. Download the ARM64 archive
wget https://get.helm.sh/helm-v3.14.0-linux-arm64.tar.gz

# 2. Unpack the tarball
tar -zxvf helm-v3.14.0-linux-arm64.tar.gz

# 3. Move the binary to your executable path
sudo mv linux-arm64/helm /usr/local/bin/helm

# 4. Verify installation
helm version

helm install prometheus prometheus-community/kube-prometheus-stack \
  --version 45.7.1 \
  --namespace monitoring \
  --create-namespace \
  --kubeconfig /home/devopsadmin/.kube/config

  