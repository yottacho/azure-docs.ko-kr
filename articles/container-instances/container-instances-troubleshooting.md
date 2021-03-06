---
title: Azure Container Instances 문제 해결
description: Azure Container Instances 관련 문제 해결 방법 알아보기
services: container-instances
author: seanmck
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 03/14/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 39c43c079ea4d10686bd656ba2d451ff42aac9f6
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34700233"
---
# <a name="troubleshoot-common-issues-in-azure-container-instances"></a>Azure Container Instances에서 일반적인 문제 해결

이 아티클에서는 컨테이너를 관리하거나 Azure Container Instances에 배포하는 경우 일반적인 문제를 해결하는 방법을 보여줍니다.

## <a name="naming-conventions"></a>명명 규칙

컨테이너 사양을 정의할 때 특정 매개 변수에는 명명 제한 사항을 준수해야 합니다. 컨테이너 그룹 속성에 대한 특정 요구 사항이 포함된 테이블은 다음과 같습니다.
Azure 명명 규칙에 대한 자세한 내용은 Azure 아키텍처 센터에서 [명명 규칙](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions)을 참조하세요.

| 범위 | 길이 | 대/소문자 구분 | 유효한 문자 | 제안된 패턴 | 예 |
| --- | --- | --- | --- | --- | --- | --- |
| 컨테이너 그룹 이름 | 1-64 |대/소문자 구분하지 않음 |첫 번째 또는 마지막 문자를 제외한 모든 위치의 영숫자 및 하이픈 |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| 컨테이너 이름 | 1-64 |대/소문자 구분하지 않음 |첫 번째 또는 마지막 문자를 제외한 모든 위치의 영숫자 및 하이픈 |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| 컨테이너 포트 | 1에서 65535 사이 |정수  |1에서 65535 사이의 정수 |`<port-number>` |`443` |
| DNS 이름 레이블 | 5-63 |대/소문자 구분하지 않음 |첫 번째 또는 마지막 문자를 제외한 모든 위치의 영숫자 및 하이픈 |`<name>` |`frontend-site1` |
| 환경 변수 | 1-63 |대/소문자 구분하지 않음 |첫 번째 또는 마지막 문자를 제외한 모든 위치의 영숫자 및 '_' 문자 |`<name>` |`MY_VARIABLE` |
| 볼륨 이름 | 5-63 |대/소문자 구분하지 않음 |첫 번째 또는 마지막 문자를 제외한 모든 위치의 소문자, 숫자 및 하이픈 두 개 연속 하이픈을 포함할 수 없습니다. |`<name>` |`batch-output-volume` |

## <a name="image-version-not-supported"></a>지원되지 않는 이미지 버전

Azure Container Instances에서 지원할 수 없는 이미지를 지정하면 `ImageVersionNotSupported` 오류가 반환됩니다. 오류 값이 `The version of image '{0}' is not supported.`이면 현재 Windows 1709 이미지에 적용됩니다. 이 문제를 해결하려면 LTS Windows 이미지를 사용합니다. Windows 1709 이미지에 대한 지원은 준비 중입니다.

## <a name="unable-to-pull-image"></a>이미지를 풀링할 수 없음

Azure Container Instances가 초기에 이미지를 풀링할 수 없는 경우 결과적으로 실패하기 전까지 당분간 재시도합니다. 이미지를 풀링할 수 없는 경우에는 다음과 같은 이미지가 [az container show][az-container-show]의 출력에 표시됩니다

```bash
"events": [
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "pulling image \"microsoft/aci-hellowrld\"",
    "name": "Pulling",
    "type": "Normal"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "Failed to pull image \"microsoft/aci-hellowrld\": rpc error: code 2 desc Error: image t/aci-hellowrld:latest not found",
    "name": "Failed",
    "type": "Warning"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:20+00:00",
    "lastTimestamp": "2017-12-21T22:57:16+00:00",
    "message": "Back-off pulling image \"microsoft/aci-hellowrld\"",
    "name": "BackOff",
    "type": "Normal"
  }
],
```

해결하려면 해당 컨테이너를 삭제하고 이미지 이름을 정확하게 입력했는 세심하게 주의하면서 배포를 다시 시도하세요.

## <a name="container-continually-exits-and-restarts"></a>컨테이너가 지속적으로 종료 후 다시 시작함

컨테이너가 완료되었다가 자동으로 다시 시작되면 [다시 시작 정책](container-instances-restart-policy.md)을 **OnFailure** 또는 **Never**로 설정해야 합니다. **OnFailure**를 지정해도 컨테이너가 계속 다시 시작되면 컨테이너에서 실행된 응용 프로그램이나 스크립트에 문제가 있을 수 있습니다.

컨테이너 인스턴스 API는 `restartCount` 속성을 포함합니다. 컨테이너에 대한 다시 시작 횟수를 확인하기 위해 Azure CLI 2.0에서 [az container show][az-container-show] 명령을 사용할 수 있습니다. 다음 예제 출력(간단히 하기 위해 일부 자름)에서는 출력 마지막에 `restartCount` 속성이 있습니다.

