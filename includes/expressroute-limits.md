---
title: 포함 파일
description: 포함 파일
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 06/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6e0d2b881e00acc6b1fccd3196187309fc36cba2
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38755984"
---
#### <a name="expressroute-limits"></a>ExpressRoute 제한
구독당 ExpressRoute 리소스에는 다음과 같은 제한이 적용됩니다.

| 리소스 | 기본 제한 |
| --- | --- |
| 구독당 ExpressRoute 회로 |10 |
| ARM에 대한 구독당 지역별 ExpressRoute 회로 |10 |
| ExpressRoute 표준을 사용할 경우 Azure 개인 피어링에 대한 최대 경로 수 |4,000 |
| ExpressRoute 프리미엄 추가 기능을 사용할 경우 Azure 개인 피어링에 대한 최대 경로 수 |10000 |
| ExpressRoute 표준을 사용할 경우 Azure 공용 피어링에 대한 최대 경로 수 |200 |
| ExpressRoute 프리미엄 추가 기능을 사용할 경우 Azure 공용 피어링에 대한 최대 경로 수 |200 |
| ExpressRoute 표준을 사용할 경우 Azure Microsoft 피어링에 대한 최대 경로 수 |200 |
| ExpressRoute 프리미엄 추가 기능을 사용할 경우 Azure Microsoft 피어링에 대한 최대 경로 수 |200 |
| 서로 다른 피어링 위치의 동일한 가상 네트워크에 연결된 ExpressRoute 회로의 최대 수 |4 |
| ExpressRoute 회로당 허용되는 가상 네트워크 링크 수 |아래 표 참조 |

#### <a name="number-of-virtual-networks-per-expressroute-circuit"></a>ExpressRoute 회로당 Virtual Network 수
| **회로 크기** | **표준을 사용할 경우 VNet 링크 수** | **프리미엄 추가 기능을 사용할 경우 VNet 링크 수** |
| --- | --- | --- |
| 50Mbps |10 |20 |
| 100Mbps |10 |25 |
| 200Mbps |10 |25 |
| 500Mbps |10 |40 |
| 1Gbps |10 |50 |
| 2Gbps |10 |60 |
| 5Gbps |10 |75 |
| 10Gbps |10 |100 |

