---
title: 자습서 - Azure Active Directory B2C를 사용하여 단일 페이지 앱으로 계정 인증하도록 설정 | Microsoft Docs
description: Azure Active Directory B2C를 사용하여 단일 페이지 응용 프로그램(JavaScript)에 대한 사용자 로그인을 제공하는 방법에 대한 자습서입니다.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.author: davidmu
ms.date: 3/02/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.component: B2C
ms.openlocfilehash: 1680ff136dfa2ccb2ca3fd92f5045d47190e75fc
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34712524"
---
# <a name="tutorial-enable-single-page-app-authentication-with-accounts-using-azure-active-directory-b2c"></a>자습서: Azure Active Directory B2C를 사용하여 단일 페이지 앱으로 계정을 인증하도록 설정

이 자습서에서는 Azure AD(Azure Active Directory) B2C를 사용하여 단일 페이지 응용 프로그램(SPA)에서 사용자를 로그인하고 등록하는 방법을 보여줍니다. Azure AD B2C를 사용하면 개방형 표준 프로토콜을 사용하여 소셜 계정, 엔터프라이즈 계정 및 Azure Active Directory 계정을 인증할 수 있습니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 샘플 단일 페이지 응용 프로그램을 Azure AD B2C 테넌트에 등록합니다.
> * 사용자 등록, 로그인, 프로필 편집 및 암호 재설정에 대한 정책을 만듭니다.
> * Azure AD B2C 테넌트를 사용하도록 응용 프로그램 예제를 구성합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건