```json
...
 "events": [
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:06+00:00",
     "lastTimestamp": "2017-11-13T21:20:06+00:00",
     "message": "Pulling: pulling image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Pulled: Successfully pulled image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Created: Created container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Started: Started container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   }
 ],
 "previousState": null,
 "restartCount": 0
...
}
```

> [!NOTE]
> Linux 배포판에 대한 대부분 컨테이너 이미지는 기본 명령으로 Bash와 같은 셸을 설정합니다. 자체 셸은 장기 실행 서비스이므로, 기본값 **Always** 다시 시작 정책이 구성되었을 때 이러한 컨테이너는 즉시 종료된 후 다시 시작 루프를 시작합니다.

## <a name="container-takes-a-long-time-to-start"></a>컨테이너는 시작하는 데 오래 걸림

Azure Container Instances에서 컨테이너 시작 시간에 영향을 주는 두 가지 주요 요인은 다음과 같습니다.

* [이미지 크기](#image-size)
* [이미지 위치](#image-location)

Windows 이미지에는 [추가 고려 사항](#cached-windows-images)이 있습니다.

### <a name="image-size"></a>이미지 크기

컨테이너가 시작하는 데 오래 걸리지만, 결과적으로 성공할 경우 먼저 컨테이너 이미지의 크기를 살펴 보세요. Azure Container Instances가 요청 시 컨테이너 이미지를 풀링하므로, 경험하는 시작 시간은 크기와 직접 관련이 있습니다.

Docker CLI에서 `docker images` 명령을 사용하여 컨테이너 이미지의 크기를 볼 수 있습니다.

```console
$ docker images
REPOSITORY                  TAG       IMAGE ID        CREATED        SIZE
microsoft/aci-helloworld    latest    7f78509b568e    13 days ago    68.1MB
```

이미지 크기를 작게 유지하는 핵심 요소는 최종 이미지에 런타임 시 필요하지 않은 것은 아무 것도 포함하지 않는 것입니다. 이 작업을 수행하는 한 가지 방법은 [다단계 빌드][docker-multi-stage-builds]를 사용하는 것입니다. 다단계 빌드를 통해 간편하게 최종 이미지에 빌드 시간에 필요한 추가 콘텐츠가 아닌 응용 프로그램에 필요한 아티팩트만 포함하도록 할 수 있습니다.

### <a name="image-location"></a>이미지 위치

컨테이너 시작 시간에 이미지 풀의 영향을 줄이는 다른 방법은 Container Instances를 배포하려는 곳과 동일한 지역의 [Azure Container Registry](/azure/container-registry/)에서 컨테이너 이미지를 호스팅하는 것입니다. 이를 통해 컨테이너 이미지가 이동해야 하는 네트워크 경를 단축하여 다운로드 시간을 크게 줄여 줍니다.

### <a name="cached-windows-images"></a>캐시된 Windows 이미지

Azure Container Instances는 특정 Windows 이미지를 기반으로 하는 이미지에서 컨테이너 시작 시간을 신속하게 수행할 수 있는 캐싱 메커니즘을 사용합니다.

Windows 컨테이너 시작 시간을 단축하려면 다음과 같은 **두 개의 이미지**에서 **세 개의 최근** 버전 중 하나를 기본 이미지로 사용합니다.

* [Windows Server 2016][docker-hub-windows-core](LTS만 해당)
* [Windows Server 2016 Nano Server][docker-hub-windows-nano]

### <a name="windows-containers-slow-network-readiness"></a>Windows 컨테이너 느린 네트워크 준비

Windows 컨테이너를 처음 만들면 최대 5초 동안 인바운드 또는 아웃바운드 연결이 설정되지 않을 수 있습니다. 초기 설치 후에 컨테이너 네트워킹은 적절하게 다시 시작해야 합니다.

## <a name="resource-not-available-error"></a>리소스 사용할 수 없음 오류

Azure에서 다양한 지역별 리소스 부하로 인해 컨테이너 인스턴스 배포를 시도할 때 다음 오류가 발생할 수 있습니다.

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

이 오류는 배포하려는 지역에서 과부하로 인해 컨테이너에 대해 지정된 리소스를 해당 시간에 할당할 수 없음을 나타냅니다. 다음 위험 완화 단계 중 하나 이상을 사용하여 문제를 해결합니다.

* [Azure Container Instances에 대한 할당량 및 지역 가용성](container-instances-quotas.md#region-availability)에 정의된 매개 변수 내에 컨테이너 배포 설정이 속하는지 확인합니다.
* 컨테이너에 대해 낮은 CPU 및 메모리 설정 지정
* 다른 Azure 지역에 배포
* 나중에 배포

## <a name="next-steps"></a>다음 단계
컨테이너를 디버깅할 수 있도록 [컨테이너 로그 및 이벤트를 검색](container-instances-get-logs.md)하는 방법을 알아봅니다.

<!-- LINKS - External -->
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/
[docker-hub-windows-core]: https://hub.docker.com/r/microsoft/windowsservercore/
[docker-hub-windows-nano]: https://hub.docker.com/r/microsoft/nanoserver/

<!-- LINKS - Internal -->
[az-container-show]: /cli/azure/container#az_container_show