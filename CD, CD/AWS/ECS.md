# ECS
Elastic Container Service
- 도커 컨테이너 기반으로 서비스 운용을 가능하게 해주는 간단한 서비스이다.
- 무중단 배포( rolling update )를 제공하며 scale up이 가능한 특징이 있다.
- 백엔드 서비스를 스케일업 가능한 형태로 배포하는데 최적화되어 있다.
- 수많은 도커 컨테이너 서버를 띄우고 LB( Load Balancing )가 이들 사이에 밸런싱을 해준다.

> **LB** <br>
> 분산된 서버에 Request 들이 적절하게 분산되도록 도와준다.