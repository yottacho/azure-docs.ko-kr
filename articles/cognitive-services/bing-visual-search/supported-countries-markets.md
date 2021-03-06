---
title: Bing Visual Search API에 대해 지원되는 국가 및 언어 | Microsoft Docs
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Bing Visual Search API에서 지원하는 국가 및 언어를 확인합니다.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 04/10/2018
ms.author: scottwhi
ms.openlocfilehash: 4723d028cc22caf8be3eb294b52506ec112cbab5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376415"
---
# <a name="bing-visual-search-countries-and-languages"></a>Bing Visual Search 국가 및 언어

Bing Visual Search API는 수십 개의 국가를 지원하며, 이 중에는 둘 이상의 언어를 사용하는 국가도 많습니다. 각 요청에는 선택한 사용자의 국가 및 언어가 포함되어야 합니다. 사용자의 시장을 알면 Bing에서 적절한 결과를 반환할 수 있습니다. 국가 및 언어를 지정하지 않으면 Bing은 사용자의 국가 및 언어를 확인하기 위해 최상의 노력을 기울입니다. 결과에 Bing에 대한 링크가 포함될 수 있기 때문에 사용자가 Bing 링크를 클릭하는 경우 국가 및 언어를 알고 있으면 기본 지역화된 Bing 사용자 환경을 제공할 수 있습니다.

국가 및 언어를 지정하려면 `mkt`(지역/국가) 쿼리 매개 변수를 아래 **지역/국가** 표의 코드로 설정합니다. 지역/국가는 국가와 언어를 둘 다 지정합니다. 사용자가 표시 텍스트를 다른 언어로 보려는 경우 `setLang` 쿼리 매개 변수를 해당 언어 코드로 설정합니다.

또는 `cc` 쿼리 매개 변수를 사용하여 국가를 지정할 수 있습니다. 국가를 지정하는 경우 `Accept-Language` HTTP 헤더를 사용하여 언어 코드도 하나 이상 지정해야 합니다. 지원되는 언어는 국가마다 다르며, 지역/국가 표에서 각 국가에 대해 제공됩니다.



> [!NOTE]
> 다음 시장 제한 사항이 적용됩니다.
> 
> - 이미지 인식 주석은 영어로만 제공됩니다. 
> - 레시피, 쇼핑 및 페이지 포함 인사이트는 en-US 시장에서만 사용할 수 있습니다. 


## <a name="countries"></a>국가

|국가|코드|
|-------|----|
|아르헨티나|AR|
|오스트레일리아|AU|
|오스트리아|AT|
|벨기에|BE|
|브라질|BR|
|캐나다|CA|
|칠레|CL|
|덴마크|DK|
|핀란드|FI|
|프랑스|FR|
|독일|DE|
|홍콩|HK|
|인도|IN|
|인도네시아|ID|
|이탈리아|IT|
|일본|JP|
|한국|KR|
|말레이시아|MY|
|멕시코|MX|
|네덜란드|NL|
|뉴질랜드|NZ|
|노르웨이|아니요|
|중국|CN|
|폴란드|PL|
|포르투갈|PT|
|필리핀|PH|
|러시아|RU|
|사우디아라비아|SA|
|남아프리카|ZA|
|스페인|ES|
|스웨덴|SE|
|스위스|CH|
|대만|TW|
|터키|TR|
|영국|GB|
|미국|US|


## <a name="markets"></a>시장

|국가|언어|시장 코드|
|-------|--------|-----------|
|아르헨티나|스페인어|es-AR|
|오스트레일리아|영어|en-AU|
|오스트리아|독일어|de-AT|
|벨기에|네덜란드어|nl-BE|
|벨기에|프랑스어|fr-BE|
|브라질|포르투갈어|pt-BR|
|캐나다|영어|en-CA|
|캐나다|프랑스어|fr-CA|
|칠레|스페인어|es-CL|
|덴마크|덴마크어|da-DK|
|핀란드|핀란드어|fi-FI|
|프랑스|프랑스어|fr-FR|
|독일|독일어|de-DE|
|홍콩|중국어 번체|zh-HK|
|인도|영어|en-IN|
|인도네시아|영어|en-ID|
|이탈리아|이탈리아어|it-IT|
|일본|일본어|ja-JP|
|한국|한국어|ko-KR|
|말레이시아|영어|en-MY|
|멕시코|스페인어|es-MX|
|네덜란드|네덜란드어|nl-NL|
|뉴질랜드|영어|en-NZ|
|중국|중국어|zh-CN|
|폴란드|폴란드어|pl-PL|
|포르투갈|포르투갈어|pt-PT|
|필리핀|영어|en-PH|
|러시아|러시아어|ru-RU|
|사우디아라비아|아랍어|ar-SA|
|남아프리카|영어|en-ZA|
|스페인|스페인어|es-ES|
|스웨덴|스웨덴어|sv-SE|
|스위스|프랑스어|fr-CH|
|스위스|독일어|de-CH|
|대만|중국어 번체|zh-TW|
|터키|터키어|tr-TR|
|영국|영어|en-GB|
|미국|영어|ko-KR|
|미국|스페인어|es-US|
