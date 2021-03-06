---
title: Azure IoT Edge를 사용하여 Azure Machine Learning 배포 | Microsoft Docs
description: Azure Machine Learning을 Edge 장치에 모듈로 배포
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/25/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 47db87bf734674bd424fecd0f0f22bff9e2df5d5
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38299257"
---
# <a name="deploy-azure-machine-learning-as-an-iot-edge-module---preview"></a>Azure Machine Learning을 IoT Edge 모듈로 배포 - 미리 보기

비즈니스 논리를 직접 IoT Edge 장치에 구현하는 코드를 배포하려면 IoT Edge 모듈을 사용할 수 있습니다. 이 자습서에서는 시뮬레이션된 컴퓨터 온도 데이터에 따라 장치가 실패하는 경우를 예측하는 Azure Machine Learning 모듈을 배포하는 과정을 안내합니다. 

이 자습서에서 만드는 Azure Machine Learning 모듈은 장치에서 생성된 환경 데이터를 읽고, 메시지에 비정상 레이블을 지정하기도 합니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure Machine Learning 모듈 만들기
> * Azure 컨테이너 레지스트리에 모듈 컨테이너 푸시
> * Azure Machine Learning 모듈을 IoT Edge 장치에 배포
> * 생성된 데이터 보기

>[!NOTE]
>Azure IoT Edge의 Azure Machine Learning 모듈은 공개 미리 보기로 있습니다. 

## <a name="prerequisites"></a>필수 조건

이 자습서에서 작성한 Machine Learning 모듈을 테스트하려면 IoT Edge 장치가 필요합니다. [Linux](quickstart-linux.md) 또는 [Windows 장치](quickstart.md)용 빠른 시작에서 구성한 장치를 사용할 수 있습니다. 

Azure Machine Learning 모듈은 ARM 프로세서를 지원하지 않습니다.

