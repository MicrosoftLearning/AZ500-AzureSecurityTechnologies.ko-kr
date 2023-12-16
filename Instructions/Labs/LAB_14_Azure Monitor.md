---
lab:
  title: 14 - Azure Monitor
  module: Module 04 - Manage security operations
---

# 랩 14: Azure Monitor

# 학생용 랩 매뉴얼

## 랩 시나리오

Azure Monitor 에이전트를 사용하여 가상 머신에서 이벤트 및 성능 카운터를 수집하라는 요청을 받았습니다.

> 이 랩의 모든 리소스에 대해 **미국 동부** 지역을 사용하고 있습니다. 이 지역을 수업에 사용할 것인지 강사에게 확인합니다. 

## 랩 목표

이 랩에서는 다음과 같은 연습을 완료합니다:

- 연습 1: Azure 가상 머신 배포
- 연습 2: Log Analytics 작업 영역 만들기
- 연습 3: Azure Storage 계정 만들기
- 연습 4: 데이터 콜렉션 규칙을 만듭니다.
  
## 지침

### 연습 1: Azure 가상 머신 배포

### 운동 타이밍: 10분

이 연습에서는 다음 작업을 완료합니다. 

- 작업 1: Azure 가상 머신을 배포합니다. 

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

### 연습 2: Log Analytics 작업 영역 만들기

### 운동 타이밍: 10분

이 연습에서는 다음 작업을 완료합니다. 

- 작업 1: Log Analytics 작업 영역을 만듭니다.

#### 작업 1: Log Analytics 작업 영역 만들기

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

### 연습 3: Azure Storage 계정 만들기

### 예상 소요 시간: 10분

이 연습에서는 다음 작업을 완료합니다.

- 작업 1: Azure Storage 계정을 만듭니다.

#### 작업 1: Azure Storage 계정 만들기

이 작업에서는 스토리지 계정을 만듭니다.

1. Azure Portal 페이지의 맨 위에 있는 **리소스, 서비스 및 문서** 검색 텍스트 상자에 Storage 계정을** 입력**하고 Enter** 키를 누릅니**다.

2. Azure Portal의 **Storage 계정** 블레이드에서 + 만들기** 단추를 클릭하여 **새 스토리지 계정을 만듭니다.

    ![이미지](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/73eb9241-d642-455a-a1ff-b504670395c0)

3. **스토리지 계정 만들기** 블레이드의 **기본** 탭에서 다음 설정을 지정합니다(나머지는 기본값을 그대로 유지).

    |설정|값|
    |---|---|
    |구독|이 랩에서 사용 중인 Azure 구독의 이름|
    |리소스 그룹|**AZ500LAB131415**|
    |스토리지 계정 이름|3~24자 사이의 문자와 숫자로 구성된 전역적으로 고유한 이름|
    |위치|**(미국) 미국 동부**|
    |성능|**표준(범용 v2 계정)**|
    |다시 배포|**LRS(로컬 중복 스토리지)**|

4. 스토리지 계정** 만들기 블레이드의 기본 사항** 탭에서 **검토를** 클릭하고 **유효성 검사 프로세스가 완료되기를 기다린 다음 만들기**를 클릭합니다**.**

     ![이미지](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/d443821c-2ddf-4794-87fa-bfc092980eba)

    >**참고**: 스토리지 계정이 만들어질 때까지 기다립니다. 이 작업은 2분 정도 걸립니다.

### 연습 3: 데이터 수집 규칙 만들기

### 예상 소요 시간: 15분

이 연습에서는 다음 작업을 완료합니다.

- 작업 1: 데이터 수집 규칙을 만듭니다.

#### 작업 1: 데이터 수집 규칙을 만듭니다.

이 작업에서는 데이터 수집 규칙을 만듭니다.

1. Azure Portal 페이지의 맨 위에 있는 **리소스, 서비스 및 문서** 검색 텍스트 상자에 Monitor**를 입력**하고 Enter** 키를 누릅니**다.

2. **모니터 설정** 블레이드에서 데이터 수집 규칙을 클릭합니다 **.**

    ![이미지](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/2184da69-12c2-476b-b2b2-b80620e822a6)

3. **데이터 수집 규칙** 만들기 블레이드의 기본** 사항 탭에서 **다음 설정을 지정합니다.
  
    |설정|값|
    |---|---|
    |**규칙 세부 정보**|
    |규칙 이름|**DCR1**|
    |구독|이 랩에서 사용 중인 Azure 구독의 이름|
    |리소스 그룹|**AZ500LAB131415**|
    |지역|**미국 동부**|
    |플랫폼 유형|**Windows**|
    |데이터 컬렉션 엔드포인트|*비워 둡니다.*|

    ![이미지](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/9b58c4ce-b7a8-4acf-8289-d95b270a6083)


4. 다음: 리소스 > 레이블이 지정된 **단추를 클릭하여 계속 진행합니다** .

5. 리소스 탭에서 **+ 리소스** 추가를 선택한 **다음, 데이터 수집 엔드포인트를 사용하도록 검사 **.****

    ![이미지](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/c8388619-c254-4c80-a1ff-dde2f35ed350)

6. 다음: 수집 및 배달 >** 레이블이 지정된 **단추를 클릭하여 계속 진행합니다.

7. + 데이터 원본** 추가를 클릭한 **다음 **데이터 원본** 추가 페이지에서 데이터 원본 유형** 드롭다운 메뉴를 변경**하여 성능 카운터를 표시**합니다.** 다음 기본 설정을 그대로 둡니다.

    |설정|값|
    |---|---|
    |**성능 카운터**|**샘플 속도(초)**|
    |CPU|60|
    |메모리|60|
    |디스크|60|
    |네트워크|60|

   ![이미지](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/a24e44ad-1d10-4533-80e2-bae1b3f6564d)

8. 다음: 대상 >** 레이블이 지정된 **단추를 클릭하여 계속 진행합니다.
  
9. **대상 유형** 드롭다운 메뉴를 변경하여 Azure Monitor 로그를 표시**합니다.** **구독** 창에서 구독*이 *표시되는지 확인하고 이전에 만든 Log Analytics 작업 영역을 반영하도록 계정 또는 네임스페이스** 드롭다운 메뉴를 변경**합니다.

   ![이미지](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/481843f5-94c4-4a8f-bf51-a10d49130bf8)

10. 페이지 아래쪽에서 **데이터 원본** 추가를 클릭합니다.
    
    ![이미지](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/964091e7-bbbc-4ca8-8383-bb2871a1e7f0)

13. **검토 + 만들기**를 클릭합니다.

    ![이미지](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/50dd8407-a106-4540-9e14-ae40a3c04830)

14. **만들기**를 클릭합니다.

> 결과: Azure Monitor 에이전트를 사용하여 가상 머신에서 이벤트 및 성능 카운터를 수집하기 위해 Azure 가상 머신, Log Analytics 작업 영역, Azure Storage 계정 및 데이터 수집 규칙을 배포했습니다.

>**참고**: 클라우드용 Microsoft Defender 랩 및 Microsoft Sentinel 랩에 필요하므로 이 랩에서 리소스를 제거하지 마세요.
 