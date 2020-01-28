# Contents
* cluster.rkestate - the state file rke creates.  You'll want to keep this around.
* cluster.yml - the file we defined for rke to build as our cluster.  In it we define what nodes to exist, what ssh key to use to connect, and what kubernetes role each node plays.
* kube_config_cluster.yml - the file rke spits out, useful for feeding to kubectl to control the cluster like so:
<code>kubectl --kubeconfig kube_config_cluster.yml get pods</code>

# To start the k8s cluster
1. Open the SRE Keepass and grab the id_rsa and id_rsa.pub from the rancher user's entry.
2. <code>rke up --config cluster.yml</code>

This will use the ssh key to connect to the nodes in the cluster.yml and provision them.

# Installing Rancher into the K8s cluster
1. <code>helm repo add rancher-stable https://releases.rancher.com/server-charts/stable</code>
2. <code>kubectl --kubeconfig kube_config_cluster.yml create namespace cattle-system</code>
3. Copy the Cluster Ingress cert from the keepass so that you have the tls.cert and tls.key files.  Then create the secret in k8s.  <code>kubectl -n cattle-system create secret tls tls-rancher-ingress --cert=tls.crt --key=tls.key</code>
4. Install Rancher <code>helm install rancher rancher-stable/rancher \
  --namespace cattle-system \
  --set hostname=chqrancher.clearent.net \
  --set ingress.tls.source=secret</code>