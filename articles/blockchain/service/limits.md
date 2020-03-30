---
title: Azure Blockchain-servicelimieten
description: Overzicht van de service- en functionele limieten in Azure Blockchain Service
ms.date: 11/22/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: f4001ee520f3f3136d1bac5ca047c80526fc92e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74455660"
---
# <a name="limits-in-azure-blockchain-service"></a>Limieten in Azure Blockchain-service

Azure Blockchain Service heeft service- en functionele limieten, zoals het aantal knooppunten dat een lid kan hebben, consortiumbeperkingen en opslagbedragen.

## <a name="pricing-tier"></a>Prijscategorie

Maximale limieten voor transacties en validatorknooppunten zijn afhankelijk van de vraag of u Azure Blockchain Service indient op basis- of standaardprijsniveaus.

| Prijscategorie | Maximale transactieknooppunten | Maximale validatorknooppunten |
|:---|:---:|:---:|
| Basic | 10 | 1 |
| Standard | 10 | 2 |

Het wijzigen van de prijslaag tussen Basic en Standard na het maken van leden wordt niet ondersteund.

## <a name="storage-capacity"></a>Opslagcapaciteit

De maximale hoeveelheid opslagruimte die per knooppunt kan worden gebruikt voor grootboekgegevens en logboeken is 1,8 terabyte.

De grootte van het aantal grootboek en logboekopslag wordt niet ondersteund.

## <a name="consortium-limits"></a>Consortiumlimieten

* **Consortium- en lidnamen moeten uniek zijn** voor andere consortium- en lidnamen in de Azure Blockchain-service.

* **Namen van leden en consortiums kunnen niet worden gewijzigd**

* **Alle leden in een consortium moeten zich in dezelfde prijscategorie**

* **Alle leden die deelnemen aan een consortium moeten in dezelfde regio wonen**

    Het eerste lid dat in een consortium is gemaakt, dicteert de regio. Uitgenodigde leden van het consortium moeten in dezelfde regio wonen als het eerste lid. Door alle leden tot dezelfde regio te beperken, wordt de netwerkconsensus niet negatief beïnvloed.

* **Een consortium moet ten minste één beheerder hebben**

    Als er slechts één beheerder in een consortium is, kunnen ze zichzelf niet uit het consortium verwijderen of hun lid verwijderen totdat een andere beheerder in het consortium is toegevoegd of gepromoot.

* **Leden die uit het consortium zijn verwijderd, kunnen niet opnieuw worden toegevoegd**

    In plaats daarvan moeten ze opnieuw worden uitgenodigd om zich bij het consortium aan te sluiten en een nieuw lid te creëren. Hun bestaande ledenbron wordt niet verwijderd om historische transacties te behouden.

* **Alle leden in een consortium moeten dezelfde grootboekversie gebruiken**

    Zie [Patchen, updates en versies](ledger-versions.md)voor meer informatie over de versies voor patchen, updates en grootboeken die beschikbaar zijn in Azure Blockchain Service.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over beleid met betrekking tot patching en upgrades van systemen - [Patching, updates en versies](ledger-versions.md).
