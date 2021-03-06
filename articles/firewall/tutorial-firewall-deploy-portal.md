---
title: Azure Portal을 사용하여 Azure 방화벽 배포 및 구성
description: 이 자습서에서는 Azure Portal을 사용하여 Azure Firewall을 배포하고 구성하는 방법을 알아봅니다.
services: firewall
author: vhorne
manager: jpconnock
ms.service: firewall
ms.topic: tutorial
ms.date: 7/11/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 7a127f53985f39913c83958be1f1730c64b33a76
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39001954"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-using-the-azure-portal"></a>자습서: Azure Portal을 사용하여 Azure Firewall 배포 및 구성

Azure Firewall에는 아웃바운드 액세스를 제어하는 두 가지 규칙 형식이 있습니다.

- **응용 프로그램 규칙**

   서브넷에서 액세스할 수 있는 정규화된 도메인 이름(FQDN)을 구성할 수 있습니다. 예를 들어 서브넷에서 *github.com*에 대한 액세스를 허용할 수 있습니다.
- **네트워크 규칙**

   원본 주소, 프로토콜, 대상 포트 및 대상 주소를 포함하는 규칙을 구성할 수 있습니다. 예를 들어 서브넷에서 DNS 서버의 IP 주소로 포트 53(DNS) 트래픽을 허용하는 규칙을 만들 수 있습니다.

네트워크 트래픽은 서브넷 기본 게이트웨이처럼 방화벽에 네트워크 트래픽을 라우팅할 경우 구성된 방화벽 규칙에 종속됩니다.

응용 프로그램 및 네트워크 규칙은 *규칙 컬렉션*에 저장됩니다. 규칙 컬렉션은 동일한 작업 및 우선 순위를 공유하는 규칙 목록입니다.  네트워크 규칙 컬렉션은 네트워크 규칙 목록이며, 응용 프로그램 규칙 컬렉션은 응용 프로그램 규칙 목록입니다.

네트워크 규칙 컬렉션은 항상 응용 프로그램 규칙 컬렉션에 앞서 처리됩니다. 모든 규칙이 종료되므로 네트워크 규칙 컬렉션에서 일치가 발견되는 경우 세션에 대한 다음과 같은 응용 프로그램 규칙 컬렉션은 처리되지 않습니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 테스트 네트워크 환경 설정
> * 방화벽 배포
> * 기본 경로 만들기
> * 응용 프로그램 규칙 구성
> * 네트워크 규칙 구성
> * 방화벽 테스트



Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [firewall-preview-notice](../../includes/firewall-preview-notice.md)]

Azure Firewall 문서의 예제에서는 이미 Azure Firewall 공개 미리 보기를 사용하도록 설정했다고 가정합니다. 자세한 내용은 [Azure Firewall 공개 미리 보기 사용하도록 설정](public-preview.md)을 참조합니다.

이 자습서에서는 세 개의 서브넷을 사용하여 단일 VNet을 만듭니다.
- **FW-SN** - 방화벽은 이 서브넷에 있습니다.
- **워크로드-SN** - 워크로드 서버는 이 서브넷에 있습니다. 이 서브넷의 네트워크 트래픽은 방화벽을 통해 이동합니다.
- **점프-SN** - The "점프" 서버는 이 서브넷에 있습니다. 점프 서버에는 원격 데스크톱을 사용하여 연결할 수 있는 공용 IP 주소가 있습니다. 여기에서 다음 워크로드 서버에 연결할 수 있습니다(다른 원격 데스크톱을 사용하여).

![자습서 네트워크 인프라](media/tutorial-firewall-rules-portal/Tutorial_network.png)

이 자습서에서는 간편한 배포를 위해 간소화 된 네트워크 구성을 사용합니다. 프로덕션 배포에서는 방화벽이 자체 VNet에 있으며 워크로드 서버가 하나 이상의 서브넷이 있는 동일한 지역의 피어링된 Vnet에 있는 [허브 및 스포크 모델](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)이 좋습니다.



## <a name="set-up-the-network-environment"></a>네트워크 환경 설정
먼저 방화벽 배포에 필요한 리소스를 포함하는 리소스 그룹을 만듭니다. 그런 다음, VNet, 서브넷 및 테스트 서버를 만듭니다.

