---
lab:
  title: 14 - 클라우드용 Microsoft Defender
  module: Module 04 - Microsoft Defender for Cloud
---

# 랩 14: 클라우드용 Microsoft Defender
# 학생용 랩 매뉴얼

## 랩 시나리오

클라우드 기반 환경용 Microsoft Defender의 개념 증명을 만들라는 요청을 받았습니다. 특히 다음을 수행해야 합니다.

- Azure 리소스를 모니터링하도록 클라우드용 Microsoft Defender를 구성합니다.
- 가상 머신을 위한 클라우드용 Microsoft Defender 권장 사항을 검토합니다.
- 게스트 구성 및 Just-In-Time VM 액세스에 대한 권장 사항을 구현합니다. 
- Secure Score를 사용하여 더욱 안전한 인프라를 만드는 진행 상황을 결정하는 방법을 검토합니다.

> 이 랩의 모든 리소스에 대해 **미국 동부** 지역을 사용하고 있습니다. 이 지역을 수업에 사용할 것인지 강사에게 확인합니다. 

## 랩 목표

이 랩에서는 다음과 같은 연습을 완료합니다.

- 연습 1: 클라우드용 Microsoft Defender 구현

## 클라우드용 Microsoft Defender 다이어그램

![이미지](https://user-images.githubusercontent.com/91347931/157537800-94a64b6e-026c-41b2-970e-f8554ce1e0ab.png)

## Instructions

### 연습 1: 클라우드용 Microsoft Defender 구현

이 연습에서는 다음 작업을 완료합니다.

- 작업 1: 클라우드용 Microsoft Defender 구성
- 작업 2: 클라우드용 Microsoft Defender 권장 사항 검토
- 작업 3: Just-In-Time VM 액세스를 사용하도록 설정하는 클라우드용 Microsoft Defender 권장 사항 구현

#### 작업 1: 클라우드용 Microsoft Defender 구성

이 작업에서는 클라우드용 Microsoft Defender를 온보딩하고 구성합니다.

1. Azure Portal **`https://portal.azure.com/`** 에 로그인합니다.

    >**참고**: 이 랩에 사용 중인 Azure 구독에 Owner 또는 Contributor 역할이 있는 계정을 사용하여 Azure Portal에 로그인합니다.

2. Azure Portal에서 페이지 위쪽에 있는 **리소스, 서비스 및 문서 검색** 텍스트 상자에 **클라우드용 Microsoft Defender**을 입력하고 **Enter** 키를 누릅니다.

3. **Microsoft Defender \| 시작 블레이드에서** **업그레이드**를 클릭합니다.
     
4. **Microsoft Defender \| 시작** 블레이드의 에이전트 설치 탭에서 아래로 스크롤하여 에이전트 설치를 클릭합니다. 

5. **클라우드 \| 시작 Microsoft Defender** 블레이드의 **업그레이드** 탭 >> **향상된 보안 기능을 사용하여 작업 영역 선택** 섹션이 표시될 때까지 아래로 스크롤합니다 >> Log Analytics 작업 영역을 선택하여 **Microsoft Defender 계획을** 켜고 큰 파란색 업그레이드 단추를 클릭합니다.  

    >**참고**: Microsoft Defender 계획의 일부로 사용할 수 있는 모든 기능을 검토합니다. 

6. **클라우드용 Microsoft Defender**로 이동하고 왼쪽의 세로 메뉴 모음에 있는 관리 설정 아래에서 **환경 설정**을 클릭합니다.

7. **클라우드용 Microsoft Defender \| 환경 설정** 블레이드에서 관련 구독을 클릭합니다. 

8. **Defender 계획** 블레이드에서 **모든 클라우드용 Microsoft Defender 계획 사용**을 선택합니다.

9. **클라우드 \| 환경 설정 Microsoft Defender** 블레이드로 돌아가서 구독이 나타날 때까지 확장하고 이전 랩에서 만든 Log Analytics 작업 영역을 나타내는 항목을 클릭합니다.

10. **설정\| Defender 계획** 블레이드에서 **모든 클라우드용 Microsoft Defender 계획 사용을 선택**했는지 확인합니다.

11. **클라우드용 Microsoft Defender \| 설정** 블레이드에서 **데이터 수집**을 선택합니다. **모든 이벤트**를 선택하고 **저장**합니다.

#### 작업 2: 클라우드용 Microsoft Defender 권장 사항 검토

이 작업에서는 클라우드용 Microsoft Defender 권장 사항을 검토합니다. 

1. Azure Portal에서 **클라우드용 Microsoft Defender\| 개요** 블레이드로 다시 이동합니다. 

2. **클라우드용 Microsoft Defender \| 개요** 블레이드에서 **보안 점수** 타일을 검토합니다.

    >**참고**: 현재 점수가 표시되면 적어 둡니다.

3. **클라우드용 Microsoft Defender \| 개요** 블레이드로 돌아와 **평가된 리소스**를 선택합니다.

4. **인벤토리** 블레이드에서 **myVM** 항목을 선택합니다.

    >**참고**: 몇 분 기다렸다가 브라우저 페이지를 새로 고쳐야 항목이 표시될 수도 있습니다.
    
5. **리소스 상태** 블레이드의 **권장 사항** 탭에서 **myVM**에 대한 권장 사항 목록을 검토합니다.

#### 작업 3: Just-In-Time VM 액세스를 사용하도록 설정하는 클라우드용 Microsoft Defender 권장 사항 구현

이 작업에서는 가상 머신에서 Just-In-Time VM 액세스를 사용할 수 있도록 클라우드용 Microsoft Defender 권장 사항을 구현합니다. 

1. Azure Portal에서 **클라우드용 Microsoft Defender \| 개요** 블레이드로 다시 이동하고 **클라우드 보안** 타일에서 **워크로드 보호**를 선택합니다.

2. **워크로드 보호** 블레이드의 **고급 보호** 섹션에서 **Just-In-Time VM 액세스** 타일을 선택하여 **Just-In-Time VM 액세스** 블레이드로 갑니다.

3. **Just-In-Time VM 액세스** 블레이드의 **가상 머신** 섹션에서 **구성되지 않음**을 선택한 뒤 **myVM** 항목을 클릭합니다.

4. **가상 머신** 섹션의 맨 오른쪽에 있는 **1 VM에서 JIT 사용** 옵션을 클릭합니다.

    >**참고**: **myVM** 항목을 사용할 수 있게 되기까지 몇 분간 기다려야 할 수도 있습니다.

5. 포트 **22**를 참조하는 행의 맨 오른쪽에 있는 **JIT VM 액세스 구성** 블레이드에서 타원 단추를 클릭한 다음 **삭제**를 클릭합니다.

6. **JIT VM 액세스 구성** 블레이드에서 **저장**을 클릭합니다.

    >**참고**: 도구 모음의 **알림** 아이콘을 클릭하고 **알림** 블레이드를 확인하여 구성 진행 상황을 모니터링합니다. 

    >**참고**: 이 랩의 권장 사항을 구현하여 보안 점수가 반영되는 데 다소 시간이 걸릴 수 있습니다. 보안 점수를 주기적으로 확인하여 이러한 기능을 구현하는 데 미치는 영향을 확인합니다. 

> 결과: 클라우드용 Microsoft Defender를 온보딩하고 가상 머신 권장 사항을 구현했습니다. 

    >**Note**: Do not remove the resources from this lab as they are needed for the Microsoft Sentinel lab.
