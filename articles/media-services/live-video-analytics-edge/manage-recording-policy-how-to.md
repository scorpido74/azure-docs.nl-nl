---
title: Het opname beleid beheren-Azure
description: In dit onderwerp wordt uitgelegd hoe u het registratie beleid beheert.
ms.topic: how-to
ms.date: 04/27/2020
ms.openlocfilehash: d3a1be915dc1cc8714e49cc7b2fe68bbe9cad161
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87011478"
---
# <a name="manage-recording-policy"></a>Opnamebeleid beheren

U kunt live video Analytics op IoT Edge gebruiken voor [continue video-opname](continuous-video-recording-concept.md), waarbij u gedurende weken of maanden video in de cloud kunt opnemen. U kunt de lengte (in dagen) van het Cloud archief beheren met behulp van de [levenscyclus beheer hulpprogramma's](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal) die zijn ingebouwd in azure Storage.  

Uw media service-account is gekoppeld aan een Azure Storage-account en wanneer u video opneemt in de Cloud, wordt de inhoud naar een media service- [Asset](../latest/assets-concept.md)geschreven. Elk activum wordt toegewezen aan een container in het opslag account. met levenscyclus beheer kunt u een [beleid](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#policy) voor een opslag account definiëren, waarbij u een [regel](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#rules) zoals de volgende kunt opgeven.

```
{
  "rules": [
    {
      "name": "NinetyDayRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "delete": { "daysAfterModificationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

De bovenstaande regel:

* Is van toepassing op alle blok-blobs in het opslag account.
* Hiermee geeft u op dat wanneer de blobs ouder zijn dan 30 dagen, ze worden verplaatst van de [laag Hot Access naar cool](../../storage/blobs/storage-blob-storage-tiers.md?tabs=azure-portal).
* En wanneer blobs ouder zijn dan 90 dagen, moeten ze worden verwijderd.

Omdat live video Analytics uw video in bepaalde tijd eenheden archiveert, bevat uw asset een reeks blobs, één BLOB per segment. Wanneer het levenscyclus beheer beleid in wordt gezet en oudere blobs verwijdert, kunt u de resterende blobs via de media service-Api's openen en afspelen. Zie [opnamen afspelen](playback-recordings-how-to.md)voor meer informatie. 

## <a name="limitations"></a>Beperkingen

Hieronder vindt u enkele bekende beperkingen met betrekking tot levenscyclus beheer:

* U kunt Maxi maal 100 regels in het beleid hebben en elke regel kan Maxi maal 10 containers bevatten. Als u dus een ander registratie beleid nodig hebt (bijvoorbeeld een archief met drie dagen voor de camera op de parkeer partij, 30 dagen voor de camera in het laad perron en 180 dagen voor de camera achter de teller voor uitchecken), kunt u met één media service-account de regels voor de meeste 1000 camera's aanpassen.
* Updates voor het levenscyclus beheer beleid zijn niet direct. Zie [deze sectie met veelgestelde vragen](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#faq) voor meer informatie.
* Als u ervoor kiest om een beleid toe te passen waarbij blobs worden verplaatst naar de cool-laag, kan dit gevolgen hebben voor het afspelen van dat gedeelte van het archief. Mogelijk worden extra latenties of sporadische fouten weer geven. Media Services biedt geen ondersteuning voor het afspelen van inhoud in de laag van het archief.

## <a name="next-steps"></a>Volgende stappen

[Opnamen afspelen](playback-recordings-how-to.md)
