---
lab:
  title: 15 - Microsoft Sentinel
  module: Module 04 - Manage Security Operations
---

# <a name="lab-15-microsoft-sentinel"></a>랩 15: Microsoft Sentinel
# <a name="student-lab-manual"></a>학생용 랩 매뉴얼

## <a name="lab-scenario"></a>랩 시나리오

**참고:** **Azure Sentinel**의 이름이 **Microsoft Sentinel**로 변경됨 

You have been asked to create a proof of concept of Microsoft Sentinel-based threat detection and response. Specifically, you want to:

- Azure Activity 및 클라우드용 Microsoft Defender에서 데이터 수집을 시작합니다.
- 기본 제공 및 사용자 지정 경고 추가 
- 플레이북을 사용하여 인시던트에 대한 응답을 자동화하는 방법을 검토합니다.

> For all the resources in this lab, we are using the <bpt id="p1">**</bpt>East US<ept id="p1">**</ept> region. Verify with your instructor this is the region to use for class. 

## <a name="lab-objectives"></a>랩 목표

이 랩에서는 다음과 같은 연습을 완료합니다.

- 연습 1: Microsoft Sentinel 구현

## <a name="microsoft-sentinel-diagram"></a>Microsoft Sentinel 다이어그램

![이미지](https://user-images.githubusercontent.com/91347931/157538440-4953be73-90be-4edd-bd23-b678326ba637.png)

## <a name="instructions"></a>Instructions

## <a name="lab-files"></a>랩 파일:

- **\\Allfiles\\Labs\\15\\changeincidentseverity.json**

### <a name="exercise-1-implement-microsoft-sentinel"></a>연습 1: Microsoft Sentinel 구현

### <a name="estimated-timing-30-minutes"></a>예상 소요 시간: 30분

이 연습에서는 다음 작업을 완료합니다.

- 작업 1: Microsoft Sentinel 온보딩
- 작업 2: Azure 활동을 Sentinel에 연결
- 작업 3: Azure 활동 데이터 커넥터를 사용하는 규칙을 만듭니다. 
- 작업 4: 플레이북 만들기
- 작업 5: 사용자 지정 경고를 만들고 플레이북을 자동 응답으로 구성합니다.
- 작업 6: 인시던트를 호출하고 연결된 작업을 검토합니다.

#### <a name="task-1-on-board-azure-sentinel"></a>작업 1: Azure Sentinel 온보딩

이 작업에서는 Microsoft Sentinel을 온보딩하고 Log Analytics 작업 영역을 연결합니다. 

1. Azure portal **`https://portal.azure.com/`** 에 로그인합니다.

    >**참고**: 이 랩에 사용 중인 Azure 구독에 Owner 또는 Contributor 역할이 있는 계정을 사용하여 Azure Portal에 로그인합니다.

2. Azure Portal에서 페이지 상단에 있는 **리소스, 서비스 및 문서 검색** 텍스트 상자에 **Microsoft Sentinel**을 입력하고 **Enter** 키를 누릅니다.

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: If this is your first attempt to action Microsoft Sentinel in the Azure dashboard complete the following step(s): In the Azure portal, in the <bpt id="p2">**</bpt>Search resources,  services, and docs<ept id="p2">**</ept> text box at the top of the Azure portal page, type <bpt id="p3">**</bpt>Microsoft Sentinel<ept id="p3">**</ept> and press the <bpt id="p4">**</bpt>Enter<ept id="p4">**</ept> key. Select <bpt id="p1">**</bpt>Microsoft Sentinel<ept id="p1">**</ept> from the <bpt id="p2">**</bpt>Services<ept id="p2">**</ept> view.
  
3. **Microsoft Sentinel** 블레이드에서 **+ 만들기**를 클릭합니다.

4. **작업 영역에 Microsoft Sentinel 추가** 블레이드에서, Azure Monitor 랩에서 만든 Log Analytics 작업 영역을 선택하고 **추가**를 클릭합니다.

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Microsoft Sentinel has very specific requirements for workspaces. For example, workspaces created by Microsoft Defender for Cloud can not be used. Read more at <bpt id="p1">[</bpt>Quickstart: On-board Azure Sentinel<ept id="p1">](https://docs.microsoft.com/en-us/azure/sentinel/quickstart-onboard)</ept>
    
#### <a name="task-2-configure-microsoft-sentinel-to-use-the-azure-activity-data-connector"></a>작업 2: Azure Sentinel을 구성하여 Azure Activity 데이터 커넥터를 사용합니다. 

이 작업에서는 Sentinel을 구성하여 Azure 활동 데이터 커넥터를 사용합니다.  

1. Azure Portal에서 **\| 개요** 블레이드의 **구성** 섹션에서 **데이터 커넥터**를 클릭합니다. 

2. **\|데이터 커넥터** 블레이드에서 사용 가능한 커넥터 목록을 검토하고 검색 창에 **Azure**를 입력한 다음 **Azure Activity** 커넥터를 나타내는 항목을 선택하고(필요한 경우 왼쪽에 있는 \<<를 사용하여 메뉴 모음을 숨김), 해당 설명 및 상태를 검토한 다음 **커넥터 페이지 열기**를 클릭합니다.

3. On the <bpt id="p1">**</bpt>Azure Activity<ept id="p1">**</ept> blade the <bpt id="p2">**</bpt>Instructions<ept id="p2">**</ept> tab should be selected, note the <bpt id="p3">**</bpt>Prerequisites<ept id="p3">**</ept> and scroll down to the <bpt id="p4">**</bpt>Configuration<ept id="p4">**</ept>. Take note of the information describing the connector update. Your Azure Pass subscription never used the legacy connection method so you can skip step 1 (the <bpt id="p1">**</bpt>Disconnect All<ept id="p1">**</ept> button will be grayed out) and proceed to step 2.

4. 2단계인 **진단 설정 새 파이프라인을 통해 구독 연결**에서 "Azure Policy 할당 마법사를 시작하고 지침을 따름" 지침을 검토한 다음 **Azure Policy 할당 마법사 시작\>** 을 클릭합니다.

5. On the <bpt id="p1">**</bpt>Configure Azure Activity logs to stream to specified Log Analytics workspace<ept id="p1">**</ept> (Assign Policy page) <bpt id="p2">**</bpt>Basics<ept id="p2">**</ept> tab, click the <bpt id="p3">**</bpt>Scope elipsis (...)<ept id="p3">**</ept> button. In the <bpt id="p1">**</bpt>Scope<ept id="p1">**</ept> page choose your Azure Pass subscription from the drop-down subscription list and click the <bpt id="p2">**</bpt>Select<ept id="p2">**</ept> button at the bottom of the page.

    >**참고**: 리소스 그룹은 선택하지 *마세요*.

6. Microsoft Sentinel 기반 위협 탐지 및 응답의 개념 증명을 만들라는 메시지가 표시됩니다.

7. 특히 다음을 수행해야 합니다.

8. **수정** 탭 아래쪽에 있는 **다음** 단추를 클릭하고 **비준수 메시지** 탭으로 진행합니다.  원할 경우 비준수 메시지를 입력하고 **비준수 메시지** 탭 아래쪽에 있는 **검토 + 만들기** 단추를 클릭합니다.

9. Click the <bpt id="p1">**</bpt>Create<ept id="p1">**</ept> button. You should observe three succeeded status messages: <bpt id="p1">**</bpt>Creating policy assignment succeeded, Role Assignments creation succeeded, and Remediation task creation succeeded<ept id="p1">**</ept>.

    >**참고**: 알림, 종 아이콘을 통해 3가지 성공 작업을 확인할 수 있습니다.

10. **Azure 활동** 창에 **데이터 수신됨** 그래프가 표시되는지 확인합니다. 브라우저 창을 새로 고쳐야 할 수도 있습니다.  

    >**참고**: 상태에 "연결됨"이 표시되고 데이터 수신됨 그래프가 표시되기까지 15분 이상 걸릴 수 있습니다.

#### <a name="task-3-create-a-rule-that-uses-the-azure-activity-data-connector"></a>작업 3: Azure 활동 데이터 커넥터를 사용하는 규칙을 만듭니다. 

이 작업에서는 Azure 활동 데이터 커넥터를 사용하는 규칙을 검토하고 만듭니다. 

1. **Microsoft Sentinel \| 구성** 블레이드에서 **Analytics**을 클릭합니다. 

2. **Microsoft Sentinel \| Analytics** 블레이드에서 **규칙 템플릿** 탭을 클릭합니다. 

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Review the types of rules you can create. Each rule is associated with a specific Data Source.

3. In the listing of rule templates, type <bpt id="p1">**</bpt>Suspicious<ept id="p1">**</ept> into the search bar form and click the <bpt id="p2">**</bpt>Suspicious number of resource creation or deployment<ept id="p2">**</ept> entry associated with the <bpt id="p3">**</bpt>Azure Activity<ept id="p3">**</ept> data source. And then, in the pane displaying the rule template properties, click <bpt id="p1">**</bpt>Create rule<ept id="p1">**</ept> (scroll to the right of the page if needed).

    >**참고**: 이 규칙은 중간 심각도를 가지고 있습니다. 

4. **분석 규칙 마법사 - 템플릿에서 새 규칙 만들기** 블레이드의 **일반** 탭에서 기본 설정을 수락하고 **다음: 규칙 논리 설정>** 을 선택합니다.

5. **분석 규칙 마법사 - 템플릿에서 새 규칙 만들기** 블레이드의 **규칙 논리 설정** 탭에서 기본 설정을 수락하고 **다음: 인시던트 설정(미리 보기) >** 를 클릭합니다.

6. **분석 규칙 마법사 - 템플릿에서 새 규칙 만들기** 블레이드의 **인시던트 설정** 탭에서 기본 설정을 수락하고 **다음: 자동 응답** 을 클릭합니다. 

    >**참고**: 여기서 논리 앱으로 구현된 플레이북을 규칙에 추가하여 문제를 자동으로 수정할 수 있습니다.

7. **분석 규칙 마법사 - 템플릿에서 새 규칙 만들기** 블레이드의 **자동 응답** 탭에서 기본 설정을 수락하고 **다음: 검토 >** 를 클릭합니다. 

8. **분석 규칙 마법사 - 템플릿에서 새 규칙 만들기** 블레이드의 **검토 및 만들기** 탭에서 **만들기**를 클릭합니다.

    >**참고**: 이제 활성 규칙이 만들어졌습니다.

#### <a name="task-4-create-a-playbook"></a>작업 4: 플레이북 만들기

In this task, you will create a playbook. A security playbook is a collection of tasks that can be invoked by Microsoft Sentinel in response to an alert. 

1. Azure Portal에서 Azure Portal 페이지 위쪽의 **리소스, 서비스 및 문서 검색** 텍스트 상자에 **사용자 지정 템플릿 배포**를 입력하고 **Enter** 키를 누릅니다.

2. **사용자 지정 배포** 블레이드에서 **편집기에서 사용자 고유의 탬플릿 빌드** 옵션을 클릭합니다.

3. **템플릿 편집** 블레이드에서 **로드 파일**을 클릭하고 **\\Allfiles\\Labs\\15\\changeincidentseverity.json** 파일을 찾아 **열기**를 클릭합니다.

    >**참고**: [https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks)에서 샘플 플레이북을 확인할 수 있습니다.

4. **템플릿 편집** 블레이드에서 **저장**을 클릭합니다.

5. **사용자 지정 배포** 블레이드에서 다음 설정이 구성되었는지 확인합니다 (다른 설정은 기본값으로 유지).

    |설정|값|
    |---|---|
    |Subscription|이 랩에서 사용 중인 Azure 구독의 이름|
    |리소스 그룹|**AZ500LAB131415**|
    |위치|**(미국) 미국 동부**|
    |플레이북 이름|**Change-Incident-Severity**|
    |사용자 이름|메일 주소|

6. **검토 + 만들기**를 클릭한 다음, **만들기**를 클릭합니다.

    >**참고**: 배포가 완료될 때까지 기다리세요.

7. Azure Portal에서 Azure Portal 페이지 위쪽의 **리소스, 서비스 및 문서 검색** 텍스트 상자에 **리소스 그룹**을 입력하고 **Enter** 키를 누릅니다.

8. **리소스 그룹** 블레이드의 리소스 그룹 목록에서 **AZ500LAB131415** 항목을 클릭합니다.

9. **AZ500LAB131415** 리소스 그룹 블레이드의 리소스 목록에서 새로 만들어진 **변경-인시던트-심각도** 논리 앱을 나타내는 항목을 클릭합니다.

10. **변경-인시던트-심각도** 블레이드에서 **편집**을 클릭합니다.

    >이 랩의 모든 리소스에 대해 **미국 동부** 지역을 사용하고 있습니다.

11. **논리 앱 디자이너** 블레이드에서 첫번째 **연결** 단계를 클릭합니다.

12. **새로 추가**를 클릭하고 **테넌트** 드롭다운 목록의 항목에 사용자의 Azure AD 테넌트 이름이 포함되어 있는지 확인하고 **로그인**을 클릭합니다.

13. 메시지가 표시되면 이 랩에서 사용하고 있는 Azure 구독에 대한 Owner 또는 Contributor 역할이 포함된 사용자 계정을 사용하여 로그인합니다.

14. 두 번째 **연결** 단계를 클릭하고, 이전 단계에서 만든 연결을 나타내는 두 번째 항목을 연결 목록에서 선택합니다.

15. 나머지 두 **연결** 단계에 대해 이전 단계를 반복합니다.

    >**참고**: 모든 단계에 경고가 표시되지 않는지 확인합니다.

16. **Logic Apps 디자이너** 블레이드에서 **저장**을 클릭하여 변경 사항을 저장합니다.

#### <a name="task-5-create-a-custom-alert-and-configure-a-playbook-as-an-automated-response"></a>작업 5: 사용자 지정 경고 만들기 및 플레이북 자동 응답으로 구성

1. Azure Portal에서 **Microsoft Sentinel \| 개요** 블레이드로 다시 이동합니다.

2. **\| 개요** 블레이드의 **구성** 섹션에서 **** 을 클릭합니다.

3. **Microsoft Sentinel \| Analytics** 블레이드에서 **+ 만들기**를 클릭하고 드롭다운 메뉴에서 **예약된 쿼리 규칙**을 클릭합니다. 

4. **분석 규칙 마법사 - 새 규칙 만들기** 블레이드의 **일반** 탭에서 다음의 설정을 지정합니다(나머지 값은 기본값으로 남겨 둡니다).

    |설정|값|
    |---|---|
    |Name|**플레이북 데모**|
    |전술|**초기 액세스**|

5. **다음: 규칙 논리 설정>** 을 선택합니다.

6. **분석 규칙 마법사 - 새 규칙 만들기** 블레이드의 **규칙 논리 설정** 탭의 **규칙 쿼리** 텍스트 상자에서 다음의 규칙 쿼리를 붙여넣기합니다. 

    ```
    AzureActivity
     | where ResourceProviderValue =~ "Microsoft.Security" 
     | where OperationNameValue =~ "Microsoft.Security/locations/jitNetworkAccessPolicies/delete" 
    ```

    >**참고**: 이 규칙은 Just-In-Time VM 액세스 정책의 제거를 식별합니다.

    >이 지역을 수업에 사용할 것인지 강사에게 확인합니다. 


7. **분석 규칙 마법사 - 새 규칙 만들기** 블레이드의 **규칙 논리 설정** 탭의 **쿼리 예약**에서 **5분**마다 **쿼리 실행**을 설정합니다.

8. **분석 규칙 마법사 - 새 규칙 만들기** 블레이드의 **규칙 논리 설정** 탭에서 나머지 설정의 기본값을 수락하고 **다음: 인시던트 설정 >** 을 클릭합니다.

9. **분석 규칙 마법사 - 새 규칙 만들기** 블레이드의 **인시던트 설정** 탭에서 기본 설정을 수락하고 **다음: 자동 응답** 을 클릭합니다. 

10. **분석 규칙 마법사 - 새 규칙 만들기** 블레이드의 **자동 응답** 탭에 있는 **경고 자동화(클래식)** 드롭다운 목록에서 **Change-Incident-Severity** 항목 옆의 체크박스를 선택하고 **다음: 검토 >** 를 클릭합니다. 

11. **분석 규칙 마법사 - 새 규칙 만들기** 블레이드의 **검토 및 만들기** 탭에서 **만들기**를 클릭합니다.

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: You now have a new active rule called <bpt id="p2">**</bpt>Playbook Demo<ept id="p2">**</ept>. If an event identified by the rue logic occurs, it will result in a medium severity alert, which will generate a corresponding incident.

#### <a name="task-6-invoke-an-incident-and-review-the-associated-actions"></a>작업 6: 인시던트를 호출하고 연결된 작업을 검토합니다.

1. Azure Portal에서 **클라우드용 Microsoft Defender\| 개요** 블레이드로 이동합니다.

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Check your secure score. By now it should have updated. 

2. **클라우드용 Microsoft Defender \|워크로드 보호** 블레이드의 **고급 보호** 아래에서 **액세스** 섹션을 클릭합니다.

3. **클라우드용 Microsoft Defender \| Just in time VM 액세스** 블레이드의 **myVM** 가상 머신을 나타내는 행의 오른쪽에서 **타원** 단추를 클릭하고 **제거**를 클릭한 다음 **예**를 클릭합니다.

    ><bpt id="p1">**</bpt>Note:<ept id="p1">**</ept> If the VM is not listed in the <bpt id="p2">**</bpt>Just-in-time VMs<ept id="p2">**</ept>, navigate to <bpt id="p3">**</bpt>Virutal Machine<ept id="p3">**</ept> blade and click the <bpt id="p4">**</bpt>Configuration<ept id="p4">**</ept>, Click the <bpt id="p5">**</bpt>Enable the Just-in-time VMs<ept id="p5">**</ept> option under the <bpt id="p6">**</bpt>Just-in-time Vm's access<ept id="p6">**</ept>. Repeat the above step to navigate back to the <bpt id="p1">**</bpt>Microsoft Defender for Cloud<ept id="p1">**</ept> and refresh the page, the VM will appear.

4. Azure Portal에서 페이지 상단에 있는 **리소르, 서비스 및 문서 검색** 텍스트 상자에서 **활동 로그**를 입력하고 **Enter** 키를 누릅니다.

5. **활동 로그** 블레이드로 이동하고 **JIT 네트워크 액세스 정책 삭제** 항목을 메모합니다. 

    >**참고**: 이 작업은 표시되기까지 1분이 소요될 수 있습니다. 

6. Azure Portal에서 **Microsoft Sentinel \| 개요** 블레이드로 다시 이동합니다.

7. **Microsoft Sentinel \|개요** 블레이드에서 대시보드를 검토하고 Just-In-Time VM 액세스 정책의 삭제에 대응하는 경고가 표시되는지 확인합니다.

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: It can take up to 5 minutes for alerts to appear on the <bpt id="p2">**</bpt>Microsoft Sentinel <ph id="ph1">\|</ph> Overview<ept id="p2">**</ept> blade. If you are not seeing an alert at that point, run the query rule referenced in the previous task to verify that the Just In Time access policy deletion activity has been propagated to the Log Analytics workspace associated with your Microsoft Sentinel instance. If that is not the case, re-create the Just in time VM access policy and delete it again.

8. **Microsoft Sentinel \| 개요** 블레이드의 **위협 관리** 섹션에서 **인시던트**를 클릭합니다.

9. 블레이드에 중간 또는 높은 심각도 수준의 인시던트가 표시되었는지 확인합니다.

    >**참고**: **Microsoft Sentinel \| 인시던트** 블레이드에 인시던트가 표시되려면 최대 5분이 소요될 수 있습니다. 

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Review the <bpt id="p2">**</bpt>Microsoft Sentinel <ph id="ph1">\|</ph> Playbooks<ept id="p2">**</ept> blade. You will find there the count of succesfull and failed runs.

    >**참고**: 인시던트에 다른 심각도 수준 및 상태를 할당할 수 있는 옵션이 있습니다.

> 결과: Microsoft Sentinel 작업 영역을 만들고 Azure Activity 로그에 연결하고, Just-In-Time VM 액세스 정책의 삭제에 대한 응답으로 트리거되는 사용자 지정 경고 및 플레이북을 만들고 구성이 유효한지 확인했습니다.

**리소스 정리**

> Remember to remove any newly created Azure resources that you no longer use. Removing unused resources ensures you will not incur unexpected costs. 

1. In the Azure portal, open the Cloud Shell by clicking the first icon in the top right of the Azure Portal. If prompted, click <bpt id="p1">**</bpt>PowerShell<ept id="p1">**</ept> and <bpt id="p2">**</bpt>Create storage<ept id="p2">**</ept>.

2. Cloud Shell 창의 왼쪽 위 모서리에 있는 드롭다운 메뉴에서 **PowerShell**이 선택되었는지 확인합니다.

3. Cloud Shell 창 내의 PowerShell 세션에서 다음을 실행하여 이 랩에서 만든 리소스 그룹을 제거합니다.
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB131415" -Force -AsJob
    ```
4. **Cloud Shell** 창을 닫습니다.
