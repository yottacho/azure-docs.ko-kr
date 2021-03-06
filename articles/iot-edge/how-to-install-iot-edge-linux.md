---
title: Linux에서 Azure IoT Edge를 설치하는 방법 | Microsoft Docs
description: Linux에서 Azure IoT Edge 설치
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: kgremban
ms.openlocfilehash: 477fa7e10fab0afadb28d7749009f87e82d9b6ba
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39035397"
---
# <a name="install-the-azure-iot-edge-runtime-on-linux-x64"></a>Linux(x64)에서 Azure IoT Edge 런타임 설치

Azure IoT Edge 런타임은 모든 IoT Edge 장치에 배포되며, 세 가지 구성 요소가 있습니다. **IoT Edge 보안 디먼**은 Edge 장치에서 보안 표준을 제공하고 유지 관리합니다. 디먼은 부팅할 때마다 시작되며, IoT Edge 에이전트를 시작하여 장치를 부트스트랩합니다. **IoT Edge 에이전트**는 IoT Edge 허브를 포함하여 IoT Edge 장치에서 모듈을 쉽게 배포하고 모니터링할 수 있게 합니다. **IoT Edge 허브**는 IoT Edge 장치의 모듈 간 통신과 장치와 IoT Hub 간의 통신을 관리합니다.

이 아티클에서는 Linux x64(Intel/AMD) 에지 장치에 Azure IoT Edge 런타임을 설치하는 단계를 나열합니다.

>[!NOTE]
>Linux 소프트웨어 저장소의 패키지는 각 패키지에 있는 사용 조건에 따릅니다(/usr/share/doc/*package-name*). 패키지를 사용하기 전에 사용 조건을 읽어보세요. 패키지를 설치 및 사용하면 이러한 사용 조건에 동의하게 됩니다. 사용 조건에 동의하지 않는 경우, 패키지를 사용하지 마세요.

## <a name="register-microsoft-key-and-software-repository-feed"></a>Microsoft 키 및 소프트웨어 리포지토리 피드 등록

### <a name="ubuntu-1604"></a>Ubuntu 16.04

```cmd/sh
# Install repository configuration
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > ./microsoft-prod.list
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/

# Install Microsoft GPG public key
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

### <a name="ubuntu-1804"></a>Ubuntu 18.04

```cmd/sh
# Install repository configuration
curl https://packages.microsoft.com/config/ubuntu/18.04/prod.list > ./microsoft-prod.list
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/

# Install Microsoft GPG public key
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/

# Perform apt upgrade
sudo apt-get upgrade
```

## <a name="install-the-container-runtime"></a>컨테이너 런타임 설치 

Azure IoT Edge는 [OCI 호환][lnk-oci] 컨테이너 런타임을 사용합니다. 프로덕션 시나리오의 경우 아래에서 제공된 [Moby-based][lnk-moby] 엔진을 사용하는 것이 좋습니다. Azure IoT Edge에서 공식적으로 지원되는 유일한 컨테이너 엔진입니다. Docker CE/EE 컨테이너 이미지는 Moby 런타임과 호환 가능합니다.

apt-get을 업데이트합니다.

```bash
sudo apt-get update
```

Moby 엔진 및 CLI(명령줄 인터페이스)를 설치합니다. CLI는 개발에 유용하지만 프로덕션 배포에서는 선택 사항입니다.*

```bash
sudo apt-get install moby-engine
sudo apt-get install moby-cli
```

## <a name="install-the-azure-iot-edge-security-daemon"></a>Azure IoT Edge 보안 디먼 설치

또한 아래 명령은 표시되지 않은 경우 표준 버전의 **iothsmlib**를 설치합니다.

```bash
sudo apt-get update
sudo apt-get install iotedge
```

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Azure IoT Edge 보안 디먼 구성

디먼은 `/etc/iotedge/config.yaml`에 있는 구성 파일을 사용하여 구성할 수 있습니다. 이 파일은 기본적으로 쓰기 금지되어 있습니다 편집하려면 관리자 권한이 필요합니다.

```bash
sudo nano /etc/iotedge/config.yaml
```

에지 장치는 [장치 연결 문자열을 사용하여 수동으로][lnk-dcs] 또는 [Device Provisioning Service를 통해 자동으로][lnk-dps] 구성할 수 있습니다.

* 수동 구성의 경우, **수동** 프로비전 모드의 주석 처리를 제거합니다. **device_connection_string**의 값을 IoT Edge 장치의 연결 문자열로 업데이트합니다.

   ```yaml
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   # provisioning: 
   #   source: "dps"
   #   global_endpoint: "https://global.azure-devices-provisioning.net"
   #   scope_id: "{scope_id}"
   #   registration_id: "{registration_id}"
   ```

* 자동 구성의 경우 **dps** 프로비전 모드의 주석 처리를 제거합니다. **scope_id** 및 **registration_id**의 값을 IoT Hub DPS 인스턴스 및 TPM이 있는 IoT Edge 장치의 값으로 업데이트합니다. 

   ```yaml
   # provisioning:
   #   source: "manual"
   #   device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   provisioning: 
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "{scope_id}"
     registration_id: "{registration_id}"
   ```

구성에서 프로비전 정보를 입력한 후에 디먼을 다시 시작합니다.

```cmd/sh
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>성공적인 설치 확인

이전 섹션의 **수동 구성** 단계를 사용한 경우 IoT Edge 런타임을 장치에 성공적으로 프로비전하고 실행해야 합니다. **자동 구성** 단계를 사용한 경우 사용자를 대신하여 런타임에서 장치를 IoT 허브에 등록할 수 있도록 몇 가지 추가 단계를 수행해야 합니다. 다음 단계는 [Linux 가상 머신에서 시뮬레이트된 TPM 에지 장치 만들기 및 프로비전](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm)을 참조하세요.

다음을 사용하여 IoT Edge 디먼의 상태를 확인할 수 있습니다.

```cmd/sh
systemctl status iotedge
```

다음을 사용하여 디먼 로그를 검사합니다.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

다음을 사용하여 실행 중인 모듈을 나열합니다.

```cmd/sh
sudo iotedge list
```

## <a name="next-steps"></a>다음 단계

Edge 런타임을 제대로 설치하는 데 문제가 있는 경우, [문제 해결][lnk-trouble] 페이지를 확인하세요.

<!-- Links -->
[lnk-dcs]: how-to-register-device-portal.md
[lnk-dps]: how-to-auto-provision-simulated-device-linux.md
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
[lnk-trouble]: troubleshoot.md