### <a name="create-a-resource-group"></a>리소스 그룹 만들기
1. [http://portal.azure.com](http://portal.azure.com)에서 Azure Portal에 로그인합니다.
1. Azure Portal 홈 페이지에서 **리소스 그룹** 및 **추가**를 차례로 클릭합니다.
2. **리소스 그룹 이름**에 **Test-FW-RG**를 입력합니다.
3. **구독**의 경우 사용자의 구독을 선택합니다.
4. **리소스 그룹 위치**의 경우 위치를 선택합니다. 만든 모든 후속 리소스는 동일한 위치에 있어야 합니다.
5. **만들기**를 클릭합니다.


### <a name="create-a-vnet"></a>VNet 만들기
1. Azure Portal 홈 페이지에서 **모든 서비스**를 클릭합니다.
2. **네트워킹**아래에서 **가상 네트워크**를 클릭합니다.
3. **추가**를 클릭합니다.
4. **이름**에 **Test-FW-VN**을 입력합니다.
5. **주소 공간**에 **10.0.0.0/16**을 입력합니다.
7. **구독**의 경우 사용자의 구독을 선택합니다.
8. **리소스 그룹**의 경우 **기존 항목 사용**을 선택한 후, **Test-FW-RG**를 선택합니다.
9. **위치**의 경우 전에 사용한 동일한 위치를 선택합니다.
10. **서브넷** 아래에서 **이름**에 **AzureFirewallSubnet**을 입력합니다.

    방화벽은 이 서브넷에 있고 해당 서브넷 이름은 AzureFirewallSubnet이 **되어야** 합니다.
11. **주소 범위**에 **10.0.1.0/24**를 입력합니다.
12. 다른 기본 설정을 사용한 다음, **만들기**를 클릭합니다.

### <a name="create-additional-subnets"></a>추가 서브넷 만들기

다음으로, 점프 서버에 대한 서브넷 및 워크로드 서버에 대한 서브넷을 만듭니다.

1. Azure Portal 홈 페이지에서 **리소스 그룹** 및 **Test-FW-RG**를 차례로 클릭합니다.
2. **Test-FW-VN** 가상 네트워크를 클릭합니다.
3. **서브넷**을 클릭한 다음, **+서브넷**을 클릭합니다.
4. **이름**에 **워크 로드-SN**을 입력합니다.
5. **주소 범위**에 **10.0.2.0/24**를 입력합니다.
6. **확인**을 클릭합니다.

주소 범위 **10.0.3.0/24**의 **점프 SN**이라는 다른 서브넷을 만듭니다.

### <a name="create-virtual-machines"></a>가상 머신 만들기

이제 점프 및 워크로드 가상 머신을 만들어 적절한 서브넷에 배치합니다.

1. Azure Portal 홈 페이지에서 **모든 서비스**를 클릭합니다.
2. **계산** 아래에서 **가상 머신**을 클릭합니다.
3. **추가**, **Windows Server**, **Windows Server 2016 Datacenter** 및 **만들기**를 차례로 클릭합니다.

**기본 사항**

1. **이름**에 **Srv-Jump**를 입력합니다.
5. 사용자 이름 및 암호를 입력합니다.
6. **구독**의 경우 사용자의 구독을 선택합니다.
7. **리소스 그룹**의 경우 **기존 항목 사용**을 선택한 다음, **Test-FW-RG**를 클릭합니다.
8. **위치**의 경우 전에 사용한 동일한 위치를 선택합니다.
9. **확인**을 클릭합니다.

**크기**

1. Windows Server를 실행하는 테스트 가상 머신에 대해 적절한 크기를 선택합니다. 예를 들어 **B2ms**(8GB RAM, 16GB 저장소).
2. **선택**을 클릭합니다.

**설정**

1. **네트워크** 아래에서 **가상 네트워크**에 대해 **Test-FW-VN**을 선택합니다.
2. **서브넷**에 대해 **Jump-SN**을 선택합니다.
3. **공용 인바운드 포트 선택**의 경우 **RDP(3389)** 를 선택합니다. 

    공용 IP 주소에 액세스를 제한하고 싶지만 포트 3389를 열어야 하므로 점프 서버에 원격 데스크톱을 연결할 수 없습니다. 
2. 다른 기본 설정을 그대로 적용하고 **확인**을 클릭합니다.

**요약**

요약을 검토한 다음, **만들기**를 클릭합니다. 이 설정은 완료하는 데 몇 분 정도 걸립니다.

**Srv-Work**라는 다른 가상 머신을 만들려면 이 프로세스를 반복합니다.

다음 표의 정보를 사용하여 Srv-Work 가상 머신에 대한 **설정**을 구성합니다. 나머지 구성은 Srv-Jump 가상 머신과 동일합니다.


|설정  |값  |
|---------|---------|
|서브넷|워크로드-SN|
|공용 IP 주소|없음|
|공용 인바운드 포트 선택|공용 인바운드 포트 없음|


## <a name="deploy-the-firewall"></a>방화벽 배포

1. 포털 홈 페이지에서 **리소스 만들기**를 클릭합니다.
2. **네트워킹**을 클릭하고, **추천** 후에 **모두 보기**를 클릭합니다.
3. **방화벽**을 클릭한 다음, **만들기**를 클릭합니다. 
4. **방화벽 만들기** 페이지에서 다음 표를 사용하여 방화벽을 구성합니다.
   
   |설정  |값  |
   |---------|---------|
   |Name     |Test-FW01|
   |구독     |\<구독\>|
   |리소스 그룹     |**기존 항목 사용**: Test-FW-RG |
   |위치     |전에 사용한 동일한 위치 선택|
   |가상 네트워크 선택     |**기존 항목 사용**: Test-FW-VN|
   |공용 IP 주소     |새로 만들기|

2. **검토 + 만들기**를 클릭합니다.
3. 요약을 검토한 다음, **만들기**를 클릭하여 방화벽을 만듭니다.

   이 설정은 배포하는 데 몇 분 정도 걸립니다.
4. 배포가 완료되면 **Test-FW-RG** 리소스 그룹으로 이동하고 **Test-FW01** 방화벽을 클릭합니다.
6. 개인 IP 주소를 참고합니다. 기본 경로를 만들 때 나중에 사용할 수 있습니다.

[//]: # (방화벽에 대한 개인 IP를 확인해야 합니다.)

## <a name="create-a-default-route"></a>기본 경로 만들기

**Workload-SN** 서브넷의 경우 방화벽을 통과하도록 아웃바운드 기본 경로를 구성합니다.

1. Azure Portal 홈 페이지에서 **모든 서비스**를 클릭합니다.
2. **네트워킹** 아래에서 **경로 테이블**을 클릭합니다.
3. **추가**를 클릭합니다.
4. **이름**에 **Firewall-route**를 입력합니다.
5. **구독**의 경우 사용자의 구독을 선택합니다.
6. **리소스 그룹**의 경우 **기존 항목 사용**을 선택하고, **Test-FW-RG**를 선택합니다.
7. **위치**의 경우 전에 사용한 동일한 위치를 선택합니다.
8. **만들기**를 클릭합니다.
9. **새로 고침**를 클릭한 다음, **Firewall-route** 경로 테이블을 클릭합니다.
10. **서브넷**을 클릭한 다음, **연결**을 클릭합니다.
11. **가상 네트워크**를 클릭한 다음, **Test-FW-VN**을 선택합니다.
12. **서브넷**의 경우 **Workload-SN**을 클릭합니다.
13. **확인**을 클릭합니다.
14. **경로**를 클릭한 다음, **추가**를 클릭합니다.
15. **경로 이름**에 **FW-DG**를 입력합니다.
16. **주소 접두사**에 **0.0.0.0/0**을 입력합니다.
17. **다음 홉 형식**의 경우 **가상 어플라이언스**를 선택합니다.

    Azure Firewall은 실제로 관리되는 서비스이지만 가상 어플라이언스는 이 상황에서 작동합니다.
1. **다음 홉 주소**에 이전에 적어둔 방화벽에 대한 개인 IP 주소를 입력합니다.
2. **확인**을 클릭합니다.


## <a name="configure-application-rules"></a>응용 프로그램 규칙 구성


1. **Test-FW-RG**를 열고 **Test-FW01** 방화벽을 클릭합니다.
1. **Test-FW01** 페이지의 **설정**에서 **규칙**을 클릭합니다.
2. **응용 프로그램 규칙 컬렉션 추가**를 클릭합니다.
3. **이름**에 **App-Coll01**를 입력합니다.
1. **우선 순위**에 **200**을 입력합니다.
2. **동작**에 대해 **허용**을 선택합니다.

6. **규칙** 아래에서 **이름**에 **AllowGH**를 입력합니다.
7. **원본 주소**에 **10.0.2.0/24**를 입력합니다.
8. **Protocol:port**에 **http, https**를 입력합니다. 
9. **대상 FQDN**에 **github.com** 입력
10. **추가**를 클릭합니다.

> [!NOTE]
> Azure Firewall은 기본적으로 허용되는 인프라 FQDN에 대한 기본 제공 규칙 컬렉션을 포함합니다. 이러한 FQDN은 플랫폼에 대해 특정적이며 다른 용도로 사용할 수 없습니다. 허용되는 인프라 FQDN은 다음과 같습니다.
>- 저장소 PIR(플랫폼 이미지 리포지토리)에 대한 액세스를 계산합니다.
>- 관리 디스크 상태 저장소 액세스.
>- Windows 진단
>
> 마지막으로 처리되는 *모두 거부* 응용 프로그램 규칙 컬렉션을 만들어 이 기본 제공 인프라 규칙 컬렉션을 재정의할 수 있습니다. 항상 인프라 규칙 컬렉션 전에 처리됩니다. 인프라 규칙 컬렉션에 없는 모든 항목은 기본적으로 거부됩니다.

## <a name="configure-network-rules"></a>네트워크 규칙 구성

1. **네트워크 규칙 컬렉션 추가**를 클릭합니다.
2. **이름**에 **Net-Coll01**을 입력합니다.
3. **우선 순위**에 **200**을 입력합니다.
4. **동작**에 대해 **허용**을 선택합니다.

6. **규칙** 아래에서 **이름**에 **AllowDNS**를 입력합니다.
8. **프로토콜**의 경우 **TCP**를 선택합니다.
9. **원본 주소**에 **10.0.2.0/24**를 입력합니다.
10. 대상 주소에 **209.244.0.3, 209.244.0.4**를 입력
11. **대상 포트**에 **53**을 입력합니다.
12. **추가**를 클릭합니다.

### <a name="change-the-primary-and-secondary-dns-address-for-the-srv-work-network-interface"></a>**Srv-Work** 네트워크 인터페이스에 대해 기본 및 보조 DNS 주소 변경

이 자습서에서는 테스트 목적으로 기본 및 보조 DNS 주소를 구성할 수 있습니다. 일반적인 Azure Firewall 요구 사항이 아닙니다. 

1. Azure Portal에서 **Test-FW-RG** 리소스 그룹을 엽니다.
2. **Srv-Work** 가상 머신에 대해 네트워크 인터페이스를 클릭합니다.
3. **설정**에서 **DNS 서버**를 클릭합니다.
4. **DNS 서버**에서 **사용자 지정**을 클릭합니다.
5. **DNS 서버 추가** 텍스트 상자에 **209.244.0.3**을 입력하고 다음 텍스트 상자에 **209.244.0.4**를 입력합니다.
6. **저장**을 클릭합니다. 
7. **Srv-Work** 가상 머신을 다시 시작합니다.


## <a name="test-the-firewall"></a>방화벽 테스트

1. Azure portal에서 **Srv-Work** 가상 머신에 대한 네트워크 설정을 검토하고 개인 IP 주소를 참고합니다.
2. 원격 데스크톱을 **Srv-Jump** 가상 머신에 연결하고, 여기에서 **Srv-Work** 개인 IP 주소까지 원격 데스크톱 연결을 엽니다.

5. Internet Explorer를 열고 http://github.com을 찾습니다.
6. 보안 경고에서 **확인** 및 **닫기**를 클릭합니다.

   GitHub 홈 페이지가 나타납니다.

7. http://www.msn.com로 이동합니다.

   방화벽에서 차단해야 합니다.

이제 방화벽 규칙이 작동하는지 확인했습니다.

- 다른 모든 FQDN이 아닌 허용된 FQDN 하나만 찾아볼 수 있습니다.
- 구성된 외부 DNS 서버를 사용하여 DNS 이름을 확인할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 모든 방화벽 관련 리소스를 삭제하려면 **Test-FW-RG** 리소스 그룹을 삭제합니다.


## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 네트워크 설정
> * 방화벽 만들기
> * 기본 경로 만들기
> * 응용 프로그램 및 네트워크 방화벽 규칙 구성
> * 방화벽 테스트

그런 다음, Azure Firewall 로그를 모니터링할 수 있습니다.

> [!div class="nextstepaction"]
> [자습서: Azure Firewall 로그 모니터링](./tutorial-diagnostics.md)
