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
ms.date: 05/30/2018
ms.author: memildin
ms.openlocfilehash: 8b069bedd3c36f27828e54a1110443ae6dfcdf3f
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268866"
---
# <a name="monitor-identity-and-access-in-azure-security-center-preview"></a>Identiteit en toegang bewaken in Azure Security Center (preview-versie)
In dit artikel leest u hoe u Azure Security Center kunt gebruiken om de identiteit en toegangsactiviteiten van gebruikers te bewaken.

> [!NOTE]
> Bewakings identiteit en-toegang zijn in preview-versie en zijn alleen beschikbaar voor de standaard-laag van Security Center. Bekijk de pagina [Prijzen](security-center-pricing.md) voor meer informatie over de tariefopties van Security Center.
>

Identiteit moet de controlelaag voor uw onderneming zijn. Het beveiligen van uw identiteit moet daarom de hoogste prioriteit hebben. De beveiligings verbinding is vanuit een netwerk verbinding met een identiteits verbinding ontwikkeld. De beveiliging wordt verminderd met het beschermen van uw netwerk en het beschermen van uw gegevens, en het beheren van de beveiliging van uw apps en gebruikers. Tegenwoordig bevinden zich steeds meer gegevens en apps in de cloud en is identiteit het nieuwe perimeternetwerk.

Door identiteitsactiviteiten te bewaken, kunt u proactieve maatregelen treffen voordat een incident plaatsvindt of reactieve maatregelen nemen om een aanvalspoging te stoppen. De identiteit & Access-dash board biedt aanbevelingen zoals:

- MFA inschakelen voor bevoegde accounts voor uw abonnement
- Externe accounts met schrijfmachtigingen verwijderen van uw abonnement
- Bevoegde externe accounts verwijderen van uw abonnement

> [!NOTE]
> Als uw abonnement meer dan 600 accounts bevat, kunnen Security Center de identiteits aanbevelingen niet uitvoeren op uw abonnement. Aanbevelingen die niet worden uitgevoerd, worden vermeld onder ' niet-beschik bare evaluaties ' die hieronder worden beschreven.
Security Center kan de identiteits aanbevelingen niet uitvoeren op de beheer agenten van een Cloud Solution Provider (CSP)-partner.
>

