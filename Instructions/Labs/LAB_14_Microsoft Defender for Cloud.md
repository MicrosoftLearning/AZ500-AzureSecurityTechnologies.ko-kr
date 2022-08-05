---
lab:
  title: 14 - 클라우드용 Microsoft Defender
  module: Module 04 - Microsoft Defender for Cloud
ms.openlocfilehash: 647e2dc79012d6fedca9da9a78f6006f64be093b
ms.sourcegitcommit: 18d4f5ccc60ae6d43b27e8b7d4d3ef7f68a02e93
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2022
ms.locfileid: "141483503"
---
# <a name="lab-14-microsoft-defender-for-cloud"></a>랩 14: 클라우드용 Microsoft Defender
# <a name="student-lab-manual"></a>학생용 랩 매뉴얼

## <a name="lab-scenario"></a>랩 시나리오

클라우드 기반 환경용 Microsoft Defender의 개념 증명을 만들라는 요청을 받았습니다. 특히 다음을 수행해야 합니다.

- Azure 리소스를 모니터링하도록 클라우드용 Microsoft Defender를 구성합니다.
- 가상 머신을 위한 클라우드용 Microsoft Defender 권장 사항을 검토합니다.
- 게스트 구성 및 Just-In-Time VM 액세스에 대한 권장 사항을 구현합니다. 
- Secure Score를 사용하여 더욱 안전한 인프라를 만드는 진행 상황을 결정하는 방법을 검토합니다.

> 이 랩의 모든 리소스에 대해 **미국 동부** 지역을 사용하고 있습니다. 이 지역을 수업에 사용할 것인지 강사에게 확인합니다. 

## <a name="lab-objectives"></a>랩 목표

이 랩에서는 다음과 같은 연습을 완료합니다.

- 연습 1: 클라우드용 Microsoft Defender 구현

## <a name="microsoft-defender-for-cloud-diagram"></a>클라우드용 Microsoft Defender 다이어그램

