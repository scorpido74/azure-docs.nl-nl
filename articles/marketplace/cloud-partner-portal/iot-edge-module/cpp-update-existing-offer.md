---
title: Een bestaande Azure IoT Edge module-aanbieding bijwerken | Azure Marketplace
description: Het bijwerken van een bestaande IoT Edge module-aanbieding op Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 10/18/2018
ms.author: pabutler
ms.openlocfilehash: cd0167e1af5bf8ef667df88237d83e9f33ed41f9
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73813397"
---
# <a name="update-an-existing-iot-edge-module-offer"></a>Een bestaande IoT Edge module-aanbieding bijwerken

In dit artikel worden de verschillende aspecten beschreven van het bijwerken van uw IoT Edge module-aanbieding in de [Cloud Partner-Portal](https://cloudpartner.azure.com/) en vervolgens het opnieuw publiceren van de aanbieding.

Er zijn verschillende redenen waarom u uw aanbieding wilt bijwerken, zoals:

-  Er wordt een nieuwe installatie kopie-versie van IoT Edge module toegevoegd aan bestaande Sku's.
-  Nieuwe Sku's toevoegen.
-  De meta gegevens van de Marketplace voor de aanbieding of afzonderlijke Sku's bijwerken.

Om u te helpen bij deze wijzigingen, biedt de Portal de functies **compare** en **history** .  


## <a name="unpermitted-changes-to-iot-edge-module-offer-or-sku"></a>Niet-toegestane wijzigingen in de IoT Edge module-aanbieding of-SKU

Er zijn kenmerken van een IoT Edge module-aanbieding of SKU die niet kan worden gewijzigd nadat de aanbieding op Azure Marketplace is opgenomen. U kunt de volgende instellingen niet wijzigen:

-  **Aanbiedings-id** en **uitgevers-id** van de aanbieding
-  **SKU-id** van bestaande sku's
-  Versie Tags, bijvoorbeeld: `1.0.1`
-  Wijzigingen in het facturerings-en licentie model voor bestaande Sku's

## <a name="common-update-operations"></a>Algemene update bewerkingen

De volgende update bewerkingen zijn gebruikelijk.

### <a name="update-the-iot-edge-module-image-version-for-a-sku"></a>De afbeeldings versie van de IoT Edge-module voor een SKU bijwerken

Het is gebruikelijk dat een installatie kopie van een IoT Edge module regel matig wordt bijgewerkt met beveiligings patches, extra functies, enzovoort. In dit scenario wilt u de installatie kopie van de IoT Edge-module met de volgende stappen bijwerken met uw SKU:

1.  Meld u aan bij de [Cloud Partner-Portal](https://cloudpartner.azure.com/).

2.  Zoek onder **alle aanbiedingen**de aanbieding die u wilt bijwerken.

3.  Selecteer op het tabblad **sku's** de SKU die is gekoppeld aan de IOT Edge module-installatie kopie die moet worden bijgewerkt.

4.  Selecteer onder **installatie kopie van rand module** **+ nieuwe afbeeldings versie** om een nieuwe IOT Edge module-installatie kopie toe te voegen.

5.  Geef de **installatie kopie versies**van de nieuwe IOT Edge-module op. De versie van de installatie kopie moet dezelfde Tags richtlijnen als eerdere versies volgen. Versie Tags moeten de indeling X. Y. Z hebben, waarbij X, Y en Z gehele getallen zijn. Controleer of de nieuwe versie die u opgeeft, groter is dan alle vorige versies.

6.  Selecteer **publiceren** om de werk stroom te starten om uw nieuwe IOT Edge module versie naar Azure Marketplace te publiceren.

### <a name="add-a-new-sku"></a>Een nieuwe SKU toevoegen

Gebruik de volgende stappen om een nieuwe SKU beschikbaar te maken voor uw aanbieding: 

1.  Meld u aan bij de [Cloud Partner-Portal](https://cloudpartner.azure.com/).

2.  Zoek onder **alle aanbiedingen**de aanbieding die u wilt bijwerken.

3.  Selecteer op het tabblad **sku's** de optie **nieuwe SKU toevoegen** en geef in het pop-upvenster een **SKU-id** op.

4.  Publiceer de IoT Edge-module opnieuw met behulp van de stappen die worden beschreven in [een IOT Edge-module publiceren naar Azure Marketplace](./cpp-publish-offer.md).

5.  Selecteer **publiceren** om de werk stroom te starten om uw nieuwe SKU te publiceren.


### <a name="update-offer-marketplace-metadata"></a>Update aanbieding Marketplace-meta gegevens

Voer de volgende stappen uit om de Marketplace-meta gegevens bij te werken die zijn gekoppeld aan uw aanbieding. (Bijvoorbeeld: Bedrijfs naam, logo's, enz.)

1.  Meld u aan bij de [Cloud Partner-Portal](https://cloudpartner.azure.com/).

2.  Zoek onder **alle aanbiedingen**de aanbieding die u wilt bijwerken.

3.  Ga naar het tabblad **Marketplace** . Gebruik de instructies in het artikel [een IOT Edge publiceren naar Azure Marketplace](./cpp-publish-offer.md) om meta gegevens te wijzigen.

4.  Selecteer **publiceren** om de werk stroom te starten om uw wijzigingen te publiceren.

## <a name="compare-feature"></a>Functie vergelijken

Wanneer u wijzigingen aanbrengt in een gepubliceerde aanbieding, kunt u de functie **compare** gebruiken om de wijzigingen die u hebt aangebracht, te controleren. 

**De functie Compare gebruiken:**

1.  Selecteer op elk moment in het bewerkings proces **vergelijken** voor uw aanbieding.

    ![Functie knop vergelijken](./media/iot-edge-module-compare.png)


2.  Bekijk de kant-en-zij-versies van marketing assets en meta gegevens.


## <a name="history-of-publishing-actions"></a>Geschiedenis van publicatie acties

Als u historische publicatie activiteiten wilt zien, selecteert u het tabblad **geschiedenis** in de linkernavigatiebalk van Cloud Partner-Portal. U kunt de tijds tempels zien die worden gemaakt tijdens de levens duur van uw aanbiedingen in azure Marketplace.  <!-- Need to find correct link here:  legal time windowsFor more information, see [History page](cpp-history-page.md) -->
