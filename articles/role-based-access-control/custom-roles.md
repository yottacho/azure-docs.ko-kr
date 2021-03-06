---
title: Azure의 사용자 지정 역할 | Microsoft Docs
description: Azure에서 리소스에 대해 세분화된 액세스를 관리하기 위해 Azure RBAC(역할 기반 액세스 제어)를 사용하여 사용자 지정 역할을 정의하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: e4206ea9-52c3-47ee-af29-f6eef7566fa5
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/17/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d7554ef46289600cd15e4675a91f42a2cd735f18
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39112664"
---
# <a name="custom-roles-in-azure"></a>Azure의 사용자 지정 역할

[기본 제공 역할](built-in-roles.md)이 조직의 특정 요구 사항을 충족하지 않는 경우, 사용자 지정 역할을 만들 수 있습니다. 기본 제공 역할과 마찬가지로 구독, 리소스 그룹 및 리소스 범위에서 사용자 지정 역할을 사용자, 그룹 및 서비스 주체에 할당할 수 있습니다. 사용자 지정 역할은 Azure AD(Active Directory) 테넌트에 저장되며 구독에서 공유할 수 있습니다. 각 테넌트는 최대 2000개의 사용자 지정 역할을 가질 수 있습니다. Azure PowerShell, Azure CLI 또는 REST API를 사용하여 사용자 지정 역할을 만들 수 있습니다.

## <a name="custom-role-example"></a>사용자 지정 역할 예제

다음 예제에서는 Azure PowerShell을 사용하여 표시되는 가상 머신을 모니터링하고 다시 시작하는 사용자 지정 역할을 보여 줍니다.

```json
{
  "Name":  "Virtual Machine Operator",
  "Id":  "88888888-8888-8888-8888-888888888888",
  "IsCustom":  true,
  "Description":  "Can monitor and restart virtual machines.",
  "Actions":  [
                  "Microsoft.Storage/*/read",
                  "Microsoft.Network/*/read",
                  "Microsoft.Compute/*/read",
                  "Microsoft.Compute/virtualMachines/start/action",
                  "Microsoft.Compute/virtualMachines/restart/action",
                  "Microsoft.Authorization/*/read",
                  "Microsoft.Resources/subscriptions/resourceGroups/read",
                  "Microsoft.Insights/alertRules/*",
                  "Microsoft.Insights/diagnosticSettings/*",
                  "Microsoft.Support/*"
  ],
  "NotActions":  [

                 ],
  "DataActions":  [

                  ],
  "NotDataActions":  [

                     ],
  "AssignableScopes":  [
                           "/subscriptions/{subscriptionId1}",
                           "/subscriptions/{subscriptionId2}",
                           "/subscriptions/{subscriptionId3}"
                       ]
}
```

사용자 지정 역할이 만들어지면 Azure Portal에 주황색 리소스 아이콘이 표시됩니다.

![사용자 지정 역할 아이콘](./media/custom-roles/roles-custom-role-icon.png)

## <a name="steps-to-create-a-custom-role"></a>사용자 지정 역할을 만드는 단계

