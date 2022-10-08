---
lab:
  title: 02 - Azure Policy
  module: Module 01 - Manage Identity and Access
---

# <a name="lab-02-azure-policy"></a>랩 02: Azure Policy
# <a name="student-lab-manual"></a>학생용 랩 매뉴얼

## <a name="lab-scenario"></a>랩 시나리오

You have been asked to create a proof of concept showing how Azure policy can be used. Specifically, you need to:

- 리소스가 특정 지역에서만 만들어지도록 허용하는 허용 위치 정책을 만듭니다.
- 허용된 위치에서만 리소스가 만들어지는지 확인하기 위한 테스트

> For all the resources in this lab, we are using the <bpt id="p1">**</bpt>East US<ept id="p1">**</ept> region. Verify with your instructor this is the region to use for class. 

## <a name="lab-objectives"></a>랩 목표

이 랩에서는 다음을 완료합니다.

- 연습 1: Azure Policy 구현. 

## <a name="azure-policy-diagram"></a>Azure Policy 다이어그램

![이미지](https://user-images.githubusercontent.com/91347931/157511920-19c1f06c-86bd-440d-80ac-d96aa27aefff.png)

## <a name="instructions"></a>Instructions

### <a name="exercise-1-implement-azure-policy"></a>연습 1: Azure Policy 구현

#### <a name="estimated-timing-20-minutes"></a>예상 소요 시간: 20분

이 연습에서는 다음 작업을 완료합니다.

- 작업 1: Azure 리소스 그룹을 만듭니다. 
- 작업 2: 허용 위치 정책 할당을 만듭니다.
- 작업 3: 허용 위치 정책 할당이 작동하는지 확인합니다. 

#### <a name="task-1-create-a-resource-group-for-the-lab"></a>작업 1: 랩을 위한 리소스 그룹을 만듭니다. 

이 작업에서는 이 랩에 사용할 리소스 그룹을 만듭니다. 

1. Azure Portal **`https://portal.azure.com/`** 에 로그인합니다.

    >**참고**: 이 랩에 사용 중인 Azure 구독에 Owner 또는 Contributor 역할이 있는 계정을 사용하여 Azure Portal에 로그인합니다.

1. Open the Cloud Shell by clicking the first icon in the top right of the Azure Portal. If prompted, select <bpt id="p1">**</bpt>PowerShell<ept id="p1">**</ept> and <bpt id="p2">**</bpt>Create storage<ept id="p2">**</ept>.

1. Cloud Shell 창의 왼쪽 위 모서리에 있는 드롭다운 메뉴에서 **PowerShell**이 선택되었는지 확인합니다.

1. Cloud Shell 창의 PowerShell 세션에서 다음을 실행하여 리소스 그룹을 만듭니다(위치 매개 변수 값에 대해 강사와 함께 확인).

    ```powershell
    New-AzResourceGroup -Name AZ500LAB02 -Location 'East US'
    ```

1. 클라우드 셸 창 내의 PowerShell 세션에서 다음을 실행하여 리소스 그룹 목록이 나타나면 새 리소스 그룹이 만들어졌는지 확인합니다.

    ```powershell
    Get-AzResourceGroup | format-table
    ```

1. **Cloud Shell**을 닫습니다.

#### <a name="task-2-create-an-allowed-locations-policy-assignment"></a>작업 2: 허용 위치 정책 할당을 만듭니다.

이 작업에서는 허용 위치 정책 할당을 만들고 정책이 사용할 수 있는 Azure 지역을 지정합니다. 

1. Azure Portal에서 Azure Portal 페이지 위쪽의 **리소스, 서비스 및 문서 검색** 텍스트 상자에 **정책**을 입력하고 **Enter** 키를 누릅니다.

1. **정책** 블레이드의 **작성** 섹션에서  **정의**를 선택합니다.

1. Take a minute to browse the built-in definitions. Use the <bpt id="p1">**</bpt>Category<ept id="p1">**</ept> drop-down to filter the list of policies.

1. **검색** 텍스트 상자에 **허용 위치**를 입력합니다. 

   ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: The <bpt id="p2">**</bpt>Allowed locations<ept id="p2">**</ept> policy allows you to restrict location of resources, not resource groups. To restrict locations of resource groups, you can use the <bpt id="p1">**</bpt>Allowed locations for resource groups<ept id="p1">**</ept> policy.

1.  **허용되는 위치** 정책 정의를 클릭하여 세부 정보를 표시합니다. 

   >Azure 정책의 사용 방법을 보여주는 개념 증명을 만들라는 요청을 받았습니다.

1. **허용 위치** 블레이드에서 **할당**을 클릭합니다.

1. **허용 위치** 블레이드의 **기본** 탭에서 **범위** 텍스트 상자 옆에 있는 줄임표(...) 단추를 클릭하고 **범위** 블레이드에서 다음 설정을 지정합니다.

   |설정|값|
   |---|---|
   |Subscription|Azure 구독명|
   |Resource group|**AZ500LAB02**|

1. **선택**을 클릭합니다.

1. **허용 위치** 블레이드의 **기본** 탭에서 다음 설정을 지정합니다(기타 설정은 default 값을 유지합니다).

   |설정|값|
   |---|---|
   |할당 이름|**AZ500LAB02에 대해 영국 남부 허용**|
   |설명|**AZ500LAB02에 대해서만 영국 남부에서 리소스를 만들 수 있도록 허용**|
   |정책 적용|**Enabled**|

1.  **다음**을 클릭합니다.

1. **허용되는 위치** 블레이드에 있는 **매개 변수** 탭의 **허용되는 위치** 드롭다운 목록에서 **영국 남부**를 유일한 허용 위치로 선택합니다.  

   >특히, 다음과 같은 내용이 필요합니다. 

1.  **검토 + 만들기**를 클릭한 다음,  **만들기**를 클릭하여 정책 할당을 만듭니다.  

   >**참고**: 할당에 성공했으며 할당이 완료되는 데 약 30분이 걸릴 수 있다는 알림이 표시됩니다.

   ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: The reason the Azure policy assignment might take up to 30 minutes to take effect is that is has to replicate globally. Typically this takes only a few minutes.  If the next task fails, simply wait a few minutes and attempt its steps again.

#### <a name="task-3-test-the-allowed-locations-policy-assignment"></a>작업 3: 허용 위치 정책 할당을 테스트합니다.

이 작업에서는 허용 위치 정책 할당을 테스트합니다. 

1. Azure Portal 내 Azure Portal 페이지 상단의 **리소스, 서비스 및 문서 검색** 텍스트 상자에 **가상 네트워크**를 입력하고 **Enter** 키를 누릅니다.

1. **가상 네트워크** 블레이드에서  **+ 만들기**를 클릭합니다.

   ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: First, you will try to create a virtual network in East US. Since this is not an allowed location, the request should be blocked. 

1. **가상 네트워크 만들기** 블레이드의 **기본** 탭에서 다음 설정을 지정합니다(기타 설정은 기본값을 유지합니다). 

    |설정|값|
    |---|---|
    |Resource group|**AZ500LAB02**|
    |이름|**myVnet**|
    |지역|**(미국) 미국 동부**|

1.  **검토 + 만들기**를 클릭합니다. 

1. **가상 네트워크 만들기** 블레이드의 **검토 + 만들기** 탭에서 **유효성 검사 실패** 메시지를 확인합니다. 

    > **참고**: **유효성 검사 실패** 경고가 표시되지 않으면 **이전**을 클릭하고 몇 분 더 기다립니다.

1. On the <bpt id="p1">**</bpt>Basics<ept id="p1">**</ept> tab, click the error message link to open the <bpt id="p2">**</bpt>Policy Assignment<ept id="p2">**</ept> blade. You will see the policy assignment that restricts the location.

1. **정책 할당** 블레이드를 닫고, **가상 네트워크 만들기** 블레이드에서 **기본** 탭을 클릭하고, **지역** 드롭다운 목록에서 **(유럽) 영국 남부**를 선택합니다.

1.  **검토 + 만들기**를 클릭하여 유효성 검사가 통과되었는지 확인하고, **만들기**를 클릭하여 가상 네트워크가 성공적으로 만들어졌는지 확인합니다. 

> 연습 결과: 이 연습에서는 기본 제공 정책 정의를 선택하고 리소스 그룹에 할당하여 Azure 정책을 적용하는 방법을 배웠습니다.

**리소스 정리**

> 이 랩의 모든 리소스에 대해 **미국 동부** 지역을 사용하고 있습니다.

1. 이 지역을 수업에 사용할 것인지 강사에게 확인합니다.

1. Cloud Shell 창 내의 PowerShell 세션에서 다음을 실행하여 이 랩에서 만든 리소스 그룹을 제거합니다.
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB02" -Force -AsJob
    ```
1.  **Cloud Shell** 창을 닫습니다. 
  
1. Azure Portal에서 Azure Portal 페이지 위쪽의 **리소스, 서비스 및 문서 검색** 텍스트 상자에 **정책**을 입력하고 **Enter** 키를 누릅니다.

1. 작성 섹션에서 **할당**을 선택합니다.

1. 할당 목록에서는 이 랩에서 만든 **허용되는 위치** 정책의 이름을 선택합니다.

1. 정책 할당에서 **할당 삭제**를 선택한 다음, **예**를 선택합니다.
