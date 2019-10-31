---
title: Status persistent in Linux-Azure Event Grid IoT Edge | Microsoft Docs
description: Meta gegevens persistent maken in Linux
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 3506399537fe2cb16014ceb3429bce5aeee8cb69
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73100339"
---
# <a name="persist-state-in-linux"></a>Status persistent in Linux

Onderwerpen en abonnementen die zijn gemaakt in de Event Grid module, worden standaard opgeslagen in het container bestandssysteem. Zonder persistentie, als de module opnieuw wordt geïmplementeerd, worden alle meta gegevens die zijn gemaakt, verloren gegaan. Momenteel worden alleen meta gegevens bewaard. Gebeurtenissen worden opgeslagen in het geheugen. Als Event Grid module opnieuw wordt geïmplementeerd of opnieuw is gestart, gaan alle niet-bezorgde gebeurtenissen verloren.

Dit artikel bevat de stappen voor het implementeren van de Event Grid module met persistentie in Linux-implementaties.

> [!NOTE]
>De Event Grid-module wordt uitgevoerd als gebruiker met beperkte bevoegdheden met UID-`2000` en naam `eventgriduser`.

## <a name="persistence-via-volume-mount"></a>Persistentie via volume koppeling

 [Docker-volumes](https://docs.docker.com/storage/volumes/) worden gebruikt om de gegevens in de implementaties te bewaren. U kunt docker automatisch een benoemd volume laten maken als onderdeel van de implementatie van de Event Grid-module. Deze optie is de eenvoudigste optie. U kunt de naam van het volume dat wordt gemaakt in de sectie **bindingen** als volgt opgeven:

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
>Wijzig het tweede deel van de bindings waarde niet. Deze verwijst naar een specifieke locatie binnen de module. Voor de module Event Grid op Linux moet het **/app/metadataDb**zijn.

De volgende configuratie resulteert bijvoorbeeld in het maken van het volume **egmetadataDbVol** , waar de meta gegevens blijven bestaan.

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
      "egmetadataDbVol:/app/metadataDb"
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

U kunt ook een docker-volume maken met behulp van docker client-opdrachten. 

## <a name="persistence-via-host-directory-mount"></a>Persistentie via Host Directory-koppeling

In plaats van een docker-volume hebt u ook de mogelijkheid om een host-map te koppelen.

1. Maak eerst een gebruiker met de naam **eventgriduser** en id **2000** op de hostmachine door de volgende opdracht uit te voeren:

    ```sh
    sudo useradd -u 2000 eventgriduser
    ```
1. Maak een map op het bestands systeem van de host door de volgende opdracht uit te voeren.

   ```sh
   md <your-directory-name-here>
   ```

    Als u bijvoorbeeld de volgende opdracht uitvoert, wordt er een map gemaakt met de naam **myhostdir**.

    ```sh
    md /myhostdir
    ```
1. Maak vervolgens de **eventgriduser** -eigenaar van deze map door de volgende opdracht uit te voeren.

   ```sh
   sudo chown eventgriduser:eventgriduser -hR <your-directory-name-here>
   ```

    Bijvoorbeeld:

    ```sh
    sudo chown eventgriduser:eventgriduser -hR /myhostdir
    ```
1. Gebruik **bindingen** om de map te koppelen en de module Event Grid van Azure Portal opnieuw te implementeren.

    ```json
    {
         "HostConfig": {
            "Binds": [
                "<your-directory-name-here>:/app/metadataDb"
             ]
         }
    }
    ```

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
                  "/myhostdir:/app/metadataDb"
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
    >Wijzig het tweede deel van de bindings waarde niet. Deze verwijst naar een specifieke locatie binnen de module. Voor de module Event Grid op Linux moet het **/app/metadata**zijn.
