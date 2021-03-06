---
title: Azure Active Directory 장치 관리 FAQ | Microsoft Docs
description: Azure Active Directory 장치 관리 FAQ
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 10d6b81915432d9f41c0d4751f98cbf380ff2d13
ms.sourcegitcommit: dc646da9fbefcc06c0e11c6a358724b42abb1438
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39136573"
---
# <a name="azure-active-directory-device-management-faq"></a>Azure Active Directory 장치 관리 FAQ

**Q: Android 또는 iOS BYOD 장치를 등록할 수 있나요?**

**A:** 예, 하이브리드 고객만 Azure 장치 등록 서비스를 사용하여 등록할 수 있습니다. AD FS의 온-프레미스 장치 등록 서비스에서는 지원되지 않습니다.

**Q: macOS 장치를 등록하려면 어떻게 할까요?**

**A:** macOS 장치를 등록하려면:

1.  [규정 준수 정책 만들기](https://docs.microsoft.com/intune/compliance-policy-create-mac-os)
2.  [macOS 장치에 대한 조건부 액세스 정책 정의](active-directory-conditional-access-azure-portal.md) 

**설명**

- 조건부 액세스 정책에 포함된 사용자는 리소스에 액세스할 수 있는 [macOS에 대해 지원되는 버전의 Office](active-directory-conditional-access-technical-reference.md#client-apps-condition)가 필요합니다. 

- 첫 번째 액세스를 시도하는 동안 사용자가 회사 포털을 사용하여 장치를 등록하라는 메시지가 표시됩니다.

---

**Q: 최근에 장치를 등록했습니다. Azure Portal에서 내 사용자 정보에 장치가 표시되지 않는 이유는 무엇인가요?**

**A:** 하이브리드 Azure AD에 가입된 Windows 10 장치가 사용자 장치 아래에 표시되지 않습니다.
Azure Portal에서 모든 장치 보기를 사용해야 합니다. PowerShell [Get-MsolDevice](/powershell/module/msonline/get-msoldevice?view=azureadps-1.0) cmdlet을 사용할 수도 있습니다.

다음 장치만 사용자 장치 아래에 나열됩니다.

- 하이브리드 Azure AD에 가입되지 않은 모든 개인 장치 
- 모든 비 Windows 10/Windows Server 2016 장치
- 모든 비 Windows 장치 

--- 

**Q: 클라이언트의 장치 등록 상태를 어떻게 알 수 있나요?**

**A:** Azure Portal에서 모든 장치로 이동한 다음, 장치 ID를 사용하여 장치를 검색할 수 있습니다. 조인 유형 열 아래의 값을 확인합니다.

등록된 장치에서 로컬 장치 등록 상태를 확인하려면 다음을 수행합니다.

- Windows 10 및 Windows Server 2016 이상 장치의 경우, dsregcmd.exe /status를 실행합니다.
- 하위 수준 OS 버전의 경우 "%programFiles%\Microsoft Workplace Join\autoworkplace.exe"를 실행합니다.

---

**Q: Azure Portal에서 또는 Windows PowerShell을 사용하여 삭제했는데 장치의 로컬 상태가 여전히 등록되어 있다고 표시되는 이유는 무엇인가요?**

**A:** 이것은 의도적인 작동입니다. 장치는 클라우드의 리소스에 액세스할 수 없습니다. 

장치를 다시 등록하려면 장치에서 직접 작업을 수행해야 합니다. 

온-프레미스 AD 도메인에 가입된 Windows 10 및 Windows Server 2016에서 가입 상태를 지우려면

1.  관리자 권한으로 명령 프롬프트를 엽니다.

2.  `dsregcmd.exe /debug /leave`를 입력합니다.

3.  로그아웃했다가 로그인하여 장치를 Azure AD에 다시 등록하는 예약된 작업을 트리거합니다. 

온-프레미스 AD 도메인에 가입된 하위 수준 Windows OS 버전의 경우:

1.  관리자 권한으로 명령 프롬프트를 엽니다.
2.  `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"`을 입력합니다.
3.  `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"`을 입력합니다.

---
**Q: 장치에서 Azure AD 조인 장치를 로컬로 조인 해제하려면 어떻게 하나요?**

**A:** 
- 하이브리드 Azure AD 조인 장치의 경우, 예약된 작업이 장치를 다시 등록하지 않도록 자동 등록을 꺼야 합니다. 그런 다음, 관리자 권한으로 명령 프롬프트를 열고 `dsregcmd.exe /debug /leave`를 입력합니다. 또는 여러 장치에서 이 명령을 스크립트로 실행하여 대량으로 조인 해제할 수 있습니다.

- 순수한 Azure AD 조인 장치의 경우, Azure AD 사용자 자격 증명으로 로그인할 수 없으므로 오프라인 로컬 관리자 계정이 있는지 확인하거나 계정을 만들어야 합니다. 그런 다음, **설정** > **계정** > **회사 또는 학교 액세스**로 이동합니다. 계정을 선택하고 **연결 끊기**를 클릭합니다. 프롬프트를 따르고, 메시지가 표시되면 로컬 관리자 자격 증명을 제공합니다. 장치를 다시 부팅하여 조인 해제 프로세스를 완료합니다.

---

**Q: 사용자가 Azure AD 조인 장치에서 프린터를 검색할 수 없습니다. Azure AD 조인 장치에서 어떻게 인쇄를 사용하도록 설정할 수 있나요?**

**A:** Azure AD 조인 장치용 프린터를 배포하려면 [Hybrid cloud print](https://docs.microsoft.com/en-us/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy)(하이브리드 클라우드 인쇄)를 참조하세요. 하이브리드 클라우드 인쇄를 배포하려면 온-프레미스 Windows Server가 필요합니다. 현재는 클라우드 기반 인쇄 서비스를 사용할 수 없습니다. 

---

**Q: 원격 Azure AD 조인 장치에 연결하려면 어떻게 하나요?**
**A:** 자세한 내용은 https://docs.microsoft.com/en-us/windows/client-management/connect-to-remote-aadj-pc 문서를 참조하세요.

---

**Q: Azure Portal에서 중복된 장치 항목이 나타나는 이유는 무엇인가요?**

**A:**

-   Windows 10 및 Windows Server 2016의 경우 같은 장치를 반복해서 가입 해제했다가 다시 가입하면 중복된 항목이 나타날 수 있습니다. 

-   회사 또는 학교 계정 추가를 사용한 경우 회사 또는 학교 계정 추가를 사용하는 각 Windows 사용자는 장치 이름이 같은 새 장치 레코드를 만들게 됩니다.

-   자동 등록을 사용하여 온-프레미스 AD 도메인에 가입된 하위 수준 Windows OS 버전을 장치에 로그인하는 각 도메인 사용자에 대해 장치 이름이 같은 새 장치 레코드를 만듭니다. 

-   초기화되었다가 같은 이름으로 다시 설치되고 다시 가입된 Azure AD 가입 컴퓨터는 장치 이름이 같은 다른 레코드로 표시됩니다.

---

**Q: Azure Portal에서 사용하지 않도록 설정한 장치에서 여전히 리소스에 액세스할 수 있는 이유는 무엇인가요?**

**A:** 해지가 적용되는 데는 최대 1시간이 소요될 수 있습니다.

>[!Note] 
>등록된 장치의 경우에는 장치를 초기화하여 사용자가 리소스에 액세스하지 못하게 하는 것이 좋습니다. 자세한 내용은 [Intune에서 관리를 위해 장치 등록](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune)을 참조하세요. 


---

**Q: 사용자에게 "여기에서 해당 위치로 이동할 수 없습니다." 메시지가 표시되는 이유는 무엇인가요?**

**A:** 특정 장치 상태를 요구하도록 특정 조건부 액세스 규칙을 구성했거나 장치가 조건을 충족하지 않을 경우 사용자가 차단되고 다음 메시지가 표시됩니다. 조건부 액세스 정책 규칙을 평가하고 장치가 이 조건을 충족하는지 확인하여 이 메시지가 표시되지 않도록 하세요.

---

**Q: 일부 사용자가 Azure AD 조인 장치에서 MFA 프롬프트를 확인하지 못하는 이유는 무엇인가요?**

**A:** 사용자가 다단계 인증을 사용하여 Azure AD에 장치를 조인하거나 등록하는 경우, 장치 자체는 해당 특정 사용자에 대한 신뢰할 수 있는 두 번째 요소가 됩니다. 이후에 동일한 사용자가 장치에 로그인하고 응용 프로그램에 액세스할 때마다 Azure AD는 장치를 두 번째 요소로 간주하고 사용자가 추가 MFA 프롬프트 없이 응용 프로그램에 원활하게 액세스할 수 있도록 합니다. 이 동작은 해당 장치에 로그인하는 다른 사용자에게 적용되지 않으므로, 해당 장치에 액세스하는 다른 모든 사용자에게 MFA가 필요한 응용 프로그램에 액세스하기 전에 MFA 인증 질문이 표시됩니다.

---

**Q: Azure Portal에서 사용자 정보에 장치 레코드가 표시되고 장치에 등록된 상태로 표시됩니다. 조건부 액세스를 사용할 수 있게 제대로 설정되어 있는 것인가요?**

**A:** deviceID로 리플렉션된 장치 가입 상태는 Azure AD의 가입 상태와 일치해야 하며 조건부 액세스에 대한 평가 조건을 만족해야 합니다. 자세한 내용은 [Azure Active Directory Device 등록 시작](active-directory-device-registration.md)을 참조하세요.

---

**Q: Azure AD에 조인한 장치에 대해 "사용자 이름 또는 암호가 올바르지 않습니다." 메시지가 표시되는 이유는 무엇인가요?**

**A:** 이 시나리오에 대한 일반적인 이유는 다음과 같습니다.

- 사용자 자격 증명이 더 이상 유효하지 않습니다.

- 컴퓨터가 Azure Active Directory와 통신할 수 없습니다. 네트워크 연결 문제가 있는지 확인합니다.

- Azure AD 조인 필수 구성 요소가 충족되지 않았습니다. [Azure Active Directory 조인을 통해 클라우드 기능을 Windows 10 장치로 확장](active-directory-azureadjoin-overview.md)에 대한 단계를 따라야 합니다.  

- 페더레이션 로그인을 수행하려면 페더레이션 서버가 Ws-Trust 활성 끝점을 지원해야 합니다. 

- 통과 인증을 사용하도록 설정했으며, 사용자에게 로그온 시 변경해야 하는 임시 암호가 있습니다.

---

**Q: PC를 Azure AD에 가입하려고 할 때 "오류가 발생했습니다." 대화 상자가 표시되는 이유는 무엇인가요?**

**A:** Intune에 Azure Active Directory를 등록했기 때문입니다. Azure AD 가입을 시도하는 사용자에게 올바른 Intune 라이선스가 할당되어 있는지 확인하세요. 자세한 내용은 [Windows 장치 관리 설정](https://docs.microsoft.com/intune/deploy-use/set-up-windows-device-management-with-microsoft-intune#azure-active-directory-enrollment)을 참조하세요.  

---

**Q: 오류 정보가 표시되지 않더라도 PC 조인 시도가 실패하는 이유는 무엇인가요?**

**A:** 한 가지 가능한 원인은 사용자가 기본 제공 로컬 관리자 계정을 사용하여 장치에 로그인했기 때문입니다. Azure Active Directory 조인을 사용하여 설치를 완료하기 전에 다른 로컬 계정을 만드세요. 

---

**Q: 자동 장치 등록의 설치에 대한 지침은 어디에서 찾나요?**

**A:** 자세한 지침은 [Windows 도메인 가입 장치의 Azure Active Directory 자동 등록을 구성하는 방법](active-directory-conditional-access-automatic-device-registration-setup.md)을 참조하세요.

---

**Q: 자동 장치 등록에 대한 문제 해결 정보는 어디에서 찾나요?**

**A:** 문제 해결 정보는 다음을 참조하세요.

- [Windows 10 및 Windows Server 2016에 대한 Azure AD 도메인 조인 컴퓨터의 자동 등록 문제 해결](device-management-troubleshoot-hybrid-join-windows-current.md)

- [Windows 하위 수준 클라이언트에 대한 Azure AD 도메인 조인 컴퓨터의 자동 등록 문제 해결](device-management-troubleshoot-hybrid-join-windows-legacy.md)
 

---

