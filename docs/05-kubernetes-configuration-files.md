# Generating Kubernetes Configuration Files for Authentication

In this lab you will generate [Kubernetes client configuration files](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/), typically called kubeconfigs, which configure Kubernetes clients to connect and authenticate to Kubernetes API Servers.

## Client Authentication Configs

In this section you will generate kubeconfig files for the `kubelet` and the `admin` user.

### The kubelet Kubernetes Configuration File

When generating kubeconfig files for Kubelets the client certificate matching the Kubelet's node name must be used. This will ensure Kubelets are properly authorized by the Kubernetes [Node Authorizer](https://kubernetes.io/docs/admin/authorization/node/).

> The following commands must be run in the same directory used to generate the SSL certificates during the [Generating TLS Certificates](04-certificate-authority.md) lab.

Create folder to store .kubeconfig files
```bash
mkdir kubeconfig
```

Generate a kubeconfig file for the node0 worker node:

```bash
for host in node01 node02; do
  kubectl config set-cluster kubernetes-the-hard-way \
    --certificate-authority=pki/ca/ca.crt \
    --embed-certs=true \
    --server=https://controlplane.kubernetes.local:6443 \
    --kubeconfig=kubeconfig/${host}.kubeconfig

  kubectl config set-credentials system:node:${host} \
    --client-certificate=pki/certs/${host}.crt \
    --client-key=pki/certs/${host}.key \
    --embed-certs=true \
    --kubeconfig=kubeconfig/${host}.kubeconfig

  kubectl config set-context default \
    --cluster=kubernetes-the-hard-way \
    --user=system:node:${host} \
    --kubeconfig=kubeconfig/${host}.kubeconfig

  kubectl config use-context default \
    --kubeconfig=kubeconfig/${host}.kubeconfig
done
```

Results:

```text
node01.kubeconfig
node02.kubeconfig
```

### The kube-proxy Kubernetes Configuration File

Generate a kubeconfig file for the `kube-proxy` service:

```bash
{
  kubectl config set-cluster kubernetes-the-hard-way \
    --certificate-authority=pki/ca/ca.crt \
    --embed-certs=true \
    --server=https://controlplane.kubernetes.local:6443 \
    --kubeconfig=kubeconfig/kube-proxy.kubeconfig

  kubectl config set-credentials system:kube-proxy \
    --client-certificate=pki/certs/kube-proxy.crt \
    --client-key=pki/certs/kube-proxy.key \
    --embed-certs=true \
    --kubeconfig=kubeconfig/kube-proxy.kubeconfig

  kubectl config set-context default \
    --cluster=kubernetes-the-hard-way \
    --user=system:kube-proxy \
    --kubeconfig=kubeconfig/kube-proxy.kubeconfig

  kubectl config use-context default \
    --kubeconfig=kubeconfig/kube-proxy.kubeconfig
}
```

Results:

```text
kube-proxy.kubeconfig
```

### The kube-controller-manager Kubernetes Configuration File

Generate a kubeconfig file for the `kube-controller-manager` service:

```bash
{
  kubectl config set-cluster kubernetes-the-hard-way \
    --certificate-authority=pki/ca/ca.crt \
    --embed-certs=true \
    --server=https://controlplane.kubernetes.local:6443 \
    --kubeconfig=kubeconfig/kube-controller-manager.kubeconfig

  kubectl config set-credentials system:kube-controller-manager \
    --client-certificate=pki/certs/kube-controller-manager.crt \
    --client-key=pki/certs/kube-controller-manager.key \
    --embed-certs=true \
    --kubeconfig=kubeconfig/kube-controller-manager.kubeconfig

  kubectl config set-context default \
    --cluster=kubernetes-the-hard-way \
    --user=system:kube-controller-manager \
    --kubeconfig=kubeconfig/kube-controller-manager.kubeconfig

  kubectl config use-context default \
    --kubeconfig=kubeconfig/kube-controller-manager.kubeconfig
}
```

Results:

```text
kube-controller-manager.kubeconfig
```


### The kube-scheduler Kubernetes Configuration File

Generate a kubeconfig file for the `kube-scheduler` service:

```bash
{
  kubectl config set-cluster kubernetes-the-hard-way \
    --certificate-authority=pki/ca/ca.crt \
    --embed-certs=true \
    --server=https://controlplane.kubernetes.local:6443 \
    --kubeconfig=kubeconfig/kube-scheduler.kubeconfig

  kubectl config set-credentials system:kube-scheduler \
    --client-certificate=pki/certs/kube-scheduler.crt \
    --client-key=pki/certs/kube-scheduler.key \
    --embed-certs=true \
    --kubeconfig=kubeconfig/kube-scheduler.kubeconfig

  kubectl config set-context default \
    --cluster=kubernetes-the-hard-way \
    --user=system:kube-scheduler \
    --kubeconfig=kubeconfig/kube-scheduler.kubeconfig

  kubectl config use-context default \
    --kubeconfig=kubeconfig/kube-scheduler.kubeconfig
}
```

Results:

```text
kube-scheduler.kubeconfig
```

### The admin Kubernetes Configuration File

Generate a kubeconfig file for the `admin` user:

```bash
{
  kubectl config set-cluster kubernetes-the-hard-way \
    --certificate-authority=pki/ca/ca.crt \
    --embed-certs=true \
    --server=https://127.0.0.1:6443 \
    --kubeconfig=kubeconfig/admin.kubeconfig

  kubectl config set-credentials admin \
    --client-certificate=pki/certs/admin.crt \
    --client-key=pki/certs/admin.key \
    --embed-certs=true \
    --kubeconfig=kubeconfig/admin.kubeconfig

  kubectl config set-context default \
    --cluster=kubernetes-the-hard-way \
    --user=admin \
    --kubeconfig=kubeconfig/admin.kubeconfig

  kubectl config use-context default \
    --kubeconfig=kubeconfig/admin.kubeconfig
}
```

Results:

```text
admin.kubeconfig
```

## Distribute the Kubernetes Configuration Files

Copy the `kubelet` and `kube-proxy` kubeconfig files to the node-0 instance:

```bash
for host in node01 node02; do
  ssh root@$host "mkdir /var/lib/{kube-proxy,kubelet}"
  
  scp kubeconfig/kube-proxy.kubeconfig \
    root@$host:/var/lib/kube-proxy/kubeconfig \
  
  scp kubeconfig/${host}.kubeconfig \
    root@$host:/var/lib/kubelet/kubeconfig
done
```

Copy the `kube-controller-manager` and `kube-scheduler` kubeconfig files to the controller instance:

```bash
scp kubeconfig/admin.kubeconfig \
  kubeconfig/kube-controller-manager.kubeconfig \
  kubeconfig/kube-scheduler.kubeconfig \
  root@controlplane:~/
```

Next: [Generating the Data Encryption Config and Key](06-data-encryption-keys.md)
