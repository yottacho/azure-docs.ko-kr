---
title: 포함 파일
description: 포함 파일
services: container-registry
author: mmacy
ms.service: container-registry
ms.topic: include
ms.date: 05/29/2018
ms.author: marsma
ms.custom: include file
ms.openlocfilehash: 92a5d162e7a0b2c752a2f8e9c5941edf43e539e3
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38991086"
---
| 리소스 | Basic | Standard | Premium |
|---|---|---|---|---|
| Storage | 10GiB | 100GiB| 500GiB |
| 최대 이미지 계층 크기 | 20GiB | 20GiB | 50GiB |
| 분당 ReadOps<sup>1, 2</sup> | 1,000 | 3,000 | 10000 |
| 분당 WriteOps<sup>1, 3</sup> | 100 | 500 | 2,000 |
| 다운로드 대역폭 MBps<sup>1</sup> | 30 | 60 | 100 |
| 업로드 대역폭 MBps<sup>1</sup> | 10 | 20 | 50 |
| Webhook | 2 | 10 | 100 |
| 지역에서 복제 | 해당 없음 | 해당 없음 | [지원됨](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication) |

<sup>1</sup> *ReadOps*, *WriteOps* 및 *대역폭*은 최소 추정치입니다. ACR은 사용량 요구에 따라 성능을 개선하려고 합니다.

<sup>2</sup> [docker pull](https://docs.docker.com/registry/spec/api/#pulling-an-image)은 이미지의 레이어 수와 매니페스트 검색을 더한 값에 따라 여러 읽기 작업으로 변환됩니다.

<sup>3</sup> [docker push](https://docs.docker.com/registry/spec/api/#pushing-an-image)는 푸시해야 하는 레이어 수에 따라 여러 쓰기 작업으로 변환됩니다. `docker push`에는 기존 이미지에 대해 매니페스트를 검색할 *ReadOps*가 포함되어 있습니다.
