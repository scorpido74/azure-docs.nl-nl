---
title: Azure Blockchain-servicelimieten
description: Overzicht van de service- en functionele limieten in Azure Blockchain Service
ms.date: 03/30/2020
ms.topic: conceptual
ms.reviewer: ravastra
ms.openlocfilehash: c728e617ac37795988cd596c7cb0c5025aac4ccf
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529581"
---
# <a name="limits-in-azure-blockchain-service"></a>Limieten in Azure Blockchain-service

Azure Blockchain Service heeft service- en functionele limieten, zoals het aantal knooppunten dat een lid kan hebben, consortiumbeperkingen en opslagbedragen.

## <a name="pricing-tier"></a>Prijscategorie

Maximale limieten voor transacties en validatorknooppunten zijn afhankelijk van de vraag of u Azure Blockchain Service indient op basis- of standaardprijsniveaus.

| Prijscategorie | Maximale transactieknooppunten | Maximale validatorknooppunten |
|:---|:---:|:---:|
| Basic | 10 | 1 |
| Standard | 10 | 2 |

Uw consortiumnetwerk moet ten minste twee Azure Blockchain Service-standaardniveauknooppunten hebben. Standaardniveauknooppunten bevatten twee validatorknooppunten. Vier validator knooppunten zijn nodig om Istanbul [Byzantijnse Fault Tolerance consensus](https://github.com/jpmorganchase/quorum/wiki/Quorum-Consensus)te voldoen.

Gebruik de basislaag is voor ontwikkeling, testen en proof of concepts. Gebruik de standaardlaag voor implementaties van productiekwaliteit. U moet ook de *standaardlaag* gebruiken als u Blockchain Data Manager gebruikt of een groot aantal privétransacties verzendt.

Het wijzigen van de prijslaag tussen basis en standaard na het maken van leden wordt niet ondersteund.

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

    In plaats daarvan moeten ze opnieuw worden uitgenodigd om zich bij het consortium aan te sluiten en een nieuw lid te creëren. Hun bestaande ledenbronnen worden niet verwijderd om historische transacties te behouden.

* **Alle leden in een consortium moeten dezelfde grootboekversie gebruiken**

    Zie [Patchen, updates en versies](ledger-versions.md)voor meer informatie over de versies voor patchen, updates en grootboeken die beschikbaar zijn in Azure Blockchain Service.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over beleid met betrekking tot patching en upgrades van systemen - [Patching, updates en versies](ledger-versions.md).
