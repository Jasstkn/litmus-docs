---
id: installation
title: ChaosCenter Cluster Scope Installation
sidebar_label: Installation
---

---

## Prerequisites

Before deploying LitmusChaos, make sure the following items are there

- Kubernetes 1.17 or later

- A Persistent volume of 20GB

  :::note
  Recommend to have a Persistent volume(PV) of 20GB, You can start with 1GB for test purposes as well. This PV is used as persistent storage to store the chaos config and chaos-metrics in the Portal. By default, litmus install would use the default storage class to allocate the PV. Provide this value
  :::

- [Helm3](https://v3.helm.sh/) or [kubectl](https://kubernetes.io/docs/tasks/tools/#kubectl)

## Installation

Users looking to use Litmus for the first time have two options available to them today. One way is to use a hosted Litmus service like [Harness Chaos Engineering SaaS](https://cloud.chaosnative.com/). Alternatively, users looking for some more flexibility can install Litmus into their own Kubernetes cluster.

Users choosing the self-hosted option can refer to our Install and Configure docs for installing alternate versions and more detailed instructions.

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

<Tabs>
  <TabItem value="self-hosted" label="Self-Hosted" default>
    Installation of Self-Hosted Litmus can be done using either of the below methods:
    <li><a href="#install-litmus-using-helm">Helm3</a> chart</li>
    <li><a href="#install-litmus-using-kubectl">Kubectl</a> yaml spec file</li>
    <br/>
    Refer to the below details for Self-Hosted Litmus installation.
  </TabItem>
  <TabItem value="hosted" label="Hosted (Beta)">
    <a href="https://harness.io/">Harness</a> offers a free service for community members which makes getting started with Litmus easy. Create an account to get started. Once logged in, create a new hosted control plane and connect to it via the up CLI. Litmus can be used as a hosted cloud service using <a href="https://cloud.chaosnative.com">Harness Chaos Engineering SaaS</a>. Harness Chaos Engineering SaaS executes your Chaos Scenarios in the cloud by managing all your Chaos Control Plane components, while the Chaos Execution Plane components exist on your Kubernetes cluster as part of an external chaos delegate.
    <br/><br/>
    To get started with Harness Chaos Engineering SaaS, visit <a href="https://cloud.chaosnative.com">Harness Chaos Engineering SaaS</a> and register for free. You can skip the below installation steps.
  </TabItem>
</Tabs>

### Install Litmus using Helm

The helm chart will install all the required service account configuration and ChaosCenter.

The following steps will help you install Litmus ChaosCenter via helm.

#### Step-1: Add the litmus helm repository

```bash
helm repo add litmuschaos https://litmuschaos.github.io/litmus-helm/
helm repo list
```

#### Step-2: Create the namespace on which you want to install Litmus ChaosCenter

- The ChaosCenter can be placed in any namespace, but for this scenario we are choose `litmus` as the namespace.

```bash
kubectl create ns litmus
```

#### Step-3: Install Litmus ChaosCenter

```bash
helm install chaos litmuschaos/litmus --namespace=litmus
```

<span style={{color: 'green'}}><b>Expected Output</b></span>

```
NAME: chaos
LAST DEPLOYED: Tue Jun 15 19:20:09 2021
NAMESPACE: litmus
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
Thank you for installing litmus 😀

Your release is named chaos and its installed to namespace: litmus.

Visit https://docs.litmuschaos.io to find more info.
```

> **Note:** Litmus uses Kubernetes CRDs to define chaos intent. Helm3 handles CRDs better than Helm2. Before you start running a chaos experiment, verify if Litmus is installed correctly.

### **Install Litmus using kubectl**

#### **Install Litmus ChaosCenter**

Applying the manifest file will install all the required service account configuration and ChaosCenter.

```bash
kubectl apply -f https://litmuschaos.github.io/litmus/2.0.0/litmus-2.0.0.yaml
```

---

## **Verify your installation**

#### **Verify if the frontend, server, and database pods are running**

- Check the pods in the namespace where you installed Litmus:

  ```bash
  kubectl get pods -n litmus
  ```

  <span style={{color: 'green'}}><b>Expected Output</b></span>

  ```bash
  NAME                                       READY   STATUS              RESTARTS   AGE
  litmusportal-server-6fd57cc89-6w5pn        1/1     Running              0          57s
  litmusportal-auth-server-7b596fff9-5s6g5   1/1     Running              0          57s
  mongo-0                                    1/1     Running              0          57s
  litmusportal-frontend-55974fcf59-cxxrf     1/1     Running              0          58s
  ```

- Check the services running in the namespace where you installed Litmus:

  ```bash
  kubectl get svc -n litmus
  ```

  <span style={{color: 'green'}}><b>Expected Output</b></span>

  ```bash
  NAME                               TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)                         AGE
  litmusportal-frontend-service      NodePort    10.43.79.17    <none>        9091:31846/TCP                  102s
  litmusportal-server-service        NodePort    10.43.30.54    <none>        9002:31245/TCP,8000:32714/TCP   101s
  litmusportal-auth-server-service   NodePort    10.43.81.108   <none>        9003:32618/TCP,3030:31899/TCP   101s
  mongo-service                      ClusterIP   10.43.227.10   <none>        27017/TCP                       101s
  mongo-headless-service             ClusterIP   None           <none>        27017/TCP                       101s
  ```

---

## **Accessing the ChaosCenter**

To setup and login to ChaosCenter expand the available services just created and copy the `PORT` of the `litmusportal-frontend-service` service

```bash
kubectl get svc -n litmus
```

<span style={{color: 'green'}}><b>Expected Output</b></span>

```bash
NAME                               TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)                         AGE
litmusportal-frontend-service      NodePort    10.43.79.17    <none>        9091:31846/TCP                  102s
litmusportal-server-service        NodePort    10.43.30.54    <none>        9002:31245/TCP,8000:32714/TCP   101s
litmusportal-auth-server-service   NodePort    10.43.81.108   <none>        9003:32618/TCP,3030:31899/TCP   101s
mongo-service                      ClusterIP   10.43.227.10   <none>        27017/TCP                       101s
mongo-headless-service             ClusterIP   None           <none>        27017/TCP                       101s
```

> **Note**: In this case, the PORT for `litmusportal-frontend-service` is `30385`. Yours will be different.

Once you have the PORT copied in your clipboard, simply use your IP and PORT in this manner `<NODEIP>:<PORT>` to access the Litmus ChaosCenter.

For example:

```yaml
http://172.17.0.3:30385/
```

> Where `172.17.0.3` is my NodeIP and `30385` is the frontend service PORT. If using a LoadBalancer, the only change would be to provide a `<LoadBalancerIP>:<PORT>`. [Learn more about how to access ChaosCenter with LoadBalancer](../user-guides/setup-without-ingress.md#with-loadbalancer)

You should be able to see the Login Page of Litmus ChaosCenter. The **default credentials** are

```yaml
Username: admin
Password: litmus
```

<img src={require('../assets/login.png').default} width="800" />

By default you are assigned with a default project with Owner permissions.

<img src={require('../assets/landing-page.png').default} width="800" />

## **Verify Successful Registration of the Self Chaos Delegate**

Once the project is created, the cluster is automatically registered as a chaos target via installation of [Chaos Delegate](resources.md#chaosagents). This is represented as [Self Chaos Delegate](resources.md#types-of-chaosagents) in [ChaosCenter](resources.md#chaoscenter).

```bash
kubectl get pods -n litmus
```

```bash
NAME                                     READY   STATUS    RESTARTS   AGE
chaos-exporter-547b59d887-4dm58          1/1     Running   0          5m27s
chaos-operator-ce-84ddc8f5d7-l8c6d       1/1     Running   0          5m27s
event-tracker-5bc478cbd7-xlflb           1/1     Running   0          5m28s
litmusportal-frontend-97c8bf86b-mx89w    1/1     Running   0          15m
litmusportal-server-6fd57cc89-6w5pn      1/1     Running   0          15m
litmusportal-auth-server-7b596fff9-5s6g5 1/1     Running   0          15m
mongo-0                                  1/1     Running   0          15m
subscriber-958948965-qbx29               1/1     Running   0          5m30s
workflow-controller-78fc7b6c6-w82m7      1/1     Running   0          5m32s
```

## Resources

<iframe width="560" height="315" src="https://www.youtube.com/embed/rOrKegj5ePI" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## Learn more

- [Install ChaosCenter in Namespace Scope](../user-guides/chaoscenter-namespace-scope-installation.md)
- [Connect External Chaos Delegates to ChaosCenter](../user-guides/chaosagents-installation.md)
- [Setup Endpoints and Access ChaosCenter without Ingress](../user-guides/setup-without-ingress.md)
- [Setup Endpoints and Access ChaosCenter with Ingress](../user-guides/setup-with-ingress.md)
