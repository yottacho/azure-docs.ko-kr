---
title: '자습서: LearnUpon과 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 LearnUpon 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: b11c6315-c79d-4f34-9610-bd17070ab7c7
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: 3409545169208adc87eb63358082afc6c6d8d575
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2018
ms.locfileid: "39042115"
---
# <a name="tutorial-azure-active-directory-integration-with-learnupon"></a>자습서: LearnUpon과 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 LearnUpon을 통합하는 방법에 대해 알아봅니다.

LearnUpon을 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- LearnUpon에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 LearnUpon에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](../manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

LearnUpon과 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- LearnUpon Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 얻을 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 LearnUpon 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-learnupon-from-the-gallery"></a>갤러리에서 LearnUpon 추가
LearnUpon의 Azure AD 통합을 구성하려면 갤러리의 LearnUpon을 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 LearnUpon을 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Active Directory][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![응용 프로그램][2]
    
3. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![응용 프로그램][3]

4. 검색 상자에 **LearnUpon**을 입력합니다.

    ![Azure AD 테스트 사용자 만들기](./media/learnupon-tutorial/tutorial_learnupon_search.png)

5. 결과 패널에서 **LearnUpon**을 선택하고 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![Azure AD 테스트 사용자 만들기](./media/learnupon-tutorial/tutorial_learnupon_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD Single Sign-on 구성 및 테스트
이 섹션에서는 “Britta Simon”이라는 테스트 사용자를 기반으로 LearnUpon에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 LearnUpon 사용자가 누군지 알고 있어야 합니다. 즉, Azure AD 사용자와 LearnUpon의 관련 사용자 간에 연결이 형성되어야 합니다.

LearnUpon에서 Azure AD의 **사용자 이름** 값을 **Username** 값으로 할당하여 링크 관계를 설정합니다.

LearnUpon에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On 테스트하는 데 사용합니다.
3. **[LearnUpon 테스트 사용자 만들기](#creating-a-learnupon-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 LearnUpon에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 LearnUpon 응용 프로그램에서 Single Sign-On을 구성합니다.

**LearnUpon에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **LearnUpon** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Configure Single Sign-On][4]

2. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Configure Single Sign-On](./media/learnupon-tutorial/tutorial_learnupon_samlbase.png)

3. **LearnUpon 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![Configure Single Sign-On](./media/learnupon-tutorial/tutorial_learnupon_url.png)

    **회신 URL** 텍스트 상자에 다음 패턴으로 URL을 입력합니다.`https://<companyname>.learnupon.com/saml/consumer`

    > [!NOTE] 
    > 이러한 값은 실제 값이 아닙니다. 이 값은 실제 회신 URL로 업데이트해야 합니다. 이 값을 얻으려면 [LearnUpon 지원 팀](https://www.learnupon.com/features/support/)에 문의합니다.



4. **SAML 서명 인증서** 섹션에서 **지문**을 찾습니다. - 이는 LearnUpon SAML 설정에 추가됩니다.

    ![Configure Single Sign-On](./media/learnupon-tutorial/tutorial_learnupon_certificate.png) 

5. **저장** 단추를 클릭합니다.

    ![Configure Single Sign-On](./media/learnupon-tutorial/tutorial_general_400.png)

6. **LearnUpon 구성** 섹션에서 **LearnUpon 구성**을 클릭하여 **로그온 구성** 창을 엽니다. **빠른 참조 섹션**에서 **로그아웃 URL, SAML 엔터티 ID 및 SAML Single Sign-On 서비스 URL**을 복사합니다.

    ![Configure Single Sign-On](./media/learnupon-tutorial/tutorial_learnupon_configure.png) 

7. 다른 브라우저 인스턴스를 열고 관리자 계정으로 LearnUpon에 로그인합니다. 

8. **설정** 탭을 클릭합니다.
   
    ![Configure Single Sign-On](./media/learnupon-tutorial/tutorial_learnupon_06.png)

9. **Single Sign On-SAML**을 클릭한 다음 **일반 설정**을 클릭하여 SAML 설정을 구성합니다.
   
    ![Configure Single Sign-On](./media/learnupon-tutorial/tutorial_learnupon_07.png) 

10. **일반 설정** 섹션에서 다음 단계를 수행합니다.
   
    ![Configure Single Sign-On](./media/learnupon-tutorial/tutorial_learnupon_08.png)  
  
    a. **사용**을 선택합니다.

    나. **버전**을 **2.0**으로 선택합니다.

    다. **건너뛰기 조건**으로 **아니오**를 선택합니다.

    d. **SAML 토큰 게시 매개 변수 이름** 텍스트 상자에서 확인하고 인증할 SAML 어설션을 포함하는 위에 표시된 SAML 소비자 URL에 대한 요청 게시 매개 변수의 이름을 입력합니다. 예를 들어 **SAMLResponse**입니다.

    e. **이름 식별자 형식** 텍스트 상자에서 사용자 식별자(메일 주소)(예: **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**)가 있는 SAML 어설션의 위치를 나타내는 값을 입력합니다.
  
    f. **ID 공급자 위치** 텍스트 상자에서 Azure Portal 로그인 화면에서 업로드된 아이콘을 클릭할 경우 사용자가 전송되는 위치를 나타내는 값을 입력합니다.
  
    g. Azure Portal에서 복사한 **로그아웃 URL**을 **로그아웃 URL** 텍스트 상자에 붙여넣습니다.
    
    h. **지문 관리**를 클릭한 다음 다운로드한 인증서의 지문을 업로드합니다.

11. **사용자 설정**을 클릭한 후 다음 단계를 수행합니다.
   
     ![Configure Single Sign-On](./media/learnupon-tutorial/tutorial_learnupon_11.png)  
 
    a. **이름 식별자 형식** 텍스트 상자에서 사용자 이름(예: **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**)이 있는 SAML 어설션의 위치를 알려주는 값을 입력합니다.
  
    나. **성 식별자 형식** 텍스트 상자에서 사용자 성(예: **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**)이 있는 SAML 어설션의 위치를 알려주는 값을 입력합니다.

> [!TIP]
> 이제 앱을 설정하는 동안 [Azure Portal](https://portal.azure.com) 내에서 이러한 지침의 간결한 버전을 읽을 수 있습니다.  **Active Directory > 엔터프라이즈 응용 프로그램** 섹션에서 이 앱을 추가한 후에는 **Single Sign-On** 탭을 클릭하고 맨 아래에 있는 **구성** 섹션을 통해 포함된 설명서에 액세스하면 됩니다. 포함된 설명서 기능에 대한 자세한 내용은 [Azure AD 포함된 설명서]( https://go.microsoft.com/fwlink/?linkid=845985)에서 확인할 수 있습니다.
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure Portal**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/learnupon-tutorial/create_aaduser_01.png) 

2. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.
    
    ![Azure AD 테스트 사용자 만들기](./media/learnupon-tutorial/create_aaduser_02.png) 

3. **사용자** 대화 상자를 열려면 대화 상자 위쪽에서 **추가**를 클릭합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/learnupon-tutorial/create_aaduser_03.png) 

4. **사용자** 대화 상자 페이지에서 다음 단계를 수행합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/learnupon-tutorial/create_aaduser_04.png) 

    a. **이름** 텍스트 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 텍스트 상자에 BrittaSimon의 **전자 메일 주소**를 입력합니다.

    다. **암호 표시**를 선택하고 **암호** 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="creating-a-learnupon-test-user"></a>LearnUpon 테스트 사용자 만들기

