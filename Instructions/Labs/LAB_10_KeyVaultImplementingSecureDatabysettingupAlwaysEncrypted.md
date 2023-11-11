---
lab:
  title: 10 - Key Vault(Always Encrypted를 설정하여 보안 데이터 구현)
  module: Module 03 - Secure Data and Applications
---

# 랩 10: Key Vault(Always Encrypted를 설정하여 보안 데이터 구현)
# Student용 랩 매뉴얼

## 랩 시나리오

Always Encrypted 기능에 대한 Azure SQL Database 지원을 사용하는 개념 증명 애플리케이션을 만들어야 합니다. 이 시나리오에 사용된 모든 비밀과 키는 Key Vault에 저장해야 합니다. 애플리케이션은 보안 태세를 향상시키기 위해 Azure Active Directory(Azure AD)에 등록해야 합니다. 이러한 목표를 달성하기 위해 개념 증명에는 다음이 포함되어야 합니다.

- Azure Key Vault를 만들어 자격 증명 모음에 키 및 비밀을 저장합니다.
- Always Encrypted를 사용하여 SQL Database를 만들고 데이터베이스 테이블의 열 콘텐츠를 암호화합니다.

>**참고**: 이 랩의 모든 리소스에 대해 **미국 동부** 지역을 사용하고 있습니다. 이 지역을 수업에 사용할 것인지 강사에게 확인합니다. 

이 개념 증명 빌드와 관련된 Azure의 보안 측면에 집중하려면 자동화된 ARM 템플릿 배포에서 시작하여 Visual Studio 2019 및 SQL Server Management Studio 19를 사용하여 Virtual Machine을 설정합니다.

## 랩 목표

이 랩에서는 다음과 같은 연습을 완료합니다:

- 연습 1: ARM 템플릿에서 기본 인프라 배포
- 연습 2: 키와 비밀을 사용하여 Key Vault 리소스 구성
- 연습 3: Azure SQL 데이터베이스 및 데이터 기반 애플리케이션 구성
- 연습 4: Azure SQL 데이터베이스 암호화 과정에서 Azure Key Vault를 사용하는 방법 시연

## Key Vault 다이어그램

