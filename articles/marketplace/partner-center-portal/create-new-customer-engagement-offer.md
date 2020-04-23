---
title: Maak een nieuwe Dynamics 365 voor customer engagement & PowerApps-aanbod in de commerciële marktplaats
description: Een nieuwe Dynamics 365 voor customer engagement-& PowerApps-aanbieding maken voor vermelding of verkoop in het Azure Marketplace-, AppSource- of via het CSP-programma (Cloud Solution Provider) via de Commercial Marketplace-portal in het Microsoft Partner Center.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 0ec62095f14b38d0c4ffebfabc14c4a0a27a70fb
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869831"
---
# <a name="create-a-new-dynamics-365-for-customer-engagement--powerapps-offer"></a>Maak een nieuwe Dynamics 365 voor customer engagement & PowerApps-aanbieding

In dit onderwerp wordt uitgelegd hoe u een nieuwe Dynamics 365 voor customer engagement & PowerApps-aanbieding maken. Alle apps voor Dynamics 365 for Customer Engagement (PowerApps, Sales, Service, Project Service en Field Service) moeten ons certificeringsproces doorlopen en een testervaring ondersteunen. Het certificeringsproces controleert uw oplossing op standaardvereisten, compatibiliteit en de juiste praktijken. Met de testervaring kunnen gebruikers uw oplossing implementeren in een live Dynamics 365-omgeving.

Als u wilt beginnen met het maken van Dynamics 365 voor klantbetrokkenheid & powerapps-aanbiedingen, moet u ervoor zorgen dat u eerst [een Partnercenter-account maakt](./create-account.md) en het [dashboard Commercial Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)opent, met het tabblad **Overzicht** geselecteerd.

![Commercieel Marketplace-dashboard in partnercentrum](./media/new-offer-overview.png)

>[!Note]
> Zodra een aanbieding is gepubliceerd, worden bewerkingen van de aanbieding in het Partnercenter alleen bijgewerkt in het systeem en worden de fronten na het opnieuw publiceren bijgewerkt. Zorg ervoor dat u de aanbieding voor publicatie indient nadat u wijzigingen hebt aangebracht.

## <a name="create-a-new-offer"></a>Een nieuwe aanbieding maken

Selecteer de knop **+ Nieuwe aanbieding** en selecteer vervolgens het **menu-item Dynamics 365 voor klantbetrokkenheid & PowerApps.** Het dialoogvenster **Nieuwe aanbieding** wordt weergegeven.

### <a name="offer-id-and-alias"></a>Aanbiedings-ID en alias

- **Aanbiedings-id:** unieke id voor elke aanbieding in uw account. Deze id is zichtbaar voor klanten in het URL-adres voor de marketplace-aanbieding en Azure Resource Manager-sjablonen (indien van toepassing). Aanbiedings-ID moet alfanumerieke tekens voor kleine letters zijn (inclusief koppeltekens en underscores, maar geen witruimte). De id is beperkt tot 50 tekens en kan niet worden gewijzigd nadat u **Maken hebt geselecteerd.** Als u hier bijvoorbeeld *test-aanbieding-1* invoert, `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`is de URL van de aanbieding .

- **Aanbiedingsalias**: De naam die wordt gebruikt om te verwijzen naar de aanbieding binnen het Partnercentrum. Deze naam wordt niet gebruikt in de marktplaats en is anders dan de aanbiedingsnaam en andere waarden die aan klanten worden getoond. Deze waarde kan niet worden gewijzigd nadat u Maken hebt **geselecteerd.**

Zodra u uw **aanbiedings-id** en **aanbiedingsalias**invoert, selecteert u **Maken**. U dan aan alle verschillende onderdelen van uw aanbod werken.

## <a name="offer-setup"></a>Instelling voor aanbieding

Op de pagina **Installatie aanbieding** wordt om de volgende informatie gevraagd. Zorg ervoor dat u **Opslaan** selecteert nadat u deze velden hebt voltooid.

### <a name="how-do-you-want-potential-customers-to-interact-with-this-listing-offer"></a>Hoe wilt u dat potentiële klanten interactie hebben met deze aanbiedingsaanbieding?

Selecteer de optie die u voor deze aanbieding wilt gebruiken.

#### <a name="get-it-now-free"></a>Krijg het nu (gratis)

Bied uw aanbieding gratis aan klanten aan door een geldige URL (te beginnen met *http* of *https)* op te geven waar ze toegang hebben tot uw app.  Bijvoorbeeld: `https://contoso.com/my-app`

#### <a name="free-trial-listing"></a>Gratis proefperiode (aanbieding)

Bied uw aanbieding aan klanten met een link naar een gratis proefperiode door een geldige URL (te beginnen met *http* of *https)* op te geven waar ze een proefversie kunnen krijgen.  Bijvoorbeeld: `https://contoso.com/trial/my-app`. Aanbiedingsvrije proefversies worden gemaakt, beheerd en geconfigureerd door uw service en hebben geen abonnementen die door Microsoft worden beheerd.

