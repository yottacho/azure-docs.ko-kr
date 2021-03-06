---
title: '자습서: ServiceChannel과 Azure Active Directory 통합 | Microsoft 문서'
description: Azure Active Directory 및 ServiceChannel 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c3546eab-96b5-489b-a309-b895eb428053
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/3/2017
ms.author: jeedes
ms.openlocfilehash: a62a6b334fd58ce360d932cdc4a8f04f36e3232d
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2018
ms.locfileid: "36302032"
---
# <a name="tutorial-azure-active-directory-integration-with-servicechannel"></a>자습서: ServiceChannel과 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 ServiceChannel을 통합하는 방법에 대해 알아봅니다.

ServiceChannel을 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- ServiceChannel에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 ServiceChannel(Single Sign-On)에 자동으로 로그인하도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure 관리 포털에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](../manage-apps/what-is-single-sign-on.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

ServiceChannel과의 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- ServiceChannel Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 않도록 합니다.
- Azure AD 평가판 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 얻을 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 ServiceChannel 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-servicechannel-from-the-gallery"></a>갤러리에서 ServiceChannel 추가
ServiceChannel의 Azure AD 통합을 구성하려면 갤러리의 ServiceChannel을 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 ServiceChannel을 추가하려면 다음 단계를 수행합니다.**

1. **[Azure 관리 포털](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Active Directory][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![응용 프로그램][2]
    
3. 대화 상자 위쪽에 있는 **추가** 단추를 클릭합니다.

    ![응용 프로그램][3]

4. 검색 상자에 **ServiceChannel**을 입력합니다.

    ![Azure AD 테스트 사용자 만들기](./media/servicechannel-tutorial/tutorial-servicechannel_000.png)

5. 결과 창에서 **ServiceChannel**을 선택하고 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![Azure AD 테스트 사용자 만들기](./media/servicechannel-tutorial/tutorial-servicechannel_2.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD Single Sign-on 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 ServiceChannel에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 ServiceChannel 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자 및 ServiceChannel의 관련 사용자 간에 연결이 형성되어야 합니다.

이 연결 관계는 Azure AD의 **사용자 이름** 값을 ServiceChannel의 **Username** 값으로 할당하여 설정합니다.

ServiceChannel에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On 테스트하는 데 사용합니다.
3. **[ServiceChannel 테스트 사용자 만들기](#creating-a-servicechannel-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure 관리 포털에서 Azure AD Single Sign-On을 사용하도록 설정하고 ServiceChannel 응용 프로그램에서 Single Sign-On을 구성합니다.

**ServiceChannel에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure 관리 포털의 **ServiceChannel** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Configure Single Sign-On][4]

2. **Single sign on** 대화 상자에서 **모드**로 **SAML 기반 로그온**을 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Configure Single Sign-On](./media/servicechannel-tutorial/tutorial-servicechannel_01.png)

3. **ServiceChannel 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![Configure Single Sign-On](./media/servicechannel-tutorial/tutorial-servicechannel_urls.png)

    a. **식별자** 텍스트 상자에 해당 값으로 `http://adfs.<domain>.com/adfs/service/trust`을 입력합니다.

    나. **회신 URL** 텍스트 상자에 다음 패턴으로 URL을 입력합니다.`https://<customer domain>.servicechannel.com/saml/acs`

    > [!NOTE] 
    > 이러한 값은 실제 값이 아닙니다. 실제 식별자 및 회신 URL로 해당 값을 업데이트해야 합니다. 식별자에는 고유한 문자열 값을 사용하는 것이 좋습니다. 이러한 값을 얻으려면 [ServiceChannel 지원 팀](https://servicechannel.zendesk.com/hc/en-us)에 문의하세요.

4. ServiceChannel 응용 프로그램은 특정 서식에서 SAML 어설션을 예상하며 이는 SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가합니다. 다음 스크린샷은 이에 대한 예제를 보여 줍니다. **NameIdentifier(사용자 식별자)** 는 유일한 필수 클레임이며 기본값은 **user.userprincipalname**이지만 ServiceChannel에서는 **user.mail**과 매핑되도록 예상합니다. JIT(Just-In-Time) 사용자 프로비전을 활성화하려는 경우 아래와 같이 다음 클레임을 추가해야 합니다. **역할** 클레임은 사용자의 역할을 포함하는 **user.assignedroles**에 매핑되어야 합니다.  

    클레임에 대한 자세한 지침은 [여기](https://servicechannel.zendesk.com/hc/en-us/articles/217514326-Azure-AD-Configuration-Example)에서 ServiceChannel 가이드를 참조할 수 있습니다.
    
    ![Configure Single Sign-On](./media/servicechannel-tutorial/tutorial_servicechannel_attribute.png)

    > [!NOTE] 
    > Azure AD에서 **역할**을 구성하는 방법을 알아보려면 [RBAC 및 Azure Portal을 사용하여 액세스 관리](../../role-based-access-control/role-assignments-portal.md)를 참조하세요.

5. **사용자 특성** 섹션에서 **기타 모든 사용자 특성 보기 및 편집**을 클릭하고 특성을 설정합니다.

    | 특성 이름 | 특성 값 |
    | --- | --- |    
    | 역할| user.assignedroles |

    a. **특성 추가**를 클릭하여 **특성 추가** 대화 상자를 엽니다.

    ![Configure Single Sign-On](./media/servicechannel-tutorial/tutorial_servicechannel_04.png)

    ![Configure Single Sign-On](./media/servicechannel-tutorial/tutorial_servicechannel_05.png)
    
    나. **이름** 텍스트 상자에서 해당 행에 표시된 특성 이름을 입력합니다.
    
    다. **값** 목록에서 해당 행에 대해 표시된 특성을 입력합니다.
    
    d. **확인**을 클릭합니다.
    
6. **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 클릭한 후 컴퓨터에 인증서 파일을 저장합니다.

    ![Configure Single Sign-On](./media/servicechannel-tutorial/tutorial-servicechannel_05.png) 

7. **저장**을 클릭합니다.

    ![Configure Single Sign-On](./media/servicechannel-tutorial/tutorial_general_400.png)

8. **ServiceChannel 구성** 섹션에서 **ServiceChannel 구성**을 클릭하여 **로그온 구성** 창을 엽니다. **빠른 참조** 섹션에서 **SAML 엔터티 ID**를 적어 둡니다.

9. **ServiceChannel** 측에서 Single Sign-On을 구성하려면 다운로드한 **인증서(Base64)** 및 **SAML 엔터티 ID**를 [ServiceChannel 지원 팀](https://servicechannel.zendesk.com/hc/en-us)에 보내야 합니다. 그러면 해당 팀에서 SAML SSO 연결이 양쪽에 제대로 설정되도록 설정합니다.

### <a name="creating-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션의 목적은 Azure 관리 포털에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure 관리 포털**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/servicechannel-tutorial/create_aaduser_01.png) 

2. **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭하여 사용자 목록을 표시합니다.
    
    ![Azure AD 테스트 사용자 만들기](./media/servicechannel-tutorial/create_aaduser_02.png) 

3. 대화 상자 위쪽에서 **추가**를 클릭하여 **사용자** 대화 상자를 엽니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/servicechannel-tutorial/create_aaduser_03.png) 

4. **사용자** 대화 상자 페이지에서 다음 단계를 수행합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/servicechannel-tutorial/create_aaduser_04.png) 

    a. **이름** 텍스트 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 텍스트 상자에 BrittaSimon의 **전자 메일 주소**를 입력합니다.

    다. **암호 표시**를 선택하고 **암호** 값을 적어둡니다.

    d. **만들기**를 클릭합니다. 

### <a name="creating-a-servicechannel-test-user"></a>ServiceChannel 테스트 사용자 만들기

응용 프로그램이 JIT(Just-in-time) 사용자 프로비저닝을 지원하며 인증 후에 응용 프로그램에서 사용자가 자동으로 만들어집니다. 전체 사용자 프로비전은 [ServiceChannel 지원 팀](https://servicechannel.zendesk.com/hc/en-us)에 문의하세요.

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 ServiceChannel에 대한 액세스 권한을 부여합니다.

![사용자 할당][200] 

**Britta Simon을 ServiceChannel에 할당하려면 다음 단계를 수행합니다.**

1. Azure 관리 포털에서 응용 프로그램 보기를 열고 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **ServiceChannel**을 선택합니다.

    ![Configure Single Sign-On](./media/servicechannel-tutorial/tutorial-servicechannel_app01.png) 

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202] 

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="testing-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 ServiceChannel 타일을 클릭하면 ServiceChannel 응용 프로그램에 자동으로 로그인됩니다.

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/servicechannel-tutorial/tutorial_general_01.png
[2]: ./media/servicechannel-tutorial/tutorial_general_02.png
[3]: ./media/servicechannel-tutorial/tutorial_general_03.png
[4]: ./media/servicechannel-tutorial/tutorial_general_04.png

[100]: ./media/servicechannel-tutorial/tutorial_general_100.png

[200]: ./media/servicechannel-tutorial/tutorial_general_200.png
[201]: ./media/servicechannel-tutorial/tutorial_general_201.png
[202]: ./media/servicechannel-tutorial/tutorial_general_202.png
[203]: ./media/servicechannel-tutorial/tutorial_general_203.png