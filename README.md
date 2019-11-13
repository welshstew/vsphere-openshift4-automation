# vsphere openshift4 automation

VERY BASIC playbook using `openshift-install` and `govc` to create an openshift cluster in vsphere.

- Downloads Installer
- Generates Manifests
- Generates Ignition Configs
- Creates VMs (prereq: template must exist in the cluster)
- Modifies VMs to add ignition configs to them

Instructions followed as per: https://blog.openshift.com/openshift-4-2-vsphere-install-quickstart/

## How to run

I run this playbook from the same host that has had https://github.com/christianh814/ocp4-upi-helpernode run on it.

Ensure you have `govc` installed. And ensure you have the appropriate GOVC environment variables populated:

```
export GOVC_USERNAME=administrator@vsphere.local
export GOVC_PASSWORD=password
export GOVC_URL=my.vcenter.lan
export GOVC_INSECURE=true
```
Ensure you have a `vars/vsphere-temp.yml` file populated as you want.

Run the playbook with:

```
ansible-playbook create-vsphere-machines.yml -vv
```

Please note that everything happens in a temp directory:

```
TASK [create-ocp4-manifests : Create temporary directory] **********************************************************************************************************
task path: /some/path/vsphere-openshift4-automation/roles/create-ocp4-manifests/tasks/setup.yml:2
changed: [localhost] => {"changed": true, "gid": 1000, "group": "swinches", "mode": "0700", "owner": "user", "path": "/tmp/ansible.s4i72i7g", "secontext": "unconfined_u:object_r:user_tmp_t:s0", "size": 40, "state": "directory", "uid": 1000}
```

In this case everything will be in `/tmp/ansible.s4i72i7g`

## Sample vars file:

```
---
cluster_id: ocp1
cluster_domain: example.lab
ssh_key_file: /home/user/.ssh/id_rsa.pub
pull_secret_file: /home/user/pull-secret.json
bootstrap_iginition_url: http://hello.com/ignition/bootstrap.ign
bootstrap_iginition_destination: 
folder:
network: VM Network
machines:
- name: bootstrapy
  host: esxhost1
  template: worker-bootstrap-template
  cores: 4
  ram: 8192
  datastore: esxhost1-2tb
  mac: 00:50:56:AA:AA:AA
  disk: 120G
  type: bootstrap
- name: master0y
  host: esxhost1
  template: master-template
  cores: 4
  ram: 16384
  datastore: esxhost1-2tb
  mac: 00:50:56:AA:AA:AB
  disk: 120G
  type: master
- name: master1y
  host: esxhost1
  template: master-template
  cores: 4
  ram: 16384
  datastore: esxhost1-2tb
  mac: 00:50:56:AA:AA:AC
  disk: 120G
  type: master
- name: master2y
  host: esxhost1
  template: master-template
  cores: 4
  ram: 16384
  datastore: esxhost1-2tb
  mac: 00:50:56:AA:AA:AD
  disk: 120G
  type: master
- name: worker0y
  host: esxhost1
  template: worker-bootstrap-template
  cores: 4
  ram: 8192
  datastore: esxhost1-2tb
  mac: 00:50:56:AA:AA:AE
  disk: 120G
  type: worker
- name: worker1y
  host: esxhost1
  template: worker-bootstrap-template
  cores: 4
  ram: 8192
  datastore: esxhost1-2tb
  mac: 00:50:56:AA:AA:AF
  disk: 120G
  type: worker
```