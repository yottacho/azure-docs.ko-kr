---
title: REST를 사용하여 Azure 사용자 할당 관리 ID를 관리하는 방법
description: REST API를 호출하기 위해 사용자 할당 관리 ID를 만들고 나열하고 삭제하는 방법에 대한 단계별 지침입니다.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/26/2018
ms.author: daveba
ms.openlocfilehash: afeac0cdb24593f5b7614a145021eefd7b376be9
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2018
ms.locfileid: "37904028"
---
# <a name="create-list-or-delete-a-user-assigned-identity-using-rest-api-calls"></a>REST API 호출을 사용하여 사용자 할당 ID 만들기, 나열 또는 삭제

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

관리 ID는 코드에 자격 증명을 포함할 필요 없이 Azure AD 인증을 지원하는 서비스에 인증하는 기능을 Azure 서비스에 제공합니다. 

이 문서에서는 CURL을 통해 REST API를 호출하여 사용자 할당 관리 ID 만들고 나열하고 삭제하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 조건

- 관리 서비스 ID를 잘 모르는 경우 [개요 섹션](overview.md)을 확인하세요. **[시스템 할당 ID와 사용자 할당 ID의 차이점](overview.md#how-does-it-work)을 반드시 검토하세요**.
- 아직 Azure 계정이 없으면 계속하기 전에 [평가판 계정](https://azure.microsoft.com/free/)에 등록해야 합니다.
- Windows를 사용하는 경우, [Linux용 Windows 하위 시스템](https://msdn.microsoft.com/commandline/wsl/about)을 설치하거나 Azure Portal에서 [Azure Cloud Shell](../../cloud-shell/overview.md)을 사용합니다.
- [Linux용 Windows 하위 시스템](https://msdn.microsoft.com/commandline/wsl/about) 또는 [Linux 배포 OS](/cli/azure/install-azure-cli-apt?view=azure-cli-latest)를 사용하는 경우, [Azure CLI 로컬 콘솔을 설치](/azure/install-azure-cli)합니다.
- Azure CLI 로컬 콘솔을 사용하는 경우, 사용자 할당 관리 ID 정보를 배포하거나 검색하려는 Azure 구독과 연결된 계정으로 `az login`을 사용하여 Azure에 로그인합니다.
- `az account get-access-token`을 통해 전달자 액세스 토큰을 검색하여 다음 사용자 할당 관리 ID 작업을 수행합니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>사용자 할당 관리 ID 만들기 

사용자 할당 관리 ID를 만들려면 Azure Resource Manager API에 다음 CURL 요청을 사용합니다. `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, `<USER ASSIGNED IDENTITY NAME>`,`<LOCATION>` 및 `<ACCESS TOKEN>` 값을 원하는 값으로 바꿉니다.

[!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X PUT -d '{"loc
ation": "<LOCATION>"}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
```

## <a name="list-user-assigned-managed-identities"></a>사용자 할당 관리 ID 나열

사용자 할당 관리 ID를 나열하려면 Azure Resource Manager API에 다음 CURL 요청을 사용합니다.  `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` 및 `<ACCESS TOKEN>` 값을 원하는 값으로 바꿉니다.

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview' -H "Authorization: Bearer <ACCESS TOKEN>"
```
## <a name="delete-a-user-assigned-managed-identity"></a>사용자 할당 관리 ID 삭제

사용자 할당 관리 ID를 삭제하려면 Azure Resource Manager API에 다음 CURL 요청을 사용합니다. `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` 및 `<ACCESS TOKEN>` 매개 변수 값을 원하는 값으로 바꿉니다.

> [!NOTE]
> 사용자 할당 ID를 삭제해도 할당된 리소스에서 참조는 제거되지 않습니다. CURL을 사용하여 VM에서 사용자 할당 관리를 제거하려면 [Azure VM에서 사용자 할당 ID 제거](qs-configure-rest-vm.md#remove-a-user-assigned identity-from-an-azure-vm)를 참조하세요.

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X DELETE -H "Authorization: Bearer <ACCESS TOKEN>"
```

## <a name="next-steps"></a>다음 단계

CURL을 사용하여 Azure VM/VMSS에 사용자 할당 ID를 할당하는 방법에 대한 자세한 내용은 [CURL을 사용하여 Azure VM에서 관리 ID 구성](qs-configure-rest-vm.md#user-assigned-identity) 및 [CURL을 사용하여 가상 머신 확장 집합에서 관리 ID 구성](qs-configure-rest-vmss.md#user-assigned-identity)을 참조하세요.


