---
title: '자습서: Ariba와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Asana 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 837e38fe-8f55-475c-87f4-6394dc1fee2b
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: jeedes
ms.openlocfilehash: b60f80353a6c2a67c5c1602987ba06aad8bd38e1
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36224830"
---
# <a name="tutorial-azure-active-directory-integration-with-asana"></a>자습서: Asana와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Asana를 통합하는 방법에 대해 알아봅니다.

Asana를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Asana에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 Asana에 자동으로 로그온(Single Sign-on)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](../manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

Asana와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Asana Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다.
이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Asana 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-asana-from-the-gallery"></a>갤러리에서 Asana 추가
Asana의 Azure AD 통합을 구성하려면 갤러리의 Asana를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Asana를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![엔터프라이즈 응용 프로그램 블레이드][2]

3. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![새 응용 프로그램 단추][3]

4. 검색 상자에 **Asana**를 입력하고 결과 패널에서 **Asana**를 선택한 후 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![Azure AD 테스트 사용자 만들기](./media/asana-tutorial/tutorial_asana_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 Asana에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Asana 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 Asana의 관련 사용자 간에 연결이 형성되어야 합니다.

Asana에서 Azure AD의 **사용자 이름** 값을 **Username** 값으로 할당하여 링크 관계를 설정합니다.

Asana에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[Asana 테스트 사용자 만들기](#create-an-asana-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Asana에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 Asana 응용 프로그램에서 Single Sign-On을 구성합니다.

**Asana에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **Asana** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Configure Single Sign-On][4]

2. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 대화 상자](./media/asana-tutorial/tutorial_asana_samlbase.png)

3. **Asana 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![Asana 도메인 및 URL Single Sign-On 정보](./media/asana-tutorial/tutorial_asana_url.png)

    a. **로그온 URL** 텍스트 상자에 `https://app.asana.com/` URL을 입력합니다.

    나. **식별자** 텍스트 상자에 `https://app.asana.com/` 값을 입력합니다.

4. **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 클릭한 후 컴퓨터에 인증서 파일을 저장합니다.

    ![인증서 다운로드 링크](./media/asana-tutorial/tutorial_asana_certificate.png)

5. **저장** 단추를 클릭합니다.

    ![Single Sign-On 구성 저장 단추](./media/asana-tutorial/tutorial_general_400.png)

6. **Asana 구성** 섹션에서 **Asana 구성**을 클릭하여 **로그온 구성** 창을 엽니다. **빠른 참조 섹션**에서 **SAML Single Sign-On 서비스 URL**을 복사합니다.

    ![Asana 구성](./media/asana-tutorial/tutorial_asana_configure.png)

7. 다른 브라우저 창에서 Asana 응용 프로그램에 로그온합니다. Asana에서 SSO를 구성하려면 화면 오른쪽 위 모서리에서 작업 영역 이름을 클릭하여 작업 영역 설정에 액세스합니다. 그런 다음 **\<작업 영역 이름\> 설정**을 클릭합니다.

    ![Asana SSO 설정](./media/asana-tutorial/tutorial_asana_09.png)

8. **조직 설정** 창에서 **관리**를 클릭합니다. 그런 다음 **멤버는 SAML을 통해 로그인해야 합니다** 를 클릭하여 SSO 구성을 활성화합니다. 다음 단계를 수행합니다.

    ![Single Sign-On 조직 설정 구성](./media/asana-tutorial/tutorial_asana_10.png)  

     a. **SAML Single Sign-On 서비스 URL**을 **로그인 페이지 URL** 텍스트 상자에 붙여 넣습니다.

     나. Azure Portal에서 다운로드한 인증서를 마우스 오른쪽 단추로 클릭한 후 메모장 또는 원하는 텍스트 편집기를 사용하여 인증서 파일을 엽니다. 시작 및 끝 인증서 제목 사이의 콘텐츠를 복사하고 **X.509 인증서**에 붙여 넣습니다.

9. **저장**을 클릭합니다. 추가 지원이 필요한 경우 [SSO 설정을 위한 Asana 가이드](https://asana.com/guide/help/premium/authentication#gl-saml) 로 이동합니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 테스트 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure Portal**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](./media/asana-tutorial/create_aaduser_01.png) 

2. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](./media/asana-tutorial/create_aaduser_02.png)

3. **사용자** 대화 상자를 열려면 대화 상자 위쪽에서 **추가**를 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/asana-tutorial/create_aaduser_03.png)

4. **사용자** 대화 상자 페이지에서 다음 단계를 수행합니다.

    ![추가 단추](./media/asana-tutorial/create_aaduser_04.png)

    a. **이름** 텍스트 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 텍스트 상자에 BrittaSimon의 **전자 메일 주소**를 입력합니다.

    다. **암호 표시**를 선택하고 **암호** 값을 적어둡니다.

    d. **만들기**를 클릭합니다.

### <a name="create-an-asana-test-user"></a>Asana 테스트 사용자 만들기

이 섹션은 Asana에서 Britta Simon이라는 사용자를 만들기 위한 것입니다. Asana는 자동 사용자 프로비전을 지원하며 기본적으로 사용하도록 설정되어 있습니다. 자동 사용자 프로비전 구성 방법에 대한 자세한 내용은 [여기](asana-provisioning-tutorial.md)에서 제공합니다.

**사용자를 수동으로 만들어야 할 경우 다음 단계를 수행합니다.**

이 섹션에서는 Asana에서 Britta Simon이라는 사용자를 만듭니다.

1. **Asana**에서 왼쪽 패널의 **팀** 섹션으로 이동합니다. 더하기 기호 단추를 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/asana-tutorial/tutorial_asana_12.png)

2. 텍스트 상자에 전자 메일 britta.simon@contoso.com을(를) 입력한 다음 **초대**를 선택합니다.

3. **초대 보내기**를 클릭합니다. 새 사용자는 자신의 전자 메일 계정에 전자 메일을 받게 됩니다. 계정을 만들고 계정의 유효성을 검사해야 합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Asana에 대한 액세스 권한을 부여합니다.

![사용자 역할 할당][200]

**Britta Simon을 Asana에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201]

2. 응용 프로그램 목록에서 **Asana**를 선택합니다.

    ![응용 프로그램 목록의 Asana 링크](./media/asana-tutorial/tutorial_asana_app.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    !["사용자 및 그룹" 링크][202]

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션은 Azure AD Single Sign-On을 테스트하기 위한 것입니다.

Asana 로그인 페이지로 이동합니다. 전자 메일 주소 텍스트 상자에서 전자 메일 주소 britta.simon@contoso.com을 삽입합니다. 암호 텍스트 상자를 비워 둔 다음 **로그인**을 클릭합니다. Azure AD 로그인 페이지로 리디렉션됩니다. Azure AD 자격 증명을 완료합니다. 이제 Asana에 로그인되었습니다.

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)
* [사용자 프로비저닝 구성](asana-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/asana-tutorial/tutorial_general_01.png
[2]: ./media/asana-tutorial/tutorial_general_02.png
[3]: ./media/asana-tutorial/tutorial_general_03.png
[4]: ./media/asana-tutorial/tutorial_general_04.png

[100]: ./media/asana-tutorial/tutorial_general_100.png

[200]: ./media/asana-tutorial/tutorial_general_200.png
[201]: ./media/asana-tutorial/tutorial_general_201.png
[202]: ./media/asana-tutorial/tutorial_general_202.png
[203]: ./media/asana-tutorial/tutorial_general_203.png
[10]: ./media/asana-tutorial/tutorial_general_060.png
[11]: ./media/asana-tutorial/tutorial_general_070.png