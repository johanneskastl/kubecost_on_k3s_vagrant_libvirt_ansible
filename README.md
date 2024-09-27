# kubecost_on_k3s_vagrant_libvirt_ansible

Vagrant-libvirt setup that creates a VM with [k3s](https://k3s.io/), the minimal
lightweight Kubernetes distribution.

On top of k3s, this setup installs [Kubecost](https://kubecost.com/).

There is also an instance of Nginx running in the default namespace, to have
some workload.

Default OS is openSUSE Leap 15.6, but that can be changed in the Vagrantfile.
Please be aware, that this might break the Ansible provisioning.

## Note on the cost model

This setup uses a very simple cost model, where only CPU usage is being
"billed".

Feel free to change the cost model in the `playbook-kubecost_installation.yml`
file in the `ansible` folder. The part you need to modify looks like this:

```
          kubecostProductConfigs:
            customPricesEnabled: true
            defaultModelPricing:
              enabled: true
              # one CPU costs 100$ per month
              CPU: "100.0"
              spotCPU: "0"
              RAM: "0"
              spotRAM: "0"
              GPU: "0"
              spotGPU: "0"
              storage: "0"
              zoneNetworkEgress: "0"
              regionNetworkEgress: "0"
              internetNetworkEgress: "0"
```

## Vagrant

1. You need `vagrant`, obviously. And `git`. And Ansible...
1. Fetch the box, per default this is `opensuse/Leap-15.6.x86_64`, using
   `vagrant box add opensuse/Leap-15.6.x86_64`.
1. Make sure the git submodules are fully working by issuing
   `git submodule init && git submodule update`
1. Run `vagrant up`
1. Open the URL that Ansible printed out at the end of the provisioning. It
   should look something like `http://kubecost.192.168.2.13.sslip.io`.
1. Or you could run a command like the following:

```
kubectl cost \
    namespace \
    --kubeconfig ansible/k3s-kubeconfig \
    --historical \
    --window 5d \
    --show-cpu \
    --show-memory \
    --show-pv \
    --show-efficiency=false
```

1. You can also use `kubectl cost tui --kubeconfig ansible/k3s-kubeconfig` to
   open an experimental text-based UI in your terminal.
1. Party!

## Cleaning up

The VMs can be torn down after playing around using `vagrant destroy`. This will
also remove the kubeconfig file `ansible/k3s-kubeconfig`.

## License

BSD-3-Clause

## Author Information

I am Johannes Kastl, reachable via git@johannes-kastl.de