이 섹션은 LearnUpon에서 Britta Simon이라는 사용자를 만들기 위한 것입니다. LearnUpon은 Just-In-Time 프로비전을 지원하며 기본적으로 사용하도록 설정합니다.

이 섹션에 작업 항목이 없습니다. 새 사용자가 아직 존재하지 않는 경우 LearnUpon에 액세스하는 동안 만들어질 수 있습니다. [Azure AD Single Sign-On 구성](#configuring-azure-ad-single-single-sign-on)

>[!NOTE]
>사용자를 수동으로 만들어야 하는 경우 [LearnUpon 지원 팀](https://www.learnupon.com/features/support/)에 문의해야 합니다. 

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 LearnUpon에 대한 액세스 권한을 부여합니다.

![사용자 할당][200] 

**Britta Simon을 LearnUpon에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **LearnUpon**을 선택합니다.

    ![Configure Single Sign-On](./media/learnupon-tutorial/tutorial_learnupon_app.png) 

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202] 

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="testing-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 LearnUpon 타일을 클릭하면 LearnUpon 응용 프로그램에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](../user-help/active-directory-saas-access-panel-introduction.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/learnupon-tutorial/tutorial_general_01.png
[2]: ./media/learnupon-tutorial/tutorial_general_02.png
[3]: ./media/learnupon-tutorial/tutorial_general_03.png
[4]: ./media/learnupon-tutorial/tutorial_general_04.png

[100]: ./media/learnupon-tutorial/tutorial_general_100.png

[200]: ./media/learnupon-tutorial/tutorial_general_200.png
[201]: ./media/learnupon-tutorial/tutorial_general_201.png
[202]: ./media/learnupon-tutorial/tutorial_general_202.png
[203]: ./media/learnupon-tutorial/tutorial_general_203.png

