

ECS EC2와 Fargate가 설정방식이 조금씩 달라서, 중요 포인트를 정리한다.

fargate는 servless container service

docker를 사용하지 않고, fire cracker라는 container를 이용함.

fire cracker는 open source로 리소르를 효과적으로 사용할 수 있게끔 설게되었기 때무넹, 보통 EC2보다는 비용이 비싸다.

cloud watch와 통합되어서 container based에서 쉽게 모니터링이 가능하다.


