---
title: Een consultingserviceaanbieding maken in partnercentrum - Azure Marketplace
description: Meer informatie over het publiceren van een Consulting-serviceaanbieding naar Azure Marketplace of AppSource met behulp van Partner Center.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: f1354ce824bc4e57ab55846c714d4a0a73a46966
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80873817"
---
# <a name="create-a-consulting-service-offer"></a>Een consultingserviceaanbieding maken

> [!IMPORTANT]
> We verplaatsen het beheer van uw Consulting-serviceaanbiedingen van Cloud Partner Portal naar Partner Center. Volg de instructies in Azure en [Dynamics 365 consulting service voor](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/consulting-services/cloud-partner-portal-consulting-services-publishing-offer) Cloud Partner Portal om uw aanbiedingen te beheren totdat uw aanbiedingen zijn gemigreerd.

In dit artikel wordt beschreven hoe u een Consulting-serviceaanbieding publiceert naar [Azure Marketplace](https://azuremarketplace.microsoft.com/) of [AppSource.](https://appsource.microsoft.com/) List Consulting-serviceaanbiedingen op basis van Microsoft [Dynamics 365](https://dynamics.microsoft.com/) en Power Platform op AppSource. Lijst consulting-serviceaanbiedingen op basis van Microsoft Azure op Azure Marketplace.

Als u een Consulting-serviceaanbieding wilt maken in Azure Marketplace- of AppSource Consulting-services, moet u eerst [een uitgeversaccount hebben in het Partnercentrum](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)en moet uw account zijn ingeschreven voor het commerciële marktplaatsprogramma. Voordat u uw aanbieding maakt, bekijkt u de vereisten in [consultingservicevereisten.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/consulting-service-prerequisites)

## <a name="publishing-benefits"></a>Publicatievoordelen

Voordelen van publiceren op de commerciële marktplaats:

- Promoot uw bedrijf met het microsoft-merk.
- Bereik mogelijk meer dan 100 miljoen Office 365- en Dynamics 365-gebruikers via AppSource en meer dan 200.000 organisaties via Azure Marketplace.
- Ontvang leads van hoge kwaliteit van deze marktplaatsen.
- Laat uw services promoten door de Microsoft-veld- en telesalesteams

## <a name="create-a-new-offer"></a>Een nieuwe aanbieding maken

Volg deze stappen om een consultingserviceaanbieding te maken nadat u aan de hierboven beschreven vereisten hebt voldaan.

1. Meld u aan bij [Partnercentrum](https://partner.microsoft.com)en selecteer **Dashboard** in het bovenste menu.
2. Selecteer in de linkernavigatiebalk de optie **Commerciële marktplaats**en selecteer **Overzicht**.

    :::image type="content" source="media/cs-menu-overview.png" alt-text="Illustreert het menu voor commerciële marktplaats":::

3. Selecteer **+ Nieuwe aanbieding**en selecteer vervolgens **Consulting-service**.

    :::image type="content" source="media/cs-menu-newoffer.png" alt-text="Hiermee wordt de knop weergegeven om een nieuwe aanbieding te maken.":::

4. Voer een **aanbiedings-id in**. Dit is een unieke id voor elke aanbieding in uw account.

    - Deze ID is zichtbaar voor klanten in het webadres voor de marktplaatsaanbieding.
    - Gebruik alleen kleine letters, cijfers, streepjes en underscores, maar geen spaties. De lengte is beperkt tot 50 tekens. Als u bijvoorbeeld **test-aanbieding-1**invoert, is `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`de URL van de aanbieding .
    - De aanbiedings-id kan&#39;niet worden gewijzigd nadat u **Maken**hebt geselecteerd.

5. Voer een **aanbiedingsalias**in . Dit is de naam die wordt gebruikt om te verwijzen naar de aanbieding in Partner Center.

    - Deze naam wordt&#39;niet gebruikt in de markt. Het&#39;verschilt van de naam van de aanbieding en andere waarden die aan klanten worden getoond. U dit veld gebruiken om een naam toe te wijzen aan de aanbieding die nuttiger voor u is om de aanbieding intern te identificeren. het wordt niet getoond aan klanten.
    - De aanbiedingsalias kan&#39;niet worden gewijzigd nadat u **Maken**hebt geselecteerd.

Nadat u deze twee waarden hebt ingevoerd, selecteert u **Maken** om door te gaan naar de pagina **Aanbiedingsinstelling.**

## <a name="offer-setup"></a>Instelling voor aanbieding

Nadat u een aanbiedings-id en aanbiedingsalias hebt ingevoerd, maakt partnercentrum een conceptaanbieding en geeft u de pagina **Aanbiedingsinstelling weer.** Volg deze stappen om uw aanbieding in te stellen.

### <a name="connect-lead-management"></a>Leadbeheer verbinden

Wanneer u uw aanbieding op de marktplaats publiceert met Partner Center, _moet_ u deze koppelen aan een CRM (Customer Relationship Management) of een marketingautomatiseringssysteem. Zo u klantcontactgegevens ontvangen zodra iemand interesse toont in of uw product gebruikt.

1. Selecteer **Verbinding maken** om aan te geven waar u wilt dat we klantleads verzenden. Partner Center ondersteunt de volgende systemen:

    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) voor customer engagement
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [SalesForce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Als uw CRM-systeem niet&#39;hierboven vermeld, gebruikt u [Azure Table](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) of [Https Endpoint](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) om klantgegevens op te slaan en exporteert u de gegevens naar uw CRM-systeem.

2. Verbind uw aanbieding met de hoofdbestemming wanneer u publiceert in partnercentrum.
3. Controleer of de verbinding met de hoofdbestemming correct is geconfigureerd. Nadat u deze hebt gepubliceerd in het Partnercenter,&#39;we de verbinding valideren en u een testlead sturen. Terwijl u een voorbeeld van de aanbieding bekijkt voordat deze live gaat, u ook uw leadverbinding testen door te proberen het aanbod zelf te kopen in de preview-omgeving.
4. Zorg ervoor dat de verbinding met de hoofdbestemming up-to-date blijft, zodat u geen leads&#39;verliezen.

Hier volgen enkele aanvullende bronnen voor leadbeheer:

- [Overzicht van leadbeheer](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [Veelgestelde vragen over leadmanagement](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Algemene leadconfiguratiefouten](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Overzicht van leadbeheer](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (Zorg ervoor dat uw pop-upblokkering is uitgeschakeld)

Selecteer **Concept opslaan** voordat u doorgaat naar de volgende sectie, Eigenschappen.

### <a name="properties"></a>Eigenschappen

Op deze pagina u het primaire product instellen dat de beste dekking biedt voor uw Consulting-service, een Consulting-servicetype instellen en toepasselijke producten kiezen.

1. Selecteer een **primair product** in de vervolgkeuzelijst.
2. Selecteer een **consulting-servicetype** in de vervolgkeuzelijst:

    - **Beoordeling** : Een evaluatie van de omgeving van een klant&#39;om de toepasbaarheid van een oplossing te bepalen en een schatting te geven van kosten en timing.
    - **Briefing** : Een inleiding tot een oplossing of een Consulting-service om de interesse van de klant te wekken door gebruik te maken van frameworks, demo's en voorbeelden van klanten.
    - **Implementatie** : Een complete installatie die resulteert in een volledig werkende oplossing. Beperk oplossingen die binnen twee weken of minder kunnen worden geïmplementeerd.
    - **Proof of concept** : Een implementatie met beperkte scope om te bepalen of een oplossing voldoet aan de eisen van de klant.
    - **Workshop** : Een interactieve betrokkenheid uitgevoerd op het terrein van een klant&#39;. Het kan gaan om training, briefings, assessments of demo's die zijn gebouwd op de gegevens of omgeving van de klant&#39;.

1. Als u een primair product van **Azure**hebt geselecteerd, selecteert u maximaal drie **oplossingsgebieden**. Deze maken het voor klanten in Azure Marketplace gemakkelijker om uw aanbieding te vinden. Als u&#39;niet voor Azure hebt gekozen, slaat u deze stap over.
2. Als u een _ander_ primair product dan Azure hebt geselecteerd, selecteert u maximaal drie **toepasselijke producten**. Deze maken het makkelijker voor klanten in AppSource om uw aanbod te vinden. Zie Richtlijnen voor [Microsoft AppSource Consulting Service Listing (PDF)](https://go.microsoft.com/fwlink/?LinkId=828734&amp;clcid=0x409) voor meer informatie.
3. Selecteer maximaal zes **industrieën waarop** uw aanbieding van toepassing is. Dit maakt het makkelijker voor klanten om uw aanbod te vinden.
4. Voeg maximaal drie **competenties toe** die uw bedrijf heeft verdiend om weer te geven op uw aanbieding voor consultingservices. Ten minste één competentie is vereist, behalve voor Azure Expert MSP&#39;s en Azure Networking MSP&#39;s.

Selecteer **Concept opslaan** voordat u doorgaat naar de volgende sectie, Aanbieding smaken.

## <a name="offer-listing"></a>Aanbieding aanbieden

Hier&#39;de aanbiedingsgegevens definiëren die op de markt worden weergegeven. Dit omvat de aanbiedingnaam, beschrijving, afbeeldingen, enzovoort. Volg het beleid op de [beleidspagina van Microsoft&#39;](https://docs.microsoft.com/legal/marketplace/certification-policies#800-consulting-services) tijdens het configureren van deze aanbieding.

> [!NOTE]
> Aanbiedingdetails zijn&#39;niet verplicht om in het Engels &quot;te zijn als de aanbiedingsbeschrijving begint met de woordgroep, Deze toepassing is alleen beschikbaar in [niet-Engelse taal]. &quot; Het&#39;is ook goed om een nuttige link te bieden om inhoud aan te bieden in een taal die anders&#39;dan die in de aanbiedingsgegevens van de aanbieding.

### <a name="name"></a>Name

De naam die u hier invoert, wordt weergegeven als de titel van uw aanbod. Dit veld is vooraf gevuld met de tekst die u hebt ingevoerd in het vak **Alias aanbieden** toen u de aanbieding hebt gemaakt. U kunt deze naam later wijzigen.

De naam:

- Kan een handelsmerk zijn (en u handelsmerk- of auteursrechtsymbolen opnemen).
- Kan&#39;niet meer dan 50 tekens lang.
- Kan&#39;geen emoji's bevatten.

### <a name="search-results-summary"></a>Overzicht van zoekresultaten

Geef een korte beschrijving van uw aanbod. Dit kan maximaal 100 tekens lang zijn en wordt gebruikt in de zoekresultaten van marktplaatsen.

### <a name="description"></a>Beschrijving

Geef een langere beschrijving van uw aanbieding, tot 3.000 tekens. Dit wordt weergegeven aan klanten in het overzicht van de marktplaatsvermelding.

Neem een of meer van de volgende gegevens op in uw beschrijving:

- De waarde en de belangrijkste voordelen van uw aanbod
- Categorie- of brancheorganisaties, of beide
- In-app aankoopmogelijkheden
- Eventuele vereiste informatie

Hier zijn enkele tips voor het schrijven van uw beschrijving:

- Beschrijf duidelijk de waarde van uw aanbieding in de eerste paar zinnen van uw beschrijving. Neem de volgende items op:
  - Beschrijving van het aanbod.
  - Het type gebruiker dat profiteert van het aanbod.
  - De behoeften van de klant of geeft de aanbiedingsadressen uit.
- Vergeet niet dat de eerste paar zinnen kunnen worden weergegeven in de zoekresultaten.
- Vertrouw&#39;niet op functies en functionaliteit om uw product te verkopen. Richt u in plaats daarvan op de waarde die uw aanbieding biedt.
- Probeer branchespecifieke woordenschat of op voordelen gebaseerde formuleringen te gebruiken.

Als u uw beschrijving aantrekkelijker wilt maken, gebruikt u de editor voor rijke tekst om uw beschrijving op te maken. Met de rich text editor u getallen, opsommingstekens, vet, cursief en inspringingen toevoegen om uw beschrijving leesbaarder te maken.

:::image type="content" source="media/cs-rich-text-editor.png" alt-text="Illustreert de teksteditor met rijke tekst om de aanbiedingsbeschrijving te schrijven." border="false":::

### <a name="keywords"></a>Trefwoorden

Voer maximaal drie zoekwoorden in die relevant zijn voor uw primaire product- en consultingservice. Deze zullen het gemakkelijker maken om uw aanbod te vinden.

### <a name="duration"></a>Duur

Stel de verwachte duur van deze overeenkomst met uw klant in.

### <a name="contact-information"></a>Contactgegevens

U moet de naam, e-mail en het telefoonnummer opgeven voor een **primaire** en **secundaire contactpersoon.** Deze informatie wordt&#39;niet aan klanten getoond. Het is beschikbaar voor Microsoft en kan worden verstrekt aan Cloud Solution Provider (CSP) partners.

### <a name="supporting-documents"></a>Bewijsstukken

Voeg maximaal drie (maar ten minste één) ondersteunende PDF-documenten toe voor uw aanbieding.

### <a name="marketplace-images"></a>Marketplace-afbeeldingen

Geef logo's en afbeeldingen om te gebruiken met uw aanbod. Alle afbeeldingen moeten in .png-indeling zijn. Wazige afbeeldingen worden afgewezen.

#### <a name="store-logos"></a>Winkellogo's

Geef .png-bestanden van uw aanbieding op&#39;logo in elk van de volgende pixelformaten:

- **Klein (48 x 48)**
- **Groot (216 x 216)**

Alle logo's zijn vereist en worden gebruikt op verschillende plaatsen in de marktplaatsvermelding.

#### <a name="screenshots-optional"></a>Schermafbeeldingen (optioneel)

Voeg maximaal vijf screenshots toe die laten zien hoe uw aanbieding werkt. Elk moet 1280 x 720 pixels groot en in .png-formaat hebben.

#### <a name="videos-optional"></a>Video's (optioneel)

Voeg maximaal vier video's toe die uw aanbod demonstreren. Voer de video&#39;de naam, het webadres (URL) en een miniatuur .png-afbeelding van de video met een grootte van 1280 x 720 pixels.

Selecteer **Concept opslaan** voordat u doorgaat naar de volgende sectie, Prijzen en beschikbaarheid.

## <a name="pricing-and-availability"></a>Prijzen en beschikbaarheid

Hier definieert u elementen zoals prijzen, markt en een privésleutel.

1. **Markt**: Stel de markt in waarin uw aanbieding beschikbaar zal zijn. U mag slechts één markt per aanbieding selecteren.
    1. Selecteer voor de markten van de Verenigde Staten of Canada **de optie Statussen** (of **provincies)** bewerken om aan te geven waar uw aanbieding beschikbaar zal zijn.
2. **Preview doelgroep:** configureer de **verborgen sleutel** die wordt gebruikt om het privépubliek voor uw aanbieding in te stellen.
3. **Prijzen:** Geef op of uw aanbieding een **gratis** of **betaalde** aanbieding is.

    > [!NOTE]
    > Consulting service aanbiedingen zijn alleen voor de aanbieding. Alle transacties zullen direct plaatsvinden, buiten de commerciële markt.

4. Geef voor een betaalde aanbieding de **prijs en valuta** op en of de prijs **vast** of **geschat**is. Als u schat, moet u in de beschrijving opgeven welke factoren van invloed zijn op de prijs.
5. Selecteer **Concept opslaan**.

## <a name="review-and-publish"></a>Bekijken en publiceren

Nadat u alle vereiste onderdelen van de aanbieding hebt voltooid, u uw voorstel indienen om te bekijken en te publiceren.

1. Wanneer u klaar bent om uw consultingserviceaanbieding te publiceren, klikt u op **Controleren en publiceren.**
2. Bekijk de details op de laatste inzendingspagina.
3. Schrijf indien nodig een notitie naar het certificeringsteam als u van mening bent dat een van de details van uw aanbieding uitleg vereist.
4. Wanneer u klaar bent, selecteert u **Verzenden**.
5. Op **de overzichtspagina Aanbieding** ziet u in welke publicatiefase uw aanbieding zich bevindt.

Zie [De publicatiestatus van uw commerciële marktplaatsaanbieding controleren voor](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status)meer informatie over hoe lang u verwachten dat uw aanbieding zich in elke publicatiefase bevindt.

## <a name="update-your-existing-consulting-service-offers"></a>Uw bestaande Consulting-serviceaanbiedingen bijwerken

- [Een bestaand aanbod bijwerken in de commerciële markt](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
