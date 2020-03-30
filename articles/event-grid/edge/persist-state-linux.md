---
title: Persist status in Linux - Azure Event Grid IoT Edge | Microsoft Documenten
description: Persist metadata in Linux
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 12655d2ceb4a1124376d9bddf82194472c98ebb9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086652"
---
# <a name="persist-state-in-linux"></a>Persist staat in Linux

Onderwerpen en abonnementen die zijn gemaakt in de module Gebeurtenisraster worden standaard opgeslagen in het containerbestandssysteem. Zonder volharding, als de module opnieuw wordt geïmplementeerd, zouden alle gemaakte metagegevens verloren gaan. Als u de gegevens voor implementaties en opnieuw opstarten wilt behouden, moet u de gegevens buiten het containerbestandssysteem blijven gebruiken.

Standaard blijven alleen metagegevens bestaan en worden gebeurtenissen nog steeds opgeslagen in het geheugen voor betere prestaties. Volg de sectie persist events om ook de persistentie van gebeurtenissen mogelijk te maken.

In dit artikel vindt u de stappen om de Event Grid-module te implementeren met volharding in Linux-implementaties.

> [!NOTE]
>De module Gebeurtenisraster wordt uitgevoerd als een `2000` gebruiker `eventgriduser`met een lage bevoorrechte gebruiker met UID en naam .

## <a name="persistence-via-volume-mount"></a>Persistentie via volumemount

 [Dockervolumes](https://docs.docker.com/storage/volumes/) worden gebruikt om de gegevens voor alle implementaties te bewaren. U docker automatisch een benoemd volume laten maken als onderdeel van de implementatie van de module Gebeurtenisraster. Deze optie is de eenvoudigste optie. U de volumenaam die moet worden gemaakt in de sectie **Bindingen** als volgt opgeven:

```json
  {
     "HostConfig": {
          "Binds": [
                 "<your-volume-name-here>:/app/metadataDb"
           ]
      }
   }
```

>[!IMPORTANT]
>Wijzig het tweede deel van de bindingswaarde niet. Het wijst naar een specifieke locatie binnen de module. Voor de Event Grid module op Linux, moet het **/app/metadataDb**zijn.

De volgende configuratie zal bijvoorbeeld resulteren in het maken van het volume **egmetadataDbVol** waar metadata zal worden aangehouden.

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge",
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true",
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge",
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ],
  "HostConfig": {
    "Binds": [
      "egmetadataDbVol:/app/metadataDb",
      "egdataDbVol:/app/eventsDb"
    ],
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
```

In plaats van een volume te monteren, u een map op het hostsysteem maken en die map monteren.

## <a name="persistence-via-host-directory-mount"></a>Persistentie via host directory mount

In plaats van een dockervolume hebt u ook de mogelijkheid om een hostmap te monteren.

1. Maak eerst een gebruiker met naam **eventgriduser** en ID **2000** op de hostmachine door de volgende opdracht uit te voeren:

    ```sh
    sudo useradd -u 2000 eventgriduser
    ```
1. Maak een map op het hostbestandssysteem door de volgende opdracht uit te voeren.

   ```sh
   md <your-directory-name-here>
   ```

    Als u bijvoorbeeld de volgende opdracht uitvoert, wordt een map met de naam **myhostdir gemaakt.**

    ```sh
    md /myhostdir
    ```
1. Maak vervolgens **eventgriduser** eigenaar van deze map door de volgende opdracht uit te voeren.

   ```sh
   sudo chown eventgriduser:eventgriduser -hR <your-directory-name-here>
   ```

    Bijvoorbeeld:

    ```sh
    sudo chown eventgriduser:eventgriduser -hR /myhostdir
    ```
1. Gebruik **Bindingen** om de map te monteren en de module Gebeurtenisraster opnieuw te implementeren vanuit Azure-portal.

    ```json
    {
         "HostConfig": {
            "Binds": [
                "<your-directory-name-here>:/app/metadataDb",
                "<your-directory-name-here>:/app/eventsDb",
             ]
         }
    }
    ```

    Bijvoorbeeld:

    ```json
    {
          "Env": [
            "inbound__serverAuth__tlsPolicy=strict",
            "inbound__serverAuth__serverCert__source=IoTEdge",
            "inbound__clientAuth__sasKeys__enabled=false",
            "inbound__clientAuth__clientCert__enabled=true",
            "inbound__clientAuth__clientCert__source=IoTEdge",
            "inbound__clientAuth__clientCert__allowUnknownCA=true",
            "outbound__clientAuth__clientCert__enabled=true",
            "outbound__clientAuth__clientCert__source=IoTEdge",
            "outbound__webhook__httpsOnly=true",
            "outbound__webhook__skipServerCertValidation=false",
            "outbound__webhook__allowUnknownCA=true"
          ],
          "HostConfig": {
                "Binds": [
                  "/myhostdir:/app/metadataDb",
                  "/myhostdir2:/app/eventsDb"
                ],
                "PortBindings": {
                      "4438/tcp": [
                        {
                          "HostPort": "4438"
                        }
                      ]
                }
          }
    }
    ```

    >[!IMPORTANT]
    >Wijzig het tweede deel van de bindingswaarde niet. Het wijst naar een specifieke locatie binnen de module. Voor de Event Grid module op linux, moet het **/app/metadataDb** en **/app/eventsDb** zijn


## <a name="persist-events"></a>Blijvende gebeurtenissen

Om gebeurtenispersistentie mogelijk te maken, moet u eerst metadatapersistentie inschakelen via volumemount of host directory mount met behulp van de bovenstaande secties.

Belangrijke dingen om op te merken over aanhoudende gebeurtenissen:

* Doorgaande gebeurtenissen zijn per gebeurtenisabonnement ingeschakeld en zijn opt-in zodra een volume of directory is gemonteerd.
* Gebeurtenispersistentie is geconfigureerd voor een gebeurtenisabonnement bij het maken van de tijd en kan niet worden gewijzigd zodra het gebeurtenisabonnement is gemaakt. Als u gebeurtenispersistentie wilt inschakelen, moet u het gebeurtenisabonnement verwijderen en opnieuw maken.
* Aanhoudende gebeurtenissen zijn bijna altijd trager dan bij geheugenbewerkingen, maar het snelheidsverschil is sterk afhankelijk van de kenmerken van de schijf. De afweging tussen snelheid en betrouwbaarheid is inherent aan alle berichtensystemen, maar wordt over het algemeen alleen op grote schaal merkbaar.

Als u het voorzetten van `persistencePolicy` `true`een gebeurtenisabonnement wilt inschakelen, moet u instellen op:

 ```json
        {
          "properties": {
            "persistencePolicy": {
              "isPersisted": "true"
            },
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-webhook-url>"
              }
            }
          }
        }
 ```