* 사용자 고유의 [Azure AD B2C 테넌트](active-directory-b2c-get-started.md)를 만듭니다.
* **ASP.NET 및 웹 개발** 워크로드가 있는 [Visual Studio 2017](https://www.visualstudio.com/downloads/)을 설치합니다.
* [.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) 이상
* [Node.js](https://nodejs.org/en/download/)

## <a name="register-single-page-app"></a>단일 페이지 앱 등록

Azure Active Directory에서 [액세스 토큰](../active-directory/develop/active-directory-dev-glossary.md#access-token)을 받으려면 먼저 응용 프로그램을 테넌트에 [등록](../active-directory/develop/active-directory-dev-glossary.md#application-registration)해야 합니다. 앱을 등록하면 테넌트에서 앱에 대한 [응용 프로그램 ID](../active-directory/develop/active-directory-dev-glossary.md#application-id-client-id)가 만들어집니다. 

Azure AD B2C 테넌트의 전역 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. Azure Portal의 서비스 목록에서 **Azure AD B2C**를 선택합니다. 

2. B2C 설정에서 **응용 프로그램**, **추가**를 차례로 클릭합니다. 

    샘플 웹앱을 테넌트에 등록하려면 다음 설정을 사용합니다.
    
    ![새 앱 추가](media/active-directory-b2c-tutorials-spa/spa-registration.png)
    
    | 설정      | 제안 값  | 설명                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Name** | 내 샘플 단일 페이지 앱 | 소비자에게 앱을 설명하는 **이름**을 입력합니다. | 
    | **웹앱/웹 API 포함** | 예 | 단일 페이지 앱에 대해 **예**를 선택합니다. |
    | **암시적 흐름 허용** | 예 | 앱에서 [OpenID Connect 로그인](active-directory-b2c-reference-oidc.md)을 사용하므로 **예**를 선택합니다. |
    | **회신 URL** | `http://localhost:6420` | 회신 URL은 Azure AD B2C에서 앱이 요청한 토큰을 반환하는 엔드포인트입니다. 이 자습서의 샘플은 로컬에서 실행되고(로컬 호스트) 6420 포트에서 수신 대기합니다. |
    | **네이티브 클라이언트 포함** | 아니오 | 이 앱은 단일 페이지 앱이지만 네이티브 클라이언트가 아니기 때문에 [아니요]를 선택합니다. |
    
3. **만들기** 를 클릭하여 앱을 등록합니다.

등록된 앱은 Azure AD B2C 테넌트에 대한 응용 프로그램 목록에 표시됩니다. 목록에서 해당하는 단일 페이지 앱을 선택합니다. 등록된 단일 페이지 앱의 속성 창이 표시됩니다.

![단일 페이지 앱 속성](./media/active-directory-b2c-tutorials-spa/b2c-spa-properties.png)

**응용 프로그램 클라이언트 ID**를 적어 둡니다. ID는 앱을 고유하게 식별하며 자습서의 뒷부분에서 앱을 구성할 때 필요합니다.

## <a name="create-policies"></a>정책 만들기

Azure AD B2C 정책은 사용자 워크플로를 정의합니다. 예를 들어 로그인, 등록, 암호 변경 및 프로필 편집은 일반적인 워크플로입니다.

### <a name="create-a-sign-up-or-sign-in-policy"></a>등록 또는 로그인 정책 만들기

액세스할 사용자를 등록한 다음 웹앱에 로그인하려면 **등록 또는 로그인 정책**을 만듭니다.

1. Azure AD B2C 포털 페이지에서 **등록 또는 로그인 정책**을 선택하고 **추가**를 클릭합니다.

    정책을 구성하려면 다음 설정을 사용합니다.

    ![등록 또는 로그인 정책 추가](media/active-directory-b2c-tutorials-web-app/add-susi-policy.png)

    | 설정      | 제안 값  | 설명                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Name** | SiUpIn | 정책에 대한 **이름**을 입력합니다. 정책 이름 앞에 **B2C_1_** 이 붙습니다. 샘플 코드에서는 전체 정책 이름(**B2C_1_SiUpIn**)을 사용합니다. | 
    | **ID 공급자** | 이메일 등록 | ID 공급자는 사용자를 고유하게 식별하는 데 사용됩니다. |
    | **등록 특성** | 표시 이름 및 우편 번호 | 등록 시 사용자로부터 수집할 특성을 선택합니다. |
    | **응용 프로그램 클레임** | 표시 이름, 우편 번호, 새 사용자, 사용자의 개체 ID | [액세스 토큰](../active-directory/develop/active-directory-dev-glossary.md#access-token)에 포함하려는 [클레임](../active-directory/develop/active-directory-dev-glossary.md#claim)을 선택합니다. |

2. **만들기**를 클릭하여 정책을 만듭니다. 

### <a name="create-a-profile-editing-policy"></a>프로필 편집 정책 만들기

사용자가 직접 사용자 프로필 정보를 다시 설정할 수 있게 하려면 **프로필 편집 정책**을 만듭니다.

1. Azure AD B2C 포털 페이지에서 **프로필 편집 정책**을 선택하고 **추가**를 클릭합니다.

    정책을 구성하려면 다음 설정을 사용합니다.

    | 설정      | 제안 값  | 설명                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Name** | SiPe | 정책에 대한 **이름**을 입력합니다. 정책 이름 앞에 **B2C_1_** 이 붙습니다. 샘플 코드에서는 전체 정책 이름(**B2C_1_SiPe**)을 사용합니다. | 
    | **ID 공급자** | 로컬 계정 로그인 | ID 공급자는 사용자를 고유하게 식별하는 데 사용됩니다. |
    | **프로필 특성** | 표시 이름 및 우편 번호 | 사용자가 프로필 편집 시 수정할 수 있는 특성을 선택합니다. |
    | **응용 프로그램 클레임** | 표시 이름, 우편 번호, 사용자의 개체 ID | 프로필을 성공적으로 편집한 후에 [액세스 토큰](../active-directory/develop/active-directory-dev-glossary.md#access-token)에 포함하려는 [클레임](../active-directory/develop/active-directory-dev-glossary.md#claim)을 선택합니다. |

2. **만들기**를 클릭하여 정책을 만듭니다. 

### <a name="create-a-password-reset-policy"></a>암호 재설정 정책 만들기

응용 프로그램에서 암호 재설정을 사용하도록 설정하려면 **암호 재설정 정책**을 만들어야 합니다. 이 정책은 암호를 다시 설정하는 동안 소비자 환경 및 응용 프로그램에서 성공적으로 완료될 때 받는 토큰의 내용을 설명합니다.

1. Azure AD B2C 포털 페이지에서 **암호 재설정 정책**을 선택하고 **추가**를 클릭합니다.

    정책을 구성하려면 다음 설정을 사용합니다.

    | 설정      | 제안 값  | 설명                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Name** | SSPR | 정책에 대한 **이름**을 입력합니다. 정책 이름 앞에 **B2C_1_** 이 붙습니다. 샘플 코드에서는 전체 정책 이름(**B2C_1_SSPR**)을 사용합니다. | 
    | **ID 공급자** | 이메일 주소를 사용하여 암호 재설정 | 사용자를 고유하게 식별하는 데 사용되는 ID 공급자입니다. |
    | **응용 프로그램 클레임** | 사용자의 개체 ID | 암호를 성공적으로 다시 설정한 후에 [액세스 토큰](../active-directory/develop/active-directory-dev-glossary.md#access-token)에 포함하려는 [클레임](../active-directory/develop/active-directory-dev-glossary.md#claim)을 선택합니다. |

2. **만들기**를 클릭하여 정책을 만듭니다. 

## <a name="update-single-page-app-code"></a>단일 페이지 앱 코드 업데이트

앱을 등록하고 정책을 만들었으니, Azure AD B2C 테넌트를 사용하도록 앱을 구성해야 합니다. 이 자습서에서는 GitHub에서 다운로드할 수 있는 샘플 SPA JavaScript 앱을 구성합니다. 

GitHub에서 [Zip 파일을 다운로드](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip)하거나 샘플 웹앱을 복제합니다.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```
샘플 앱은 단일 페이지 앱에서 등록, 로그인 및 보호되는 Web API 호출에 Azure AD B2C를 사용하는 방법을 보여줍니다. 테넌트에서 앱 등록을 사용하도록 앱을 변경하고 앞에서 만든 정책을 구성해야 합니다. 

앱 설정을 변경하려면 다음을 수행합니다.

1. Node.js 단일 페이지 앱 샘플에서 `index.html` 파일을 엽니다.
2. Azure AD B2C 테넌트 등록 정보를 사용하여 샘플을 구성합니다. 다음 코드 줄을 변경합니다.

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    var applicationConfig = {
        clientID: '<Application ID for your SPA obtained from portal app registration>',
        authority: "https://login.microsoftonline.com/tfp/<your-tenant-name>.onmicrosoft.com/B2C_1_SiUpIn",
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/demoapi/demo.read"],
        webApi: 'https://fabrikamb2chello.azurewebsites.net/hello',
    };
    ```

    이 자습서에 사용된 정책 이름은 **B2C_1_SiUpIn**입니다. 다른 정책 이름을 사용하는 경우 `authority` 값에 해당 정책 이름을 사용합니다.

## <a name="run-the-sample"></a>샘플 실행

1. Node.js 명령 프롬프트를 시작합니다.
2. Node.js 샘플이 포함된 디렉터리로 변경합니다. 예: `cd c:\active-directory-b2c-javascript-msal-singlepageapp`
3. 다음 명령을 실행합니다.
    ```
    npm install && npm update
    node server.js
    ```

    콘솔 창에는 앱이 호스트되는 포트 번호가 표시됩니다.
    
    ```
    Listening on port 6420...
    ```

4. 앱을 보려면 브라우저를 사용하여 `http://localhost:6420` 주소로 이동합니다.

샘플 앱은 등록, 로그인, 프로필 수정 및 암호 재설정을 지원합니다. 이 자습서에서는 사용자가 이메일 주소를 사용하여 앱에 등록하고 사용하는 방법을 중점적으로 설명합니다. 다른 시나리오를 직접 탐색할 수 있습니다.

### <a name="sign-up-using-an-email-address"></a>전자 메일 주소를 사용하여 등록

1. **로그인**을 클릭하여 SPA 앱의 사용자로 등록합니다. 여기서는 이전 단계에서 정의한 **B2C_1_SiUpIn** 정책을 사용합니다.

2. Azure AD B2C에서 등록 링크가 있는 로그인 페이지를 제공합니다. 아직 계정이 없으므로 **지금 등록** 링크를 클릭합니다. 

3. 등록 워크플로에서 이메일 주소를 사용하여 사용자의 ID를 수집하고 확인하는 페이지를 제공합니다. 또한 등록 워크플로는 사용자의 암호와 요청된 특성(정책에 정의되어 있음)을 수집합니다.

    유효한 이메일 주소를 사용하고 확인 코드를 사용하여 유효성을 검사합니다. 암호를 설정합니다. 요청된 특성에 대한 값을 입력합니다. 

    ![등록 워크플로](media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.png)

4. **만들기**를 클릭하여 로컬 계정을 Azure AD B2C 테넌트에 만듭니다.

이제 사용자는 이메일 주소를 사용하여 로그인하고 SPA 앱을 사용할 수 있습니다.

> [!NOTE]
> 로그인하면 앱에 "권한 부족" 오류가 표시됩니다. 데모 테넌트에서 리소스에 액세스하려고 시도하기 때문에 이 오류가 표시되는 것입니다. 액세스 토큰이 Azure AD 테넌트에만 유효하므로 API 호출에 권한이 부여되지 않습니다. 그 다음 자습서를 계속 진행하여 테넌트의 보호되는 웹 API 키를 만드세요. 

## <a name="clean-up-resources"></a>리소스 정리

다른 Azure AD B2C 자습서를 사용하려는 경우 Azure AD B2C 테넌트를 사용할 수 있습니다. 더 이상 필요하지 않으면 [Azure AD B2C 테넌트를 삭제](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant)할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure AD B2C 테넌트를 만들고, 정책을 만들고, Azure AD B2C 테넌트를 사용하도록 샘플 단일 페이지 앱을 업데이트하는 방법을 알아보았습니다. 그 다음 자습서로 넘어가서 데스크톱 앱에서 보호되는 Web API를 등록, 구성 및 호출하는 방법을 알아보세요.

> [!div class="nextstepaction"]
> 
