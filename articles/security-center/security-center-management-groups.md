---
title: Azure Security Center에 대한 테넌트 수준 가시성 얻기 | Microsoft Docs
description: Azure Security Center에서 테넌트 수준 가시성 얻기에 대해 알아봅니다.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: b85c0e93-9982-48ad-b23f-53b367f22b10
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/22/2018
ms.author: terrylan
ms.openlocfilehash: 05504f94d72f39762672d0e79669dd50d8e6435e
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/03/2018
ms.locfileid: "37441507"
---
# <a name="gain-tenant-wide-visibility-for-azure-security-center"></a>Azure Security Center에 대한 테넌트 수준 가시성 얻기
이 문서에서는 Azure Security Center에서 제공하는 혜택을 최대화하는 여러 작업을 수행하여 시작하도록 돕습니다. 이러한 작업을 수행하면 Azure Active Directory 테넌트에 연결된 모든 Azure 구독에 대한 가시성을 얻고 집합적 방식으로 여러 구독에 보안 정책을 적용하여 모든 규모에서 조직의 보안 상태를 효율적으로 관리할 수 있습니다.

## <a name="management-groups"></a>관리 그룹
Azure 관리 그룹은 액세스, 정책 및 구독 그룹에 대한 보고를 효율적으로 관리하는 기능을 제공하고 루트 관리 그룹에서 작업을 수행하여 전체 Azure 공간을 효율적으로 관리합니다. 각 Azure AD 테넌트에는 루트 관리 그룹이라고 하는 단일 최상위 관리 그룹이 부여됩니다. 이 루트 관리 그룹은 모든 관리 그룹과 구독이 루트 관리 그룹까지 접히도록 만들어집니다. 이 그룹은 전역 정책 및 RBAC 할당을 디렉터리 수준에서 적용하는 것을 허용합니다. 