Zie [aanbevelingen](security-center-identity-access.md#recommendations) voor een lijst met de aanbevelingen voor identiteits-en toegang die door Security Center worden aangeboden.

## <a name="monitoring-security-health"></a>Beveiligingsstatus bewaken
U kunt de beveiligingsstatus van uw resources bewaken op de **Security Center-overzicht** dashboard. De sectie **resources** is een status indicator met de ernst voor elk resource type.

U kunt een lijst van alle problemen weergeven door te selecteren **aanbevelingen**. Onder **resources**kunt u een lijst met problemen bekijken die specifiek zijn voor het berekenen van & apps, gegevens beveiliging, netwerken of identiteits & toegang. Zie voor meer informatie over het toepassen van aanbevelingen [beveiligingsaanbevelingen implementeren in Azure Security Center](security-center-recommendations.md).

Zie [aanbevelingen](security-center-identity-access.md#recommendations)voor een volledige lijst met aanbevelingen voor identiteits-en toegangs vereisten.

Als u wilt door gaan, selecteert u **identiteit & toegang** onder **resources** of in het hoofd menu van Security Center.

![Dashboard van Security Center][1]

## <a name="monitor-identity-and-access"></a>Identiteit en toegang bewaken
Onder **identiteits & toegang**bevinden zich twee tabbladen:

- **Overzicht**: aanbevelingen die worden geïdentificeerd door Security Center.
- **Abonnementen**: de lijst met uw abonnementen en de huidige beveiligings status van elke.

![Identity & Access][2]

### <a name="overview-section"></a>Overzichts sectie
Onder **overzicht**vindt u een lijst met aanbevelingen. De eerste kolom bevat de aanbeveling. De tweede kolom bevat het totale aantal abonnementen dat door deze aanbeveling wordt beïnvloed. De derde kolom toont de ernst van het probleem.

1. Selecteer een aanbeveling. Het venster aanbevelingen wordt geopend en geeft het volgende weer:

   - Beschrijving van de aanbeveling
   - Lijst met slechte en goede abonnementen
   - Lijst met resources die niet worden gescand vanwege een mislukte evaluatie of als de resource zich onder een abonnement bevindt dat wordt uitgevoerd op de gratis laag en niet wordt beoordeeld

   ![Venster van aanbeveling][3]

1. Selecteer een abonnement in de lijst voor meer informatie.

### <a name="subscriptions-section"></a>Sectie abonnementen
Onder **abonnementen**bevindt zich een lijst met abonnementen. De eerste kolom bevat de abonnementen. De tweede kolom geeft het totale aantal aanbevelingen voor elk abonnement. In de derde kolom worden de ernst van de problemen weer gegeven.

![Tabblad abonnementen][4]

1. Selecteer een abonnement. Een overzicht wordt geopend met drie tabbladen:

   - **Aanbevelingen**: op basis van beoordelingen die door Security Center die niet zijn uitgevoerd.
   - **Evaluaties doorgegeven**: lijst met beoordelingen uitgevoerd door Security Center die doorgegeven.
   - **Niet-beschik bare evaluaties**: een lijst met evaluaties die niet kunnen worden uitgevoerd vanwege een fout of omdat het abonnement meer dan 600 accounts bevat.

   Onder **aanbevelingen** vindt u een lijst met aanbevelingen voor het geselecteerde abonnement en de ernst van elke aanbeveling.

   ![Aanbevelingen voor Select-abonnement][5]

1. Selecteer een aanbeveling voor een beschrijving van de aanbeveling, een lijst met slechte en goede abonnementen en een lijst met niet-gescande resources.

   ![Beschrijving van aanbeveling][6]

   Onder **evaluaties doorgegeven** is een lijst van geslaagde evaluaties.  Ernst van deze beoordeling is altijd groen.

   ![Geslaagde evaluaties][7]

1. Selecteer een door gegeven evaluatie in de lijst voor een beschrijving van de evaluatie en een lijst met goede abonnementen. Er is een tabblad voor de beschadigde abonnementen met een lijst met alle abonnementen die zijn mislukt.

   ![Geslaagde evaluaties][8]

## <a name="recommendations"></a>Aanbevelingen
Gebruik de onderstaande tabel als referentie om inzicht te krijgen in de beschik bare identiteit & aanbevelingen voor toegang en wat er allemaal gebeurt als u deze toepast.

|Resourcetype|Beveiligingsscore|Aanbeveling|Description|
|----|----|----|----|
|Subscription|50|MFA moet zijn ingeschakeld voor accounts met eigenaars machtigingen voor uw abonnement|Schakel multi-factor Authentication (MFA) in voor alle abonnements accounts met beheerders bevoegdheden om te voor komen dat accounts of bronnen worden geschonden.|
|Subscription|40|MFA moet zijn ingeschakeld voor uw abonnements accounts met schrijf machtigingen|Schakel multi-factor Authentication (MFA) in voor alle abonnements accounts met schrijf bevoegdheden om te voor komen dat accounts of bronnen worden geschonden.|
|Subscription|30|Externe accounts met eigenaars machtigingen moeten worden verwijderd uit uw abonnement|Verwijder externe accounts met eigenaars machtigingen van uw abonnement om onbewaakte toegang te voor komen.|
|Subscription|30|MFA moet zijn ingeschakeld voor uw abonnements accounts met lees machtigingen|Schakel multi-factor Authentication (MFA) in voor alle abonnements accounts met lees rechten om te voor komen dat accounts of bronnen worden geschonden.|
|Subscription|25|Externe accounts met schrijfmachtigingen moeten worden verwijderd uit uw abonnement|Verwijder externe accounts met schrijf machtigingen van uw abonnement om onbewaakte toegang te voor komen. |
|Subscription|20|Afgeschafte accounts met eigenaars machtigingen moeten worden verwijderd uit uw abonnement|Afgeschafte accounts met eigenaars machtigingen uit uw abonnementen verwijderen.|
|Subscription|5|Afgeschafte accounts moeten worden verwijderd uit uw abonnement|Verouderde accounts uit uw abonnementen verwijderen om alleen de huidige gebruikers toegang te geven. |
|Subscription|5|Er moet meer dan één eigenaar aan uw abonnement zijn toegewezen|Wijs meer dan een eigenaar van het abonnement aan om beheerders toegang te laten hebben.|
|Subscription|5|Er moeten Maxi maal drie eigen aren worden opgegeven voor uw abonnement|Wijs minder dan 3 abonnements eigenaren toe om de kans op schending door een verzwakte eigenaar te verminderen.|
|Sleutelkluis|5|Diagnostische logboeken in Key Vault moeten worden ingeschakeld|Logboeken inschakelen en ze maximaal een jaar bewaren. Hiermee kunt u activiteit sporen onderzoek opnieuw maken wanneer een beveiligingsincident voordoet of uw netwerk is gecompromitteerd. |
|Subscription|15|Externe accounts met lees machtigingen moeten worden verwijderd uit uw abonnement|Verwijder externe accounts met lees rechten van uw abonnement om onbewaakte toegang te voor komen.| 

> [!NOTE]
> Als u een beleid voor voorwaardelijke toegang hebt gemaakt waarvoor MFA is vereist, maar er uitsluitingen zijn ingesteld, beschouwt de Security Center MFA-aanbevelings beoordeling het beleid dat niet compatibel is, omdat sommige gebruikers zich kunnen aanmelden bij Azure zonder MFA.

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende artikelen voor meer informatie over de aanbevelingen die van toepassing zijn op andere Azure-resource typen:

- [Uw machines en toepassingen beveiligen in Azure Security Center](security-center-virtual-machine-protection.md)
- [Protecting your network in Azure Security Center](security-center-network-recommendations.md) (Uw netwerk beveiligen in Azure Security Center)
- [Uw Azure SQL-service en-gegevens beveiligen in Azure Security Center](security-center-sql-service-recommendations.md)

Zie de volgende artikelen voor meer informatie over Security Center:
* [Beveiligingswaarschuwingen beheren en erop reageren in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Informatie over het beheren van waarschuwingen en het reageren op beveiligingsincidenten in Security Center.
* [Beveiligingswaarschuwingen in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Meer informatie over de verschillende typen beveiligingswaarschuwingen.
* [Veelgestelde vragen over Azure Security Center](security-center-faq.md). Vind antwoorden op veelgestelde vragen over het gebruik van Security Center.


<!--Image references-->
[1]: ./media/security-center-identity-access/overview.png
[2]: ./media/security-center-identity-access/identity-dashboard.png
[3]: ./media/security-center-identity-access/select-subscription.png
[4]: ./media/security-center-identity-access/subscriptions.png
[5]: ./media/security-center-identity-access/recommendations.png
[6]: ./media/security-center-identity-access/designate.png
[7]: ./media/security-center-identity-access/passed-assessments.png
[8]: ./media/security-center-identity-access/remove.png
