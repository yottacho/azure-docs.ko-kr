---
title: Azure Media Services 동적 패키징 개요 | Microsoft Docs
description: 이 항목에서는 동적 패키징에 대해 간략하게 설명합니다.
author: Juliako
manager: cfowler
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 0d9e4f54-5daa-45c1-bfaa-cf09ca89b812
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: juliako
ms.openlocfilehash: 8f05015da1f66331413086c0e27c25cd5da75f5c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33783032"
---
# <a name="dynamic-packaging"></a>동적 패키징
## <a name="overview"></a>개요
Microsoft Azure Media Services를 사용하여 많은 미디어 소스 파일 형식, 미디어 스트리밍 형식 및 콘텐츠 보호 형식을 다양한 클라이언트 기술(예: iOS, XBOX, Silverlight, Windows 8)에 제공할 수 있습니다. 이러한 클라이언트는 여러 가지 프로토콜을 이해합니다. 예를 들어 iOS에는 HLS(HTTP 라이브 스트리밍) V4 형식이 필요하고 Silverlight와 Xbox에는 부드러운 스트리밍이 필요합니다. MPEG DASH, HLS 또는 부드러운 스트리밍을 이해하는 클라이언트에 제공하려는 적응 비트 전송률(다중 비트 전송률) MP4(ISO 기본 미디어 14496-12) 파일 집합이나 적응 비트 전송률 부드러운 스트리밍 파일 집합이 있는 경우 Media Services 동적 패키징을 이용해야 합니다.

동적 패키징을 사용하는 경우 적응 비트 전송률 MP4 파일 또는 적응 비트 전송률 부드러운 스트리밍 파일의 집합이 포함된 자산을 만들기만 하면 됩니다. 이렇게 하면 매니페스트 또는 조각 요청의 지정된 형식에 따라 주문형 스트리밍 서버는 사용자가 선택한 프로토콜로 스트림을 받을 수 있도록 합니다. 따라서 사용자는 단일 저장소 형식으로 파일을 저장하고 해당 파일에 대한 요금을 지불하기만 하면 되며, Media Services 서비스에서 클라이언트의 요청에 따라 적절한 응답을 작성하고 제공합니다.

다음 다이어그램에서는 기존의 인코딩 및 정적 패키징 워크플로를 보여 줍니다.

![정적 Encoding](./media/media-services-dynamic-packaging-overview/media-services-static-packaging.png)

다음 다이어그램에서는 동적 패키징 워크플로를 보여 줍니다.

![동적 Encoding](./media/media-services-dynamic-packaging-overview/media-services-dynamic-packaging.png)


## <a name="common-scenario"></a>일반적인 시나리오
1. 입력 파일(mezzanine 파일이라고 함)을 업로드합니다. H.264, MP4 또는 WMV를 예로 들 수 있습니다(지원되는 형식의 목록은 [미디어 인코더 표준에서 지원하는 형식](media-services-media-encoder-standard-formats.md)참조).
2. mezzanine 파일을 H.264 MP4 적응 비트 전송률 집합으로 인코딩합니다.
3. 주문형 로케이터를 만들어 적응 비트 전송률 MP4 집합이 포함된 자산을 게시합니다.
4. 콘텐츠에 액세스하고 콘텐츠를 스트리밍할 스트리밍 URL을 작성합니다.

## <a name="preparing-assets-for-dynamic-streaming"></a>동적 스트리밍을 위한 자산 준비
동적 스트리밍을 위해 자산을 준비하려면 다음 두 가지 옵션을 사용할 수 있습니다.

1. [마스터 파일을 업로드합니다](media-services-dotnet-upload-files.md).
2. [미디어 인코더 표준 인코더를 사용하여 H.264 MP4 적응 비트 전송률 집합을 생성합니다](media-services-dotnet-encode-with-media-encoder-standard.md).
3. [콘텐츠를 스트림합니다](media-services-deliver-content-overview.md).

## <a id="unsupported_formats"></a>동적 패키징에서 지원하지 않는 형식
다음과 같은 소스 파일 형식은 동적 패키징에서 지원하지 않습니다.

* Dolby Digital mp4 파일
* Dolby Digital 부드러운 파일

## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

