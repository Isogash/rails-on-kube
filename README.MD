# Rails on Kubernetes play

This is an experimental project for a development environment spanning Docker, Docker Compose and Kubernetes.

You will need:

* Docker
* Docker Compose (not installed by default on Linux)
* Kubectl
* Minikube


## Local Development with Docker Compose

To build and test the Rails app normally, use:

    docker-compose build
    docker-compose up

The app should be available on http://localhost:8000

The service mounts your local directory so that changes to the code can be immediately tested.

## Testing with Kubernetes

    minikube start

    kubectl config set-context minikube

    # Set local Docker to use the minikube Docker daemon
    eval $(minikube docker-env)

    docker-compose build # Need to rebuild image on Minikube

    kubectl create -f rails-test-app-deployment.yaml
    kubectl create -f rails-test-app-service.yaml

    # Open the service URL
    minikube service rails-test-app

    minikube dashboard

    # Tear down
    minikube stop
    minikube delete
    eval $(docker-machine env -u)
