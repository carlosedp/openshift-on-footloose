# Running Openshift on Footloose

This project allows one to deploy OpenShift on Docker containers inside a Docker container itself.

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