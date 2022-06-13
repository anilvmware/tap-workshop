# TKO Workshop
TKO workshop Logical Diagram
![](./images/TKOWorkshop.png)


### TKO Workshop Scope
#### **In Scope TKO Bundle Products**
- TKGm
- TMC
- TSM
- TO
- Application Transformer (discussion in progress)
  
#### **Out of Scope TKO Bundle Products**
- vSphere with Tanzu
- AVI
- TKGI

### TKO Workshop Setup Process
Setting up TKO Workshop is 3 step process:
- Setting up TKO workshop hosting environment and installing/configuring learning center
- Developing Workshop content
- Setting up TKO workshop execution/run environment

Let's take a closer look at what is involved in each steps:

**Setting up TKO workshop hosting environment and installing/configuring learning center**
- Setup an AKS Cluster
- Install TAP 1.1 as it also brings learning center package
- Create a Secret for workshop Ingress domain certificate in `learningcenter` namespace. You need to have certificate pem file and private key. e.g.
```sh
kubectl create secret tls tkoworkshop.tanzupartnerdemo.com-tls --cert fullchain.pem --key privkey.pem -n learningcenter
```
- Update `learningcenter` deployment with newly created secret
```sh
kubectl set env deployment/learningcenter-operator -n learningcenter INGRESS_SECRET=tkoworkshop.tanzupartnerdemo.com-tls
```
- Update `learningcenter` package with following values in `tap-values.yaml` file
```sh
learningcenter:
  ingressDomain: "tkoworkshop.tanzupartnerdemo.com"
  ingressSecret:
    secretName: tkoworkshop.tanzupartnerdemo.com-tls
  imageRegistry:
    username: "registry-username"
    password: "registry-password"
    host: "registry-host"
  prepullImages: tanzudemoreg.azurecr.io/taptsm:1.4 # Here we can define the list of images to be pre-pulled on nodes to speed up workshp creation
```

**Developing Workshop content**

Refer any TKO module repository to understand the content

- [Module 1 - TKG](https://github.com/Tanzu-Partner-SE/TKG-workshop-module1)

- [Module 2 - TMC](https://github.com/Tanzu-Partner-SE/tmc-tko-lc)

- [Module 3 - TSM](https://github.com/Tanzu-Partner-SE/tsm-tko)

- [Module 4 - TO](https://github.com/Tanzu-Partner-SE/TKO-TO-Workshop)

You will notice that there is a `Dockerfile` in each module and it is used to create a container image required for particular module run. Each `Dockerfile` is based on TAP package and additional packages etc. are added based on module requirement.

Once `Dockefile` is updated, build a container image and push it to the container registry.


**Setting up TKO workshop execution/run environment**

This task is only needed for TSM module as the required clusters should be pre-created for each participant. Contant workshop owner for more detail.


### TKO Workshop Launch Steps
Finally, we need to launch the workshop.

Follow the below steps to run TKO workshop using Learning Center.

***Steps to follow***
- Update the `GitHub PAT` in each workshop yaml files before applying them
- Ensure that the certificate and image registry secrets are available on the cluster
- learning center deployment pod is labeled with tls secret
- Update `workshop.yaml` files with an appropriate image name, tag and other parameters
- Update the `spec.portal.ingress.hostname` and `spec.portal.ingress.domain` in `training-portal.yaml` file based on the client name.
- Apply the `workshop.yaml` files first
```sh
kubectl apply -f tko-tkg-workshop.yaml -f tko-tmc-workshop.yaml -f tko-tsm-workshop.yaml -f tko-to-workshop.yaml
```
- Apply the `training-portal.yaml` 
```sh
kubectl apply -f training-portal.yaml
```
- Get the training portal url and open it on a browser
```sh
kubectl get trainingportal
```
#### WIP Items
#### Workshop General Questions
* Latest one's are in a separate excel sheet - Ask `Dinesh Tripathi` for access

|**#** |**Topic** |**Comment** |
|-----|--------|----------------
|1|where the base cluster will be hosted | |
|2 |Image registry to store the workshop images. do we know if more than one reg can be used? | |
|3 |Workshop execution Model (Parallel/Standalone) |- With Multiple Partners <br> - with one ?|
|4 |Challanges while using own subscription | |
|5 |Worshop URL | |
|6 |Workshop Ingress Domain | tkoworkshop.tanzupartnerdemo.com|
|7 |Domain Reservation | Dinesh's AWS account usig route53 <br>tanzupartnerdemo.com|
|8 |Access Management (if single base cluster) | |
|9 |SaaS Services Access to the Participants | Dinesh and Srini to finalize|
|10 |Number of Participants |At the moment, it's 10 max |
|11 |Workshop code sharing with partners |Not ready |


