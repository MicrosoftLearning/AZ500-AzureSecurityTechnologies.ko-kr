---
lab:
  title: ' 12 - 서비스 엔드포인트 및 스토리지 보안'
  module: Module 03 - Secure data and applications
---

# <a name="lab-12-service-endpoints-and-securing-storage"></a>랩 12: 서비스 엔드포인트 및 보안 스토리지
# <a name="student-lab-manual"></a>학생용 랩 매뉴얼

## <a name="lab-scenario"></a>랩 시나리오

You have been asked to create a proof of concept to demonstrate securing Azure file shares. Specifically, you want to:
    
- Azure Storage로 향하는 트래픽이 항상 Azure 백본 네트워크 내에 유지되도록 스토리지 엔드포인트를 만듭니다.
- 특정 서브넷의 리소스만 스토리지에 액세스할 수 있도록 스토리지 엔드포인트를 구성합니다.
- 특정 서브넷 외부의 리소스가 스토리지에 액세스할 수 없는지 확인합니다. 

> For all the resources in this lab, we are using the <bpt id="p1">**</bpt>East US<ept id="p1">**</ept> region. Verify with your instructor this is the region to use for class. 

## <a name="lab-objectives"></a>랩 목표

이 랩에서는 다음과 같은 연습을 완료합니다.

- 연습 1: 서비스 엔드포인트 및 보안 스토리지

## <a name="service-endpoints-and-securing-storage-diagram"></a>서비스 엔드포인트 및 스토리지 보안 다이어그램

