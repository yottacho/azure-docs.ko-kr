---
title: Azure Stack 보안 제어 이해 | Microsoft Docs
description: 서비스 관리자로 Azure Stack에 적용 된 보안 제어에 알아봅니다
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: cccac19a-e1bf-4e36-8ac8-2228e8487646
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: mabrigg
ms.openlocfilehash: a3bd314a1df3c45c76b2e3a5acb31c1474d0fdf5
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39009476"
---
# <a name="azure-stack-infrastructure-security-posture"></a>Azure Stack 인프라의 보안 상태

*적용 대상: Azure Stack 통합 시스템*

하이브리드 클라우드를 사용 하는 것에 대 한 기본 드라이버 보안 고려 사항 및 규정 되입니다. Azure Stack가 이러한 시나리오에 맞게 디자인 하 고 Azure Stack을 도입 하는 경우 이미 있는 컨트롤을 이해 해야 합니다.

두 보안 포스 처 계층은 Azure Stack에서 공존합니다. 첫 번째 계층은 Azure Resource Manager 최대 하드웨어 구성 요소를 포함 하는 Azure Stack 인프라입니다. 첫 번째 계층 관리자 및 테 넌 트 포털을 포함합니다. 두 번째 계층 워크 로드를 생성, 배포 및 테 넌 트에서 관리 이루어져 있습니다. 두 번째 계층에는 가상 머신과 App Services 웹 사이트와 같은 항목이 포함 됩니다.

## <a name="security-approach"></a>보안 접근 방식

Azure Stack에 대 한 보안 태세는 최신 위협 으로부터 보호 하도록 설계 되었습니다 및 주요 규정 준수 표준에서 요구 사항을 충족 하도록 구축 되었습니다. 결과적으로, Azure Stack 인프라의 보안 태세는 두 가지 기본 요소에서 빌드됩니다.

 - **보안 위험 가정**  
에 집중 하는 시스템에 이미 위반 가정에서 시작 하며, *검색 및 위반의 영향을 제한* 및 공격을 방지 하려고 합니다. 
 - **기본적으로 강화**  
인프라는 잘 정의 된 하드웨어 및 소프트웨어, Azure Stack에서 실행 되므로 *사용 하도록 설정, 구성 및 보안 기능 모두의 유효성을 검사* 기본적으로 합니다.

Azure Stack 통합된 시스템으로 배달 되기, 때문에 Azure Stack 인프라의 보안 태세는 Microsoft에서 정의 됩니다. Azure에서와 마찬가지로 테 넌 트는 테 넌 트 워크 로드의 보안 태세를 정의 하는 일을 담당 합니다. 이 문서는 Azure Stack 인프라의 보안 상태에 기본 기술 자료를 제공합니다.

## <a name="data-at-rest-encryption"></a>암호화 미사용 데이터
모든 Azure Stack 인프라 및 테 넌 트 데이터는 Bitlocker를 사용 하 여 미사용 암호화 됩니다. 이 암호화에 대해 물리적 손실이 나 도난 Azure Stack 저장소 구성 요소 보호합니다. 

## <a name="data-in-transit-encryption"></a>데이터 전송 암호화
Azure Stack 인프라 구성 요소는 TLS 1.2를 사용 하 여 암호화 된 채널을 사용 하 여 통신 합니다. 암호화 인증서는 자체 인프라에 의해 관리 됩니다. 

REST 끝점 또는 Azure Stack 포털에 같은 모든 외부 인프라 끝점에 보안 통신을 위해 TLS 1.2를 지원 합니다. 이러한 끝점에 대 한 암호화 인증서에 다른 공급 업체 또는 엔터프라이즈 인증 기관에서 제공 되어야 합니다. 

이러한 외부 끝점에 대해 자체 서명 된 인증서를 사용할 수 있지만 Microsoft는 사용에 대 한 적극 권장 합니다. 

