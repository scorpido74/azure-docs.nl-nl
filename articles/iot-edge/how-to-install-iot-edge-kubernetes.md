---
title: IoT Edge installeren op Kubernetes | Microsoft Docs
description: Meer informatie over het installeren van IoT Edge op Kubernetes met behulp van een lokale ontwikkel cluster omgeving
author: kgremban
manager: philmea
ms.author: veyalla
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d11d23cf7d96482028a9d3738196fc5a787fec91
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76510206"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>IoT Edge installeren op Kubernetes (preview-versie)

IoT Edge kan worden geïntegreerd met Kubernetes als een robuuste, Maxi maal beschik bare infrastructuur laag. Hiermee wordt een IoT Edge *aangepaste resource definitie* (CRD) geregistreerd bij de KUBERNETES-API-server. Daarnaast biedt het een *operator* (IOT Edge-agent) die de gewenste status van de Cloud Managed reconcilieert met de status van het lokale cluster.

De levens duur van de module wordt beheerd door de Kubernetes scheduler, die de beschik baarheid van modules onderhoudt en de plaatsing kiest. IoT Edge beheert het Edge-toepassings platform dat op de voor grond draait en doorlopend de gewenste status die is opgegeven in IoT Hub met de status op het Edge-cluster. Het rand toepassings model is nog steeds het vertrouwde model op basis van IoT Edge modules en routes. De operator IoT Edge agent voert *automatische* omzetting naar de Kubernetes native-constructies uit, zoals een Peul, implementaties, services enzovoort.

Hier volgt een architectuur diagram op hoog niveau:

![kubernetese Arch](./media/how-to-install-iot-edge-kubernetes/k8s-arch.png)

Elk onderdeel van de Edge-implementatie ligt binnen het bereik van een Kubernetes-naam ruimte die specifiek is voor het apparaat, waardoor het mogelijk is om dezelfde cluster bronnen te delen tussen apparaten met meerdere randen en hun implementaties.

>[!NOTE]
>IoT Edge op Kubernetes is in [open bare preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="install-locally-for-a-quick-test-environment"></a>Lokaal installeren voor een snelle test omgeving

### <a name="prerequisites"></a>Vereisten

* Kubernetes 1,10 of nieuwer. Als u geen bestaande cluster installatie hebt, kunt u [Minikube](https://kubernetes.io/docs/setup/minikube/) gebruiken voor een lokale cluster omgeving.

* [Helm](https://helm.sh/docs/using_helm/#quickstart-guide), de Kubernetes-pakket Manager.

* [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) voor het weer geven en communiceren met het cluster.

### <a name="setup-steps"></a>Instellingsstappen

1. **Minikube** starten

    ``` shell
    minikube start
    ```

1. Het **helm** Server-onderdeel (*Tiller*) in uw cluster initialiseren

    ``` shell
    helm init
    ```

1. IoT Edge opslag plaats toevoegen en de helm-installatie bijwerken

    ``` shell
    helm repo add edgek8s https://edgek8s.blob.core.windows.net/helm/
    helm repo update
    ```

1. [Maak een IOT hub](../iot-hub/iot-hub-create-through-portal.md), [Registreer een IOT edge-apparaat](how-to-register-device.md)en noteer de Connection String.

1. Iotedged-en IoT Edge-agent in uw cluster installeren

    ```shell
    helm install \
    --name k8s-edge1 \
    --set "deviceConnectionString=replace-with-device-connection-string" \
    edgek8s/edge-kubernetes
    ```

1. Het Kubernetes-dash board openen in de browser

    ```shell
    minikube dashboard
    ```

    Onder de cluster naam ruimten ziet u een voor het IoT Edge-apparaat volgens de Conventie *msiot-\<iothub-name >-\<edgedevice-name >* . De IoT Edge-agent en iotedged-peul moeten in deze naam ruimte worden uitgevoerd.

1. Voeg een gesimuleerde temperatuur sensor module toe met behulp van de stappen in de sectie [een module implementeren](quickstart-linux.md#deploy-a-module) van de Snelstartgids. IoT Edge module beheer wordt uitgevoerd vanaf de IoT Hub Portal, net als bij andere IoT Edge-apparaten. Het wordt niet aanbevolen lokale wijzigingen aan te brengen in module configuratie via Kubernetes-hulpprogram ma's, omdat deze mogelijk worden overschreven.

1. Over een paar seconden zal de pagina van de **peul** onder de naam ruimte Edge device in het dash board worden weer gegeven voor de IOT Edge hub en gesimuleerde sensor peulen, zoals uitgevoerd met de IOT Edge hub pod opnemen van gegevens in IOT hub.

## <a name="clean-up-resources"></a>Resources opschonen

Als u alle resources wilt verwijderen die zijn gemaakt door de Edge-implementatie, gebruikt u de volgende opdracht met de naam die u in stap 5 van de vorige sectie hebt gebruikt.

``` shell
helm delete --purge k8s-edge1
```

## <a name="next-steps"></a>Volgende stappen

### <a name="deploy-as-a-highly-available-edge-gateway"></a>Implementeren als een Maxi maal beschik bare Edge-gateway

Het edge-apparaat in een Kubernetes-cluster kan worden gebruikt als een IoT-gateway voor downstream-apparaten. Het kan zodanig worden geconfigureerd dat er een storing optreedt in het knoop punt, waardoor er hoge Beschik baarheid wordt geboden aan Edge-implementaties. Zie deze [gedetailleerde stapsgewijze instructies](https://github.com/Azure-Samples/iotedge-gateway-on-kubernetes) voor het gebruik van IOT Edge in dit scenario.
