---
lab:
  title: 01 - 역할 기반 액세스 제어
  module: Module 01 - Manage Identity and Access
ms.openlocfilehash: 9ddbfd416d9897bcdf891c9c9265d8205277c176
ms.sourcegitcommit: 79ca7b110859fe71a3849a28fdc781cad95d1567
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/15/2022
ms.locfileid: "146381343"
---
# <a name="lab-01-role-based-access-control"></a>랩 01: 역할 기반 액세스 제어
# <a name="student-lab-manual"></a>학생용 랩 매뉴얼

## <a name="lab-scenario"></a>랩 시나리오

Azure 사용자와 그룹이 어떻게 만들어지는지 보여주는 개념 증명을 만들라는 요청을 받았습니다. 역할 기반 액세스 제어를 사용하여 그룹에 역할을 할당하는 방법도 포함됩니다. 특히, 다음과 같은 내용이 필요합니다.

- 최대식(사용자 계정)이 구성원으로 포함된 상급 관리자 그룹을 만듭니다.
- 정지우(사용자 계정)를 구성원으로 포함하여 하급 관리자 그룹을 만듭니다.
- 최두리(사용자 계정)를 구성원으로 포함하는 서비스 데스크 그룹을 만듭니다.
- 서비스 데스크 그룹에 가상 머신 기여자 역할을 할당합니다. 

> 이 랩의 모든 리소스에 대해 **미국 동부** 지역을 사용하고 있습니다. 이 지역을 수업에 사용할 것인지 강사에게 확인합니다. 

## <a name="lab-objectives"></a>랩 목표

이 랩에서는 다음과 같은 연습을 완료합니다:

- 연습 1: 최대식(사용자 계정)을 Azure Portal의 구성원으로 포함하는 상급 관리자 그룹을 만듭니다. 
- 연습 2: 정지우(사용자 계정)가 PowerShell의 구성원인 하급 주니어 관리자 그룹을 만듭니다.
- 연습 3: 사용자 Dylan Williams가 Azure CLI의 구성원인 서비스 데스크 그룹을 만듭니다. 
- 연습 4: 서비스 데스크 그룹에 가상 머신 기여자 역할을 할당합니다.

## <a name="role-based-access-control-architecture-diagram"></a>역할 기반 액세스 제어 아키텍처 다이어그램

