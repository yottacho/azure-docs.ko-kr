---
title: 앱별 크기 조정을 사용한 Azure App Service의 고밀도 호스팅 | Microsoft Docs
description: Azure App Service의 고밀도 호스팅
author: btardif
manager: erikre
editor: ''
services: app-service\web
documentationcenter: ''
ms.assetid: a903cb78-4927-47b0-8427-56412c4e3e64
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 01/22/2018
ms.author: byvinyal
ms.openlocfilehash: 97e1efe34417c3bf2f23801b2112b718f55d3416
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/26/2018
ms.locfileid: "36962407"
---
# <a name="high-density-hosting-on-azure-app-service-using-per-app-scaling"></a>앱별 크기 조정을 사용한 Azure App Service의 고밀도 호스팅
기본적으로 실행하는 [App Service 계획](azure-web-sites-web-hosting-plans-in-depth-overview.md)의 크기 조정을 통해 App Service 앱을 크기 조정합니다. 여러 앱이 같은 App Service 계획에서 실행되는 경우 각 스케일 아웃 인스턴스는 계획의 모든 앱을 실행합니다.

*앱별 크기 조정*을 App Service 계획 수준에서 사용하도록 설정할 수 있습니다. 호스팅하는 App Service 계획에서 독립적으로 앱을 크기 조정합니다. 이러한 방식으로 App Service 계획은 10개의 인스턴스로 확장될 수 있지만 앱은 5개만 사용하도록 설정될 수 있습니다.

> [!NOTE]
> 앱별 크기 조정은 **표준**, **프리미엄**, **프리미엄 V2** 및 **격리** 가격 책정 계층에서만 사용할 수 있습니다.
>

## <a name="per-app-scaling-using-powershell"></a>PowerShell을 사용하여 앱 크기 조정당

```-perSiteScaling $true``` 특성을 ```New-AzureRmAppServicePlan``` commandlet에 전달하여 앱별 크기 조정으로 계획을 만듭니다.

```powershell
New-AzureRmAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan `
                            -Location $Location `
                            -Tier Premium -WorkerSize Small `
                            -NumberofWorkers 5 -PerSiteScaling $true
```

앱별 크기 조정으로 기존 App Service 계획을 업데이트하려면 다음을 수행합니다. 

- 대상 계획 가져오기```Get-AzureRmAppServicePlan```
- 속성 로컬 수정```$newASP.PerSiteScaling = $true```
- 변경 내용을 Azure에 다시 게시```Set-AzureRmAppServicePlan``` 

```powershell
# Get the new App Service Plan and modify the "PerSiteScaling" property.
$newASP = Get-AzureRmAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan
$newASP

#Modify the local copy to use "PerSiteScaling" property.
$newASP.PerSiteScaling = $true
$newASP
    
#Post updated app service plan back to azure
Set-AzureRmAppServicePlan $newASP
```

앱 수준에서 앱이 App Service 계획에 사용할 수 있는 인스턴스 수를 구성합니다.

아래 예제에서 이 앱은 기본 앱 서비스 계획이 규모를 확장하는 인스턴스 수와 상관없이 2개 인스턴스로 제한됩니다.

```powershell
# Get the app we want to configure to use "PerSiteScaling"
$newapp = Get-AzureRmWebApp -ResourceGroupName $ResourceGroup -Name $webapp
    
# Modify the NumberOfWorkers setting to the desired value.
$newapp.SiteConfig.NumberOfWorkers = 2
    
# Post updated app back to azure
Set-AzureRmWebApp $newapp
```

> [!IMPORTANT]
> `$newapp.SiteConfig.NumberOfWorkers`는 `$newapp.MaxNumberOfWorkers`와 다릅니다. 앱별 크기 조정은 `$newapp.SiteConfig.NumberOfWorkers`를 사용하여 앱의 크기 조정 특성을 결정합니다.

## <a name="per-app-scaling-using-azure-resource-manager"></a>Azure Resource Manager를 사용하여 앱별 크기 조정

다음 Azure Resource Manager 템플릿은 다음을 만듭니다.

- 10개 인스턴스로 규모를 확장하는 App Service 계획
- 최대 5개 인스턴스로 확장하도록 구성된 앱

App Service 계획은 **PerSiteScaling** 속성을 true(`"perSiteScaling": true`)로 설정합니다. 앱은 **작업자 수**를 5(`"properties": { "numberOfWorkers": "5" }`)로 설정합니다.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters":{
        "appServicePlanName": { "type": "string" },
        "appName": { "type": "string" }
        },
    "resources": [
    {
        "comments": "App Service Plan with per site perSiteScaling = true",
        "type": "Microsoft.Web/serverFarms",
        "sku": {
            "name": "P1",
            "tier": "Premium",
            "size": "P1",
            "family": "P",
            "capacity": 10
            },
        "name": "[parameters('appServicePlanName')]",
        "apiVersion": "2015-08-01",
        "location": "West US",
        "properties": {
            "name": "[parameters('appServicePlanName')]",
            "perSiteScaling": true
        }
    },
    {
        "type": "Microsoft.Web/sites",
        "name": "[parameters('appName')]",
        "apiVersion": "2015-08-01-preview",
        "location": "West US",
        "dependsOn": [ "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" ],
        "properties": { "serverFarmId": "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" },
        "resources": [ {
                "comments": "",
                "type": "config",
                "name": "web",
                "apiVersion": "2015-08-01",
                "location": "West US",
                "dependsOn": [ "[resourceId('Microsoft.Web/Sites', parameters('appName'))]" ],
                "properties": { "numberOfWorkers": "5" }
            } ]
        }]
}
```

## <a name="recommended-configuration-for-high-density-hosting"></a>고밀도 호스팅에 대한 권장된 구성
앱별 크기 조정은 전역 Azure 지역 및 [App Service Environment](environment/app-service-app-service-environment-intro.md) 모두에서 사용할 수 있는 기능입니다. 그러나 권장되는 전략은 해당 고급 기능 및 큰 용량의 풀을 활용하도록 App Service Environment를 사용하는 것입니다.  

앱에 대해 고밀도 호스팅을 구성하려면 이 단계를 수행합니다.

1. App Service 환경을 구성하고 고밀도 호스팅 시나리오에 전용으로 사용되는 작업자 풀을 선택합니다.
1. 단일 App Service 계획을 만들고 확장하여 작업자 풀에서 모든 사용 가능한 용량을 사용합니다.
1. App Service 계획에서 `PerSiteScaling` 플래그를 true로 설정합니다.
1. 새 앱이 만들어지고 **1**로 설정된 **numberOfWorkers** 속성이 있는 해당 App Service 계획에 할당됩니다. 이 구성을 사용하면 작업자 풀에서 고밀도가 가능해 집니다.
1. 작업자 수는 앱마다 독립적으로 구성되어 필요에 따라 추가 리소스를 부여할 수 있습니다. 예: 
    - 사용량이 많은 앱은 해당 앱에 대한 더 많은 처리 용량을 갖도록 **numberOfWorkers**를 **3**으로 설정할 수 있습니다. 
    - 많이 사용되지 않는 앱은 **numberOfWorkers**를 **1**로 설정합니다.

## <a name="next-steps"></a>다음 단계

- [Azure App Service 계획의 포괄 개요](azure-web-sites-web-hosting-plans-in-depth-overview.md)
- [App Service Environment 소개](environment/app-service-app-service-environment-intro.md)
