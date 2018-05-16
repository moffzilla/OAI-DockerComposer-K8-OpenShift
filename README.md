# OAI-DockerComposer-K8-OpenShift

This project is aimed to run OpenAirInterface in Docker Containers with different platforms: Docker Composer, Kubernetes | Helm and OpenShift.

OpenAirInterface is an implementation of the 3GPP specifications concerning the Evolved Packet Core Networks, that means it contains the implementation of the following network elements: MME, HSS, S-GW, P-GW. 
[More information about Open Air Interface](https://gitlab.eurecom.fr/oai/openair-cn)


## Minimum Requirements

- Host running Docker engine requires Linux 4.7.2-040702-lowlatency Kernel
- Running on Xenial (Cores=2 Mem=8G Root-Disk=30G)
- Ubuntu Xenial(16.04) amd64/ Kernel 4.7.2 Low Latency | Cores=2 Mem=8G Root-disk=30G


## General Install Requirements

The enviroment installation:

- Install Xenial(16.04) 
- Install Linux 4.7.2 low latency Kernel (4.7.1 is also supported)


## Instructions for Kubernetes

- To avoid the certificate issue always include "--insecure-skip-tls-verify=true"
	kubectl get all --insecure-skip-tls-verify=true
- K8 Dashboard URL and CLI credentails can be found at https://[Master-IP]:8880
- You can generate the " ~/.kube/config" contents from "OAI" enviroment then "Kubernetes --> CLI"

Create Kubernetes Objects for OAI

kubectl create -f oai-k8-artifacts/mme.yaml --insecure-skip-tls-verify=true
kubectl create -f oai-k8-artifacts/db.yaml --insecure-skip-tls-verify=true
kubectl create -f oai-k8-artifacts/hss.yaml --insecure-skip-tls-verify=true
kubectl create -f oai-k8-artifacts/spgw.yaml --insecure-skip-tls-verify=true

Verify all objects are created

kubectl describe deployment mme  --insecure-skip-tls-verify=true
kubectl describe service mme  --insecure-skip-tls-verify=true

*Replace specific OAI Role

## Instructions for OpenShift 



## Instructions for Docker Compose


- Run the Ansible Playbook

       `ansible-playbook ec2_module-oai.yml -vvvv --user=ubuntu`
       
    The playbook install Docker SW, builds images and deploys all roles at once

## Basic Docker Execution

Instructions:

1) Pull the latest image
`docker pull moffzilla/oai-epc:v02`

2) Execute as follows:
`docker run --expose=1-9000 -p 3868:3868 -p 2152:2152 -p 2123:2123 -t -d -h=epc --privileged=true --name oai-testing --cap-add=ALL -v /dev:/dev -v /lib/modules:/lib/modules moffzilla/oai-epc:v02`

3) Attach to the running container
`docker exec -it oai-testing /bin/bash`

4) Verify all EPC services are started

`docker exec -it oai-testing /bin/bash`

Verify sockets are listening:

`netstat -na | grep 21`

udp        0      0 192.171.11.1:2123       0.0.0.0:*                        LISTEN   (MME_PORT_FOR_S11_MME)

udp        0      0 0.0.0.0:2152            0.0.0.0:*  (SGW_IPV4_PORT_FOR_S1U_S12_S4_UP)


`netstat -na | grep 3868`

tcp        0      0 0.0.0.0:3868            0.0.0.0:*               LISTEN    (HSS)

tcp        0      0 127.0.0.1:3868          127.0.0.1:39554         ESTABLISHED  ( S6A HSS <--> MME )

tcp        0      0 127.0.0.1:39554         127.0.0.1:3868          ESTABLISHED  ( S6A MME <--> HSS )


## Manual Low Latency Kernel instructions

1) Log in to Ubuntu host machine
2) Install KERNEL 4.7.x on your host machine, currently 4.7.1 and 4.7.2 is supported.

Please note that AWS may not like the here referenced low latency Kernel, in MS Azure and Baremetal/VMs it works fine

Download Kernel

 `wget http://kernel.ubuntu.com/~kernel-ppa/mainline/v4.7.2/linux-headers-4.7.2-040702_4.7.2-040702.201608201334_all.deb`

 `wget http://kernel.ubuntu.com/~kernel-ppa/mainline/v4.7.2/linux-headers-4.7.2-040702-lowlatency_4.7.2-040702.201608201334_amd64.deb`

 `wget http://kernel.ubuntu.com/~kernel-ppa/mainline/v4.7.2/linux-image-4.7.2-040702-lowlatency_4.7.2-040702.201608201334_amd64.deb`

2.1) Install Kernel
 `sudo dpkg -i *.deb`

2.2) Restart your host machine
 `sudo shutdown -r now`

After reboot, login again and check Kernel

2.3) Verify your kernel
 `uname -r`

For other architectures:
http://kernel.ubuntu.com/~kernel-ppa/mainline/v4.7.2/