![이미지](https://user-images.githubusercontent.com/91347931/157532938-c724cc40-f64f-4d69-9e91-d75344c5e0a2.png)

## Instructions

## 랩 파일:

- **\\Allfiles\\Labs\\10\\az-500-10_azuredeploy.json**

- **\\Allfiles\\Labs\\10\\program.cs**

### 총 랩 소요 시간(예상): 60분

### 연습 1: ARM 템플릿에서 기본 인프라 배포

이 연습에서는 다음 작업을 완료합니다.

- 작업 1: Azure VM 및 Azure SQL 데이터베이스 배포

#### 작업 1: Azure VM 및 Azure SQL 데이터베이스 배포

이 작업에서는 배포의 일부로 Visual Studio 2019 및 SQL Server Management Studio 19를 자동으로 설치하는 Azure VM을 배포합니다. 

1. Azure portal **`https://portal.azure.com/`** 에 로그인합니다.

    >**참고**: 이 랩에 사용 중인 Azure 구독에 Owner 또는 Contributor 역할이 있는 계정을 사용하여 Azure Portal에 로그인합니다.

2. Azure Portal에서 Azure Portal 페이지 위쪽의 **리소스, 서비스 및 문서 검색** 텍스트 상자에 **사용자 지정 템플릿 배포**를 입력하고 **Enter** 키를 누릅니다.

3. **사용자 지정 배포** 블레이드에서 **편집기에서 사용자 고유의 탬플릿 빌드** 옵션을 클릭합니다.

4. **템플릿 편집** 블레이드에서 **파일 로드**를 클릭하고 **\\Allfiles\\Labs\\10\\az-500-10_azuredeploy.json** 파일을 찾아 **열기**를 클릭합니다.

5. **템플릿 편집** 블레이드에서 **저장**을 클릭합니다.

6. **사용자 지정 배포** 블레이드의 **배포 범위**에서 다음 설정이 구성되어 있는지 확인합니다. 다른 설정은 모두 기본값으로 유지합니다.

   |설정|값|
   |---|---|
   |구독|이 랩에서 사용할 Azure 구독의 이름|
   |Resource group|**새로 만들기**를 클릭하고 **AZ500LAB10**을 이름으로 입력합니다.|
   |위치|**미국 동부**|
   |관리자 사용자 이름|**학생**|
   |관리자 암호|**랩 04 > 연습 1 > 작업 1 > 9단계에서 만든 개인 암호를 사용하세요.**|
   
    >**참고**: 가상 머신에 로그온하는 데 사용하는 관리 자격 증명을 변경할 수는 있지만 반드시 변경할 필요는 없습니다.

    >**참고**: Azure VM을 프로비전할 수 있는 Azure 지역을 식별하려면 [ **https://azure.microsoft.com/en-us/regions/offers/** ](https://azure.microsoft.com/en-us/regions/offers/)를 참조하세요.

7. **검토 및 만들기** 단추를 클릭한 후에 **만들기** 단추를 클릭하여 배포를 확인합니다. 

    >**참고**: 이렇게 하면 이 랩에 필요한 Azure VM 및 Azure SQL 데이터베이스 배포가 시작됩니다. 

    >**참고**: ARM 템플릿 배포가 완료될 때까지 기다리지 말고 다음 연습을 진행하세요. 배포는 **20~25분** 정도 걸릴 수 있습니다. 

### 연습 2: 키와 비밀을 사용하여 Key Vault 리소스 구성

>**참고**: 이 랩의 모든 리소스에 대해 **미국 동부** 지역을 사용하고 있습니다. 강사에게 이 지역을 수업에서 사용하는지 확인합니다. 

이 연습에서는 다음 작업을 완료합니다.

- 작업 1: Key Vault 만들기 및 구성
- 작업 2: Key Vault에 키 추가
- 작업 3: Key Vault에 암호 추가

#### 작업 1: Key Vault 만들기 및 구성

이 작업에서는 Azure Key Vault 리소스를 만듭니다. 그리고 Azure Key Vault 권한도 구성합니다.

1. Azure Portal 오른쪽 위의 검색 창 옆에 있는 첫 번째 아이콘을 클릭하여 Cloud Shell을 엽니다. 메시지가 표시되면 **PowerShell** 및 **스토리지 만들기**를 선택합니다.

2. Cloud Shell 창의 왼쪽 위 모서리에 있는 드롭다운 메뉴에서 **PowerShell**이 선택되었는지 확인합니다.

3. Cloud Shell 창 내의 PowerShell 세션에서 다음 명령을 실행하여 리소스 그룹 **AZ500LAB10**에 Azure Key Vault를 만듭니다. 작업 1에서 이 랩의 리소스 그룹에 다른 이름을 선택했다면 이 작업에서도 해당 이름을 사용하세요. Key Vault 이름은 고유해야 합니다. 선택한 이름을 기억하세요. 이 랩에서 필요합니다.  

    ```powershell
    $kvName = 'az500kv' + $(Get-Random)

    $location = (Get-AzResourceGroup -ResourceGroupName 'AZ500LAB10').Location

    New-AzKeyVault -VaultName $kvName -ResourceGroupName 'AZ500LAB10' -Location $location
    ```

    >**참고**: 마지막으로 실행된 명령의 출력에 자격 증명 모음 이름과 URI가 표시됩니다. 자격 증명 모음 URI는 `https://<vault_name>.vault.azure.net/` 형식입니다.

4. Cloud Shell 창을 닫습니다. 

5. Azure Portal에서 Azure Portal 페이지 위쪽의 **리소스, 서비스 및 문서 검색** 텍스트 상자에 **리소스 그룹**을 입력하고 **Enter** 키를 누릅니다.

6. **리소스 그룹** 블레이드의 리소스 그룹 목록에서 **AZ500LAB10** 항목이나 이전 작업에서 리소스 그룹용으로 선택한 다른 이름을 클릭합니다.

7. 리소스 그룹 블레이드에서 새로 만든 Key Vault를 나타내는 항목을 클릭합니다. 

8. Key Vault 블레이드의 **개요** 섹션에서 **액세스 정책**을 클릭한 다음, **+ 만들기**를 클릭합니다.

9. **액세스 정책 만들기** 블레이드에서 다음 설정을 지정합니다(다른 모든 설정을 기본값으로 남겨둠). 

    |설정|값|
    |----|----|
    |템플릿에서 구성(선택 사항)|**키, 비밀 및 인증서 관리**|
    |키 권한|선택한 총 **9**개의 사용 권한의 결과 모두** 선택 클릭 **|
    |키 권한/암호화 작업|선택한 총 **1**개의 사용 권한이 있는 서명** 클릭 **|
    |비밀 권한|**모두 선택**을 클릭하면 총 **7개의 선택된** 권한이 나타납니다.|
    |인증 권한|**모두 선택**을 클릭하면 총 **15개의 선택된** 권한이 나타납니다.|
    |보안 주체 선택|**선택된 항목 없음**을 클릭하고, **보안 주체** 블레이드에서 사용자 계정을 선택하고 **다음**을 클릭합니다.|
    |애플리케이션(선택 사항)|**다음**을 누릅니다|
    |검토 + 만들기|**만들기**를 클릭합니다.|
    
    >**참고**: 이전 검토 + 만들기 작업은 애플리케이션, 메일, 키 권한, 비밀 권한, 인증서 권한을 나열하는 액세스 정책 페이지로 돌아갑니다.
      
#### 작업 2: Key Vault에 키 추가

이 작업에서는 Key Vault에 키를 추가하고 키에 대한 정보를 확인합니다. 

1. Azure Portal의 Cloud Shell 창에서 PowerShell 세션을 엽니다.

2. Cloud Shell 창의 왼쪽 위 드롭다운 메뉴에서 **PowerShell**이 선택되어 있는지 확인합니다.

3. Cloud Shell 창 내의 PowerShell 세션에서 다음을 실행하여 Key Vault에 소프트웨어로 보호된 키를 추가합니다. 

    ```powershell
    $kv = Get-AzKeyVault -ResourceGroupName 'AZ500LAB10'

    $key = Add-AZKeyVaultKey -VaultName $kv.VaultName -Name 'MyLabKey' -Destination 'Software'
    ```

    >**참고**: 키 이름은 **MyLabKey**입니다.

4. Cloud Shell 창 내의 PowerShell 세션에서 다음을 실행하여 키가 만들어졌는지 확인합니다.

    ```powershell
    Get-AZKeyVaultKey -VaultName $kv.VaultName
    ```

5. Cloud Shell 창 내의 PowerShell 세션에서 다음을 실행하여 키 식별자를 표시합니다.

    ```powershell
    $key.key.kid
    ```

6. Cloud Shell 창을 최소화합니다. 

7. Azure Portal로 돌아와서 Key Vault 블레이드의 **개체** 섹션에서 **키**를 클릭합니다.

8. 키 목록에서 **MyLabKey** 항목을 클릭한 다음 **MyLabKey** 블레이드에서 키의 현재 버전을 나타내는 항목을 클릭합니다.

    >**참고**: 만든 키와 관련된 정보를 점검합니다.

    >**참고**: 키 식별자를 사용하여 모든 키를 참조할 수 있습니다. 최신 버전을 얻으려면 `https://<key_vault_name>.vault.azure.net/keys/MyLabKey`를 참조하거나 `https://<key_vault_name>.vault.azure.net/keys/MyLabKey/<key_version>`을 사용하여 특정 버전을 가져옵니다.


#### 작업 3: Key Vault에 비밀 추가

1. Cloud Shell 창으로 다시 전환합니다.

2. Cloud Shell 창 내의 PowerShell 세션에서 다음을 실행하여 보안 문자열 값이 있는 변수를 만듭니다.

    ```powershell
    $secretvalue = ConvertTo-SecureString 'Pa55w.rd1234' -AsPlainText -Force
    ```

3.  Cloud Shell 창 내의 PowerShell 세션에서 다음을 실행하여 자격 증명 모음에 암호를 추가합니다.

    ```powershell
    $secret = Set-AZKeyVaultSecret -VaultName $kv.VaultName -Name 'SQLPassword' -SecretValue $secretvalue
    ```

    >**참고**: 암호의 이름은 SQLPassword입니다. 

4.  Cloud Shell 창 내의 PowerShell 세션에서 다음을 실행하여 비밀이 만들어졌는지 확인합니다.

    ```powershell
    Get-AZKeyVaultSecret -VaultName $kv.VaultName
    ```

5. Cloud Shell 창을 최소화합니다. 

6. Azure Portal에서 Key Vault 블레이드로 다시 이동하여 **개체** 섹션에서 **비밀**을 클릭합니다.

7. 비밀 목록에서 **SQLPassword** 항목을 클릭한 다음 **SQLPassword** 블레이드에서 현재 버전의 비밀을 나타내는 항목을 클릭합니다.

    >**참고**: 만든 비밀과 관련된 정보를 점검합니다.

    >**참고**: 최신 버전의 비밀을 얻으려면 `https://<key_vault_name>.vault.azure.net/secrets/<secret_name>`을 참조하고, 특정 버전을 얻으려면 `https://<key_vault_name>.vault.azure.net/secrets/<secret_name>/<secret_version>`을 참조합니다.


### 연습 3: Azure SQL 데이터베이스 및 데이터 기반 애플리케이션 구성

이 연습에서는 다음 작업을 완료합니다.

- 작업 1: 클라이언트 애플리케이션이 Azure SQL Database 서비스에 액세스할 수 있도록 설정
- 작업 2: 애플리케이션의 Key Vault 액세스를 허용하는 정책 만들기
- 작업 3: SQL Azure 데이터베이스 ADO.NET 연결 문자열 검색 
- 작업 4: Visual Studio 2019 및 SQL Management Studio 19를 실행하는 Azure VM에 로그온
- 작업 5: SQL Database에서 테이블을 만들고 암호화를 위한 데이터 열을 선택합니다.


#### 작업 1: 클라이언트 애플리케이션이 Azure SQL Database 서비스에 액세스할 수 있도록 설정 

이 작업에서는 클라이언트 애플리케이션이 Azure SQL Database 서비스에 액세스할 수 있도록 설정합니다. 이렇게 하려면 필요한 인증을 설정하고, 애플리케이션을 인증하는 데 필요한 애플리케이션 ID 및 비밀을 가져옵니다.

1. Azure Portal에서 Azure Portal 페이지 위쪽의 **리소스, 서비스 및 문서 검색** 텍스트 상자에서 **앱 등록**을 입력하고 **Enter** 키를 누릅니다.

2. **앱 등록** 블레이드에서 **+ 새 등록**을 클릭합니다. 

3. **애플리케이션 등록** 블레이드에서 다음 설정을 지정합니다(다른 모든 설정을 기본값으로 남겨둡니다).

    |설정|값|
    |----|----|
    |이름|**sqlApp**|
    |리디렉션 URI(선택 사항)|**웹** 및 **https://sqlapp**|

4. **애플리케이션 등록** 블레이드에서 **등록**을 클릭합니다. 

    >**참고**: 등록이 완료되면 브라우저가 자동으로 **sqlApp**블레이드로 리디렉션합니다. 

5. **sqlApp** 블레이드에서 **애플리케이션(클라이언트) ID**의 값을 식별합니다. 

    >**참고**: 이 값을 기록해 둡니다. 다음 작업에서 필요합니다.

6. **sqlApp** 블레이드의 **관리** 섹션에서 **인증서 및 비밀**을 클릭합니다.

7. **인증서 및 비밀** 블레이드의 **클라이언트 비밀** 섹션에서 **+ 새 클라이언트 암호**를 클릭합니다.

8. **클라이언트 암호 추가** 창에서 다음 설정을 지정합니다.

    |설정|값|
    |----|----|
    |설명|**Key1**|
    |만료|**12개월**|
    
9. 애플리케이션 자격 증명을 업데이트하려면 **추가**를 클릭합니다.

10. **sqlApp | 인증서 및 비밀** 블레이드에서 **Key1**의 값을 식별합니다.

    >**참고**: 이 값을 기록해 둡니다. 다음 작업에서 필요합니다. 

    >**참고**: 블레이드에서 멀리 *이동*하기 전에 값을 복사해야 합니다. 이렇게 하면 키의 일반 텍스트 값을 더 이상 검색할 수 없습니다.


#### 작업 2: 애플리케이션의 Key Vault 액세스를 허용하는 정책 만들기

이 작업에서는 Key Vault에 저장된 비밀 액세스 권한을 새로 등록된 앱에 부여합니다.

1. Azure Portal의 Cloud Shell 창에서 PowerShell 세션을 엽니다.

2. Cloud Shell 창의 왼쪽 위 드롭다운 메뉴에서 **PowerShell**이 선택되어 있는지 확인합니다.

3. Cloud Shell 창 내의 PowerShell 세션에서 다음을 실행하여 이전 작업에서 기록한 **애플리케이션(클라이언트) ID**를 저장하는 변수를 만듭니다(`<Azure_AD_Application_ID>` 자리 표시자를 **애플리케이션(클라이언트) ID**의 값으로 대체).
   
    ```powershell
    $applicationId = '<Azure_AD_Application_ID>'
    ```
4. Cloud Shell 창 내의 PowerShell 세션에서 다음 명령을 실행하여 Key Vault 이름을 저장하는 변수를 만듭니다.
    ```
    $kvName = (Get-AzKeyVault -ResourceGroupName 'AZ500LAB10').VaultName

    $kvName
    ```

5. Cloud Shell 창의 PowerShell 세션에서 다음 명령을 실행하여 이전 작업에서 등록한 애플리케이션에 Key Vault 관련 권한을 부여합니다.

    ```powershell
    Set-AZKeyVaultAccessPolicy -VaultName $kvName -ResourceGroupName AZ500LAB10 -ServicePrincipalName $applicationId -PermissionsToKeys get,wrapKey,unwrapKey,sign,verify,list
    ```

6. Cloud Shell 창을 닫습니다. 


#### 작업 3: SQL Azure 데이터베이스 ADO.NET 연결 문자열 검색 

연습 1에서 ARM 템플릿을 배포할 때 Azure SQL Server 인스턴스 및 Azure SQL 데이터베이스(**medical**)가 프로비전되었습니다. 이제 새 테이블 구조를 적용하여 빈 데이터베이스 리소스를 업데이트하고 암호화용 데이터 열을 선택합니다.

1. Azure Portal의 페이지 상단에 있는 **리소스, 서비스 및 문서 검색** 텍스트 상자에서 **SQL 데이터베이스**를 입력하고 **Enter** 키를 누릅니다.

2. SQL 데이터베이스 목록에서 **medical(<randomsqlservername>)** 항목을 클릭합니다.

    >**참고**: 데이터베이스를 찾을 수 없으면 연습 1에서 시작한 배포가 아직 완료되지 않았을 가능성이 높습니다. Azure 리소스 그룹 "AZ500LAB10" 또는 선택한 이름의 그룹으로 이동한 다음 설정 창에서 **배포**를 선택하면 배포 완료 여부를 확인할 수 있습니다.  

3. SQL Database 블레이드의 **설정** 섹션에서 **연결 문자열**을 클릭합니다. 

    >**참고**: 인터페이스에는 ADO.NET, JDBC, ODBC, PHP 및 Go용 연결 문자열이 포함됩니다. 
   
4. **ADO.NET(SQL 인증)** 연결 문자열 기록합니다. 이 시간은 나중에 필요합니다.

    >**참고**: 연결 문자열을 사용하는 경우 `{your_password}` 자리 표시자를 연습 1의 배포에서 구성한 암호로 바꿔야 합니다.

#### 작업 4: Visual Studio 2019 및 SQL Management Studio 19를 실행하는 Azure VM에 로그온

이 작업에서는 연습 1에서 배포를 시작한 Azure VM에 로그온합니다. 이 Azure VM은 Visual Studio 2019 및 SQL Server Management Studio 19를 호스트합니다.

    >**Note**: Before you proceed with this task, ensure that the deployment you initiated in the first exercise has completed successfully. You can validate this by navigating to the blade of the Azure resource group "Az500Lab10" (or other name you chose) and selecting **Deployments** from the Settings pane.  

1. Azure Portal 페이지 위쪽의 **리소스, 서비스 및 문서 검색** 텍스트 상자에 **가상 머신**을 입력하고 **Enter** 키를 누릅니다.

2. 표시된 가상 머신 목록에서 **az500-10-vm1** 항목을 선택합니다. **az500-10** 블레이드의 **필수** 창에서 **공용 IP 주소**를 클릭합니다. 나중에 수행할 작업에서 이 주소를 사용합니다. 

#### 작업 5: SQL Database에서 테이블을 만들고 암호화를 위한 데이터 열을 선택합니다.

이 작업에서는 SQL Server Management Studio를 사용하여 SQL Database에 연결하고 테이블을 만듭니다. 그런 다음 Azure Key Vault에서 자동 생성된 키를 사용하여 데이터 열 두 개를 암호화합니다. 

1. Azure Portal에서 **medical** SQL 데이터베이스의 블레이드로 이동하여 **필수** 섹션에서 **서버 이름**(클립보드에 복사)을 확인한 다음 도구 모음에서 **서버 방화벽 설정**을 클릭합니다.  

    >**참고**: 서버 이름을 기록합니다. 이 작업의 후반에서 필요할 것입니다.

2. 방화벽 설정 블레이드에서 **규칙 이름으로 아래로 스크롤하고 , + 방화벽 규칙** 추가를 클릭하고**, 다음 설정을 지정**합니다. 

    |설정|값|
    |---|---|
    |규칙 이름|**관리 VM 허용**|
    |시작 IP|az500-10-vm1의 공용 IP 주소|
    |종료 IP|az500-10-vm1의 공용 IP 주소|

3. 저장**을 클릭하여 **변경 내용을 저장하고 확인 창을 닫습니다. 

    >**참고**: 이렇게 하면 서버 방화벽 설정이 수정됩니다. 그에 따라 이 랩에서 배포한 Azure VM 공용 IP 주소에서 medical 데이터베이스에 연결할 수 있게 됩니다.

4. **az500-10-vm1** 블레이드로 다시 이동하여 **개요**, **연결**을 차례로 클릭하고 드롭다운 메뉴에서 **RDP**를 클릭합니다. 

5. **RDP 파일 다운로드**를 클릭하고 원격 데스크톱을 통해 **az500-10-vm1** Azure VM에 연결하는 데 사용합니다. 인증하라는 메시지가 표시되면 다음 자격 증명을 입력합니다.

    |설정|값|
    |---|---|
    |사용자 이름|**Student**|
    |암호|**랩 04 > 연습 1 > 작업 1 > 9단계에서 만든 개인 암호를 사용하세요.**|

    >**참고**: 원격 데스크톱 세션과 **서버 관리자**가 로드될 때까지 기다립니다. 서버 관리자를 닫습니다. 

    >**참고**: 이 랩의 나머지 단계는 원격 데스크톱 세션 내에서 **az500-10-vm1** Azure VM에 대해 수행됩니다. 

6. 시작을** 클릭하고 시작 메뉴에서 Microsoft SQL Server 도구 19** 폴더를 확장하고 **Micosoft SQL Server Management Studio** 메뉴 항목을 클릭합니다**.** **** 

7. **서버에 연결** 대화 상자에서, 다음 설정을 지정합니다. 

    |설정|값|
    |---|---|
    |서버 유형|**데이터베이스 엔진**|
    |서버 이름|이 작업의 앞부분에서 식별한 서버 이름|
    |인증|**SQL Server 인증**|
    |로그인|**Student**|
    |암호|**랩 04 > 연습 1 > 작업 1 > 9단계에서 만든 개인 암호를 사용하세요.**|

8. **서버에 연결** 대화 상자에서 **연결**을 클릭합니다.

9. **SQL Server Management Studio** 콘솔 내, **개체 탐색기** 창에서 **데이터베이스** 폴더를 확장합니다.

10. **개체 탐색기** 창에서 **의료** 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **새 쿼리**를 클릭합니다.

11. 다음 코드를 쿼리 창에 붙여넣고 **실행**을 클릭합니다. 그러면 **Patients** 테이블이 만들어집니다.

     ```sql
     CREATE TABLE [dbo].[Patients](
        [PatientId] [int] IDENTITY(1,1),
        [SSN] [char](11) NOT NULL,
        [FirstName] [nvarchar](50) NULL,
        [LastName] [nvarchar](50) NULL,
        [MiddleName] [nvarchar](50) NULL,
        [StreetAddress] [nvarchar](50) NULL,
        [City] [nvarchar](50) NULL,
        [ZipCode] [char](5) NULL,
        [State] [char](2) NULL,
        [BirthDate] [date] NOT NULL 
     PRIMARY KEY CLUSTERED ([PatientId] ASC) ON [PRIMARY] );
     ```
12. 테이블이 정상적으로 작성되면 **개체 탐색기** 창에서 **medical** 데이터베이스 노드, **tables** 노드를 차례로 확장하고 **dbo.Patients** 노드를 마우스 오른쪽 단추로 클릭한 다음 **열 암호화**를 클릭합니다. 

    >**참고**: 그러면 **Always Encrypted** 마법사가 시작됩니다.

13. **소개** 페이지에서 **다음**을 클릭합니다.

14. **열 선택** 페이지에서 **SSN** 및 **Birthdate** 열을 선택하고 **SSN** 열의 **암호화 유형**은 **명확함**으로, **Birthdate** 열의 암호화 유형은 **무작위**로 설정한 후에 **다음**을 클릭합니다.

    >**참고**: 암호화를 수행하는 중에 **호출 대상에 의해 예외가 throw됨** 같이 **Rotary(Microsoft.SQLServer.Management.ServiceManagement)** 와 관련된 오류가 throw되면 **키 권한**의 **순환 정책 작업** 값이 **선택 취소**되어 있는지 확인합니다. 현재 위치가 Azure Portal이 아닌 경우 **Key Vault** >> **액세스 정책** >> **키 권한**으로 이동하고, **순환 정책 작업** 아래에서 모든 값의 선택을 취소한 다음, **권한 있는 키 작업** 아래에서 **해제**를 선택 취소합니다.

15. **마스터 키 구성** 페이지에서 **Azure Key Vault**를 선택하고 **로그인**을 클릭합니다. 메시지가 표시되면 이 랩의 앞부분에서 Azure Key Vault 인스턴스를 프로비전하는 데 사용한 것과 같은 사용자 계정을 사용하여 인증을 진행합니다. 그런 다음 **Azure Key Vault 선택** 드롭다운 목록에 Key Vault가 표시되는지 확인하고 **다음**을 클릭합니다.

16. **실행 설정** 페이지에서 **다음**을 클릭합니다.
    
17. **요약** 페이지에서 **마침**을 클릭하여 암호화를 진행합니다. 메시지가 표시되면 이 랩의 앞부분에서 Azure Key Vault 인스턴스 프로비전에 사용한 것과 동일한 사용자 계정을 사용하여 다시 로그인합니다.

18. 암호화 프로세스가 완료되면 **결과** 페이지에서 **닫기**를 클릭합니다.

19. **SQL Server Management Studio** 콘솔의 **개체 탐색기** 창에서 **medical** 노드 아래에 있는 **보안** 및 **항상 암호화 키** 하위 노드를 확장합니다. 

    >**참고**: **항상 암호화 키** 하위 노드에는 **열 마스터 키** 및 **열 암호화 키** 하위 폴더가 포함되어 있습니다.


### 연습 4: Azure SQL 데이터베이스 암호화 과정에서 Azure Key Vault를 사용하는 방법 시연

이 연습에서는 다음 작업을 완료합니다.

- 작업 1: 데이터 기반 애플리케이션을 실행하여 Azure SQL 데이터베이스 암호화 과정에서 Azure Key Vault를 사용하는 방법 시연

#### 작업 1: 데이터 기반 애플리케이션을 실행하여 Azure SQL 데이터베이스 암호화 과정에서 Azure Key Vault를 사용하는 방법 시연

이 작업에서는 Visual Studio를 사용하여 콘솔 애플리케이션을 만들어 암호화된 열에 데이터를 로드합니다. 그런 다음 Key Vault의 키에 액세스하는 연결 문자열을 사용하여 해당 데이터에 안전하게 액세스합니다.

1. **az500-10-vm1**로의 RDP 세션 **시작 메뉴**에서 **Visual Studio 2019**를 시작합니다.

2. Visual Studio 2019 시작 메시지가 표시된 창으로 전환하여 **로그인** 단추를 클릭합니다. 메시지가 표시되면 이 랩에서 사용 중인 Azure 구독에 인증하는 데 사용한 자격 증명을 입력합니다.

3. **시작** 페이지에서 **새 프로젝트 만들기**를 클릭합니다. 

4. 프로젝트 템플릿 목록에서 **콘솔 앱(.NET 프레임워크)** 을 검색하고, 결과 목록에서 **C#** 에 대해 **콘솔 앱(.NET 프레임워크)** 을 클릭한 후 **다음**을 클릭합니다.

5. **새 프로젝트 구성** 페이지에서 다음 설정을 지정하고(다른 설정은 기본값으로 남겨둠) **만들기**를 클릭합니다.

    |설정|값|
    |---|---|
    |프로젝트 이름|**OpsEncrypt**|
    |솔루션 이름|**OpsEncrypt**|
    |프레임워크|**.NET Framework 4.7.2**|

6. Visual Studio 콘솔에서 **도구** 메뉴를 클릭하고, 드롭다운 메뉴에서 **NuGet 패키지 관리자**를 클릭한 후 계단식 메뉴에서 **패키지 관리자 콘솔**을 클릭합니다.

7. **패키지 관리자 콘솔** 창에서 다음을 실행하여 첫 번째 필수 **NuGet** 패키지를 설치합니다.

    ```powershell
    Install-Package Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider
    ```

8. **패키지 관리자 콘솔** 창에서 다음을 실행하여 두 번째 필수 **NuGet** 패키지를 설치합니다.

    ```powershell
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```
    
9. Azure 가상 머신에 대한 RDP 세션을 최소화한 다음, **\\Allfiles\\Labs\\10\\program.cs**로 이동하고, 파일을 메모장에서 열고, 콘텐츠를 클립보드에 복사합니다.

10. RDP 세션으로 돌아가고 Visual Studio 콘솔의 **솔루션 탐색기** 창에서 **Program.cs**를 클릭하고, 해당 콘텐츠를 클립보드에 복사한 코드로 바꿉니다.

11. Visual Studio 창에서 **Program.cs** 창의 줄 15에서 `<connection string noted earlier>` 자리 표시자를 랩의 앞부분에서 기록한 Azure SQL Database **ADO.NET** 연결 문자열로 바꿉니다. 연결 문자열 placehodler를 `{your_password}` 연습 1의 배포에서 지정한 암호로 바꿉니다. 랩 컴퓨터에 문자열을 저장한 경우 RDP 세션을 떠나서 ADO 문자열을 복사한 다음, Azure 가상 머신으로 돌아가서 붙여 넣어야 할 수 있습니다.

12. Visual Studio 창에서 **Program.cs** 창의 줄 16에서 `<client id noted earlier>` 자리 표시자를 랩의 앞부분에서 기록한 등록된 앱의 **애플리케이션(클라이언트) ID** 값으로 바꿉니다. 

13. Visual Studio 창에서 **Program.cs** 창의 줄 17에서 `<key value noted earlier>` 자리 표시자를 랩의 앞부분에서 기록한 등록된 앱의 **Key1** 값으로 바꿉니다. 

14. Visual Studio 콘솔에서 **시작** 단추를 클릭하여 콘솔 애플리케이션의 빌드를 시작합니다.

15. 애플리케이션은 명령 프롬프트 창을 시작합니다. 암호를 묻는 메시지가 표시되면 연습 1의 배포에서 지정한 암호를 입력하여 Azure SQL Database에 연결합니다. 

16. 콘솔 앱은 실행 중인 상태로 유지하고 **SQL Management Studio** 콘솔로 전환합니다. 

17. **개체 탐색기** 창에서 **medical 데이터베이스**를 마우스 오른쪽 단추로 클릭하고 마우스 오른쪽 클릭 메뉴에서 **새 쿼리**를 클릭합니다.

18. 쿼리 창에서 다음 쿼리를 실행하여 콘솔 앱의 데이터베이스에 로드된 데이터가 암호화되었는지 확인합니다.

    ```sql
    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
    ```

19. 이제 콘솔 애플리케이션으로 다시 전환하면 유효한 SSN을 입력하라는 메시지가 표시됩니다. 이 SSN을 입력하면 암호화된 열에서 데이터를 쿼리합니다. 명령 프롬프트에서 다음을 입력하고 Enter 키를 누릅니다.

    ```cmd
    999-99-0003
    ```

    >**참고**: 쿼리에서 반환된 데이터가 암호화되지 않았는지 확인합니다.

20. 콘솔 앱을 종료하려면 Enter 키를 누릅니다.

**리소스 정리**

> 더 이상 사용하지 않는 새로 만든 Azure 리소스는 모두 제거하세요. 사용하지 않는 리소스를 제거하면 예상하지 못한 비용이 발생하지 않습니다.

1. Azure Portal 오른쪽 위의 첫 번째 아이콘을 클릭하여 Cloud Shell을 엽니다. 

2. Cloud Shell 창의 왼쪽 위 드롭다운 메뉴에서 필요한 경우 PowerShell**을 선택하고 **메시지가 표시되면 확인을** 클릭합니다**.

3. Cloud Shell 창 내의 PowerShell 세션에서 다음을 실행하여 이 랩에서 만든 리소스 그룹을 제거합니다.
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB10" -Force -AsJob
    ```

4.  **Cloud Shell** 창을 닫습니다. 
