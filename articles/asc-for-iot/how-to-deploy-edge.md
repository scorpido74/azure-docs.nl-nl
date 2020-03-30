---
title: Azure Security Center voor IoT Edge-module implementeren| Microsoft Documenten
description: Meer informatie over het implementeren van een Azure Security Center voor IoT-beveiligingsagent op IoT Edge.
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
ms.date: 1/30/2020
ms.author: mlottner
ms.openlocfilehash: b2af392dc4dc848a099b8297bb58e7d4a7104fa6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76964036"
---
# <a name="deploy-a-security-module-on-your-iot-edge-device"></a>Een beveiligingsmodule implementeren op uw IoT Edge-apparaat


**Azure Security Center for IoT-module** biedt een uitgebreide beveiligingsoplossing voor uw IoT Edge-apparaten.
De beveiligingsmodule verzamelt, verzamelt en analyseert ruwe beveiligingsgegevens van uw besturingssysteem en containersysteem tot bruikbare beveiligingsaanbevelingen en waarschuwingen.
Zie [Beveiligingsmodule voor IoT Edge voor](security-edge-architecture.md)meer informatie.

In dit artikel leert u hoe u een beveiligingsmodule implementeert op uw IoT Edge-apparaat.

## <a name="deploy-security-module"></a>Beveiligingsmodule implementeren

Gebruik de volgende stappen om een Azure Security Center for IoT-beveiligingsmodule voor IoT Edge te implementeren.

### <a name="prerequisites"></a>Vereisten

1. Controleer in uw IoT-hub of uw apparaat is [geregistreerd als Een IoT Edge-apparaat.](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal)

1. Azure Security Center for IoT Edge-module vereist dat het [AuditD-framework](https://linux.die.net/man/8/auditd) is geïnstalleerd op het IoT Edge-apparaat.

    - Installeer het framework door de volgende opdracht uit te voeren op uw IoT Edge-apparaat:
   
    `sudo apt-get install auditd audispd-plugins`

    - Controleer of AuditD actief is door de volgende opdracht uit te voeren: 
   
    `sudo systemctl status auditd`<br>
    - Verwachte reactie is:`active (running)` 
        

### <a name="deployment-using-azure-portal"></a>Implementatie met Azure-portal

1. Open **Marketplace**via de Azure-portal.

1. Selecteer **Internet of Things**en zoek vervolgens naar Azure Security Center voor **IoT** en selecteer het.

   ![Azure Security Center voor IoT selecteren](media/howto/edge-onboarding-8.png)

1. Klik **op Maken** om de implementatie te configureren. 

1. Kies het **Azure-abonnement** van uw IoT-hub en selecteer vervolgens uw **IoT-hub.**<br>Selecteer **Implementeren op een apparaat** om één apparaat te **targeten** of selecteer Implementeren op schaal om meerdere apparaten te **targeten**en klik op Maken . Zie [Hoe u](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor)het implementeren implementeren voor meer informatie over het implementeren op schaal. 

    >[!Note] 
    >Als u **Deploy op schaal hebt**geselecteerd, voegt u de apparaatnaam en details toe voordat u in de volgende instructies verdergaat met het tabblad Modules **toevoegen.**     

Voltooi elke stap om uw IoT Edge-implementatie voor Azure Security Center voor IoT te voltooien. 

#### <a name="step-1-modules"></a>Stap 1: Modules

1. Selecteer de **AzureSecurityCenterforIoT-module.**
1. Wijzig op het tabblad **Module-instellingen** de **naam** in **azureiotsecurity.**
1. Voeg op het tabblad **Enviroment Variables** indien nodig een variabele toe (bijvoorbeeld foutopsporingsniveau).
1. Voeg op het tabblad **Opties voor containermaken** de volgende configuratie toe:

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
    
1. Voeg op het tabblad **Tweeinstellingen voor modules** de volgende configuratie toe:
      
    ``` json
      "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration":{}
    ```

1. Selecteer **Update**.

#### <a name="step-2-runtime-settings"></a>Stap 2: Runtime-instellingen

1. Selecteer **Runtime-instellingen**.
1. Wijzig **onder Randhub**de **afbeelding** in **mcr.microsoft.com/azureiotedge-hub:1.0.8.3**.
1. Controleer of **Opties voor maken** is ingesteld op de volgende configuratie: 
         
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
    
1. Selecteer **Opslaan**.
   
1. Selecteer **Volgende**.

#### <a name="step-3-specify-routes"></a>Stap 3: Routes opgeven 

1. Controleer op het tabblad **Routes opgeven** of u een route (expliciet of impliciet) hebt die berichten van de **azureiotsecuritymodule** doorstuurt naar **$upstream** volgens de volgende voorbeelden. Alleen wanneer de route op zijn plaats is, selecteert u **Volgende**.

   Voorbeeldroutes:

    ~~~Default implicit route
    "route": "FROM /messages/* INTO $upstream" 
    ~~~

    ~~~Explicit route
    "ASCForIoTRoute": "FROM /messages/modules/azureiotsecurity/* INTO $upstream"
    ~~~

1. Selecteer **Volgende**.

#### <a name="step-4-review-deployment"></a>Stap 4: Implementatie controleren

- Controleer op het tabblad **Implementatie controleren** uw implementatiegegevens en selecteer **Vervolgens Maken** om de implementatie te voltooien.

## <a name="diagnostic-steps"></a>Diagnostische stappen

Als u een probleem ondervindt, zijn containerlogboeken de beste manier om meer te weten te komen over de status van een IoT Edge-beveiligingsmoduleapparaat. Gebruik de opdrachten en hulpprogramma's in deze sectie om informatie te verzamelen.

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>Controleer of de vereiste containers zijn geïnstalleerd en functioneren zoals verwacht

1. Voer de volgende opdracht uit op uw IoT Edge-apparaat:
    
    `sudo docker ps`
   
1. Controleer of de volgende containers worden uitgevoerd:
   
   | Name | AFBEELDING |
   | --- | --- |
   | azureiotsecurity | mcr.microsoft.com/ascforiot/azureiotsecurity:1.0.2 |
   | edgeHub edgeHub | mcr.microsoft.com/azureiotedge-hub:1.0.8.3 |
   | edgeAgent | mcr.microsoft.com/azureiotedge-agent:1.0.1 |
   
   Als de minimaal vereiste containers niet aanwezig zijn, controleert u of uw IoT Edge-implementatiemanifest is afgestemd op de aanbevolen instellingen. Zie [IoT Edge-module implementeren](#deployment-using-azure-portal)voor meer informatie.

### <a name="inspect-the-module-logs-for-errors"></a>De modulelogboeken controleren op fouten
   
1. Voer de volgende opdracht uit op uw IoT Edge-apparaat:

   `sudo docker logs azureiotsecurity`
   
1. Voor meer verboselogboeken voegt u de volgende omgevingsvariabele `logLevel=Debug`toe aan de implementatie **van azureiotsecurity-modules:** .

## <a name="next-steps"></a>Volgende stappen

Ga voor meer informatie over configuratieopties verder naar de handleiding voor moduleconfiguratie. 
> [!div class="nextstepaction"]
> [Handleiding voor moduleconfiguratie](./how-to-agent-configuration.md)
