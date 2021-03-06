---
title: Azure Automation Runbook을 사용하여 오류 해결
description: Azure Automation Runbook을 사용하여 문제를 해결하는 방법 알아보기
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 07/13/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 286a777e16dea72e38b316e86ba57e1811888eec
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2018
ms.locfileid: "39044869"
---
# <a name="troubleshoot-errors-with-runbooks"></a>Runbook으로 오류 해결

## <a name="authentication-errors-when-working-with-azure-automation-runbooks"></a>Azure Automation Runbook을 사용할 때 인증 오류

### <a name="sign-in-failed"></a>시나리오: Azure 계정 로그인에 실패

#### <a name="issue"></a>문제

`Add-AzureAccount` 또는 `Connect-AzureRmAccount` cmdlet을 사용할 때 오류가 발생합니다.
:

```
Unknown_user_type: Unknown User Type
```

#### <a name="cause"></a>원인

이 오류는 자격 증명 자산 이름이 유효하지 않거나 Automation 자격 증명 자산을 설정하는 데 사용한 사용자 이름과 암호가 유효하지 않은 경우에 발생합니다.

#### <a name="resolution"></a>해결 방법

무엇이 문제인지 확인하기 위해 다음 단계를 수행합니다.  

1. Azure에 연결할 때 사용하는 Automation 자격 증명 자산에 **@** 문자를 비롯한 특수 문자가 들어 있지는 않은지 확인합니다.  
2. Azure Automation 자격 증명에 저장된 사용자 이름 및 암호를 로컬 PowerShell ISE 편집기에서 사용할 수 있는지 확인합니다. PowerShell ISE에서 다음 cmdlet을 실행하여 확인할 수 있습니다.  

   ```powershell
   $Cred = Get-Credential  
   #Using Azure Service Management   
   Add-AzureAccount –Credential $Cred  
   #Using Azure Resource Manager  
   Connect-AzureRmAccount –Credential $Cred
   ```

3. 인증이 로컬에서 실패하면 Azure Active Directory 자격 증명을 올바르게 설정하지 않았다는 뜻입니다. Azure Active Directory 계정을 올바르게 설정하는 방법은 [Azure Active Directory를 사용하여 Azure에 인증](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) 블로그 게시물을 참조하세요.  

### <a name="unable-to-find-subscription"></a>시나리오: Azure 구독을 찾을 수 없음

#### <a name="issue"></a>문제

`Select-AzureSubscription` 또는 `Select-AzureRmSubscription` cmdlet을 사용할 때 오류가 발생합니다.

```
The subscription named <subscription name> cannot be found.
```

#### <a name="error"></a>오류

이 오류는 구독 이름이 유효하지 않거나 구독 세부 정보를 가져오려는 Azure Active Directory 사용자가 구독 관리자로 구성되지 않은 경우에 발생합니다.

#### <a name="resolution"></a>해결 방법

Azure에 올바르게 인증하여 선택하려는 구독에 대한 액세스 권한을 획득했는지 확인하기 위해 다음 단계를 수행합니다.  

1. **Add-AzureAccount**를 실행한 후 **Select-AzureSubscription** cmdlet을 실행합니다.  
2. 이 오류 메시지가 계속 나타나면 **Add-AzureAccount** 뒤에 **Get-AzureSubscription** cmdlet을 추가하여 코드를 수정한 다음 코드를 실행합니다. 이제 Get-AzureSubscription의 출력에 구독 세부 정보가 포함되었는지 확인합니다.  

   * 출력에 구독 세부 정보가 보이지 않으면 아직 구독이 초기화되지 않았다는 뜻입니다.  
   * 출력에 구독 세부 정보가 보이면 **Select-AzureSubscription** cmdlet을 사용하여 올바른 구독 이름 또는 ID를 사용하고 있는지 확인합니다.

### <a name="auth-failed-mfa"></a>시나리오: Multi-Factor Authentication이 활성화되어 Azure 인증에 실패

#### <a name="issue"></a>문제

Azure 사용자 이름 및 암호를 사용하여 Azure에 인증할 때 다음 오류가 발생합니다.

