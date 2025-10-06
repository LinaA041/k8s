The following documents the process used to expose applications deployed on a local Kubernetes cluster (Minikube) using the different types of services available: ClusterIP, NodePort, LoadBalancer, and Ingress.

1.Cluster IP

In this case, we start from the fact that: ClusterIP is the default type of Service. It is only accessible from within the cluster (other pods, DaemonSets, Jobs, etc.). It does not expose ports to the host or the Internet.


![alt text](image-1.png)
The previous image shows the status of the replicas. 

![alt text](image.png)
The previous image demonstrates the query executed using curl, which was successful as it returned a response from the nginx server.

2. NODEPORT


Se creó el deployment a partir de la imagen de httpd:
![alt text](image-3.png)

Subsequently, the service was exposed and its status was validated to ensure that the pods were running correctly and accessible within the cluster.

![alt text](image-2.png)

NodePort is a type of service that exposes a port from the pods directly on the host, allowing access from outside the cluster. Unlike ClusterIP, the service can be accessed from the host machine without the need for additional tunnels.

Since Minikube runs with Docker as the container controller, containers are isolated from the host. However, for NodePort, this does not prevent access, because the service automatically assigns a port on the host machine that redirects to the pods.

To validate the service, the following command was used:

![alt text](image-4.png)

This command returns the accessible URL of the service, including the host IP and the port assigned by NodePort. Therefore, when accessing the indicated URL, it is evident that it works effectively:

![alt text](image-5.png)

The pod and service are validated in the same way:

![alt text](image-6.png)

3. LOAD BALANCER

The LoadBalancer service allows you to expose a service through an external load balancer, typically with a public IP address in cloud environments.

Creating the deployment and exposing the service:

![alt text](image-10.png)

Setting up the tunnel in Minikube:

![alt text](image-7.png)

Note: In Minikube, since there is no actual external load balancer, minikube tunnel simulates this behavior by assigning the IP address 127.0.0.1 to the service to route local traffic to the pods.

When running within WSL, this tunnel is only accessible from the Minikube or WSL environment. Therefore, tests from the pods responded correctly, while external requests from the host machine did not receive a response.

Finally, the service was validated using:

![alt text](image-11.png)
![alt text](image-12.png)

Note: Two different IP addresses are shown in the images because the LoadBalancer was created at two different times during the lab.

4. Ingress

Ingress allows you to define HTTP/HTTPS routing rules to expose multiple services through a single IP or domain, managing the entry of external traffic to the internal services of the cluster.

For the Ingress example, the deployment was created using the traefik/whoami image. The Ingress controller was enabled and its status verified, noting that it functions similarly to a LoadBalancer within the cluster. Afterwards, the /etc/hosts file on the host machine was updated to map the custom domain to the Minikube IP. Finally, the Ingress routing was tested successfully using curl to query the domain, confirming that traffic was correctly routed to the pod.


![alt text](image-9.png)