---
title: Azure의 RBAC(역할 기반 액세스 제어)란? | Microsoft Docs
description: Azure의 RBAC(역할 기반 액세스 제어) 개요를 살펴봅니다. 역할 할당을 사용하여 Azure 리소스에 대한 액세스를 제어합니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8f8aadeb-45c9-4d0e-af87-f1f79373e039
ms.service: role-based-access-control
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/02/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 4dcfb71e0adb05922603715e4dbcbdb243305927
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/03/2018
ms.locfileid: "37438203"
---
# <a name="what-is-role-based-access-control-rbac"></a>RBAC(역할 기반 액세스 제어)란?

클라우드 리소스에 대한 액세스 관리는 클라우드를 사용하는 모든 조직에서 중요한 기능입니다. RBAC(역할 기반 액세스 제어)는 Azure 리소스에 액세스할 수 있는 사용자, 해당 리소스로 수행할 수 있는 작업 및 액세스 권한이 있는 영역을 관리하는 데 도움을 줍니다.

RBAC는 Azure 리소스에 대한 액세스를 세밀하게 관리할 수 있는 [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) 기반의 권한 부여 시스템입니다. RBAC를 사용하면 팀 내에서 업무를 분리하고 사용자에게 해당 작업을 수행하는 데 필요한 만큼의 권한만 부여할 수 있습니다. Azure 구독 또는 리소스에서 모든 사람에게 무제한 권한을 제공하는 대신, 특정 범위에서 특정 작업만 허용할 수 있습니다.

## <a name="what-can-i-do-with-rbac"></a>RBAC로 무엇을 할 수 있나요?

RBAC로 다음과 같은 일을 할 수 있습니다.

- 한 사용자는 구독의 가상 머신을 관리하고 다른 사용자는 가상 네트워크를 관리하도록 허용
- DBA 그룹이 구독의 SQL 데이터베이스를 관리하도록 허용
- 사용자가 가상 머신, 웹 사이트, 서브넷 등 리소스 그룹의 모든 리소스를 관리하도록 허용
- 응용 프로그램이 리소스 그룹의 모든 리소스에 액세스하도록 허용

## <a name="how-rbac-works"></a>RBAC의 작동 원리

RBAC를 사용하여 리소스에 대한 액세스를 제어하는 방법은 역할 할당을 만드는 것입니다. 이 개념을 반드시 이해해야 하며, 이 원리에 따라 권한이 적용됩니다. 역할 할당은 보안 주체, 역할 정의, 범위의 세 가지 요소로 구성됩니다.

### <a name="security-principal"></a>보안 주체

*보안 주체*는 사용자, 그룹 또는 Azure 리소스에 대한 액세스를 요청하는 서비스 주체를 나타내는 개체입니다.

![역할 할당에 대한 보안 주체](./media/overview/rbac-security-principal.png)

- 사용자 - Azure Active Directory에 프로필이 있는 개인. 다른 테넌트의 사용자에게 역할을 할당할 수도 있습니다. 다른 조직의 사용자에 대한 내용은 [Azure Active Directory B2B](/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)를 참조하세요.
- 그룹 - Azure Active Directory에서 만든 사용자 집합. 그룹에 역할을 할당하면 해당 그룹의 모든 사용자가 같은 역할을 갖습니다. 
- 서비스 주체 - 응용 프로그램 또는 서비스에서 특정 Azure 리소스에 액세스하기 위해 사용하는 보안 ID. 응용 프로그램의 *사용자 ID*(사용자 이름과 암호 또는 인증서)로 생각하시면 됩니다.

### <a name="role-definition"></a>역할 정의

*역할 정의*는 권한 컬렉션입니다. 때로는 *역할*이라고 합니다. 역할 정의에는 읽기, 쓰기 및 삭제와 같이 수행할 수 있는 작업이 나열됩니다. 역할은 소유자처럼 대략적일 수도 있고 가상 머신 판독기처럼 구체적일 수도 있습니다.

![역할 할당에 대한 역할 정의](./media/overview/rbac-role-definition.png)

Azure에는 사용 가능한 [기본 제공 역할](built-in-roles.md)이 여러 개 있습니다. 다음은 네 가지 기본 제공 역할입니다. 처음 세 개는 모든 리소스 종류에 적용됩니다.

