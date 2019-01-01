# Kubernetes Deployment for Jenkins on Raspberry Pi

## Getting Started
### Mount Thumb Drive
Mount a thumb drive at /media/usb-drive/jenkins_home on a worker node.
Ensure that the drive has at least 5 GB avaiable.
### Cluster Prerequisites
Node labeled with 
```
storage: jenkins_home
```
## Deployment
### Jenkins Container

The deployment is expecting to use paulwroe/jenkins:v5 from docker hub.
* https://cloud.docker.com/u/paulwroe/repository/docker/paulwroe/jenkins

See https://github.com/CommanderPaul/jenkins-deployment/blob/master/jenkins-dockerfile.md to make your own container.


### First Run Deployment
If jenkins_home has not been initialized,  set -Djenkins.install.runSetupWizard=true to run the setup wizard.
If jenkins_home has been initialized, ensure that -Djenkins.install.runSetupWizard=false.

### Deploy the script
```
kubectl apply -f jenkins-deployment.yaml
```
