#### Steps:


```sh
systemctl start docker
minikube start --network-plugin=cni --cni=calico --force 
# it is never recomended to run this cmd with root permission; for test purpose you can add this --force flag to ignore the warning
watch kubectl get pods -A
# keep watching the cluster services creation; you can exit the watch once all pods are in running state: CTR+C
yum install git make
git clone https://github.com/slahmer97/ICT4IALAB.git ict4ia
ls
#you should find this repo cloned under ict4ia name
cd ict4ia/templates/
kubectl apply -f pod.yaml
watch kubectl get pods -A -o wide
# wait until the new pod named yolo-v3 is in the running state (it should take some time as it updates the container+install dependencies+then run the server)
#if you want to check what's going on; exit the watch program; and execute the following cmd
kubectl logs yolo-v3 -f
#you would see startup procedure; wait until the server start and displays Application Startup Complete.
#Now at this point your ML inference pod is running; there are only some few steps needed
kubectl get pods -A
# memorize the ip address of yolo-v3 and calico-node then try to ping the ip addrs of yolo-v3?
ping YOLO-V3-IP
# The pod is not reachable; let's examine why!
ip route
# this will display the routing table in your host VM; as you can see there is no route for the pod IP or subnet!
ip route add YOLO_IP_SUBNET via IP_ADDRS_OF_CALICO_NODE
#try again to ping YOLO_v3?; the next step is to start sending inference requests to the yolo-v3 pod
#Finally, you will use curl as an HTTP client, you can find the cmd in ict4ia/client/curls file; just execute that cmd by replacing localhost by the ip address of the YOLO-V3-IP; the first time that you execute that cmd will take some seconds, as it will download the model; afterward, it should be taking few ms per request!


# DO NOT APPLY THIS UNTIL THE HTTP's LOG DISPLAY THAT THE SERVER IS RUNNING
kubectl apply -f service.yaml
ip route add YOLO_SVC_IP via IP_ADDRS_OF_CALICO_NODE


```
