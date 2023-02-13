# Demo microservice deployment in Kubernetes cluster through Kustomize

Kustomize: kubernetes native configuration management

## Pre-requisits

Install kustomize with GO:

`$ go get sigs.k8s.io/kustomize`

Install kustomize Binaries:
`$ curl -s "https://raw.githubusercontent.com/kubernetes-sigs/kustomize/master/hack/install_kustomize.sh"  | bash`

Install kustomize with Docker:
`$ docker pull k8s.gcr.io/kustomize/kustomize:v3.8.7`
`$ docker run k8s.gcr.io/kustomize/kustomize:v3.8.7 version`

Homebrew / MacPorts
`$ brew install kustomize`
`$ sudo port install kustomize`

Chocolatey
`$ choco install kustomize`

## General

File system approach :

```
└── project-name
     ├── dev
     │   ├── base
     │   │   ├── deployment.yaml
     │   │   ├── kustomization.yaml
     │   │   └── service.yaml
     │   ├── municipality
     │   │   └── monterotondo
     │   │       ├── kustomization.yaml
     │   │       └── patches
     │   │           └── deployment.yaml
     │   ├── province
     │   │   └── roma
     │   │       ├── kustomization.yaml
     │   │       └── patches
     │   │           └── deployment.yaml
     │   └── region
     │       ├── base
     │       │   └── kustomization.yaml
     │       └── lazio
     │           ├── kustomization.yaml
     │           └── patches
     │               └── deployment.yaml
     ├── preprod
     │   ├── base
     │   │   ├── deployment.yaml
     │   │   ├── kustomization.yaml
     │   │   └── service.yaml
     │   ├── municipality
     │   │   └── monterotondo
     │   │       ├── kustomization.yaml
     │   │       └── patches
     │   │           └── deployment.yaml
     │   ├── province
     │   │   └── roma
     │   │       ├── kustomization.yaml
     │   │       └── patches
     │   │           └── deployment.yaml
     │   └── region
     │       ├── base
     │       │   └── kustomization.yaml
     │       └── lazio
     │           ├── kustomization.yaml
     │           └── patches
     │               └── deployment.yaml
     └── prod
         ├── base
         │   ├── deployment.yaml
         │   ├── kustomization.yaml
         │   └── service.yaml
         ├── municipality
         │   └── monterotondo
         │       ├── kustomization.yaml
         │       └── patches
         │           └── deployment.yaml
         ├── province
         │   └── roma
         │       ├── kustomization.yaml
         │       └── patches
         │           └── deployment.yaml
         └── region
             ├── base
             │   └── kustomization.yaml
             └── lazio
                 ├── kustomization.yaml
                 └── patches
                     └── deployment.yaml
```

* In *base* folder we define common resources.
* In overlays folders we define environment specific patches. Into the environment folder we can have, for example, a yaml file called replica_count.yaml or cpu_count.yaml instead of generic name like deployment.yaml.

## Deployment

## Deploy on a specific environment

Dry-run deployment in dev environment:

`$ kustomize build deploy/demo/overlays/dev`

Create/deploy the resources for the dev environment variant:

`$ kustomize build deploy/demo/overlays/dev | kubectl apply -f -`

## Deploy on a specific environment and set the image tag dynamically

If we want to deploy and set the tag used on an image to match an environment variable, run:

`$ kustomize edit set image scraly/test.dockerapp:123456`

(*demo* is our image name in this example)

and then our previous command which build and apply:

`$ kustomize build deploy/demo/overlays/dev | kubectl apply -f -`

*Kustomize edit* command will edit the *kustomization.yaml* file in *deploy/k8s/* folder:

```
...
images:
- name: scraly/test.dockerapp
  newTag: "123456"
```