# Running Openshift on Footloose

This project allows one to deploy OpenShift inside a Docker container, also known as DinD or Docker in Docker. This was inspired by [Alex Ellis post](https://blog.alexellis.io/openshift-in-a-footloose-container/) describing the steps to deploy OpenShift and install OpenFaaS.

The deployment is done using Footloose, a project from Weaveworks that aims to be *Containers that look like Virtual Machines* and is done completely by an Ansible playbook. You can deploy and use most features from Openshift like using the catalog, deploying Source2Image applications and more.

There is also a convenience playbook to install [OpenFaaS](https://www.openfaas.com/) on the newly deployed OpenShift.

## Requirements

* [Docker](https://www.docker.com/)
* [Footloose](https://github.com/weaveworks/footloose)
* [Ansible](https://www.ansible.com/)

### Mac

```bash
# Install Homebrew package manager (if you don't have it yet)
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

# Install Docker
brew cask install docker
# Start Docker
open /Applications/Docker.app

# Install Footloose and Ansible
brew install ansible sshpass
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
curl -Lo footloose https://github.com/weaveworks/footloose/releases/download/0.3.0/footloose-0.3.0-linux-x86_64
chmod +x footloose
sudo mv footloose /usr/local/bin/
```

## Running

To deploy a container machine, install the pre-reqs and start OpenShift, do:

`ansible-playbook main.yml`

To deploy OpenFaas into the cluster, run:

`ansible-playbook openfaas.yml`

Openshift console will be available on [`https://127.0.0.1.nip.io:8443/console`](https://127.0.0.1.nip.io:8443/console) and OpenFaas on [`https://openfaas.127.0.0.1.nip.io`](https://openfaas.127.0.0.1.nip.io).

Log-in with user *admin / any password* to access cluster as cluster-admin or user *developer / any password*.

You can deploy any application from the catalog and create routes that will be exposed using [nip.io](http://nip.io/) to avoid fiddling with `/etc/hosts`.

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
