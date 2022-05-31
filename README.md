
---

## Installation

Export variables

    export k3s_username=
    export k3s_master=
    export k3s_worker=
    export k3s_version=v1.22.3+k3s1

Install master and slave

    k3sup install --host=${k3s_master} --user=${k3s_username} --k3s-extra-args="--disable servicelb --disable traefik --disable metrics-server" --k3s-k3s_version=${k3s_version}
    k3sup join --host=$ k3s_worker} --server-host=${k3s_master} --user=${k3s_username} --k3s-k3s_version=${k3s_version}

Move kubeconfig and check flux compatibility 

    mv kubeconfig ~/.kube/config
    flux check --pre

Create namespace and add sops age key

    kubectl create namespace flux-system --dry-run=client -o yaml | kubectl apply -f -\n
    cat ~/.config/sops/age/keys.txt | kubectl -n flux-system create secret generic sops-age --from-file=age.agekey=/dev/stdin

Apply cluster twice due to race conditions

    kubectl apply --kustomize=./cluster/base/flux-system
    kubectl apply --kustomize=./cluster/base/flux-system

---

    sudo echo 8196 > /proc/sys/fs/inotify/max_user_instances
    sudo sysctl -w net.ipv4.conf.all.src_valid_mark=1
