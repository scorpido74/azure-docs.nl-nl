---
title: Een Aanbieding voor de Power BI-app maken - Azure Marketplace
description: Meer informatie over het maken en publiceren van een Power BI-appaanbieding naar Microsoft AppSource.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/02/2020
ms.openlocfilehash: dc036ae3cba6aa4d3a689562afffb991fadc8c0a
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81867603"
---
# <a name="create-a-power-bi-app"></a>Een Power BI-app maken

> [!IMPORTANT]
> We verplaatsen het beheer van uw Power BI-appaanbiedingen van Cloud Partner Portal naar Partner Center. Volg de instructies in Een Power [BI-app-aanbieding](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/power-bi/cpp-create-offer) voor Cloud Partner Portal maken om uw aanbiedingen te beheren totdat uw aanbiedingen zijn gemigreerd totdat uw aanbiedingen zijn gemigreerd.

In dit artikel wordt beschreven hoe u een Power BI-appaanbieding maakt en publiceert naar de Microsoft [AppSource.](https://appsource.microsoft.com/)

Voordat u een Aanbieding voor een Power BI-app maken, moet u een account over een commerciële marktplaats hebben in het Partnercentrum. Zie [Een Commercieel Marketplace-account maken in het Partnercentrum-account](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) als je er nog geen hebt gemaakt.

## <a name="create-a-new-offer"></a>Een nieuwe aanbieding maken

1. Meld u aan bij [Partner Center](https://partner.microsoft.com/dashboard/home).
2. Selecteer in het menu links navigatie de optie **Overzicht van commerciële marktplaatsen** > **.**

    :::image type="content" source="media/power-bi-menu-overview.png" alt-text="overzicht van het commerciële marktplaatsmenu" border="false":::

3. Selecteer de**Power BI-app** **voor nieuw aanbod** > . Het dialoogvenster **Nieuwe aanbieding** wordt weergegeven.

> [!IMPORTANT]
> Als de optie **Power BI-app**&#39;niet wordt weergegeven of niet is ingeschakeld&#39;, heeft uw account&#39;geen toestemming om dit aanbiedingstype te maken. Controleer of je&#39;hebt voldaan aan alle [vereisten](create-power-bi-app-overview.md) voor dit aanbiedingstype, inclusief het registreren voor een ontwikkelaarsaccount

### <a name="offer-id-and-alias"></a>Aanbiedings-ID en alias

Voer een **aanbiedings-id in**. Dit is een unieke id voor elke aanbieding in uw account.

- Deze id is zichtbaar voor klanten in het webadres voor de marketplace-aanbieding en Azure Resource Manager-sjablonen, indien van toepassing.
- Gebruik alleen kleine letters en cijfers. Het kan koppeltekens en underscores bevatten, maar geen spaties, en is beperkt tot 50 tekens. Als u bijvoorbeeld **test-aanbieding-1** invoert, wordt `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`het webadres van de aanbieding .

- De aanbiedings-id kan niet worden gewijzigd nadat u **Maken**hebt geselecteerd.

Voer een **aanbiedingsalias**in . Dit is de naam die wordt gebruikt om te verwijzen naar de aanbieding in Partner Center.

- Deze naam wordt niet gebruikt in de markt en verschilt van de aanbiedingsnaam en andere waarden die aan klanten worden weergegeven.
- Dit kan niet worden gewijzigd nadat u Maken hebt **geselecteerd.**

Nadat u deze twee waarden hebt ingevoerd, selecteert u **Maken** om door te gaan naar de pagina Weergaveoverzicht.

## <a name="offer-overview"></a>Aanbiedingsoverzicht

De **overzichtspagina Aanbieding** toont een visuele weergave van de stappen die nodig zijn om deze aanbieding te publiceren (zowel voltooid als aanstaande) en hoe lang elke stap moet duren om te voltooien.

Het bevat koppelingen om bewerkingen uit te voeren op basis van de selectie die u maakt. Bijvoorbeeld:

- Als de aanbieding een concept is - [Conceptvoorstel verwijderen](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- Als het aanbod live is - [Stop met de verkoop van het aanbod](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- Als de aanbieding in preview is - [Go-live](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- Als u de afmelding van uitgevers nog niet hebt voltooid, [kunt u publiceren annuleren](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="offer-setup"></a>Instelling voor aanbieding

Volg deze stappen om uw aanbieding in te stellen.

### <a name="connect-lead-management"></a>Leadbeheer verbinden

Wanneer u uw aanbieding op de marktplaats publiceert met Partner Center, moet u deze koppelen aan uw CRM-systeem (Customer Relationship Management). Hiermee u klantcontactgegevens ontvangen zodra iemand interesse toont in of uw product gebruikt.

1. Selecteer een leadbestemming waarnaar wij de klantenleads moeten sturen. Partnercenter ondersteunt de volgende CRM-systemen:

    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) voor customer engagement
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [SalesForce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Als uw CRM-systeem hierboven niet wordt vermeld, gebruikt u [Azure Table](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) of [Https Endpoint](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) om klantgegevens op te slaan. Exporteer de gegevens vervolgens naar uw CRM-systeem.

2. Verbind uw aanbieding met de hoofdbestemming wanneer u publiceert in partnercentrum.
3. Controleer of de verbinding met de hoofdbestemming correct is geconfigureerd. Nadat u deze hebt gepubliceerd in het Partnercenter, valideren we de verbinding en sturen we u een testlead. Terwijl u een voorbeeld van de aanbieding bekijkt voordat deze live gaat, u ook uw leadverbinding testen door te proberen het aanbod zelf te kopen in de preview-omgeving.
4. Zorg ervoor dat de verbinding met de hoofdbestemming up-to-date blijft, zodat u geen leads verliest.

Hier volgen enkele aanvullende bronnen voor leadbeheer:

- [Overzicht van leadbeheer](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [Veelgestelde vragen over leadmanagement](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Algemene leadconfiguratiefouten](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Overzicht van leadbeheer](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (zorg ervoor dat uw pop-upblokkering is uitgeschakeld)

Selecteer **Concept opslaan** voordat u doorgaat naar de volgende sectie, Eigenschappen.

## <a name="properties"></a>Eigenschappen

Op deze pagina u de categorieën en bedrijfstakken definiëren die worden gebruikt om uw aanbieding op de marktplaats, uw app-versie en de juridische contracten die uw aanbieding ondersteunen, te groeperen.

### <a name="category"></a>Categorie

Selecteer minimaal één en maximaal drie categorieën. Deze categorieën worden gebruikt om uw aanbieding in de juiste zoekgebieden op marktplaats te plaatsen en worden weergegeven op uw pagina met aanbiedingsgegevens. Leg in de aanbiedingsbeschrijving uit hoe uw aanbieding deze categorieën ondersteunt.

### <a name="industry"></a>Branche

Selecteer optioneel maximaal twee bedrijfstakken en twee verticalen onder elke bedrijfstak. Terwijl categorieën worden gebruikt voor het weergeven van uw aanbieding, worden branche en verticalen gebruikt in zoekfilters en worden ze toegepast in de Storefront. Als uw aanbod zich richt op een specifieke branche en/of verticaal, gebruikt u de aanbiedingsbeschrijving om uit te leggen hoe uw aanbod de geselecteerde industrieën of verticalen ondersteunt. Als uw aanbieding&#39;niet branchespecifiek is, laat u deze sectie leeg.

> [!NOTE]
> Terwijl we werken aan het introduceren van nieuwe industrieën en verticalen om de detectie-ervaring van het aanbod te verbeteren, zijn sommige industrieën of verticalen mogelijk nog niet zichtbaar op de Storefront. Industrieën en verticalen die zijn gemarkeerd met een (*) zullen beschikbaar zijn op een toekomstige datum. Alle gepubliceerde aanbiedingen zijn vindbaar via trefwoord zoeken.
<p>&nbsp;

| **Branche** | **Subindustrie** |
| --- | --- |
| *Automotive | *Automotive |
| Landbouw | *Overig - Niet gesegmenteerd |
| Distributie | *Groothandel<br>Verzending van pakketten en pakketten |
| Education | *Hoger<br> onderwijs* Basis- en Voortgezet Onderwijs / K-12<br>*Bibliotheken en musea |
| Financiële dienstverlening | *Bank- en<br> kapitaalmarkten* Verzekering |
| Overheid | *Defensie en Intelligentie (vroeger nationale en<br> openbare veiligheid genoemd)* Openbare veiligheid en rechtvaardigheid<br>*Civiele regering |
| Gezondheidszorg (vroeger gezondheid genoemd) | *Health Payor<br> * Zorgverlener<br>*Farmaceutische producten |
| Productie en resources (vroeger Manufacturing genoemd) | *Chemische en<br> Agrochemische* Discrete productie<br>*Energie |
| Retail en consumer goods (vroeger retail genoemd) | *Consumptiegoederen<br> * Detailhandelaren |
| *Media en communicatie (heette vroeger Media en Entertainment) | *Media en<br> Entertainment* Telecommunicatie |
| Professionele diensten | *Wettelijk<br> * Partner Professional Services |
| *Architectuur en bouw (heette vroeger Architectuurtechniek) | *Overig - Niet gesegmenteerd |
| *Gastvrijheid en reizen | *Hotels en<br> Vrije tijd* Reizen en vervoer<br>*Restaurants en foodservices |
| *Andere bedrijfstakken van de publieke sector | *Bosbouw en<br> visserij* Nonprofits |
| *Onroerend goed | *Overig - Niet gesegmenteerd |

### <a name="legal"></a>Juridisch

#### <a name="terms-and-conditions"></a>Voorwaarden

Als u uw eigen aangepaste algemene voorwaarden wilt opgeven, voert u maximaal 10.000 tekens in het vak **Algemene voorwaarden** in. Als uw algemene voorwaarden een langere beschrijving vereisen, voert u één weblink in naar waar ze kunnen worden gevonden. Het zal worden weergegeven aan klanten als een actieve link.

Klanten moeten deze voorwaarden accepteren voordat ze uw aanbieding kunnen proberen.

Selecteer **Concept opslaan** voordat u doorgaat naar de volgende sectie, Aanbieding smaken.

## <a name="offer-listing"></a>Aanbieding aanbieden

Hier definieert u de aanbiedingsgegevens die op de markt worden weergegeven. Dit omvat de aanbiedingnaam, beschrijving, afbeeldingen, enzovoort.

### <a name="language"></a>Taal

Selecteer de taal waarin uw aanbieding wordt weergegeven. Momenteel is **Engels (Verenigde Staten)** de enige beschikbare optie.

Definieer marktplaatsdetails (zoals aanbiedingsnaam, beschrijving en afbeeldingen) voor elke taal/markt. Selecteer de taal/marktnaam om deze informatie te verstrekken.

> [!NOTE]
> Aanbiedingdetails hoeven niet in het Engels te zijn als &quot;de aanbiedingsbeschrijving begint met de woordgroep, Deze toepassing is alleen beschikbaar in [niet-Engelse taal]. &quot; Het&#39;is ook goed om een nuttige link te bieden om inhoud aan te bieden in een taal die anders&#39;dan die in de aanbiedingsvermelding.

### <a name="name"></a>Naam

De naam die u hier invoert, wordt weergegeven als de titel van uw aanbieding. Dit veld is vooraf gevuld met de tekst die u hebt ingevoerd in het vak **Alias aanbieden** toen u de aanbieding hebt gemaakt. U kunt deze naam later wijzigen.

De naam:

- Kan een handelsmerk zijn (en u handelsmerk- of auteursrechtsymbolen opnemen).
- Kan niet langer dan 50 tekens zijn.
- Kan geen emoji's bevatten.

### <a name="search-results-summary"></a>Overzicht van zoekresultaten

Geef een korte beschrijving van uw aanbod. Dit kan maximaal 100 tekens lang zijn en wordt gebruikt in de zoekresultaten van marktplaatsen.

### <a name="description"></a>Beschrijving

Geef een langere beschrijving van uw aanbieding, tot 3.000 tekens. Dit wordt weergegeven aan klanten in het overzicht van de marktplaatsvermelding.

Neem een of meer van de volgende gegevens op in uw beschrijving:

- De waarde en de belangrijkste voordelen van uw aanbod.
- Categorie- of brancheorganisaties, of beide.
- In-app aankoopmogelijkheden.
- Alle vereiste onthullingen.

Hier zijn enkele tips voor het schrijven van uw beschrijving:

- Beschrijf duidelijk de waarde van uw aanbieding in de eerste paar zinnen van uw beschrijving. Neem de volgende items op:
  - Beschrijving van het aanbod.
  - Het type gebruiker dat profiteert van het aanbod.
  - De behoeften van de klant of geeft de aanbiedingsadressen uit.
- Vergeet niet dat de eerste paar zinnen kunnen worden weergegeven in de zoekresultaten.
- Vertrouw niet op functies en functionaliteit om uw product te verkopen. Richt u in plaats daarvan op de waarde die uw aanbieding biedt.
- Probeer branchespecifieke woordenschat of op voordelen gebaseerde formuleringen te gebruiken.

Als u de **beschrijving van** uw aanbieding aantrekkelijker wilt maken, gebruikt u de editor voor rijke tekst om uw beschrijving op te maken. Met de rich text editor u getallen, opsommingstekens, vet, cursief en inspringingen toevoegen om uw beschrijving leesbaarder te maken.

:::image type="content" source="media/power-bi-rich-text-editor.png" alt-text="illustreert de rich text editor" border="false":::

### <a name="search-keywords"></a>Zoektrefwoorden

Voer maximaal drie optionele zoekwoorden in om klanten te helpen uw aanbieding op de marktplaats te vinden. Voor de beste resultaten, ook gebruik maken van deze zoekwoorden in uw beschrijving.

### <a name="helpprivacy-web-addresses"></a>Help/Privacy-webadressen

Geef links om klanten te helpen uw aanbod beter te begrijpen.

#### <a name="help-link"></a>Help-koppeling

Voer het webadres in waar klanten meer te weten kunnen komen over uw aanbieding.

#### <a name="privacy-policy-url"></a>URL van het privacybeleid

Voer het webadres in voor het privacybeleid van uw organisatie. U bent verantwoordelijk om ervoor te zorgen dat uw aanbod voldoet aan de privacywet- en regelgeving. Je bent ook verantwoordelijk voor het plaatsen van een geldig privacybeleid op je website.

### <a name="contact-information"></a>Contactgegevens

U moet de naam, e-mail en het telefoonnummer opgeven voor een **contactpersoon voor ondersteuning** en een contactpersoon voor **engineering.** Deze informatie wordt&#39;niet aan klanten getoond. Het is beschikbaar voor Microsoft en kan worden verstrekt aan Cloud Solution Provider (CSP) partners.

- Ondersteuningscontact (vereist): voor algemene ondersteuningsvragen.
- Technische contactpersoon (vereist): Voor technische vragen en certificeringsproblemen.
- CSP-programmacontact (optioneel): voor resellervragen met betrekking tot het CSP-programma.

Geef in de sectie **Contactpersoon ondersteuning** het webadres van de **ondersteuningswebsite** op waar partners ondersteuning voor uw aanbieding kunnen vinden.

### <a name="supporting-documents"></a>Bewijsstukken

Geef ten minste één en maximaal drie gerelateerde marketingdocumenten op in PDF-formaat. Bijvoorbeeld whitepapers, brochures, checklists of presentaties.

### <a name="marketplace-images"></a>Marketplace-afbeeldingen

Geef logo's en afbeeldingen om te gebruiken met uw aanbod. Alle afbeeldingen moeten in .png-indeling zijn. Wazige afbeeldingen worden afgewezen.

>[!Note]
>Als u een probleem hebt met het uploaden van https://upload.xboxlive.com bestanden, controleert u of uw lokale netwerk de service die door het Partnercentrum wordt gebruikt, niet blokkeert.

#### <a name="store-logos"></a>Winkellogo's

Geef .png-bestanden van uw aanbieding&#39;s-logo in twee formaten: **klein** (48 x 48 pixels) en **Groot** (216 x 216 pixels).

Beide logo's zijn vereist en worden gebruikt op verschillende plaatsen in de marktplaats vermelding.

#### <a name="screenshots"></a>Schermopnamen

Voeg ten minste één en maximaal vijf screenshots toe die laten zien hoe uw aanbieding werkt. Elk moet 1280 x 720 pixels groot en in .png-formaat hebben.

#### <a name="videos-optional"></a>Video's (optioneel)

Voeg maximaal vijf video's toe die je aanbod demonstreren. Voer de video&#39;s naam, het webadres en de miniatuur .png-afbeelding van de video met een grootte van 1280 x 720 pixels in.

#### <a name="additional-marketplace-listing-resources"></a>Aanvullende bronnen voor marktplaatsvermeldingen

Zie Aanbevolen aanbiedingsaanbiedingen aanbieden voor meer informatie over het maken van [aanbiedingsaanbiedingen.](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="technical-configuration"></a>Technische configuratie

Promoot uw app in Power BI-service in productie en bied de installatiekoppeling van de Power BI-app waarmee klanten uw app kunnen installeren. Zie [Apps publiceren met dashboards en rapporten publiceren in Power BI](https://docs.microsoft.com/power-bi/service-create-distribute-apps)voor meer informatie.

## <a name="supplemental-content"></a>Aanvullende inhoud

Geef aanvullende informatie over uw aanbieding om ons te helpen deze te valideren. Deze informatie wordt&#39;niet aan klanten getoond of op de marktplaats gepubliceerd.

### <a name="validation-assets"></a>Validatie-elementen

Voeg eventueel instructies (maximaal 3000 tekens) toe om het Microsoft-validatieteam te helpen uw app te configureren, verbinding te maken en te testen. Voeg typische configuratie-instellingen, accounts, parameters of andere informatie toe die kunnen worden gebruikt om de optie Gegevens verbinden te testen. Deze informatie is alleen zichtbaar voor het validatieteam en wordt alleen gebruikt voor validatiedoeleinden.

## <a name="review-and-publish"></a>Bekijken en publiceren

Nadat u alle vereiste onderdelen van de aanbieding hebt&#39;voltooid, u uw voorstel indienen om te bekijken en te publiceren.

Selecteer in de rechterbovenhoek van de portal de optie **Controleren en publiceren**.

Op de review pagina u:

- Zie de voltooiingsstatus voor elk gedeelte van de aanbieding. U&#39;niet publiceren totdat alle gedeelten van de aanbieding als voltooid zijn gemarkeerd.
  - **Niet gestart** - De sectie is nog niet gestart en moet worden voltooid.
  - **Onvolledig** - De sectie bevat fouten die moeten worden opgelost of waarvoor u meer informatie moet verstrekken. Zie de secties eerder in dit document voor richtlijnen.
  - **Compleet** - De sectie bevat alle vereiste gegevens en er zijn geen fouten. Alle onderdelen van de aanbieding moeten volledig zijn voordat u de aanbieding indienen.
- Geef het certificeringsteam testinstructies om ervoor te zorgen dat uw app correct is getest. Geef ook aanvullende notities die nuttig zijn voor het begrijpen van uw aanbod.

Als u de publicatieaanbieding wilt indienen, selecteert u **Publiceren**.

We sturen je een e-mail om je te laten weten wanneer een preview-versie van de aanbieding beschikbaar is om te bekijken en goed te keuren. Als u uw aanbieding wilt publiceren naar het publiek (of als een privé-aanbieding, naar een privépubliek) gaat, gaat u naar partnercentrum en selecteert **u Go-live.**
