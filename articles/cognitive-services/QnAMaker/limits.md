---
title: QnA Maker 제한 - Azure Cognitive Services | Microsoft Docs
description: QnA Maker 제한
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: 4d2bafad08ffab76743cb802733a5d2f01a97f06
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2018
ms.locfileid: "35383226"
---
# <a name="qna-maker-limits"></a>QnA Maker 제한
QnA Maker에 대한 포괄적인 제한 목록입니다.

## <a name="knowledge-bases"></a>기술 자료

* [Azure Search 계층 제한](https://docs.microsoft.com/en-us/azure/search/search-limits-quotas-capacity)을 기반으로 하는 최대 기술 자료 수

|**Azure Search 계층** | **Free** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|허용된 최대 게시 기술 자료 수(최대 인덱스 - 1(테스트용으로 예약됨))|2|14|49|199|199|2999|

## <a name="extraction-limits"></a>추출 제한
* 추출할 수 있는 최대 파일 수 및 최대 파일 크기: [QnA Maker 가격](https://azure.microsoft.com/en-in/pricing/details/cognitive-services/qna-maker/) 참조
* FAQ HTML 페이지에서 QnA 추출을 위해 크롤링할 수 있는 최대 딥 링크 수: 20

## <a name="metadata-limits"></a>메타데이터 제한
* [Azure Search 계층 제한](https://docs.microsoft.com/en-us/azure/search/search-limits-quotas-capacity)을 기반으로 하는 기술 자료별 최대 메타데이터 필드 수

|**Azure Search 계층** | **Free** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|QnA Maker 서비스별 최대 메타데이터 필드 수(모든 기술 자료에서)|1000|100*|1000|1000|1000|1000|

## <a name="knowledge-base-content-limits"></a>기술 자료 콘텐츠 제한
기술 자료에서 전반적인 콘텐츠 제한 사항은 아래와 같습니다.
* 답변 텍스트 길이: 250000
* 질문 텍스트 길이: 1000
* 메타데이터 키/값 텍스트 길이: 100
* 메타데이터 이름의 지원되는 문자: 영문자, 숫자 및 _  
* 메타데이터 값의 지원되는 문자: 모든 문자(: 및 | 제외) 
* 파일 이름 길이: 200
* 지원되는 파일 형식: “.tsv”, “.pdf”, “.txt”, “.docx”, “.xlsx”
* 최대 대체 질문 수: 100
* 최대 질문-답변 쌍 수: 선택한 [Azure Search 계층](https://docs.microsoft.com/en-in/azure/search/search-limits-quotas-capacity#document-limits)에 따라 다름 

## <a name="create-knowledge-base-call-limits"></a>기술 자료 호출 제한 만들기
이는 각 기술 자료 만들기 작업(‘기술 자료 만들기’ 클릭 또는 CreateKnowledgeBase API 호출)에 대한 제한을 나타냅니다.
* 답변별 최대 대체 질문 수: 100
* 최대 URL 수: 10
* 최대 파일 수: 10

## <a name="update-knowledge-base-call-limits"></a>기술 자료 호출 제한 업데이트
이는 각 업데이트 작업(‘저장 및 학습’ 클릭 또는 UpdateKnowledgeBase API 호출)에 대한 제한을 나타냅니다.
* 각 원본 이름 길이: 300
* 추가 또는 삭제된 최대 대체 질문 수: 100
* 추가 또는 삭제된 최대 메타데이터 필드 수: 10
* 새로 고칠 수 있는 최대 URL 수: 5
