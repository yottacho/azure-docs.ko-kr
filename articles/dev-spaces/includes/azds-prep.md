---
title: 포함 파일
description: 포함 파일
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: d44f33b0e9f71c1d8d6e2c9878b08f9fa0e1f8a1
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38991018"
---
## <a name="preparing-code-for-docker-and-kubernetes-development"></a>Docker 및 Kubernetes 개발을 위한 코드 준비
지금까지 로컬로 실행할 수 있는 기본 웹앱이 있었습니다. 이제 앱의 컨테이너 및 Kubernetes에 배포되는 방법을 정의하는 자산을 만들어 컨테이너화합니다. 이 작업은 Azure Dev Spaces를 사용하여 쉽게 할 수 있습니다. 

1. VS Code를 시작하고 `webfrontend` 폴더를 엽니다. (디버그 자산을 추가하거나 프로젝트를 복원하라는 모든 기본 프롬프트를 무시할 수 있습니다.)
1. VS Code에서 통합 터미널을 엽니다(**보기 > 통합 터미널** 메뉴 사용).
1. 이 명령을 실행합니다(**webfrontend**가 현재 폴더인지 확인).

    ```cmd
    azds prep --public
    ```

Azure CLI의 `azds prep` 명령은 기본 설정으로 Docker 및 Kubernetes 자산을 생성합니다.
* `./Dockerfile`은 앱의 컨테이너 이미지 및 원본 코드가 빌드되는 방법을 설명하고 컨테이너 내에서 실행됩니다.
* `./charts/webfrontend` 아래의 [Helm 차트](https://docs.helm.sh)는 Kubernetes에 컨테이너를 배포하는 방법을 설명합니다.

지금은 이러한 파일의 전체 컨텐츠를 이해할 필요가 없습니다. 언급할 가치가 있지만 **코드 자산으로 동일한 Kubernetes 및 Docker 구성을 개발에서 프로덕션까지 사용할 수 있으므로 서로 다른 환경에서 더 나은 일관성을 제공합니다.**
 
또한 `./azds.yaml`이라는 파일이 `prep` 명령으로 생성되며, 이는 Azure Dev Spaces에 대한 구성 파일입니다. Azure에서 반복 개발 환경을 활성화하는 추가 구성으로 Docker 및 Kubernetes 아티팩트를 보완합니다.
