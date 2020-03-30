---
title: Een bestaande Azure-toepassingsaanbieding bijwerken | Azure Marketplace
description: Een bestaande Azure-toepassingsaanbieding bijwerken op de Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: dsindona
ms.openlocfilehash: 152fd24fbc5d2762d381ffce2a937bc448858b0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275948"
---
# <a name="update-an-existing-azure-application-offer"></a>Een bestaande Azure-toepassingsaanbieding bijwerken

Er zijn verschillende soorten updates die u zou willen doen om uw aanbieding nadat het is gepubliceerd en is live. Elke wijziging die u aanbrengt in uw nieuwe versie van uw aanbieding, moet worden opgeslagen en opnieuw worden gepubliceerd om deze in de Marketplace te laten weergeven. In dit artikel worden de verschillende aspecten van het bijwerken van uw beheerde toepassingsaanbod in de [Cloud Partner Portal doorgenomen.](https://cloudpartner.azure.com/)

Er zijn verschillende redenen waarom u uw aanbieding zou willen bijwerken, zoals:

- Een nieuwe afbeeldingsversie toevoegen aan bestaande SKU's.
- Het toevoegen van nieuwe SKU's.
- Het bijwerken van de marktplaatsmetadata voor de aanbieding of individuele SKU's.

Om u te helpen bij deze wijzigingen, biedt de portal de functies **Vergelijken** en **Geschiedenis.**

## <a name="unpermitted-changes-to-an-azure-application-offer-or-sku"></a>Niet-toegestane wijzigingen in een Azure-toepassingsaanbieding of SKU

Er zijn kenmerken van een containeraanbieding of SKU die niet kunnen worden gewijzigd nadat de aanbieding live is op de Azure Marketplace. U de volgende instellingen niet wijzigen:

- Id van aanbieding en publisher ID van de aanbieding
- SKU-id van bestaande SKU's
- Versietags, bijvoorbeeld:`1.0.1`
- Wijzigingen in facturering/licentiemodel in bestaande SKU's

## <a name="common-update-operations"></a>Algemene updatebewerkingen

De volgende updatebewerkingen zijn gebruikelijk.

### <a name="update-image-version-for-a-sku"></a>Afbeeldingsversie voor een SKU bijwerken

Het is gebruikelijk dat een afbeelding periodiek wordt bijgewerkt met beveiligingspatches, extra functies, enzovoort. In dit scenario wilt u de afbeelding bijwerken waarnaar uw SKU verwijst met de volgende stappen:

1. Meld u aan bij de [Cloud Partner Portal](https://cloudpartner.azure.com/).
2. Zoek **onder Alle aanbiedingen**de aanbieding die u wilt bijwerken.
3. Selecteer op het tabblad **SKU** de SKU die is gekoppeld aan de afbeelding die u wilt bijwerken.
4. Selecteer **+ Nieuwe afbeeldingsversie** om een nieuwe afbeelding toe te voegen.
5. Geef de nieuwe afbeeldingsversies op. De afbeeldingsversie moet dezelfde richtlijnen voor tags volgen als eerdere versies. Versietags moeten van het formulier X.Y.Z zijn, waarbij X, Y en Z gehele getallen zijn. Controleer of de nieuwe versie die u aanbiedt groter is dan alle vorige versies.
6. Selecteer **Publiceren** om de werkstroom te starten om uw nieuwe containerafbeeldingsversie te publiceren op de Azure Marketplace.

### <a name="add-a-new-sku"></a>Een nieuwe SKU toevoegen

Gebruik de volgende stappen om een nieuwe SKU beschikbaar te maken voor uw aanbieding:

1. Meld u aan bij de [Cloud Partner Portal](https://cloudpartner.azure.com/).
2. Zoek **onder Alle aanbiedingen**de aanbieding die u wilt bijwerken.
3. Selecteer onder het tabblad **SKU's** de optie **Nieuwe SKU toevoegen** en geef een **SKU-id** op in het pop-upvenster.
4. Publiceer de aanbieding opnieuw met de stappen die zijn beschreven in [de aanbieding Azure-toepassingspubliceren](./cpp-publish-offer.md).
5. Selecteer **Publiceren** om de werkstroom te starten om uw nieuwe SKU te publiceren.

### <a name="update-offer-marketplace-metadata"></a>Update bieden marktplaats metadata

Gebruik de volgende stappen om de metagegevens van de marktplaats bij te werken die aan uw aanbieding zijn gekoppeld. (Bijvoorbeeld: bedrijfsnaam, logo's, enz.)

1. Meld u aan bij de [Cloud Partner Portal](https://cloudpartner.azure.com/).
2. Zoek **onder Alle aanbiedingen**de aanbieding die u wilt bijwerken.
3. Ga naar het tabblad **Marketplace.** Gebruik de instructies in [de aanbieding Azure-toepassing publiceren](./cpp-publish-offer.md) om metagegevens wijzigingen aan te brengen.
4. Selecteer **Publiceren** om de werkstroom te starten om uw wijzigingen te publiceren.
 
>[!Note]
>Cloud Solution Providers (CSP) partnerkanaal opt-in is nu beschikbaar.  Raadpleeg [Cloud Solution Providers](../../cloud-solution-providers.md) voor meer informatie over het op de markt brengen van uw aanbieding via de Microsoft CSP-partnerkanalen.

## <a name="deleting-an-existing-offer"></a>Een bestaand aanbod verwijderen

Je besluiten je aanbieding van de Marketplace te verwijderen. Het verwijderen van een aanbieding heeft geen invloed op de huidige aankopen van die aanbieding. Deze aankopen van klanten blijven werken zoals voorheen. De aanbieding is echter niet beschikbaar voor nieuwe aankopen nadat de verwijdering is voltooid.

Beëindiging van de aanbieding is het proces van beëindiging van de service- en/of licentieovereenkomst tussen u en uw bestaande klanten.
Richtlijnen en beleidsregels met betrekking tot het verwijderen en beëindigen van aanbiedingen vallen onder de Microsoft Marketplace Publisher Agreement (zie sectie 7) en het participatiebeleid (zie paragraaf 6.2).

Zie [Verwijderen en aanbieden/SKU van Azure Marketplace](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-managed-app-offer-delete)voor meer informatie.

## <a name="compare-feature"></a>Functie vergelijken

Wanneer u wijzigingen aanbrengt in een gepubliceerde aanbieding, u de functie Vergelijken gebruiken om de wijzigingen die u hebt aangebracht te controleren.

Ga als u de functie Vergelijken gebruiken:

1. Selecteer Op elk moment in het bewerkingsproces de optie Vergelijken voor uw aanbieding.
2. Kijk naar side-by-side versies van marketing assets en metadata.

## <a name="history-of-publishing-actions"></a>Geschiedenis van publicatieacties

Als u historische publicatieactiviteiten wilt bekijken, selecteert u het tabblad **Geschiedenis** op de linkernavigatiemenubalk van Cloud Partner Portal. U de tijdstempelacties zien die zijn uitgevoerd tijdens de levensduur van uw Azure Marketplace-aanbiedingen.

## <a name="next-steps"></a>Volgende stappen

[Azure-toepassingsaanbieding](./cpp-azure-app-offer.md)
