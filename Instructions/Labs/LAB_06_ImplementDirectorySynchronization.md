---
lab:
  title: 06 - 디렉터리 동기화 구현
  module: Module 01 - Manage Identity and Access
---

# 랩 06: 디렉터리 동기화 구현
# 학생용 랩 매뉴얼

## 랩 시나리오

온-프레미스 Microsoft Entra Do기본 Services 환경을 Microsoft Entra 테넌트와 통합하는 방법을 보여 주는 개념 증명을 만들라는 요청을 받았습니다. 특히 다음을 수행해야 합니다.

- Microsoft Entra Do기본를 호스팅하는 Azure VM을 배포하여 Microsoft Entra Do기본 Services 포리스트를 구현합니다기본 Services do기본 컨트롤러
- Microsoft Entra 테넌트 만들기 및 구성
- Microsoft Entra Do기본 Services 포리스트를 Microsoft Entra 테넌트와 동기화

> 이 랩의 모든 리소스에 대해 **미국 동부** 지역을 사용하고 있습니다. 이 지역을 수업에 사용할 것인지 강사에게 확인합니다. 

## 랩 목표

이 랩에서는 다음과 같은 연습을 완료합니다:

- 연습 1: Microsoft Entra ID를 호스팅하는 Azure VM 배포기본 컨트롤러
- 연습 2: Microsoft Entra 테넌트 만들기 및 구성
- 연습 3: Microsoft Entra ID 포리스트를 Microsoft Entra 테넌트와 동기화

## 디렉터리 동기화 구현

![이미지](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/5d9cc4c7-7dcd-4d88-920d-9c97d5a482a2)

## 지침

### 연습 1: Microsoft Entra ID를 호스팅하는 Azure VM 배포기본 컨트롤러

### 예상 소요 시간: 10분

이 연습에서는 다음 작업을 완료합니다.

- 작업 1: Azure VM 배포에 사용할 수 있는 DNS 이름 식별
- 작업 2: ARM 템플릿을 사용하여 Microsoft Entra ID를 호스팅하는 Azure VM을 배포합니다기본 컨트롤러

#### 작업 1: Azure VM 배포에 사용할 수 있는 DNS 이름 식별

이 작업에서는 Azure VM 배포용 DNS 이름을 식별합니다. 

1. Azure Portal **`https://portal.azure.com/`** 에 로그인합니다.

    >**참고**: 이 랩에 사용 중인 Azure 구독에 Owner 또는 Contributor 역할이 있는 계정을 사용하여 Azure Portal에 로그인합니다.

2. Azure Portal 오른쪽 상단에 있는 첫 번째 아이콘을 클릭하여 Cloud Shell을 엽니다. 메시지가 표시되면 **PowerShell**, 그리고 **스토리지 만들기**를 클릭합니다.

3. Cloud Shell 창의 왼쪽 위 모서리에 있는 드롭다운 메뉴에서 **PowerShell**이 선택되었는지 확인합니다.

