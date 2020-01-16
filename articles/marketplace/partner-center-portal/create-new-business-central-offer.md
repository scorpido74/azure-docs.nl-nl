---
title: Maak een nieuwe aanbieding voor Dynamics 365 Business Central in de commerciële Marketplace
description: Het maken van een nieuwe Dynamics 365 Business Central-aanbieding voor het aanbieden of verkopen in azure Marketplace, AppSource of via het programma Cloud Solution Provider (CSP) met behulp van de commerciële Marketplace-Portal in micro soft Partner Center.
author: ChJenk
manager: evansma
ms.author: v-chjen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 22a5b86c8b3df4da5fe338a8ba1230d0247a5826
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76046000"
---
# <a name="create-a-new-dynamics-365-business-central-offer"></a>Een nieuwe Dynamics 365 Business Central-aanbieding maken

In dit onderwerp wordt uitgelegd hoe u een nieuwe aanbieding voor Dynamics 365 Business Central maakt. [Micro soft Dynamics 365 Business Central](https://dynamics.microsoft.com/business-central) is een ERP-systeem (Enter prise resource planning) dat een breed scala aan bedrijfs processen verwerkt, waaronder financiën, bedrijfs activiteiten, toeleverings keten, CRM en project beheer en elektronische handel. Premium-pakketten bieden ook ondersteuning voor het klassieke implementatie model en productie. Alle aanbiedingen voor Dynamics 365 Business Central moeten het certificerings proces door lopen.

Als u Dynamics 365 Business Central-aanbiedingen wilt maken, moet u eerst [een partner centrum-account maken](./create-account.md) en het [dash board commerciële Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)openen, waarbij de pagina **overzicht** is geselecteerd.

![Dash board voor commerciële Marketplace in het partner centrum](./media/new-offer-overview.png)

>[!Note]
> Zodra een aanbieding is gepubliceerd, worden wijzigingen die zijn aangebracht in het partner centrum, alleen in het systeem bijgewerkt en opgeslagen vóór het opnieuw publiceren. Zorg ervoor dat u de aanbieding voor publicatie verzendt nadat u wijzigingen hebt aangebracht.

## <a name="create-a-new-offer"></a>Een nieuwe aanbieding maken

Selecteer de knop **+ nieuwe aanbieding** en selecteer vervolgens het menu-item **Dynamics 365 Business Central** . Het dialoog venster **nieuwe aanbieding** wordt weer gegeven.

### <a name="offer-id-and-alias"></a>Aanbiedings-ID en alias

- **Aanbiedings-id**: unieke id voor elke aanbieding in uw account. Deze ID is zichtbaar voor klanten in het URL-adres voor de Marketplace-aanbieding en Azure Resource Manager sjablonen (indien van toepassing). De aanbiedings-ID moet kleine letters en alfanumerieke tekens zijn (inclusief afbreek streepjes en onderstrepingen, maar geen witruimte), beperkt tot 50 tekens en kan niet worden gewijzigd nadat u **maken**hebt geselecteerd.  Als u hier bijvoorbeeld *test-aanbieding-1* invoert, wordt de URL van de aanbieding `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.

- **Aanbiedings alias**: de naam die wordt gebruikt om te verwijzen naar de aanbieding in het partner centrum. Deze naam wordt niet gebruikt in Marketplace en wijkt af van de naam van de aanbieding en andere waarden die aan klanten worden weer gegeven. Deze waarde kan niet worden gewijzigd nadat u **maken**hebt geselecteerd.

Wanneer u uw **aanbiedings-id** en **aanbiedings alias**hebt ingevoerd, selecteert u **maken**. U kunt nu aan de verschillende onderdelen van uw aanbieding werken.

## <a name="offer-setup"></a>Installatie van aanbieding

Op de pagina **aanbieding instellen** wordt u gevraagd om de volgende informatie op te vragen. Zorg ervoor dat u **Opslaan** selecteert na het volt ooien van deze velden.

### <a name="how-do-you-want-potential-customers-to-interact-with-this-listing-offer"></a>Hoe wilt u potentiële klanten interactie laten doen met deze aanbieding?

Selecteer de optie die u wilt gebruiken voor deze aanbieding.

#### <a name="get-it-now-free"></a>Nu downloaden (gratis)

Bied uw aanbod aan klanten gratis aan door een geldige URL op te geven (te beginnen met *http* of *https*), waar ze toegang hebben tot uw app.  Bijvoorbeeld: `https://contoso.com/my-app`

#### <a name="free-trial-listing"></a>Gratis proef versie (lijst)

Bied uw aanbieding aan klanten aan met een koppeling naar een gratis proef versie door een geldige URL op te geven (te beginnen met *http* of *https*), zodat deze een proef versie kan krijgen.  Bijvoorbeeld: `https://contoso.com/trial/my-app`. Gratis proef versies van aanbieding worden gemaakt, beheerd en geconfigureerd door uw service en er zijn geen abonnementen die door micro soft worden beheerd.

> [!NOTE]
> De tokens die uw toepassing via uw proef koppeling ontvangt, kunnen alleen worden gebruikt voor het verkrijgen van gebruikers gegevens via Azure Active Directory (Azure AD) om het maken van accounts in uw app te automatiseren. Micro soft-accounts worden niet ondersteund voor verificatie met behulp van dit token.

#### <a name="contact-me"></a>Contact opnemen

Contact gegevens van klanten verzamelen door verbinding te maken met uw CRM-systeem (Customer Relationship Management). De klant wordt gevraagd om toestemming te krijgen om hun gegevens te delen. Deze klant gegevens, samen met de naam van de aanbieding, ID en Marketplace-bron waar ze uw aanbieding vinden, worden verzonden naar het CRM-systeem dat u hebt geconfigureerd. Zie [Connect lead management](#connect-lead-management)(Engelstalig) voor meer informatie over het configureren van uw CRM. 

### <a name="test-drive"></a>Test drive

Een test drive is een fantastische manier om uw aanbieding aan potentiële klanten te laten presen teren door hen de mogelijkheid te geven voor ' voor u ' te kopen, wat resulteert in een verhoogde conversie en de generatie van uiterst gekwalificeerde leads. [Meer informatie over test stations.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

Als u een test drive wilt inschakelen, schakelt u het selectie vakje **een test drive inschakelen** in. Vervolgens moet u een demonstratie omgeving configureren in de [technische configuratie van het test station](#test-drive-technical-configuration) configureren zodat klanten uw aanbieding voor een bepaalde periode kunnen uitproberen. 

#### <a name="type-of-test-drive"></a>Type test drive

Maak een keuze uit de volgende opties:

- **[Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)** : een implementatie sjabloon die alle Azure-resources bevat waaruit uw oplossing bestaat. Voor producten die in dit scenario passen, worden alleen Azure-resources gebruikt.
- **[Dynamics 365 voor bedrijven Central](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)** : micro soft host en onderhoudt de Test Drive-service (met inbegrip van inrichting en implementatie) voor een bedrijf voor bedrijfs centrale bedrijfs resources (Financiën, bewerkingen, toeleverings keten, CRM, enzovoort).  
- **[Dynamics 365 voor klant betrokkenheid](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)** : micro soft host en onderhoudt de Test Drive service (inclusief inrichting en implementatie) voor een klant engagementsysteem (verkoop, service, project service, veld Service, enzovoort).  
- **[Dynamics 365 voor bewerkingen](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)** : micro soft host en onderhoudt de Test Drive-service (inclusief inrichting en implementatie) voor een financieel en operationeel systeem voor de planning van bedrijfs middelen (Financiën, bedrijfs activiteiten, productie, toeleverings keten, enzovoort). 
- **[Logische app](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)** : een implementatie sjabloon met alle complexe oplossings architecturen. Aangepaste producten moeten dit type test station gebruiken.
- **[Power bi](https://docs.microsoft.com/power-bi/service-template-apps-overview)** : een Inge sloten koppeling naar een aangepast dash board. Producten die een interactief Power BI visueel element willen demonstreren, moeten dit type test station gebruiken. Alles wat u moet uploaden, is uw Inge sloten Power BI URL.

#### <a name="additional-test-drive-resources"></a>Aanvullende test drive resources

- [Best practices voor test stations](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Best practices voor het testen van de marketing](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Overzicht van testen van één pager](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>Lead beheer verbinden

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Zie [Lead Management Overview](./commercial-marketplace-get-customer-leads.md)voor meer informatie.

Vergeet niet om te **slaan** voordat u verdergaat met de volgende sectie.

## <a name="properties"></a>Eigenschappen

Op de pagina **Eigenschappen** kunt u de categorieën en industrieën definiëren die worden gebruikt voor het groeperen van uw aanbieding op Marketplace, uw app-versie en de juridische contracten die uw aanbieding ondersteunen. Selecteer **Opslaan** nadat u deze pagina hebt voltooid.

### <a name="category"></a>Categorie

Selecteer mini maal één en Maxi maal drie categorieën. deze worden gebruikt om uw aanbieding in de juiste Zoek gebieden voor Marketplace te plaatsen. Neem contact op met de manier waarop uw aanbod deze categorieën ondersteunt in de beschrijving van de aanbieding. 

### <a name="industry"></a>Industrie

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="app-version"></a>App-versie

Voer het versie nummer van uw aanbieding in. Klanten zien dat deze versie wordt weer gegeven op de detail pagina van de aanbieding.

### <a name="standard-contract"></a>Standaardcontract

Om het aankoop proces voor klanten te vereenvoudigen en de juridische complexiteit voor software leveranciers te verminderen, biedt micro soft een standaard contract sjabloon om een trans actie in de Marketplace te vergemakkelijken.

In plaats van aangepaste voor waarden te gebruiken, kunt u ervoor kiezen om uw software aan te bieden onder het Standard-contract, wat klanten alleen maar één keer hoeven te accepteren.

Het standaard contract vindt u hier: https://go.microsoft.com/fwlink/?linkid=2041178

Als u het standaard contract wilt gebruiken, schakelt u het selectie vakje **standaard contract gebruiken** in.

#### <a name="terms-of-use"></a>Gebruiksvoorwaarden

Als u het selectie vakje **standaard contract gebruiken** niet inschakelt, moet u uw eigen juridische voor waarden opgeven in het veld **Gebruiksvoorwaarden** . Voer Maxi maal 10.000 tekens in of, als u de gebruiks voorwaarden een langere beschrijving nodig hebt, geeft u de URL op waar u de aanvullende licentie voorwaarden kunt vinden. Klanten moeten deze voor waarden accepteren voordat ze uw app kunnen proberen.

## <a name="offer-listing"></a>Aanbieding weer geven

De aanbiedings pagina bevat de talen waarin uw aanbieding wordt weer gegeven. **Engels (Verenigde Staten)** is momenteel de enige beschik bare optie.

U moet de details van de Marketplace (naam, beschrijving, installatie kopieën, enzovoort) voor elke taal/markt opgeven. Selecteer de naam van de taal/markt om deze gegevens op te geven.

> [!NOTE]
> Aanbiedings inhoud (zoals de beschrijving, documenten, scherm afbeeldingen, gebruiks voorwaarden, enzovoort) is niet vereist in het Engels, zolang de beschrijving van het aanbod begint met de woord groep ' deze toepassing is alleen beschikbaar in [niet-Engelse taal] '. Het is ook acceptabel om een *nuttige koppelings-URL* te bieden om inhoud te bieden in een andere taal dan de versie die wordt gebruikt in de inhoud van de aanbieding.

### <a name="name"></a>Name

De naam die u hier invoert, wordt aan klanten weer gegeven als de titel van de aanbieding. Dit veld wordt vooraf ingevuld met de tekst die u hebt ingevoerd voor de **aanbiedings alias** tijdens het maken van de aanbieding, maar u kunt deze waarde wijzigen. Deze naam kan worden aangemerkt (en u kunt symbolen van het handels merk of copyright bevatten). De naam mag niet langer zijn dan 50 tekens en mag geen emojis bevatten.

### <a name="short-description"></a>Korte beschrijving

Geef een korte beschrijving van uw aanbieding (Maxi maal 100 tekens), die kan worden gebruikt in Zoek resultaten voor Marketplace.

### <a name="description"></a>Beschrijving

Geef een langere beschrijving van uw aanbieding (Maxi maal 3.000 tekens). Deze beschrijving wordt weer gegeven aan klanten in het overzicht van Marketplace-vermeldingen. Neem de toegevoegde waarde van de prijs van uw aanbod, belang rijke voor delen, categorie-en/of branche koppelingen, in-app aankoop kansen en eventuele vereiste informatie op. 

Tips voor het schrijven van uw beschrijving:  

- Beschrijf duidelijk de toegevoegde waarde van uw aanbieding in de eerste paar zinnen van uw beschrijving. Neem de volgende items op in de toegevoegde waarde:
  - Beschrijving van het product
  - Het type gebruiker dat voor deel is van het product
  - Klant behoeften of pijn dat de product adressen
- Houd er rekening mee dat de eerste paar zinnen kunnen worden weer gegeven in de resultaten van de zoek machine.  
- Vertrouw niet op functies en functionaliteit om uw product te verkopen. Richt u in plaats daarvan op de waarde die u levert.  
- Gebruik zo veel mogelijk branchespecifieke woorden lijst of op basis van voor delen.
- U kunt HTML-tags gebruiken om uw beschrijving te Format teren en het aantrekkelijker te maken.

Als u de beschrijving van uw aanbieding aantrekkelijker wilt maken, gebruikt u de RTF-editor om uw beschrijving op te maken.

![De RTF-editor gebruiken](./media/text-editor2.png)

Gebruik de volgende instructies voor het gebruik van de RTF-editor:

- Als u de indeling van de inhoud wilt wijzigen, markeert u de tekst die u wilt opmaken en selecteert u een tekst stijl, zoals hieronder wordt weer gegeven:

     ![De RTF-editor gebruiken om de tekst indeling te wijzigen](./media/text-editor3.png)

- Gebruik de onderstaande opties om een lijst met opsommings tekens of genummerde lijsten aan de tekst toe te voegen:

     ![De RTF-editor gebruiken om lijsten toe te voegen](./media/text-editor4.png)

- Gebruik de onderstaande opties om inspringing toe te voegen aan of te verwijderen uit de tekst:

     ![De Rich Text Editor gebruiken om in te springen](./media/text-editor5.png)

### <a name="search-keywords"></a>Sleutelwoorden zoeken

U kunt eventueel Maxi maal drie Zoek trefwoorden invoeren om klanten te helpen uw aanbieding op Marketplace te vinden. Probeer deze tref woorden ook in uw beschrijving te gebruiken voor de beste resultaten.

### <a name="products-your-app-works-with"></a>Producten waarmee uw app werkt

Als u klanten wilt laten weten dat uw app werkt met specifieke producten, voert u hier Maxi maal drie product namen in.

### <a name="support-urls"></a>Url's voor ondersteuning

In deze sectie vindt u koppelingen waarmee klanten meer inzicht kunnen krijgen in uw aanbieding.

#### <a name="help-link"></a>Help-koppeling

Voer de URL in waar klanten meer kunnen weten over uw aanbieding.

#### <a name="privacy-policy-url"></a>URL van privacybeleid

Voer de URL in voor het privacybeleid van uw organisatie. U bent verantwoordelijk om ervoor te zorgen dat uw app voldoet aan de wetten en voor schriften van de privacy en om een geldig privacybeleid te bieden.

### <a name="contacts"></a>Contactpersonen

In deze sectie moet u de naam, het e-mail adres en het telefoon nummer opgeven voor een **ondersteunings contact** en een **technische contact persoon**. Deze informatie wordt niet weer gegeven aan klanten, maar is beschikbaar voor micro soft en kan worden verschaft aan CSP-partners.

In het gedeelte **ondersteuning voor contact opnemen** moet u ook de **ondersteunings-URL** opgeven waar CSP-partners ondersteuning voor uw aanbieding kunnen vinden.

### <a name="supporting-documents"></a>Ondersteunende documenten

Geef hier ten minste één (en Maxi maal drie) gerelateerde marketing documenten op, zoals witboeken, brochures, controle lijsten of presentaties. Deze documenten moeten de PDF-indeling hebben.

### <a name="marketplace-images"></a>Marketplace-installatie kopieën

In deze sectie kunt u logo's en installatie kopieën opgeven die worden gebruikt wanneer uw aanbieding wordt weer gegeven aan de klant. Alle installatie kopieën moeten de PNG-indeling hebben.

#### <a name="store-logos"></a>Winkel logo's

Geef het logo van uw aanbod op in twee grootten: **klein (48 x 48)** en **groot (216 x 216)** .

#### <a name="hero"></a>Hero

De afbeelding van de held is optioneel. Als u een waarde opgeeft, moet deze 815 x 290 pixels meten.

#### <a name="screenshots"></a>Schermopnamen

Scherm afbeeldingen toevoegen die laten zien hoe uw aanbieding werkt. Er zijn ten minste drie scherm afbeeldingen vereist en u kunt Maxi maal vijf toevoegen. Alle scherm afbeeldingen moeten 1280 x 720 pixels zijn.

#### <a name="videos"></a>Video’s

U kunt optioneel Maxi maal vijf Video's toevoegen die uw aanbieding aantonen. Deze Video's moeten worden gehost op YouTube en/of Vimeo. Voer voor elke video de naam, de URL en een miniatuur afbeelding van de video in (1280 x 720 pixels)

#### <a name="additional-marketplace-listing-resources"></a>Aanvullende bronnen voor Marketplace-lijst

- [Best practices voor Marketplace-aanbiedingen](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="availability"></a>Beschikbaarheid

De pagina **Beschik baarheid** bevat opties voor waar en hoe u uw aanbieding beschikbaar kunt stellen.

### <a name="markets"></a>Landen

In deze sectie kunt u de markten opgeven waarin uw aanbieding beschikbaar moet zijn. Als u dit wilt doen, selecteert u **markten bewerken**om het pop-upvenster **markt selectie** weer te geven.

Standaard zijn er geen markten geselecteerd, maar u moet ten minste één markt selecteren om uw aanbieding te publiceren. Klik op **Alles selecteren** om uw aanbieding beschikbaar te stellen op elke mogelijke markt of selecteer de specifieke markten die u wilt toevoegen. Wanneer u klaar bent, selecteert u **Opslaan**.

Uw selecties zijn hier alleen van toepassing op nieuwe verwervingen; Als iemand uw app al in een bepaalde markt heeft en u deze markt later verwijdert, kunnen de personen die de aanbieding al op die markt hebben, deze blijven gebruiken, maar kunnen er geen nieuwe klanten op deze markt worden aangeboden.

> [!IMPORTANT]
> Het is uw verantwoordelijkheid om te voldoen aan alle lokale wettelijke vereisten, zelfs als deze vereisten hier niet worden vermeld of in het partner centrum.

Houd er rekening mee dat zelfs als u alle markten, lokale wetgeving en beperkingen of andere factoren selecteert, kan worden voor komen dat bepaalde aanbiedingen in sommige landen en regio's worden weer gegeven.

### <a name="preview-audience"></a>Voor beeld van doel groep

Voordat u uw aanbieding naar de bredere Marketplace-aanbieding publiceert, moet u deze eerst beschikbaar maken voor een beperkte preview- **doel groep**. Voer hier een **Hide-toets** (wille keurige teken reeks met alleen kleine letters en/of cijfers) in. Leden van uw preview-doel groep kunnen deze verbergen sleutel als een token gebruiken om een voor beeld van uw aanbieding op Marketplace te bekijken.

Wanneer u klaar bent om uw aanbieding beschikbaar te maken en de preview-beperking te verwijderen, moet u de **sleutel verbergen** verwijderen en opnieuw publiceren.

## <a name="technical-configuration"></a>Technische configuratie

Op de pagina **technische configuratie** worden de technische details gedefinieerd die worden gebruikt om verbinding te maken met uw aanbieding. Met deze verbinding kan ons uw aanbieding voor de eind klant inrichten als deze ervoor kiezen deze te verkrijgen.

### <a name="package-type"></a>Pakkettype

Selecteer de optie die van toepassing is op uw aanbieding:

- **Toevoegen aan**: een invoeg toepassings toepassing breidt de ervaring en de bestaande functionaliteit van Dynamics 365 Business Central uit. Zie [add-on apps](https://docs.microsoft.com/dynamics365/business-central/dev-itpro/developer/readiness/readiness-add-on-apps)(Engelstalig) voor meer informatie.
- **Verbinding maken**: een Connect-app kan worden gebruikt in het scenario waarin een Point-to-point-verbinding tussen Dynamics 365 Business Central en een oplossing of service van derden is ingesteld. Zie [verbinding maken](https://docs.microsoft.com/dynamics365/business-central/dev-itpro/developer/readiness/readiness-connect-apps)voor meer informatie.

### <a name="file-upload"></a>Bestand uploaden

Als u hierboven **invoeg toepassing** hebt geselecteerd, waar u het pakket bestand van uw aanbieding uploadt, samen met de pakket bestanden voor elke uitbrei ding waarvoor afhankelijkheden gelden.

#### <a name="extensions-package-file"></a>Extensie pakket bestand

Upload het bestand met het extensie pakket (. app) voor uw aanbieding.

#### <a name="library-package-file"></a>Bibliotheek pakket bestand

Vereist als uw aanbieding moet worden geïnstalleerd samen met een andere extensie die niet op Marketplace wordt gepubliceerd. Als u het app-bestand hier uploadt.

#### <a name="dependency-package-file"></a>Afhankelijkheids pakket bestand

Vereist als uw aanbieding moet worden geïnstalleerd samen met een andere extensie die al op de Marketplace is gepubliceerd. Als dit het geval is, uploadt u het `.app` of `.zip` bestand hier.

### <a name="url-to-app-installation"></a>URL voor installatie van de app

Als u hierboven **verbinding maken** hebt geselecteerd, geeft u hier de URL voor uw app-installatie op.

## <a name="test-drive-technical-configuration"></a>Technische configuratie van test station

Als u op de pagina [aanbieding instellen](#offer-setup) de **optie Test Drive inschakelen** hebt geselecteerd, moet u hier gegevens opgeven zodat klanten een test drive van uw aanbieding kunnen ervaren.

Op de pagina **test station** kunt u een demonstratie (of "test drive") instellen waarmee klanten uw aanbieding kunnen proberen voordat deze zich aanmeldt. Meer informatie in het artikel [Wat is een test station?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive) Als u geen test drive voor uw aanbieding wilt opgeven, keert u terug naar de pagina voor het instellen van de **[aanbieding](#offer-setup)** en schakelt u **Test Drive inschakelen**in.

De volgende typen test stations zijn beschikbaar, elk met hun eigen technische configuratie vereisten.

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [Logische app](#technical-configuration-for-logic-app-test-drive)
- [Power bi](#technical-configuration-not-required-for-power-bi-test-drives) (technische configuratie niet vereist)

### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Technische configuratie voor Azure Resource Manager test drive

Een implementatie sjabloon die alle Azure-resources bevat waaruit uw oplossing bestaat. Voor producten die in dit scenario passen, worden alleen Azure-resources gebruikt. Meer informatie over het instellen van een [Azure Resource Manager test drive](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

- **Regio's** (vereist): momenteel zijn er 26 door Azure ondersteunde regio's waar uw test drive beschikbaar kan worden gemaakt. Normaal gesp roken wilt u uw test drive beschikbaar maken in de regio's waar u het grootste aantal klanten verwacht, zodat ze de dichtstbijzijnde regio voor de beste prestaties kunnen selecteren. U moet ervoor zorgen dat uw abonnement alle resources mag implementeren die nodig zijn voor elk van de regio's die u selecteert.

- **Instanties**: Selecteer het type (warme of koud) en het aantal beschik bare instanties dat wordt vermenigvuldigd met het aantal regio's waar uw aanbieding beschikbaar is.

**Dynamisch**: dit type exemplaar wordt geïmplementeerd en wacht op toegang per geselecteerde regio. Klanten hebben direct toegang tot *Hot* instances van een test drive in plaats van te wachten op een implementatie. Het verschil is dat deze instanties altijd worden uitgevoerd op uw Azure-abonnement, zodat ze een grotere uptime kosten wordt gebracht. Het wordt ten zeerste aanbevolen om ten minste één *Hot* instance te hebben, aangezien de meeste klanten niet hoeven te wachten op volledige implementaties, wat leidt tot uitval van klant gebruik als er geen *Hot* instance beschikbaar is.

**Koud**: dit type exemplaar vertegenwoordigt het totale aantal instanties dat mogelijk per regio kan worden geïmplementeerd. Voor koude instanties is het hele test station Resource Manager-sjabloon vereist om te implementeren wanneer een klant de test drive aanvraagt, zodat *koude* instanties veel langzamer worden geladen dan *Hot* instances. Het bedrag dat u alleen hoeft te betalen voor de duur van de test drive, wordt *niet* altijd uitgevoerd op uw Azure-abonnement, net als bij een *Hot* -instance.

- **Azure Resource Manager sjabloon voor het station testen**: Upload de. zip met uw Azure Resource Manager sjabloon.  Meer informatie over het maken van een Azure Resource Manager sjabloon in het Quick Start-artikel [Azure Resource Manager sjablonen maken en implementeren met behulp van de Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).

- **Duur van test drive** (vereist): Geef aan hoe lang het test station actief blijft, in aantal uren. De Test Drive wordt automatisch beëindigd na afloop van deze periode. Deze duur mag alleen worden ingesteld met een geheel aantal uren (bijvoorbeeld ' 2 ' uur, ' 1,5 ' is niet geldig).

### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Technische configuratie voor Dynamics 365 test drive

Micro soft kan de complexiteit van het instellen van een test drive verwijderen door de service-inrichting en-implementatie te hosten en te onderhouden met dit type test drive. De configuratie voor dit type gehoste test drive is hetzelfde, ongeacht of de test drive is gericht op een zakelijke centrale, klant afspraak of bewerkings publiek.

- **Maxi maal aantal gelijktijdige test schijven** (vereist): Stel het maximum in voor de klanten die uw test drive tegelijk kunnen gebruiken. Elke gelijktijdige gebruiker gebruikt een Dynamics 365-licentie terwijl de test drive actief is. u moet er dus voor zorgen dat er voldoende licenties beschikbaar zijn voor de ondersteuning van de maximum limiet. Aanbevolen waarde van 3-5.

- **Duur van test drive** (vereist): Geef aan hoe lang het test station actief blijft door het aantal uren te definiëren. Na dit aantal uur wordt de sessie beëindigd en wordt een van uw licenties niet meer verbruikt. We raden een waarde van 2-24 uur aan, afhankelijk van de complexiteit van uw aanbieding. Deze duur mag alleen worden ingesteld met een geheel aantal uren (bijvoorbeeld ' 2 ' uur, ' 1,5 ' is niet geldig).  De gebruiker kan een nieuwe sessie aanvragen als deze niet langer actief zijn en de test drive opnieuw willen gebruiken.

- **URL van exemplaar** (vereist): de URL waar de klant de test drive begint. Doorgaans de URL van uw Dynamics 365-exemplaar waarop uw app wordt uitgevoerd, waarbij voorbeeld gegevens zijn geïnstalleerd (bijvoorbeeld https://testdrive.crm.dynamics.com).

- **URL** van de Web-API voor het exemplaar (vereist): Haal de Web-API-URL voor uw Dynamics 365-exemplaar op door u aan te melden bij uw Microsoft 365-account en te navigeren naar **instellingen** \&gt; **Aanpassing** \&gt; **Resources voor ontwikkel aars** \&gt; **Web-API van het exemplaar (Service root URL)** , kopieer de URL die u hier kunt vinden (bijvoorbeeld https://testdrive.crm.dynamics.com/api/data/v9.0).

- **Rolnaam** (vereist): Geef de beveiligingsrol naam op die u hebt gedefinieerd in uw aangepaste Dynamics 365 test drive, die wordt toegewezen aan de gebruiker tijdens hun test drive (bijvoorbeeld test station-Role).

### <a name="technical-configuration-for-logic-app-test-drive"></a>Technische configuratie voor de test drive Logic-app

Aangepaste producten moeten gebruikmaken van dit type test drive-implementatie sjabloon dat een verscheidenheid aan complexe oplossings architecturen omvat. Ga voor meer informatie over het instellen van de test schijven voor logische apps naar [activiteiten](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) en [klanten betrokkenheid](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) op github.

- **Regio** (vereist, vervolg keuzelijst met één selectie): er zijn momenteel 26 door Azure ondersteunde regio's waar uw test drive beschikbaar kan worden gemaakt. De resources voor uw logische app worden geïmplementeerd in de regio die u selecteert. Als uw logische app aangepaste resources heeft die zijn opgeslagen in een bepaalde regio, moet u ervoor zorgen dat de regio hier wordt geselecteerd. De beste manier is om uw logische app lokaal te implementeren in uw Azure-abonnement in de portal en te controleren of deze correct werkt voordat u deze selectie maakt.

- **Maxi maal aantal gelijktijdige test schijven** (vereist): Stel het maximum in voor de klanten die uw test drive tegelijk kunnen gebruiken. Deze test stations zijn al geïmplementeerd, zodat klanten deze direct kunnen openen zonder te hoeven wachten op een implementatie.

- **Duur van test drive** (vereist): Geef aan hoe lang het test station actief blijft, in aantal uren. De test drive wordt automatisch beëindigd nadat deze periode is verstreken.

- **Naam van de Azure-resource groep** (vereist): Voer de naam in van de [Azure-resource groep](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) waar de logische app test drive wordt opgeslagen.

- **Naam van de Azure Logic-app** (vereist): Voer de naam in van de logische app die de test drive aan de gebruiker toewijst. Deze logische app moet worden opgeslagen in de bovenstaande Azure-resource groep.

- De naam van de **logische app** voor het ongedaan maken van de inrichting (vereist): Voer de naam in van de logische app die de test drive nadat de klant is voltooid. Deze logische app moet worden opgeslagen in de bovenstaande Azure-resource groep.

### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Technische configuratie niet vereist voor Power BI test stations

Producten die een interactief Power BI visueel element willen demonstreren, kunnen een Inge sloten koppeling gebruiken om een aangepast dash board te delen als hun test drive, maar er is geen verdere technische configuratie vereist. Meer informatie over het instellen van[Power bi](https://docs.microsoft.com/power-bi/service-template-apps-overview) sjabloon-apps.

### <a name="deployment-subscription-details"></a>Details van implementatie abonnement

Als u het test station namens u wilt implementeren, moet u een apart en uniek Azure-abonnement maken en opgeven. (Niet vereist voor Power BI test stations).

- **Azure-abonnements-id** (vereist voor Azure Resource Manager en Logic apps): Voer de abonnements-id in om toegang te verlenen tot uw Azure-account services voor rapportage en facturering van resource gebruik. Het is raadzaam om [een afzonderlijk Azure-abonnement te maken](https://docs.microsoft.com/azure/billing/billing-create-subscription) dat u voor test stations kunt gebruiken als u er nog geen hebt. U kunt uw Azure-abonnements-ID vinden door u aan te melden bij de [Azure Portal](https://portal.azure.com/) en te navigeren naar het tabblad **abonnementen** van het menu aan de linkerkant. Als u het tabblad selecteert, wordt uw abonnements-ID weer gegeven (bijvoorbeeld "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **Azure AD-Tenant-id** (vereist): voer uw Azure Active Directory (AD) [Tenant-id](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)in. Als u deze ID wilt vinden, meldt u zich aan bij de [Azure Portal](https://portal.azure.com/), selecteert u het tabblad Active Directory in het menu aan de linkerkant, selecteert u * * eigenschappen en zoekt u naar de weer gegeven **Directory-id** (bijvoorbeeld 50c464d3-4930-494c-963c-1e951d15360e). U kunt ook de Tenant-ID van uw organisatie opzoeken met uw domein naam-URL op: [https://www.whatismytenantid.com](https://www.whatismytenantid.com).

- **Naam van Azure AD-Tenant** (vereist voor dynamische 365): Voer de naam van uw Azure Active Directory (AD) in. Als u deze naam wilt vinden, meldt u zich aan bij de [Azure Portal](https://portal.azure.com/). in de rechter bovenhoek wordt de naam van de Tenant vermeld onder uw account naam.

- **Azure AD-App-ID** (vereist): voer uw Azure Active Directory (AD) [-toepassings-id](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)in. Als u deze ID wilt vinden, meldt u zich aan bij de [Azure Portal](https://portal.azure.com/), selecteert u het tabblad Active Directory in het menu links, selecteert u **app-registraties**en zoekt u naar het **toepassings-id-** nummer dat wordt weer gegeven (bijvoorbeeld 50c464d3-4930-494c-963c-1e951d15360e).

- **Azure AD-App-client geheim** (vereist): Voer uw Azure AD-toepassings [client geheim](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)in. Als u deze waarde wilt vinden, meldt u zich aan bij de [Azure Portal](https://portal.azure.com/). Selecteer in het linkermenu het tabblad **Azure Active Directory** , selecteer **app-registraties**en selecteer vervolgens uw test drive-app. Selecteer vervolgens **certificaten en geheimen**, selecteer **Nieuw client geheim**, voer een beschrijving in, selecteer **nooit** onder **verlooptd**en kies vervolgens **toevoegen**. Zorg ervoor dat u de waarde kopieert. (Verlaat de pagina niet voordat u dit doet, anders hebt u geen toegang tot de waarde.)

Vergeet niet om te **slaan** voordat u verdergaat met de volgende sectie.

### <a name="test-drive-marketplace-listings"></a>Marketplace-vermeldingen testen

Met de optie **Marketplace-vermelding** die is gevonden op het tabblad **test station** worden de talen weer gegeven waarin uw test drive beschikbaar is. Momenteel is **Engels (Verenigde Staten)** de enige locatie beschikbaar. Selecteer de naam van de taal om informatie in te voeren die de test drive ervaring beschrijft.

- **Beschrijving** (vereist): Geef een beschrijving van uw test drive, wat wordt aangetoond, wat de gebruiker kan doen om te experimenteren met, functies om te verkennen en alle relevante informatie om de gebruiker te helpen bepalen of u uw aanbieding moet verkrijgen. In dit veld kunnen Maxi maal 3.000 tekens tekst worden ingevoerd. 

- **Toegangs gegevens** (vereist voor Azure Resource Manager en logische test stations): Leg uit wat een klant moet weten om deze test drive te kunnen openen en gebruiken. Door loop een scenario voor het gebruik van uw aanbieding en precies wat de klant moet weten voor toegang tot de functies in de test drive. In dit veld kunnen Maxi maal 10.000 tekens tekst worden ingevoerd.

- **Gebruikers handleiding** (vereist): een diep gaande overzicht van uw test drive ervaring. De gebruikers handleiding moet betrekking hebben op precies wat u wilt dat de klant het test drive verkrijgt en zich als referentie voor eventuele vragen bevindt. Het bestand moet een PDF-indeling hebben en een naam hebben (255 tekens Max) na het uploaden.

- **Video's: Video's toevoegen** (optioneel): Video's kunnen worden geüpload naar YouTube of Vimeo en hiernaar wordt verwezen met een koppeling en een miniatuur afbeelding (533 x 324 pixels), zodat een klant een overzicht van de informatie kan bekijken om hen meer inzicht te geven in de test drive, met inbegrip van de manier waarop u de functies van uw aanbieding kunt gebruiken en inzicht kunt krijgen in de voor delen.
  - **Naam** (vereist)
  - **URL (alleen YouTube of Vimeo)** (vereist)
  - **Miniatuur (533 x 324 px)** : het afbeeldings bestand moet de PNG-indeling hebben.

## <a name="supplemental-content"></a>Aanvullende inhoud

Op deze pagina kunt u aanvullende informatie over uw aanbieding opgeven zodat we uw aanbieding kunnen valideren. Deze informatie wordt niet weer gegeven aan klanten of gepubliceerd op Marketplace.

### <a name="target-release"></a>Doel release

Geef aan welke release van micro soft Dynamics business Central uw oplossingen doelen: **Current**, **Next Major**of **Next minor**. Met deze informatie kunnen we uw oplossing op de juiste manier testen.

### <a name="supported-editions"></a>Ondersteunde versies

Als voor uw aanbieding de Premium-editie van micro soft Dynamics 365 Business Central is vereist, selecteert u alleen **Premium** . Als dat niet het geval is, selecteert u zowel **kernen** als **Premium**.

### <a name="key-usage-scenario"></a>Scenario voor sleutel gebruik

U moet een `.pdf`-bestand uploaden met een lijst met de scenario's voor het gebruik van sleutels die worden vermeld in een document (PDF-indeling). Alle scenario's die hier worden vermeld, kunnen door ons validatie team worden geverifieerd voordat we uw aanbieding voor Marketplace goed keuren.

### <a name="app-tests-automation"></a>Automatisering van app-tests

U kunt eventueel een **automatiserings** bestand voor app-tests uploaden (. app).

### <a name="test-accounts"></a>Test accounts

Als u een test account nodig hebt zodat het certificerings team uw aanbieding correct kan controleren, uploadt u een PDF-, doc-of DOCX-bestand met de gegevens van uw **test accounts** .

## <a name="publish"></a>Publiceren

### <a name="submit-offer-to-preview"></a>Aanbieding verzenden naar Preview

Zodra u alle vereiste delen van de aanbieding hebt voltooid, selecteert u **publiceren** in de rechter bovenhoek van de portal. U wordt omgeleid naar de pagina **controleren en publiceren** . 

Als dit de eerste keer is dat u deze aanbieding publiceert, kunt u het volgende doen:

- Bekijk de voltooiings status voor elke sectie van de aanbieding.
    - *Niet gestart* : de sectie is niet gerakend en moet worden voltooid.
    - *Onvolledig* : de sectie bevat fouten die moeten worden hersteld of waarvoor meer informatie moet worden verstrekt. Ga terug naar de sectie (s) en werk deze bij.
    - *Voltooid* : de sectie is voltooid, alle vereiste gegevens zijn opgegeven en er zijn geen fouten. Alle secties van de aanbieding moeten een volledige status hebben voordat u de aanbieding kunt indienen.
- Geef in de sectie **opmerkingen voor certificering** test instructies op voor het certificerings team om ervoor te zorgen dat uw app correct wordt getest, naast eventuele aanvullende notities die nuttig zijn voor de uitleg van uw app.
- Verzend de aanbieding voor publicatie door **verzenden**te selecteren. We sturen u een e-mail wanneer een preview-versie van de aanbieding beschikbaar is om te controleren en goed te keuren. Ga terug naar het partner centrum en selecteer **Go-Live** voor de aanbieding om uw aanbieding te publiceren naar het open bare (of als u een privé-aanbieding naar de persoonlijke doel groep hebt).

## <a name="next-steps"></a>Volgende stappen

- [Een bestaande aanbieding bijwerken in Commerciële Marketplace](./update-existing-offer.md)
