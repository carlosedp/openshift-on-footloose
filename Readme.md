# Running Openshift on Footloose

This project allows one to deploy OpenShift inside a Docker container, also known as DinD or Docker in Docker. This was inspired by [Alex Ellis post](https://blog.alexellis.io/openshift-in-a-footloose-container/) describing the steps to deploy OpenShift and install OpenFaaS.

The deployment is done using Footloose, a project from Weaveworks that aims to be *Containers that look like Virtual Machines* and is done completely by an Ansible playbook. You can deploy and use most features from Openshift like using the catalog, deploying Source2Image applications and more.

There is also a convenience playbook to install [OpenFaaS](https://www.openfaas.com/) on the newly deployed OpenShift.

## Requirements

* [Docker](https://www.docker.com/)
* [Footloose](https://github.com/weaveworks/footloose)
* [Ansible](https://www.ansible.com/)
* [OpenShift](https://docs.openshift.com/container-platform/3.7/cli_reference/get_started_cli.html#installing-the-cli) and/or [Kubernetes](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl) CLI tools

### Mac

```bash
# Install Homebrew package manager (if you don't have it yet)
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

# Install Docker
brew cask install docker
# Start Docker
open /Applications/Docker.app

# Install Footloose, Ansible and Openshift/Kubernetes CLI
brew tap hudochenkov/sshpass # Required by Ansible to connect using SSH with passwords
brew install ansible sshpass kubernetes-cli openshift-cli
brew tap weaveworks/tap
brew install weaveworks/tap/footloose
```

### Linux

```bash
# Install Docker
curl -fsSL https://get.docker.com/ | sh

# Install Ansible
sudo dnf install ansible # or
sudo yum install ansible # or
echo "deb http://ppa.launchpad.net/ansible/ansible/ubuntu trusty main" >> /etc/apt/sources.list
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 93C4A3FD7BB9C367
sudo apt-get update
sudo apt-get install ansible

# Install Footloose
curl -Lo footloose https://github.com/weaveworks/footloose/releases/download/0.4.0/footloose-0.4.0-linux-x86_64
chmod +x footloose
sudo mv footloose /usr/local/bin/
```

## Running

To deploy a container machine, install the pre-reqs and start OpenShift, do:

`ansible-playbook main.yml`

You can also deploy the monitoring stack based on CoreOs cluster-monitoring-operator into the the cluster. Just do:

`ansible-playbook monitoring-operator.yml`

To deploy OpenFaas into the cluster, run:

`ansible-playbook openfaas.yml`

Openshift console will be available on [https://127.0.0.1.nip.io:8443/console](https://127.0.0.1.nip.io:8443/console) and OpenFaas on [https://openfaas.127.0.0.1.nip.io](https://openfaas.127.0.0.1.nip.io).

Log-in with user *admin / any password* to access cluster as cluster-admin or user *developer / any password*.

In the command line, the cluster configuration file will be available on your local directory. You can use OpenShift `oc` or Kubernetes `kubectl` to access the cluster just do:

```bash
mkdir -p $HOME/.kube
cp openshift-config $HOME/.kube/config

oc login -u system:admin
oc get nodes
oc get pods --all-namespaces
#or
kubectl get nodes
kubectl get pods --all-namespaces
```

You can deploy any application from the catalog and create routes that will be exposed using [nip.io](http://nip.io/) to avoid fiddling with `/etc/hosts`.

To stop the Openshift, just use `footloose stop`. To restart, you can either do `footloose start && footloose ssh root@node0 /root/oc-up.sh` or re-run the `main.yml` playbook.

## OpenFaaS Demo

```bash
# Install openfaas cli from https://github.com/openfaas/faas-cli
export OPENFAAS_URL=https://openfaas.127.0.0.1.nip.io
faas-cli store deploy --tls-no-verify certinfo
echo -n www.openfaas.com | faas-cli invoke --tls-no-verify certinfo
```

## Example App - Source2Image

```bash
oc new-project nodejs-echo \
    --display-name="nodejs" --description="Sample Node.js app"
oc project nodejs-echo
oc new-app https://github.com/sclorg/nodejs-ex -l name=myapp

oc status
oc logs -f bc/nodejs-ex

oc expose svc/nodejs-ex --hostname=nodejs-ex.127.0.0.1.nip.io
```

## Remove the container and data

```bash
ansible-playbook teardown.yml
```
