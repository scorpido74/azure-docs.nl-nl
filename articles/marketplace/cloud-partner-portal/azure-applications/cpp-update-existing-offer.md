---
title: Een bestaande Azure-toepassings aanbieding bijwerken | Azure Marketplace
description: Het bijwerken van een bestaande Azure-toepassings aanbieding op de Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: pabutler
ms.openlocfilehash: 6854e445ea63639866e9e39a6afc725237bbc8fe
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826102"
---
# <a name="update-an-existing-azure-application-offer"></a>Een bestaande Azure-toepassings aanbieding bijwerken

Er zijn verschillende soorten updates die u mogelijk wilt uitvoeren voor uw aanbieding nadat deze is gepubliceerd en Live is. Wijzigingen die u aanbrengt in uw nieuwe versie van uw aanbieding, moeten worden opgeslagen en opnieuw worden gepubliceerd zodat deze op de Marketplace worden weer gegeven. In dit artikel worden de verschillende aspecten beschreven van het bijwerken van de aanbieding van uw beheerde toepassing in de [Cloud Partner-Portal](https://cloudpartner.azure.com/).

Er zijn verschillende redenen waarom u uw aanbieding wilt bijwerken, zoals:

- Er wordt een nieuwe installatie kopie versie toegevoegd aan bestaande Sku's.
- Nieuwe Sku's toevoegen.
- De meta gegevens van de Marketplace voor de aanbieding of afzonderlijke Sku's bijwerken.

Om u te helpen bij deze wijzigingen, biedt de Portal de functies **compare** en **history** .

## <a name="unpermitted-changes-to-an-azure-application-offer-or-sku"></a>Niet-toegestane wijzigingen in een Azure-toepassings aanbieding of-SKU

Er zijn kenmerken van een container aanbod of SKU die niet kunnen worden gewijzigd nadat de aanbieding Live is op Azure Marketplace. U kunt de volgende instellingen niet wijzigen:

- Aanbiedings-ID en uitgevers-ID van de aanbieding
- SKU-ID van bestaande Sku's
- Versie Tags, bijvoorbeeld: `1.0.1`
- Wijzigingen in het facturerings-en licentie model voor bestaande Sku's

## <a name="common-update-operations"></a>Algemene update bewerkingen

De volgende update bewerkingen zijn gebruikelijk.

### <a name="update-image-version-for-a-sku"></a>Afbeeldings versie voor een SKU bijwerken

Het is gebruikelijk dat een installatie kopie regel matig wordt bijgewerkt met beveiligings patches, extra functies, enzovoort. In dit scenario wilt u de installatie kopie die door uw SKU wordt verwezen, bijwerken met behulp van de volgende stappen:

1. Meld u aan bij de [Cloud Partner-Portal](https://cloudpartner.azure.com/).
2. Zoek onder **alle aanbiedingen**de aanbieding die u wilt bijwerken.
3. Selecteer op het tabblad **sku's** de SKU die is gekoppeld aan de afbeelding die moet worden bijgewerkt.
4. Selecteer **+ nieuwe afbeeldings versie** om een nieuwe installatie kopie toe te voegen.
5. Geef de nieuwe versies van de installatie kopie op. De versie van de installatie kopie moet dezelfde Tags richtlijnen als eerdere versies volgen. Versie Tags moeten de indeling X. Y. Z hebben, waarbij X, Y en Z gehele getallen zijn. Controleer of de nieuwe versie die u opgeeft, groter is dan alle vorige versies.
6. Selecteer **publiceren** om de werk stroom te starten om de nieuwe versie van de container installatie kopie naar Azure Marketplace te publiceren.

### <a name="add-a-new-sku"></a>Een nieuwe SKU toevoegen

Gebruik de volgende stappen om een nieuwe SKU beschikbaar te maken voor uw aanbieding:

1. Meld u aan bij de [Cloud Partner-Portal](https://cloudpartner.azure.com/).
2. Zoek onder **alle aanbiedingen**de aanbieding die u wilt bijwerken.
3. Selecteer op het tabblad **sku's** de optie **nieuwe SKU toevoegen** en geef in het pop-upvenster een **SKU-id** op.
4. Publiceer de aanbieding opnieuw met behulp van de stappen die worden beschreven in [Azure-toepassings aanbieding publiceren](./cpp-publish-offer.md).
5. Selecteer **publiceren** om de werk stroom te starten om uw nieuwe SKU te publiceren.

### <a name="update-offer-marketplace-metadata"></a>Update aanbieding Marketplace-meta gegevens

Voer de volgende stappen uit om de Marketplace-meta gegevens bij te werken die zijn gekoppeld aan uw aanbieding. (Bijvoorbeeld: Bedrijfs naam, logo's, enz.)

1. Meld u aan bij de [Cloud Partner-Portal](https://cloudpartner.azure.com/).
2. Zoek onder **alle aanbiedingen**de aanbieding die u wilt bijwerken.
3. Ga naar het tabblad **Marketplace** . Volg de instructies in [Azure Application offer publiceren](./cpp-publish-offer.md) om meta gegevens wijzigingen door te voeren.
4. Selecteer **publiceren** om de werk stroom te starten om uw wijzigingen te publiceren.
 
>[!Note]
>Opt-in voor Cloud Solution Providers (CSP)-partner kanaal is nu beschikbaar.  Raadpleeg [Cloud Solution Providers](../../cloud-solution-providers.md) voor meer informatie over het marketing gebruik van uw aanbieding via de micro soft CSP-partner kanalen.

## <a name="deleting-an-existing-offer"></a>Een bestaande aanbieding verwijderen

U kunt ervoor kiezen om uw aanbieding uit de Marketplace te verwijderen. Het verwijderen van een aanbieding heeft geen invloed op de huidige aankopen van die aanbieding. Deze klant aankopen blijven werken zoals voorheen. De aanbieding is echter niet beschikbaar voor nieuwe aankopen nadat het verwijderen is voltooid.

Aanbieding beëindigen is het proces van beëindiging van de service en/of licentieovereenkomst tussen u en uw bestaande klanten.
Richt lijnen en beleids regels met betrekking tot het verwijderen en beëindigen van de aanbieding zijn onderhevig aan Microsoft Marketplace Uitgever overeenkomst (zie sectie 7) en het deelname beleid (zie sectie 6,2).

Zie voor meer informatie [verwijderen en aanbieden/SKU in azure Marketplace](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-managed-app-offer-delete).

## <a name="compare-feature"></a>Functie vergelijken

Wanneer u wijzigingen aanbrengt in een gepubliceerde aanbieding, kunt u de functie Compare gebruiken om de wijzigingen die u hebt aangebracht, te controleren.

De functie Compare gebruiken:

1. Selecteer op elk moment in het bewerkings proces vergelijken voor uw aanbieding.
2. Bekijk de kant-en-zij-versies van marketing assets en meta gegevens.

## <a name="history-of-publishing-actions"></a>Geschiedenis van publicatie acties

Als u historische publicatie activiteiten wilt zien, selecteert u het tabblad **geschiedenis** in de linkernavigatiebalk van Cloud Partner-Portal. U kunt de tijds tempels zien die worden gemaakt tijdens de levens duur van uw aanbiedingen in azure Marketplace.

## <a name="next-steps"></a>Volgende stappen

[Aanbieding voor Azure-toepassing](./cpp-azure-app-offer.md)