![이미지](https://user-images.githubusercontent.com/91347931/157751243-5aa6e521-9bc1-40af-839b-4fd9927479d7.png)

## <a name="instructions"></a>Instructions

### <a name="exercise-1-create-the-senior-admins-group-with-the-user-account-joseph-price-as-its-member"></a>연습 1: Joseph Price 사용자 계정을 구성원으로 하여 선임 관리자 그룹을 만듭니다. 

#### <a name="estimated-timing-10-minutes"></a>예상 소요 시간: 10분

이 연습에서는 다음 작업을 완료합니다.

- 작업 1: Azure Portal을 사용하여 Joseph Price에 대한 사용자 계정을 만듭니다.
- 작업 2: Azure Portal을 사용하여 선임 관리자 그룹을 만들고 Joseph Price 사용자 계정을 그룹에 추가합니다.

#### <a name="task-1-use-the-azure-portal-to-create-a-user-account-for-joseph-price"></a>작업 1: Azure Portal을 사용하여 Joseph Price에 대한 사용자 계정 만들기 

이 작업에서는 Joseph Price에 대한 사용자 계정을 만들겠습니다. 

1. 브라우저 세션을 시작하고 Azure Portal **`https://portal.azure.com/`** 에 로그인합니다.

    >**참고**: 이 랩에서 사용하는 Azure 구독의 Owner 또는 Contributor 역할과 해당 구독과 연결된 Azure AD 테넌트의 전역 관리자 역할을 가진 계정을 사용하여 Azure Portal에 로그인합니다.

2. Azure Portal 페이지 상단의 **검색 리소스, 서비스 및 문서** 텍스트 상자에서 **Azure Active Directory** 를 입력하고 **Enter** 키를 누릅니다.

3. Azure Active Directory 테넌트의 **개요** 블레이드의 **관리** 섹션에서 **사용자** 및 **+ 새 사용자** 를 차례로 선택합니다.

4. **새 사용자** 블레이드에서 **사용자 만들기** 옵션이 선택되었는지 확인하고 다음 설정을 지정합니다.

   |설정|값|
   |---|---|
   |사용자 이름|**Joseph**|
   |Name|**Joseph Price**|

5. **사용자 이름** 옆에 있는 복사 아이콘을 클릭하여 전체 사용자를 복사합니다.

6. **자동 생성** 암호가 선택되었는지 확인하고 **암호 표시** 체크박스를 선택하여 자동으로 생성된 암호를 식별합니다. Joseph에게 사용자 이름과 함께 이 암호를 제공해야 합니다. 

7. **만들기** 를 클릭합니다.

8. **사용자 \| 모든 사용자** 블레이드를 새로 고침하여 새로운 사용자가 해당 Azure AD 테넌트에서 생성됐는지 확인합니다.

#### <a name="task2-use-the-azure-portal-to-create-a-senior-admins-group-and-add-the-user-account-of-joseph-price-to-the-group"></a>작업 2: Azure Portal을 사용하여 선임 관리자 그룹을 만들고 Joseph Price 사용자 계정을 그룹에 추가합니다.

이 작업에서는 *선임 관리자* 그룹을 만들고 그룹에 Joseph Price 사용자 계정을 추가하여 그룹 소유자로 구성합니다.

1. Azure Portal에서 Azure Active Directory 테넌트를 표시하는 블레이드로 다시 이동합니다. 

2. **관리** 섹션에서 **그룹** 을 클릭한 다음, **+ 새 그룹** 을 선택합니다.
 
3. **새 그룹** 블레이드에서 다음 설정을 지정합니다(다른 설정은 기본 값으로 남겨둡니다).

   |설정|값|
   |---|---|
   |그룹 형식|**보안**|
   |그룹 이름|**선임 관리자**|
   |멤버 자격 유형|**할당됨**|
    
4. **소유자 추가** 블레이드에서 **선택한 소유자 없음** 링크를 클릭하고 **Joseph Price** 를 선택한 후 **선택** 을 클릭합니다.

5. **구성원 추가** 블레이드에서 **선택한 구성원 없음** 링크를 클릭하고 **Joseph Price** 를 선택하고 **선택** 을 클릭합니다.

6. **새 그룹** 블레이드로 돌아가서 **만들기** 를 클릭합니다.

> 결과: Azure Portal을 사용하여 사용자와 그룹을 만들고 사용자를 그룹을 그룹에 할당했습니다. 

### <a name="exercise-2-create-a-junior-admins-group-containing-the-user-account-of-isabel-garcia-as-its-member"></a>연습 2: 정지우(사용자 계정)를 구성원으로 포함하여 하급 관리자 그룹을 만듭니다.

#### <a name="estimated-timing-10-minutes"></a>예상 소요 시간: 10분

이 연습에서는 다음 작업을 완료합니다.

- 작업 1: PowerShell을 사용하여 Isabel Garcia에 대한 사용자 계정을 만듭니다.
- 작업 2: PowerShell을 사용하여 후임 관리자 그룹을 만들고 Isabel Garcia의 사용자 계정을 그룹에 추가합니다. 

#### <a name="task-1-use-powershell-to-create-a-user-account-for-isabel-garcia"></a>작업 1: PowerShell을 사용하여 Isabel Garcia에 대한 사용자 계정을 만듭니다.

이 작업에서는 PowerShell을 사용하여 Isabel Garcia의 사용자 계정을 만들겠습니다.

1. Azure Portal 오른쪽 상단에 있는 첫 번째 아이콘을 클릭하여 Cloud Shell을 엽니다. 메시지가 표시되면 **PowerShell** 및 **스토리지 만들기** 를 선택합니다.

2. Cloud Shell 창의 왼쪽 위 모서리에 있는 드롭다운 메뉴에서 **PowerShell** 이 선택되었는지 확인합니다.

   >**참고**: 복사된 텍스트를 Cloud Shell에 붙여 넣으려면 창 내에서 마우스 오른쪽 단추를 클릭하고 **붙여넣기** 를 선택합니다. 또는 **Shift+Insert** 키 조합을 사용할 수 있습니다.

3. Cloud Shell 창 내의 PowerShell 세션에서 다음을 실행하여 암호 프로필 개체를 만듭니다.

    ```powershell
    $passwordProfile = New-Object -TypeName Microsoft.Open.AzureAD.Model.PasswordProfile
    ```

4. Cloud Shell 창 내의 PowerShell 세션에서 다음을 실행하여 프로필 개체 내에서 암호값을 설정합니다.
    ```powershell
    $passwordProfile.Password = "Pa55w.rd1234"
    ```

5. Cloud Shell 창 내의 PowerShell 세션에서 다음을 실행하여 Azure Active Directory에 연결합니다.

    ```powershell
    Connect-AzureAD
    ```
      
6. Cloud Shell 창 내의 PowerShell 세션에서 다음을 실행하여 Azure AD 테넌트의 이름을 식별합니다. 

    ```powershell
    $domainName = ((Get-AzureAdTenantDetail).VerifiedDomains)[0].Name
    ```

7. Cloud Shell 창 내의 PowerShell 세션에서 다음을 실행하여 Isabel Garcia에 대한 사용자 계정을 만듭니다. 

    ```powershell
    New-AzureADUser -DisplayName 'Isabel Garcia' -PasswordProfile $passwordProfile -UserPrincipalName "Isabel@$domainName" -AccountEnabled $true -MailNickName 'Isabel'
    ```

8. Cloud Shell 창의 PowerShell 세션에서 다음을 실행하여 Azure AD 사용자를 나열합니다(Joseph 및 Isabel의 계정은 나열됨에 표시되어야 합니다). 

    ```powershell
    Get-AzureADUser 
    ```

#### <a name="task2-use-powershell-to-create-the-junior-admins-group-and-add-the-user-account-of-isabel-garcia-to-the-group"></a>작업 2: PowerShell을 사용하여 후임 관리자 그룹을 만들고 Isabel Garcia의 사용자 계정을 그룹에 추가합니다.

이 작업에서는 후임 관리자 그룹을 만들고 PowerShell을 사용하여 Isabel Garcia의 사용자 계정을 그룹에 추가합니다.

1. Cloud Shell 창 내의 동일한 PowerShell 세션에서 다음을 실행하여 후임 관리자라는 새 보안 그룹을 만듭니다.
    
    ```powershell
    New-AzureADGroup -DisplayName 'Junior Admins' -MailEnabled $false -SecurityEnabled $true -MailNickName JuniorAdmins
    ```

2. Cloud Shell 창 내의 PowerShell 세션에서 다음을 실행하여 Azure AD 테넌트에 그룹을 나열합니다(목록에는 상위 관리자 및 하위 관리자 그룹이 포함되어야 함).

    ```powershell
    Get-AzureADGroup
    ```

3. Cloud Shell 창 내의 PowerShell 세션에서 다음을 실행하여 Isabel Garcia의 사용자 계정에 대한 참조를 얻습니다.

    ```powershell
    $user = Get-AzureADUser -Filter "MailNickName eq 'Isabel'"
    ```

4. Cloud Shell 창 내의 PowerShell 세션에서 다음을 실행하여 Isabel의 사용자 계정을 하위 관리자 그룹에 추가합니다.
    
    ```powershell
    Add-AzADGroupMember -MemberUserPrincipalName $user.userPrincipalName -TargetGroupDisplayName "Junior Admins" 
    ```

5. Cloud Shell 창 내의 PowerShell 세션에서 후임 관리자 그룹에 Isabel의 사용자 계정이 포함되어 있는지 확인하려면 다음을 실행합니다.

    ```powershell
    Get-AzADGroupMember -GroupDisplayName "Junior Admins"
    ```

> 결과: PowerShell을 사용하여 사용자와 그룹 계정을 만들고 사용자 계정을 그룹 계정에 추가했습니다. 


### <a name="exercise-3-create-a-service-desk-group-containing-the-user-account-of-dylan-williams-as-its-member"></a>연습 3: 최두리(사용자 계정)를 구성원으로 포함하는 서비스 데스크 그룹을 만듭니다.

#### <a name="estimated-timing-10-minutes"></a>예상 소요 시간: 10분

이 연습에서는 다음 작업을 완료합니다.

- 작업 1: Azure CLI를 사용하여 Dylan Williams의 사용자 계정을 만듭니다.
- 작업 2: Azure CLI를 사용하여 서비스 데스크 그룹을 만들고 Dylan의 사용자 계정을 그룹에 추가합니다. 

#### <a name="task-1-use-azure-cli-to-create-a-user-account-for-dylan-williams"></a>작업 1: Azure CLI를 사용하여 Dylan Williams의 사용자 계정을 만듭니다.

이 작업에서는 Dylan Williams의 사용자 계정을 만듭니다.

1. Cloud Shell 창의 왼쪽 위 모서리에 있는 드롭다운 메뉴에서 **Bash** 를 선택하고 메시지가 표시되면 **확인** 을 클릭합니다. 

2. Cloud Shell 창 내의 Bash 세션에서 다음을 실행하여 해당 Azure AD 테넌트의 이름을 식별합니다.

    ```cli
    DOMAINNAME=$(az ad signed-in-user show --query 'userPrincipalName' | cut -d '@' -f 2 | sed 's/\"//')
    ```

3. Cloud Shell 창 내의 Bash 세션에서 다음을 실행하여 Dylan Williams라는 사용자를 만듭니다. *yourdomain* 을 사용합니다.
 
    ```cli
    az ad user create --display-name "Dylan Williams" --password "Pa55w.rd1234" --user-principal-name Dylan@$DOMAINNAME
    ```
      
4. Cloud Shell 내의 Bash 세션에서 다음을 실행하여 Azure AD 사용자 계정을 나열합니다(목록에 Joseph, Isabe 및 Dylan의 사용자 계정이 포함되어야 합니다)
    
    ```cli
    az ad user list --output table
    ```

#### <a name="task-2-use-azure-cli-to-create-the-service-desk-group-and-add-the-user-account-of-dylan-to-the-group"></a>작업 2: Azure CLI를 사용하여 서비스 데스크 그룹을 만들고 Dylan의 사용자 계정을 그룹에 추가합니다. 

이 작업에서는 서비스 데스크 그룹을 만들고 그룹에 Dylan을 할당합니다. 

1. Cloud Shell 창 내의 동일한 Bash 세션에서 다음을 실행하여 서비스 데스크라는 새 보안 그룹을 만듭니다.

    ```cli
    az ad group create --display-name "Service Desk" --mail-nickname "ServiceDesk"
    ```
 
2. Cloud Shell 창 내의 Bash 세션에서 다음을 실행하여 Azure AD 그룹을 나열합니다(목록에는 서비스 데스크, 선임 관리자 및 후임 관리자 그룹이 포함되어야 합니다).

    ```cli
    az ad group list -o table
    ```

3. Cloud Shell 창 내의 Bash 세션에서 다음 명령을 실행하여 Dylan Williams 사용자 계정에 대한 참조를 가져옵니다. 

    ```cli
    USER=$(az ad user list --filter "displayname eq 'Dylan Williams'")
    ```

4. Cloud Shell 창 내의 Bash 세션에서 다음을 실행하여 Dylan Williams 사용자 계정의 objectId 속성을 얻습니다. 

    ```cli
    OBJECTID=$(echo $USER | jq '.[].id' | tr -d '"')
    ```

5. Cloud Shell 창 내의 Bash 세션에서 다음을 실행하여 서비스 데스크 그룹에 Dylan의 사용자 계정을 추가합니다. 

    ```cli
    az ad group member add --group "Service Desk" --member-id $OBJECTID
    ```

6. Cloud Shell 창 내의 Bash 세션에서 다음을 실행하여 서비스 데스크 그룹의 구성원을 나열하고 Dylan의 사용자 계정이 포함되어 있는지 확인합니다.

    ```cli
    az ad group member list --group "Service Desk"
    ```

7. Cloud Shell 창을 닫습니다.

> 결과: Azure CLI를 사용하여 사용자와 그룹 계정을 만들고 그룹에 사용자 계정을 추가했습니다. 


### <a name="exercise-4-assign-the-virtual-machine-contributor-role-to-the-service-desk-group"></a>연습 4: 서비스 데스크 그룹에 가상 머신 기여자 역할을 할당합니다.

#### <a name="estimated-timing-10-minutes"></a>예상 소요 시간: 10분

이 연습에서는 다음 작업을 완료합니다.

- 작업 1: 리소스 그룹을 만듭니다. 
- 작업 2: 리소스 그룹에 서비스 데스크 가상 머신 Contributor 권한을 할당합니다.  

#### <a name="task-1-create-a-resource-group"></a>작업 1: 리소스 그룹 만들기

1. Azure Portal에서 Azure Portal 페이지 위쪽의 **리소스, 서비스 및 문서 검색** 텍스트 상자에 **리소스 그룹** 을 입력하고 **Enter** 키를 누릅니다.

2. **리소스 그룹** 블레이드에서 **+ 만들기** 를 클릭하고 다음 설정을 지정합니다.

   |설정|값|
   |---|---|
   |구독 이름|Azure 구독의 이름|
   |리소스 그룹 이름|**AZ500Lab01**|
   |위치|**미국 동부**|

3. **검토 + 만들기** 를 클릭한 다음 **만들기** 를 클릭합니다.

   >**참고**: 리소스 그룹이 배포될 때까지 기다립니다. **알림** 아이콘(오른쪽 상단)을 사용하여 배포 상태의 진행률을 트랙킹합니다.

4. **리소스 그룹** 블레이드에서 페이지를 새로 고침하고 새 리소스 그룹이 리소스 그룹 목록에 나타나는지 확인합니다.


#### <a name="task-2-assign-the-service-desk-virtual-machine-contributor-permissions"></a>작업 2: 서비스 데스크 가상 머신 Contributor 권한을 할당합니다. 

1. **리소스 그룹** 블레이드에서 **AZ500LAB01** 리소스 그룹 항목을 클릭합니다.

2. **AZ500Lab01** 블레이드의 가운데 창에서 **액세스 제어(IAM)** 를 클릭합니다.

3. **AZ500Lab01 \| 액세스 제어(IAM)** 블레이드에서 **+ 추가** 를 클릭하고 드롭다운 메뉴에서 **역할 할당 추가** 를 클릭합니다.

4. **역할 할당 추가** 블레이드에서 다음 설정을 지정하고 각 단계 후 **다음** 을 클릭합니다.

   |설정|값|
   |---|---|
   |검색 탭의 역할|**Virtual Machine 참가자**|
   |액세스 할당(멤버 창 아래)|**사용자, 그룹 또는 서비스 주체**|
   |(+멤버 선택) 선택|**서비스 데스크**|

5. **검토 + 할당** 을 두 번 클릭하여 역할 할당을 만듭니다.

6. **액세스 제어(IAM)** 블레이드에서 **역할 할당** 을 선택합니다.

7. **AZ500Lab01 \| 액세스 제어(IAM)** 블레이드의 **액세스 확인** 탭에서 **이름 또는 이메일 주소로 검색** 텍스트 상자에 **Dylan Williams** 를 입력합니다.

8. 검색 결과 목록에서 Dylan Williams의 사용자 계정을 선택하고 **Dylan Williams 할당 - AZ500Lab01** 블레이드에서 새로 생성된 할당을 확인합니다.

9. **Dylan Williams 할당 - AZ500Lab01** 블레이드를 닫습니다.

10. **Joseph Price** 의 액세스를 확인하려면 마지막 두 단계를 똑같이 반복합니다. 

> 결과: RBAC 권한을 할당하고 확인했습니다. 

**리소스 정리**

> 더 이상 사용하지 않는 새로 만든 Azure 리소스는 모두 제거하세요. 사용하지 않는 리소스를 제거하면 예상하지 못한 비용이 발생하지 않습니다.

1. Azure Portal 오른쪽 위의 첫 번째 아이콘을 클릭하여 Cloud Shell을 엽니다. 

2. Cloud Shell 창의 왼쪽 위 모서리에 있는 드롭다운 메뉴에서 **PowerShell** 을 선택하고 메시지가 표시되면 **확인** 을 클릭합니다. 

3. Cloud Shell 창 내의 PowerShell 세션에서 다음을 실행하여 이 랩에서 만든 리소스 그룹을 제거합니다.
  
    ```
    Remove-AzResourceGroup -Name "AZ500LAB01" -Force -AsJob
    ```

4.  **Cloud Shell** 창을 닫습니다. 
