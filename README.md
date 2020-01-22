# Openshift
How to create openjdk imagestream into your openshift namespace using oc client

This example will guide you to create image stream inside your openshift namespace. I have windows 10 machine and configured oc client 3.9v. 

```bash
Version:
PS C:\WINDOWS\system32> oc version                                                                                                                                                                                 oc v3.9.0+191fece
kubernetes v1.9.1+a0ce1bc657
features: Basic-Auth

Server https://10.0.75.2:8443
openshift v3.9.0+2e78773-56
kubernetes v1.9.1+a0ce1bc657
```
Once your cluster is ready, you can see below imagestreams are already available in your machine but openjdk. login using system:admin user

```bash
PS C:\WINDOWS\system32> oc login -u system:admin                                                                                                                                                                   Logged into "https://10.0.75.2:8443" as "system:admin" using existing credentials.

You have access to the following projects and can switch between them with 'oc project <projectname>':

    default
    kube-public
    kube-system
  * myproject
    openshift
    openshift-infra
    openshift-node
    openshift-web-console

Using project "myproject".
```
Select openshift project and retrive default images streams loaded with cluster up command.
```bash
PS C:\WINDOWS\system32> oc get is                                                                                                                                                                                  NAME             DOCKER REPO                                TAGS                           UPDATED
dotnet           172.30.1.1:5000/openshift/dotnet           2.0,latest                     40 minutes ago
dotnet-runtime   172.30.1.1:5000/openshift/dotnet-runtime   2.0,latest                     40 minutes ago
httpd            172.30.1.1:5000/openshift/httpd            2.4,latest                     40 minutes ago
jenkins          172.30.1.1:5000/openshift/jenkins          1,2,latest                     40 minutes ago
mariadb          172.30.1.1:5000/openshift/mariadb          10.1,10.2,latest               40 minutes ago
mongodb          172.30.1.1:5000/openshift/mongodb          2.4,2.6,3.2 + 2 more...        40 minutes ago
mysql            172.30.1.1:5000/openshift/mysql            5.5,5.6,5.7 + 1 more...        40 minutes ago
nginx            172.30.1.1:5000/openshift/nginx            1.10,1.12,1.8 + 1 more...      40 minutes ago
nodejs           172.30.1.1:5000/openshift/nodejs           0.10,4,6 + 2 more...           40 minutes ago
perl             172.30.1.1:5000/openshift/perl             5.20,5.24,latest + 1 more...   40 minutes ago
php              172.30.1.1:5000/openshift/php              5.5,5.6,7.0 + 2 more...        40 minutes ago
postgresql       172.30.1.1:5000/openshift/postgresql       9.2,9.4,9.5 + 2 more...        40 minutes ago
python           172.30.1.1:5000/openshift/python           2.7,3.3,3.4 + 3 more...        40 minutes ago
redis            172.30.1.1:5000/openshift/redis            3.2,latest                     40 minutes ago
ruby             172.30.1.1:5000/openshift/ruby             2.4,latest,2.0 + 2 more...     40 minutes ago
wildfly          172.30.1.1:5000/openshift/wildfly          10.0,10.1,8.1 + 2 more...      40 minutes ago
```
## Create admin user and assign cluster admin role to it
```bash
PS C:\WINDOWS\system32> oc adm policy add-cluster-role-to-user cluster-admin admin                                                                                                                                 cluster role "cluster-admin" added: "admin"
```
## Add image builder role to admin user 
```bash
PS C:\WINDOWS\system32> oc adm policy add-role-to-user system:image-builder admin                                                                                                                                  role "system:image-builder" added: "admin"
```
## Login using admin user
```bash
oc login -u admin -p admin                                                                                                                                                                 Login successful.

You have access to the following projects and can switch between them with 'oc project <projectname>':

    default
    kube-public
    kube-system
  * myproject
    openshift
    openshift-infra
    openshift-node
    openshift-web-console

Using project "myproject".
```
## List docker images to check if openjdk18 already exists
```bash
PS C:\WINDOWS\system32> docker images                                                                                                                                                                              REPOSITORY                         TAG                 IMAGE ID            CREATED             SIZE
openshift/origin-web-console       v3.9.0              ffd45c003bd1        2 months ago        507MB
openshift/origin-docker-registry   v3.9.0              f6d2c17f6173        2 months ago        477MB
openshift/origin-haproxy-router    v3.9.0              5338aa79b428        2 months ago        1.3GB
openshift/origin-deployer          v3.9.0              f3df0b117c25        2 months ago        1.27GB
openshift/origin                   v3.9.0              737c38c2b682        2 months ago        1.27GB
openshift/origin-pod               v3.9.0              240e2af79076        2 months ago        226MB
```
## Pull openjdk18 from registry.access.redhat.com
```bash
PS C:\WINDOWS\system32> docker pull registry.access.redhat.com/redhat-openjdk-18/openjdk18-openshift                                                                                                               
Using default tag: latest
latest: Pulling from redhat-openjdk-18/openjdk18-openshift
00f17e0b37b0: Pull complete                                                                                                                                                                                        
305d73a95c8f: Pull complete                                                                                                                                                                                        
3167f10b37c3: Pull complete                                                                                                                                                                                        
Digest: sha256:bba9e03649444755ccd03e7ae9ede407f65788de53ddac4ba6d04d3eb7088cda
Status: Downloaded newer image for registry.access.redhat.com/redhat-openjdk-18/openjdk18-openshift:latest
registry.access.redhat.com/redhat-openjdk-18/openjdk18-openshift:latest
PS C:\WINDOWS\system32> docker images                                                                                                                                                                              
REPOSITORY                                                         TAG                 IMAGE ID            CREATED             SIZE
registry.access.redhat.com/redhat-openjdk-18/openjdk18-openshift   latest              963af2cdd61f        4 weeks ago         469MB
openshift/origin-web-console                                       v3.9.0              ffd45c003bd1        2 months ago        507MB
openshift/origin-docker-registry                                   v3.9.0              f6d2c17f6173        2 months ago        477MB
openshift/origin-haproxy-router                                    v3.9.0              5338aa79b428        2 months ago        1.3GB
openshift/origin-deployer                                          v3.9.0              f3df0b117c25        2 months ago        1.27GB
openshift/origin                                                   v3.9.0              737c38c2b682        2 months ago        1.27GB
openshift/origin-pod                                               v3.9.0              240e2af79076        2 months ago        226MB
```
## Login into docker internal registry using admin user
```bash
PS C:\WINDOWS\system32> oc whoami -t                                                                                                                                                                               W96smEUpWDbl817jbPJCutoH_3WAe4Xhi38Pafi0hFs
PS C:\WINDOWS\system32> docker login -u admin -p W96smEUpWDbl817jbPJCutoH_3WAe4Xhi38Pafi0hFs 172.30.1.1:5000                                                                                                       WARNING! Using --password via the CLI is insecure. Use --password-stdin.
Login Succeeded
```
## Tag image and push into docker internal registry
Note that after push output shows for me is "Layer already exists" but for you it will be different. 
```bash
PS C:\WINDOWS\system32> docker tag registry.access.redhat.com/redhat-openjdk-18/openjdk18-openshift 172.30.1.1:5000/openshift/openjdk18                                                                            
PS C:\WINDOWS\system32> docker push  172.30.1.1:5000/openshift/openjdk18                                                                                                                                           The push refers to repository [172.30.1.1:5000/openshift/openjdk18]
ea6748bd08c5: Layer already exists                                                                                                                                                                                 967fdc97a8ce: Layer already exists                                                                                                                                                                                 2ea2a8104ce0: Layer already exists                                                                                                                                                                                 latest: digest: sha256:6e57efae4828dbae77e18f12d2d3691df959e344fcd87e91ec38e03c5aec5f98 size: 949
```
## Check if image stream exists in your openshift namespace with openjdk18
```bash
PS C:\WINDOWS\system32> oc get is                                                                                                                                                                                  NAME             DOCKER REPO                                TAGS                          UPDATED
dotnet           172.30.1.1:5000/openshift/dotnet           2.0,latest                    40 minutes ago
dotnet-runtime   172.30.1.1:5000/openshift/dotnet-runtime   2.0,latest                    40 minutes ago
httpd            172.30.1.1:5000/openshift/httpd            2.4,latest                    40 minutes ago
jenkins          172.30.1.1:5000/openshift/jenkins          1,2,latest                    40 minutes ago
mariadb          172.30.1.1:5000/openshift/mariadb          10.1,10.2,latest              40 minutes ago
mongodb          172.30.1.1:5000/openshift/mongodb          2.4,2.6,3.2 + 2 more...       40 minutes ago
mysql            172.30.1.1:5000/openshift/mysql            5.5,5.6,5.7 + 1 more...       40 minutes ago
nginx            172.30.1.1:5000/openshift/nginx            1.8,latest,1.10 + 1 more...   40 minutes ago
nodejs           172.30.1.1:5000/openshift/nodejs           latest,0.10,4 + 2 more...     40 minutes ago
openjdk18        172.30.1.1:5000/openshift/openjdk18        latest                        2 minutes ago
perl             172.30.1.1:5000/openshift/perl             5.16,5.20,5.24 + 1 more...    40 minutes ago
php              172.30.1.1:5000/openshift/php              5.5,5.6,7.0 + 2 more...       40 minutes ago
postgresql       172.30.1.1:5000/openshift/postgresql       9.6,latest,9.2 + 2 more...    40 minutes ago
python           172.30.1.1:5000/openshift/python           2.7,3.3,3.4 + 3 more...       40 minutes ago
redis            172.30.1.1:5000/openshift/redis            latest,3.2                    40 minutes ago
ruby             172.30.1.1:5000/openshift/ruby             2.0,2.2,2.3 + 2 more...       40 minutes ago
wildfly          172.30.1.1:5000/openshift/wildfly          10.0,10.1,8.1 + 2 more...     40 minutes ago
```
## Describe imagestream and istag 
```bash
PS C:\WINDOWS\system32> oc describe is openjdk18    
PS C:\WINDOWS\system32> oc describe istag openjdk18:latest  
```
## Deploy your application using the imagestream to see if it works.
```bash
PS C:\WINDOWS\system32> oc new-project demo  
 oc new-app -i openjdk18 https://github.com/manojsingh-manoj/SpringBEx.git --name springbootexample 
```
