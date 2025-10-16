---
lab:
  title: 09 - 클라우드용 Microsoft Defender의 서버용 강화된 보안 기능 구성
  module: Module 03 - Configure and manage threat protection by using Microsoft Defender for Cloud
---

# 랩 09: 클라우드용 Microsoft Defender의 서버용 강화된 보안 기능 구성

# 학생용 랩 매뉴얼

## 랩 시나리오

글로벌 전자 상거래 회사의 Azure 보안 엔지니어는 회사의 클라우드 인프라를 보호해야 합니다. 해당 조직은 Azure VM(가상 머신) 및 온-프레미스 서버에 크게 의존하여 중요한 애플리케이션을 실행하고, 고객 데이터를 관리하고, 트랜잭션을 처리합니다. CISO(최고 정보 보안 책임자)는 사이버 위협, 취약성 및 잘못된 구성으로부터 이러한 리소스를 보호하기 위해 강화된 보안 조치의 필요성을 확인했습니다. 클라우드용 Microsoft Defender에서 서버용 Microsoft Defender를 사용하도록 설정하여 Azure VM 및 하이브리드 서버 모두에 대한 고급 위협 방지 및 보안 모니터링을 제공하는 임무를 맡았습니다.

## 랩 목표

- 서버에 대한 클라우드용 Microsoft Defender 보안 강화 기능 구성
  
- 서버용 Microsoft Defender 계획 2에 대한 강화된 보안 기능 검토

## 연습 지침

### 서버에 대한 클라우드용 Microsoft Defender 보안 강화 기능 구성

1. Azure Portal에서 페이지 위쪽의 리소스, 서비스 및 문서 검색 텍스트 상자에 **클라우드용 Microsoft Defender**를 입력하고 **Enter** 키를 누릅니다.

2. **클라우드용 Microsoft Defender**의 **관리 블레이드**에서 **환경 설정**으로 이동합니다. **구독** 섹션이 표시될 때까지 환경 설정 폴더를 확장한 다음 **구독**을 클릭하여 세부 정보를 확인합니다.

   ![클라우드용 Microsoft Defender의 환경 설정 스크린샷](../media/defender-for-cloud-environment-settings.png)
   
3. **설정** 블레이드의 **Defender 계획**에서 **CWP(클라우드 워크로드 보호)** 를 확장합니다.

4. **CWP(클라우드 워크로드 보호) 계획** 목록에서 **서버**를 선택합니다. 페이지 오른쪽에서 **상태**를 **꺼짐**에서 **켜짐**으로 변경한 다음 **저장**을 클릭합니다.

5. **서버용 Microsoft Defender 플랜 2**의 세부 정보를 검토하려면 **플랜 변경 >** 을 선택합니다.

   참고: CWP(클라우드 워크로드 보호) 서버 계획을 Off에서 On으로 활성화하도록 설정하면 서버용 Microsoft Defender 플랜 2가 활성화됩니다.
 
   ![클라우드용 Microsoft Defender 플랜 선택 페이지 스크린샷](../media/defender-for-cloud-plan-selection.png)
   
> **결과**: 구독에서 서버용 Microsoft Defender 플랜 2를 사용하도록 설정했습니다.
