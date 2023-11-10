---
lab:
  title: 13 - Azure VM 및 Log Analytics 작업 영역 배포
  module: Module 04 - Manage security operations
---

# 랩 13: Azure VM 및 Log Analytics 작업 영역 배포

# 학생용 랩 매뉴얼

## 랩 시나리오

Azure 가상 machione 및 Log Analytics 작업 영역을 만들라는 요청을 받았습니다.

- Azure 가상 머신 배포
- Log Analytics 작업 영역을 만듭니다.

> 이 랩의 모든 리소스에 대해 **미국 동부** 지역을 사용하고 있습니다. 이 지역을 수업에 사용할 것인지 강사에게 확인합니다. 

## 랩 목표

이 랩에서는 다음과 같은 연습을 완료합니다.

- 연습 1: Azure 가상 머신 만들기
  
## 지침

### 연습 1: Azure 가상 머신 배포

### 운동 타이밍: 10분

이 연습에서는 다음 작업을 완료합니다. 

- 작업 1: Azure 가상 머신 배포 

#### 작업 1: Azure 가상 머신 배포

1. Azure portal **`https://portal.azure.com/`** 에 로그인합니다.

    >**참고**: 이 랩에 사용 중인 Azure 구독에 Owner 또는 Contributor 역할이 있는 계정을 사용하여 Azure Portal에 로그인합니다.

2. Azure Portal 오른쪽 상단에 있는 첫 번째 아이콘을 클릭하여 Cloud Shell을 엽니다. 메시지가 표시되면 **PowerShell** 및 **스토리지 만들기**를 선택합니다.

3. Cloud Shell 창의 왼쪽 위 모서리에 있는 드롭다운 메뉴에서 **PowerShell**이 선택되었는지 확인합니다.

4. Cloud Shell 창 내의 PowerShell 세션에서 다음을 실행하여 이 랩에서 사용할 리소스 그룹을 만듭니다.
  
    ```powershell
    New-AzResourceGroup -Name AZ500LAB131415 -Location 'EastUS'
    ```

    >**참고**: 이 리소스 그룹은 랩 13, 14 및 15에 사용됩니다. 

5. Cloud Shell 창 내의 PowerShell 세션에서 다음을 실행하여 새 Azure 가상 머신을 만듭니다. 

    ```powershell
    New-AzVm -ResourceGroupName "AZ500LAB131415" -Name "myVM" -Location 'EastUS' -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName   "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress" -PublicIpSku Standard -OpenPorts 80,3389 -Size Standard_DS1_v2 
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

#### 작업 2: Log Analytics 작업 영역 만들기

이 작업에서는 Log Analytics 작업 영역을 만듭니다. 

1. Azure Portal에서 Azure Portal 페이지 위쪽의 **검색 리소스, 서비스 및 문서** 텍스트 상자에서 **Log Analytics 작업 영역**을 입력하고 **Enter** 키를 누릅니다.

2. **Log Analytics 작업 영역** 블레이드에서  **+ 만들기**를 클릭합니다.

3. **Log Analytics 작업 영역 만들기** 블레이드의  **기본** 탭에서 다음 설정을 지정합니다(다른 설정을 기본값으로 남겨둡니다).

    |설정|값|
    |---|---|
    |구독|이 랩에서 사용 중인 Azure 구독의 이름|
    |리소스 그룹|**AZ500LAB131415**|
    |이름|유효하며 전역적으로 고유한 이름|
    |지역|**미국 동부**|

4. **검토 + 만들기**를 선택합니다.

5. **Log Analytics 작업 영역 만들기** 블레이드의 **검토 + 만들기** 탭에서 **만들기**를 클릭합니다.

> 결과: Azure 가상 머신을 배포하고 Log Analytics 작업 영역을 만들었습니다.

>**참고**: 클라우드용 Microsoft Defender 랩 및 Microsoft Sentinel 랩에 필요하므로 이 랩에서 리소스를 제거하지 마세요.
 
