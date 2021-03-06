---
title: 호출 및 응답 - Azure Cognitive Services용 Python 빠른 시작, Bing Image Search API | Microsoft Docs
description: Azure의 Microsoft Cognitive Services에서 Bing Image Search API를 사용하여 신속하게 시작할 수 있도록 정보 및 코드 샘플을 가져옵니다.
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 9/21/2017
ms.author: v-jerkin
ms.openlocfilehash: 3b5d6a961ce4bcde8aaf73f1fbd30689a6c2c2d1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374023"
---
# <a name="call-and-response-your-first-bing-image-search-query-in-python"></a>호출 및 응답: Python에서 첫 번째 Bing Image Search 쿼리

사용자 검색 쿼리를 Bing에 전송하고 관련 이미지 목록을 돌려 받게 함으로써 Bing Images Search API는 Bing.com/Images와 비슷한 환경을 제공합니다.

이 연습에서는 Bing Image Search API를 호출하고 결과 JSON 개체를 사후 처리하는 간단한 예를 보여 줍니다. 자세한 내용은 [Bing Image Search 설명서](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)를 참조하세요.

바인더 배지 시작을 클릭하여 [MyBinder](https://mybinder.org)에서 Jupyter 노트북으로 이 예제를 실행할 수 있습니다. 

[![바인더](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingImageSearchAPI.ipynb)

## <a name="prerequisites"></a>필수 조건

**Bing Search API**를 사용하는 [Cognitive Services API 계정](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)이 있어야 합니다. 이 빠른 시작에는 [평가판](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)이면 충분합니다. 평가판을 활성화할 때 제공된 액세스 키가 필요하며, Azure 대시보드에서 유료 구독 키를 사용해도 됩니다.

## <a name="running-the-walkthrough"></a>연습 실행
연습을 계속하려면 Bing API 서비스에 대한 API 키에 `subscription_key`를 설정합니다.


```python
subscription_key = None
assert subscription_key
```

다음으로, `search_url` 엔드포인트가 올바른지 확인합니다. 이 문서 작성 시 Bing Search API에 대해 하나의 엔드포인트만 사용됩니다. 권한 부여 오류가 발생하는 경우 Azure 대시보드에서 Bing 검색 엔드포인트에 대해 이 값을 다시 확인합니다.


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/images/search"
```

강아지의 이미지를 찾으려면 `search_term`을 설정합니다.


```python
search_term = "puppies"
```

다음 블록은 Python에서 `requests` 라이브러리를 사용하여 Bing 검색 API를 호출하고 JSON 개체로 결과를 반환합니다. `headers` 사전을 통해 API 키를 전달하고 `params` 사전을 통해 검색 용어를 전달합니다. 검색 결과를 필터링하는 데 사용할 수 있는 옵션의 전체 목록을 보려면 [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) 설명서를 참조합니다.


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "license": "public", "imageType": "photo"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

`search_results` 개체에는 실제 이미지와 함께 관련 항목 같은 풍부한 메타데이터가 포함됩니다. 예를 들어 다음 코드 줄은 처음 16개 결과에 대해 썸네일 URL을 추출할 수 있습니다.


```python
thumbnail_urls = [img["thumbnailUrl"] for img in search_results["value"][:16]]
```

그런 다음, `PIL` 라이브러리를 사용하여 썸네일 이미지를 다운로드하고 `matplotlib` 라이브러리를 사용하여 썸네일 이미지를 $4 \times 4$ 그리드에 렌더링할 수 있습니다.


```python
%matplotlib inline
import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

f, axes = plt.subplots(4, 4)
for i in range(4):
    for j in range(4):
        image_data = requests.get(thumbnail_urls[i+4*j])
        image_data.raise_for_status()
        image = Image.open(BytesIO(image_data.content))        
        axes[i][j].imshow(image)
        axes[i][j].axis("off")
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Bing Image Search 단일 페이지 앱 자습서](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>참고 항목 

[Bing Image Search 개요](../overview.md)  
[사용해 보세요](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
[평가판 액세스 키 받기](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
[Bing Image Search API 참조](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
