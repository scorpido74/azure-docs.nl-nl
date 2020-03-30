---
title: Azure Blockchain Service ledger-versies, patching, & upgrade
description: Overzicht van de ondersteunde grootboekenversies in Azure Blockchain Service, inclusief beleid met betrekking tot patching van systemen en systeembeheer- en gebruikersbeheerupgrades.
ms.date: 11/20/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 8d4a77699dd80743acfadd4d72d6d75bc1939b3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74325191"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>Ondersteunde Azure Blockchain Service-grootboekversies

Azure Blockchain Service maakt gebruik van het [op](https://www.goquorum.com/developers) Ethereum gebaseerde Quorum-grootboek dat is ontworpen voor de verwerking van privétransacties binnen een groep bekende deelnemers, geïdentificeerd als een consortium in Azure Blockchain-service.

Momenteel ondersteunt Azure Blockchain Service [Quorum-versie 2.2.3](https://github.com/jpmorganchase/quorum/releases/tag/v2.2.3) en [Tessera-transactiebeheer](https://github.com/jpmorganchase/tessera).

## <a name="managing-updates-and-upgrades"></a>Updates en upgrades beheren

Versien in Quorum gebeurt via een grote, kleine en patchreleases. Als de Quorum-versie bijvoorbeeld 2.0.1 is, wordt het releasetype als volgt gecategoriseerd:

|Primair | Secundair  | Patch  |
| :--- | :----- | :----- |
| 2 | 0 | 1 | 

Azure Blockchain Service werkt automatisch patchreleases van Quorum bij aan bestaande lopende leden binnen 30 dagen nadat ze beschikbaar zijn gesteld vanuit Quorum.

## <a name="availability-of-new-ledger-versions"></a>Beschikbaarheid van nieuwe grootboekversies

Azure Blockchain Service biedt de nieuwste grote en kleine versies van het Quorum-grootboek binnen 60 dagen nadat het beschikbaar is bij de fabrikant van het Quorum. Er zijn maximaal vier kleine releases beschikbaar waar consortia uit kunnen kiezen bij de inrichting van een nieuw lid en consortium. Upgraden van naar een grote of kleine release wordt momenteel niet ondersteund. Als u bijvoorbeeld versie 2.x uitvoert, wordt een upgrade naar versie 3.x momenteel niet ondersteund. Als u versie 2.2 uitvoert, wordt een upgrade naar versie 2.3 momenteel niet ondersteund.

## <a name="next-steps"></a>Volgende stappen

[Limieten in Azure Blockchain-service](limits.md)
