---
title: Problemen oplossen - IoT-rand voor Azure-gebeurtenisraster | Microsoft Documenten
description: Probleemoplossing in gebeurtenisraster op IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/24/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 95181d0eb23d5956b2c6af52c77f85714b107345
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73100159"
---
# <a name="common-issues"></a>Algemene problemen

Als u problemen ondervindt bij het gebruik van Azure Event Grid op IoT Edge in uw omgeving, gebruikt u dit artikel als een handleiding voor het oplossen van problemen en het oplossen van problemen.

## <a name="view-event-grid-module-logs"></a>Modulelogboeken voor gebeurtenisrasterweergeven

Als u problemen wilt oplossen, moet u mogelijk toegang krijgen tot logboeken van de gebeurtenisrastermodule. Voer hiervoor op de VM waar de module is geïmplementeerd de volgende opdracht uit:

Op Windows,

```sh
docker -H npipe:////./pipe/iotedge_moby_engine container logs eventgridmodule
```

Op Linux,

```sh
sudo docker logs eventgridmodule
```

## <a name="unable-to-make-https-requests"></a>Kan geen HTTPS-verzoeken indienen

* Controleer eerst of de module Event Grid **inbound:serverAuth:tlsPolicy** is ingesteld op **strikt** of **ingeschakeld.**

* Als de communicatie tussen module en module is, moet u ervoor zorgen dat u de aanroep op poort **4438** voert en dat de naam van de module overeenkomt met wat wordt geïmplementeerd. 

  Bijvoorbeeld als de module Gebeurtenisraster is geïmplementeerd met de naam **eventgridmodule,** moet uw URL . **https://eventgridmodule:4438** Zorg ervoor dat de behuizing en het poortnummer correct zijn.
    
* Als deze niet afkomstig is van een niet-IoT-module, controleert u of de gebeurtenisrasterpoort tijdens de implementatie in kaart is gebracht op de hostmachine,

    ```json
    "HostConfig": {
                "PortBindings": {
                  "4438/tcp": [
                    {
                        "HostPort": "4438"
                    }
                  ]
                }
     }
    ```

## <a name="unable-to-make-http-requests"></a>Kan http-aanvragen niet indienen

* Controleer eerst of de module Event Grid **inbound:serverAuth:tlsPolicy** is ingesteld **op ingeschakeld** of **uitgeschakeld.**

* Als de communicatie tussen module en module is, moet u ervoor zorgen dat u de aanroep op poort **5888** voert en dat de naam van de module overeenkomt met wat wordt geïmplementeerd. 

  Bijvoorbeeld als de module Gebeurtenisraster is geïmplementeerd met de naam **eventgridmodule,** moet uw URL . **http://eventgridmodule:5888** Zorg ervoor dat de behuizing en het poortnummer correct zijn.
    
* Als deze niet afkomstig is van een niet-IoT-module, controleert u of de gebeurtenisrasterpoort tijdens de implementatie in kaart is gebracht op de hostmachine,

    ```json
    "HostConfig": {
                "PortBindings": {
                  "5888/tcp": [
                    {
                        "HostPort": "5888"
                    }
                  ]
                }
    }
    ```

## <a name="certificate-chain-was-issued-by-an-authority-thats-not-trusted"></a>Certificaatketen is uitgegeven door een autoriteit die niet wordt vertrouwd

Standaard is de module Event Grid geconfigureerd om clients te verifiëren met een certificaat dat is uitgegeven door de IoT Edge-beveiligingsdaemon. Zorg ervoor dat de klant een certificaat presenteert dat is geworteld in deze keten.

**IoTSecurity-klasse** laat [https://github.com/Azure/event-grid-iot-edge](https://github.com/Azure/event-grid-iot-edge) zien hoe u certificaten ophalen bij IoT Edge Security-daemon en deze gebruiken om uitgaande oproepen te configureren.

Als het een niet-productieomgeving is, hebt u de optie om clientverificatie uit te schakelen. Raadpleeg [Beveiliging en verificatie](security-authentication.md) voor meer informatie over hoe u dit doen.

## <a name="debug-events-not-received-by-subscriber"></a>Foutopsporingsgebeurtenissen niet ontvangen door abonnee

Typische redenen hiervoor zijn:

* Het evenement is nooit succesvol geboekt. Er moet een HTTP-statuscode van 200(OK) zijn ontvangen bij het plaatsen van een gebeurtenis in de module Event Grid.

* Controleer het gebeurtenisabonnement om het te verifiëren:
    * De URL van eindpunt is geldig
    * Filters in het abonnement veroorzaken niet dat de gebeurtenis wordt "verwijderd".

* Controleren of de abonneemodule wordt uitgevoerd

* Meld u aan bij de VM waar de module Gebeurtenisraster wordt geïmplementeerd en bekijk de logboeken.

* Schakel per delivery logging in door **broker in te stellen:logDeliverySuccess=true** en de module Event Grid opnieuw te implementeren en de aanvraag opnieuw uit te proberen. Het inschakelen van logboekregistratie per levering kan van invloed zijn op de doorvoer en latentie, dus zodra debugging is voltooid, is het onze aanbeveling om dit terug te draaien naar **broker:logDeliverySuccess=false** en het opnieuw implementeren van event grid-module.

* Schakel statistieken in door **statistieken in te stellen:reportertype=console** en de module Gebeurtenisraster opnieuw te implementeren. Alle bewerkingen daarna resulteren in statistieken die worden geregistreerd op de console van de Event Grid-module, die kan worden gebruikt om verder te debuggen. Onze aanbeveling is om statistieken alleen in te schakelen voor foutopsporing en eenmaal voltooid om deze uit te schakelen door statistieken in te **stellen:reportertype=none** en de module Event Grid opnieuw te implementeren.

## <a name="next-steps"></a>Volgende stappen

Eventuele problemen, suggesties rapporteren met het gebruik [https://github.com/Azure/event-grid-iot-edge/issues](https://github.com/Azure/event-grid-iot-edge/issues)van gebeurtenisraster op IoT Edge op .