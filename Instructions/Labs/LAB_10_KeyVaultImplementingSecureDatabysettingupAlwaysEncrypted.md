---
lab:
  title: 10 - Key Vault(Always Encrypted를 설정하여 보안 데이터 구현)
  module: Module 03 - Secure Data and Applications
---

# <a name="lab-10-key-vault-implementing-secure-data-by-setting-up-always-encrypted"></a>랩 10: Key Vault(Always Encrypted를 설정하여 보안 데이터 구현)
# <a name="student-lab-manual"></a>학생용 랩 매뉴얼

## <a name="lab-scenario"></a>랩 시나리오

You have been asked to create a proof of concept application that makes use of the Azure SQL Database support for Always Encrypted functionality. All of the secrets and keys used in this scenario should be stored in Key Vault. The application should be registered in Azure Active Directory (Azure AD) in order to enhance its security posture. To accomplish these objectives, the proof of concept should include:

- Azure Key Vault를 만들어 자격 증명 모음에 키 및 비밀을 저장합니다.
- Always Encrypted를 사용하여 SQL Database를 만들고 데이터베이스 테이블의 열 콘텐츠를 암호화합니다.

><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: For all the resources in this lab, we are using the <bpt id="p2">**</bpt>East US<ept id="p2">**</ept> region. Verify with your instructor this is the region to use for class. 

이 개념 증명 작성과 관련된 Azure의 보안 기능을 중점적으로 살펴보려면 먼저 자동 ARM 템플릿 배포부터 진행해야 합니다. 이 작업을 수행하려면 Visual Studio 2019 및 SQL Server Management Studio 2018을 사용하여 가상 머신을 설정해야 합니다.

## <a name="lab-objectives"></a>랩 목표

이 랩에서는 다음과 같은 연습을 완료합니다:

- 연습 1: ARM 템플릿에서 기본 인프라 배포
- 연습 2: 키와 비밀을 사용하여 Key Vault 리소스 구성
- 연습 3: Azure SQL 데이터베이스 및 데이터 기반 애플리케이션 구성
- 연습 4: Azure SQL 데이터베이스 암호화 과정에서 Azure Key Vault를 사용하는 방법 시연

## <a name="key-vault-diagram"></a>Key Vault 다이어그램