```
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

#### <a name="cause"></a>원인

Azure 계정에서 Multi-Factor Authentication을 사용하면 Azure Active Directory 사용자를 사용하여 Azure에 인증할 수 없습니다. 그 대신 인증서 또는 서비스 주체를 사용하여 Azure에 인증해야 합니다.

#### <a name="resolution"></a>해결 방법

Azure 클래식 배포 모델 cmdlet에 인증서를 사용하려면 [인증서를 만들고 추가하여 Azure 서비스 관리](http://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx)를 참조하세요. Azure Resource Manager cmdlet에 서비스 주체를 사용하려면 [Azure Portal을 사용하여 서비스 주체 만들기](../../azure-resource-manager/resource-group-create-service-principal-portal.md) 및 [Azure Resource Manager를 사용하여 서비스 주체 인증](../../azure-resource-manager/resource-group-authenticate-service-principal.md)을 참조하세요.

## <a name="common-errors-when-working-with-runbooks"></a>Runbook을 사용할 때 발생하는 일반적인 오류

### <a name="not-recognized-as-cmdlet"></a>시나리오: 누락된 cmdlet으로 인해 Runbook이 실패합니다.

#### <a name="issue"></a>문제

다음 예제와 비슷한 오류가 발생하여 Runbook이 실패합니다.

```
The term 'Connect-AzureRmAccount' is not recognized as the name of a cmdlet, function, script file, or operable program.  Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

#### <a name="cause"></a>원인

이 오류는 다음과 같은 원인으로 인해 발생할 수 있습니다.

1. cmdlet을 포함하는 모듈을 자동화 계정으로 가져오지 않습니다.
2. cmdlet을 포함하는 모듈을 가져오지만 만료되었습니다.

#### <a name="resolution"></a>해결 방법

다음 작업 중 하나를 완료하여 이 오류를 해결할 수 있습니다.

모듈이 Azure 모듈인 경우 [Azure Automation에서 Azure PowerShell 모듈을 업데이트하는 방법](../automation-update-azure-modules.md)을 참조하여 자동화 계정에서 모듈을 업데이트하는 방법을 알아봅니다.

별도 모듈인 경우 모듈을 Automation 계정에 가져왔는지 확인합니다.

### <a name="job-attempted-3-times"></a>시나리오: Runbook 작업 시작을 세 번 시도했지만 매번 시작하지 못했습니다.

#### <a name="issue"></a>문제

다음 오류로 인해 Runbook이 실패합니다.

```
The job was tried three times but it failed
```

#### <a name="cause"></a>원인

이 오류는 다음과 같은 원인으로 인해 발생할 수 있습니다.

