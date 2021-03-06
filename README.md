# A small docker NFS client. Perfect for enabling whatever to NFS, compatible with databases (d3fk/nfs-client). 

This is a Docker image for a light NFS client (~10MB) compatible with database usage. By default NFS 3 is used (but the ENV enable you to change this).

## Docker image

pre-build from Docker hub with "automated build" option.

image name **d3fk/nfs-client**

`docker pull d3fk/nfs-client`

Docker hub repository: https://hub.docker.com/r/d3fk/nfs-client/


## Origin
Based on https://github.com/flaccid/docker-nfs-client

*The image is now built from the original Alpine with automated build.
Default NFS type modified to NFS3 for local IT requirements. 
The entry script was adapted to be compatible with using this NFS client with database (mariadb, mysql...) and to permit running the container without setting the SERVER and SHARE env parameters, simply to share on the host's network the NFS client capabilities for mounting any NFS shared path on the host (quite useful with small os)* 

## ENVIRONMENT

- `SERVER` - the hostname or IP of the NFS server to connect to
- `SHARE` - the NFS shared path to mount
- `MOUNT_OPTIONS` - mount options to mount the NFS share with
- `FSTYPE` - the filesystem type; specify `nfs4` for NFSv4, default is `nfs3`
- `MOUNTPOINT` - the mount point for the NFS share within the container (default is /mnt/nfs-1)

## Usage


docker run -itd --privileged=true --net=host -e FSTYPE=cifs -e MOUNT_OPTIONS=user=,pass= -e SERVER=//IP -e SHARE=sharefoldername nfsclient_lin

Several possibilities:
### 1. Mount your NFS mount-point **manually** on your host

Run the container

`docker run -itd --privileged=true --net=host d3fk/nfs-client`

then you can use NFS to mount all your mountpoints on your host

`sudo mount -t nfs SERVER_IP:/shared_path /mount_point`

### 2. Mount the mount-point **into** the nfs-client container

Basic command
`docker run -itd --privileged=true --net=host  -e SERVER=nfs_server_ip -e SHARE=shared_path d3fk/nfs-client`

**It is more convenient to set a volume **

Simply add a volume if you need to share the volume with other containers or mount it directly on your host (take care to add the **:shared** mention on the volume option)
`docker run -itd --privileged=true --name nfs --net=host -v /mnt/shared_nfs:/mnt/nfs-1:shared -e SERVER=nfs_server_ip -e SHARE=shared_path d3fk/nfs-client`

Then, using the `--volume-from nfs` option when runing another container will also made available the nfs shared content in this new container   
*Alternatively if you are not using a "named volume" but a "shared volume" you could also directly mount the host's directory that mounts the nfs in the new container*



### 3.  For [RancherOS](https://github.com/rancher/os/) users it is possible to run this nfs-client container **at [RancherOS](https://github.com/rancher/os/) startup** 

by adding the nfs service to one of your cloud-config.yml, user-config.yml or enabled service.yml... 

i.e: see the file [rancheros-cloud-config.yml](https://github.com/Angatar/docker-nfs-client/blob/master/rancheros-cloud-config.yml)


You could also use the [additional mount syntax](https://rancher.com/docs/os/v1.x/en/storage/additional-mounts/) addapted to NFS (since you now have a nfs-client started at os startup). 
ie:

```
#cloud-config
        mounts:
             - ["SERVER_IP:/shared_path", "/mnt/nfs-1", "nfs", ""]
```





License
-------------------

```text
The MIT License (MIT)

Copyright (c) 2015 Evey Quirk
Copyright (c) 2015 Chris Fordham
Copyright (c) 2016 d3fk

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```