## <a name="secret-management"></a>비밀 관리
Azure Stack 인프라 작동 하려면 다양 한 암호와 같은 암호를 사용 합니다. 대부분의는 자동으로 24 시간 마다 회전 하는 그룹 관리 서비스 계정은 이기 때문에 자주 회전 됩니다.

그룹 관리 서비스 계정은 권한 있는 끝점에서 스크립트를 사용 하 여 수동으로 회전할 수 없는 나머지 암호입니다.

## <a name="code-integrity"></a>코드 무결성
Azure Stack 최신 Windows Server 2016을 사용 하면 보안 기능입니다. 그 중 하나는 Windows Defender Device Guard, 응용 프로그램 허용 목록을 제공 하 고 확인 하는 권한 있는 Azure Stack 인프라 내에서 코드 실행 합니다. 

Microsoft 또는 OEM 파트너에서 권한 있는 코드 서명 및 Microsoft에서 정의한 정책에 지정 된 허용 된 소프트웨어 목록에 포함 되어 있습니다. 즉, Azure Stack 인프라에서 실행 되도록 승인 된 소프트웨어만 실행할 수 있습니다. 승인 되지 않은 코드를 실행 하려는 모든 시도 차단 하 고 감사 생성 됩니다.

또한 Device Guard 정책을 Azure Stack 인프라에서 실행에서 타사 에이전트 또는 소프트웨어를 방지 합니다.

## <a name="credential-guard"></a>Credential Guard
Azure Stack의 또 다른 Windows Server 2016 보안 기능-Pass-the-hash 및 티켓을 전달 공격 으로부터 Azure Stack 인프라 자격 증명을 보호 하는 데 사용 되는 Windows Defender Credential Guard 합니다.

## <a name="antimalware"></a>맬웨어 방지
Azure Stack (Hyper-v 호스트 및 가상 머신)에서 모든 구성 요소는 Windows Defender 바이러스 백신을 사용 하 여 보호 됩니다.

연결 된 시나리오에서 바이러스 백신 정 및 엔진 업데이트에는 하루에 여러 번 적용 됩니다. 연결이 끊긴된 시나리오에서 맬웨어 방지 업데이트는 월별 Azure Stack 업데이트의 일부로 적용 됩니다. 참조 [Azure Stack에 Windows Defender 바이러스 백신 업데이트](azure-stack-security-av.md) 자세한 내용은 합니다.

## <a name="constrained-administration-model"></a>제한 된 관리 모델
Azure Stack의 관리는 세 가지 진입점, 특정 목적을 사용 하 여 각각 사용 하 여 제어 됩니다. 
1. 합니다 [관리자 포털](azure-stack-manage-portals.md) 일상적인 관리 작업에 대 한 포인트 클릭 환경을 제공 합니다.
2. Azure Resource Manager PowerShell 및 Azure CLI에서 사용 하는 REST API를 통해 관리자 포털의 모든 관리 작업을 노출 합니다. 
3. 특정 하위 수준 작업에 대 한 데이터 센터 통합 또는 시나리오를 지원 합니다. 예를 들어, 라는 PowerShell 끝점을 노출 하는 Azure Stack [Privileged 끝점](azure-stack-privileged-endpoint.md)합니다. 이 끝점 집합에만 허용 목록에 cmdlet 노출 하 고 감사할 과도 하 게 합니다.

## <a name="network-controls"></a>네트워크 컨트롤
Azure Stack 인프라의 네트워크 액세스 제어 List(ACL) 여러 계층으로 제공 됩니다. Acl은 인프라 구성 요소에 대 한 무단된 액세스를 방지 하 고 인프라 통신 기능을 위해 필요한 경로만을 제한 합니다. 

네트워크 Acl은 세 가지 계층에 적용 됩니다.
1.  Top of Rack
2.  소프트웨어 정의 네트워크
3.  호스트 및 VM 운영 체제 방화벽

## <a name="next-steps"></a>다음 단계

- [Azure Stack에서 암호를 회전 하는 방법에 알아봅니다.](azure-stack-rotate-secrets.md)
