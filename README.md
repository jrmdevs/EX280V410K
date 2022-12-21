# EX280V410K
Tips para la preparación del examen de certificación


## Documentación:

Tener la siguiente documentación presente mientras se hace el examen para localizar algo rápidamente en caso de duda.

- Generic: Post installation configuration
    - Instrucciones para la configuración una vez que está instalado Openshift. 
    - Quitar usuario kubeadmin
- Secrets: Nodes > Working with Pods > Understanding Secrets
- Identity Provider: Authentication and Authorization > Understanding Identity Provider
- RBAC: Authentication and Authorization > Using RBAC
- Users and Groups: Authentication and Authorization > Understanding Authentication
- SCC: Authentication and Authorization
- Routes: Networking > Configuring Routes
- Network Policy: Networking > Network Policy
- Limit Range: Nodes > Working with Clusters > Setting Limit Ranges
- Quota: Applications > Quotas

## Prácticas:

Task 1: Setting up Authentication
    *Configure your cluster to use the HTPasswd identity provider
    *Create the following user and groups
      -Group admins, with users admin and anna
      -Group developers, with users linda and developer
      -Group viewers, with user lisa
      -User anouk
    *Ensure that all these users can log in to the cluster
    *Remove the default kubeadmin user that was created when installing the cluster

Task 2: Setting up Authorization
    *The group admins has admin permissions on the cluster
    *The group developers has view and edit permissions on the cluster
    *The group viewers has view permissions on the cluster
    *User anouk has view and edit permissions on the namespace test-namespace

Task 3: Creating a Project Template
    *Create a new project template, according to the following requirements
      -Each project has a label with the name of the project
      -NetworkPolicy is set up such that:
        .Routes can be accessed by Pods in namespaces with the network.openshift.io/policy-group=ingress label
        .Pods in the same namespace can communicate with each other
        .Pods are only accessible to Pods in a different namespace if that namespace is configured with the network.openshift.io/polic-group=ingress label

Task 3: Creating a Project Template
    *LimitRange is set up as follows:
      -Each container requests 100 millicores of CPU
      -Each container requests 50 MiB of memory
      -Each container is limited to 200 millicores of CPU
      -Each container is limited to 100 MiB of memory
    *ResourceQuota is set up as follows:
      -Projects are limited to 20 Pods
      -Project can request a maximum of 1 GiB of memory
      -Projects can request a maximum of 2 CPUs
      -Projects can use a maximum of 2GiB of memory
      -Projects can use a maximum of 4 CPUs 

Task 4: Creating a Project
    *As the user developer, create a project with the name local-project. Ensure that this project inherits settings from the new project template created in Task 3.    

Task 5: Running a Secure Application
  *Create the project my-project
  *In this project, run a deployment that is based on the Docker image sandervanvugt/openshift:latest with the name hello-app
  *Configure this deployment such that it uses a TLS certificate at the expected location. Use the TLS certificate that was created in the preparation steps of this lab
  
  mkdir openssl
  cd openssl
  -Create Certificate Authority key (CAkey)
    openssl genrsa -des3 -out myCA.key 2048
    Enter pass phrase for myCA.key: 1234 
  -Sign CAkey  
    openssl req -x509 -new -nodes -key myCA.key -sha256 -days 3650 -out myCA.pem
    Enter pass phrase for myCA.key: 1234  
   (Indicar solo el common name, nos lo inventamos: jrm.example.com)
  -Create the certificates:
    openssl genrsa -out tls.key 2048
  -Sign request:
    openssl req -new -key tls.key -out tls.csr
    (indicar el common name de la URLS segura que utilizará la aplicación: secure-app-myproject.apps.ocp4.example.com)
  -Self-sign the certificate:
    openssl x509 -req -in tls.csr -CA myCA.pem -CSAkey myCA.key -CAcreateserial -out tls.crt -days 1650 -sha256
    Enter pass phrase for myCA.key: 1234 
  
Task 6: Creating a Passthrough Route
    *Create a passthrough route to the secure-app service, which points to secure-app-myproject.apps-crc.testing
    *Use curl -insecure https:///secure-app-myproject.apps-crc.testing to verify that the route responds to external requests

Task 7: Configure AutoScaling
    

Task 8: Configuring MySQL
    •As the developer user, use a deployment to create an application named mysql in the microservice project
    •Create a generic secret named mysql, using password as the key and mypassword as its value. Use this secret to set the MYSOL ROOT PASSWORD environment variable to the value of the password in the secret.
    •Configure the MySQL application to mount a PVC to /mnt. The PVC must have a 1GiB size, and the ReadWriteOnce access mode
    •Use a Nodeselector to ensure that MySQL will only run on your CRC node

Task 9: Configuring Wordpress
    *As the developer user, use a deployment to create an application named wordpress in the microservice project
    *Run this application with the anyuid security context assigned to the wordpress-sa service account
    *Create a route to the Wordpress application, using the hostname wordpress-microservice.apps-crc.testing
    *Use secrets and or ConfigMaps to set environment variables:
    ﻿﻿  -WORDPRESS_ DB HOST: is set to mysql
    ﻿﻿  -WORDPRESS DB NAME: is set to the value of wordpress
    ﻿﻿  -WORDPRESS DB USER: has the value "root"
    ﻿﻿  -WORDPRESS DB PASSWORD is set to the value of the password key in the mysgi secret
