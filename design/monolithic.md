# 모놀리식 아키텍처

전통의 아키텍처를 지칭하는 것으로 소프트웨어의 모든 구성요소를 하나로 통합되어 하나의 파일로 배포되는 것을 의미하는 것으로 나쁜 아키텍처는 아닙니다.

애플리케이션의 규모에 따라 틀리지만 모놀리식 아키텍처는 다음과 같은 장점에 따른 단점이 있습니다.

<figure><img src="../.gitbook/assets/image (177).png" alt="" width="350"><figcaption></figcaption></figure>

1. **개발이 간단하다.**\
   단일 애플리케이션을 구축하므로 IDE등 개발 Tool을 사용하여 쉽게 개발을 할 수 있습니다.\
   \- <mark style="color:orange;">규모가 커지고 코드의 량이 많아 질 수록 개발 Tool의 실행 속도가 느려지고 수정 테스트를 하기 위해서 빌드 시간도 늦어지므로 생산성 저하가 발생 합니다.</mark>
2. **수정을 쉽게 할 수 있다.**\
   단일 애플리케이션으로 코드 수정 후 빌드/배포가 용이합니다.\
   <mark style="color:orange;">- 규모가 커지고 코드의 량이 많아 질 수록 개발자는 쉽게 코드를 이해 할 수 없어 버그 수정이 용이 하지 않아지고 새 기능을 추가 하기 위해서 영향도를 파악 하고 정확이 구현하기에 시간이 많이 걸리고 힘들어 집니다 또한 코드가 많아져서 빌드 시간이 오래 걸립니다.</mark>
3. **테스트가 용이하다**.\
   단일 애플리케이션으로 개발자가 코드를 수정 후 시작점 부터 종단까지 테스트를 작성 할 수 있습니다.\
   <mark style="color:orange;">- 비대해지 코드를 빌드 하고 테스트를 진행 하다가 오류가 발생 하며 다시 수정 후 테스트를 진행 하는데 코드 수정 시간 보드 비대한 코드로 인한 개발 Tool 실행 속도, 빌드 속도등의 개발 이외의 시간이 늦어져서 테스트 시간이 많이 걸립니다.</mark>
4. **배포가 쉽다.**\
   WAR, JAR 배포로 복사를 하면 됩니다.\
   <mark style="color:orange;">- 테스트를 완벽히 하였다 하여도 여러 개발자가 커밋한 소스를 병합 하는 과정이 힘들며 병합 이후 테스트를 다시 수행하여 문제가 발견되면 수정 후 다시 병합 과정을 거쳐야 하므로 시간이 많이 소요 됩니다..</mark>
5. **확장이 쉽다.**\
   &#x20;Load Balancer가 분산 시키므로 인스턴스 확장을 통해서 쉽게 할 수 있다.\
   <mark style="color:orange;">- 어떤 업무에서는 성능을 고려 하여 Cache를 사용하거나 In-Memory DB를 사용하거나 No-SQL 을 사용 하는 경우 리소스의 요건에 따라 정당한 서버에 배포를 해야 하는데 단일 애플리케이션이므로 리소스의 배분을 쉽게 할 수 없습니다.</mark>


