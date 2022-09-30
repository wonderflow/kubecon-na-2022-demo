# KubeVela Demo for KubeCon NA 2022

## Installation of KubeVela with VelaD

1. Create a virtual machine with public IP.

My demo VM OS is Ubuntu 22.04, make sure your VPC has the following ports exposed:

```
Kubernetes port: 6443/6443
Any nodeport for demo range from: 30000/40000
```

2. Install VelaD:

```
curl -fsSl https://static.kubevela.net/script/install-velad.sh | bash -s v1.6.0-alpha.3
```

3. Install KubeVela

```
velad install --bind-ip=47.251.8.82
```

4. Enable VelaUX

```
vela addon enable /root/.vela/addons/velaux serviceType=NodePort
```

5. Get the kubeconfig, you can use it anywhere

```
cat $(velad kubeconfig --name default --external)
```

5. Get the velaux endpoint

```
vela status addon-velaux --endpoint -n vela-system
```

User and password: `admin` / `VelaUX12345`

Display the first app deploy on console.

6. enable o11y addons

```
vela addon enable prometheus-server
```

7. Check the o11y dashboard on UI console

Grafana Default password: `admin` / `kubevela`

8. Extend a component type by writing CUE definitions

```
vela def apply war-definition/java-war.cue
```

