---
title: Azure Service Fabric CLI-sfctl-instellingen telemetrie | Microsoft Docs
description: Hierin worden de telemetrie-opdrachten voor de Service Fabric CLI sfctl-instellingen beschreven.
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
ms.openlocfilehash: d926c71ae8fd9e196b86c14c7fb96cc65b587b65
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900981"
---
# <a name="sfctl-settings-telemetry"></a>sfctl settings telemetry
Telemetrie-instellingen configureren die lokaal zijn voor dit exemplaar van sfctl.

Sfctl-telemetrie verzamelt opdracht naam zonder opgegeven para meters of hun waarden, Sfctl versie, type besturings systeem, python-versie, het slagen of mislukken van de opdracht, het fout bericht dat wordt geretourneerd.

## <a name="commands"></a>Opdrachten

|Opdracht|Beschrijving|
| --- | --- |
| set-telemetrie | Telemetrie in-of uitschakelen. |

## <a name="sfctl-settings-telemetry-set-telemetry"></a>sfctl-instellingen telemetrie-set-telemetrie
Telemetrie in-of uitschakelen.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --uit | Schakel telemetrie uit. |
| --aan | Schakel telemetrie in. Dit is de standaardwaarde. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --fouten opsporen | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --Help-h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden\: JSON, jsonc, Table, TSV.  Standaard\: JSON. |
| --query | JMESPath-query reeks. Zie http\://jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

### <a name="examples"></a>Voorbeelden

Schakel telemetrie uit.

```
sfctl settings telemetry set_telemetry --off
```

Schakel telemetrie in.

```
sfctl settings telemetry set_telemetry --on
```


## <a name="next-steps"></a>Volgende stappen
- [Stel](service-fabric-cli.md) de service Fabric cli in.
- Meer informatie over het gebruik van de Service Fabric CLI met behulp van de [voorbeeld scripts](/azure/service-fabric/scripts/sfctl-upgrade-application).