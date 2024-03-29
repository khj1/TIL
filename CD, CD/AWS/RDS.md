# RDS( Relational Database Service )
## AWS VPC를 활용해 RDS에 안전하게 접근하기
- 개발 단계에선 퍼블릭 엑세스 가능성을 열어두어도 되지만
- 실제 상용화가 이루어졌을 때는 퍼블릭 엑세스를 닫아놓고 VPC를 활용해 EC2를 통해서만 RDS에 접근할 수 있도록 설정해야한다.
- EC2와 RDS를 같은 VPC에 설정해두면 된다.

### VPC ( Virtual Private Cloud )
내부망을 의미한다.

#### 과정
1. EC2에 생성된 인스턴의 VPC ID, 보안 그룹명, 보안 그룹 아이디를 활용해야한다.
2. 새롭게 보안 그룹을 생성하고 인바운드 규칙을 새롭게 지정한다.
3. 인바운드 규칙으론 우리가 저장한 보안 그룹을 아이디를 통해서만 접근할 수 있도록 설정해준다.
4. RDS 탭으로 넘어가 서브넷 그룹을 생성해줘야한다.
5. 서브넷 그룹의 VPC를 EC2 인스턴스의 VPC와 동일하게 지정해준다.
6. 해당 VPC와 관련된 모든 서브넷을 추가해준다.
7. 이후 RDS 생성 시 서브넷 그룹을 우리가 새로 생성해준 서브넷으로 설정한다.
8. 보안 그룹도 새롭게 생성한 보안 그룹으로 설정한다.
9. 퍼블릭 엑세스 가능성을 닫아준다.
10. 모든 과정이 끝나면 EC2 내에 해당하는 DB를 설치해준다( MySQL, MariaDB )