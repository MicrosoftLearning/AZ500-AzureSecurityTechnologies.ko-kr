---
lab:
  title: 14 - 클라우드용 Microsoft Defender
  module: Module 04 - Microsoft Defender for Cloud
---

# <a name="lab-14-microsoft-defender-for-cloud"></a>랩 14: 클라우드용 Microsoft Defender
# <a name="student-lab-manual"></a>학생용 랩 매뉴얼

## <a name="lab-scenario"></a>랩 시나리오

You have been asked to create a proof of concept of Microsoft Defender for Cloud-based environment. Specifically, you want to:

- Azure 리소스를 모니터링하도록 클라우드용 Microsoft Defender를 구성합니다.
- 가상 머신을 위한 클라우드용 Microsoft Defender 권장 사항을 검토합니다.
- 게스트 구성 및 Just-In-Time VM 액세스에 대한 권장 사항을 구현합니다. 
- Secure Score를 사용하여 더욱 안전한 인프라를 만드는 진행 상황을 결정하는 방법을 검토합니다.

> For all the resources in this lab, we are using the <bpt id="p1">**</bpt>East US<ept id="p1">**</ept> region. Verify with your instructor this is the region to use for class. 

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

2. Azure Portal에서 페이지 위쪽에 있는 **리소스, 서비스 및 문서 검색** 텍스트 상자에 **클라우드용 Microsoft Defender**을 입력하고 **Enter** 키를 누릅니다.

3. 이전에 완료되지 않은 경우 **클라우드용 Microsoft Defender | 시작** 블레이드에서 **업그레이드**를 클릭합니다.
     
4. 이전에 완료되지 않은 경우 **클라우드용 Microsoft Defender | 시작** 블레이드의 **에이전트 설치** 탭에서 아래로 스크롤하여 **에이전트 설치**를 클릭합니다.

5. **클라우드용 Microsoft Defender | 시작** 블레이드의 **업그레이드** 탭 >> **강화된 보안 기능이 있는 작업 영역 선택** 섹션에서 Log Analytics 작업 영역을 선택하여 **Microsoft Defender 플랜**을 켭니다. 

    >**참고**: Microsoft Defender 계획의 일부로 사용할 수 있는 모든 기능을 검토합니다. 

6. **클라우드용 Microsoft Defender**로 이동하고 왼쪽의 세로 메뉴 모음에 있는 관리 설정 아래에서 **환경 설정**을 클릭합니다.

7. **클라우드용 Microsoft Defender | 환경 설정** 블레이드에서 관련 구독을 클릭합니다. 

8. **Defender 계획** 블레이드에서 **모든 클라우드용 Microsoft Defender 계획 사용**을 선택하고 **저장**을 클릭합니다.

9. **설정 | Defender 플랜** 블레이드의 왼쪽 세로 메뉴에서 **자동 프로비저닝**을 클릭합니다. 

10. **설정 | 자동 프로비저닝** 블레이드에서 첫 번째 항목인 **Azure VM용 Log Analytics 에이전트**의 자동 프로비저닝이 **켜기**로 설정되어 있는지 확인합니다.

11. **설정 | 워크플로 자동화** 블레이드에서 사용 가능한 설정을 검토합니다. 

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: You can trigger actions based threat detection alerts and Microsoft Defender for Cloud recommendations. You can also configure an action based on Logic apps. 
    
12. **워크플로 자동화 추가** 블레이드에서 사용 가능한 설정을 검토합니다.

    >**참고**: 클라우드용 Microsoft Defender는 시스템 업데이트 상태, OS 보안 구성 및 엔드포인트 보호를 포함한 가상 머신에 대한 많은 인사이트를 제공합니다.

13. **워크플로 자동화 추가** 블레이드에서 **취소**를 클릭합니다.

14. **클라우드용 Microsoft Defender | 환경 설정** 블레이드로 다시 이동하여 구독을 확장하고 이전 랩에서 만든 Log Analytics 작업 영역을 나타내는 항목을 클릭합니다.

15. **설정 | Defender 플랜** 블레이드에서 **모든 클라우드용 Microsoft Defender 플랜 사용**이 선택되어 있는지 확인하고 **저장**을 클릭합니다.