![이미지](https://user-images.githubusercontent.com/91347931/157534883-29664a05-85d1-4c70-99a7-f16d2360755d.png)

## <a name="instructions"></a>Instructions

### <a name="exercise-1-service-endpoints-and-security-storage"></a>연습 1: 서비스 엔드포인트 및 보안 스토리지

### <a name="estimated-timing-45-minutes"></a>예상 소요 시간: 45분

이 연습에서는 다음 작업을 완료합니다.

- 작업 1: 가상 네트워크 만들기
- 작업 2: 가상 네트워크에 서브넷을 추가하고 스토리지 엔드포인트를 구성합니다.
- 작업 3: 서브넷에 대한 액세스를 제한하도록 네트워크 보안 그룹을 구성합니다.
- 작업 4: 공용 서브넷에서 rdp를 허용하도록 네트워크 보안 그룹 구성
- 작업 5: 파일 공유를 통해 스토리지 계정 만들기
- 작업 6: 지정된 서브넷에 가상 머신 배포
- 작업 7: 프라이빗 서브넷에서 스토리지 연결을 테스트하여 액세스가 허용되는지 확인
- 작업 8: 공용 서브넷에서 스토리지 연결을 테스트하여 액세스가 거부되는지 확인 

#### <a name="task-1-create-a-virtual-network"></a>작업 1: 가상 네트워크 만들기

이 작업에서는 가상 네트워크를 만듭니다.

1. Azure Portal **`https://portal.azure.com/`** 에 로그인합니다.

    >**참고**: 이 랩에 사용 중인 Azure 구독에 Owner 또는 Contributor 역할이 있는 계정을 사용하여 Azure Portal에 로그인합니다.

2. Azure Portal 내 Azure Portal 페이지 상단의 **리소스, 서비스 및 문서 검색** 텍스트 상자에 **가상 네트워크**를 입력하고 **Enter** 키를 누릅니다.

3. **가상 네트워크** 블레이드에서  **+ 만들기**를 클릭합니다.

4. **가상 네트워크 만들기** 블레이드의 **기본** 탭에서 다음 설정을 지정하고(다른 설정은 기본값으로 둡니다) **다음: IP 주소**를 클릭합니다.

    |설정|값|
    |---|---|
    |Subscription|이 랩에서 사용 중인 Azure 구독의 이름|
    |Resource group|**새로 만들기**를 클릭하고 **AZ500LAB12**를 이름으로 입력합니다.|
    |이름|**myVirtualNetwork**|
    |지역|**(미국) 미국 동부**|

5. **가상 네트워크 만들기** 블레이드의 **IP 주소** 탭에서 **IPv4 주소 공간**을 **10.0.0.0/16**으로 설정하고 **서브넷 이름** 열에서 **기본값**을 클릭한 후 **서브넷 편집** 블레이드에서 다음 설정을 지정하고 **저장**을 클릭합니다.

    |설정|값|
    |---|---|
    |서브넷 이름|**공용**|
    |서브넷 주소 범위|**10.0.0.0/24**|

6. 다시 **가상 네트워크 만들기** 블레이드의 **IP 주소** 탭에서 **검토 + 만들기**를 클릭합니다.

7. **가상 네트워크 만들기** 블레이드의 **검토 + 만들기** 탭에서 **만들기**를 클릭합니다.

#### <a name="task-2-add-a-subnet-to-the-virtual-network-and-configure-a-storage-endpoint"></a>작업 2: 가상 네트워크에 서브넷을 추가하고 스토리지 엔드포인트를 구성합니다.

In this task, you will create another subnet and enable a service endpoint on that subnet. Service endpoints are enabled per service, per subnet. 

1. Azure Portal에서 **가상 네트워크** 블레이드로 돌아갑니다.

2. **가상 네트워크** 블레이드에서 **myVirtualNetwork** 항목을 클릭합니다.

3. **myVirtualNetwork** 블레이드의 **설정** 섹션에서 **서브넷**을 클릭합니다.

4. **myVirtualNetwork \| 서브넷** 블레이드에서 **+ 서브넷**을 클릭합니다. 

5. **서브넷 추가** 블레이드에서 다음 설정을 지정합니다(다른 설정은 기본값으로 둡니다).

    |설정|값|
    |---|---|
    |서브넷 이름|**개인**|
    |서브넷 주소 범위|**10.0.1.0/24**|
    |서비스 엔드포인트|**Microsoft.Storage**|

6. **서브넷 추가** 블레이드에서 **저장**을 클릭하여 새 서브넷을 추가합니다.

    >**참고**: 이제 가상 네트워크에는 두 개의 서브넷이 있습니다: 공용 및 프라이빗. 
    
#### <a name="task-3-configure-a-network-security-group-to-restrict-access-to-the-subnet"></a>작업 3: 서브넷에 대한 액세스를 제한하도록 네트워크 보안 그룹을 구성합니다.

In this task, you will create a network security group with two outbound security rules (Storage and internet) and one inbound security rule (RDP). You will also associate the network security group with the Private subnet. This will restrict outbound traffic from Azure VMs connected to that subnet.

1. Azure Portal에서 Azure Portal 페이지 위쪽의 **리소스, 서비스 및 문서 검색** 텍스트 상자에 **네트워크 보안 그룹**을 입력하고 **Enter** 키를 누릅니다.

2. **네트워크 보안 그룹** 블레이드에서 **+ 만들기**를 클릭합니다.

3. **네트워크 보안 그룹 만들기** 블레이드의 **기본** 탭에서 다음 설정을 지정합니다. 

    |설정|값|
    |---|---|
    |Subscription|이 랩에서 사용 중인 Azure 구독의 이름|
    |Resource group|**AZ500LAB12**|
    |이름|**myNsgPrivate**|
    |지역|**미국 동부**|

4. **검토 + 만들기**를 클릭한 다음, **만들기**를 클릭합니다.

    >**참고**: 다음 단계에서는 Azure Storage 서비스와의 통신을 허용하는 아웃바운드 보안 규칙을 만듭니다. 

5. Azure Portal에서 **네트워크 보안 그룹** 블레이드로 다시 이동하여 **myNsgPrivate** 항목을 클릭합니다.

6. **myNsgPrivate** 블레이드의 **설정** 섹션에서 **아웃바운드 보안 규칙**을 클릭합니다.

7. **myNsgPrivate \| 아웃바운드 보안 규칙** 블레이드에서 **+ 추가**를 클릭합니다.

8. **아웃바운드 보안 규칙** 블레이드에서 Azure Storage에 대한 아웃바운드 트래픽을 명시적으로 허용하도록 다음 설정을 지정합니다(다른 모든 값은 기본 설정으로 둡니다). 

    |설정|값|
    |---|---|
    |원본|**서비스 태그**|
    |원본 서비스 태그|**VirtualNetwork**|
    |원본 포트 범위|**\***|
    |대상|**서비스 태그**|
    |대상 서비스 태그|**스토리지**|
    |대상 포트 범위|**\***|
    |프로토콜|**임의**|
    |작업|**허용**|
    |우선 순위|**1000**|
    |이름|**Allow-Storage-All**|

9. **아웃바운드 보안 규칙 추가** 블레이드에서 **추가**를 클릭하여 새 아웃바운드 규칙을 만듭니다. 

10. **myNsgPrivate** 블레이드의 **설정** 섹션에서 **아웃바운드 보안 규칙**을 클릭한 다음 **+ 추가**를 클릭합니다.

11. **아웃바운드 보안 규칙 추가** 블레이드에서 인터넷에 대한 아웃바운드 트래픽을 명시적으로 거부하도록 다음 설정을 지정합니다(다른 모든 값은 기본 설정으로 둡니다). 

    |설정|값|
    |---|---|
    |원본|**서비스 태그**|
    |원본 서비스 태그|**VirtualNetwork**|
    |원본 포트 범위|**\***|
    |대상|**서비스 태그**|
    |대상 서비스 태그|**인터넷**|
    |대상 포트 범위|**\***|
    |프로토콜|**임의**|
    |작업|**Deny**|
    |우선 순위|**1100**|
    |이름|**Deny-Internet-All(모든 인터넷 거부)**|

    >**참고**: 이 규칙은 모든 네트워크 보안 그룹에서 아웃바운드 인터넷 통신을 허용하는 기본 규칙을 재정의합니다. 

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: In the next steps, you will create an inbound security rule that allows Remote Desktop Protocol (RDP) traffic to the subnet. The rule overrides a default security rule that denies all inbound traffic from the internet. Remote Desktop connections are allowed to the subnet so that connectivity can be tested in a later step.

12. **myNsgPrivate** 블레이드의 **설정** 섹션에서 **인바운드 보안 규칙**을 클릭한 다음 **+ 추가**를 클릭합니다.

13. **인바운드 보안 규칙 추가** 블레이드에서 다음 설정을 지정합니다(다른 모든 값은 기본값으로 둡니다). 

    |설정|값|
    |---|---|
    |원본|**임의**|
    |원본 포트 범위|**\***|
    |대상|**서비스 태그**|
    |대상 서비스 태그|**VirtualNetwork**|
    |대상 포트 범위|**3389**|
    |프로토콜|**TCP**|
    |작업|**허용**|
    |우선 순위|**1200**|                                                    
    |이름|**Allow-RDP-All**|

14. **인바운드 보안 규칙 추가** 블레이드에서 **추가**를 클릭하여 새 인바운드 규칙을 만듭니다. 

    >**참고**: 이제 네트워크 보안 그룹을 개인 서브넷과 연결합니다.

15. **서브넷** 블레이드에서 **+ 연결**을 선택하고 **서브넷 연결** 섹션에서 다음 설정을 지정한 다음 **확인**을 클릭합니다.

    |설정|값|
    |---|---|
    |가상 네트워크|**myVirtualNetwork**|
    |서브넷|**개인**|

#### <a name="task-4-configure-a-network-security-group-to-allow-rdp-on-the-public-subnet"></a>작업 4: 공용 서브넷에서 RDP를 허용하도록 네트워크 보안 그룹 구성

Azure 파일 공유 보안을 입증하기 위해 개념 증명을 만들라는 메시지가 표시됩니다.

1. Azure Portal에서 Azure Portal 페이지 위쪽의 **리소스, 서비스 및 문서 검색** 텍스트 상자에 **네트워크 보안 그룹**을 입력하고 **Enter** 키를 누릅니다.

2. **네트워크 보안 그룹** 블레이드에서 **+ 만들기**를 클릭합니다.

3. **네트워크 보안 그룹 만들기** 블레이드의 **기본** 탭에서 다음 설정을 지정합니다. 

    |설정|값|
    |---|---|
    |Subscription|이 랩에서 사용 중인 Azure 구독의 이름|
    |Resource group|**AZ500LAB12**|
    |이름|**myNsgPublic**|
    |지역|**미국 동부**|

4. **검토 + 만들기**를 클릭한 다음, **만들기**를 클릭합니다.

    >**참고**: 다음 단계에서는 Azure Storage 서비스와의 통신을 허용하는 아웃바운드 보안 규칙을 만듭니다. 

5. Azure 포털에서 **네트워크 보안 그룹** 블레이드로 돌아가 **myNsgPublic** 항목을 클릭합니다.

6. **myNsgPublic** 블레이드의 **설정** 섹션에서 **인바운드 보안 규칙**을 클릭한 다음 + **추가**를 클릭합니다.

7. **인바운드 보안 규칙 추가** 블레이드에서 다음 설정을 지정합니다(다른 모든 값은 기본값으로 둡니다). 

    |설정|값|
    |---|---|
    |원본|**임의**|
    |원본 포트 범위|**\***|
    |대상|**서비스 태그**|
    |대상 서비스 태그|**VirtualNetwork**|
    |대상 포트 범위|**3389**|
    |프로토콜|**TCP**|
    |작업|**허용**|
    |우선 순위|**1200**|                                                    
    |이름|**Allow-RDP-All**|

8. **인바운드 보안 규칙 추가** 블레이드에서 **추가**를 클릭하여 새 인바운드 규칙을 만듭니다. 

    >**참고**: 이제 네트워크 보안 그룹을 공용 서브넷과 연결합니다.

9. **서브넷** 블레이드에서 **+ 연결**을 선택하고 **서브넷 연결** 섹션에서 다음 설정을 지정한 다음 **확인**을 클릭합니다.

    |설정|값|
    |---|---|
    |가상 네트워크|**myVirtualNetwork**|
    |서브넷|**공용**|
    
#### <a name="task-5-create-a-storage-account-with-a-file-share"></a>작업 5: 파일 공유를 통해 스토리지 계정 만들기

이 작업에서는 파일 공유가 있는 스토리지 계정을 만들고 스토리지 계정 키를 가져옵니다.  

1. Azure Portal에서 Azure Portal 페이지 위쪽의 **리소스, 서비스 및 문서 검색** 텍스트 상자에 **스토리지 계정**을 입력하고 **Enter** 키를 누릅니다.

2. **스토리지 계정** 블레이드에서  **+ 만들기**를 클릭합니다.

3. **스토리지 계정 만들기** 블레이드의 **기본** 탭에서 다음 설정을 지정합니다(나머지는 기본값을 그대로 유지).

    |설정|값|
    |---|---|
    |Subscription|이 랩에서 사용 중인 Azure 구독의 이름|
    |Resource group|**AZ500LAB12**|
    |스토리지 계정 이름|3~24자 사이의 문자와 숫자로 구성된 전역적으로 고유한 이름|
    |위치|**(미국) 미국 동부**|
    |성능|**표준(범용 v2 계정)**|
    |중복성|**LRS(로컬 중복 스토리지)**|

4. **스토리지 계정 만들기** 블레이드의 **기본** 탭에서 **검토 + 만들기**를 클릭하고 유효성 검사 프로세스가 완료될 때까지 기다렸다가 **만들기**를 클릭합니다.

    >특히 다음을 수행해야 합니다.

5. Azure Portal에서 Azure Portal 페이지 위쪽의 **리소스, 서비스 및 문서 검색** 텍스트 상자에 **리소스 그룹**을 입력하고 **Enter** 키를 누릅니다.

6. **리소스 그룹** 블레이드의 리소스 그룹 목록에서 **AZ500LAB12** 항목을 클릭합니다.

7. **AZ500LAB12** 리소스 그룹 블레이드의 리소스 목록에서 새로 만든 스토리지 계정을 나타내는 항목을 클릭합니다.

8. 스토리지 계정 **개요** 블레이드에서 **데이터 스토리지** 탭 아래의 **파일 공유**를 클릭한 다음 **+ 파일 공유**를 클릭합니다.

9. **새 파일 공유** 블레이드에서 다음 설정을 지정합니다.

    |설정|값|
    |---|---|
    |이름|**my-file-share**|

10. **새 파일 공유** 블레이드에서 **만들기**를 클릭합니다.

    >**참고**: 이제 Azure 파일 공유에 드라이브 매핑을 만드는 PowerShell 스크립트를 검색하고 기록하세요. 

11. 스토리지 계정 블레이드의 파일 공유 목록에서 **내 파일 공유**를 클릭하세요.

12. **내 파일 공유** 블레이드에서 **연결**을 클릭하세요.

13. **연결** 블레이드의 **Windows** 탭에서 파일 공유에 Z 드라이브 매핑을 만드는 PowerShell 스크립트를 복사하세요. 

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Record this script. You will need this in a later in this lab in order to map the file share from the Azure virtual machine on the <bpt id="p1">**</bpt>Private<ept id="p1">**</ept> subnet.

14. 스토리지 계정 블레이드로 다시 이동하여 **보안 + 네트워킹** 섹션에서 **네트워킹**을 클릭합니다.

15. **방화벽 및 가상 네트워크** 블레이드 아래에서 **선택한 가상 네트워크 및 IP 주소에서 사용** 옵션을 선택하고 **+ 기존 가상 네트워크 추가** 링크를 클릭합니다. 

16. **네트워크 추가** 블레이드에서 다음 설정을 지정합니다.

    |설정|값|
    |---|---|
    |Subscription|이 랩에서 사용 중인 Azure 구독의 이름|
    |가상 네트워크|**myVirtualNetwork**|
    |서브넷|**개인**|

17. **네트워크 추가** 블레이드에서 **추가**를 클릭합니다. 

18. 스토리지 계정 블레이드로 돌아가서 **저장**을 클릭합니다.

    >**참고**: 이 시점에 랩에서 파일 공유와 함께 스토리지 계정, 네트워크 보안 그룹, 가상 네트워크를 구성했습니다. 

#### <a name="task-6-deploy-virtual-machines-into-the-designated-subnets"></a>작업 6: 지정된 서브넷에 가상 머신 배포

이 작업에서는 두 개의 가상 머신을 하나는 프라이빗 서브넷에, 다른 하나는 공용 서브넷에 만들게 됩니다. 

>**참고**: 첫 번째 가상 머신은 프라이빗 서브넷에 연결됩니다.

1. Azure Portal 페이지 위쪽의 **리소스, 서비스 및 문서 검색** 텍스트 상자에 **가상 머신**을 입력하고 **Enter** 키를 누릅니다.

2. **가상 머신** 창에서 **+ 만들기**를 클릭하고 드롭다운 목록에서 **+ Azure 가상 머신**을 클릭합니다.

3. **가상 머신 만들기** 블레이드의 **기본 사항** 탭에서 다음 설정을 지정합니다(나머지는 기본값을 그대로 사용).

    |설정|값|
    |---|---|
    |Subscription|이 랩에서 사용할 Azure 구독의 이름|
    |Resource group|**AZ500LAB12**|
    |가상 머신 이름|**myVmPrivate**|
    |지역|**(미국)미국 동부**|
    |이미지|**Windows Server 2022 Datacenter: Azure Edition - Gen 2**|
    |사용자 이름|**localadmin**|
    |암호|**랩 04 > 연습 1 > 작업 1 > 9단계에서 만든 개인 암호를 사용하세요.**|
    |공용 인바운드 포트|**없음**|
    |이미 Windows Server 라이선스가 있으신가요?|**아니요**|

    >**참고**: 공용 인바운드 포트의 경우 미리 만들어진 NSG에 의존합니다. 

4. **다음: 디스크 >** 를 클릭하고, **가상 머신 만들기** 블레이드의 **디스크** 탭에서 **OS 디스크 유형**을 **표준 HDD**로 설정하고 **다음: 네트워킹 >** 을 선택합니다.

5. **다음: 네트워킹 >** 을 클릭하고, **가상 머신 만들기** 블레이드의 **네트워킹** 탭에서 다음 설정을 지정합니다(다른 설정은 기본값으로 둡니다).

    |설정|값|
    |---|---|
    |가상 네트워크|**myVirtualNetwork**|
    |서브넷|**개인(10.0.1.0/24)**|
    |공용 IP|**(new)myVmPrivate-ip**|
    |NIC 네트워크 보안 그룹 추가|**없음**|

6. **다음: 관리 >** 를 클릭하고, **가상 머신 만들기** 블레이드의 **관리** 탭에서 기본 설정을 수락하고 **검토 + 만들기**를 클릭합니다.

7. **검토 + 만들기** 블레이드에서 유효성 검사가 성공했는지 확인한 다음 **만들기**를 클릭합니다.

    >**참고**: 두 번째 가상 머신은 공용 서브넷에 연결됩니다.

8. **가상 머신** 창에서 **+ 추가**를 클릭하고 드롭다운 목록에서 **+ Azure 가상 머신**을 클릭합니다.

9. **가상 머신 만들기** 블레이드의 **기본 사항** 탭에서 다음 설정을 지정합니다(나머지는 기본값을 그대로 사용).

    |설정|값|
    |---|---|
    |Subscription|이 랩에서 사용할 Azure 구독의 이름|
    |Resource group|**AZ500LAB12**|
    |가상 머신 이름|**myVmPublic**|
    |지역|**(미국)미국 동부**|
    |이미지|**Windows Server 2022 Datacenter: Azure Edition - Gen 2**|
    |사용자 이름|**localadmin**|
    |암호|**랩 04 > 연습 1 > 작업 1 > 9단계에서 만든 개인 암호를 사용하세요.**|
    |공용 인바운드 포트|**없음**|
    |이미 Windows Server 라이선스가 있으신가요?|**아니요**|

    >**참고**: 공용 인바운드 포트의 경우 미리 만들어진 NSG에 의존합니다. 

10. **다음: 디스크 >** 를 클릭하고, **가상 머신 만들기** 블레이드의 **디스크** 탭에서 **OS 디스크 유형**을 **표준 HDD**로 설정하고 **다음: 네트워킹 >** 을 선택합니다.

11. **다음: 네트워킹 >** 을 클릭하고, **가상 머신 만들기** 블레이드의 **네트워킹** 탭에서 다음 설정을 지정합니다(다른 설정은 기본값으로 둡니다).

    |설정|값|
    |---|---|
    |가상 네트워크|**myVirtualNetwork**|
    |서브넷|**공용(10.0.0.0/24)**|
    |공용 IP|**(new)myVmPublic-ip**|
    |NIC 네트워크 보안 그룹 추가|**없음**|

12. **다음: 관리 >** 를 클릭하고, **가상 머신 만들기** 블레이드의 **관리** 탭에서 기본 설정을 수락하고 **검토 + 만들기**를 클릭합니다.

13. **검토 + 만들기** 블레이드에서 유효성 검사가 성공했는지 확인한 다음 **만들기**를 클릭합니다.

    >**참고**: **myVMPrivate** Azure VM의  배포가 완료되면 다음 작업으로 진행할 수 있습니다.

#### <a name="task-7-test-the-storage-connection-from-the-private-subnet-to-confirm-that-access-is-allowed"></a>작업 7: 프라이빗 서브넷에서 스토리지 연결을 테스트하여 액세스가 허용되는지 확인

이 작업에서는 원격 데스크톱을 통해 myVMPrivate 가상 머신에 연결하고 드라이브를 파일 공유에 매핑합니다. 

1. **가상 머신** 블레이드로 돌아갑니다. 

2. **가상 머신** 블레이드에서 **myVMPrivate** 항목을 클릭합니다.

3. **myVMPrivate** 블레이드에서 **연결**을 클릭하고 드롭다운 메뉴에서 **RDP**를 클릭합니다. 

4. Click <bpt id="p1">**</bpt>Download RDP File<ept id="p1">**</ept> and use it to connect to the <bpt id="p2">**</bpt>myVMPrivate<ept id="p2">**</ept> Azure VM via Remote Desktop. When prompted to authenticate, provide the following credntials:

    |설정|값|
    |---|---|
    |사용자 이름|**localadmin**|
    |암호|**랩 04 > 연습 1 > 작업 1 > 9단계에서 만든 개인 암호를 사용하세요.**|

    >**참고**: 원격 데스크톱 세션이 열리고 서버 관리자가 로드될 때까지 기다립니다.

    >**참고**: 이제 Windows Server 2022 컴퓨터 원격 데스크톱 세션 내에서 드라이브 Z를 Azure 파일 공유에 매핑합니다.

5. **myVMPrivate**의 원격 데스크톱 세션 내에서 **시작**을 클릭한 다음, **Windows PowerShell ISE**를 클릭합니다.

6. Within the <bpt id="p1">**</bpt>Windows PowerShell ISE<ept id="p1">**</ept> window, open the <bpt id="p2">**</bpt>Script<ept id="p2">**</ept> pane, then paste and run the PowerShell script that you recorded earlier in this lab. The script has the following format:

    ```powershell
    $connectTestResult = Test-NetConnection -ComputerName <storage_account_name>.file.core.windows.net -Port 445
    if ($connectTestResult.TcpTestSucceeded) {
       # Save the password so the drive will persist on reboot
       cmd.exe /C "cmdkey /add:`"<storage_account_name>.file.core.windows.net`" /user:`"localhost\<storage_account_name>`"  /pass:`"<storage_account_key>`""
       # Mount the drive
       New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage_account_name>.file.core.windows.net\my-file-share" -Persist
    } else {
       Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN, Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
    }
    ```
    >**참고**: `<storage-account-name>` 자리 표시자는 파일 공유를 호스팅하는 스토리지 계정의 이름을 나타내고, `<storage_account_name>` 자리 표시자는 기본 키를 나타냅니다.

7. 파일 탐색기를 시작하고 Z: 드라이브 매핑이 성공적으로 만들어졌는지 확인합니다.

8. 다음으로 **Windows PowerShell ISE** 콘솔 창에서 다음을 실행하여 가상 머신에 인터넷에 대한 아웃바운드 연결이 없는지 확인합니다.

    ```powershell
    Test-NetConnection -ComputerName www.bing.com -Port 80
    ```

    >**참고**: 프라이빗 서브넷에 연결된 네트워크 보안 그룹은 인터넷에 대한 아웃바운드 액세스를 허용하지 않으므로 테스트에 실패합니다.

9. **myVmPrivate** Azure VM에 대해 원격 데스크톱 세션을 종료합니다.

    >**참고**: 이 시점에서 프라이빗 서브넷의 가상 머신이 스토리지 계정에 액세스할 수 있음을 확인했습니다. 

####  <a name="task-8-test-the-storage-connection-from-the-public-subnet-to-confirm-that-access-is-denied"></a>작업 8: 공용 서브넷에서 스토리지 연결을 테스트하여 액세스가 거부되는지 확인 

1. **가상 머신** 블레이드로 돌아갑니다. 

2. **가상 머신** 블레이드에서 **myVMPublic** 항목을 클릭합니다.

3. **myVMPublic** 블레이드에서 **연결**을 클릭하고 드롭다운 메뉴에서 **RDP**를 클릭합니다. 

4. Click <bpt id="p1">**</bpt>Download RDP File<ept id="p1">**</ept> and use it to connect to the <bpt id="p2">**</bpt>myVMPublic<ept id="p2">**</ept> Azure VM via Remote Desktop. When prompted to authenticate, provide the following credntials:

    |설정|값|
    |---|---|
    |사용자 이름|**localadmin**|
    |암호|**랩 04 > 연습 1 > 작업 1 > 9단계에서 만든 개인 암호를 사용하세요.**|

    >**참고**: 원격 데스크톱 세션이 열리고 서버 관리자가 로드될 때까지 기다립니다.

    >**참고**: 이제 Windows Server 2022 컴퓨터 원격 데스크톱 세션 내에서 드라이브 Z를 Azure 파일 공유에 매핑합니다.

5. **myVMPublic**의 원격 데스크톱 세션 내에서 **시작**을 클릭한 다음, **Windows PowerShell ISE**를 클릭합니다.

6. **Windows PowerShell ISE** 창 내에서 **스크립트** 창을 열고  **myVMPrivate** Azure VM에 대한 원격 데스크톱 세션 내에서 실행한 동일한 PowerShell 스크립트를 붙여넣고 실행합니다.

    >**참고**: 이번에는 **New-PSDrive: 액세스가 거부되었습니다** 오류가 표시됩니다. 

    >이 랩의 모든 리소스에 대해 **미국 동부** 지역을 사용하고 있습니다.

7. 다음으로 **Windows PowerShell ISE** 콘솔 창에서 다음을 실행하여 가상 머신에 인터넷에 대한 아웃바운드 연결이 있는지 확인합니다. 

    ```powershell
    Test-NetConnection -ComputerName www.bing.com -Port 80
    ```

    >**참고**: 공용 서브넷에서 인터넷을 거부하는 아웃바운드 보안 규칙이 없으므로 테스트가 성공합니다.

8. **myVmPublic** Azure VM에 대해 원격 데스크톱 세션을 종료합니다.

    >**참고**: 이 시점에서 공용 서브넷의 가상 머신이 스토리지 계정에 액세스할 수 없지만 인터넷에는 액세스할 수 있음을 확인했습니다.


**리소스 정리**

> 이 지역을 수업에 사용할 것인지 강사에게 확인합니다. 

1. Open the Cloud Shell by clicking the first icon in the top right of the Azure Portal. If prompted, select <bpt id="p1">**</bpt>PowerShell<ept id="p1">**</ept> and <bpt id="p2">**</bpt>Create storage<ept id="p2">**</ept>.

2. Cloud Shell 창의 왼쪽 위 모서리에 있는 드롭다운 메뉴에서 **PowerShell**이 선택되었는지 확인합니다.

3. Cloud Shell 창 내의 PowerShell 세션에서 다음을 실행하여 이 랩에서 만든 리소스 그룹을 제거합니다.
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB12" -Force -AsJob
    ```

4.  **Cloud Shell** 창을 닫습니다. 

