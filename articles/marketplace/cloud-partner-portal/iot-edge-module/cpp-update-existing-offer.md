---
title: Een bestaande Azure IoT Edge-moduleaanbieding bijwerken | Azure Marketplace
description: Een bestaande IoT Edge-moduleaanbieding bijwerken op Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 019711f35d249e6684013feddb6b453d509f66d4
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985351"
---
# <a name="update-an-existing-iot-edge-module-offer"></a>Een bestaande IoT Edge-moduleaanbieding bijwerken

>[!Important]
>Vanaf 13 april 2020 beginnen we met het verplaatsen van het beheer van uw IoT Edge-moduleaanbiedingen naar Partner Center. Na de migratie maak en beheer je je aanbiedingen in partnercentrum. Volg de instructies in [Een IoT Edge-moduleaanbieding maken](https://aka.ms/AzureCreateIoT) om uw gemigreerde aanbiedingen te beheren.

In dit artikel worden de verschillende aspecten van het bijwerken van uw IoT Edge-moduleaanbieding in de [Cloud Partner Portal](https://cloudpartner.azure.com/) doorlopen en vervolgens de aanbieding opnieuw gepubliceerd.

Er zijn verschillende redenen waarom u uw aanbieding zou willen bijwerken, zoals:

-  Een nieuwe IoT Edge-moduleafbeeldingsversie toevoegen aan bestaande SKU's.
-  Het toevoegen van nieuwe SKU's.
-  Het bijwerken van de marktplaatsmetadata voor de aanbieding of individuele SKU's.

Om u te helpen bij deze wijzigingen, biedt de portal de functies **Vergelijken** en **Geschiedenis.**  


## <a name="unpermitted-changes-to-iot-edge-module-offer-or-sku"></a>Niet-toegestane wijzigingen in het aanbod van IoT Edge-modules of SKU

Er zijn kenmerken van een IoT Edge-moduleaanbieding of SKU die niet kunnen worden gewijzigd nadat de aanbieding live is op de Azure Marketplace. U de volgende instellingen niet wijzigen:

-  **Id van aanbieding** en **publisher ID** van de aanbieding
-  **SKU-id** van bestaande SKU's
-  Versietags, bijvoorbeeld:`1.0.1`
-  Wijzigingen in facturering/licentiemodel in bestaande SKU's

## <a name="common-update-operations"></a>Algemene updatebewerkingen

De volgende updatebewerkingen zijn gebruikelijk.

### <a name="update-the-iot-edge-module-image-version-for-a-sku"></a>De afbeeldingsversie van de IoT Edge-module voor een SKU bijwerken

Het is gebruikelijk dat een IoT Edge-moduleafbeelding periodiek wordt bijgewerkt met beveiligingspatches, extra functies, enzovoort. In dit scenario wilt u de Afbeelding van de IoT Edge-module waarnaar uw SKU verwijst, bijwerken met behulp van de volgende stappen:

1.  Meld u aan bij de [Cloud Partner Portal](https://cloudpartner.azure.com/).

2.  Zoek **onder Alle aanbiedingen**de aanbieding die u wilt bijwerken.

3.  Selecteer op het tabblad **SKU de** SKU die is gekoppeld aan de afbeelding van de IoT Edge-module die u wilt bijwerken.

4.  Selecteer **onder Afbeelding van de randmodule**de optie + Nieuwe **afbeeldingsversie** om een nieuwe IoT Edge-moduleafbeelding toe te voegen.

5.  Geef de nieuwe IoT **Edge-moduleafbeeldingsversies**op. De afbeeldingsversie moet dezelfde richtlijnen voor tags volgen als eerdere versies. Versietags moeten van het formulier X.Y.Z zijn, waarbij X, Y en Z gehele getallen zijn. Controleer of de nieuwe versie die u aanbiedt groter is dan alle vorige versies.

6.  Selecteer **Publiceren** om de werkstroom te starten om uw nieuwe IoT Edge-moduleversie te publiceren naar de Azure Marketplace.

### <a name="add-a-new-sku"></a>Een nieuwe SKU toevoegen

Gebruik de volgende stappen om een nieuwe SKU beschikbaar te maken voor uw aanbieding: 

1.  Meld u aan bij de [Cloud Partner Portal](https://cloudpartner.azure.com/).

2.  Zoek **onder Alle aanbiedingen**de aanbieding die u wilt bijwerken.

3.  Selecteer onder het tabblad **SKU's** de optie **Nieuwe SKU toevoegen** en geef een **SKU-id** op in het pop-upvenster.

4.  Publiceer de IoT Edge-module opnieuw met de stappen die zijn beschreven in [Een IoT Edge-module publiceren naar Azure Marketplace.](./cpp-publish-offer.md)

5.  Selecteer **Publiceren** om de werkstroom te starten om uw nieuwe SKU te publiceren.


### <a name="update-offer-marketplace-metadata"></a>Update bieden marktplaats metadata

Gebruik de volgende stappen om de metagegevens van de marktplaats bij te werken die aan uw aanbieding zijn gekoppeld. (Bijvoorbeeld: bedrijfsnaam, logo's, enz.)

1.  Meld u aan bij de [Cloud Partner Portal](https://cloudpartner.azure.com/).

2.  Onder **Alle aanbiedingen**vindt u de aanbieding die u wilt bijwerken.

3.  Ga naar het tabblad **Marketplace.** Gebruik de instructies in de [Publicatie-module IoT Edge in Azure Marketplace](./cpp-publish-offer.md) om metagegevens wijzigingen aan te brengen.

4.  Selecteer **Publiceren** om de werkstroom te starten om uw wijzigingen te publiceren.

## <a name="compare-feature"></a>Functie vergelijken

Wanneer u wijzigingen aanbrengt in een gepubliceerde aanbieding, u de functie **Vergelijken** gebruiken om de wijzigingen die u hebt aangebracht te controleren. 

**Ga als u de functie Vergelijken gebruiken:**

1.  Selecteer Op elk moment in het bewerkingsproces de optie **Vergelijken** voor uw aanbieding.

    ![Knop Functie vergelijken](./media/iot-edge-module-compare.png)


2.  Kijk naar side-by-side versies van marketing assets en metadata.


## <a name="history-of-publishing-actions"></a>Geschiedenis van publicatieacties

Als u historische publicatieactiviteiten wilt bekijken, selecteert u het tabblad **Geschiedenis** op de linkernavigatiemenubalk van Cloud Partner Portal. U de tijdstempelacties zien die zijn uitgevoerd tijdens de levensduur van uw Azure Marketplace-aanbiedingen.  <!-- Need to find correct link here:  legal time windowsFor more information, see [History page](cpp-history-page.md) -->
