---
title: Een bestaande Azure-containers-aanbieding bijwerken | Azure Marketplace
description: Het bijwerken van een bestaande container aanbieding op Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/01/2018
ms.author: pabutler
ms.openlocfilehash: 40cd375b11eb5b7ad5943fea9839b6339a7d002f
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823085"
---
# <a name="update-an-existing-container-offer"></a>Een bestaande container aanbieding bijwerken

In dit artikel worden de verschillende aspecten beschreven van het bijwerken van uw container aanbod in de [Cloud Partner-Portal](https://cloudpartner.azure.com/).

Er zijn verschillende redenen waarom u uw aanbieding wilt bijwerken, zoals:

-  De versie van een nieuwe container installatie kopie wordt toegevoegd aan bestaande Sku's.
-  Nieuwe Sku's toevoegen.
-  De meta gegevens van de Marketplace voor de aanbieding of afzonderlijke Sku's bijwerken.

Om u te helpen bij deze wijzigingen, biedt de Portal de functies **compare** en **history** .  


## <a name="unpermitted-changes-to-a-container-offer-or-sku"></a>Niet-toegestane wijzigingen in een container aanbod of SKU

Er zijn kenmerken van een container aanbod of SKU die niet kunnen worden gewijzigd nadat de aanbieding Live is op Azure Marketplace. U kunt de volgende instellingen niet wijzigen:

-  **Aanbiedings-id** en **uitgevers-id** van de aanbieding
-  **SKU-id** van bestaande sku's
-  Versie Tags, bijvoorbeeld: `1.0.1`
-  Wijzigingen in het facturerings-en licentie model voor bestaande Sku's

## <a name="common-update-operations"></a>Algemene update bewerkingen

De volgende update bewerkingen zijn gebruikelijk.

### <a name="update-container-image-version-for-a-sku"></a>Versie van container installatie kopie bijwerken voor een SKU

Het is gebruikelijk dat een container installatie kopie regel matig wordt bijgewerkt met beveiligings patches, extra functies, enzovoort. In dit scenario wilt u de container installatie kopie die door uw SKU wordt verwezen, bijwerken met behulp van de volgende stappen:

1. Meld u aan bij de [Cloud Partner-Portal](https://cloudpartner.azure.com/).
2. Zoek onder **alle aanbiedingen**de aanbieding die u wilt bijwerken.
3. Selecteer op het tabblad **sku's** de SKU die is gekoppeld aan de container installatie kopie die moet worden bijgewerkt.
4. Selecteer onder **container installatie kopie** **+ nieuwe afbeeldings versie** om een nieuwe container installatie kopie toe te voegen.
5. Geef de nieuwe versies van de container **installatie kopie**op. De versie van de installatie kopie moet dezelfde Tags richtlijnen als eerdere versies volgen. Versie Tags moeten de indeling X. Y. Z hebben, waarbij X, Y en Z gehele getallen zijn. Controleer of de nieuwe versie die u opgeeft, groter is dan alle vorige versies.
6. Selecteer **publiceren** om de werk stroom te starten om de nieuwe versie van de container installatie kopie naar Azure Marketplace te publiceren.

### <a name="add-a-new-sku"></a>Een nieuwe SKU toevoegen

Gebruik de volgende stappen om een nieuwe SKU beschikbaar te maken voor uw aanbieding:

1. Meld u aan bij de [Cloud Partner-Portal](https://cloudpartner.azure.com/).
2. Zoek onder **alle aanbiedingen**de aanbieding die u wilt bijwerken.
3. Selecteer op het tabblad **sku's** de optie **nieuwe SKU toevoegen** en geef in het pop-upvenster een **SKU-id** op.
4. Publiceer de container opnieuw met behulp van de stappen die worden beschreven in de aanbieding voor de [publicatie container](./cpp-publish-offer.md).
5. Selecteer **publiceren** om de werk stroom te starten om uw nieuwe SKU te publiceren.

### <a name="update-offer-marketplace-metadata"></a>Update aanbieding Marketplace-meta gegevens

Voer de volgende stappen uit om de Marketplace-meta gegevens bij te werken die zijn gekoppeld aan uw aanbieding. (Bijvoorbeeld: Bedrijfs naam, logo's, enzovoort)

1. Meld u aan bij de [Cloud Partner-Portal](https://cloudpartner.azure.com/).
2. Zoek onder **alle aanbiedingen**de aanbieding die u wilt bijwerken.
3. Ga naar het tabblad **Marketplace** . Volg de instructies in het artikel aanbieding van de [publicatie container](./cpp-publish-offer.md) maken om meta gegevens te wijzigen.
4. Selecteer **publiceren** om de werk stroom te starten om uw wijzigingen te publiceren.

## <a name="compare-feature"></a>Functie vergelijken

Wanneer u wijzigingen aanbrengt in een gepubliceerde aanbieding, kunt u de functie **compare** gebruiken om de wijzigingen die u hebt aangebracht, te controleren.

### <a name="to-use-the-compare-feature"></a>De functie Compare gebruiken:

1. Selecteer op elk moment in het bewerkings proces vergelijken voor uw aanbieding.
2. Bekijk de kant-en-zij-versies van marketing assets en meta gegevens.


## <a name="history-of-publishing-actions"></a>Geschiedenis van publicatie acties

Als u historische publicatie activiteiten wilt zien, selecteert u het tabblad **geschiedenis** in de linkernavigatiebalk van Cloud Partner-Portal. U kunt de tijds tempels zien die worden gemaakt tijdens de levens duur van uw aanbiedingen in azure Marketplace.