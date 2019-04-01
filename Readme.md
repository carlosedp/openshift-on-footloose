# Running Openshift 3.11 on Footloose

This project allows one to deploy OpenShift on Docker containers inside a Docker container itself.

The deployment is done using Footloose, a project from Weaveworks that is Containers that look like Virtual Machines.

There is also a convenience playbook to install [OpenFaaS](https://www.openfaas.com/) on the newly deployed OpenShift.

## Requirements

* [Docker](https://www.docker.com/)
* [Footloose](https://github.com/weaveworks/footloose)
* [Ansible](https://www.ansible.com/)

## Running

To deploy a container machine, install it's pre-reqs and start OpenShift run:

`ansible-playbook main.yml`

To deploy OpenFaas into the cluster, run:

`ansible-playbook openfaas.yml`

Openshift console will be available on [`https://127.0.0.1.nip.io:8443/console`](https://127.0.0.1.nip.io:8443/console) and OpenFaas on [`https://openfaas.127.0.0.1.nip.io`](https://openfaas.127.0.0.1.nip.io).

You can deploy any application from the catalog and create routes that will be exposed using [nip.io](http://nip.io/) to avoid fiddling with `/etc/hosts`.