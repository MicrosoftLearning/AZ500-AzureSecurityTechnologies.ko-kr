---
lab:
  title: 09 - 클라우드용 Microsoft Defender
  module: Module 03 - Manage security posture by using Microsoft Defender for Cloud
---

# 랩 09: 클라우드용 Microsoft Defender
# 학생용 랩 매뉴얼

## 랩 시나리오

클라우드 기반 환경용 Microsoft Defender의 개념 증명을 만들라는 요청을 받았습니다. 특히 다음을 수행해야 합니다.

- 가상 머신을 모니터링하도록 클라우드용 Microsoft Defender의 향상된 서버용 보안 기능을 구성합니다.
- 가상 머신을 위한 클라우드용 Microsoft Defender 권장 사항을 검토합니다.
- 게스트 구성 및 Just-In-Time VM 액세스에 대한 권장 사항을 구현합니다. 
- Secure Score를 사용하여 더욱 안전한 인프라를 만드는 진행 상황을 결정하는 방법을 검토합니다.

> 이 랩의 모든 리소스에 대해 **미국 동부** 지역을 사용하고 있습니다. 이 지역을 수업에 사용할 것인지 강사에게 확인합니다. 

## 랩 목표

이 랩에서는 다음과 같은 연습을 완료합니다.

- 연습 1: 클라우드용 Microsoft Defender 구현

## 클라우드용 Microsoft Defender 다이어그램

![이미지](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/c31055cc-de95-41f6-adef-f09d756a68eb)

## 지침

### 연습 1: 클라우드용 Microsoft Defender 구현

이 연습에서는 다음 작업을 완료합니다.

- 작업 1: 클라우드용 Microsoft Defender 구성
- 작업 2: 클라우드용 Microsoft Defender 권장 사항 검토
- 작업 3: Just-In-Time VM 액세스를 사용하도록 설정하는 클라우드용 Microsoft Defender 권장 사항 구현

#### 작업 1: 클라우드용 Microsoft Defender의 향상된 서버용 보안 기능

이 작업에서는 클라우드용 Microsoft Defender의 향상된 서버용 보안 기능을 온보딩하고 구성합니다.

1. 브라우저 세션을 시작하고 [Azure 구독](https://azure.microsoft.com/en-us/free/?azure-portal=true)에 로그인합니다. 관리 액세스 권한이 있는 경우입니다.

2. Azure Portal에서 페이지 위쪽에 있는 리소스, 서비스 및 문서 검색 텍스트 상자에 클라우드용 Microsoft Defender을 입력하고 Enter 키를 누릅니다.

3. 클라우드용 Microsoft Defender의 관리 블레이드에서 환경 설정 영역으로 이동합니다. 구독 섹션이 표시될 때까지 환경 설정 폴더를 확장한 다음 구독을 클릭하여 세부 정보를 확인합니다.

4. 설정 블레이드의 Defender 계획에서 CWP(클라우드 워크로드 보호)를 확장합니다.
  
5. CWP(클라우드 워크로드 보호) 계획 목록에서 서버를 선택합니다. 페이지 오른쪽에서 상태를 Off에서 On으로 변경한 다음 저장을 클릭합니다.
  
6. 서버용 Microsoft Defender 플랜 2의 세부 정보를 검토하려면 계획 을 선택합니다.

>**참고**: CWP(클라우드 워크로드 보호) 서버 계획을 Off에서 On으로 활성화하도록 설정하면 서버용 Microsoft Defender 플랜 2가 활성화됩니다.

#### 작업 2: 클라우드용 Microsoft Defender 권장 사항 검토

이 작업에서는 클라우드용 Microsoft Defender 권장 사항을 검토합니다. 

1. Azure Portal에서 **클라우드용 Microsoft Defender\| 개요** 블레이드로 다시 이동합니다. 

2. **클라우드용 Microsoft Defender \| 개요** 블레이드에서 **보안 점수** 타일을 검토합니다.

    >**참고**: 현재 점수가 표시되면 적어 둡니다.

3. **클라우드용 Microsoft Defender \| 개요** 블레이드로 다시 이동하여 **평가된 리소스**를 클릭합니다.

4. **인벤토리** 블레이드에서 **myVM** 항목을 클릭합니다.

    >**참고**: 몇 분 기다렸다가 브라우저 페이지를 새로 고쳐야 항목이 표시될 수도 있습니다.
    
5. **리소스 상태** 블레이드의 **권장 사항** 탭에서 **myVM**에 대한 권장 사항 목록을 검토합니다.

#### 작업 3: Just-In-Time VM 액세스를 사용하도록 설정하는 클라우드용 Microsoft Defender 권장 사항 구현

이 작업에서는 가상 머신에서 Just-In-Time VM 액세스를 사용할 수 있도록 클라우드용 Microsoft Defender 권장 사항을 구현합니다. 

1. Azure Portal에서 **클라우드용 Microsoft Defender \| 개요** 블레이드로 다시 이동하여 왼쪽 탐색 패널의 **클라우드 보안** 아래에서 **워크로드 보호**를 클릭합니다.

2. **클라우드용 Microsoft Defender \| 워크로드 보호** 블레이드에서 **고급 보호** 섹션까지 아래로 스크롤하고 **Just-in-time VM 액세스** 타일을 클릭합니다.

3. **Just-in-time VM 액세스** 블레이드의 **가상 머신** 섹션에서 **구성되지 않음**을 선택한 다음 **myVM** 항목의 확인란을 선택합니다.

    >**참고**: 항목이 표시되려면 몇 분 정도 기다렸다가 브라우저 페이지를 새로 고치고 다시 **구성되지 않음**을 선택해야 할 수도 있습니다.

4. **가상 머신** 섹션의 맨 오른쪽에 있는 **1 VM에서 JIT 사용** 옵션을 클릭합니다.

5. 포트 **22**를 참조하는 행의 맨 오른쪽에 있는 **JIT VM 액세스 구성** 블레이드에서 타원 단추를 클릭한 다음 **삭제**를 클릭합니다.

6. **JIT VM 액세스 구성** 블레이드에서 **저장**을 클릭합니다.

    >**참고**: 도구 모음의 **알림** 아이콘을 클릭하고 **알림** 블레이드를 확인하여 구성 진행 상황을 모니터링합니다. 

    >**참고**: 이 랩의 권장 사항을 구현하여 보안 점수가 반영되는 데 다소 시간이 걸릴 수 있습니다. 보안 점수를 주기적으로 확인하여 이러한 기능을 구현하는 데 미치는 영향을 확인합니다. 

> 결과: 클라우드용 Microsoft Defender를 온보딩하고 가상 머신 권장 사항을 구현했습니다. 

    >**Note**: Do not remove the resources from this lab as they are needed for the Microsoft Sentinel lab.
