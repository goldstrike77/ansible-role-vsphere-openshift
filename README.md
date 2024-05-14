![](https://img.shields.io/badge/Ansible-openshift-green.svg?logo=angular&style=for-the-badge)

>__Please note that the original design goal of this role was more concerned with the initial installation and bootstrapping environment, which currently does not involve performing continuous maintenance, and therefore are only suitable for testing and development purposes,  should not be used in production environments. The author does not guarantee the accuracy, completeness, reliability, and availability of the role content. Under no circumstances will the author be held responsible or liable in any way for any claims, damages, losses, expenses, costs or liabilities whatsoever, including, without limitation, any direct or indirect damages for loss of profits, business interruption or loss of information.__

>__请注意，此角色的最初设计目标更关注初始安装和引导环境，目前不涉及执行连续维护，因此仅适用于测试和开发目的，不应在生产环境中使用。作者不对角色内容之准确性、完整性、可靠性、可用性做保证。在任何情况下，作者均不对任何索赔，损害，损失，费用，成本或负债承担任何责任，包括但不限于因利润损失，业务中断或信息丢失而造成的任何直接或间接损害。__

__Table of Contents__

- [Overview](#overview)
- [Requirements](#requirements)
  * [vSphere systems](#vSphere-systems)
  * [Prerequisites](#Prerequisites)
- [ Role variables](#Role-variables)
  * [Main Configuration](#Main-parameters)
  * [Other Configuration](#Other-parameters)
- [Dependencies](#dependencies)
- [Example Playbook](#example-playbook)
  * [Hosts inventory file](#Hosts-inventory-file)
  * [Vars in role configuration](#vars-in-role-configuration)
  * [Combination of group vars and playbook](#combination-of-group-vars-and-playbook)
- [License](#license)
- [Author Information](#author-information)
- [Donations](#Donations)

## Overview
Red Hat OpenShift, the industry's leading hybrid cloud application platform powered by Kubernetes, brings together tested and trusted services to reduce the friction of developing, modernizing, deploying, running, and managing applications. OpenShift delivers a consistent experience across public cloud, on-premise, hybrid cloud, or edge architecture.

## Requirements
### vSphere systems
This role will work on the following operating systems:
  * 7.0.3

### Prerequisites:
  - [Adding vCenter root CA certificates to ansible controller system trust.](https://docs.openshift.com/container-platform/4.13/installing/installing_vsphere/installing-vsphere-installer-provisioned.html#installation-adding-vcenter-root-certificates_installing-vsphere-installer-provisioned)
  - [Install the OpenShift command line interface.](https://access.redhat.com/downloads/content/290)
  - [Install the butane.](https://github.com/coreos/butane)
  - Prepare an S3 object storage bucket.

## Role variables
### Main parameters #
There are some variables in defaults/main.yml which can (Or needs to) be overridden:

##### General parameters
* `openshift_state`: Determine whether or not to install the OpenShift, Valid values are present or absent.
* `openshift_version`: Specify the OpenShift version.
* `openshift_release`: Define OpenShift distribution, Valid values are ocp(OpenShift Container Platform) or okd(Origin Community Distribution).
* `openshift_cluster`: Define OpenShift cluster name.
* `openshift_domain`: Define the primary domain name.
* `openshift_sshkey`: The SSH key to authenticate access to your cluster machines.
* `openshift_capabilities: # Determine whether enable or disable optional components prior to installation.

##### Network parameters
* `openshift_network`: Define OpenShift cluster network configuration.

##### vSphere parameters
* `openshift_vsphere`: Define installation configuration parameters for vSphere.

##### Container Registry parameters
* `openshift_cr`: Define container registry configuration.

##### HTTP URL parameters
* `openshift_ignition_bucket`: Define ignition file URL configuration.

##### DNS parameters
* `openshift_dns`: Define DNS provider configuration.

##### Node parameters
* `openshift_node`: Define compute machines configuration for vSphere..

##### Load Balancer parameters
* `openshift_lb`: Define load balancer provider configuration.

##### Service Mesh
* `customer`: Define the customer name.
* `environments`: Define the service environment.
* `project`: Define the project name.
* `group`: Define the group name.

### Other parameters
There are some variables in vars/main.yml:

## Dependencies
- Ansible versions >= 2.15
- Python >= 3.11.2

## Example

### Combination of group vars and playbook
```yaml
openshift_state: "present"
openshift_version: "4.15"
openshift_release: "ocp"
openshift_cluster: "{{ openshift_release }}-{{ customer }}-{{ environments }}-{{ project }}-{{ group }}-01"
openshift_domain: "home.local"
openshift_sshkey: "ssh-rsa AAAA...."
openshift_capabilities:
  - "Build"
  - "CloudCredential"
  - "Console"
  - "CSISnapshot"
  - "MachineAPI"
  - "Storage"
openshift_network:
  cluster: "10.128.0.0/14"
  machine: "192.168.0.0/24"
  hostprefix: "23"
  service: "172.30.0.0/16"
  apivips: "192.168.0.167"
  ingressvips: "192.168.0.166"
openshift_vsphere:
  vcenter: "vsphere.local"
  user: "administrator"
  password: "password"
  cluster: "cn-north-1"
  datacenter: "cn-north"
  defaultdatastore: "ds-san-lun0"
  folder: "{{ customer }}/{{ environments }}/{{ project }}/{{ group }}"
  resourcepool:
    name: "rp-{{ customer }}-{{ environments }}-{{ project }}-{{ group }}"
    mem_shares: "normal"
    mem_limit: -1
    mem_reservation: 0
    mem_expandable_reservations: true
    cpu_shares: "normal"
    cpu_limit: -1
    cpu_reservation: 0
    cpu_expandable_reservations: true
  networks: ["vlan-trunk-portrgoup"]
openshift_cr:
  pullsecret: '{"auths":{"obs.home.local":{"auth":"YWRtaW46UGFzc3cwcmQ=","email":"you@example.com"}}}'
  imagecontentsources:
    - mirrors: ["obs.home.local/ocp4/openshift4"]
      source: "quay.io/openshift-release-dev/ocp-release"
    - mirrors: ["obs.home.local/ocp4/openshift4"]
      source: "quay.io/openshift-release-dev/ocp-v4.0-art-dev"
  additionaltrustbundle: |
    -----BEGIN CERTIFICATE-----
    MIIDRDCCAiwCCQCLL4t71Jt1ojANBgkqhkiG9w0BAQsFADBkMQswCQYDVQQGEwJY
    WDENMAsGA1UECAwETWFyczEWMBQGA1UEBwwNTW91bnQgT2x5bXB1czEQMA4GA1UE
    CgwHQ29tcGFueTENMAsGA1UECwwEVW5pdDENMAsGA1UEAwwEUk9PVDAeFw0yMDA2
    MTEwOTE2MzlaFw00MDA2MTEwOTE2MzlaMGQxCzAJBgNVBAYTAlhYMQ0wCwYDVQQI
    DARNYXJzMRYwFAYDVQQHDA1Nb3VudCBPbHltcHVzMRAwDgYDVQQKDAdDb21wYW55
    MQ0wCwYDVQQLDARVbml0MQ0wCwYDVQQDDARST09UMIIBIjANBgkqhkiG9w0BAQEF
    AAOCAQ8AMIIBCgKCAQEA0gpK3LDFYKib5XZs0IlEL/Mzx4WdLhhHCPeiYNNKkxKn
    VaUKE+M/B1K+hTZmYLfG5VAOZgkdpWr7pfmPSN9qWyKNqderAHzQu8A4YbxbSSQV
    DKVcpQCaHEfjcEKjl0zDIX1MKzJiArJ4YPTCixgDlni195BmDBw100YGa3jw+DxS
    LLlR8TA7yeLu/0NmDU31/3u5fAVZ+Vqsnlvn+rJ87Zzdkkq4EPPXojpbDIol+TL1
    BIW+KNLMwZ4g8AGmdzfRP+ztmNqnK/M7j+KhK7XI2YpvkdhSCXmdx/gUs5S8xmhP
    XKcSLFYhWsxj3wo9ZXsmwAjfQaS6Zfy4+F7junBlDQIDAQABMA0GCSqGSIb3DQEB
    CwUAA4IBAQBDrF/ceHWzXwobHoPLLwi+mJxGsS6ROU3Qs2siLdBz374NMfA2Stke
    Mj4uOTvcvgzl/yMMVLg/sQXLyo/gEa9Ya4XdX4qNiDcCp3x6J3gmkl0kqO687yQJ
    EumEWUWrfxgjxtHR1C/cTEgqc6F0RWGsV+23dOBoLoQBkv4cTldyj0FLDIdIHwjw
    AW3Py12YobJ54lv8jlfaUEf5x7gwyMny04uh4hM5MGMVGof+wQZuM4bY30dV526y
    AOqx13cHJzMBEmxhWQ5gdP3c9wJqUnI+002ON7bZr9mUtCEZoBSu41oT8lhc4m4d
    YB2cjNpMuRLjcS6Ge5rABpyAFYoTThXv
    -----END CERTIFICATE-----
openshift_ignition_bucket:
  provider: "s3"
  access_key: 'admin'
  secret_key: 'password'
  bucket: 'ignition'
  endpoint_url: "http://obs.home.local:9000"
  encrypt: false
openshift_dns:
  provider: "win"
  forward_zone: '{{ openshift_domain }}'
  reverse_zone: "192.in-addr.arpa"
openshift_node:
  gateway: "192.168.0.1"
  netmask: "255.255.255.0"
  dns: ["192.168.0.251"]
  ntp: ["ntp1.aliyun.com", "ntp2.aliyun.com", "ntp3.aliyun.com"]
  datastore: "ds-san-lun1"
  disktype: "thin"
  template: "template-RHCOS415"
  specs:
    bootstrap:
      cpucount: 4
      memory: 8
    master:
      cpucount: 8
      memory: 16
      disk: 63
    worker:
      cpucount: 10
      memory: 32
      disk: 127
  hosts:
    - { name: "s-bootstrap-01", ip: "192.168.0.120", role: "bootstrap" }
    - { name: "s-master-01", ip: "192.168.0.121", role: "master" }
    - { name: "s-master-02", ip: "192.168.0.122", role: "master" }
    - { name: "s-master-03", ip: "192.168.0.123", role: "master" }
    - { name: "s-worker-01", ip: "192.168.0.124", role: "worker" }
    - { name: "s-worker-02", ip: "192.168.0.125", role: "worker" }
    - { name: "s-worker-03", ip: "192.168.0.126", role: "worker" }
openshift_lb:
  provider: "F5"
  server: "192.168.0.170"
  user: "admin"
  password: "password"
  partition: "{{ customer }}-{{ environments }}-{{ project }}-{{ group }}"
customer: "it"
environments: "prd"
project: "shared"
group: "toolchain"
```

## License
![](https://img.shields.io/badge/MIT-purple.svg?style=for-the-badge)

## Author Information
Please send your suggestions to make this role better.

## Donations
Please donate to the following monero address.

    46CHVMbb6wQV2PJYEbahb353SYGqXhcdFQVEWdCnHb6JaR5125h3kNQ6bcqLye5G7UF7qz6xL9qHLDSAY3baagfmLZABz75
<p><img src="https://raw.githubusercontent.com/goldstrike77/goldstrike77.github.io/master/img/xmr_address.png" align="left" /></p>