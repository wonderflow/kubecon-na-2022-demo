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
curl -fsSl https://static.kubevela.net/script/install-velad.sh | bash
```

3. Install KubeVela with public IP, my demo here is `47.251.8.82`, please change to yours as the following command.

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

## Extend KubeVela Component Type

1. Extend a component type by writing CUE definitions

```
vela def apply war-definition/java-war.cue
```

This definition will launch a tomcat service with only war package as user input.

2. Apply the sample app

```
vela up -f war-definition/sample.yaml
```

3. Check the endpoint and visit it

```
vela status kubecon-demo --endpoint
```

Visiting the output url: http://47.251.8.82.nip.io/sample , you'll get the page.

## Multi-cluster Application Delivery

1. Create another cluster with VelaD (without kubevela control plane), assume we have created a new virtual machine with public IP `116.62.199.204`.

```
velad install --cluster-only --bind-ip 116.62.199.204
```

Check and copy the kubeconfig to your local.

Check it by:

```
cat $(velad kubeconfig --name default --external)
```

Copy it to your local, you can also create a file named `remote.con` with the data pasted into it:

```
scp root@116.62.199.204:/etc/rancher/k3s/k3s-external.yaml remote.conf 
```

2. Join the remote cluster into control plane.

```
vela cluster join remote.conf --name remote
```

3. Create an application with multi-cluster topology and workflow steps.

```
vela up -f war-definition/demo2.yaml
```

4. Check the status until all three components are running and workflow suspended.

```
vela status demo2
```

5. Visiting the endpoint in the control plane( local ) cluster.

```
vela status demo2 --endpoint
```

It should be like : http://47.251.8.82.nip.io/order/ .

6. Continue the deploy workflow to remote clusters

```
vela workflow resume demo2
```

7. Check the status again

```
vela status demo2
```

8. After all components in remote cluster ready, you get the endpoint from remote cluster by:

```
vela status demo2 --endpoint
```

It should be like : http://116.62.199.204.nip.io/order .
