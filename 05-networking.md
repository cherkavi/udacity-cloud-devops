# networking
## how it works
![network](https://user-images.githubusercontent.com/8113355/231363237-ade170bb-8b4a-4110-8f47-f845b6e9c627.jpg)  

## subnets
* public - with a "Route Table" that has a route to an InternetGateWay
* private - without route to IGW

## autoscaling & load balancing
![image](https://user-images.githubusercontent.com/8113355/230970596-fdf7252d-817c-429a-816c-5ec6e2cadahjjjjja0.png)  
![load balancing](https://drive.google.com/uc?id=1-oQsTivqkXEuLibHvSYfq7gGXkXAJ5jx)  

Health-Check endpoint of application:
* mandatory for Application Load Balancer
* can be used for Network Load Balancer

## NetworkAddressTranslation
![NAT](https://user-images.githubusercontent.com/8113355/230967248-88910ef1-7b5a-4d4d-be0f-9ab7cfe58d57.png)
    NAT gateway serves as an intermediary to take a private resource's request, connect to the Internet, and then relay the response back to the private resource without exposing that private resource's IP address to the public.
> EIP is necessary for starting EC2 with the same address inside subnet

## Route table
How to "move traffic in/out Subnets" not about allowing or denying traffic.  
A routing table defines the rules, meaning if a packet arrives for a particular destination, then it should be moved towards the defined target.

* To allow/deny traffic at the network/subnetwork level you have Access Control List.
* To allow/deny traffic at an instance level, you have Security Groups.