1. 메모리 제한. 샌드박스 [Automation 서비스 제한](../../azure-subscription-service-limits.md#automation-limits)에 할당 된 메모리 양에 대한 제한이 문서화되어 있으므로 400MB 이상의 메모리를 사용하는 경우 작업이 실패할 수 있습니다.

2. 모듈이 호환되지 않음. 이는 모듈 종속성이 올바르지 않은 경우에 발생할 수 있으며 그렇지 않은 경우 일반적으로 Runbook은 "명령을 찾을 수 없습니다." 또는 "매개 변수를 바인딩할 수 없습니다." 메시지를 반환합니다.

#### <a name="resolution"></a>해결 방법

다음 해결 방법 중 하나를 사용하여 문제를 해결합니다.

* 메모리 제한 내에서 작업하는 경우 여러 Runbook 간에 워크로드를 분할하고, 메모리에서 많은 데이터를 처리하지 않고, Runbook에서 불필요한 출력을 작성하지 않으며, PowerShell 워크플로 Runbook에 작성하는 검사점의 수를 고려하는 것이 좋습니다.  

* [Azure Automation에서 Azure PowerShell 모듈을 업데이트하는 방법](../automation-update-azure-modules.md) 단계에 따라 Azure 모듈을 업데이트합니다.  

### <a name="fails-deserialized-object"></a>시나리오: 역직렬화된 개체로 인해 Runbook 실패

#### <a name="issue"></a>문제

다음 오류로 인해 Runbook이 실패합니다.

```
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

#### <a name="cause"></a>원인

Runbook이 PowerShell 워크플로인 경우, 워크플로가 일시 중단되더라도 Runbook 상태를 유지하기 위해 복합 개체를 역직렬화된 형식으로 저장합니다.

#### <a name="resolution"></a>해결 방법

다음 세 가지 해결 방법 중 하나로 이 문제를 해결할 수 있습니다.

1. 한 cmdlet에서 다른 cmdlet으로 복합 개체를 파이핑하는 경우 이러한 cmdlet을 InlineScript에 래핑합니다.
2. 개체 전체를 전달하지 말고 복합 개체에서 필요한 이름 또는 값만 전달합니다.
3. PowerShell 워크플로 Runbook 대신 PowerShell Runbook을 사용합니다.

### <a name="quota-exceeded"></a>시나리오: 할당된 할당량을 초과하여 Runbook 작업 실패

#### <a name="issue"></a>문제

다음 오류로 인해 Runbook 작업이 실패합니다.

```
The quota for the monthly total job run time has been reached for this subscription
```

#### <a name="cause"></a>원인

작업 실행 시간이 계정에 할당된 무료 500분을 초과하면 이 오류가 발생합니다. 이 할당량은 작업을 테스트하고, 포털에서 작업을 시작하고, 웹 후크를 사용하여 작업을 실행하고, Azure 포털 또는 데이터 센터를 사용하여 실행할 작업을 예약하는 등의 모든 작업 실행에 적용됩니다. Automation 가격 책정에 대한 자세한 내용은 [Automation 가격 책정](https://azure.microsoft.com/pricing/details/automation/)을 참조하세요.

#### <a name="resolution"></a>해결 방법

매월 처리 시간을 500분 이상 사용하려면 구독을 무료 계층에는 기본 계층으로 변경해야 합니다. 다음 단계에 따라 기본 계층으로 업그레이드할 수 있습니다.  

1. Azure 구독에 로그인합니다.  
2. 업그레이드할 Automation 계정을 선택합니다.  
3. **설정** > **가격 책정**을 클릭합니다.
4. 페이지 아래쪽의 **사용**을 클릭하여 계정을 **기본** 계층으로 업그레이드합니다.

### <a name="cmdlet-not-recognized"></a>시나리오: Runbook을 실행해도 cmdlet이 인식되지 않음

#### <a name="issue"></a>문제

다음 오류로 인해 Runbook 작업이 실패합니다.

```
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

#### <a name="cause"></a>원인

PowerShell 엔진이 사용자가 Runbook에서 사용하는 cmdlet을 찾을 수 없는 경우에 이 오류가 발생합니다. cmdlet을 포함하고 있는 모듈이 계정에 없어서, Runbook 이름과 충돌하는 이름이 있어서 또는 다른 모듈에도 cmdlet이 있어서 Azure Automation이 이름을 확인할 수 없는 것이 원인일 수 있습니다.

#### <a name="resolution"></a>해결 방법

다음 해결 방법 중 하나를 사용하여 문제를 해결합니다.  

* cmdlet 이름을 올바르게 입력했는지 확인합니다.  
* Automation 계정에 Cmdlet이 있고 충돌이 없는지 확인합니다. cmdlet이 있는지 확인하려면 Runbook을 편집 모드로 열고 라이브러리에서 원하는 cmdlet을 검색하거나 `Get-Command <CommandName>`를 실행합니다. cmdlet을 계정에 사용할 수 있고 다른 cmdlet 또는 Runbook과 이름이 충돌하지 않는 것으로 확인되면 캔버스에 cmdlet을 추가하고 Runbook에 유효한 매개 변수 집합을 사용하고 있는지 확인합니다.  
* 이름이 충돌하고 cmdlet이 서로 다른 두 모듈에서 사용되는 경우 cmdlet에 대한 정규화된 이름을 사용하여 이 문제를 해결할 수 있습니다. 예를 들어 **ModuleName\CmdletName**을 사용할 수 있습니다.  
* Hybrid Worker 그룹에서 runbook 온-프레미스를 실행하는 경우 모듈/cmdlet이 Hybrid Worker를 호스트하는 시스템에 설치되어야 합니다.

### <a name="evicted-from-checkpoint"></a>시나리오: 장기 실행 runbook이 “동일한 검사점에서 반복적으로 제거되어 작업을 계속 실행할 수 없습니다.” 예외로 인해 지속적으로 실패함

#### <a name="issue"></a>문제

Azure Automation 내의 프로세스에 대한 “공평 분배” 모니터링으로 인해 의도적으로 설계된 동작입니다. 이 동작은 Runbook이 3시간 이상 실행되는 경우, 자동으로 일시 중단합니다. 그러나 반환된 오류 메시지에서는 "다음 단계" 옵션을 제공하지 않습니다.

#### <a name="cause"></a>원인

runbook은 다양한 이유로 일시 중단될 수 있습니다. 일시 중단은 주로 오류로 인해 발생합니다. 예를 들어 Runbook의 catch되지 않은 예외, 네트워크 오류 또는 Runbook을 실행하는 Runbook Worker의 작동 중단은 모두 Runbook을 일시 중단시키며, 재개 시 마지막 검사점에서 시작되도록 합니다.

#### <a name="resolution"></a>해결 방법

이 문제를 방지하기 위한 문서화된 해결 방법은 워크플로에서 검사점을 사용하는 것입니다. 자세한 내용은 [PowerShell 워크플로 학습](../automation-powershell-workflow.md#checkpoints)을 참조하세요. "공평 분배" 및 검사점에 대한 자세한 설명은 [Runbook에서 검사점 사용](https://azure.microsoft.com/blog/azure-automation-reliable-fault-tolerant-runbook-execution-using-checkpoints/) 블로그 문서에서 확인 수 있습니다.

### <a name="long-running-runbook"></a>시나리오: 장기 실행 Runbook이 완료되지 않음

#### <a name="issue"></a>문제

이 동작은 Azure Automation 내의 프로세스에 대한 “공평 분배” 모니터링으로 인해 Azure 샌드박스에서 의도적으로 설계된 동작으로, Runbook이 3시간 넘게 실행되는 경우, 자동으로 Runbook을 일시 중단합니다.

#### <a name="cause"></a>원인

Runbook이 Azure 샌드박스의 공평 분배에서 허용되는 3시간 한도를 초과하여 실행됨

#### <a name="resolution"></a>해결 방법

권장 솔루션은 [Hybrid Runbook Worker](../automation-hrw-run-runbooks.md)에서 Runbook을 실행하는 것입니다. Hybrid Worker는 Azure 샌드박스처럼 [공평 분배](../automation-runbook-execution.md#fair-share)의 3시간 Runbook 한도로 제한되지 않습니다.

## <a name="common-errors-when-importing-modules"></a>모듈을 가져올 때 발생하는 일반적인 오류

### <a name="module-fails-to-import"></a>시나리오: 모듈이 가져오기를 실패하거나 가져오기를 실행한 후 cmdlet을 실행할 수 없음

#### <a name="issue"></a>문제

모듈이 가져오기를 실패하거나 성공하더라도 cmdlet이 추출되지 않습니다.

#### <a name="cause"></a>원인

모듈이 Azure Automation을 가져오지 못하는 몇 가지 일반적인 이유는 다음과 같습니다.

* 구조가 Automation에서 요구하는 구조와 일치하지 않습니다.
* 모듈이 Automation 계정에 배포되지 않은 다른 모듈에 종속되어 있습니다.
* 모듈 폴더에 종속성이 없습니다.
* `New-AzureRmAutomationModule` cmdlet이 모듈 업로드에 사용되고 있으며, 사용자가 공개적으로 액세스 가능한 URL을 사용하여 저장소 전체 경로를 입력하거나 모듈을 로드하지 않았습니다.

#### <a name="resolution"></a>해결 방법

다음 해결 방법 중 하나를 사용하여 문제를 해결합니다.

* 모듈이 다음 형식을 따르는지 확인합니다. ModuleName.Zip **->** 모듈 이름 또는 버전 번호 **->** (ModuleName.psm1, ModuleName.psd1)
* .Psd1 파일을 열고 모듈에 종속성이 있는지 확인합니다. 종속성이 있으면 이러한 모듈을 Automation 계정에 업로드합니다.
* 모듈 폴더에 참조되는 .dll이 있는지 확인합니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우, 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* [Azure 포럼](https://azure.microsoft.com/support/forums/)을 통해 Azure 전문가로부터 답변을 얻습니다.
* [@AzureSupport](https://twitter.com/azuresupport)를 사용하여 연결 – Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결하여 고객 환경을 개선하는 공식 Microsoft Azure 계정입니다.
* 추가 지원이 필요한 경우, Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/) 로 가서 **지원 받기**를 선택합니다.