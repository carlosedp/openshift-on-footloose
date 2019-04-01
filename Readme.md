# Running Openshift on Footloose

This project allows one to deploy OpenShift on Docker containers inside a Docker container itself. This was inspired by [Alex Ellis post](https://blog.alexellis.io/openshift-in-a-footloose-container/) describing the steps to deploy OpenShift and install OpenFaaS. 

The deployment is done using Footloose, a project from Weaveworks that aims to be *Containers that look like Virtual Machines*.

There is also a convenience playbook to install [OpenFaaS](https://www.openfaas.com/) on the newly deployed OpenShift.

## Requirements

* [Docker](https://www.docker.com/)
* [Footloose](https://github.com/weaveworks/footloose)
* [Ansible](https://www.ansible.com/)

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