> [!NOTE]
> De tokens die uw toepassing ontvangt via uw testkoppeling, kunnen alleen worden gebruikt om gebruikersgegevens te verkrijgen via Azure Active Directory (Azure AD) om het maken van accounts in uw app te automatiseren. Microsoft-accounts worden niet ondersteund voor verificatie met behulp van dit token.

#### <a name="contact-me"></a>Neem contact met mij op

Verzamel klantcontactgegevens door uw CRM-systeem (Customer Relationship Management) met elkaar te verbinden. De klant wordt om toestemming gevraagd om zijn informatie te delen. Deze klantgegevens, samen met de naam van de aanbieding, id en marktplaatsbron waar ze uw aanbieding hebben gevonden, worden verzonden naar het CRM-systeem dat u hebt geconfigureerd. Zie [Leadmanagement](#connect-lead-management)verbinden voor meer informatie over het configureren van uw CRM. 

### <a name="test-drive"></a>Proefrit

Een testrit is een geweldige manier om uw aanbod aan potentiële klanten te presenteren door hen de mogelijkheid te geven om 'proberen voordat u koopt', wat resulteert in een verhoogde conversie en het genereren van hooggekwalificeerde leads. [Meer informatie over testritten.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

Als u een testrit wilt inschakelen, schakelt u het selectievakje **Een teststation inschakelen in.** U moet dan een demonstratieomgeving configureren in de technische configuratie van de [testdrive,](#test-drive-technical-configuration) om klanten uw aanbieding gedurende een bepaalde periode te laten proberen. 

#### <a name="type-of-test-drive"></a>Type proefrit

Maak een keuze uit de volgende opties:

- **[Azure Resource Manager:](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)** een implementatiesjabloon die alle Azure-resources bevat die uw oplossing bevatten. Producten die in dit scenario passen, gebruiken alleen Azure-bronnen.
- **[Dynamics 365 voor Business Central](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)**: Microsoft host en onderhoudt de testdrive-service (inclusief inrichting en implementatie) voor een Business Central enterprise resource planning systeem (financiën, operations, supply chain, CRM, etc.).  
- **[Dynamics 365 voor customer engagement](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)**: Microsoft host en onderhoudt de testdrive-service (inclusief inrichting en implementatie) voor een Customer Engagement-systeem (verkoop, service, projectservice, buitendienst, enz.).  
- **[Dynamics 365 for Operations](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)**: Microsoft host en onderhoudt de testdrive-service (inclusief inrichting en implementatie) voor een enterprise resource planning-systeem voor financiën en operaties (financiën, bewerkingen, productie, supply chain, enz.). 
- **[Logische app:](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)** een implementatiesjabloon die alle complexe oplossingsarchitecturen omvat. Alle aangepaste producten moeten dit type teststation gebruiken.
- **[Power BI:](https://docs.microsoft.com/power-bi/service-template-apps-overview)** een ingebouwde koppeling naar een op maat gemaakt dashboard. Producten die een interactieve Power BI-visual willen demonstreren, moeten dit type teststation gebruiken. Alles wat je nodig hebt om hier te uploaden is je ingesloten Power BI URL.

#### <a name="additional-test-drive-resources"></a>Aanvullende teststationbronnen

- [Technische best practices voor testdrive](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Best practices voor testdrive marketing](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Overzicht van teststation Één pager](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>Leadbeheer verbinden

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Zie [Overzicht van leadbeheer](./commercial-marketplace-get-customer-leads.md)voor meer informatie.

Vergeet niet op te **slaan** voordat u naar de volgende sectie gaat.

## <a name="properties"></a>Eigenschappen

Op de pagina **Eigenschappen** u de categorieën en bedrijfstakken definiëren die worden gebruikt om uw aanbieding op de marktplaats, uw app-versie en de juridische contracten ter ondersteuning van uw aanbieding te groeperen. Selecteer **Opslaan** nadat u deze pagina hebt voltooid.

### <a name="category"></a>Categorie

Selecteer minimaal één en maximaal drie categorieën. De **rubriek** wordt gebruikt om uw aanbieding in de juiste zoekgebieden op marktplaats te plaatsen. Zorg ervoor dat u in de aanbiedingsbeschrijving roept hoe uw aanbieding deze categorieën ondersteunt.

### <a name="industry"></a>Branche

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="applicable-dynamics-365-products"></a>Toepasselijke Dynamics 365-producten

Selecteer alle Dynamics 365-producten waarop deze aanbieding van toepassing is.

### <a name="app-version"></a>App-versie

Voer het versienummer van uw aanbieding in. Klanten zien deze versie vermeld op de detailpagina van de aanbieding. Als u alleen het versienummer bijwerkt vanwege marketing-/beschrijvende wijzigingen, schakelt u het selectievakje **Alleen wijzigen voor marketing in.** Met deze optie kan het aanbod de certificerings- en inrichtingsfasen omzeilen.

### <a name="terms-and-conditions"></a>Voorwaarden

Geef uw eigen wettelijke voorwaarden op het gebied **van Algemene Voorwaarden.** U ook de URL opgeven waar uw algemene voorwaarden te vinden zijn. Klanten moeten deze voorwaarden accepteren voordat ze uw aanbieding kunnen proberen.

## <a name="offer-listing"></a>Aanbieding aanbieden

Op de pagina Aanbiedingsaanbieding worden de talen weergegeven waarin uw aanbieding wordt weergegeven. Momenteel is **Engels (Verenigde Staten)** de enige beschikbare optie.

U moet marktplaatsdetails (aanbiedingsnaam, beschrijving, afbeeldingen, enz.) voor elke taal/markt definiëren. Selecteer de taal/marktnaam om deze informatie te verstrekken.

> [!NOTE]
> Aanbieding sominhoud (zoals de beschrijving, documenten, screenshots, gebruiksvoorwaarden, enz.) is niet verplicht om in het Engels te zijn, zolang de aanbiedingsbeschrijving begint met de zinsnede: "Deze toepassing is alleen beschikbaar in [niet-Engelse taal]." Het is ook aanvaardbaar om een *URL voor nuttige koppelingen* op te geven om inhoud aan te bieden in een andere taal dan die welke wordt gebruikt in de inhoud van de aanbiedingsvermelding.

### <a name="name"></a>Naam

De naam die u hier invoert, wordt aan klanten weergegeven als de titel van uw aanbiedingsvermelding. Dit veld wordt vooraf ingevuld met de tekst die u hebt ingevoerd voor **Alias Aanbieding** toen u de aanbieding maakte, maar u deze waarde wijzigen. Deze naam kan een handelsmerk hebben (en u handelsmerk- of auteursrechtsymbolen bevatten). De naam mag niet meer dan 50 tekens bevatten en kan geen emoji's bevatten.

### <a name="short-description"></a>Korte beschrijving

Geef een korte beschrijving van uw aanbieding (maximaal 100 tekens). Deze beschrijving kan worden gebruikt in de zoekresultaten van marktplaatsen.











### <a name="description"></a>Beschrijving

Geef een langere beschrijving van uw aanbieding (tot 3.000 tekens). Deze beschrijving wordt weergegeven aan klanten in het overzicht van de marktplaatsvermelding. Neem de waardepropositie van uw aanbieding, belangrijke voordelen, categorie- en/of brancheorganisaties, in-app aankoopmogelijkheden en eventuele vereiste informatie op.

Enkele tips voor het schrijven van uw beschrijving:  

- Beschrijf duidelijk de waardepropositie van uw aanbieding in de eerste paar zinnen van uw beschrijving. Neem de volgende informatie op in uw waardepropositie:
  - Beschrijving van het product
  - Het type gebruiker dat profiteert van het product
  - Behoeften van de klant of pijn die het product aanpakt
- Houd er rekening mee dat de eerste paar zinnen kunnen worden weergegeven in de resultaten van zoekmachines.  
- Vertrouw niet op functies en functionaliteit om uw product te verkopen. Richt u in plaats daarvan op de waarde die u levert.  
- Gebruik zoveel mogelijk gebruik van branchespecifieke woordenschat of op voordelen gebaseerde formuleringen.
- Overweeg HTML-tags te gebruiken om uw beschrijving op te maken en deze aantrekkelijker te maken.

Als u de beschrijving van uw aanbieding aantrekkelijker wilt maken, gebruikt u de teksteditor met rijke tekst om uw beschrijving op te maken.

![De teksteditor met rijke tekst gebruiken](./media/text-editor2.png)

Gebruik de volgende instructies om de teksteditor met rijke tekst te gebruiken:

- Als u de indeling van uw inhoud wilt wijzigen, markeert u de tekst die u wilt opmaken en selecteert u een tekststijl, zoals hieronder wordt weergegeven:

     ![De teksteditor met rijke tekst gebruiken om de tekstnotatie te wijzigen](./media/text-editor3.png)

- Als u een lijst met opsommingstekens of nummers aan de tekst wilt toevoegen, gebruikt u de onderstaande opties:

     ![De teksteditor met rijke tekst gebruiken om lijsten toe te voegen](./media/text-editor4.png)

- Als u inspringing aan de tekst wilt toevoegen of verwijderen, gebruikt u de onderstaande opties:

     ![De teksteditor met rijke tekst gebruiken om in te springen](./media/text-editor5.png)







### <a name="search-keywords"></a>Zoektrefwoorden

U optioneel maximaal drie zoekzoekwoorden invoeren om klanten te helpen uw aanbieding op de marktplaats te vinden. Voor de beste resultaten, probeer deze zoekwoorden te gebruiken in uw beschrijving ook.

### <a name="products-your-app-works-with"></a>Producten waarmee uw app werkt

Als u klanten wilt laten weten dat uw app werkt met specifieke producten, voert u hier maximaal drie productnamen in.

### <a name="support-urls"></a>URL's ondersteunen

In deze sectie u koppelingen aanbieden om klanten meer inzicht te geven in uw aanbieding.

#### <a name="help-link"></a>Help-koppeling

Voer de URL in waar klanten meer te weten kunnen komen over uw aanbieding.

#### <a name="privacy-policy-url"></a>URL van het privacybeleid

Voer de URL in voor het privacybeleid van uw organisatie. U bent verantwoordelijk voor het waarborgen dat uw app voldoet aan de privacywet- en regelgeving en voor het verstrekken van een geldig privacybeleid.

### <a name="contacts"></a>Contactpersonen

In deze sectie moet u de naam, e-mail en het telefoonnummer opgeven voor een **contactpersoon voor ondersteuning** en een contactpersoon voor **engineering.** Deze informatie wordt niet aan klanten weergegeven, maar is beschikbaar voor Microsoft en kan worden verstrekt aan CSP-partners.

In de sectie **Contactpersoon voor ondersteuning** moet u ook de URL voor **ondersteuning** opgeven waar CSP-partners ondersteuning voor uw aanbieding kunnen vinden.

### <a name="supporting-documents"></a>Bewijsstukken

U moet hier ten minste één (en maximaal drie) gerelateerde marketingdocumenten overleggen, zoals whitepapers, brochures, checklists of presentaties. Deze documenten moeten in .pdf-formaat zijn.

### <a name="marketplace-images"></a>Marketplace-afbeeldingen

In deze sectie u logo's en afbeeldingen verstrekken die worden gebruikt bij het weergeven van uw aanbieding aan de klant. Alle afbeeldingen moeten in .png-indeling zijn.

>[!Note]
>Als u een probleem hebt met het uploaden van https://upload.xboxlive.com bestanden, controleert u of uw lokale netwerk de service die door het Partnercentrum wordt gebruikt, niet blokkeert.

#### <a name="store-logos"></a>Winkellogo's

U het logo van uw aanbieding in drie maten aanbieden: **klein (48 x 48),** **groot (216 x 216)** en **Wide (255 x 115).** De kleine en grote maten zijn vereist.

#### <a name="hero"></a>Held

De hero afbeelding is optioneel. Als u er een opgeeft, moet deze 815 x 290 pixels meten.

#### <a name="screenshots"></a>Schermopnamen

Voeg schermafbeeldingen toe die laten zien hoe uw aanbieding werkt. Ten minste één screenshot is vereist en u er maximaal vijf toevoegen. Alle screenshots moeten 1280 x 720 pixels zijn.

#### <a name="videos"></a>Video's

Je optioneel maximaal vier video's toevoegen die je aanbod demonstreren. Deze video's moeten worden gehost op YouTube en/of Vimeo. Voer voor elke video de naam, de URL en een miniatuurafbeelding van de video (1280 x 720 pixels) in.

#### <a name="additional-marketplace-listing-resources"></a>Aanvullende bronnen voor marktplaatsvermeldingen

- [Aanbevolen procedures voor aanbiedingen voor marktplaatsaanbiedingen](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="availability"></a>Beschikbaarheid

De **pagina Beschikbaarheid** geeft u opties over waar en hoe u uw aanbieding beschikbaar stellen.

### <a name="markets"></a>Markten

In deze sectie u opgeven in welke markten uw aanbieding beschikbaar moet zijn. Selecteer hiervoor **Markten bewerken,** waarin het pop-upvenster **Market-selectie** wordt weergegeven.

Standaard worden er geen markten geselecteerd, maar u moet ten minste één markt selecteren om uw aanbieding te publiceren. Klik **op Alles selecteren** om uw aanbieding beschikbaar te maken in elke mogelijke markt of selecteer de specifieke markten die u wilt toevoegen. Zodra u klaar bent, selecteert u **Opslaan**.

Uw selecties hier zijn alleen van toepassing op nieuwe acquisities; als iemand uw app al in een bepaalde markt heeft en u later die markt verwijdert, kunnen de mensen die het aanbod al in die markt hebben, deze blijven gebruiken, maar geen nieuwe klanten in die markt kunnen uw aanbod krijgen.

> [!IMPORTANT]
> Het is uw verantwoordelijkheid om te voldoen aan alle lokale wettelijke vereisten, zelfs als deze vereisten hier of in partnercentrum niet worden vermeld. Zelfs als u alle markten selecteert, kunnen lokale wetten en beperkingen of andere factoren voorkomen dat bepaalde aanbiedingen in sommige landen en regio's worden vermeld.

### <a name="preview-audience"></a>Een voorbeeld van doelgroep bekijken

Voordat u uw aanbieding live publiceert naar het bredere marketplace-aanbod, moet u het eerst beschikbaar maken voor een beperkt **preview-publiek.** Voer hier een **hide-toets** in (elke tekenreeks met alleen kleine letters en/of cijfers). Leden van je preview-doelgroep kunnen deze verborgen sleutel gebruiken als een token om een voorbeeld van je aanbieding in de marketplace te bekijken.

Wanneer u vervolgens klaar bent om uw aanbieding beschikbaar te stellen en de voorbeeldbeperking te verwijderen, moet u de **sleutel Verbergen** verwijderen en opnieuw publiceren.

## <a name="technical-configuration"></a>Technische configuratie

De **pagina Technische configuratie** definieert de technische details die worden gebruikt om verbinding te maken met uw aanbieding. Deze verbinding stelt ons in staat om uw aanbod voor de eindklant in te richten als ze ervoor kiezen om het te verwerven.

### <a name="base-license-model"></a>Basislicentiemodel

Het basislicentiemodel bepaalt hoe klanten uw toepassing toegewezen krijgen in het CRM-beheercentrum. Selecteer **Resource** voor op een voorbeeld gebaseerde licentie of **Gebruiker** als licenties één per tenant worden toegewezen.

### <a name="requires-s2s-outbound-and-crm-secure-store-access"></a>Vereist S2S outbound en CRM Secure Store Access

Schakel dit selectievakje in om de configuratie van crm secure store of s2s-outbound access (Server-to-Server) in te schakelen. Deze functie vereist gespecialiseerde aandacht van het Dynamics 365-team tijdens de certificeringsfase. Microsoft neemt contact met u op om aanvullende stappen uit te voeren om deze functie te ondersteunen.

### <a name="application-configuration-url"></a>URL voor toepassingsconfiguratie

Geef de URL op voor de configuratiewebpagina waarmee de klant uw app kan configureren.

### <a name="crm-package"></a>CRM-pakket

Voer in het **veld URL van uw pakketlocatie** de URL in van een Azure Storage-account dat het geüploade CRM-pakket .zip-bestand bevat. Deze URL moet een alleen-lezen SAS-sleutel bevatten waarmee Microsoft uw pakket kan ophalen voor verificatie.

Schakel het vakje met het label **Er is meer dan één CRM-pakket in mijn pakketbestand**, indien van toepassing. Als dat het zo is, moet u alle pakketten in uw .zip-bestand opnemen.

### <a name="crm-package-availability"></a>Beschikbaarheid CRM-pakket

Selecteer in deze sectie **regio + Regio toevoegen** om de geografische regio's op te geven waarin uw CRM-pakket beschikbaar is voor klanten. Implementeren naar de volgende soevereine regio's vereist speciale toestemming en validatie tijdens het certificeringsproces: [Duitsland,](https://docs.microsoft.com/azure/germany/) [Us Government Cloud](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)en TIP.

Standaard wordt de url van de **toepassingsconfiguratie** die u hierboven hebt ingevoerd, voor elke regio gebruikt. U desgevraagd een aparte URL voor toepassingsconfiguratie invoeren voor een of meer specifieke regio's. 

## <a name="test-drive-technical-configuration"></a>Technische configuratie van teststations

Als u **Een testrit inschakelen** hebt geselecteerd op de pagina Installatie van [aanbiedingen,](#offer-setup) moet u hier details opgeven om klanten een testrit van uw aanbieding te laten ervaren.

Met **de pagina Teststation** u een demonstratie (of proefrit) instellen waarmee klanten uw aanbieding kunnen proberen voordat ze zich ertoe verbinden deze te kopen. Meer informatie in het artikel [Wat is Test Drive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive). Als u geen testrit meer wilt geven voor uw aanbieding, gaat u terug naar de pagina **[Installatie aanbieden](#offer-setup)** en schakelt u **de testrit inschakelen uit.**

De volgende typen testritten zijn beschikbaar, elk met hun eigen technische configuratievereisten.

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [Logische app](#technical-configuration-for-logic-app-test-drive)
- [Power BI](#technical-configuration-not-required-for-power-bi-test-drives) (Technische configuratie niet vereist)

### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Technische configuratie voor Azure Resource Manager-teststation

Een implementatiesjabloon die alle Azure-bronnen bevat die uw oplossing bevatten. Producten die in dit scenario passen, gebruiken alleen Azure-bronnen. Meer informatie over het instellen van een [Azure Resource Manager-teststation](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

- **Regio's** (vereist): Momenteel zijn er 26 door Azure ondersteunde regio's waar uw testrit beschikbaar kan worden gesteld. Normaal gesproken wilt u uw testrit beschikbaar maken in de regio's waar u anticipeert op het grootste aantal klanten, zodat ze de dichtstbijzijnde regio kunnen selecteren voor de beste prestaties. U moet ervoor zorgen dat uw abonnement alle benodigde resources kan implementeren in elk van de regio's die u selecteert.

- **Instanties**: Selecteer het type (warm of koud) en het aantal beschikbare exemplaren, dat wordt vermenigvuldigd met het aantal regio's waar uw aanbieding beschikbaar is.

**Hot:** Dit type instantie wordt geïmplementeerd en wacht op toegang per geselecteerde regio. Klanten hebben direct toegang tot *Hot-exemplaren* van een testrit, in plaats van te hoeven wachten op een implementatie. De afweging is dat deze exemplaren altijd worden uitgevoerd op uw Azure-abonnement, zodat ze een grotere uptime kosten. Het wordt ten zeerste aanbevolen om ten minste één *Hot-exemplaar* te hebben, omdat de meeste klanten niet willen wachten op volledige implementaties, wat resulteert in een drop-off in het gebruik van de klant als er geen *Hot-exemplaar* beschikbaar is.

**Koud:** dit type instantie vertegenwoordigt het totale aantal exemplaren dat mogelijk per regio kan worden geïmplementeerd. Koude instanties vereisen dat de volledige sjabloon Test Drive Resource Manager wordt geïmplementeerd wanneer een klant de testrit aanvraagt, zodat *cold* instances veel langzamer worden geladen dan *Hot-exemplaren.* De afweging is dat u alleen hoeft te betalen voor de duur van de testrit, het wordt *niet* altijd uitgevoerd op uw Azure-abonnement zoals bij een *Hot-exemplaar.*

- **Sjabloon Azure Resource Manager voor testsstation:** upload de .zip met uw Azure Resource Manager-sjabloon.  Meer informatie over het maken van een Azure Resource Manager-sjabloon in het quickstart-artikel [Azure Resource Manager-sjablonen maken en implementeren met behulp van de Azure-portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).

- **Duur van de testrit** (vereist): Voer de tijdsduur in dat de testrit in aantal uren actief blijft. De testdrive eindigt automatisch nadat deze periode is afgelopen. Deze duur mag slechts worden ingesteld door een geheel aantal uren (bijvoorbeeld "2" uur; "1,5" is ongeldig).

### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Technische configuratie voor Dynamics 365-testrit

Microsoft kan de complexiteit van het instellen van een teststation verwijderen door de servicevoorziening en -implementatie te hosten en te onderhouden met behulp van dit type teststation. De configuratie voor dit type gehoste testrit is hetzelfde, ongeacht of de testrit is gericht op een Business Central-, Customer Engagement- of Operations-doelgroep.

- **Maximale gelijktijdige testritten** (vereist): Stel het maximum aantal klanten in dat uw testrit in één keer kan gebruiken. Elke gelijktijdige gebruiker verbruikt een Dynamics 365-licentie terwijl de testrit actief is, dus u moet ervoor zorgen dat u voldoende licenties beschikbaar hebt om de ingestelde maximumlimiet te ondersteunen. Aanbevolen waarde van 3-5.

- **Duur van de testrit** (vereist): Voer de tijdsduur in dat de testrit actief blijft door het aantal uren te definiëren. Na deze vele uren wordt de sessie beëindigd en verbruikt de sessie geen van uw licenties meer. Afhankelijk van de complexiteit van uw aanbod raden wij u een waarde van 2-24 uur aan. Deze duur mag slechts worden ingesteld door een geheel aantal uren (bijvoorbeeld "2" uur; "1,5" is ongeldig). De gebruiker kan een nieuwe sessie aanvragen als de tijd op raakt en de testrit opnieuw wil worden geopend.

- **Instantie-URL** (vereist): de URL waar de klant zijn testrit begint. Meestal de URL van uw Dynamics 365-exemplaar waarop uw `https://testdrive.crm.dynamics.com`app wordt uitgevoerd met voorbeeldgegevens die zijn geïnstalleerd (bijvoorbeeld ).

- **Instance Web API URL** (vereist): Haal de URL van de web-API voor uw Dynamics 365-exemplaar op door in te loggen op uw Microsoft 365-account en door te navigeren naar **Instellingen** \&gt; **Aanpassing** \&gt; **Ontwikkelaarsbronnen** \&gt; **Instance Web API (Url van serviceroot)**, kopieer `https://testdrive.crm.dynamics.com/api/data/v9.0`de URL hier (bijvoorbeeld ).

- **Rolnaam** (vereist): geef de beveiligingsrolnaam op die u hebt gedefinieerd in uw aangepaste Dynamics 365-testrit. Deze naam van de beveiligingsrol wordt tijdens zijn testrit (bijvoorbeeld test-drive-rol) aan de gebruiker toegewezen.

### <a name="technical-configuration-for-logic-app-test-drive"></a>Technische configuratie voor het teststation van de Logic-app

Alle aangepaste producten moeten dit type implementatiesjabloon voor teststations gebruiken, die een verscheidenheid aan complexe oplossingsarchitecturen omvat. Ga voor meer informatie over het instellen van Logic App-testritten naar [Operations](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) en [Customer Engagement](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) op GitHub.

- **Regio** (vereiste vervolgkeuzelijst met één selectie): Momenteel zijn er 26 door Azure ondersteunde regio's waar uw testrit beschikbaar kan worden gesteld. De bronnen voor uw Logic-app worden geïmplementeerd in de regio die u selecteert. Als in uw Logic App aangepaste bronnen zijn opgeslagen in een specifieke regio, controleert u of dat gebied hier is geselecteerd. De beste manier om dit te doen is door uw Logic App lokaal volledig te implementeren op uw Azure-abonnement in de portal en te controleren of het correct functioneert voordat u deze selectie maakt.

- **Maximale gelijktijdige testritten** (vereist): Stel het maximum aantal klanten in dat uw testrit in één keer kan gebruiken. Deze testritten zijn al geïmplementeerd, zodat klanten er direct toegang toe hebben zonder te wachten op een implementatie.

- **Duur van de testrit** (vereist): Voer de tijdsduur in dat de testrit in aantal uren actief blijft. De testrit eindigt automatisch nadat deze periode is afgelopen.

- **Naam van azure-brongroepgroep** (vereist): voer de naam van de [Azure-brongroep](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) in waarin uw Logische App-teststation is opgeslagen.

- **Azure logic app name** (vereist): Voer de naam in van de Logic-app die de testrit aan de gebruiker toewijst. Deze Logic-app moet worden opgeslagen in de azure-bronnengroep hierboven.

- **Naam van de logische app deprovision** (vereist): voer de naam in van de Logic-app die de testrit deprovisioneert zodra de klant is voltooid. Deze Logic-app moet worden opgeslagen in de azure-bronnengroep hierboven.

### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Technische configuratie niet vereist voor Power BI-testritten

Producten die een interactieve Power BI-visual willen demonstreren, kunnen een ingesloten koppeling gebruiken om een op maat gemaakt dashboard te delen als testrit, zonder verdere technische configuratie vereist. Meer informatie over het instellen van[Power BI-sjabloon-apps.](https://docs.microsoft.com/power-bi/service-template-apps-overview)

### <a name="deployment-subscription-details"></a>Details van het implementatieabonnement

Als u de testdrive namens u wilt implementeren, maakt en verstrekt u een afzonderlijk, uniek Azure-abonnement (dat is niet vereist voor Power BI-teststations).

- **Azure-abonnements-ID** (vereist voor Azure Resource Manager- en Logic-apps): Voer de abonnements-id in om toegang te verlenen tot uw Azure-accountservices voor rapportage en facturering van resourcegebruik. We raden u aan om [een apart Azure-abonnement](https://docs.microsoft.com/azure/billing/billing-create-subscription) te maken dat u gebruiken voor testritten als u er nog geen hebt. U uw Azure-abonnements-id vinden door u aan te melden bij de [Azure-portal](https://portal.azure.com/) en te navigeren naar het tabblad **Abonnementen** van het linkermenu. Als u het tabblad selecteert, wordt uw abonnements-ID weergegeven (bijvoorbeeld "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **Azure AD-tenant-id** (vereist): voer uw Azure Active Directory (AD) [tenant ID in](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in). Als u deze id wilt vinden, meldt u zich aan bij de [Azure-portal,](https://portal.azure.com/)selecteert u het tabblad Active Directory in het linkermenu, selecteert u **Eigenschappen**en zoekt u vervolgens naar het vermelde **Directory-ID-nummer** (bijvoorbeeld 50c464d3-4930-494c-963c-1e951d15360e). U ook de tenant-id van uw organisatie [https://www.whatismytenantid.com](https://www.whatismytenantid.com)opzoeken met uw URL van uw domeinnaam op:.

- **Azure AD-tenantnaam** (vereist voor Dynamic 365): voer de naam van uw Azure Active Directory (AD) in. Als u deze naam wilt vinden, meldt u zich aan bij de [Azure-portal,](https://portal.azure.com/)in de rechterbovenhoek wordt uw tenantnaam vermeld onder uw accountnaam.

- **Azure AD-app-id** (vereist): voer uw Azure Active Directory (AD) [toepassings-id in](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in). Als u deze id wilt vinden, meldt u zich aan bij de [Azure-portal,](https://portal.azure.com/)selecteert u het tabblad Active Directory in het linkermenu, selecteert u **App-registraties**en zoekt u vervolgens naar het vermelde **toepassings-id-nummer** (bijvoorbeeld 50c464d3-4930-494c-963c-1e951d15360e).

- **Azure AD-app client geheim** (vereist): Voer het geheim van uw Azure AD-clientclient [in](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets). Als u deze waarde wilt vinden, meldt u zich aan bij de [Azure-portal.](https://portal.azure.com/) Selecteer het tabblad **Azure Active Directory** in het linkermenu, selecteer **App-registraties**en selecteer vervolgens de app voor een teststation. Selecteer vervolgens **Certificaten en geheimen,** selecteer **Nieuw clientgeheim,** voer een beschrijving in, selecteer **Nooit** onder **Verloopt**en kies **Vervolgens Toevoegen**. Zorg ervoor dat u de waarde naar beneden kopieert. Navigeer niet weg van de pagina voordat u de waarde kopieert of u hebt geen toegang tot de waarde.)

### <a name="test-drive-marketplace-listings"></a>Marktplaatsvermeldingen voor teststations

In de optie **Marketplace-vermelding** onder het tabblad **Teststation** worden de talen weergegeven waarin uw teststation beschikbaar is. Momenteel is **Engels (Verenigde Staten)** de enige beschikbare locatie. Selecteer de taalnaam die u wilt invoeren van informatie die de testritervaring beschrijft.

- **Beschrijving** (vereist): Beschrijf uw testrit, wat wordt aangetoond, doelstellingen voor de gebruiker om mee te experimenteren, functies om te verkennen en alle relevante informatie om de gebruiker te helpen bepalen of hij uw aanbieding wil verwerven. In dit veld kunnen maximaal 3.000 tekens tekst worden ingevoerd. 

- **Toegangsgegevens** (vereist voor Azure Resource Manager- en Logic-teststations): leg uit wat een klant moet weten om toegang te krijgen tot deze testrit en deze te gebruiken. Loop door een scenario voor het gebruik van uw aanbieding en precies wat de klant moet weten om toegang te krijgen tot functies tijdens de testrit. In dit veld kunnen maximaal 10.000 tekens tekst worden ingevoerd.

- **Handleiding** (vereist): een diepgaande doorloop van uw testritervaring. De gebruikershandleiding moet precies betrekking hebben op wat u wilt dat de klant te winnen bij het ervaren van de proefrit en dienen als een referentie voor eventuele vragen die ze kunnen hebben. Het bestand moet in PDF-formaat zijn en worden benoemd (maximaal 255 tekens) na het uploaden.

- **Video's: Video's toevoegen** (optioneel): video's kunnen worden geüpload naar YouTube of Vimeo en hier worden verwezen met een link en miniatuurafbeelding (533 x 324 pixels), zodat een klant een overzicht van informatie kan bekijken om de testrit beter te begrijpen, inclusief hoe ze de functies van uw aanbieding met succes kunnen gebruiken en scenario's kunnen begrijpen die hun voordelen benadrukken.
  - **Naam** (vereist)
  - **URL (alleen YouTube of Vimeo)** (vereist)
  - **Miniatuur (533 x 324px)**: Afbeeldingsbestand moet in PNG-indeling zijn.

## <a name="supplemental-content"></a>Aanvullende inhoud

Op deze pagina u aanvullende informatie geven over uw aanbieding om ons te helpen uw aanbod te valideren. Deze informatie wordt niet getoond aan klanten of gepubliceerd op de marktplaats.

### <a name="key-usage-scenario"></a>Scenario voor sleutelgebruik

Het is noodzakelijk om een .pdf-bestand te uploaden met de belangrijkste gebruiksscenario's van uw aanbieding die in een document worden vermeld (.pdf-indeling). Alle scenario's die hier worden vermeld, kunnen worden geverifieerd door ons validatieteam voordat we uw aanbieding voor de marktplaats goedkeuren.

## <a name="publish"></a>Publiceren

### <a name="submit-offer-to-preview"></a>Voorstel indienen om een voorbeeld te bekijken

Zodra u alle vereiste gedeelten van de aanbieding hebt voltooid, selecteert u **publiceren** in de rechterbovenhoek van het portaal. U wordt doorgestuurd naar de **pagina Beoordeling en publicatie.**

Als het de eerste keer is dat u deze aanbieding publiceert, u het als volgt doen:

- Zie de voltooiingsstatus voor elk gedeelte van de aanbieding.
    - *Niet gestart* - betekent dat de sectie niet is aangeraakt en moet worden voltooid.
    - *Onvolledig* - betekent dat de sectie fouten bevat die moeten worden opgelost of dat er meer informatie moet worden verstrekt. Ga terug naar de sectie(s) en werk deze bij.
    - *Voltooid* - betekent dat de sectie volledig is, alle vereiste gegevens zijn verstrekt en er geen fouten zijn. Alle onderdelen van de aanbieding moeten volledig zijn voordat u de aanbieding indienen.
- Geef in de sectie **Notities voor certificering** testinstructies aan het certificeringsteam om ervoor te zorgen dat uw app correct is getest, naast eventuele aanvullende notities die nuttig zijn om uw app te begrijpen.
- Dien de publicatieaanbieding in door **Verzenden te selecteren.** We sturen je een e-mail om je te laten weten wanneer een preview-versie van de aanbieding beschikbaar is om te bekijken en goed te keuren. Keer terug naar partnercentrum en selecteer **Go-live** voor de aanbieding die moet worden gepubliceerd voor het publiek (of als een privé-aanbieding, aan het privépubliek).

## <a name="next-steps"></a>Volgende stappen

- [Een bestaande aanbieding bijwerken in Commerciële Marketplace](./update-existing-offer.md)