- [소유자](built-in-roles.md#owner) - 액세스 권한을 다른 사용자에게 위임할 수 있는 권한을 포함하여 모든 리소스에 대한 전체 액세스 권한을 보유합니다.
- [기여자](built-in-roles.md#contributor) - 모든 유형의 Azure 리소스를 만들고 관리할 수 있지만 다른 사용자에게 액세스 권한을 부여할 수 없습니다.
- [읽기 권한자](built-in-roles.md#reader) - 기존 Azure 리소스를 볼 수 있습니다.
- [사용자 액세스 관리자](built-in-roles.md#user-access-administrator) - Azure 리소스에 대한 사용자 액세스를 관리할 수 있습니다.

나머지 기본 제공 역할은 특정 Azure 리소스의 관리를 허용합니다. 예를 들어 [Virtual Machine 기여자](built-in-roles.md#virtual-machine-contributor) 역할을 사용하면 사용자가 가상 머신을 만들고 관리할 수 있습니다. 기본 제공 역할이 조직의 특정 요구 사항을 충족하지 않는 경우 [사용자 지정 역할](custom-roles.md)을 만들면 됩니다.

Azure에는 개체 내 데이터에 대한 액세스 권한을 부여할 수 있는 데이터 작업(현재 미리 보기)이 도입되었습니다. 예를 들어 사용자가 저장소 계정에 대한 데이터 읽기 액세스 권한을 갖고 있는 경우 해당 저장소 계정 내의 BLOB 또는 메시지를 읽을 수 있습니다. 자세한 내용은 [역할 정의 이해](role-definitions.md)를 참조하세요.

### <a name="scope"></a>범위

*범위*는 액세스가 적용되는 경계입니다. 역할에 할당할 때 범위를 정의하여 허용되는 작업을 추가로 제한할 수 있습니다. 이러한 기능은 누군가를 단일 리소스 그룹에 대해서만 [웹 사이트 기여자](built-in-roles.md#website-contributor)로 지정하려는 경우에 유용합니다.

Azure는 여러 수준(구독, 리소스 그룹 또는 리소스)에서 구독을 지정할 수 있습니다. 범위는 모든 자식이 한 부모만 갖는 부모-자식 관계로 구조화됩니다.

![역할 할당 범위](./media/overview/rbac-scope.png)

부모 범위에서 할당하는 액세스 권한은 자식 범위에서 상속됩니다. 예:

- 구독 범위에서 그룹에 [읽기 권한자](built-in-roles.md#reader) 역할을 할당하면 해당 그룹의 멤버가 구독의 모든 리소스 그룹 및 리소스를 볼 수 있습니다.
- 리소스 그룹 범위에서 응용 프로그램에 [기여자](built-in-roles.md#contributor) 역할을 할당하면 해당 응용 프로그램은 해당 리소스 그룹의 모든 리소스 종류를 관리할 수 있지만, 구독의 다른 리소스 그룹을 관리할 수 없습니다.

Azure는 구독보다 위에 있는 [관리 그룹](../azure-resource-manager/management-groups-overview.md)이라고 하는 범위를 포함하고 있으며, 현재는 미리 보기입니다. 관리 그룹은 여러 구독을 관리하는 방법입니다. RBAC의 범위를 지정할 때 관리 그룹을 지정하거나 구독, 리소스 그룹 또는 리소스 계층 구조를 지정할 수 있습니다.

### <a name="role-assignment"></a>역할 할당

*역할 할당*은 액세스 권한을 부여하기 위해 특정 범위에서 역할 정의를 사용자, 그룹 또는 서비스 주체에 바인딩하는 프로세스입니다. 역할 할당을 만들어서 액세스 권한을 부여하고, 역할 할당을 제거하여 액세스 권한을 취소합니다.

다음 다이어그램은 역할 할당의 예를 보여줍니다. 이 예제에서 마케팅 그룹에는 pharma-sales 리소스 그룹에 대한 [기여자](built-in-roles.md#contributor) 역할이 할당되었습니다. 즉, 마케팅 그룹의 해당 사용자가 pharma-sales 리소스 그룹에 Azure 리소스를 만들거나 관리할 수 있습니다. 마케팅 사용자는 다른 역할 할당에 포함되지 않는 한, pharma-sales 리소스 그룹 외부에 있는 리소스에 액세스할 수 없습니다.

![액세스를 제어하는 역할 할당](./media/overview/rbac-overview.png)

Azure Portal, Azure CLI, Azure PowerShell, Azure SDK 또는 REST API를 사용하여 역할 할당을 만들 수 있습니다. 각 구독에서 최대 2000개의 역할 할당을 유지할 수 있습니다. 역할 할당을 만들거나 제거하려면 `Microsoft.Authorization/roleAssignments/*` 권한이 필요합니다. 이 권한은 [소유자](built-in-roles.md#owner) 또는 [사용자 액세스 관리자](built-in-roles.md#user-access-administrator) 역할을 통해 부여됩니다.

## <a name="next-steps"></a>다음 단계

- [빠른 시작: RBAC 및 Azure Portal을 사용하여 사용자에게 액세스 권한 부여](quickstart-assign-role-user-portal.md)
- [RBAC 및 Azure Portal을 사용하여 액세스 관리](role-assignments-portal.md)
- [Azure의 다양한 역할 이해](rbac-and-directory-admin-roles.md)
