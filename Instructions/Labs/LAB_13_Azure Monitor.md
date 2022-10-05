---
lab:
  title: 13 - Azure Monitor
  module: Module 04 - Manage security operations
---

# <a name="lab-13-azure-monitor"></a>랩 13: Azure Monitor
# <a name="student-lab-manual"></a>학생용 랩 매뉴얼

## <a name="lab-scenario"></a>랩 시나리오

You have been asked to create a proof of concept of monitoring virtual machine performance. Specifically, you want to:

- 원격 분석 및 로그를 수집할 수 있도록 가상 머신을 구성합니다.
- 수집할 수 있는 원격 분석 및 로그를 보여줍니다.
- 데이터를 사용하고 쿼리하는 방법을 보여줍니다. 

> For all the resources in this lab, we are using the <bpt id="p1">**</bpt>East US<ept id="p1">**</ept> region. Verify with your instructor this is the region to use for class. 

## <a name="lab-objectives"></a>랩 목표

이 랩에서는 다음과 같은 연습을 완료합니다.

- 연습 1: Azure Monitor를 사용하여 Azure 가상 머신에서 데이터 수집

## <a name="azure-monitor"></a>Azure Monitor

![이미지](https://user-images.githubusercontent.com/91347931/157536648-0a286514-a7e2-4058-9dea-e42da21eef76.png)

## <a name="instructions"></a>지침

### <a name="exercise-1-collect-data-from-an-azure-virtual-machine-with-azure-monitor"></a>연습 1: Azure Monitor를 사용하여 Azure 가상 머신에서 데이터 수집

### <a name="exercise-timing-20-minutes"></a>연습 시간: 20분

이 연습에서는 다음 작업을 완료합니다. 

- 작업 1: Azure 가상 머신 배포 
- 작업 2: Log Analytics 작업 영역 만들기
- 작업 3: Log Analytics 가상 머신 확장을 사용하도록 설정
- 작업 4: 가상 머신 이벤트 및 성능 데이터 수집
- 작업 5: 수집된 데이터 보기 및 쿼리 

#### <a name="task-1-deploy-an-azure-virtual-machine"></a>작업 1: Azure 가상 머신 배포

1. Azure portal **`https://portal.azure.com/`** 에 로그인합니다.

    >**참고**: 이 랩에 사용 중인 Azure 구독에 Owner 또는 Contributor 역할이 있는 계정을 사용하여 Azure Portal에 로그인합니다.

2. Open the Cloud Shell by clicking the first icon in the top right of the Azure Portal. If prompted, select <bpt id="p1">**</bpt>PowerShell<ept id="p1">**</ept> and <bpt id="p2">**</bpt>Create storage<ept id="p2">**</ept>.

3. Cloud Shell 창의 왼쪽 위 모서리에 있는 드롭다운 메뉴에서 **PowerShell**이 선택되었는지 확인합니다.

4. Cloud Shell 창 내의 PowerShell 세션에서 다음을 실행하여 이 랩에서 사용할 리소스 그룹을 만듭니다.
  
    ```powershell
    New-AzResourceGroup -Name AZ500LAB131415 -Location 'EastUS'
    ```

    >**참고**: 이 리소스 그룹은 랩 13, 14 및 15에 사용됩니다. 

5. Cloud Shell 창 내의 PowerShell 세션에서 다음을 실행하여 새 Azure 가상 머신을 만듭니다. 

    ><bpt id="p1">**</bpt>Attention<ept id="p1">**</ept>: The New-AzVm command doesn't work in the Azure CLI version 4.24 and Microsoft is currently investigating for resolution.  The work around in this lab is to install and revert back to Az.Compute version 4.23.0, which is unaffected by this issue.
   
    >**지침**: Az.Compute 버전 4.23.0으로 되돌리기 
  
   #### <a name="step-1-download-the-working-version-of-the-module-4230-into-your-cloud-shell-session"></a>1단계: 해당 클라우드 셸 세션에 모듈의 작업 버전(4.23.0) 다운로드 
   **유형**: Install-Module -Name Az.Compute -Force -RequiredVersion 4.23.0

   #### <a name="step-2-start-a-new-powershell-session-that-will-allow-the-azcompute-assembly-version-to-be-loaded"></a>2단계: Az.Compute 어셈블리 버전을 로드하는 새 PowerShell 세션 시작 
   **형식**: pwsh

   #### <a name="step-3-verify-that-version-4230-is-loaded"></a>3단계: 버전 4.23.0이 로드되었는지 확인
   **유형**: Get-Module -Name Az.Compute
   
    ```powershell
    New-AzVm -ResourceGroupName "AZ500LAB131415" -Name "myVM" -Location 'EastUS' -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName   "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress" -OpenPorts 80,3389
    ```

6.  자격 증명에 대한 메시지가 표시되면:

    |설정|값|
    |---|---|
    |사용자 |**localadmin**|
    |암호|**랩 04 > 연습 1 > 작업 1 > 9단계에서 만든 개인 암호를 사용하세요.**|

    >**참고**: 배포가 완료될 때까지 기다립니다. 

7. Cloud Shell 창 내의 PowerShell 세션에서 다음을 실행하여 **myVM**이라는 가상 머신이 생성되고 해당 **ProvisioningState**가 **성공**인지 확인합니다.

    ```powershell
    Get-AzVM -Name 'myVM' -ResourceGroupName 'AZ500LAB131415' | Format-Table
    ```

8. Cloud Shell 창을 닫습니다. 

#### <a name="task-2-create-a-log-analytics-workspace"></a>작업 2: Log Analytics 작업 영역 만들기

이 작업에서는 Log Analytics 작업 영역을 만듭니다. 

1. Azure Portal에서 Azure Portal 페이지 위쪽의 **검색 리소스, 서비스 및 문서** 텍스트 상자에서 **Log Analytics 작업 영역**을 입력하고 **Enter** 키를 누릅니다.

2. **Log Analytics 작업 영역** 블레이드에서  **+ 만들기**를 클릭합니다.

3. **Log Analytics 작업 영역 만들기** 블레이드의  **기본** 탭에서 다음 설정을 지정합니다(다른 설정을 기본값으로 남겨둡니다).

    |설정|값|
    |---|---|
    |Subscription|이 랩에서 사용 중인 Azure 구독의 이름|
    |리소스 그룹|**AZ500LAB131415**|
    |이름|유효하며 전역적으로 고유한 이름|
    |지역|**(미국) 미국 동부**|

4. **검토 + 만들기**를 선택합니다.

5. **Log Analytics 작업 영역 만들기** 블레이드의 **검토 + 만들기** 탭에서 **만들기**를 클릭합니다.

#### <a name="task-3-enable-the-log-analytics-virtual-machine-extension"></a>작업 3: Log Analytics 가상 머신 확장을 사용하도록 설정

In this task, you will enable the Log Analytics virtual machine extension. This extension installs the Log Analytics agent on Windows and Linux virtual machines. This agent collects data from the virtual machine and transfers it to the Log Analytics workspace that you designate. Once the agent is installed it will be automatically upgraded ensuring you always have the latest features and fixes. 

1. Azure Portal에서 **Log Analytics 작업 영역** 블레이드로 다시 이동한 다음 작업 영역 목록에서 이전 작업에서 만든 작업 영역을 나타내는 항목을 클릭합니다.

2. **데이터 원본 연결** 섹션 내 **개요** 페이지의 Log Analytics 작업 영역 블레이드에서 **Azure 가상 머신(VM)** 항목을 클릭합니다.

    >**참고**: 에이전트를 성공적으로 설치하려면 가상 머신이 실행되어야 합니다.

3. 가상 머신 목록에서 이 연습의 첫 번째 작업에 배포한 Azure VM **myVM**을 나타내는 항목을 찾습니다. 이것은 **연결되지 않음**으로 나열되어 있습니다.

4. **myVM** 항목을 클릭한 다음 **myVM** 블레이드에서 **연결**을 클릭합니다. 

5. 가상 머신이 Log Analytics 작업 영역에 연결될 때까지 기다립니다.

    >가상 머신 성능을 모니터링하는 개념 증명을 만들어야 합니다. 

#### <a name="task-4-collect-virtual-machine-event-and-performance-data"></a>작업 4: 가상 머신 이벤트 및 성능 데이터 수집

특히 다음을 수행해야 합니다.

1. Azure Portal에서 이 연습에서 이전에 만든 Log Analytics 작업 영역으로 다시 이동합니다.

2. Log Analytics 작업 영역 블레이드의 **설정** 섹션에서 **레거시 에이전트 관리**를 클릭합니다.

3. **에이전트 구성** 블레이드에서 Windows 이벤트 로그, Windows 성능 카운터, Linux 성능 카운터, IIS 로그 및 Syslog를 포함한 구성 가능한 설정을 검토합니다. 

4. **Windows 이벤트 로그**가 선택되어 있는지 확인하고 **+ Windows 이벤트 로그 추가**를 클릭한 다음 이벤트 로그 유형 목록에서 **시스템**을 선택합니다.

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: This is how you add event logs to the workspace. Other choices include, for example, <bpt id="p1">**</bpt>Hardware events<ept id="p1">**</ept> or <bpt id="p2">**</bpt>Key Management Service<ept id="p2">**</ept>.  

5. **정보** 체크박스 선택을 취소하고 **오류** 및 **경고** 체크박스를 선택한 상태로 둡니다.

6. **Windows 성능 카운터**를 클릭하고 **+ 성능 카운터 추가**를 클릭한 다음 사용 가능한 성능 카운터 목록을 검토하고 다음 카운터를 추가합니다.

    - Memory(\*)\Available Memory Mbytes
    - 프로세스(\*)\\% 프로세서 시간
    - Event Tracing for Windows\Total Memory Usage --- Non-Paged Pool
    - Event Tracing for Windows\Total Memory Usage --- Paged Pool

    >**참고**: 카운터가 추가되고 수집 샘플 간격은 60초로 구성됩니다.
  
7. **에이전트 구성** 블레이드에서 **적용**을 클릭합니다.

#### <a name="task-5-view-and-query-collected-data"></a>작업 5: 수집된 데이터 보기 및 쿼리

이 작업에서는 데이터 컬렉션에서 로그 검색을 실행합니다. 

1. Azure Portal에서 이 연습에서 이전에 만든 Log Analytics 작업 영역으로 다시 이동합니다.

2. Log Analytics 작업 영역 블레이드에 있는 **일반** 섹션에서 **로그**를 클릭합니다.

3. 필요한 경우 **Log Analytics 시작** 창을 닫습니다. 

4. **쿼리** 창의 **모든 쿼리** 열에서 리소스 유형 목록 아래쪽으로 스크롤하고 **가상 머신**을 클릭합니다.
    
5. 미리 정의된 쿼리 목록을 검토하고, **메모리 및 CPU 사용**을 선택하고, 해당 **실행** 단추를 클릭합니다.

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: You can start with the query <bpt id="p2">**</bpt>Virtual machine available memory<ept id="p2">**</ept>. If you don't get any results check the scope is set to virtual machine

6. 쿼리는 새 쿼리 탭에서 자동으로 열립니다. 

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Log Analytics uses the Kusto query language. You can customize the existing queries or create your own. 

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: The results of the query you selected are automatically displayed below the query pane. To re-run the query, click <bpt id="p1">**</bpt>Run<ept id="p1">**</ept>.

    >**참고**: 이 가상 머신은 방금 만들어졌기 때문에 아직 데이터가 없을 수 있습니다. 

    >이 랩의 모든 리소스에 대해 **미국 동부** 지역을 사용하고 있습니다.

    >**참고**: 다음 단계를 사용하여 이 랩의 앞부분에서 배포한 Azure VM에 일부 추가 부하를 생성할 수 있습니다.

    1. Azure VM 블레이드로 이동합니다.
    2. Azure VM 블레이드의 **작업** 섹션에서 **명령 실행**을 선택하고, **RunPowerShellScript** 블레이드에서 다음 스크립트를 입력하고 **실행**을 클릭합니다.
    3. 
       ```cmd
       cmd
       :loop
       dir c:\ /s > SWAP
       goto loop
       ```
       
    4. 이 지역을 수업에 사용할 것인지 강사에게 확인합니다.

> 결과: Log Analytics 작업 영역을 사용하여 데이터 원본 및 쿼리 로그를 구성했습니다. 

**리소스 정리**

>**참고**: Azure Security Center 랩 및 Azure Sentinel 랩에 필요한 리소스를 이 랩에서 제거하지 마세요.
 
