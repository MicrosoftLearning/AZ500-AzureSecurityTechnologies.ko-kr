---
lab:
  title: 03 - Microsoft Entra 확인된 ID 테넌트 구성
  module: Module 01 - Manage Identity and Access
---
# 랩 03: Microsoft Entra 확인된 ID 테넌트 구성
# 학생용 랩 매뉴얼

## 랩 시나리오

# Microsoft Entra Verified ID에 대한 테넌트 구성

>[!Note] 
> Azure Active Directory 확인 가능한 자격 증명은 이제 Microsoft Entra 확인 ID이며 Microsoft Entra 제품군의 일부입니다. ID 솔루션의 [Microsoft Entra 제품군](https://aka.ms/EntraAnnouncement)에 대해 자세히 알아보고 [통합 Microsoft Entra 관리 센터](https://entra.microsoft.com)에서 시작합니다.

Microsoft Entra Verified ID는 조직을 보호할 수 있도록 하는 탈중앙화 ID 솔루션입니다. 이 서비스를 사용하면 자격 증명을 발급하고 확인할 수 있습니다. 발급자는 Verified ID 서비스를 사용하여 자신의 사용자 지정 확인 가능한 자격 증명을 발급할 수 있습니다. 검증 도구는 서비스의 무료 REST API를 사용하여 앱 및 서비스에서 확인 가능한 자격 증명을 쉽게 요청하고 수락할 수 있습니다. 두 경우 모두 Azure AD 테넌트가 자체 확인 가능한 자격 증명을 발급하거나 타사에서 발급한 사용자의 확인 가능한 자격 증명을 표시하도록 구성해야 합니다. 발급자 및 검증 도구인 경우 단일 Azure AD 테넌트를 사용하여 자체 확인 가능한 자격 증명을 발급하고 다른 사용자의 자격 증명을 확인할 수 있습니다.

이 자습서에서는 검증 가능한 자격 증명 서비스를 사용하도록 Azure AD 테넌트를 구성하는 방법을 알아봅니다.

특히 다음 방법을 알아봅니다.

> - Azure Key Vault 인스턴스를 만듭니다.
> - 확인된 ID 서비스를 설정합니다.
> - Azure AD에서 애플리케이션을 등록합니다.
> - DID(탈중앙화 ID)에 대한 도메인 소유권 확인

다음 다이어그램은 Verified ID 아키텍처와 구성하는 구성 요소를 보여 줍니다.

![이미지](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/8d4d01c2-3110-421a-91a8-7b052bc8d793)


## 필수 조건

- 구성하려는 디렉터리에 대한 전역 관리자 또는 인증 정책 관리자 권한이 있어야 합니다. 전역 관리자가 아닌 경우 관리자 동의 부여를 포함하여 앱 등록을 완료하려면 애플리케이션 관리자 권한이 필요합니다.
- Azure 구독 또는 Azure Key Vault를 배포할 리소스 그룹에 대한 기여자 역할이 있는지 확인합니다.

## 키 자격 증명 모음 만들기

Azure Key Vault는 비밀 및 키의 보안 스토리지 및 액세스를 가능하게 하는 클라우드 서비스입니다. Verified ID 서비스는 Azure Key Vault에 공용 및 프라이빗 키를 저장합니다. 이러한 키는 자격 증명에 서명하고 확인하는 데 사용됩니다.

### Azure Portal을 사용하여 Azure Key Vault를 만듭니다.

1. 브라우저 세션을 시작하고 Azure Portal 메뉴에 [로그인합니다.](https://portal.azure.com/)
   
2. Azure Portal 검색 상자에 Key Vault를 입력 **합니다.**

3. 결과 목록에서 **Key Vault**를 선택합니다.

4. 키 자격 증명 모음 섹션에서 만들기를 선택합니다 **.**

5. **키** 자격 증명 모음 만들기의 **기본 사항** 탭에서 다음 정보를 입력하거나 선택합니다.
   
   |설정|값|
   |---|---|
   |**프로젝트 세부 정보**|
   |Subscription|구독을 선택합니다.|
   |Resource group|azure-rg-1을 입력합니다 **.** **확인**을 선택합니다.|
   |**인스턴스 세부 정보**|
   |키 자격 증명 모음 이름|Contoso-vault2를 입력 **합니다.**|
   |지역|**미국 동부**를 선택합니다.|
   |가격 책정 계층|시스템 기본 **표준**|
   |삭제된 자격 증명 모음 보존 일수|시스템 기본값 **90**|

6. 검토 + 만들기 탭**을 **선택하거나 페이지 아래쪽에 있는 파란색 검토 + 만들기 단추를 선택합니다.
  
7. **만들기**를 실행합니다.

다음 두 가지 속성에 유의합니다.

* **자격 증명 모음 이름**: 예제에서 Contoso-Vault2**입니다**. 다른 단계에서는 이 이름을 사용합니다.
* **자격 증명 모음 URI**: 예제에서 자격 증명 모음 URI는 `https://contoso-vault2.vault.azure.net/`. REST API를 통해 자격 증명 모음을 사용하는 애플리케이션은 이 URI를 사용해야 합니다.

이때 사용자의 Azure 계정은 이 새 자격 증명 모음에서 작업을 수행할 권한이 있는 유일한 계정입니다.

>[!NOTE]
>기본적으로 자격 증명 모음을 만드는 계정에만 액세스 권한이 있습니다. Verified ID 서비스는 키 자격 증명 모음에 대한 액세스 권한이 필요합니다. 구성 중에 사용된 계정이 키를 만들고 삭제할 수 있도록 하는 액세스 정책으로 키 자격 증명 모음을 구성해야 합니다. 구성 중에 사용되는 계정에는 Verified ID에 대한 도메인 바인딩을 만들 수 있도록 서명 권한도 필요합니다. 테스트하는 동안 동일한 계정을 사용하는 경우에는 자격 증명 모음 생성자에게 부여된 기본 권한 외에도 계정 서명 권한을 부여하도록 기본 정책을 수정합니다.

### Key Vault에 대한 액세스 정책 설정

Key Vault는 지정된 보안 주체가 Key Vault 비밀 및 키에 대한 작업을 수행할 수 있는지 여부를 정의합니다. Verified ID 서비스 관리자 계정과 만든 Request Service API 보안 주체 모두에 대해 키 자격 증명 모음에서 액세스 정책을 설정합니다.
Key Vault를 만든 후 확인 가능한 자격 증명은 메시지 보안을 제공하는 데 사용되는 키 집합을 생성합니다. 이러한 키는 Key Vault에 저장됩니다. 확인 가능한 자격 증명을 서명, 업데이트 및 복구하는 데 키 집합을 사용합니다.

### Verified ID Admin 사용자에 대한 액세스 정책 설정

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 이 자습서에 사용하는 키 자격 증명 모음으로 이동합니다.

3. **설정**에서 **액세스 정책**을 선택합니다.

4. **액세스 정책 추가**의 **사용자** 아래에서 이 자습서를 수행하는 데 사용할 계정을 선택합니다.

5. **키 권한**에 대해 **가져오기**, **만들기**, **삭제**, **서명** 권한이 선택되어 있는지 확인합니다. 기본적으로 **만들기**와 **삭제**는 이미 사용하도록 설정되어 있습니다. **서명** 키 권한만 업데이트하면 됩니다.

      ![이미지](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/7c8a92ea-24f1-41e6-9656-869e8486af72)


6. 변경 내용을 저장하려면 **저장**을 선택합니다.

## Verified ID 설정

![이미지](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/b4b857a2-24b8-4c3f-9f5c-43d23b58427f)


Verified ID를 설정하려면 다음 단계를 따릅니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 확인된 ID*를 *검색합니다. 그런 다음 **Verified ID**를 선택합니다.

3. 왼쪽 메뉴에서 **설치**를 선택합니다.

4. 가운데 메뉴에서 조직 설정 정의를 선택합니다 **.**

5. 다음 정보를 제공하여 조직을 설정합니다.

    1. **조직 이름**: 확인된 ID 내에서 비즈니스를 참조할 이름을 입력합니다. 고객에게는 이 이름이 표시되지 않습니다.

    1. **신뢰할 수 있는 도메인**: DID(탈중앙화 ID) 문서의 서비스 엔드포인트에 추가되는 도메인을 입력합니다. 도메인은 DID를 사용자가 해당 비즈니스에 대해 알 수 있는 유형의 무언가에 바인딩하는 것입니다. Microsoft Authenticator 및 기타 디지털 지갑에서 이 정보를 사용하여 DID가 도메인에 연결되어 있는지 확인합니다. 월렛이 DID를 확인할 수 있으면 확인된 기호가 표시됩니다. 월렛이 DID를 확인할 수 없는 경우 유효성을 검사할 수 없는 조직에서 자격 증명이 발급되었음을 사용자에게 알립니다.

        >[!IMPORTANT]
        > 도메인은 리디렉션이 될 수 없습니다. 리디렉션이 가능한 경우에는 DID와 도메인을 연결할 수 없습니다. HTTPS를 도메인에 사용해야 합니다. 예: `https://did.woodgrove.com`

    1. **Key Vault**: 이전에 만든 키 자격 증명 모음을 선택합니다.

    1. **고급**에서 테넌트에 사용할 **트러스트 시스템**을 선택할 수 있습니다. **웹** 또는 **ION** 중에서 선택할 수 있습니다. 웹은 테넌트가 [did:web](https://w3c-ccg.github.io/did-method-web/)을 did 메서드로 사용한다는 것을 의미하고 ION이 [did:ion](https://identity.foundation/ion/)을 사용한다는 것을 의미합니다.

        >[!IMPORTANT]
        > 트러스트 시스템을 변경하는 유일한 방법은 확인된 ID 서비스를 옵트아웃하고 온보딩을 다시 수행하는 것입니다.

1. **저장**을 선택합니다.  

   ![이미지](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/b191676e-03e3-423f-aa28-1e3d2887ea07)


### 확인된 ID 서비스 주체에 대한 액세스 정책 설정

이전 단계에서 확인된 ID를 설정하면 Azure Key Vault의 액세스 정책이 자동으로 업데이트되어 확인된 ID에 대한 서비스 주체에게 필요한 권한을 부여합니다.  
사용 권한을 수동으로 다시 설정해야 하는 경우 액세스 정책은 다음과 같습니다.

| 서비스 주체 | AppId | 키 권한 |
| -------- | -------- | -------- |
| 확인 가능한 자격 증명 서비스 | bb2a64ee-5d29-4b07-a491-25806dc854d3 | 가져오기, 서명 |
| 확인 가능한 자격 증명 서비스 요청 | 3db474b9-6a0c-4840-96ac-1fceb342124f | Sign |



![이미지](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/896b8ea0-b88b-43b8-a93b-4771defedfde)


## Azure AD에서 애플리케이션 등록

애플리케이션은 자격 증명을 발급하거나 확인할 수 있도록 Microsoft Entra Verified ID를 호출하려는 경우 액세스 토큰을 가져와야 합니다. 액세스 토큰을 받기 위해서는 웹 애플리케이션을 등록하고 Verified ID 요청 서비스에 대한 API 권한을 부여해야 합니다. 예를 들어 웹 애플리케이션에 대해 다음 단계를 사용합니다.

1. 관리자 계정으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 여러 테넌트에 대한 액세스 권한이 있는 경우 **디렉터리 + 구독**을 선택합니다. 그런 다음, **Azure Active Directory**를 검색하여 선택합니다.

1. **관리** 아래에서 **앱 등록** > **새 등록**을 선택합니다.  

   ![이미지](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/03ca3d13-5564-4ce2-b42c-f8333bc97fb5)


1. 애플리케이션의 표시 이름을 입력합니다. 예를 들어 *verifiable-credentials-app*과 같습니다.

1. **지원되는 계정 유형**에 대해 **이 조직 디렉터리의 계정만(기본 디렉터리만 - 단일 테넌트)** 을 선택합니다.

1. **등록**을 선택하여 애플리케이션을 만듭니다.

   ![이미지](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/d0a15737-c8a9-4522-990d-1cf6bc12cc1e)


### 액세스 토큰을 얻을 수 있는 권한 부여

이 단계에서는 권한을 **확인 가능한 자격 증명 서비스 요청** 서비스 주체에 부여합니다.

필요한 권한을 추가하려면 다음 단계를 수행합니다.

1. **verifiable-credentials-app** 애플리케이션 세부 정보 페이지에 남아 있습니다. **API 권한** > **권한 추가**를 선택합니다.

   ![이미지](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/adf97022-2081-4273-8d61-f2b53628e989)

1. **조직에서 사용하는 API** 선택.

1. **확인 가능한 자격 증명 서비스 요청** 서비스 주체를 검색하고 선택합니다.

   ![이미지](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/01691eb2-ab7f-4778-9911-342eb9350f99)

1. **애플리케이션 권한**을 선택하고, **VerifiableCredential.Create.All**을 펼칩니다.

   ![이미지](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/3c5e008e-2ba5-417a-90ff-22e8f622ad34)


1. **권한 추가**를 선택합니다.

1. 에 대한 관리자 동의 부여를 \<your tenant name\>**선택합니다**.

범위를 다른 애플리케이션으로 분리하려는 경우 발급 및 프레젠테이션 권한을 별도로 부여하도록 선택할 수 있습니다.

![이미지](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/640def45-e666-423e-bf69-598e8980b125)

## 탈중앙화 ID 등록 및 도메인 소유권 확인

Azure Key Vault가 설정되고 서비스에 서명 키가 있으면 설정에서 2단계와 3단계를 완료해야 합니다.

![이미지](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/40e10177-b025-4d10-b16a-d66c06762c3f)

1. Azure Portal에서 확인된 ID 서비스로 이동합니다.  
1. 왼쪽 메뉴에서 **설치**를 선택합니다.
1. 가운데 메뉴에서 소유권 확인을 선택합니다**기본**.

# DID(탈중앙화 ID)에 대한 도메인 소유권 확인

## 도메인 소유권 확인 및 did-configuration.json 파일 배포

이 도메인의 경우 도메인을 사용자가 제어해야 하며 `https://www.example.com/` 형식이어야 합니다. 

1. Azure Portal에서 Verified ID 페이지로 이동합니다.

1. **설치**를 선택한 다음, **도메인 소유권 확인**을 선택하고 도메인에 대해 **확인**을 선택합니다.

1. `did-configuration.json` 파일을 복사하거나 다운로드합니다.

   ![이미지](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/342fa12f-2d0d-40cf-a2f9-8796a86f0824)

1. `did-configuration.json` 파일을 지정된 위치에 호스트합니다. 예: 도메인 `https://www.example.com`을 지정한 경우 이 URL `https://www.example.com/.well-known/did-configuration.json`에서 파일을 호스트해야 합니다.
   URL에는 `.well-known path` 이름 이외의 추가 경로가 있을 수 없습니다.

1. `did-configuration.json`을 .well-known/did-configuration.json URL에서 공개적으로 사용할 수 있는 경우 **확인 상태 새로 고침** 단추를 눌러 확인합니다.

   ![이미지](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/49a06251-af56-49b2-9059-0bd4ca678da6)

> 결과: Azure Key Vault 인스턴스를 성공적으로 만들고, 확인된 ID 서비스를 설정하고, Azure AD에 애플리케이션을 등록하고, DID(탈중앙화 식별자)에 대한 소유권을 확인했습니다기본.

**리소스 정리**

> 더 이상 사용하지 않는 새로 만든 Azure 리소스는 모두 제거하세요. 사용하지 않는 리소스를 제거하면 예상하지 못한 비용이 발생하지 않습니다. 
