---
title: Status blijven in Windows - Azure Event Grid IoT Edge | Microsoft Documenten
description: Status blijven bestaan in Windows
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: c2bae3bd268dba8efdf23ae314671b17a2c89420
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086621"
---
# <a name="persist-state-in-windows"></a>Status blijven bestaan in Windows

Onderwerpen en abonnementen die zijn gemaakt in de module Gebeurtenisraster worden standaard opgeslagen in het containerbestandssysteem. Zonder volharding, als de module opnieuw wordt geïmplementeerd, zouden alle gemaakte metagegevens verloren gaan. Als u de gegevens voor implementaties en opnieuw opstarten wilt behouden, moet u de gegevens buiten het containerbestandssysteem blijven gebruiken. 

Standaard blijven alleen metagegevens bestaan en worden gebeurtenissen nog steeds opgeslagen in het geheugen voor betere prestaties. Volg de sectie persist events om ook de persistentie van gebeurtenissen mogelijk te maken.

In dit artikel vindt u de stappen die nodig zijn om de module Gebeurtenisraster te implementeren met volharding in Windows-implementaties.

> [!NOTE]
>De module Gebeurtenisraster wordt uitgevoerd als **containeruser met** een lage bevoorrechte gebruiker in Windows.

## <a name="persistence-via-volume-mount"></a>Persistentie via volumemount

[Dockervolumes](https://docs.docker.com/storage/volumes/) worden gebruikt om gegevens voor alle implementaties te bewaren. Als u een volume wilt monteren, moet u het maken met behulp van dockeropdrachten, machtigingen geven zodat de container kan lezen, schrijven en vervolgens de module kunnen implementeren.

1. Maak een volume door de volgende opdracht uit te voeren:

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume create <your-volume-name-here>
    ```

    Bijvoorbeeld:

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume create myeventgridvol
   ```
1. De hostmap weergeven waarnaar het volume wordt toegewezen door de onderstaande opdracht uit te voeren

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume inspect <your-volume-name-here>
    ```

    Bijvoorbeeld:

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume inspect myeventgridvol
   ```

   Voorbeelduitvoer:-

   ```json
   [
          {
            "CreatedAt": "2019-07-30T21:20:59Z",
            "Driver": "local",
            "Labels": {},
            "Mountpoint": "C:\\ProgramData\\iotedge-moby\u000bolumes\\myeventgridvol\\_data",
            "Name": "myeventgridvol",
            "Options": {},
            "Scope": "local"
          }
   ]
   ```
1. Voeg de groep **Gebruikers** toe aan de waarde die door **Mountpoint** als volgt wordt aangeduid:
    1. Start Verkenner.
    1. Navigeer naar de map die door **Mountpoint**is gericht .
    1. Klik met de rechtermuisknop en selecteer **Eigenschappen**.
    1. Selecteer **Beveiliging**.
    1. Selecteer Onder *Groeps- of gebruikersnamen de optie **Bewerken**.
    1. Selecteer **Toevoegen,** `Users`invoeren, selecteer Namen **controleren**en selecteer **Ok**.
    1. Selecteer Onder *Machtigingen voor gebruikers*de optie **Wijzigen**en selecteer **Ok**.
1. **Binds** gebruiken om dit volume te monteren en gebeurtenisrastermodule opnieuw te implementeren vanuit Azure-portal

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
                  "<your-volume-name-here>:C:\\app\\metadataDb"
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
   >Wijzig het tweede deel van de bindingswaarde niet. Het wijst naar een specifieke locatie in de module. Voor Event Grid module op windows, moet het **C:\\\\app metadataDb**.


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
                "myeventgridvol:C:\\app\\metadataDb",
                "C:\\myhostdir2:C:\\app\\eventsDb"
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

## <a name="persistence-via-host-directory-mount"></a>Persistentie via host directory mount

In plaats van een volume te monteren, u een map op het hostsysteem maken en die map monteren.

1. Maak een map op het hostbestandssysteem door de volgende opdracht uit te voeren.

   ```sh
   mkdir <your-directory-name-here>
   ```

   Bijvoorbeeld:

   ```sh
   mkdir C:\myhostdir
   ```
1. Gebruik **Bindingen** om uw directory te monteren en de module Gebeurtenisraster opnieuw te implementeren vanuit Azure-portal.

    ```json
    {
         "HostConfig": {
            "Binds": [
                "<your-directory-name-here>:C:\\app\\metadataDb"
             ]
         }
    }
    ```

    >[!IMPORTANT]
    >Wijzig het tweede deel van de bindingswaarde niet. Het wijst naar een specifieke locatie in de module. Voor de Event Grid module op windows, moet het **C:\\\\app metadataDb**.

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
                "C:\\myhostdir:C:\\app\\metadataDb",
                "C:\\myhostdir2:C:\\app\\eventsDb"
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
## <a name="persist-events"></a>Blijvende gebeurtenissen

Om gebeurtenispersistentie mogelijk te maken, moet u eerst het persistentie van gebeurtenissen inschakelen via volumemount of host directory mount met behulp van de bovenstaande secties.

Belangrijke dingen om op te merken over aanhoudende gebeurtenissen:

* Doorgaande gebeurtenissen zijn per gebeurtenisabonnement ingeschakeld en zijn opt-in zodra een volume of directory is gemonteerd.
* Gebeurtenispersistentie is geconfigureerd voor een gebeurtenisabonnement bij het maken van de tijd en kan niet worden gewijzigd zodra het gebeurtenisabonnement is gemaakt. Als u gebeurtenispersistentie wilt inschakelen, moet u het gebeurtenisabonnement verwijderen en opnieuw maken.
* Aanhoudende gebeurtenissen zijn bijna altijd trager dan bij geheugenbewerkingen, maar het snelheidsverschil is sterk afhankelijk van de kenmerken van de schijf. De afweging tussen snelheid en betrouwbaarheid is inherent aan alle berichtensystemen, maar wordt pas op grote schaal merkbaar.

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