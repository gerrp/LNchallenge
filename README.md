# Challenge Devops LeafNoise
## Despliegue de infraestructura con kubernetes

## Vagrant                                                                
Utilicé **vagrant** para la creación de la máquina virtual, en el **bootstrap.sh** instalé las siguientes dependencias necesarias:                                                 

**Docker**                                                                                                                       
                                                                                                                                                
**Kubectl**                                                                                                                   

**Minikube** 

**Helm** 

Además, dentro del **vagrantfile** configuré los siguientes puertos para permitir el port-forwarding y visualizar las aplicaciones dentro de la máquina host:                                                             

**80** (Apache-Angular)                      
**5000** (Flask)                                      
**31000** (Nodeport Kubernetes-Flask)                                                                                      
**31001** (Nodeport Kubernetes-Angular)

Levantamos la máquina e instalamos las dependencias con ``` vagrant up 
                                                             ```
                                                          
Nos conectamos con ``` vagrant ssh
                                           ```      
## Docker
Dentro del dockerfile de **flask** instale los requirements de la aplicación y expuse el puerto 5000                                               
Dentro del dockerfile de **angular** instale su cli, los requirements de la aplicación, configure un servidor de apache y expuse el puerto 80

## Github Actions
Configure 2 workflows, cada uno actuando sobre el contenido de su aplicación como trigger, **LNchallenge/Flask-Example/**** y **LNchallenge/angular-realworld-example-app/**** respectivamente. Esto quiere decir que con cada cambio dentro de sus carpetas se activará el siguiente pipeline:   
                                            
1 - **Hadolint**, herramienta que verifica que el dockerfile cumpla con las mejores prácticas en cuestiones de eficacia y seguridad  
2 - **Login a dockerhub**, utilizando secretos de repositorio                                                                                                                                                      
3 - **Docker build**, construcción de imagen                                                                                                                          
4 - **Push a dockerhub**, subida de imagen

## Docker Compose
Podemos levantar ambas aplicaciones situándonos en la carpeta **/docker-compose** y usando ``` docker compose up ```                                                                  
En este momento visualizaremos las mismas ingresando a ``` localhost:80 ``` y ``` localhost:5000 ```                                                                                                        

## Kubernetes
La infraestructura de kubernetes consta de:                                                                                  
**namespaceFlask** y **namespaceAngular** , para dividir los recursos de cada aplicación                                                                            
**nodeportFlask** y **nodeportAngular**, para establecer un loadbalancer sobre los pods desplegados **( 31000 Flask )** **(31001 Angular)**                          
**deploymentFlask** y **deploymentAngular**, deploy principal de pods con 3 replicas por defecto y rolling update                                      

## Helm
Finalmente configuré templates para cada uno de los recursos con distintas variables, para la visualización o modificación de las mismas -> helm/chart/**values.yaml**     
                                                                        
Para implementar la infraestructura vamos a necesitar un cluster de kubernetes levantado, para eso utilizamos ``` minikube start```                                                                          

Instalamos el paquete de **helm** situándonos en **/helm** y utilizando ```helm install flask-angular ./chart```                                                    

En este momento ya podremos acceder desde nuestra máquina host a ```localhost:31000``` y ```localhost:31001```, visualizando el contenido de cualquiera de los pods replicados

