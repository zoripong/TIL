> You can use a NAT device to enable instances in a private subnet to connect to the internet (for example, for software updates) or other AWS services, but prevent the internet from initiating connections with the instances.

- private subnet에 있는 인스턴스가 외부로 요청을 할 수 있지만,
- 외부에서 요청이 들어오는 것은 막을 수 있도록 하기 위해 NAT를 사용할 수 있다.

> A NAT device forwards traffic from the instances in the private subnet to the internet or other AWS services, and then sends the response back to the instances.

NAT은 private subnet에 있는 인스턴스의 트래픽을 인터넷 혹은 다른 AWS 서비스로 전달한 후 응답을 다시 인스턴스로 보내준다.

> When traffic goes to the internet, the source IPv4 address is replaced with the NAT device’s address and similarly, when the response traffic goes to those instances, the NAT device translates the address back to those instances’ private IPv4 addresses.

트래픽이 인터넷으로 보내질 때, 출발 IPv4 주소는  NAT 장치의 주소로 교체되고, 비슷하게 응답 트래픽이 인스턴스로 갈 때 NAT 장치는 주소를 다시 그 인스턴스의 private IPv4 주소로 변환시킵니다.