루트 관리 그룹은 다음 작업 중 하나를 실행할 때 자동으로 만들어집니다. 
1. [Azure Portal](https://portal.azure.com)에서 **관리 그룹**으로 이동하여 Azure 관리 그룹을 사용하도록 옵트인합니다.
2. API 호출을 통해 관리 그룹을 만듭니다.
3. PowerShell을 사용하여 관리 그룹을 만듭니다.

관리 그룹의 자세한 개요는 [Azure 관리 그룹으로 리소스 구성](../azure-resource-manager/management-groups-overview.md) 문서를 참조하세요.

## <a name="create-a-management-group-in-the-azure-portal"></a>Azure Portal에서 관리 그룹 만들기
관리 그룹에 구독을 구성하고 거버넌스 정책을 관리 그룹에 적용합니다. 관리 그룹에 속하는 모든 구독은 관리 그룹에 적용되는 정책을 자동으로 상속합니다. 관리 그룹은 Security Center에 등록할 필요가 없지만 루트 관리 그룹이 생성되도록 적어도 하나의 관리 그룹을 만드는 것이 좋습니다. 그룹이 만들어진 후 Azure AD 테넌트 아래의 모든 하위 구독이 연결됩니다. PowerShell에 대한 지침 및 추가 정보는 [리소스 및 조직 관리를 위한 관리 그룹 만들기](../azure-resource-manager/management-groups-create.md)를 참조하세요.

 
1. [Azure 포털](http://portal.azure.com)에 로그인합니다.
2. **모든 서비스** > **관리 그룹**을 선택합니다.
3. 주 페이지에서 **새 관리 그룹**을 선택합니다. 

    ![기본 그룹](./media/security-center-management-groups/main.png) 
4.  관리 그룹 ID 필드를 채웁니다. 
    - **관리 그룹 ID**는 이 관리 그룹에 명령을 전송하는 데 사용되는 디렉터리 고유 식별자입니다. 이 식별자는 Azure 시스템 전체에서 이 그룹을 식별하는 데 사용되므로 만든 후에 편집할 수 없습니다. 
    - 표시 이름 필드는 Azure Portal 내에 표시되는 이름을 포함합니다. 별도 표시 이름은 관리 그룹을 만들 때 사용되는 선택적 필드로, 언제든지 변경할 수 있습니다.  

      ![생성](./media/security-center-management-groups/create_context_menu.png)  
5.  **저장**을 선택합니다.

### <a name="view-management-groups-in-the-azure-portal"></a>Azure Portal에서 관리 그룹 보기
1. [Azure Portal](http://portal.azure.com)에 로그인합니다.
2. 관리 그룹을 보려면 Azure 주 메뉴 아래에서 **모든 서비스**를 선택합니다.
3. **일반** 아래에서 **관리 그룹**을 선택합니다.

    ![관리 그룹 만들기](./media/security-center-management-groups/all-services.png)

## <a name="grant-tenant-level-visibility-and-the-ability-to-assign-policies"></a>테넌트 수준 가시성 및 정책을 할당하는 기능 부여

Azure AD 테넌트에 등록된 모든 구독의 보안 상태로 가시성을 가져오려면 루트 관리 그룹에 할당될 충분한 읽기 권한이 있는 RBAC 역할이 필요합니다.

### <a name="elevate-access-for-a-global-administrator-in-azure-active-directory"></a>Azure Active Directory에서 전역 관리자에 대한 액세스 권한 상승
Azure Active Directory 테넌트 관리자는 Azure 구독에 대한 직접 액세스를 갖지 않습니다. 그러나 디렉터리 관리자로서 액세스 권한을 갖는 역할로 자체적으로 권한을 상승시킬 수 있습니다. Azure AD 테넌트 관리자는 RBAC 역할을 할당할 수 있도록 루트 관리 그룹 수준에서 사용자 액세스 관리자로 스스로를 승격해야 합니다. PowerShell 지침 및 추가 정보는 [Azure Active Directory에서 전역 관리자에 대한 액세스 권한 상승](../role-based-access-control/elevate-access-global-admin.md)을 참조하세요. 


1. [Azure Portal](https://portal.azure.com) 또는 [Azure Active Directory 관리 센터](https://aad.portal.azure.com)에 로그인합니다.

2. 탐색 목록에서 **Azure Active Directory**를 클릭한 다음, **속성**을 클릭합니다.

   ![Azure AD 속성 - 스크린샷](./media/security-center-management-groups/aad-properties.png)

3. **전역 관리자는 Azure 구독 및 관리 그룹을 관리할 수 있습니다.** 에서 스위치를 **예**로 설정합니다.

   ![전역 관리자는 Azure 구독 및 관리 그룹을 관리할 수 있습니다. - 스크린샷](./media/security-center-management-groups/aad-properties-global-admin-setting.png)

   - 스위치를 **예**로 설정하면 전역 관리자 계정(현재 로그인한 사용자)이 루트 범위(`/`)에서 Azure RBAC의 사용자 액세스 관리자 역할에 추가됩니다. 여기에서는 Azure AD 테넌트와 연결된 모든 Azure 구독에서 보고 보고할 액세스할 수 있는 권한을 부여합니다.

   - 스위치를 **아니요**로 설정하면 전역 관리자 계정(현재 로그인한 사용자)이 Azure RBAC의 사용자 액세스 관리자 역할에서 제거됩니다. Azure AD 테넌트와 연결된 모든 Azure 구독을 볼 수 없습니다. 또한 액세스 권한이 부여된 Azure 구독만을 보고 관리할 수 있습니다.

4. **Save**를 클릭하여 설정을 저장합니다.

    - 이 설정은 전역 속성이 아니며 현재 로그인된 사용자에게만 적용됩니다.

5. 권한이 상승된 액세스를 확인하는 데 필요한 작업을 수행합니다. 작업이 완료되면 스위치를 다시 **아니요**로 설정합니다.

### <a name="assign-rbac-roles-to-users"></a>사용자에게 RBAC 역할 할당
테넌트 관리자가 상승된 엑세스 권한을 가지면 루트 관리 그룹 수준에서 관련 사용자에게 RBAC 역할을 할당할 수 있습니다. 할당할 권장 역할은 [**Reader**](../role-based-access-control/built-in-roles.md#reader)입니다. 이 역할은 테넌트 수준 가시성을 제공하는 데 필요합니다. 할당된 역할은 모든 관리 그룹 및 루트 관리 그룹 아래의 구독으로 자동으로 전파됩니다. RBAC 역할에 대한 자세한 내용은 [사용 가능한 역할](../active-directory/users-groups-roles/directory-assign-admin-roles.md#available-roles)을 참조하세요.

1. [Azure PowerShell](/powershell/azure/install-azurerm-ps)을 설치합니다.
2. 다음 명령을 실행합니다. 

    ```azurepowershell
    # Install Management Groups Powershell module
    Install-Module AzureRM.Resources
    
    # Login to Azure as a Global Administrator user
    Login-AzureRmAccount
    ```

3. 메시지가 표시되면 전역 관리자 자격 증명으로 로그인합니다. 

    ![로그인 프롬프트 스크린샷](./media/security-center-management-groups/azurerm-sign-in.PNG)

4. 다음 명령을 실행하여 reader 역할 권한을 부여합니다.

    ```azurepowershell
    # Add Reader role to the required user on the Root Management Group
    # Replace "user@domian.com” with the user to grant access to
    New-AzureRmRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/"
    ```
5. 역할을 제거하려면 다음 명령을 사용합니다. 

    ```azurepowershell
    Remove-AzureRmRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/" 
    ```

<!-- Currently, PowerShell method only 6/26/18

1. Sign in to the [Azure portal](https://portal.azure.com). 
2. To view management groups, select **All services** under the Azure main menu then select **Management Groups**.
3.  Select a management group and click **details**.

    ![Management Groups details screenshot](./media/security-center-management-groups/management-group-details.PNG)
 
4. Click **Access control (IAM)** then **Add**.
5. Select the role to assign and the user, then click **Save**.  
   
   ![Add Security Reader role screenshot](./media/security-center-management-groups/asc-security-reader.png)
-->

### <a name="remove-elevated-access"></a>상승된 액세스 제거 
RBAC 역할이 사용자에게 할당되면 테넌트 관리자는 사용자 액세스 관리자 역할에서 스스로를 제거해야 합니다.

1. [Azure Portal](https://portal.azure.com) 또는 [Azure Active Directory 관리 센터](https://aad.portal.azure.com)에 로그인합니다.

2. 탐색 목록에서 **Azure Active Directory**를 클릭한 다음, **속성**을 클릭합니다.

3. **전역 관리자는 Azure 구독 및 관리 그룹을 관리할 수 있습니다.** 에서 스위치를 **아니요**로 설정합니다.

4. **Save**를 클릭하여 설정을 저장합니다.

### <a name="open-or-refresh-security-center"></a>Security Center 열기 또는 새로 고치기
RBAC 역할을 할당하면 Azure Security Center를 열거나 새로 고쳐 Azure AD 테넌트 아래의 모든 구독에 대한 가시성이 있는지 확인합니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
2. Azure 주 메뉴 아래에서 **모든 서비스**를 선택한 다음, **Security Center**를 선택합니다.
3. **개요**에 구독 적용 범위 차트가 있습니다. 
    ![구독 적용 범위 차트 스크린샷](./media/security-center-management-groups/security-center-subscription-coverage.png)
4. **적용 범위**를 클릭하여 적용되는 구독 목록을 봅니다. 
    ![구독 적용 범위 목록 스크린샷](./media/security-center-management-groups/security-center-coverage.png)

## <a name="adding-subscriptions-to-a-management-groups"></a>관리 그룹에 구독 추가
만든 관리 그룹에 구독을 추가할 수 있습니다. 이러한 단계는 테넌트 수준의 가시성과 글로벌 정책 및 액세스 관리를 얻는 데 필수적이지 않습니다.

1. **관리 그룹** 아래에서 구독을 추가할 관리 그룹을 선택합니다.

    ![구독을 추가할 관리 그룹 선택](./media/security-center-management-groups/management-group-subscriptions.png)

2. **기존 항목 추가**를 선택합니다.

    ![기존 항목 추가](./media/security-center-management-groups/add-existing.png)

3. **기존 리소스 추가** 아래에서 구독을 입력하고 **저장**을 클릭합니다.

4. 범위에 모든 구독을 추가할 때까지 1-3단계를 반복합니다.

 > [!NOTE]
 > 관리 그룹은 구독 및 자식 관리 그룹 모두를 포함할 수 있습니다. 사용자에게 부모 관리 그룹에 대한 RBAC 역할을 할당하면 액세스는 자식 관리 그룹의 구독에서 상속됩니다. 부모 관리 그룹에서 설정된 정책도 자식에서 상속됩니다. 

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Security Center에 대한 테넌트 수준 가시성을 얻는 방법을 배웠습니다. Security Center에 대한 자세한 내용은 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md)

> [!div class="nextstepaction"]
> [Azure Security Center에서 보안 경고 관리 및 응답](security-center-managing-and-responding-alerts.md)

