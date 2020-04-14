---
title: Een bestaande Azure-containeraanbieding bijwerken | Azure Marketplace
description: Een bestaande containeraanbieding bijwerken op Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: dsindona
ms.openlocfilehash: 650ef45e56184b67efba67810580d9d6e763a4d0
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81271264"
---
# <a name="update-an-existing-container-offer"></a>Een bestaande containeraanbieding bijwerken

> [!IMPORTANT]
> Vanaf 13 april 2020 beginnen we met het verplaatsen van het beheer van uw Azure Container-aanbiedingen naar partnercentrum. Na de migratie maak en beheer je je aanbiedingen in partnercentrum. Volg de instructies in [Een Azure Container-aanbieding maken](https://aka.ms/CreateContainerOffer) om uw gemigreerde aanbiedingen te beheren.

In dit artikel worden de verschillende aspecten van het bijwerken van uw containeraanbod in de [Cloud Partner Portal doorgenomen.](https://cloudpartner.azure.com/)

Er zijn verschillende redenen waarom u uw aanbieding zou willen bijwerken, zoals:

-  Een nieuwe containerafbeeldingsversie toevoegen aan bestaande SKU's.
-  Het toevoegen van nieuwe SKU's.
-  Het bijwerken van de marktplaatsmetadata voor de aanbieding of individuele SKU's.

Om u te helpen bij deze wijzigingen, biedt de portal de functies **Vergelijken** en **Geschiedenis.**  


## <a name="unpermitted-changes-to-a-container-offer-or-sku"></a>Niet-toegestane wijzigingen in een containeraanbieding of SKU

Er zijn kenmerken van een containeraanbieding of SKU die niet kunnen worden gewijzigd nadat de aanbieding live is op de Azure Marketplace. U de volgende instellingen niet wijzigen:

-  **Id van aanbieding** en **publisher ID** van de aanbieding
-  **SKU-id** van bestaande SKU's
-  Versietags, bijvoorbeeld:`1.0.1`
-  Wijzigingen in facturering/licentiemodel in bestaande SKU's

## <a name="common-update-operations"></a>Algemene updatebewerkingen

De volgende updatebewerkingen zijn gebruikelijk.

### <a name="update-container-image-version-for-a-sku"></a>Containerafbeeldingsversie voor een SKU bijwerken

Het is gebruikelijk dat een containerafbeelding periodiek wordt bijgewerkt met beveiligingspatches, extra functies, enzovoort. In dit scenario wilt u de containerafbeelding bijwerken waarnaar uw SKU verwijst met de volgende stappen:

1. Meld u aan bij de [Cloud Partner Portal](https://cloudpartner.azure.com/).
2. Zoek **onder Alle aanbiedingen**de aanbieding die u wilt bijwerken.
3. Selecteer op het tabblad **SKU** de SKU die is gekoppeld aan de containerafbeelding die u wilt bijwerken.
4. Selecteer **onder Containerafbeelding**de optie **+ Nieuwe afbeeldingsversie** om een nieuwe containerafbeelding toe te voegen.
5. Geef de nieuwe **versies van containerafbeeldingen op.** De afbeeldingsversie moet dezelfde richtlijnen voor tags volgen als eerdere versies. Versietags moeten van het formulier X.Y.Z zijn, waarbij X, Y en Z gehele getallen zijn. Controleer of de nieuwe versie die u aanbiedt groter is dan alle vorige versies.
6. Selecteer **Publiceren** om de werkstroom te starten om uw nieuwe containerafbeeldingsversie te publiceren op de Azure Marketplace.

### <a name="add-a-new-sku"></a>Een nieuwe SKU toevoegen

Gebruik de volgende stappen om een nieuwe SKU beschikbaar te maken voor uw aanbieding:

1. Meld u aan bij de [Cloud Partner Portal](https://cloudpartner.azure.com/).
2. Zoek **onder Alle aanbiedingen**de aanbieding die u wilt bijwerken.
3. Selecteer onder het tabblad **SKU's** de optie **Nieuwe SKU toevoegen** en geef een **SKU-id** op in het pop-upvenster.
4. Publiceer de container opnieuw met de stappen die zijn beschreven in [aanbieding Voor de container publiceren](./cpp-publish-offer.md).
5. Selecteer **Publiceren** om de werkstroom te starten om uw nieuwe SKU te publiceren.

### <a name="update-offer-marketplace-metadata"></a>Update bieden marktplaats metadata

Gebruik de volgende stappen om de metagegevens van de marktplaats bij te werken die aan uw aanbieding zijn gekoppeld. (Bijvoorbeeld: bedrijfsnaam, logo's en etc.)

1. Meld u aan bij de [Cloud Partner Portal](https://cloudpartner.azure.com/).
2. Zoek **onder Alle aanbiedingen**de aanbieding die u wilt bijwerken.
3. Ga naar het tabblad **Marketplace.** Gebruik de instructies in het artikel [Aanbiedingsaanbod publiceren](./cpp-publish-offer.md) om metagegevens wijzigingen aan te brengen.
4. Selecteer **Publiceren** om de werkstroom te starten om uw wijzigingen te publiceren.

## <a name="compare-feature"></a>Functie vergelijken

Wanneer u wijzigingen aanbrengt in een gepubliceerde aanbieding, u de functie **Vergelijken** gebruiken om de wijzigingen die u hebt aangebracht te controleren.

### <a name="to-use-the-compare-feature"></a>Ga als u de functie Vergelijken gebruiken:

1. Selecteer Op elk moment in het bewerkingsproces de optie Vergelijken voor uw aanbieding.
2. Kijk naar side-by-side versies van marketing assets en metadata.


## <a name="history-of-publishing-actions"></a>Geschiedenis van publicatieacties

Als u historische publicatieactiviteiten wilt bekijken, selecteert u het tabblad **Geschiedenis** op de linkernavigatiemenubalk van Cloud Partner Portal. U de tijdstempelacties zien die zijn uitgevoerd tijdens de levensduur van uw Azure Marketplace-aanbiedingen.