![이미지](https://user-images.githubusercontent.com/91347931/157532938-c724cc40-f64f-4d69-9e91-d75344c5e0a2.png)

## <a name="instructions"></a>Instructions

## <a name="lab-files"></a>랩 파일:

- **\\Allfiles\\Labs\\10\\az-500-10_azuredeploy.json**

- **\\Allfiles\\Labs\\10\\program.cs**

### <a name="total-lab-time-estimate-60-minutes"></a>총 랩 소요 시간(예상): 60분

### <a name="exercise-1-deploy-the-base-infrastructure-from-an-arm-template"></a>연습 1: ARM 템플릿에서 기본 인프라 배포

이 연습에서는 다음 작업을 완료합니다.

- 작업 1: Azure VM 및 Azure SQL 데이터베이스 배포

#### <a name="task-1-deploy-an-azure-vm-and-an-azure-sql-database"></a>작업 1: Azure VM 및 Azure SQL 데이터베이스 배포

이 작업에서는 Azure VM을 배포합니다. 이 배포 과정에서는 Visual Studio 2019 및 SQL Server Management Studio 2018이 자동으로 설치됩니다. 

1. Azure Portal **`https://portal.azure.com/`** 에 로그인합니다.

    >**참고**: 이 랩에 사용 중인 Azure 구독에 Owner 또는 Contributor 역할이 있는 계정을 사용하여 Azure Portal에 로그인합니다.

2. Azure Portal에서 Azure Portal 페이지 위쪽의 **리소스, 서비스 및 문서 검색** 텍스트 상자에 **사용자 지정 템플릿 배포**를 입력하고 **Enter** 키를 누릅니다.

3. **사용자 지정 배포** 블레이드에서 **편집기에서 사용자 고유의 탬플릿 빌드** 옵션을 클릭합니다.

4. **템플릿 편집** 블레이드에서 **파일 로드**를 클릭하고 **\\Allfiles\\Labs\\10\\az-500-10_azuredeploy.json** 파일을 찾아 **열기**를 클릭합니다.

5. **템플릿 편집** 블레이드에서 **저장**을 클릭합니다.

6. **사용자 지정 배포** 블레이드의 **배포 범위**에서 다음 설정이 구성되어 있는지 확인합니다. 다른 설정은 모두 기본값으로 유지합니다.

   |설정|값|
   |---|---|
   |Subscription|이 랩에서 사용할 Azure 구독의 이름|
   |Resource group|**새로 만들기**를 클릭하고 **AZ500LAB10**을 이름으로 입력합니다.|
   |위치|**(미국) 미국 동부**|
   |관리자 사용자 이름|**학생**|
   |관리자 암호|**랩 04 > 연습 1 > 작업 1 > 9단계에서 만든 개인 암호를 사용하세요.**|
   
    >**참고**: 가상 머신에 로그온하는 데 사용하는 관리 자격 증명을 변경할 수는 있지만 반드시 변경할 필요는 없습니다.

    >**참고**: Azure VM을 프로비전할 수 있는 Azure 지역을 식별하려면 [ **https://azure.microsoft.com/en-us/regions/offers/** ](https://azure.microsoft.com/en-us/regions/offers/)를 참조하세요.

7. **검토 및 만들기** 단추를 클릭한 후에 **만들기** 단추를 클릭하여 배포를 확인합니다. 

    >**참고**: 이렇게 하면 이 랩에 필요한 Azure VM 및 Azure SQL 데이터베이스 배포가 시작됩니다. 

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Do not wait for the ARM template deployment to be completed, but instead continue to the next exercise. The deployment might take between <bpt id="p1">**</bpt>20-25 minutes<ept id="p1">**</ept>. 

### <a name="exercise-2-configure-the-key-vault-resource-with-a-key-and-a-secret"></a>연습 2: 키와 비밀을 사용하여 Key Vault 리소스 구성

><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: For all the resources in this lab, we are using the <bpt id="p2">**</bpt>East (US)<ept id="p2">**</ept> region. Verify with your instructor this is region to use for you class. 

이 연습에서는 다음 작업을 완료합니다.

- 작업 1: Key Vault 만들기 및 구성
- 작업 2: Key Vault에 키 추가
- 작업 3: Key Vault에 암호 추가

#### <a name="task-1-create-and-configure-a-key-vault"></a>작업 1: Key Vault 만들기 및 구성

In this task, you will create an Azure Key Vault resource. You will also configure the Azure Key Vault permissions.

1. Always Encrypted 기능에 대한 Azure SQL Database 지원을 사용하는 개념 증명 애플리케이션을 만들어야 합니다.

2. Cloud Shell 창의 왼쪽 위 모서리에 있는 드롭다운 메뉴에서 **PowerShell**이 선택되었는지 확인합니다.

3. 이 시나리오에 사용된 모든 비밀과 키는 Key Vault에 저장해야 합니다.  

    ```powershell
    $kvName = 'az500kv' + $(Get-Random)

    $location = (Get-AzResourceGroup -ResourceGroupName 'AZ500LAB10').Location

    New-AzKeyVault -VaultName $kvName -ResourceGroupName 'AZ500LAB10' -Location $location
    ```

    >애플리케이션은 보안 태세를 향상시키기 위해 Azure Active Directory(Azure AD)에 등록해야 합니다.

4. Cloud Shell 창을 닫습니다. 

5. Azure Portal에서 Azure Portal 페이지 위쪽의 **리소스, 서비스 및 문서 검색** 텍스트 상자에 **리소스 그룹**을 입력하고 **Enter** 키를 누릅니다.

6. **리소스 그룹** 블레이드의 리소스 그룹 목록에서 **AZ500LAB10** 항목이나 이전 작업에서 리소스 그룹용으로 선택한 다른 이름을 클릭합니다.

7. 리소스 그룹 블레이드에서 새로 만든 Key Vault를 나타내는 항목을 클릭합니다. 

8. Key Vault 블레이드의 **설정** 섹션에서 **액세스 정책**을 클릭한 다음 **+ 액세스 정책 추가**를 클릭합니다.

9. **액세스 정책 추가** 블레이드에서 다음 설정을 지정합니다(다른 모든 설정을 기본값으로 남겨둠). 

    |설정|값|
    |----|----|
    |템플릿에서 구성(선택 사항)|**키, 비밀 및 인증서 관리**|
    |키 권한|**모두 선택**을 클릭하면 권한이 **17개 선택**됩니다(**순환 정책 작업** 권한이 **선택 취소**되어 있어야 합니다). |
    |비밀 권한|**모두 선택**을 클릭하면 총 **8개의 선택된** 권한이 나타납니다.|
    |인증 권한|**모두 선택**을 클릭하면 권한이 총 **16개 선택**됩니다.|
    |보안 주체 선택|**선택된 항목 없음**을 클릭하고, **보안 주체** 블레이드에서 사용자 계정을 선택하고 **선택**을 클릭합니다.|

10. **액세스 정책 추가** 블레이드로 돌아와서 **추가**를 클릭하여 액세스 정책을 추가합니다. 그런 다음 Key Vault의 액세스 정책 블레이드로 다시 이동하여 **저장**을 클릭해 변경 내용을 저장합니다. 

#### <a name="task-2-add-a-key-to-key-vault"></a>작업 2: Key Vault에 키 추가

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

7. Azure Portal로 돌아와서 Key Vault 블레이드의 **설정** 섹션에서 **키**를 클릭합니다.

8. 키 목록에서 **MyLabKey** 항목을 클릭한 다음 **MyLabKey** 블레이드에서 키의 현재 버전을 나타내는 항목을 클릭합니다.

    >**참고**: 만든 키와 관련된 정보를 점검합니다.

    >이러한 목표를 달성하기 위해 개념 증명에는 다음이 포함되어야 합니다.


#### <a name="task-3-add-a-secret-to-key-vault"></a>작업 3: Key Vault에 비밀 추가

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

6. Azure Portal에서 Key Vault 블레이드로 다시 이동하여 **설정** 섹션에서 **비밀**을 클릭합니다.

7. 비밀 목록에서 **SQLPassword** 항목을 클릭한 다음 **SQLPassword** 블레이드에서 현재 버전의 비밀을 나타내는 항목을 클릭합니다.

    >**참고**: 만든 비밀과 관련된 정보를 점검합니다.

    >**참고**: 최신 버전의 비밀을 얻으려면 `https://<key_vault_name>.vault.azure.net/secrets/<secret_name>`을 참조하고, 특정 버전을 얻으려면 `https://<key_vault_name>.vault.azure.net/secrets/<secret_name>/<secret_version>`을 참조합니다.


### <a name="exercise-3-configure-an-azure-sql-database-and-a-data-driven-application"></a>연습 3: Azure SQL 데이터베이스 및 데이터 기반 애플리케이션 구성

이 연습에서는 다음 작업을 완료합니다.

- 작업 1: 클라이언트 애플리케이션이 Azure SQL Database 서비스에 액세스할 수 있도록 설정
- 작업 2: 애플리케이션의 Key Vault 액세스를 허용하는 정책 만들기
- 작업 3: SQL Azure 데이터베이스 ADO.NET 연결 문자열 검색 
- 작업 4: Visual Studio 2019 및 SQL Management Studio 2018을 실행하는 Azure VM에 로그온
- 작업 5: SQL Database에서 테이블을 만들고 암호화를 위한 데이터 열을 선택합니다.


#### <a name="task-1-enable-a-client-application-to-access-the-azure-sql-database-service"></a>작업 1: 클라이언트 애플리케이션이 Azure SQL Database 서비스에 액세스할 수 있도록 설정 

In this task, you will enable a client application to access the Azure SQL Database service. This will be done by setting up the required authentication and acquiring the Application ID and Secret that you will need to authenticate your application. T

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

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Record this value. You will need it in the next task.

6. **sqlApp** 블레이드의 **관리** 섹션에서 **인증서 및 비밀**을 클릭합니다.

7. **인증서 및 비밀** 블레이드의 **클라이언트 비밀** 섹션에서 **+ 새 클라이언트 암호**를 클릭합니다.

8. **클라이언트 암호 추가** 창에서 다음 설정을 지정합니다.

    |설정|값|
    |----|----|
    |설명|**Key1**|
    |만료|**12개월**|
    
9. 애플리케이션 자격 증명을 업데이트하려면 **추가**를 클릭합니다.

10. **sqlApp | 인증서 및 비밀** 블레이드에서 **Key1**의 값을 식별합니다.

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Record this value. You will need it in the next task. 

    >**참고**: 이 랩의 모든 리소스에 대해 **미국 동부** 지역을 사용하고 있습니다.


#### <a name="task-2-create-a-policy-allowing-the-application-access-to-the-key-vault"></a>작업 2: 애플리케이션의 Key Vault 액세스를 허용하는 정책 만들기

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


#### <a name="task-3-retrieve-sql-azure-database-adonet-connection-string"></a>작업 3: SQL Azure 데이터베이스 ADO.NET 연결 문자열 검색 

이 지역을 수업에 사용할 것인지 강사에게 확인합니다.

1. Azure Portal의 페이지 상단에 있는 **리소스, 서비스 및 문서 검색** 텍스트 상자에서 **SQL 데이터베이스**를 입력하고 **Enter** 키를 누릅니다.

2. SQL 데이터베이스 목록에서 **medical(<randomsqlservername>)** 항목을 클릭합니다.

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: If the database cannot be found, this likely means the deployment you initiated in Exercise 1 has not completed yet. You can validate this by browsing to the Azure Resource Group "AZ500LAB10" (or the name you chose), and selecting <bpt id="p1">**</bpt>Deployments<ept id="p1">**</ept> from the Settings pane.  

3. SQL Database 블레이드의 **설정** 섹션에서 **연결 문자열**을 클릭합니다. 

    >**참고**: 인터페이스에는 ADO.NET, JDBC, ODBC, PHP 및 Go용 연결 문자열이 포함됩니다. 
   
4. Record the <bpt id="p1">**</bpt>ADO.NET Connection String<ept id="p1">**</ept>. You will need it later.

    >**참고**: 연결 문자열을 사용하는 경우 `{your_password}` 자리 표시자를 연습 1의 배포에서 구성한 암호로 바꿔야 합니다.

#### <a name="task-4-log-on-to-the-azure-vm-running-visual-studio-2019-and-sql-management-studio-2018"></a>작업 4: Visual Studio 2019 및 SQL Management Studio 2018을 실행하는 Azure VM에 로그온

In this task, you log on to the Azure VM, which deployment you initiated in Exercise 1. This Azure VM hosts Visual Studio 2019 and SQL Server Management Studio 2018.

    >**Note**: Before you proceed with this task, ensure that the deployment you initiated in the first exercise has completed successfully. You can validate this by navigating to the blade of the Azure resource group "Az500Lab10" (or other name you chose) and selecting **Deployments** from the Settings pane.  

1. Azure Portal 페이지 위쪽의 **리소스, 서비스 및 문서 검색** 텍스트 상자에 **가상 머신**을 입력하고 **Enter** 키를 누릅니다.

2. In the list of Virtual Machines shown, select the <bpt id="p1">**</bpt>az500-10-vm1<ept id="p1">**</ept> entry. On the <bpt id="p1">**</bpt>az500-10-vm1<ept id="p1">**</ept> blade, on the <bpt id="p2">**</bpt>Essentials<ept id="p2">**</ept> pane, take note of the <bpt id="p3">**</bpt>Public IP address<ept id="p3">**</ept>. You will use this later. 

#### <a name="task-5-create-a-table-in-the-sql-database-and-select-data-columns-for-encryption"></a>작업 5: SQL Database에서 테이블을 만들고 암호화를 위한 데이터 열을 선택합니다.

In this task, you will connect to the SQL Database with SQL Server Management Studio and create a table. You will then encrypt two data columns using an autogenerated key from the Azure Key Vault. 

1. Azure Portal에서 **medical** SQL 데이터베이스의 블레이드로 이동하여 **필수** 섹션에서 **서버 이름**(클립보드에 복사)을 확인한 다음 도구 모음에서 **서버 방화벽 설정**을 클릭합니다.  

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Record the server name. You will need the server name later in this task.

2. **방화벽 설정** 블레이드에서 아래쪽의 **규칙 이름**으로 스크롤하여 다음 설정을 지정합니다. 

    |설정|값|
    |---|---|
    |규칙 이름|**관리 VM 허용**|
    |시작 IP|az500-10-vm1의 공용 IP 주소|
    |종료 IP|az500-10-vm1의 공용 IP 주소|

3. **저장**, **확인**을 차례로 클릭하여 변경 내용을 저장하고 확인 창을 닫습니다. 

    >**참고**: 이렇게 하면 서버 방화벽 설정이 수정됩니다. 그에 따라 이 랩에서 배포한 Azure VM 공용 IP 주소에서 medical 데이터베이스에 연결할 수 있게 됩니다.

4. **az500-10-vm1** 블레이드로 다시 이동하여 **개요**, **연결**을 차례로 클릭하고 드롭다운 메뉴에서 **RDP**를 클릭합니다. 

5. Click <bpt id="p1">**</bpt>Download RDP File<ept id="p1">**</ept> and use it to connect to the <bpt id="p2">**</bpt>az500-10-vm1<ept id="p2">**</ept> Azure VM via Remote Desktop. When prompted to authenticate, provide the following credntials:

    |설정|값|
    |---|---|
    |사용자 이름|**학생**|
    |암호|**랩 04 > 연습 1 > 작업 1 > 9단계에서 만든 개인 암호를 사용하세요.**|

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Wait for the Remote Desktop session and <bpt id="p2">**</bpt>Server Manager<ept id="p2">**</ept> to load. Close Server Manager. 

    >**참고**: 이 랩의 나머지 단계는 원격 데스크톱 세션 내에서 **az500-10-vm1** Azure VM에 대해 수행됩니다. 

6. **시작**을 클릭하고, **시작** 메뉴에서**Microsoft SQL Server Tools 18** 폴더를 확장하고  **Microsoft SQL Server Management Studio** 메뉴 항목을 클릭합니다.

7. **서버에 연결** 대화 상자에서, 다음 설정을 지정합니다. 

    |설정|값|
    |---|---|
    |서버 유형|**데이터베이스 엔진**|
    |서버 이름|이 작업의 앞부분에서 식별한 서버 이름|
    |인증|**SQL Server 인증**|
    |로그인|**학생**|
    |암호|**랩 04 > 연습 1 > 작업 1 > 9단계에서 만든 개인 암호를 사용하세요.**|

8. **서버에 연결** 대화 상자에서 **연결**을 클릭합니다.

9. **SQL Server Management Studio** 콘솔 내, **개체 탐색기** 창에서 **데이터베이스** 폴더를 확장합니다.

10. **개체 탐색기** 창에서 **의료** 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **새 쿼리**를 클릭합니다.

11. Paste the following code into the query window and click <bpt id="p1">**</bpt>Execute<ept id="p1">**</ept>. This will create a <bpt id="p1">**</bpt>Patients<ept id="p1">**</ept> table.

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
    
17. On the <bpt id="p1">**</bpt>Summary<ept id="p1">**</ept> page, click <bpt id="p2">**</bpt>Finish<ept id="p2">**</ept> to proceed with the encryption. When prompted, sign in again by using the same user account you used to provision the Azure Key Vault instance earlier in this lab.

18. 암호화 프로세스가 완료되면 **결과** 페이지에서 **닫기**를 클릭합니다.

19. **SQL Server Management Studio** 콘솔의 **개체 탐색기** 창에서 **medical** 노드 아래에 있는 **보안** 및 **항상 암호화 키** 하위 노드를 확장합니다. 

    >**참고**: **항상 암호화 키** 하위 노드에는 **열 마스터 키** 및 **열 암호화 키** 하위 폴더가 포함되어 있습니다.


### <a name="exercise-4-demonstrate-the-use-of-azure-key-vault-in-encrypting-the-azure-sql-database"></a>연습 4: Azure SQL 데이터베이스 암호화 과정에서 Azure Key Vault를 사용하는 방법 시연

이 연습에서는 다음 작업을 완료합니다.

- 작업 1: 데이터 기반 애플리케이션을 실행하여 Azure SQL 데이터베이스 암호화 과정에서 Azure Key Vault를 사용하는 방법 시연

#### <a name="task-1-run-a-data-driven-application-to-demonstrate-the-use-of-azure-key-vault-in-encrypting-the-azure-sql-database"></a>작업 1: 데이터 기반 애플리케이션을 실행하여 Azure SQL 데이터베이스 암호화 과정에서 Azure Key Vault를 사용하는 방법 시연

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

11. In the Visual Studio window, in the <bpt id="p1">**</bpt>Program.cs<ept id="p1">**</ept> pane, in line 15, replace the <ph id="ph1">`&lt;connection string noted earlier&gt;`</ph> placeholder with the Azure SQL database <bpt id="p2">**</bpt>ADO.NET<ept id="p2">**</ept> connection string you recorded earlier in the lab. In the connection string, replace the <ph id="ph1">`{your_password}`</ph> placehodler, with <ph id="ph2">`Pa55w.rd1234`</ph>. If you saved the string on the lab computer, you may need to leave the RDP session to copy the ADO string, then return to the Azure virtual machine to paste it in.

12. Visual Studio 창에서 **Program.cs** 창의 줄 16에서 `<client id noted earlier>` 자리 표시자를 랩의 앞부분에서 기록한 등록된 앱의 **애플리케이션(클라이언트) ID** 값으로 바꿉니다. 

13. Visual Studio 창에서 **Program.cs** 창의 줄 17에서 `<key value noted earlier>` 자리 표시자를 랩의 앞부분에서 기록한 등록된 앱의 **Key1** 값으로 바꿉니다. 

14. Visual Studio 콘솔에서 **시작** 단추를 클릭하여 콘솔 애플리케이션의 빌드를 시작합니다.

15. The application will start a Command Prompt window. When prompted for password, type the password that you specified in the deployment in Exercise 1 to connect to Azure SQL Database. 

16. 콘솔 앱은 실행 중인 상태로 유지하고 **SQL Management Studio** 콘솔로 전환합니다. 

17. **개체 탐색기** 창에서 **medical 데이터베이스**를 마우스 오른쪽 단추로 클릭하고 마우스 오른쪽 클릭 메뉴에서 **새 쿼리**를 클릭합니다.

18. 쿼리 창에서 다음 쿼리를 실행하여 콘솔 앱의 데이터베이스에 로드된 데이터가 암호화되었는지 확인합니다.

    ```sql
    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
    ```

19. Switch back to the console application where you are prompted to enter a valid SSN. This will query the encrypted column for the data. At the Command Prompt, type the following and press the Enter key:

    ```cmd
    999-99-0003
    ```

    >**참고**: 쿼리에서 반환된 데이터가 암호화되지 않았는지 확인합니다.

20. 콘솔 앱을 종료하려면 Enter 키를 누릅니다.

**리소스 정리**

> Remember to remove any newly created Azure resources that you no longer use. Removing unused resources ensures you will not incur unexpected costs.

1. Azure Portal 오른쪽 위의 첫 번째 아이콘을 클릭하여 Cloud Shell을 엽니다. 

2. Cloud Shell 창의 왼쪽 위 드롭다운 메뉴에서 **PowerShell**을 선택하고 메시지가 표시되면 **확인**을 클릭합니다.

3. Cloud Shell 창 내의 PowerShell 세션에서 다음을 실행하여 이 랩에서 만든 리소스 그룹을 제거합니다.
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB10" -Force -AsJob
    ```

4.  **Cloud Shell** 창을 닫습니다. 
