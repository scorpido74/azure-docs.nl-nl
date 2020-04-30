---
title: Een advies service-aanbieding maken in het Partner Center-Azure Marketplace
description: Meer informatie over het publiceren van een advies service-aanbieding naar Azure Marketplace of AppSource met behulp van partner centrum.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: eff37750f0580a28c9644ee1ffb7fe4e95038709
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81869785"
---
# <a name="create-a-consulting-service-offer"></a>Een advies service-aanbieding maken

> [!IMPORTANT]
> We verhuizen het beheer van uw aanbiedingen voor advies services van Cloud Partner-portal naar het partner centrum. Totdat uw aanbiedingen zijn gemigreerd, volgt u de instructies in [Azure en de Dynamics 365 consulting service-aanbieding](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/consulting-services/cloud-partner-portal-consulting-services-publishing-offer) voor Cloud Partner-Portal voor het beheren van uw aanbiedingen.

In dit artikel wordt beschreven hoe u een advies service publiceert naar [Azure Marketplace](https://azuremarketplace.microsoft.com/) of [AppSource](https://appsource.microsoft.com/). Lijst met advies services die zijn gebaseerd op micro soft [Dynamics 365](https://dynamics.microsoft.com/) en het Power platform op AppSource. Vermeld de aanbiedingen voor advies Services op basis van Microsoft Azure op Azure Marketplace.

Als u een advies service-aanbod wilt maken in azure Marketplace of AppSource Consulting Services, moet u eerst [een Publisher-account hebben in Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)en moet uw account zijn Inge schreven in het programma voor commerciële Marketplace. Voordat u uw aanbieding maakt, controleert u de vereisten in de [consulting service-vereisten](https://docs.microsoft.com/azure/marketplace/partner-center-portal/consulting-service-prerequisites).

## <a name="publishing-benefits"></a>Publicatie voordelen

Voor delen van het publiceren naar de commerciële Marketplace:

- Promoot uw bedrijf met behulp van het merk micro soft.
- Mogelijk bereikt u meer dan 100.000.000 Office 365-en Dynamics 365-gebruikers op AppSource en meer dan 200.000 organisaties via Azure Marketplace.
- Ontvang leads van hoge kwaliteit van deze markt plaatsen.
- Laat uw services door het veld en de Televerkoop teams van micro soft worden gepromoveerd

## <a name="create-a-new-offer"></a>Een nieuwe aanbieding maken

Na het voldoen aan de bovenstaande vereisten, volgt u deze stappen voor het maken van een advies service-aanbieding.

1. Meld u aan bij [Partner Center](https://partner.microsoft.com)en selecteer **dash board** in het bovenste menu.
2. Selecteer in de linkernavigatiebalk-balk de optie **Commercial Marketplace**en selecteer vervolgens **overzicht**.

    :::image type="content" source="media/cs-menu-overview.png" alt-text="Illustreert het menu voor commerciële Marketplace":::

3. Selecteer **+ nieuwe aanbieding**en selecteer vervolgens **Consulting Service**.

    :::image type="content" source="media/cs-menu-newoffer.png" alt-text="Illustreert de knop voor het maken van een nieuwe aanbieding.":::

4. Voer een **aanbiedings-id**in. Dit is een unieke id voor elke aanbieding in uw account.

    - Deze ID is zichtbaar voor klanten in het webadres voor de Marketplace-aanbieding.
    - Gebruik alleen kleine letters, cijfers, streepjes en onderstrepings tekens, maar geen spaties. De lengte is beperkt tot 50 tekens. Als u bijvoorbeeld **test-aanbieding-1**invoert, is de URL van de aanbieding `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
    - De aanbiedings-ID kan&#39;t worden gewijzigd nadat u **maken**hebt geselecteerd.

5. Voer een **alias**voor de aanbieding in. Dit is de naam die wordt gebruikt om te verwijzen naar de aanbieding in Partner Center.

    - Deze naam hebben&#39;t gebruikt in Marketplace. Het&#39;s verschilt van de naam van de aanbieding en andere waarden die aan klanten worden weer gegeven. U kunt dit veld gebruiken om een naam toe te wijzen aan de aanbieding die nuttiger is voor u om de aanbieding intern te identificeren. het wordt niet weer gegeven voor klanten.
    - De aanbiedings alias kan&#39;t worden gewijzigd nadat u **maken**hebt geselecteerd.

Nadat u deze twee waarden hebt ingevoerd, selecteert u **maken** om door te gaan naar de pagina voor het instellen van de **aanbieding** .

## <a name="offer-setup"></a>Installatie van aanbieding

Nadat u een aanbiedings-ID en aanbiedings alias hebt ingevoerd, maakt partner centrum een concept aanbieding en wordt de pagina voor het instellen van de **aanbieding** weer gegeven. Volg deze stappen om uw aanbieding in te stellen.

### <a name="connect-lead-management"></a>Lead beheer verbinden

Wanneer u uw aanbieding naar Marketplace publiceert met het partner centrum, _moet_ u deze verbinden met een CRM-of marketing-automatiserings systeem. Zo kunt u contact gegevens van de klant ontvangen zodra iemand interesse in of gebruikt voor uw product.

1. Selecteer **verbinden** om op te geven waar u leads van klanten wilt verzenden. Het partner Centrum ondersteunt de volgende systemen:

    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) voor klant betrokkenheid
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [SalesForce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Als uw CRM-systeem hebben&#39;t hierboven vermeld, gebruikt u [Azure Table](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) of [https-eind punt](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) om klant lead gegevens op te slaan en exporteert u de gegevens vervolgens naar uw CRM-systeem.

2. Verbind uw aanbieding met de doel locatie van de lead wanneer u publiceert in het partner centrum.
3. Controleer of de verbinding met de doel locatie juist is geconfigureerd. Nadat u het hebt gepubliceerd in Partner Center,&#39;u de verbinding valideren en kunt u een test lead sturen. Wanneer u een voor beeld van de aanbieding bekijkt voordat deze live gaat, kunt u ook uw lead verbinding testen door zelf te proberen de aanbieding aan te schaffen in de preview-omgeving.
4. Zorg ervoor dat de verbinding met de doel locatie van de lead bijgewerkt blijft, zodat u geen leads meer&#39;.

Hier volgen enkele aanvullende bronnen voor het beheer van leads:

- [Overzicht Lead beheer](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [Veelgestelde vragen over leadmanagement](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Algemene leadconfiguratiefouten](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Overzicht Lead beheer](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (zorg ervoor dat de pop-upblokkering is uitgeschakeld)

Selecteer **concept opslaan** voordat u doorgaat naar de volgende sectie, eigenschappen.

### <a name="properties"></a>Eigenschappen

Op deze pagina kunt u het primaire product instellen dat door uw consulting service wordt aanbevolen, een advies service type instellen en van toepassing zijnde producten kiezen.

1. Selecteer een **primair product** in de vervolg keuzelijst.
2. Selecteer een **advies service type** in de vervolg keuzelijst:

    - **Beoordeling** : een evaluatie van een klant&#39;s-omgeving om de toepas baarheid van een oplossing te bepalen en een schatting te krijgen van de kosten en timing.
    - Meer **informatie** : een inleiding tot een oplossing of een advies service om de interesse van klanten te tekenen met behulp van frameworks, demo's en klant voorbeelden.
    - **Implementatie** : een volledige installatie die resulteert in een volledig werkende oplossing. Beperken tot oplossingen die in twee weken of minder kunnen worden geïmplementeerd.
    - **Testen van concept** : een implementatie met een beperkt bereik om te bepalen of een oplossing voldoet aan de vereisten van de klant.
    - **Workshop** : een interactieve betrokkenheid die wordt uitgevoerd op een klant&#39;s. Dit kan betrekking hebben op trainingen, uitspraken, beoordelingen of demo's die zijn gebouwd op basis van de klant&#39;s-gegevens of-omgeving.

1. Als u een primair product van **Azure**hebt geselecteerd, selecteert u Maxi maal drie **oplossings gebieden**. Dit maakt het gemakkelijker voor klanten in azure Marketplace om uw aanbieding te vinden. Als u&#39;t werkt, kunt u deze stap overs Laan.
2. Als u een _ander_ primair product dan Azure hebt geselecteerd, selecteert u Maxi maal drie **toepasselijke producten**. Zo kunnen klanten in AppSource gemakkelijker uw aanbieding vinden. Zie [Microsoft AppSourceing Consulting Service List guideline](https://go.microsoft.com/fwlink/?LinkId=828734&amp;clcid=0x409) (PDF) (Engelstalig) voor meer informatie.
3. Selecteer Maxi maal zes **branches** waarop uw aanbieding van toepassing is. Dit maakt het voor klanten gemakkelijker om uw aanbieding te vinden.
4. Voeg Maxi maal drie **competenties** toe die uw bedrijf heeft behaald om weer te geven in uw aanbieding voor advies service. Er is ten minste één competentie vereist, met uitzonde ring van Azure expert MSP&#39;s en Azure Networking MSP&#39;s.

Selecteer **concept opslaan** voordat u doorgaat naar de volgende sectie, aanbieding aanbieden.

## <a name="offer-listing"></a>Aanbieding weer geven

Hier&#39;u de details van de aanbieding definiëren die worden weer gegeven in de Marketplace. Dit omvat de naam van de aanbieding, beschrijving, installatie kopieën, enzovoort. Zorg ervoor dat u op de [pagina beleid van micro soft&#39;s](https://docs.microsoft.com/legal/marketplace/certification-policies#800-consulting-services) de beleids regels volgt die worden beschreven tijdens het configureren van deze aanbieding.

> [!NOTE]
> Details van aanbieding zijn&#39;t moet in het Engels zijn als de beschrijving van het aanbod begint met &quot;de woord groep, is deze toepassing alleen beschikbaar in [niet-Engelse taal]. &quot; Het&#39;s biedt ook een handige koppeling voor het aanbieden van inhoud in een taal die&#39;s afwijkt van de versie die wordt gebruikt in de details van de aanbieding.

### <a name="name"></a>Naam

De naam die u hier opgeeft, wordt weer gegeven als de titel van uw aanbieding. Dit veld is vooraf ingevuld met de tekst die u hebt ingevoerd in het vak **aanbiedings alias** wanneer u de aanbieding hebt gemaakt. U kunt deze naam later wijzigen.

De naam:

- Kan worden gemerkt (en u kunt symbolen van het handels merk of copyright bevatten).
- Mag&#39;t meer dan 50 tekens lang zijn.
- Kan&#39;e emojis bevatten.

### <a name="search-results-summary"></a>Samen vatting van zoek resultaten

Geef een korte beschrijving van uw aanbieding. Dit kan Maxi maal 100 tekens lang zijn en wordt gebruikt in Zoek resultaten voor Marketplace.

### <a name="description"></a>Beschrijving

Geef een langere beschrijving van uw aanbieding, Maxi maal 3.000 tekens. Dit wordt weer gegeven aan klanten in het overzicht van Marketplace-aanbiedingen.

Neem een of meer van de volgende opties op in uw beschrijving:

- De waarde en belangrijkste voor delen van uw aanbieding bieden
- Categorie-of branche koppelingen, of beide
- Aankoop kansen in de app
- Vereiste informatie

Hier volgen enkele tips voor het schrijven van uw beschrijving:

- Geef in de eerste paar zinnen van uw beschrijving een duidelijke beschrijving van de waarde van uw aanbieding. De volgende items bevatten:
  - De beschrijving van de aanbieding.
  - Het type gebruiker dat voor deel is van de aanbieding.
  - De klant heeft of de adressen van de aanbieding.
- Houd er rekening mee dat de eerste paar zinnen kunnen worden weer gegeven in Zoek resultaten.
- Don&#39;u geen gebruik hebt van functies en functionaliteit om uw product te verkopen. Richt u in plaats daarvan op de waarde die uw aanbieding biedt.
- Probeer branchespecifieke woorden lijsten of op basis van voor delen te gebruiken.

Als u uw beschrijving aantrekkelijker wilt maken, gebruikt u de RTF-editor om uw beschrijving op te maken. Met de RTF-editor kunt u nummers, opsommings tekens, vet, cursief en inspringingen toevoegen om uw beschrijving beter leesbaar te maken.

:::image type="content" source="media/cs-rich-text-editor.png" alt-text="Illustreert de RTF-editor voor het schrijven van de beschrijving van het aanbod." border="false":::

### <a name="keywords"></a>Trefwoorden

Voer Maxi maal drie Zoek trefwoorden in die relevant zijn voor uw primaire product en advies service. Zo kunt u gemakkelijker uw aanbieding vinden.

### <a name="duration"></a>Duur

Stel de verwachte duur van deze afspraak in op uw klant.

### <a name="contact-information"></a>Contactgegevens

U moet de naam, het e-mail adres en het telefoon nummer opgeven voor een **primaire** en **secundaire contact persoon**. Deze informatie hebben&#39;t weer gegeven aan klanten. Het is beschikbaar voor micro soft en kan worden meegeleverd met de Cloud Solution Provider (CSP)-partners.

### <a name="supporting-documents"></a>Ondersteunende documenten

Voeg Maxi maal drie (maar ten minste één) PDF-documenten voor uw aanbieding toe.

### <a name="marketplace-images"></a>Marketplace-installatie kopieën

Bied logo's en installatie kopieën die u kunt gebruiken met uw aanbieding. Alle installatie kopieën moeten de PNG-indeling hebben. Vage afbeeldingen worden geweigerd.

>[!Note]
>Als u een probleem hebt met het uploaden van bestanden, moet u ervoor zorgen dat uw https://upload.xboxlive.com lokale netwerk de door Partner Center gebruikte service niet blokkeert.

#### <a name="store-logos"></a>Winkel logo's

Geef. PNG-bestanden van uw aanbieding&#39;s-logo in elk van de volgende pixel grootten:

- **Klein (48 x 48)**
- **Groot (216 x 216)**

Alle logo's zijn vereist en worden op verschillende plaatsen in de Marketplace-vermelding gebruikt.

#### <a name="screenshots-optional"></a>Scherm afbeeldingen (optioneel)

Voeg Maxi maal vijf scherm opnamen toe die laten zien hoe uw aanbieding werkt. Elk moet 1280 x 720 pixels groot zijn en de indeling. png.

#### <a name="videos-optional"></a>Video's (optioneel)

Voeg Maxi maal vier Video's toe die uw aanbieding aantonen. Voer de naam van de video&#39;s, het webadres (URL) en een miniatuur. PNG-afbeelding van de video in op 1280 x 720 pixels.

Selecteer **concept opslaan** voordat u verdergaat met de volgende sectie, prijzen en beschik baarheid.

## <a name="pricing-and-availability"></a>Prijzen en beschikbaarheid

Hier definieert u elementen zoals prijzen, de markt en een persoonlijke sleutel.

1. **Market**: Stel de markt in die uw aanbieding beschikbaar is in. U mag slechts één markt per aanbieding selecteren.
    1. Voor de Verenigde Staten of Canada-markten selecteert u **statussen** (of **provincies**) bewerken om aan te geven waar uw aanbieding beschikbaar is.
2. **Preview doel groep**: Configureer de **sleutel** die wordt gebruikt om de persoonlijke doel groep voor uw aanbieding in te stellen.
3. **Prijzen**: Geef op of uw aanbieding een **gratis** of een **betaalde** aanbieding is.

    > [!NOTE]
    > Aanbiedingen voor advies Services gelden alleen voor de aanbieding. Trans acties worden direct uitgevoerd buiten de commerciële Marketplace.

4. Voor een betaalde aanbieding geeft u de **prijs en valuta** op en bepaalt u of de prijs **vast** of **geraamd**is. Als u een schatting maakt, moet u in de beschrijving opgeven welke factoren van invloed zijn op de prijs.
5. Selecteer **concept opslaan**.

## <a name="review-and-publish"></a>Controleren en publiceren

Nadat u alle vereiste gedeelten van de aanbieding hebt voltooid, kunt u uw aanbieding verzenden om deze te controleren en te publiceren.

1. Wanneer u klaar bent om uw advies service-aanbieding te publiceren, klikt u op **controleren en publiceren**.
2. Bekijk de details op de pagina definitieve verzen ding.
3. Als dat nodig is, schrijft u een opmerking naar het certificerings team als u denkt dat de details van uw aanbieding uitleg vereisen.
4. Wanneer u klaar bent, selecteert u **verzenden**.
5. Op de **overzichts** pagina van de aanbieding ziet u in welke publicatie fase uw aanbieding zich bevindt.

Zie [de publicatie status van uw aanbieding voor commerciële Marketplace controleren](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status)voor meer informatie over hoe lang uw aanbieding in elke publicatie fase kan worden verwacht.

## <a name="update-your-existing-consulting-service-offers"></a>De aanbiedingen van uw bestaande advies service bijwerken

- [Een bestaande aanbieding bijwerken in de commerciële Marketplace](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
