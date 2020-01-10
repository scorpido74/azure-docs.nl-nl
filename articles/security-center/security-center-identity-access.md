---
title: Identiteit en toegang bewaken in Azure Security Center | Microsoft Docs
description: Lees hoe u de functies voor identiteit en toegang in Azure Security Center gebruikt om problemen met de toegang en identiteit van uw gebruikers te bewaken.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2019
ms.author: memildin
ms.openlocfilehash: 6b262baddd10c9d0dff4b196b733972b97d99872
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552981"
---
# <a name="monitor-identity-and-access-preview"></a>Identiteit en toegang bewaken (preview-versie)
Wanneer Security Center mogelijke beveiligings problemen identificeert, worden er aanbevelingen gemaakt die u door het proces van het configureren van de benodigde besturings elementen leiden om uw resources te beschermen en te beveiligen.

In dit artikel wordt de pagina **identiteit en toegang** van de sectie resource beveiliging van Azure Security Center uitgelegd.

Zie [aanbevelingen voor identiteits-en toegangs rechten](recommendations-reference.md#recs-identity)voor een volledige lijst met aanbevelingen die u op deze pagina kunt zien.

> [!NOTE]
> Bewakings identiteit en-toegang zijn in preview-versie en zijn alleen beschikbaar voor de standaard-laag van Security Center. Bekijk de pagina [Prijzen](security-center-pricing.md) voor meer informatie over de tariefopties van Security Center.
>

De identiteit moet het besturings vlak van uw onderneming zijn en het beschermen van identiteiten moet uw hoogste prioriteit hebben. De beveiligings verbinding is vanuit een netwerk verbinding met een identiteits verbinding ontwikkeld. De beveiliging wordt verminderd met het beschermen van uw netwerk en het beschermen van uw gegevens, en het beheren van de beveiliging van uw apps en gebruikers. Tegenwoordig bevinden zich steeds meer gegevens en apps in de cloud en is identiteit het nieuwe perimeternetwerk.

Door identiteitsactiviteiten te bewaken, kunt u proactieve maatregelen treffen voordat een incident plaatsvindt of reactieve maatregelen nemen om een aanvalspoging te stoppen. De identiteit & Access-dash board biedt aanbevelingen zoals:

- MFA inschakelen voor bevoegde accounts voor uw abonnement
- Externe accounts met schrijfmachtigingen verwijderen van uw abonnement
- Bevoegde externe accounts verwijderen van uw abonnement

> [!NOTE]
> Als uw abonnement meer dan 600 accounts bevat, kunnen Security Center de identiteits aanbevelingen niet uitvoeren op uw abonnement. De aanbevelingen die niet worden uitgevoerd, worden vermeld onder "niet-beschik bare evaluaties" hieronder.
Security Center kan de identiteits aanbevelingen niet uitvoeren op de beheer agenten van een Cloud Solution Provider (CSP)-partner.
>

## <a name="monitor-identity-and-access"></a>Identiteit en toegang bewaken

Open de lijst met geïdentificeerde identiteits-en toegangs problemen door **identiteit & toegang** te selecteren vanuit de Security Center zijbalk (onder **resources**) of op de pagina overzicht. 

Onder **identiteits & toegang**bevinden zich twee tabbladen:

- **Overzicht**: aanbevelingen die worden geïdentificeerd door Security Center.
- **Abonnementen**: de lijst met uw abonnementen en de huidige beveiligings status van elke.

[![identiteit & toegang](./media/security-center-identity-access/identity-dashboard.png)](./media/security-center-identity-access/identity-dashboard.png#lightbox)

### <a name="overview-section"></a>Overzichtssectie
Onder **overzicht**vindt u een lijst met aanbevelingen. De eerste kolom bevat de aanbeveling. De tweede kolom bevat het totale aantal abonnementen dat door deze aanbeveling wordt beïnvloed. De derde kolom toont de ernst van het probleem.

1. Selecteer een aanbeveling. Het venster aanbevelingen wordt geopend en geeft het volgende weer:

   - Beschrijving van de aanbeveling
   - Lijst met slechte en goede abonnementen
   - Lijst met resources die niet worden gescand vanwege een mislukte evaluatie of als de resource zich onder een abonnement bevindt dat wordt uitgevoerd op de gratis laag en niet wordt beoordeeld

    [het venster aanbevelingen voor ![](./media/security-center-identity-access/select-subscription.png)](./media/security-center-identity-access/select-subscription.png#lightbox)

1. Selecteer een abonnement in de lijst voor meer informatie.

### <a name="subscriptions-section"></a>Sectie abonnementen
Onder **abonnementen**bevindt zich een lijst met abonnementen. De eerste kolom bevat de abonnementen. De tweede kolom geeft het totale aantal aanbevelingen voor elk abonnement. In de derde kolom worden de ernst van de problemen weer gegeven.

[![tabblad abonnementen](./media/security-center-identity-access/subscriptions.png)](./media/security-center-identity-access/subscriptions.png#lightbox)

1. Selecteer een abonnement. Een overzicht wordt geopend met drie tabbladen:

   - **Aanbevelingen**: gebaseerd op evaluaties die zijn uitgevoerd door Security Center die zijn mislukt.
   - **Geslaagde evaluaties**: een lijst met evaluaties die zijn uitgevoerd door Security Center die zijn door gegeven.
   - **Niet-beschik bare evaluaties**: een lijst met evaluaties die niet kunnen worden uitgevoerd vanwege een fout of omdat het abonnement meer dan 600 accounts bevat.

   Onder **aanbevelingen** vindt u een lijst met aanbevelingen voor het geselecteerde abonnement en de ernst van elke aanbeveling.

   [Aanbevelingen ![voor Select-abonnement](./media/security-center-identity-access/recommendations.png)](./media/security-center-identity-access/recommendations.png#lightbox)

1. Selecteer een aanbeveling voor een beschrijving van de aanbeveling, een lijst met slechte en goede abonnementen en een lijst met niet-gescande resources.

   [![beschrijving van aanbeveling](./media/security-center-identity-access/designate.png)](./media/security-center-identity-access/designate.png#lightbox)

   Onder **geslaagde evaluaties** is een lijst met door gegeven evaluaties.  Ernst van deze beoordeling is altijd groen.

   [Geslaagde evaluaties ![](./media/security-center-identity-access/passed-assessments.png)](./media/security-center-identity-access/passed-assessments.png#lightbox)

1. Selecteer een door gegeven evaluatie in de lijst voor een beschrijving van de evaluatie en een lijst met goede abonnementen. Er is een tabblad voor de beschadigde abonnementen met een lijst met alle abonnementen die zijn mislukt.

   [Geslaagde evaluaties ![](./media/security-center-identity-access/remove.png)](./media/security-center-identity-access/remove.png#lightbox)

> [!NOTE]
> Als u een beleid voor voorwaardelijke toegang hebt gemaakt waarvoor MFA is vereist, maar er uitsluitingen zijn ingesteld, beschouwt de Security Center MFA-aanbevelings beoordeling het beleid dat niet compatibel is, omdat sommige gebruikers zich kunnen aanmelden bij Azure zonder MFA.

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende artikelen voor meer informatie over de aanbevelingen die van toepassing zijn op andere Azure-resource typen:

- [Uw machines en toepassingen beveiligen in Azure Security Center](security-center-virtual-machine-protection.md)
- [Protecting your network in Azure Security Center](security-center-network-recommendations.md) (Uw netwerk beveiligen in Azure Security Center)
- [Uw Azure SQL-service en-gegevens beveiligen in Azure Security Center](security-center-sql-service-recommendations.md)