16. Select <bpt id="p1">**</bpt>Data collection<ept id="p1">**</ept> from the <bpt id="p2">**</bpt>Microsoft Defender for Cloud | Settings<ept id="p2">**</ept> blade. Select <bpt id="p1">**</bpt>All Events<ept id="p1">**</ept> and <bpt id="p2">**</bpt>Save<ept id="p2">**</ept>.


#### <a name="task-2-review-the-microsoft-defender-for-cloud-recommendation"></a>작업 2: 클라우드용 Microsoft Defender 권장 사항 검토

이 작업에서는 클라우드용 Microsoft Defender 권장 사항을 검토합니다. 

1. Azure Portal에서 **클라우드용 Microsoft Defender| 개요** 블레이드로 다시 이동합니다. 

2. **클라우드용 Microsoft Defender | 개요** 블레이드에서 **보안 점수** 타일을 검토합니다.

    >**참고**: 현재 점수가 표시되면 적어 둡니다.

3. **클라우드용 Microsoft Defender | 개요** 블레이드로 돌아와서 **평가된 리소스**를 선택합니다.

4. **인벤토리** 블레이드에서 **myVM** 항목을 선택합니다.

    >**참고**: 몇 분 기다렸다가 브라우저 페이지를 새로 고쳐야 항목이 표시될 수도 있습니다.
    
5. **리소스 상태** 블레이드의 **권장 사항** 탭에서 **myVM**에 대한 권장 사항 목록을 검토합니다.


#### <a name="task-3-implement-the-microsoft-defender-for-cloud-recommendation-to-enable-just-in-time-vm-access"></a>작업 3: Just-In-Time VM 액세스를 사용하도록 설정하는 클라우드용 Microsoft Defender 권장 사항 구현

이 작업에서는 가상 머신에서 Just-In-Time VM 액세스를 사용할 수 있도록 클라우드용 Microsoft Defender 권장 사항을 구현합니다. 

1. Azure Portal에서 **클라우드용 Microsoft Defender | 개요** 블레이드로 다시 이동하고 **클라우드 보안** 타일에서 **워크로드 보호**를 선택합니다.

2. **워크로드 보호** 블레이드의 **고급 보호** 섹션에서 **Just-In-Time VM 액세스** 타일을 선택하고, **Just-In-Time VM 액세스 블레이드**에서 **Just-In-Time VM 액세스 시도**를 클릭합니다.

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: If the VMs are not listed, navigate to <bpt id="p2">**</bpt>Virtual Machine<ept id="p2">**</ept> blade and click the <bpt id="p3">**</bpt>Configuration<ept id="p3">**</ept>, Click the <bpt id="p4">**</bpt>Enable the Just-in-time VMs<ept id="p4">**</ept> option under the <bpt id="p5">**</bpt>Just-in-time Vm's access<ept id="p5">**</ept>. Repeat the above step to navigate back to the <bpt id="p1">**</bpt>Microsoft Defender for Cloud<ept id="p1">**</ept> and refresh the page, the VM will appear.

3. **Just-In-Time VM 액세스**에서 **구성되지 않음**을 선택한 후에 **myVM** 항목을 클릭합니다.

    >**참고**: **myVM** 항목을 사용할 수 있게 되기까지 몇 분간 기다려야 할 수도 있습니다.

4. **VM 1대에서 JIT 사용**을 선택합니다.

5. 포트 **22**를 참조하는 행의 맨 오른쪽에 있는 **JIT VM 액세스 구성** 블레이드에서 타원 단추를 클릭한 다음 **삭제**를 클릭합니다.

6. **JIT VM 액세스 구성** 블레이드에서 **저장**을 클릭합니다.

    >**참고**: 도구 모음의 **알림** 아이콘을 클릭하고 **알림** 블레이드를 확인하여 구성 진행 상황을 모니터링합니다. 

    >클라우드 기반 환경용 Microsoft Defender의 개념 증명을 만들라는 요청을 받았습니다. 

> 결과: 클라우드용 Microsoft Defender를 온보딩하고 가상 머신 권장 사항을 구현했습니다. 

    >**Note**: Do not remove the resources from this lab as they are needed for the Azure Sentinel lab.
