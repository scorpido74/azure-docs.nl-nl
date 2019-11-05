---
title: Azure Security Center implementeren voor IoT Edge module | Microsoft Docs
description: Meer informatie over het implementeren van een Azure Security Center voor IoT-beveiligings agent op IoT Edge.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/08/2019
ms.author: mlottner
ms.openlocfilehash: e85738c344189486726b4e7b7f5a76ab03c0ffa9
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/28/2019
ms.locfileid: "72991432"
---
# <a name="deploy-a-security-module-on-your-iot-edge-device"></a>Een beveiligings module op uw IoT Edge-apparaat implementeren


**Azure Security Center voor IOT** -module biedt een uitgebreide beveiligings oplossing voor uw IOT edge-apparaten.
De beveiligings module verzamelt, aggregeert en analyseert onbewerkte beveiligings gegevens van uw besturings systeem en container systeem in aanbevelingen voor beveiliging en waarschuwingen.
Zie [beveiligings module voor IOT Edge voor](security-edge-architecture.md)meer informatie.

In dit artikel leert u hoe u een beveiligings module implementeert op uw IoT Edge-apparaat.

## <a name="deploy-security-module"></a>Beveiligings module implementeren

Gebruik de volgende stappen om een Azure Security Center voor de IoT-beveiligings module voor IoT Edge te implementeren.

### <a name="prerequisites"></a>Vereisten

1. Zorg ervoor dat uw apparaat is [geregistreerd als een IOT edge-apparaat](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal)In uw IOT hub.

