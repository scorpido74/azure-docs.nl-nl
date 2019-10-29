---
title: Status persistent in Windows-Azure Event Grid IoT Edge | Microsoft Docs
description: Status voor persistent in Windows
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 485c6d4a92539a2ba67aece319c68d31649e8045
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992260"
---
# <a name="persist-state-in-windows"></a>Status voor persistent in Windows

Onderwerpen en abonnementen die zijn gemaakt in de Event Grid module, worden standaard opgeslagen in het container bestandssysteem. Zonder persistentie, als de module opnieuw wordt geïmplementeerd, worden alle meta gegevens die zijn gemaakt, verloren gegaan. Als u de gegevens in de implementatie wilt behouden, moet u de gegevens buiten het bestands systeem van de container bewaren. Op dit moment worden alleen meta gegevens persistent gemaakt. Gebeurtenissen worden opgeslagen in het geheugen. Als Event Grid module opnieuw wordt geïmplementeerd of opnieuw is gestart, gaan alle niet-bezorgde gebeurtenissen verloren.

In dit artikel worden de stappen beschreven die nodig zijn om Event Grid module te implementeren met persistentie in Windows-implementaties.

> [!NOTE]
>De Event Grid-module wordt uitgevoerd als een gebruiker met beperkte rechten **ContainerUser** in Windows.

## <a name="persistence-via-volume-mount"></a>Persistentie via volume koppeling

[Docker-volumes](https://docs.docker.com/storage/volumes/) worden gebruikt voor het bewaren van gegevens over implementaties. Als u een volume wilt koppelen, moet u dit maken met behulp van docker-opdrachten, machtigingen verlenen zodat de container er naar kan lezen, ernaar schrijven en de module vervolgens implementeren. Er is geen bepaling om automatisch een volume met de benodigde machtigingen voor Windows te maken. Het moet worden gemaakt voordat u implementeert.

1. Maak een volume door de volgende opdracht uit te voeren:

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume create <your-volume-name-here>
    ```

    Bijvoorbeeld:

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume create myeventgridvol
   ```
1. Haal de host-map op waaraan het volume is toegewezen door de onderstaande opdracht uit te voeren

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume inspect <your-volume-name-here>
    ```

    Bijvoorbeeld:

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume inspect myeventgridvol
   ```

   Voorbeeld uitvoer:-

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
1. Voeg als volgt de groep **gebruikers** toe aan waarde punt **koppel punt** :
    1. Start bestanden Verkenner.
    1. Ga naar de map die is gepointt door **koppel punt**.
    1. Klik met de rechter muisknop en selecteer **Eigenschappen**.
    1. Selecteer **beveiliging**.
    1. Selecteer onder * groeps-of gebruikers namen de optie **bewerken**.
    1. Selecteer **toevoegen**, voer `Users`, selecteer **Namen controleren**en selecteer **OK**.
    1. Selecteer onder *machtigingen voor gebruikers* **wijzigen**en selecteer **OK**.
1. Gebruik **bindingen** om dit volume te koppelen en Event grid module opnieuw te implementeren vanuit Azure Portal

   Bijvoorbeeld:

    ```json
        {
              "Env": [
                "inbound:serverAuth:tlsPolicy=strict",
                "inbound:serverAuth:serverCert:source=IoTEdge",
                "inbound:clientAuth:sasKeys:enabled=false",
                "inbound:clientAuth:clientCert:enabled=true",
                "inbound:clientAuth:clientCert:source=IoTEdge",
                "inbound:clientAuth:clientCert:allowUnknownCA=true",
                "outbound:clientAuth:clientCert:enabled=true",
                "outbound:clientAuth:clientCert:source=IoTEdge",
                "outbound:webhook:httpsOnly=true",
                "outbound:webhook:skipServerCertValidation=false",
                "outbound:webhook:allowUnknownCA=true"
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
   >Wijzig het tweede deel van de bindings waarde niet. Deze verwijst naar een specifieke locatie in de module. Voor de module Event Grid in Windows moet **C:\\app\\metadataDb**zijn.


    Bijvoorbeeld:

    ```json
    {
        "Env": [
            "inbound:serverAuth:tlsPolicy=strict",
            "inbound:serverAuth:serverCert:source=IoTEdge",
            "inbound:clientAuth:sasKeys:enabled=false",
            "inbound:clientAuth:clientCert:enabled=true",
            "inbound:clientAuth:clientCert:source=IoTEdge",
            "inbound:clientAuth:clientCert:allowUnknownCA=true",
            "outbound:clientAuth:clientCert:enabled=true",
            "outbound:clientAuth:clientCert:source=IoTEdge",
            "outbound:webhook:httpsOnly=true",
            "outbound:webhook:skipServerCertValidation=false",
            "outbound:webhook:allowUnknownCA=true"
         ],
         "HostConfig": {
            "Binds": [
                "myeventgridvol:C:\\app\\metadataDb"
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

## <a name="persistence-via-host-directory-mount"></a>Persistentie via Host Directory-koppeling

U kunt er ook voor kiezen om een map op het hostsysteem te maken en die map te koppelen.

1. Maak een map op het host-bestands systeem door de volgende opdracht uit te voeren.

   ```sh
   mkdir <your-directory-name-here>
   ```

   Bijvoorbeeld:

   ```sh
   mkdir C:\myhostdir
   ```
1. Gebruik **bindingen** om uw map te koppelen en de module Event Grid van Azure Portal opnieuw te implementeren.

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
    >Wijzig het tweede deel van de bindings waarde niet. Deze verwijst naar een specifieke locatie in de module. Voor de module Event Grid in Windows moet **C:\\app\\metadataDb**zijn.

    Bijvoorbeeld:

    ```json
    {
        "Env": [
            "inbound:serverAuth:tlsPolicy=strict",
            "inbound:serverAuth:serverCert:source=IoTEdge",
            "inbound:clientAuth:sasKeys:enabled=false",
            "inbound:clientAuth:clientCert:enabled=true",
            "inbound:clientAuth:clientCert:source=IoTEdge",
            "inbound:clientAuth:clientCert:allowUnknownCA=true",
            "outbound:clientAuth:clientCert:enabled=true",
            "outbound:clientAuth:clientCert:source=IoTEdge",
            "outbound:webhook:httpsOnly=true",
            "outbound:webhook:skipServerCertValidation=false",
            "outbound:webhook:allowUnknownCA=true"
         ],
         "HostConfig": {
            "Binds": [
                "C:\\myhostdir:C:\\app\\metadataDb"
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
