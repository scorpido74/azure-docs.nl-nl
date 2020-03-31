---
title: Marktplaatsaanbiedingen verwijderen | Azure Marketplace
description: Aanbiedingen op de Azure- en AppSource-marketplaces verwijderen met behulp van de Cloud Partner Portal
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: dsindona
ms.openlocfilehash: fa8ee834bd6d9261c47c540e9517c200c894b6c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80286452"
---
# <a name="delete-azure-marketplace-and-appsource-offers-or-skus"></a>Azure Marketplace- en AppSource-aanbiedingen of SKU's verwijderen

Om verschillende redenen u besluiten om uw aanbieding in te trekken van de Microsoft-marktplaats, die twee vormen kan aannemen:

- *Het verwijderen van aanbiedingen* zorgt ervoor dat nieuwe klanten uw aanbieding niet meer kunnen kopen of implementeren, maar heeft geen invloed op bestaande klanten, die u moet ondersteunen volgens uw licentieovereenkomst en relevante wetten. 
- *Beëindiging* van de aanbieding is het proces van beëindiging van de service- en/of licentieovereenkomst tussen u en uw bestaande klanten. 

Richtlijnen en beleidsregels met betrekking tot het verwijderen en beëindigen van aanbiedingen vallen onder de [Microsoft Marketplace Publisher Agreement](https://go.microsoft.com/fwlink/?LinkID=699560) en het [participatiebeleid](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) (sectie [Schorsing en verwijdering aanbieden).](https://docs.microsoft.com/legal/marketplace/participation-policy#offering-suspension-and-removal) 

In dit artikel wordt gesproken over de verschillende ondersteunde verwijderingsscenario's en de stappen die nodig zijn om elk van deze scenario's uit te voeren.  

> [!NOTE]
> U een aanbieding verwijderen die niet is gepubliceerd door **simpelweg** de knop Verwijderen op de werkbalk van het tabblad **Editor** te selecteren.


## <a name="delete-a-published-sku-from-the-azure-marketplace"></a>Een gepubliceerde SKU verwijderen uit de Azure Marketplace

U een gepubliceerde SKU verwijderen uit Azure Marketplace met de volgende stappen:

1.  Meld u aan bij de [Cloud Partner Portal](https://cloudpartner.azure.com/).
2.  Selecteer op de pagina **Alle aanbiedingen** uw aanbieding.  Uw aanbieding moet worden weergegeven op het tabblad **Editor.**
3.  Selecteer op de linkerwerkbalk het tabblad **SKU's.** 
4.  Selecteer de SKU die u wilt verwijderen en klik op **verwijderen.**
5.  [Publiceer](./cpp-publish-offer.md) de aanbieding opnieuw naar Azure Marketplace.

Nadat de gewijzigde aanbieding is gepubliceerd in de Azure Marketplace, wordt de geselecteerde SKU niet langer vermeld in de Azure Marketplace- en Azure-portal.


## <a name="roll-back-to-a-previous-sku-version"></a>Terugdraaien naar een vorige SKU-versie

U de huidige versie van een gepubliceerde SKU verwijderen uit Azure Marketplace met behulp van de stappen hier. Wanneer het proces is voltooid, wordt de SKU teruggezet naar de vorige versie.

1. Meld u aan bij de [Cloud Partner Portal](https://cloudpartner.azure.com/).
2. Selecteer op de pagina **Alle aanbiedingen** uw aanbieding.  Uw aanbieding moet worden weergegeven op het tabblad **Editor.**
3. Selecteer op de linkerwerkbalk het tabblad **SKU's.** 
4. Verwijder de nieuwste versie van het bijbehorende oplossingsitem uit de lijst met schijfversies.  Afhankelijk van het aanbiedingstype kan dit veld **schijfversie,** **pakketversies**of vergelijkbare activa zijn. 
5. [Publiceer](./cpp-publish-offer.md) de aanbieding opnieuw naar Azure Marketplace.

Nadat de gewijzigde aanbieding is gepubliceerd op de Azure Marketplace, wordt de huidige versie van de vermelde SKU niet meer vermeld. in de Azure Marketplace en de Azure-portal.  De SKU wordt teruggezet naar de vorige versie.


## <a name="delete-a-live-offer"></a>Een live-aanbieding verwijderen

Er zijn verschillende procedurele, zakelijke en juridische aspecten aan het verwijderen van een live-aanbod. Volg de volgende stappen om begeleiding te krijgen van het ondersteuningsteam om een live-aanbieding uit de Azure Marketplace te verwijderen:

1.  Een ondersteuningsticket verhogen via de pagina [Een incident maken](https://go.microsoft.com/fwlink/?linkid=844975) of door te klikken op **Ondersteuning** in de rechterbovenhoek van de Cloud [Partner Portal.](https://cloudpartner.azure.com/)

2.  Selecteer uw specifieke aanbiedingstype in de lijst **Probleemtype** en selecteer **Een gepubliceerde aanbieding verwijderen** in de lijst **Categorie.**

3.  Dien het verzoek in.

Het ondersteuningsteam begeleidt u door het verwijderingsproces van de aanbieding.

> [!NOTE]
> Het verwijderen van een aanbieding (of SKU) heeft geen invloed op de huidige aankopen van die aanbieding (of SKU). Deze aankopen blijven werken als voorheen. Verwijderde aanbiedingen of SKU's zijn echter niet beschikbaar voor toekomstige aankopen.


## <a name="next-steps"></a>Volgende stappen

Nadat u bekend bent met de basisbewerkingen die worden gebruikt voor het beheren van aanbiedingen, bent u klaar om een exemplaar van een [Microsoft-marktplaatsaanbieding](../cpp-marketplace-offers.md)te maken.