![이미지](https://user-images.githubusercontent.com/91347931/157537800-94a64b6e-026c-41b2-970e-f8554ce1e0ab.png)

## <a name="instructions"></a>Instructions

### <a name="exercise-1-implement-microsoft-defender-for-cloud"></a>연습 1: 클라우드용 Microsoft Defender 구현

이 연습에서는 다음 작업을 완료합니다.

- 작업 1: 클라우드용 Microsoft Defender 구성
- 작업 2: 클라우드용 Microsoft Defender 권장 사항 검토
- 작업 3: Just-In-Time VM 액세스를 사용하도록 설정하는 클라우드용 Microsoft Defender 권장 사항 구현

#### <a name="task-1-configure-microsoft-defender-for-cloud"></a>작업 1: 클라우드용 Microsoft Defender 구성

이 작업에서는 클라우드용 Microsoft Defender를 온보딩하고 구성합니다.

1. Azure Portal **`https://portal.azure.com/`** 에 로그인합니다.

    >**참고**: 이 랩에 사용 중인 Azure 구독에 Owner 또는 Contributor 역할이 있는 계정을 사용하여 Azure Portal에 로그인합니다.

2. Azure Portal에서 페이지 위쪽에 있는 **리소스, 서비스 및 문서 검색** 텍스트 상자에 **클라우드용 Microsoft Defender** 을 입력하고 **Enter** 키를 누릅니다.

3. 이전에 완료되지 않은 경우 **클라우드용 Microsoft Defender | 시작** 블레이드에서 **업그레이드** 를 클릭합니다.
     
4. 이전에 완료되지 않은 경우 **클라우드용 Microsoft Defender | 시작** 블레이드의 **에이전트 설치** 탭에서 아래로 스크롤하여 **에이전트 설치** 를 클릭합니다.

5. **클라우드용 Microsoft Defender | 시작** 블레이드의 **업그레이드** 탭 >> **강화된 보안 기능이 있는 작업 영역 선택** 섹션에서 Log Analytics 작업 영역을 선택하여 **Microsoft Defender 플랜** 을 켭니다. 

    >**참고**: Microsoft Defender 계획의 일부로 사용할 수 있는 모든 기능을 검토합니다. 

6. **클라우드용 Microsoft Defender** 로 이동하고 왼쪽의 세로 메뉴 모음에 있는 관리 설정 아래에서 **환경 설정** 을 클릭합니다.

7. **클라우드용 Microsoft Defender | 환경 설정** 블레이드에서 관련 구독을 클릭합니다. 

8. **Defender 계획** 블레이드에서 **모든 클라우드용 Microsoft Defender 계획 사용** 을 선택하고 **저장** 을 클릭합니다.

9. **설정 | Defender 플랜** 블레이드의 왼쪽 세로 메뉴에서 **자동 프로비저닝** 을 클릭합니다. 

10. **설정 | 자동 프로비저닝** 블레이드에서 첫 번째 항목인 **Azure VM용 Log Analytics 에이전트** 의 자동 프로비저닝이 **켜기** 로 설정되어 있는지 확인합니다.

11. **설정 | 워크플로 자동화** 블레이드에서 사용 가능한 설정을 검토합니다. 

    >**참고**: 위협 탐지 경고 및 클라우드용 Microsoft Defender 권장 사항을 기반으로 작업을 트리거할 수 있습니다. Logic Apps를 기반으로 작업을 구성할 수도 있습니다. 
    
12. **워크플로 자동화 추가** 블레이드에서 사용 가능한 설정을 검토합니다.

    >**참고**: 클라우드용 Microsoft Defender는 시스템 업데이트 상태, OS 보안 구성 및 엔드포인트 보호를 포함한 가상 머신에 대한 많은 인사이트를 제공합니다.

13. **워크플로 자동화 추가** 블레이드에서 **취소** 를 클릭합니다.

14. **클라우드용 Microsoft Defender | 환경 설정** 블레이드로 다시 이동하여 구독을 확장하고 이전 랩에서 만든 Log Analytics 작업 영역을 나타내는 항목을 클릭합니다.

15. **설정 | Defender 플랜** 블레이드에서 **모든 클라우드용 Microsoft Defender 플랜 사용** 이 선택되어 있는지 확인하고 **저장** 을 클릭합니다.

16. **클라우드용 Microsoft Defender | 설정** 블레이드에서 **데이터 수집** 을 선택합니다. **모든 이벤트** 를 선택하고 **저장** 합니다.


#### <a name="task-2-review-the-microsoft-defender-for-cloud-recommendation"></a>작업 2: 클라우드용 Microsoft Defender 권장 사항 검토

이 작업에서는 클라우드용 Microsoft Defender 권장 사항을 검토합니다. 

1. Azure Portal에서 **클라우드용 Microsoft Defender| 개요** 블레이드로 다시 이동합니다. 

2. **클라우드용 Microsoft Defender | 개요** 블레이드에서 **보안 점수** 타일을 검토합니다.

    >**참고**: 현재 점수가 표시되면 적어 둡니다.

3. **클라우드용 Microsoft Defender | 개요** 블레이드로 돌아와서 **평가된 리소스** 를 선택합니다.

4. **인벤토리** 블레이드에서 **myVM** 항목을 선택합니다.

    >**참고**: 몇 분 기다렸다가 브라우저 페이지를 새로 고쳐야 항목이 표시될 수도 있습니다.
    
5. **리소스 상태** 블레이드의 **권장 사항** 탭에서 **myVM** 에 대한 권장 사항 목록을 검토합니다.


#### <a name="task-3-implement-the-microsoft-defender-for-cloud-recommendation-to-enable-just-in-time-vm-access"></a>작업 3: Just-In-Time VM 액세스를 사용하도록 설정하는 클라우드용 Microsoft Defender 권장 사항 구현

이 작업에서는 가상 머신에서 Just-In-Time VM 액세스를 사용할 수 있도록 클라우드용 Microsoft Defender 권장 사항을 구현합니다. 

1. Azure Portal에서 **클라우드용 Microsoft Defender | 개요** 블레이드로 다시 이동하고 **클라우드 보안** 타일에서 **워크로드 보호** 를 선택합니다.

2. **워크로드 보호** 블레이드의 **고급 보호** 섹션에서 **Just-In-Time VM 액세스** 타일을 선택하고, **Just-In-Time VM 액세스 블레이드** 에서 **Just-In-Time VM 액세스 시도** 를 클릭합니다.

    >**참고**: VM이 나열되지 않은 경우 **가상 머신** 블레이드로 이동하여 **구성** 을 클릭하고, **Just-In-Time VM 액세스** 에서 **Just-In-Time VM 사용** 옵션을 클릭합니다. 위의 단계를 반복하여 **클라우드용 Microsoft Defender** 로 다시 이동하고 페이지를 새로 고치면 VM이 표시됩니다.

3. **Just-In-Time VM 액세스** 에서 **구성되지 않음** 을 선택한 후에 **myVM** 항목을 클릭합니다.

    >**참고**: **myVM** 항목을 사용할 수 있게 되기까지 몇 분간 기다려야 할 수도 있습니다.

4. **VM 1대에서 JIT 사용** 을 선택합니다.

5. 포트 **22** 를 참조하는 행의 맨 오른쪽에 있는 **JIT VM 액세스 구성** 블레이드에서 타원 단추를 클릭한 다음 **삭제** 를 클릭합니다.

6. **JIT VM 액세스 구성** 블레이드에서 **저장** 을 클릭합니다.

    >**참고**: 도구 모음의 **알림** 아이콘을 클릭하고 **알림** 블레이드를 확인하여 구성 진행 상황을 모니터링합니다. 

    >**참고**: 이 랩의 권장 사항을 구현하여 보안 점수가 반영되는 데 다소 시간이 걸릴 수 있습니다. 보안 점수를 주기적으로 확인하여 이러한 기능을 구현하는 데 미치는 영향을 확인합니다. 

> 결과: 클라우드용 Microsoft Defender를 온보딩하고 가상 머신 권장 사항을 구현했습니다. 

    >**Note**: Do not remove the resources from this lab as they are needed for the Azure Sentinel lab.
