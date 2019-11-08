---
title: Marketplace-aanbiedingen verwijderen | Azure Marketplace
description: Aanbiedingen verwijderen op de Azure-en AppSource-markt plaatsen met behulp van de Cloud Partner-portal
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: pabutler
ms.openlocfilehash: 6a75fbb0c4b9c364342a406a8076128346943101
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826766"
---
# <a name="delete-azure-marketplace-and-appsource-offers-or-skus"></a>Azure Marketplace-en AppSource-aanbiedingen of Sku's verwijderen

Om verschillende redenen kunt u besluiten om uw aanbieding uit te trekken van de Marketplace van micro soft, die twee formulieren kan aannemen:

- Met het *verwijderen van aanbiedingen* kunt u ervoor zorgen dat nieuwe klanten uw aanbieding niet meer kunnen kopen of implementeren, maar geen invloed hebben op bestaande klanten, die u moet ondersteunen volgens uw gebruiksrecht overeenkomst en relevante wetgeving. 
- *Beëindiging van aanbieding* is het proces waarbij de service en/of licentie overeenkomst tussen u en uw bestaande klanten wordt beëindigd. 

Richt lijnen en beleids regels die betrekking hebben op het verwijderen en beëindigen van de aanbieding, zijn onderworpen aan [Microsoft Marketplace Publisher-overeenkomst](https://go.microsoft.com/fwlink/?LinkID=699560) en het [deelname beleid](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) (sectie die [opschorting en verwijdering biedt](https://docs.microsoft.com/legal/marketplace/participation-policy#offering-suspension-and-removal)). 

In dit artikel vindt u informatie over de verschillende scenario's voor het verwijderen en de stappen die nodig zijn om elk uit te voeren.  

> [!NOTE]
> U kunt een aanbieding die niet is gepubliceerd, verwijderen door simpelweg op de knop **verwijderen** te klikken in de werk balk van het tabblad **Editor** .


## <a name="delete-a-published-sku-from-the-azure-marketplace"></a>Een gepubliceerde SKU verwijderen uit Azure Marketplace

Met de volgende stappen kunt u een gepubliceerde SKU uit Azure Marketplace verwijderen:

1.  Meld u aan bij de [Cloud Partner-Portal](https://cloudpartner.azure.com/).
2.  Selecteer op de pagina **alle aanbiedingen** uw aanbieding.  Uw aanbieding moet worden weer gegeven op het tabblad **Editor** .
3.  Selecteer op de werk balk links het tabblad **sku's** . 
4.  Selecteer de SKU die u wilt verwijderen en klik op de knop **verwijderen** .
5.  [Publiceer](./cpp-publish-offer.md) de aanbieding opnieuw naar Azure Marketplace.

Nadat de gewijzigde aanbieding is gepubliceerd op de Azure Marketplace, wordt de geselecteerde SKU niet meer weer gegeven in de Azure Marketplace en Azure Portal.


## <a name="roll-back-to-a-previous-sku-version"></a>Terugkeren naar een eerdere SKU-versie

U kunt de huidige versie van een gepubliceerde SKU verwijderen uit Azure Marketplace met behulp van de volgende stappen. Wanneer het proces is voltooid, wordt de SKU teruggezet naar de vorige versie.

1. Meld u aan bij de [Cloud Partner-Portal](https://cloudpartner.azure.com/).
2. Selecteer op de pagina **alle aanbiedingen** uw aanbieding.  Uw aanbieding moet worden weer gegeven op het tabblad **Editor** .
3. Selecteer op de werk balk links het tabblad **sku's** . 
4. Verwijder de meest recente versie van het gekoppelde Asset-oplossing uit de lijst met schijf versies.  Afhankelijk van het type aanbieding, kan dit veld **schijf versie**, **pakket versies**of vergelijk bare activa zijn. 
5. [Publiceer](./cpp-publish-offer.md) de aanbieding opnieuw naar Azure Marketplace.

Nadat de gewijzigde aanbieding is gepubliceerd op theAzure Marketplace, wordt de huidige versie van de vermelde SKU niet meer weer gegeven. in azure Marketplace en de Azure Portal.  De SKU wordt teruggedraaid naar de vorige versie.


## <a name="delete-a-live-offer"></a>Een live-aanbieding verwijderen

Er zijn verschillende procedure-, bedrijfs-en juridische aspecten voor het verwijderen van een live-aanbieding. Volg de volgende stappen om hulp te krijgen van het ondersteunings team om een live-aanbieding te verwijderen uit Azure Marketplace:

1.  Verhoog een ondersteunings ticket via de pagina [een incident maken](https://go.microsoft.com/fwlink/?linkid=844975) of door te klikken op **ondersteuning** in de rechter bovenhoek van de [Cloud Partner-Portal](https://cloudpartner.azure.com/).

2.  Selecteer uw specifieke aanbiedings type in de lijst **probleem type** en selecteer **een gepubliceerde aanbieding verwijderen** in de **categorie** lijst.

3.  Dien de aanvraag in.

Het ondersteunings team helpt u bij het verwijderings proces van de aanbieding.

> [!NOTE]
> Het verwijderen van een aanbieding (of SKU) heeft geen invloed op de huidige aankopen van die aanbieding (of SKU). Deze aankopen blijven werken zoals voorheen. Verwijderde aanbiedingen of Sku's zijn echter niet beschikbaar voor toekomstige aankopen.


## <a name="next-steps"></a>Volgende stappen

Wanneer u bekend bent met de basis bewerkingen die worden gebruikt voor het beheren van aanbiedingen, bent u klaar om een exemplaar van een micro soft [Marketplace-aanbieding](../cpp-marketplace-offers.md)te maken.