4. Cloud Shell 창 내의 PowerShell 세션에서 다음을 실행하여 이 연습의 다음 작업에서 Azure VM 배포에 사용할 수 있는 사용 가능한 DNS 이름을 식별합니다.

    ```powershell
    Test-AzDnsAvailability -DomainNameLabel <custom-label> -Location '<location>'
    ```

    >**참고**: `<custom-label>` 자리 표시자를 전역적으로 고유할 가능성이 높은 유효한 DNS 이름으로 변경합니다. `<location>` 자리 표시자를 이 랩에서 사용할 Active Directory 도메인 컨트롤러를 호스트하는 Azure VM을 배포하려는 지역의 이름으로 바꿉니다.

    >**참고**: Azure VM을 프로비전할 수 있는 Azure 지역을 식별하려면 [ **https://azure.microsoft.com/en-us/regions/offers/** ](https://azure.microsoft.com/en-us/regions/offers/)를 참조하세요.

5. 이 명령이 **True**를 반환하는지 확인합니다. True가 반환되지 않으면 **True**가 반환될 때까지 다른 `<custom-label>` 값을 사용해 같은 명령을 다시 실행합니다.

6. 성공적인 결과가 반환된 `<custom-label>` 값을 기록합니다. 다음 작업에서 해당 값이 필요합니다.

7. Cloud Shell 창을 닫습니다.

#### 작업 2: ARM 템플릿을 사용하여 Microsoft Entra ID를 호스팅하는 Azure VM을 배포합니다기본 컨트롤러

이 작업에서는 Microsoft Entra ID를 호스트하는 Azure VM을 배포합니다기본 컨트롤러

1. 동일한 브라우저 창에서 다른 브라우저 탭을 열고 **https://github.com/Azure/azure-quickstart-templates/tree/master/application-workloads/active-directory/active-directory-new-domain** 으로 이동합니다.

2. **새 AD 포리스트**를 사용하여 Azure VM 만들기 페이지에서 Azure**에 배포를 클릭합니다**. 이렇게 하면 Azure Portal에 **Create an Azure VM with a new AD Forest** 블레이드로 브라우저가 자동으로 리디렉션됩니다. 

3. **새 AD 포리스트가 있는 Azure VM 만들기 **블레이드에서 **매개 변수 편집**을 클릭합니다.

4. **매개 변수 편집** 블레이드에서 **파일 로드**를 클릭하고, **열기** 대화 상자에서 **\\\\AllFiles\Labs\\06\\active-directory-new-domain\\azuredeploy.parameters.json** 폴더로 이동하여 **열기**를 클릭한 다음 **저장**을 클릭합니다.

5. **새 AD 포리스트를 사용하여 Azure VM 만들기** 블레이드에서 다음 설정을 지정합니다(나머지는 기존 값을 그대로 유지).

   |설정|값|
   |---|---|
   |구독|Azure 구독명|
   |Resource group|**새로 만들기**를 클릭하고 **AZ500LAB06**을 이름으로 입력합니다.|
   |지역|이전 작업에서 확인한 Azure 지역|
   |관리자 사용자 이름|**학생**|
   |관리자 암호|**랩 04 > 연습 1 > 작업 1 > 9단계에서 만든 개인 암호를 사용하세요.**|
   |도메인 이름|**adatum.com**|
   |DNS 접두사|이전 작업에서 만든 DNS 호스트 이름|
   |VM 크기|**Standard_D2s_v3**|

6. **새 AD 포리스트가 있는 Azure VM 만들기** 블레이드에서 **검토 + 만들기**, **만들기**를 차례로 클릭합니다.

    >**참고**: 배포가 완료될 때까지 기다리지 말고 다음 연습을 진행합니다. 배포에는 약 15분이 소요될 수 있습니다. 이 랩의 세 번째 연습에서이 작업에 배포된 가상 머신을 사용합니다.

> 결과: 이 연습을 완료한 후 Azure Resource Manager 템플릿을 사용하여 Microsoft Entra ID do기본 컨트롤러를 호스트하는 Azure VM 배포를 시작했습니다.


### 연습 2: Microsoft Entra 테넌트 만들기 및 구성 

### 예상 소요 시간: 20분

이 연습에서는 다음 작업을 완료합니다.

- 작업 1: Microsoft Entra 테넌트 만들기
- 작업 2: 새 Microsoft Entra 테넌트에 사용자 지정 DNS 이름 추가
- 작업 3: Global 관리istrator 역할을 사용하여 Microsoft Entra ID 사용자 만들기

#### 작업 1: Microsoft Entra 테넌트 만들기

이 작업에서는 이 랩에서 사용할 새 Microsoft Entra 테넌트를 만듭니다. 

1. Azure Portal의 Azure Portal 페이지 위쪽에 있는 **리소스, 서비스 및 문서** 검색 텍스트 상자에 Microsoft Entra ID**를 입력**하고 Enter** 키를 누릅니**다.

2. 현재 Microsoft Entra 테넌트 개요를 **표시하는 블레이드에서 테넌트** 관리를 클릭한 **다음 다음 화면에서 + 만들기**를 클릭합니다****.

3. **테넌트** 만들기 블레이드의 **기본 사항** 탭에서 Microsoft Entra ID** 옵션이 **선택되어 있는지 확인하고 다음: 구성 >** 클릭합니다**.

4. **디렉터리 만들기** 블레이드의 **구성** 탭에서 다음 설정을 구성합니다.

   |설정|값|
   |---|---|
   |조직 이름|**AdatumSync**|
   |초기 도메인 이름|문자와 숫자의 조합으로 구성된 고유한 이름|
   |국가 또는 지역|**미국**|

    >**참고**: 초기 도메인 이름을 기록합니다. 이 랩의 후반부에서 필요합니다.

    >**참고**: **초기 도메인 이름** 텍스트 상자의 녹색 확인 표시는 입력한 도메인 이름이 유효하고 고유한지 여부를 나타냅니다. (나중에 사용하기 위해 초기 도메인 이름 기록).

5. **검토 + 만들기**를 클릭한 다음, **만들기**를 클릭합니다.

    >**참고**: 새 테넌트가 만들어질 때까지 기다립니다. **알림** 아이콘을 사용하여 배포 상태를 모니터링합니다. 

#### 작업 2: 새 Azure AD 테넌트에 사용자 지정 DNS 이름 추가

이 작업에서는 새 Azure AD 테넌트에 사용자 지정 DNS 이름을 추가합니다. 

1. Azure Portal의 도구 모음에서 Cloud Shell 셸 아이콘 오른쪽에 있는 **디렉터리 + 구독** 아이콘을 클릭합니다. 

2. **디렉터리 + 구독** 블레이드에서 새로 만든 테넌트인 **AdatumSync** 줄을 선택하고 **전환** 단추를 클릭합니다.

    >**참고**: **AdatumSync** 항목이 **디렉터리 + 구독** 필터 목록에 나타나지 않으면 브라우저 창을 새로 고침합니다.

3. **AdatumSync \| Microsoft Entra ID** 블레이드의 **관리** 섹션에서 사용자 지정 do기본 이름을 클릭합니다.****

4. **AdatumSync \| 사용자 지정 도메인 이름** 블레이드에서 **+ 사용자 지정 도메인 추가**를 클릭합니다.

5. **사용자 지정 도메인 이름** 블레이드에서 **사용자 지정 도메인 이름** 텍스트 상자에서 **adatum.com**을 입력하고 **도메인 추가**를 클릭합니다.

6. **adatum.com** 블레이드에서 Microsoft Entra do기본 이름 확인을 수행하는 데 필요한 정보를 검토한 다음 삭제**를 두 번 선택합니다**. 

    >**참고**: **adatum.com** DNS 도메인 이름이 없으므로 유효성 검사 프로세스를 완료할 수 없습니다. 이렇게 하면 Microsoft Entra Do기본 Services에서 수행하는 adatum.com** Microsoft Entra 테넌트 기본 동기화**할 수 없습니다. 이 용도로 이전 작업에서 식별한 Microsoft Entra 테넌트의 초기 DNS 이름(onmicrosoft.com** 접미사로 **끝나는 이름)을 사용합니다. 그러나 결과적으로 DNS는 Microsoft Entra Do기본 Services의 기본 이름이고기본 Microsoft Entra 테넌트 DNS 이름은 다릅니다. 즉, Adatum 사용자는 Microsoft Entra Do기본 Services에 로그인할 때기본 Microsoft Entra 테넌트에 로그인할 때 다른 이름을 사용해야 합니다.

#### 작업 3: Global 관리istrator 역할을 사용하여 Microsoft Entra ID 사용자 만들기

이 작업에서는 새 Microsoft Entra ID 사용자를 추가하고 전역 관리istrator 역할에 할당합니다. 

1. **AdatumSync** Microsoft Entra 테넌트 블레이드의 **관리** 섹션에서 사용자를** 클릭합니다**.

2. **사용자 | 모든 사용자** 블레이드에서 + 새 사용자를** 클릭한 다음 **새 사용자** 만들기를 클릭합니다**.

3. 새 사용자 블레이드에서 **사용자** 만들기 옵션이 선택되어 있는지 확인하고 **기본 탭에서 다음 설정을 지정하고(다른 모든 설정을 기본값으로 유지) 다음을 클릭합니다 **. 속성 >**.**

   |설정|값|
   |---|---|
   |사용자 이름|**syncadmin**|
   |이름|**syncadmin**|
   |암호|**암호 자동 생성** 옵션이 선택되어 있는지 확인하고 **암호 표시**를 클릭합니다.|

    >**참고**: 전체 사용자 이름을 기록합니다. 드롭다운 목록의 오른쪽에 있는 클립보드**로 복사 단추를 클릭하여 **할 일기본 이름을 표시하고 메모장 문서에 붙여넣으면 해당 값을 복사할 수 있습니다. 이 랩에서 나중에 필요합니다.

    >**참고**: 암호 텍스트 상자의 오른쪽에 있는 클립보드**로 복사 단추를 클릭하고 **메모장 문서에 붙여넣어 사용자의 암호를 기록합니다. 이 랩에서 나중에 필요합니다.

4. 속성** 탭에서 **아래쪽으로 스크롤하여 사용 위치를 **지정합니다. 미국**(다른 모든 항목은 기본값으로 유지)하고 다음: 할당 >** 클릭합니다**.

5. 할당** 탭에서 **+ 역할** 추가를 클릭하고 **전역 관리istrator**를 검색하여 선택한 **다음 선택을** 클릭합니다**. **검토 + 만들기**를 클릭한 다음, **만들기**를 클릭합니다.
   
    >**참고**: Microsoft Entra 커넥트 구현하려면 Global 관리istrator 역할을 가진 Azure AD 사용자가 필요합니다.

6. InPrivate 브라우저 창을 엽니다.

7. Azure Portal로 **`https://portal.azure.com/`** 이동하여 syncadmin** 사용자 계정을 사용하여 **로그인합니다. 메시지가 표시되면 이 작업의 앞부분에서 기록한 암호를 복잡성 요구 사항을 충족하는 사용자 고유의 암호로 변경하고 나중에 참조할 수 있도록 기록합니다. 이후 작업에서 이 암호를 입력하라는 메시지가 표시됩니다.

    >**참고**: 로그인하려면 이 작업의 앞부분에서 기록한 Microsoft Entra 테넌트 DNS do기본 이름을 포함하여 syncadmin** 사용자 계정의 **정규화된 이름을 제공해야 합니다. 이 사용자 이름은 고유한 Microsoft Entra 테넌트 이름을 나타내는 자리 표시자인 syncadmin@`<your_tenant_name>`.onmicrosoft.com `<your_tenant_name>` 형식입니다. 

8. **syncadmin**으로 로그아웃하고 InPrivate 브라우저 창을 닫습니다.

> **결과**: 이 연습을 완료한 후 AMicrosoft Entra 테넌트를 만들고, 새 Microsoft Entra 테넌트에 사용자 지정 DNS 이름을 추가하는 방법을 알아보고, Global 관리istrator 역할을 사용하여 Azure AD 사용자를 만들었습니다.


### 연습 3: Microsoft Entra ID 포리스트를 Microsoft Entra 테넌트와 동기화

### 예상 소요 시간: 20분

이 연습에서는 다음 작업을 완료합니다.

- 작업 1: 디렉터리 동기화를 위해 Microsoft Entra Do기본 서비스 준비
- 작업 2: Microsoft Entra 커넥트 설치
- 작업 3: 디렉터리 동기화 확인

#### 작업 1: 디렉터리 동기화를 위해 Microsoft Entra Do기본 서비스 준비

이 작업에서는 Microsoft Entra Do기본 Services do기본 컨트롤러를 실행하는 Azure VM에 연결하고 디렉터리 동기화 계정을 만듭니다. 

   > 이 작업을 시작하기 전에 이 랩의 첫 번째 연습에서 시작한 템플릿 배포가 완료되었는지 확인합니다.

1. Azure Portal에서 이 랩의 **첫 번째 연습에서 Azure VM을 배포한 Azure 구독과 연결된 Microsoft Entra 테넌트로 디렉터리 + 구독** 필터를 설정합니다.

2. Azure Portal 페이지 위쪽의 **리소스, 서비스 및 문서 검색** 텍스트 상자에 **가상 머신**을 입력하고 **Enter** 키를 누릅니다.

3. **가상 머신** 블레이드에서 **adVM** 항목을 클릭합니다. 

4. **adVM** 블레이드에서 **연결**을 클릭하고 드롭다운 메뉴에서 **RDP**를 클릭합니다. 

5. **IP 주소** 드롭다운에서 부하 분산 장치 공용 IP 주소를** 선택한 **다음 RDP 파일** 다운로드를 클릭하고 **이를 사용하여 원격 데스크톱을 통해 adVM** Azure VM에 연결**합니다. 인증하라는 메시지가 표시되면 다음 자격 증명을 입력합니다.

   |설정|값|
   |---|---|
   |사용자 이름|**Student**|
   |암호|**랩 04 > 연습 1 > 작업 1 > 9단계에서 만든 개인 암호를 사용하세요.**|

    >**참고**: 원격 데스크톱 세션과 **서버 관리자**가 로드될 때까지 기다립니다.  

    >**참고**: 다음 단계는 **adVM** Azure VM에 대한 원격 데스크톱 세션에서 수행됩니다.

    >**참고**: RDP 블레이드의 IP 주소 드롭다운에서 **부하 분산 장치 공용 IP 주소를**** 사용할 수 없는 경우 **Azure Portal에서 공용 IP 주소를** 검색**하고 adPublicIP**를 선택하고 **해당 IP 주소를 기록해 둡니다. 시작 단추를 클릭하고 MSTSC를 입력**한 다음 Enter 키를** 눌러 **원격 데스크톱 클라이언트를 시작**합니다. 컴퓨터:** 텍스트 상자에 부하 분산 장치의 공용 IP 주소를 **입력하고 커넥트** 클릭합니다**.

6. 서버 관리자 도구를 클릭하고 드롭다운 메뉴에서 Microsoft Entra ID 관리istrative Center**를 클릭합니다**.********

7. **Microsoft Entra ID 관리istrative Center**에서 adatum(로컬)****을 클릭하고 **작업** 창의 do기본 이름 **adatum(로컬)** 에서 새로** 만들기를 클릭하고 **계단식 메뉴에서 조직 구성 단위**를 클릭합니다**.

8. **조직 구성 단위 만들기** 창의 **이름** 텍스트 상자에 **ToSync**를 입력하고 **확인**을 클릭합니다.

9. Microsoft Entra ID 관리Istrative Center 콘솔의 세부 정보 창에 콘텐츠가 표시되도록 새로 만든 **ToSync** 조직 구성 단위를 두 번 클릭합니다. 

10. **작업** 창의 **ToSync** 섹션 내에서 **신규**를 클릭하고 계단식 메뉴에서 **사용자**를 클릭합니다.

11. **사용자 만들기** 창에서 다음 설정이 있는 새 사용자 계정을 만들고(다른 설정은 기존값으로 남겨둡니다) **확인**을 클릭합니다.
    
    |설정|값|
    |---|---|
    |전체 이름|**aduser1**|
    |사용자 UPN 로그온|**aduser1**|
    |사용자 SamAccountName 로그온|**aduser1**|
    |암호 및 암호 확인|**랩 04 > 연습 1 > 작업 1 > 9단계에서 만든 개인 암호를 사용하세요.**|
    |다른 암호 옵션|**암호 사용 기간 제한 없음**|


#### 작업 2: Microsoft Entra 커넥트 설치

이 작업에서는 가상 머신에 Microsoft Entra 커넥트 설치합니다. 

1. **adVM**에 대한 원격 데스크톱 세션에서 Microsoft Edge를 사용하여 **https://portal.azure.com** 의 Azure Portal로 이동하고, 이전 연습에서 만든 **syncadmin** 사용자 계정을 사용하여 로그인합니다. 메시지가 표시되면 이전 연습에서 기록한 전체 사용자 계정 이름 및 암호를 지정합니다.

2. Azure Portal의 Azure Portal 페이지 위쪽에 있는 **리소스, 서비스 및 문서** 검색 텍스트 상자에 Microsoft Entra ID**를 입력**하고 Enter** 키를 누릅니**다.

3. Azure Portal**의 AdatumSync \| 개요** 블레이드의 관리** 아래 **왼쪽 탐색 패널에서 Microsoft Entra 커넥트** 클릭합니다**.

4. **Microsoft Entra 커넥트 \| 시작** 블레이드**의 왼쪽 탐색 패널에서 커넥트 동기화**를 클릭한 다음 Microsoft Entra 커넥트** 다운로드 링크를 클릭합니다**. Microsoft Entra 커넥트** 다운로드 페이지로 **리디렉션됩니다.

5. **Microsoft Entra 커넥트** 다운로드 페이지에서 다운로드**를 클릭합니다**.

6. 메시지가 표시되면 실행을** 클릭하여 **Microsoft Entra 커넥트** 마법사를 시작**합니다.

7. **Microsoft Entra 커넥트 마법사의 **Microsoft Entra 커넥트**** 시작 페이지에서 사용 조건 및 개인 정보 보호 알림**에 동의하는 검사 상자를 **클릭하고 계속**을 클릭합니다**.

8. **Microsoft Entra 커넥트** 마법사의 **Express 설정** 페이지에서 사용자 지정** 옵션을 클릭합니다**.

9. **필수 구성 요소 설치** 페이지에서 모든 선택 구성 옵션의 선택을 취소하고 **설치**를 클릭합니다.

10. **사용자 로그인** 페이지에서 **암호 해시 동기화**만 사용하도록 설정되어 있는지 확인하고 **다음**을 클릭합니다.

11. **Microsoft Entra ID**로 커넥트 페이지에서 이전 연습에서 만든 syncadmin** 사용자 계정의 **자격 증명을 사용하여 인증하고 다음**을 클릭합니다**. 

12. **디렉터리 연결** 페이지에서 **adatum.com** 포리스트 항목 오른쪽에 있는 **디렉터리 추가** 단추를 클릭합니다.

13. **AD 포리스트 계정** 창에서 새 Microsoft Entra ID 계정을** 만드는 옵션이 **선택되어 있는지 확인하고 다음 자격 증명을 지정한 다음 확인을** 클릭합니다**.

    |설정|값|
    |---|---|
    |사용자 이름|**ADATUM\\Student**|
    |암호|**랩 06 > 연습 1 > 작업 2에서 만든 개인 암호를 사용하세요.**|

14. **디렉터리 연결** 페이지에서 **adatum.com** 항목이 구성된 디렉터리로 나타나는지 확인하고 **다음**을 클릭합니다.

15. **Microsoft Entra ID 로그인 구성** 페이지에서 UPN 접미사가 확인된 do기본 이름과** 일치하지 않는 경우 사용자가 온-프레미스 자격 증명을 사용하여 Microsoft Entra ID에 로그인할 수 없다는 경고**에 유의하고, 확인될 모든 UPN 접미사를 일치시키지 않고 검사box **Continue를 사용하도록 설정하고기본** 다음**을 클릭합니다**.

    >**참고**: 앞에서 설명한 대로 사용자 지정 Microsoft Entra ID DNS**를 확인할 수 없으므로 기본 adatum.com**.

16. **Do기본 및 OU 필터링** 페이지에서 선택한 do기본 및 OU** 옵션을 **클릭하고 do기본 이름 **adatum.com 옆에 있는 검사** 상자를 선택 취소합니다. adatum.com 확장하고 **ToSync** OU 옆에 **있는 검사 상자만 선택한 다음 다음을 클릭합니다****.**

17. **사용자 고유하게 식별** 페이지에서 기본 설정을 수락하고 **다음**을 클릭합니다.

18. **사용자 및 디바이스 필터링** 페이지에서 기본 설정을 수락하고 **다음**을 클릭합니다.

19. **선택적 기능** 페이지에서 기본 설정을 수락하고 **다음**을 클릭합니다.

20. **구성 준비 완료** 페이지에서 **구성이 완료되면 동기화 프로세스를 시작** 체크박스가 선택되었는지 확인하고 **설치**를 클릭합니다.

    >**참고**: 설치에는 약 2분이 소요됩니다.

21. 구성 완료 페이지에서 정보를 **검토하고 끝내기**를 클릭하여 **Microsoft Entra 커넥트** 창을 닫습니다**.**


#### 작업 3: 디렉터리 동기화 확인

이 작업에서는 디렉터리 동기화가 작동하는지 확인합니다. 

1. AdVM에 대한 **원격 데스크톱 세션 내에서 Azure Portal을 표시하는 Microsoft Edge 창에서 Adatum Lab AMicrosoft Entra ID 테넌트의 사용자 - 모든 사용자(미리 보기)** 블레이드로 이동합니다**.**

2. **사용자 \| 모든 사용자** 블레이드에서 사용자 개체 목록에 **aduser1** 계정이 포함되어 있음을 확인합니다. 

   >**참고**: 몇 분 동안 기다렸다가 **aduser1** 사용자 계정이 표시되도록 **새로 고침**을 선택해야 할 수 있습니다.

3. **aduser1** 계정을 클릭하고 속성** 탭을 **선택합니다. 온-프레미스 섹션**까지 **아래로 스크롤합니다. 온-프레미스** 동기화 사용** 특성이 Yes**로 **설정되어 있습니다.

4. **aduser1** 블레이드의 **작업 정보** 섹션에서 **부서** 특성이 설정되지 않았습니다.

5. adVM에 대한 **원격 데스크톱 세션 내에서 Microsoft Entra ID 관리istrative Center**로 전환**하고 ToSync** OU의 개체 **목록에서 aduser1** 항목을 선택하고 **작업 **** 창**의 aduser1** 섹션에서 속성을** 선택합니다**.**

6. **조직** 섹션의 **aduser1** 창에서 **부서** 입력란에 **영업**을 입력하고 **확인**을 선택합니다.

7. **adVM**에 대한 원격 데스크톱 세션 내에서 **Windows PowerShell**을 시작합니다.

8. **관리istrator: Windows PowerShell** 콘솔에서 다음을 실행하여 델타 동기화를 커넥트 Microsoft Entra를 시작합니다.

    ```powershell
    Import-Module -Name 'C:\Program Files\Microsoft Azure AD Sync\Bin\ADSync\ADSync.psd1'

    Start-ADSyncSyncCycle -PolicyType Delta
    ```

9. aduser1** 블레이드를 **표시하는 Microsoft Edge 창으로 전환하고 페이지를 새로 고치고 부서 속성이 Sales로 설정되어 있는지 확인합니다.

    >**참고**: 부서** 특성이 다시 설정되지 않은 경우 **최대 3분 동안 기다렸다가 페이지를 다시 새로 고쳐야 할 기본 있습니다.

> **결과**: 이 연습을 완료한 후 디렉터리 동기화를 위해 Microsoft Entra Do기본 Services를 준비하고, Microsoft Entra 커넥트 설치하고, 디렉터리 동기화를 확인했습니다.


**리소스 정리**

>**참고**: Microsoft Entra ID 동기화를 사용하지 않도록 설정하여 시작

1. **adVM**에 대한 원격 데스크톱 세션 내에서 관리자로 Windows PowerShell을 시작합니다.

2. Windows PowerShell 콘솔에서 다음을 실행하여 MsOnline PowerShell 모듈을 설치합니다(메시지가 나타날 때 NuGet 공급자에서 대화 상자를 계속하려면 **예**를 입력한 뒤 Enter 키를 누릅니다):

    ```powershell
    [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
    Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
    Install-Module MsOnline -Force
    ```

3. Windows PowerShell 콘솔에서 다음을 실행하여 AdatumSync Microsoft Entra 테넌트에 연결합니다(메시지가 표시되면 syncadmin** 자격 증명으로 **로그인).

    ```powershell
    Connect-MsolService
    ```

4. Windows PowerShell 콘솔에서 다음을 실행하여 Microsoft Entra 커넥트 동기화를 사용하지 않도록 설정합니다.

    ```powershell
    Set-MsolDirSyncEnabled -EnableDirSync $false -Force
    ```

5. Windows PowerShell 콘솔에서 다음을 실행하여 동작이 성공했는지 확인합니다.

    ```powershell
    (Get-MSOLCompanyInformation).DirectorySynchronizationEnabled
    ```
    >**참고**: 결과는 `False`여야 합니다. 그렇지 않은 경우 1분 정도 기다렸다가 명령을 다시 실행합니다.

    >**참고**: 다음으로 Azure 리소스를 제거
6. 원격 데스크톱 세션을 닫습니다.

7. Azure Portal에서 디렉터리 + 구독** 필터를 adVM Azure VM**을 배포한 **Azure 구독과 연결된 Microsoft Entra 테넌트로 설정합니다**.

8. Azure Portal 오른쪽 위의 첫 번째 아이콘을 클릭하여 Cloud Shell을 엽니다. 

9. Cloud Shell 창의 왼쪽 위 모서리에 있는 드롭다운 메뉴에서 **PowerShell**을 선택하고 메시지가 표시되면 **확인**을 클릭합니다. 

10. Cloud Shell 창 내의 PowerShell 세션에서 다음을 실행하여 이 랩에서 만든 리소스 그룹을 제거합니다.
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB06" -Force -AsJob
    ```
11. **Cloud Shell** 창을 닫습니다.

    >**참고**: 마지막으로 Microsoft Entra 테넌트 제거
    
    >**참고 2**: 테넌트 삭제는 매우 어려운 프로세스이기 때문에 실수로 또는 악의적으로 수행해서는 안 됩니다.  따라서 이 랩의 일부로 테넌트를 제거하는 일은 효과적이지 않을 수 있습니다.  여기에서 테넌트 삭제 단계를 진행하면서 이 랩을 완료할 필요는 없습니다. 실제 상황에서 테넌트를 제거해야 하는 경우 DOCS.Microsoft.com에 있는 문서를 참조하면 도움이 됩니다.

12. Azure Portal로 돌아가서 디렉터리 + 구독** 필터를 사용하여 **AdatumSync** Microsoft Entra 테넌트로 전환**합니다.

13. Azure Portal에서 **사용자 - 모든 사용자** 블레이드로 이동하여 **syncadmin** 사용자 계정을 나타내는 항목을 클릭합니다. **syncadmin - 프로필** 블레이드에서 **삭제**를 클릭하고 확인하라는 메시지가 표시되면 **예**를 클릭합니다.

14. 동일한 순서로 단계를 반복하여 **aduser1** 사용자 계정과 **온-프레미스 디렉터리 동기화 서비스 계정**을 삭제합니다.

15. **AdatumSync - Microsoft Entra 테넌트의 개요** 블레이드로 이동하고, 테넌트** 관리를 클릭하고**, AdatumSync** 디렉터리의 검사 상자를 **선택하고, 삭제**를 클릭하고**, **테넌트 'AdatumSync'** 삭제 블레이드에서 Azure 리소스 삭제 권한 가져오기 링크를 클릭하고 **, AMicrosoft Entra의 속성** 블레이드에서 Azure 리소스****에 **대한 액세스 관리를 예**로 **설정하고**, 저장을 클릭합니다 **.**.

    >**참고**: 삭제할 때 **모든 사용자 삭제**와 같은 경고 기호가 표시되는 경우 계속 진행하여 만든 모든 사용자를 삭제하거나, 경고 기호에 **LinkedIn 애플리케이션 삭제**가 표시되는 경우 경고 메시지를 클릭하고 LinkedIn 애플리케이션의 삭제를 확인합니다. 테넌트 삭제를 전달하려면 모든 경고를 처리해야 합니다.

16. Azure Portal에서 로그아웃하고 다시 로그인합니다. 

17. **'AdatumSync' 테넌트 삭제** 블레이드로 다시 이동하여 **삭제**를 클릭합니다.

> 이 작업과 관련된 자세한 내용은 [https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/directory-delete-howto](https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/directory-delete-howto)를 참조하세요.
