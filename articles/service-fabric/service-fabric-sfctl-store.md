---
title: Azure Service Fabric CLI-sfctl Store | Microsoft Docs
description: Beschrijft de Service Fabric CLI sfctl Store-opdrachten.
services: service-fabric
documentationcenter: na
author: jeffj6123
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: e8a085c4aa6df34441f22da5542231999930d89f
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900947"
---
# <a name="sfctl-store"></a>sfctl store
Voer elementaire bewerkingen op bestands niveau uit in het cluster installatie kopie archief.

## <a name="commands"></a>Opdrachten

|Opdracht|Beschrijving|
| --- | --- |
| delete | Hiermee verwijdert u de bestaande inhoud van het installatie kopie archief. |
| hoofdmap-info | Hiermee haalt u de inhouds informatie op in de hoofdmap van het archief met installatie kopieën. |
| gave | Hiermee wordt de informatie over de inhoud van de installatie kopie opgehaald. |

## <a name="sfctl-store-delete"></a>sfctl Store verwijderen
Hiermee verwijdert u de bestaande inhoud van het installatie kopie archief.

Hiermee verwijdert u de bestaande inhoud van het archief van de installatie kopie in het relatieve pad van de opgegeven afbeeldings opslag. Deze opdracht kan worden gebruikt om Geüploade toepassings pakketten te verwijderen nadat deze zijn ingericht.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --content-path [required] | Het relatieve pad naar het bestand of de map in het archief van de installatie kopie van de hoofdmap. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --fouten opsporen | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --Help-h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden\: JSON, jsonc, Table, TSV.  Standaard\: JSON. |
| --query | JMESPath-query reeks. Zie http\://jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-store-root-info"></a>hoofdmap van sfctl-archief-info
Hiermee haalt u de inhouds informatie op in de hoofdmap van het archief met installatie kopieën.

Hiermee wordt de informatie over de inhoud van de installatie kopie in de hoofdmap van de installatie kopie-Archief geretourneerd.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --fouten opsporen | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --Help-h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden\: JSON, jsonc, Table, TSV.  Standaard\: JSON. |
| --query | JMESPath-query reeks. Zie http\://jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-store-stat"></a>sfctl Store-stat
Hiermee wordt de informatie over de inhoud van de installatie kopie opgehaald.

Retourneert informatie over de inhoud van de afbeeldings opslag in de opgegeven contentPath. De contentPath is relatief ten opzichte van de hoofdmap van de opslag voor installatie kopieën.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --content-path [required] | Het relatieve pad naar het bestand of de map in het archief van de installatie kopie van de hoofdmap. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --fouten opsporen | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --Help-h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden\: JSON, jsonc, Table, TSV.  Standaard\: JSON. |
| --query | JMESPath-query reeks. Zie http\://jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |


## <a name="next-steps"></a>Volgende stappen
- [Stel](service-fabric-cli.md) de service Fabric cli in.
- Meer informatie over het gebruik van de Service Fabric CLI met behulp van de [voorbeeld scripts](/azure/service-fabric/scripts/sfctl-upgrade-application).