1. Voor Azure Security Center voor IoT Edge-module moet het [gecontroleerde Framework](https://linux.die.net/man/8/auditd) op het IOT edge-apparaat zijn geïnstalleerd.

    - Installeer het Framework door de volgende opdracht uit te voeren op uw IoT Edge-apparaat:
   
    `sudo apt-get install auditd audispd-plugins`

    - Controleer of controle actief is door de volgende opdracht uit te voeren: 
   
    `sudo systemctl status auditd`<br>
    - Het verwachte antwoord is: `active (running)` 
        

### <a name="deployment-using-azure-portal"></a>Implementatie met behulp van Azure Portal

1. Open **Marketplace**vanuit het Azure Portal.

1. Selecteer **Internet of Things**, zoek naar **Azure Security Center voor IOT** en selecteer deze.

   ![Azure Security Center selecteren voor IoT](media/howto/edge-onboarding-8.png)

1. Klik op **maken** om de implementatie te configureren. 

1. Kies het Azure- **abonnement** van uw IOT hub en selecteer vervolgens uw **IOT hub**.<br>Selecteer **implementeren op een apparaat** om één apparaat te richten of selecteer **implementeren op schaal** om meerdere apparaten te richten en klik op **maken**. Zie [How to deploy](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor)(Engelstalig) voor meer informatie over de implementatie op schaal. 

    >[!Note] 
    >Als u **implementeren op schaal**hebt geselecteerd, voegt u de naam van het apparaat en de details toe voordat u doorgaat met het tabblad **modules toevoegen** in de volgende instructies.     

Er zijn drie stappen voor het maken van een IoT Edge implementatie voor Azure Security Center voor IoT. In de volgende secties wordt elke stap uitgelegd. 

#### <a name="step-1-add-modules"></a>Stap 1: modules toevoegen

1. Klik op het tabblad **modules toevoegen** , gebied **implementatie modules** , op de optie **configureren** voor **AzureSecurityCenterforIoT**. 
   
1. Wijzig de **naam** in **azureiotsecurity**.
1. Wijzig de **afbeeldings-URI** in **MCR.Microsoft.com/ascforiot/azureiotsecurity:1.0.0**.
1. Controleer of de waarde voor het maken van de **container** is ingesteld op:      
    ``` json
    {
        "NetworkingConfig": {
            "EndpointsConfig": {
                "host": {}
            }
        },
        "HostConfig": {
            "Privileged": true,
            "NetworkMode": "host",
            "PidMode": "host",
            "Binds": [
                "/:/host"
            ]
        }
    }    
    ```
1. Controleer of **set de gewenste eigenschappen van de module** configureren is geselecteerd en wijzig het configuratie object in:
      
    ``` json
    { 
       "properties.desired":{ 
      "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration":{ 

          }
       }
    }
    ```

1. Klik op **Opslaan**.
1. Ga naar de onderkant van het tabblad en selecteer **Geavanceerde instellingen voor Edge-runtime configureren**. 
   
1. Wijzig de **afbeelding** onder **Edge hub** in **MCR.Microsoft.com/azureiotedge-hub:1.0.8.3**.

1. Controleer of de **Create-opties** zijn ingesteld op: 
         
    ``` json
    { 
       "HostConfig":{ 
          "PortBindings":{ 
             "8883/tcp":[ 
                { 
                   "HostPort":"8883"
                }
             ],
             "443/tcp":[ 
                { 
                   "HostPort":"443"
                }
             ],
             "5671/tcp":[ 
                { 
                   "HostPort":"5671"
                }
             ]
          }
       }
    }
    ```
1. Klik op **Opslaan**.
   
1. Klik op **Volgende**.

#### <a name="step-2-specify-routes"></a>Stap 2: routes opgeven 

1. Controleer op het tabblad **routes opgeven** of u een route (expliciet of impliciet) hebt waarmee berichten vanuit de **azureiotsecurity** -module worden doorgestuurd naar **$upstream** volgens de volgende voor beelden, en klik vervolgens op **volgende**. 

~~~Default implicit route
"route": "FROM /messages/* INTO $upstream" 
~~~

~~~Explicit route
"ASCForIoTRoute": "FROM /messages/modules/azureiotsecurity/* INTO $upstream"
~~~

#### <a name="step-3-review-deployment"></a>Stap 3: de implementatie controleren

- Controleer uw implementatie gegevens op het tabblad beoordeling van de **implementatie** en selecteer vervolgens **verzenden** om de implementatie te volt ooien.

## <a name="diagnostic-steps"></a>Diagnostische stappen

Als er een probleem optreedt, zijn container Logboeken de beste manier om meer te weten te komen over de status van een IoT Edge Security module-apparaat. Gebruik de opdrachten en hulpprogramma's in deze sectie om informatie te verzamelen.

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>Controleer of de vereiste containers zijn geïnstalleerd en werken zoals verwacht

1. Voer de volgende opdracht uit op uw IoT Edge-apparaat:
    
    `sudo docker ps`
   
1. Controleer of de volgende containers worden uitgevoerd:
   
   | Naam | BITMAPAFBEELDING |
   | --- | --- |
   | azureiotsecurity | mcr.microsoft.com/ascforiot/azureiotsecurity:1.0.1 |
   | edgeHub | mcr.microsoft.com/azureiotedge-hub:1.0.8.3 |
   | edgeAgent | mcr.microsoft.com/azureiotedge-agent:1.0.1 |
   
   Als de mini maal vereiste containers niet aanwezig zijn, controleert u of uw IoT Edge-implementatie manifest is afgestemd op de aanbevolen instellingen. Zie [IOT Edge module implementeren](#deployment-using-azure-portal)voor meer informatie.

### <a name="inspect-the-module-logs-for-errors"></a>De module Logboeken controleren op fouten
   
1. Voer de volgende opdracht uit op uw IoT Edge-apparaat:

   `sudo docker logs azureiotsecurity`
   
1. Voor uitgebreidere logboeken voegt u de volgende omgevings variabele toe aan de implementatie van de **azureiotsecurity** -module: `logLevel=Debug`.

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over configuratie opties gaat u naar de hand leiding voor het configureren van de module. 
> [!div class="nextstepaction"]
> [Hand leiding voor module configuratie](./how-to-agent-configuration.md)
