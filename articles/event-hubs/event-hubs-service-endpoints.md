---
title: Azure Event Hubs에 대한 가상 네트워크 서비스 엔드포인트 및 규칙 | Microsoft Docs
description: 가상 네트워크에 Microsoft.EventHub 서비스 끝점을 추가합니다.
services: event-hubs
documentationcenter: ''
author: clemensv
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.date: 06/26/2018
ms.author: clemensv
ms.openlocfilehash: 3746c4b7d1b53d7522f317fd2e349d31ba77f406
ms.sourcegitcommit: dc646da9fbefcc06c0e11c6a358724b42abb1438
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39136341"
---
# <a name="use-virtual-network-service-endpoints-with-azure-event-hubs"></a>Azure Event Hubs에서 Virtual Network 서비스 엔드포인트 사용

[VNet(Virtual Network) 서비스 엔드포인트][vnet-sep]와 Event Hubs를 통합하면 양쪽 엔드에서 네트워크 트래픽 경로를 보호하여 가상 네트워크에 바인딩된 가상 머신과 같은 워크로드의 메시징 기능에 대한 액세스를 보호할 수 있습니다. 

적어도 하나의 가상 네트워크 서브넷 서비스 엔드포인트에 바인딩되도록 구성하면 해당하는 Event Hubs 네임스페이스는 권한이 부여된 가상 네트워크를 제외한 곳의 트래픽을 더 이상 허용하지 않습니다. 가상 네트워크 큐브 뷰에서 Event Hubs 네임스페이스를 서비스 엔드포인트에 바인딩하면 가상 네트워크 서브넷에서 메시징 서비스로 격리된 네트워킹 터널을 구성합니다.

메시징 서비스 엔드포인트의 관찰 가능한 네트워크 주소가 공용 IP 범위에 있음에도 서브넷에 바인딩된 워크로드와 해당하는 Event Hubs 네임스페이스 간에 격리된 개인 관계가 생성됩니다.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>VNet 통합에서 사용하도록 설정한 고급 보안 시나리오 

엄격하게 분류된 보안이 필요한 솔루션은 일반적으로 해당 구획에 위치한 서비스 간에 통신 경로가 필요합니다. 해당 솔루션에서 가상 네트워크 서브넷은 분류된 서비스 간를 구분합니다.

TCP/IP에서 HTTPS를 수행하는 경로를 비롯한 구획 간의 즉시 IP 경로를 사용하면 네트워크 레이어에서 취약성을 악용할 위험이 있습니다. 메시징 서비스는 완전히 격리된 통신 경로를 제공합니다. 여기서 메시지는 항목을 전환할 때 디스크에도 작성됩니다. 동일한 Event Hubs 인스턴스에 바인딩된 두 개의 고유 가상 네트워크에 있는 워크로드는 메시지를 통해 효율적이고 안정적으로 통신할 수 있습니다. 이 동안에 해당 네트워크 격리 경계의 무결성은 지켜집니다.
 
즉, 보안이 중요한 클라우드 솔루션은 Azure에서 제공하는 업계 최고의 안정적이고 확장성 있는 비동기 메시징 기능에 액세스할 뿐만 아니라 메시징을 사용하여 HTTPS 및 기타 TLS 보안 소켓 프로토콜을 비롯한 보안 솔루션 구획(피어 간 통신 모드로 달성 가능한 수준보다 본질적으로 더 안전함) 간에 통신을 만들 수도 있습니다.

## <a name="bind-event-hubs-to-virtual-networks"></a>가상 네트워크에 Event Hubs 바인딩

*가상 네트워크 규칙*은 Azure Event Hubs 서버가 특정 가상 네트워크 서브넷의 연결을 수락할지 여부를 제어하는 방화벽 보안 기능입니다.

Virtual Networks에 Event Hubs를 바인딩하는 작업은 2단계 프로세스입니다. 먼저 Virtual Network 서브넷에 **Virtual Network 서비스 끝점**을 만들고, [서비스 끝점 개요][vnet-sep]에 설명된 대로 “Microsoft.EventHub”에 사용하도록 설정해야 합니다. 서비스 엔드포인트를 추가했다면 여기에 *가상 네트워크 규칙*을 사용하여 Event Hubs 네임스페이스를 바인딩합니다.

가상 네트워크 규칙은 가상 네트워크 서브넷을 사용하는 Event Hubs 네임스페이스의 명명된 연결입니다. 규칙이 있는 한 서브넷에 바인딩된 모든 워크로드는 Event Hubs 네임스페이스에 대한 액세스 권한이 부여됩니다. Event Hubs 자체는 아웃바운드 연결을 설정하지 않고, 액세스 권한을 가져올 필요도 없습니다. 따라서 이 규칙을 사용하여 서브넷에 대한 액세스 권한이 부여되지 않습니다.

### <a name="create-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿을 사용하여 가상 네트워크 규칙 만들기

다음과 같은 Resource Manager 템플릿을사용 하면 기존 Event Hubs 네임스페이스에 가상 네트워크 규칙을 추가할 수 있습니다.

템플릿 매개 변수:

* **namespaceName**: Event Hubs 네임스페이스입니다.
* **vnetRuleName**: 만들 Virtual Network 규칙의 이름입니다.
* **virtualNetworkingSubnetId**: 가상 네트워크 서브넷에 대해 정규화된 Resource Manager 경로입니다(예: 가상 네트워크 기본 서브넷의 경우 `subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default`).

```json
{  
   "$schema":"http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
   "contentVersion":"1.0.0.0",
   "parameters":{     
          "namespaceName":{  
             "type":"string",
             "metadata":{  
                "description":"Name of the namespace"
             }
          },
          "vnetRuleName":{  
             "type":"string",
             "metadata":{  
                "description":"Name of the Authorization rule"
             }
          },
          "virtualNetworkSubnetId":{  
             "type":"string",
             "metadata":{  
                "description":"subnet Azure Resource Manager ID"
             }
          }
      },
    "resources": [
        {
            "apiVersion": "2018-01-01-preview",
            "name": "[concat(parameters('namespaceName'), '/', parameters('vnetRuleName'))]",
            "type":"Microsoft.EventHub/namespaces/VirtualNetworkRules",         
            "properties": {             
                "virtualNetworkSubnetId": "[parameters('virtualNetworkSubnetId')]"  
            }
        } 
    ]
}
```

템플릿을 배포하려면 [Azure Resource Manager][lnk-deploy]에 대한 지침을 따르세요.

## <a name="next-steps"></a>다음 단계

가상 네트워크에 대한 자세한 내용은 다음 링크를 참조하세요.

- [Azure 가상 네트워크 서비스 끝점][vnet-sep]
- [Azure Event Hubs IP 필터링][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[ip-filtering]: event-hubs-ip-filtering.md