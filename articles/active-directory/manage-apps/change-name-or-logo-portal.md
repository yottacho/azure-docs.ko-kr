---
title: Azure Active Directory에서 엔터프라이즈 앱의 이름 또는 로고 변경 | Microsoft Docs
description: Azure Active Directory에서 사용자 지정 엔터프라이즈 앱에 대한 이름 또는 로고를 변경하는 방법
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: barbkess
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: ad424d6ca8ea8c35aa502a3d1bd98940591c38e8
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35302552"
---
# <a name="change-the-name-or-logo-of-an-enterprise-app-in-azure-active-directory"></a>Azure Active Directory에서 엔터프라이즈 앱의 이름 또는 로고 변경
Azure AD(Azure Active Directory)에서 사용자 정의 엔터프라이즈 응용 프로그램에 대한 이름 또는 로고를 변경하는 것은 쉽습니다. 이러한 변경을 수행하려면 적절한 권한이 있고 사용자 지정 앱에 대한 작성자여야 합니다.

## <a name="how-do-i-change-an-enterprise-apps-name-or-logo"></a>엔터프라이즈 앱의 이름 또는 로고를 변경하려면 어떻게 해야 합니까?
1. 디렉터리에 대한 전역 관리자인 계정으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **모든 서비스**를 선택하고 텍스트 상자에 **Azure Active Directory**를 입력한 다음, **입력**을 선택합니다.
3. **Azure Active Directory - *directoryname*** 창, 즉 관리 중인 디렉터리에 대한 Azure AD 창에서 **엔터프라이즈 응용 프로그램**을 선택합니다.

    ![엔터프라이즈 앱 열기](./media/change-name-or-logo-portal/open-enterprise-apps.png)
4. **엔터프라이즈 응용 프로그램** 창에서 **모든 응용 프로그램**을 선택합니다. 관리할 수 있는 앱의 목록이 표시됩니다.
5. **엔터프라이즈 응용 프로그램 - 모든 응용 프로그램** 창에서 앱을 선택합니다.
6. ***appname*** 창, 즉 제목에서 선택된 앱의 이름을 사용한 창에서 **속성**을 선택합니다.

    ![속성 명령 선택](./media/change-name-or-logo-portal/select-app.png)
7. ***appname*** **- 속성** 창에서 새 로고로 사용하거나 앱 이름 또는 둘 다를 편집할 파일을 찾습니다.

    ![앱 로고 또는 nameproperties 명령 변경](./media/change-name-or-logo-portal/change-logo.png)
8. **저장** 명령을 선택합니다.

## <a name="next-steps"></a>다음 단계
* [내 그룹 모두 보기](../active-directory-groups-view-azure-portal.md)
* [엔터프라이즈 앱에 사용자 또는 그룹 할당](assign-user-or-group-access-portal.md)
* [엔터프라이즈 앱에서 사용자 또는 그룹 할당 제거](remove-user-or-group-access-portal.md)
* [엔터프라이즈 앱에 대한 사용자 로그인 비활성화](disable-user-sign-in-portal.md)