개발 컴퓨터에 다음 필수 구성 요소가 있어야 합니다. 
* Azure Machine Learning 계정입니다. [Azure Machine Learning 계정 만들기 및 Azure Machine Learning Workbench 설치](../machine-learning/service/quickstart-installation.md#create-azure-machine-learning-services-accounts)의 지침을 따릅니다. 이 자습서에 사용하기 위해 워크벤치 응용 프로그램을 설치할 필요는 없습니다. 
* 컴퓨터에 있는 Azure ML용 모듈 관리입니다. 환경을 설정하고 계정을 만들려면 [모델 관리 설정](../machine-learning/desktop-workbench/deployment-setup-configuration.md)의 지침을 따릅니다.

### <a name="disable-process-identification"></a>프로세스 식별 사용 안 함

>[!NOTE]
>
> 미리 보기로 있는 동안 Azure Machine Learning은 IoT Edge에서 기본적으로 사용하도록 설정된 프로세스 식별 보안 기능을 지원하지 않습니다. 
> 이를 사용하지 않도록 설정하는 단계는 다음과 같습니다. 그러나 프로덕션 환경에서 사용하는 경우 이러한 단계는 적합하지 않습니다.

프로세스 식별을 사용하지 않도록 설정하려면 IoT Edge 디먼 구성의 **connect** 섹션에 **workload_uri** 및 **management_uri**에 대한 IP 주소와 포트를 제공해야 합니다.

먼저 IP 주소를 가져옵니다. 명령줄에 `ifconfig`를 입력하고 **docker0** 인터페이스의 IP 주소를 복사합니다.

IoT Edge 디먼 구성 파일을 편집합니다.

```cmd/sh
sudo nano /etc/iotedge/config.yaml
```

구성의 **connect** 섹션을 해당 IP 주소로 업데이트합니다. 예: 
```yaml
connect:
  management_uri: "http://172.17.0.1:15580"
  workload_uri: "http://172.17.0.1:15581"
```

구성의 **listen** 섹션에 동일한 주소를 입력합니다. 예: 

```yaml
listen:
  management_uri: "http://172.17.0.1:15580"
  workload_uri: "http://172.17.0.1:15581"
```

management_uri 주소를 사용하여 IOTEDGE_HOST 환경 변수를 만듭니다(영구적으로 설정하려면 `/etc/environment`에 추가). 예를 들어 다음과 같습니다.

```cmd/sh
export IOTEDGE_HOST="http://172.17.0.1:15580"
```


## <a name="create-the-azure-ml-container"></a>Azure ML 컨테이너 만들기
이 섹션에서는 학습된 모델 파일을 다운로드하고 이를 Azure ML 컨테이너로 변환합니다.

Azure ML용 모듈 관리를 실행하는 컴퓨터에서, GitHub에 있는 Azure ML IoT Toolkit의 [iot_score.py](https://github.com/Azure/ai-toolkit-iot-edge/blob/master/IoT%20Edge%20anomaly%20detection%20tutorial/iot_score.py) 및 [model.pkl](https://github.com/Azure/ai-toolkit-iot-edge/blob/master/IoT%20Edge%20anomaly%20detection%20tutorial/model.pkl)을 다운로드하고 저장합니다. 이 파일은 Iot Edge 장치에 배포할 학습된 기계 학습 모델을 정의합니다.

학습된 모델을 사용하여 IoT Edge 장치에 배포할 수 있는 컨테이너를 만듭니다. 다음과 같은 작업에 다음 명령을 사용합니다.

   * 장치를 등록합니다.
   * 매니페스트를 만듭니다.
   * *machinelearningmodule*이라는 Docker 컨테이너 이미지를 만듭니다.
   * AKS(Azure Kubernetes Service) 클러스터에 이미지를 배포합니다.

```cmd
az ml service create realtime --model-file model.pkl -f iot_score.py -n machinelearningmodule -r python
```

### <a name="view-the-container-repository"></a>컨테이너 리포지토리 보기

컨테이너 이미지가 성공적으로 만들어지고 기계 학습 환경과 연결된 Azure Container Registry에 저장되었는지 확인합니다.

1. [Azure Portal](https://portal.azure.com)에서 **모든 서비스**로 이동하여 **컨테이너 레지스트리**를 선택합니다.
2. 레지스트리를 선택합니다. 이름은 **mlcr**로 시작되어야 하며 모듈 관리를 설정하는 데 사용한 리소스 그룹, 위치 및 구독에 속합니다.
3. **액세스 키**를 선택합니다.
4. **로그인 서버**, **사용자 이름** 및 **암호**를 복사합니다.  Edge 장치에서 레지스트리에 액세스하려면 이러한 항목이 필요합니다.
5. **리포지토리** 선택
6. **machinelearningmodule** 선택
7. 이제 컨테이너의 전체 이미지 경로가 있습니다. 다음 섹션에서 사용하기 위해 이 이미지 경로를 적어둡니다. 다음과 같이 표시됩니다. **<registry_name>.azureacr.io/machinelearningmodule:1**

## <a name="deploy-to-your-device"></a>새 장치에 배포

1. [Azure Portal](https://portal.azure.com)에서 IoT Hub로 이동합니다.

1. **IoT Edge**로 이동하여 IoT Edge 장치를 선택합니다.

1. **모듈 설정**을 선택합니다.

1. **레지스트리 설정** 섹션에서 Azure 컨테이너 레지스트리에서 복사한 자격 증명을 추가합니다. 

   ![레지스트리 자격 증명 추가](./media/tutorial-deploy-machine-learning/registry-settings.png)

1. 이전에 IoT Edge 장치에 tempSensor 모듈을 배포한 경우 자동으로 입력될 수 있습니다. 모듈 목록에 없는 경우 모듈을 추가합니다.

    1. **추가**를 클릭하고 **IoT Edge 모듈**을 선택합니다.
    2. **이름** 필드에 `tempSensor`을 입력합니다.
    3. **이미지 URI** 필드에 `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0`를 입력합니다.
    4. **저장**을 선택합니다.

1. 만든 기계 학습 모듈을 추가합니다.

    1. **추가**를 클릭하고 **Azure Machine Learning 모듈**을 선택합니다.
    1. **이름** 필드에 `machinelearningmodule`을 입력합니다.
    1. **이미지** 필드에 이미지 주소(예: `<registry_name>.azurecr.io/machinelearningmodule:1`)를 입력합니다.
    1. **저장**을 선택합니다.

1. **모듈 추가** 단계로 돌아가서 **다음**을 선택합니다.

1. **경로 지정** 단계에서 다음 JSON을 텍스트 상자에 복사합니다. 첫 번째 경로는 모든 Azure Machine Learning 모듈이 사용하는 끝점인 "amlInput" 끝점을 통해 온도 센서에서 Machine Learning 모듈로 메시지를 전송합니다. 두 번째 경로는 Machine Learning 모듈에서 IoT Hub로 메시지를 전송합니다. 이 경로에서 “amlOutput”은 모든 Azure Machine Learning 모듈이 데이터를 출력하는 데 사용하는 끝점이고 ‘$upstream’은 IoT Hub를 나타냅니다.

    ```json
    {
        "routes": {
            "sensorToMachineLearning":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/machinelearningmodule/inputs/amlInput\")",
            "machineLearningToIoTHub": "FROM /messages/modules/machinelearningmodule/outputs/amlOutput INTO $upstream"
        }
    }
    ```

1. **다음**을 선택합니다.

1. **배포 검토** 단계에서 **제출**을 선택합니다.

1. 장치 세부 정보 페이지로 돌아가서 **새로 고침**을 선택합니다.  **tempSensor** 모듈 및 IoT Edge 런타임 모듈과 함께 실행되는 새로운 **machinelearningmodule**이 표시됩니다.

## <a name="view-generated-data"></a>생성된 데이터 보기

각 IoT Edge 모듈에서 생성된 메시지를 볼 수 있으며 IoT 허브에 전달된 메시지도 볼 수 있습니다.

### <a name="view-data-on-your-iot-edge-device"></a>IoT Edge 장치에서 데이터 보기

IoT Edge 장치에서 모든 개별 모듈에서 보낸 메시지를 볼 수 있습니다. 

Linux 장치에서 이러한 명령을 수행하는 경우 승격된 권한에 대해 `sudo`를 사용해야 할 수 있습니다.

1. IoT Edge 장치에서 모든 모듈을 봅니다.

   ```cmd/sh
   iotedge list
   ```

2. 특정 장치에서 보낸 메시지를 봅니다. 이전 명령의 출력에서 나온 모듈 이름을 사용합니다.

   ```cmd/sh
   iotedge logs <module_name> -f
   ```

### <a name="view-data-arriving-at-your-iot-hub"></a>IoT 허브에 도착한 데이터 보기

[IoT Hub 탐색기 도구](https://github.com/azure/iothub-explorer) 또는 [Visual Studio Code용 Azure IoT Toolkit 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)을 사용하여 IoT 허브에서 받는 장치-클라우드 메시지를 볼 수 있습니다.

다음 단계에서는 IoT 허브에 도착한 장치-클라우드 메시지를 모니터링하도록 Visual Studio Code를 설정하는 방법을 보여 줍니다. 

1. Visual Studio Code에서 **IoT Hub 장치**를 선택합니다.

2. **...** 를 선택하고 나서 메뉴에서 **IoT Hub 연결 문자열 설정**을 선택합니다.

   ![IoT Hub 장치 추가 메뉴](./media/tutorial-deploy-machine-learning/set-connection.png)

3. 페이지의 맨 위에 열리는 텍스트 상자에 IoT Hub의 iothubowner 연결 문자열을 입력합니다. IoT Edge 장치가 IoT Hub 장치 목록에 표시됩니다.

4. **...** 를 다시 선택한 후 **D2C 메시지 모니터링 시작**을 선택합니다.

5. 5초마다 tempSensor에서 보낸 메시지를 관찰합니다. 메시지 본문에 machinelearningmodule이 true 또는 false 값을 제공하는 **anomaly**라는 속성이 포함되어 있습니다. **AzureMLResponse** 속성에는 모델이 성공적으로 실행된 경우 값 "OK"가 포함됩니다.

   ![메시지 본문의 Azure ML 응답](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="clean-up-resources"></a>리소스 정리 

<!--[!INCLUDE [iot-edge-quickstarts-clean-up-resources](../../includes/iot-edge-quickstarts-clean-up-resources.md)] -->

권장되는 다음 아티클을 계속 진행하는 경우 지금까지 만든 리소스와 구성을 그대로 유지하고 다시 사용할 수 있습니다.

그렇지 않으면 요금이 부과되지 않도록 이 아티클에서 만든 로컬 구성과 Azure 리소스를 삭제할 수 있습니다. 

> [!IMPORTANT]
> Azure 리소스와 리소스 그룹을 삭제하면 되돌릴 수 없습니다. 일단 삭제되면 리소스 그룹 및 해당 그룹에 포함된 모든 리소스가 영구적으로 삭제됩니다. 잘못된 리소스 그룹 또는 리소스를 자동으로 삭제하지 않도록 해야 합니다. 보관할 리소스가 포함된 기존 리소스 그룹 내에 IoT Hub를 만든 경우 리소스 그룹을 삭제하지 말고 IoT Hub 리소스만 삭제하면 됩니다.
>

IoT Hub만 삭제하려면 허브 이름과 리소스 그룹 이름을 사용하여 다음 명령을 실행합니다.

```azurecli-interactive
az iot hub delete --name MyIoTHub --resource-group TestResources
```


이름으로 전체 리소스 그룹을 삭제하려면 다음을 수행합니다.

1. [Azure 포털](https://portal.azure.com) 에 로그인하고 **리소스 그룹**을 클릭합니다.

2. **이름을 기준으로 필터링...** 텍스트 상자에 IoT Hub가 들어 있는 리소스 그룹의 이름을 입력합니다. 

3. 결과 목록의 리소스 그룹 오른쪽에서 **...** 를 클릭한 다음, **리소스 그룹 삭제**를 클릭합니다.

<!--
   ![Delete](./media/iot-edge-quickstarts-clean-up-resources/iot-edge-delete-resource-group.png)
-->
4. 리소스 그룹을 삭제할지 확인하는 메시지가 표시됩니다. 리소스 그룹의 이름을 다시 입력하여 확인한 다음, **삭제**를 클릭합니다. 잠시 후, 리소스 그룹 및 해당 그룹에 포함된 모든 리소스가 삭제됩니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Machine Learning에서 제공하는 IoT Edge 모듈을 배포했습니다. Azure IoT Edge에서 데이터를 통해 비즈니스 통찰력을 얻는 데 도움이 되는 다른 방법을 알아보려면 다른 자습서 중 하나를 계속 진행할 수 있습니다.

> [!div class="nextstepaction"]
> [C# 코드를 사용하여 센서 데이터 필터링](tutorial-csharp-module.md)

<!--Links-->
[lnk-tutorial1-win]: quickstart.md
[lnk-tutorial1-lin]: quickstart-linux.md