1. 필요한 권한 결정

    사용자 지정 역할을 만드는 경우 권한을 정의하는 데 사용할 수 있는 리소스 공급자 작업을 알고 있어야 합니다. 작업 목록을 보려면 [Get-AzureRMProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) 또는 [az provider operation list](/cli/azure/provider/operation#az-provider-operation-list) 명령을 사용할 수 있습니다.
    사용자 지정 역할에 대한 권한을 지정하려면 [역할 정의](role-definitions.md)의 `Actions` 또는 `NotActions` 속성에 해당 작업을 추가합니다. 데이터 작업이 있는 경우 이러한 작업은 `DataActions` 또는 `NotDataActions` 속성에 추가합니다.

2. 사용자 지정 역할 만들기

    Azure PowerShell 또는 Azure CLI를 사용하여 사용자 지정 역할을 만들 수 있습니다. 일반적으로 기존 기본 제공 역할로 시작한 다음, 필요에 따라 수정합니다. 그런 다음, [New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) 또는 [az role definition create](/cli/azure/role/definition#az-role-definition-create) 명령을 사용하여 사용자 지정 역할을 만듭니다. 사용자 지정 역할을 만들려면 [소유자](built-in-roles.md#owner) 또는 [사용자 액세스 관리자](built-in-roles.md#user-access-administrator)와 같이 모든 `AssignableScopes`에 대한 `Microsoft.Authorization/roleDefinitions/write` 권한이 있어야 합니다.

3. 사용자 지정 역할 테스트

    사용자 지정 역할이 있으면 해당 역할을 테스트하여 예상대로 작동하는지 확인해야 합니다. 조정해야 하는 경우 사용자 지정 역할을 업데이트할 수 있습니다.

## <a name="custom-role-properties"></a>사용자 지정 역할 속성

사용자 지정 역할의 속성은 다음과 같습니다.

| 자산 | 필수 | 형식 | 설명 |
| --- | --- | --- | --- |
| `Name` | 예 | 문자열 | 사용자 지정 역할의 표시 이름입니다. 테넌트에서 고유해야 합니다. 문자, 숫자, 공백 및 특수 문자를 포함할 수 있습니다. 최대 문자 수는 128자입니다. |
| `Id` | 예 | 문자열 | 사용자 지정 역할의 고유 ID입니다. Azure PowerShell 및 Azure CLI의 경우 이 ID는 새 역할을 만들 때 자동으로 생성됩니다. |
| `IsCustom` | 예 | 문자열 | 사용자 지정 역할인지 여부를 나타냅니다. 사용자 지정 역할인 경우 `true`로 설정합니다. |
| `Description` | 예 | 문자열 | 사용자 지정 역할에 대한 설명입니다. 문자, 숫자, 공백 및 특수 문자를 포함할 수 있습니다. 최대 문자 수는 1,024자입니다. |
| `Actions` | 예 | 문자열[] | 역할에서 수행할 수 있는 관리 작업을 지정하는 문자열 배열입니다. 자세한 내용은 [Actions](role-definitions.md#actions)를 참조하세요. |
| `NotActions` | 아니오 | 문자열[] | 허용된 `Actions`에서 제외되는 관리 작업을 지정하는 문자열 배열입니다. 자세한 내용은 [NotActions](role-definitions.md#notactions)를 참조하세요. |
| `DataActions` | 아니오 | 문자열[] | 역할에서 해당 개체 내의 데이터에 대해 수행할 수 있는 데이터 작업을 지정하는 문자열 배열입니다. 자세한 내용은 [DataActions(미리 보기)](role-definitions.md#dataactions-preview)를 참조하세요. |
| `NotDataActions` | 아니오 | 문자열[] | 허용된 `DataActions`에서 제외되는 데이터 작업을 지정하는 문자열 배열입니다. 자세한 내용은 [NotDataActions(미리 보기)](role-definitions.md#notdataactions-preview)를 참조하세요. |
| `AssignableScopes` | 예 | 문자열[] | 할당에 사용할 수 있는 사용자 지정 역할에 대한 범위를 지정하는 문자열 배열입니다. 루트 범위(`"/"`)로는 설정할 수 없습니다. 자세한 내용은 [AssignableScopes](role-definitions.md#assignablescopes)를 참조하세요. |

## <a name="assignablescopes-for-custom-roles"></a>사용자 지정 역할에 대한 AssignableScopes

기본 제공 역할과 마찬가지로, `AssignableScopes` 속성은 할당에 사용할 수 있는 역할에 대한 범위를 지정합니다. 그러나 사용자 지정 역할에는 루트 범위(`"/"`)를 사용할 수 없습니다. 시도하면 권한 부여 오류가 발생합니다. 또한 사용자 지정 역할에 대한 `AssignableScopes` 속성은 사용자 지정 역할을 생성, 삭제, 수정 또는 표시할 수 있는 사용자도 제어합니다.

| Task | 작업 | 설명 |
| --- | --- | --- |
| 사용자 지정 역할 만들기/삭제 | `Microsoft.Authorization/ roleDefinition/write` | 사용자 지정 역할의 모든 `AssignableScopes`에 이 작업이 부여된 사용자는 해당 범위에서 사용할 사용자 지정 역할을 만들거나 삭제할 수 있습니다. 예를 들어 구독, 리소스 그룹 및 리소스의 [소유자](built-in-roles.md#owner) 및 [사용자 액세스 관리자](built-in-roles.md#user-access-administrator)가 있습니다. |
| 사용자 지정 역할 수정 | `Microsoft.Authorization/ roleDefinition/write` | 사용자 지정 역할의 모든 `AssignableScopes`에 이 작업이 부여된 사용자는 해당 범위에의 사용자 지정 역할을 수정할 수 있습니다. 예를 들어 구독, 리소스 그룹 및 리소스의 [소유자](built-in-roles.md#owner) 및 [사용자 액세스 관리자](built-in-roles.md#user-access-administrator)가 있습니다. |
| 사용자 지정 역할 보기 | `Microsoft.Authorization/ roleDefinition/read` | 범위에서 이 작업이 부여된 사용자는 해당 범위에서 할당에 사용할 수 있는 사용자 지정 역할을 볼 수 있습니다. 모든 기본 제공 역할을 통해 사용자 지정 역할을 할당할 수 있습니다. |

## <a name="next-steps"></a>다음 단계
- [Azure PowerShell을 사용하여 사용자 지정 역할 만들기](custom-roles-powershell.md)
- [Azure CLI를 사용하여 사용자 지정 역할 만들기](custom-roles-cli.md)
- [역할 정의 이해](role-definitions.md)
