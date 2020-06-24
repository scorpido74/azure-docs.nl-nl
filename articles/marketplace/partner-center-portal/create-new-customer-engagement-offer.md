---
title: Een Dynamics 365 maken voor klant betrokkenheid & PowerApps-aanbieding in micro soft Commercial Marketplace
description: Het maken van een nieuwe Dynamics 365 voor klant betrokkenheid & PowerApps-aanbieding voor het aanbieden of verkopen in azure Marketplace, AppSource of via het programma Cloud Solution Provider (CSP) in partner centrum.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 51df588b1ccd41bf05d8851f00399b61292b0736
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85214095"
---
# <a name="create-a-dynamics-365-for-customer-engagement--powerapps-offer"></a>Een Dynamics 365 for Customer Engagement- en PowerApps-aanbieding maken

In dit onderwerp wordt uitgelegd hoe u een nieuwe Dynamics 365 maakt voor klant betrokkenheid & PowerApps-aanbieding. Alle apps voor Dynamics 365 voor klant betrokkenheid (PowerApps, Sales, service, project service en Field Service) moeten het certificerings proces door lopen en een proef ervaring bieden. Het certificerings proces controleert uw oplossing op standaard vereisten, compatibiliteit en goede prak tijken. Met de proef ervaring kunnen gebruikers uw oplossing implementeren in een live Dynamics 365-omgeving.

Voordat u begint, moet u [een commercieel Marketplace-account maken in Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) als u dit nog niet hebt gedaan. Zorg ervoor dat uw account is inge schreven in het Commercial Marketplace-programma.

## <a name="create-a-new-offer"></a>Een nieuwe aanbieding maken

1. Meld u aan bij [Partner Center](https://partner.microsoft.com/dashboard/home).
2. Selecteer in het menu links de optie **commerciële Marketplace**-  >  **overzicht**.
3. Selecteer op de pagina overzicht **+ nieuw aanbod**  >  **Dynamics 365 voor klant betrokkenheid & PowerApps**.

    ![Illustreert het navigatie menu.](./media/new-offer-dynamics-365-customer-engagement-powerapps.png)

> [!NOTE]
> Nadat een aanbieding is gepubliceerd, worden wijzigingen die in het partner centrum zijn aangebracht, alleen weer gegeven in de winkel wanneer u de aanbieding opnieuw publiceert. Zorg ervoor dat u altijd opnieuw publiceert nadat u wijzigingen hebt aangebracht.

## <a name="new-offer"></a>Nieuwe aanbieding

Voer een **aanbiedings-id**in. Dit is een unieke id voor elke aanbieding in uw account.

- Deze ID is zichtbaar voor klanten in het webadres voor de Marketplace-aanbieding en Azure Resource Manager sjablonen, indien van toepassing.
- Gebruik alleen kleine letters en cijfers. Dit kan afbreek streepjes en onderstrepings tekens bevatten, maar mag niet langer zijn dan 50. Als u bijvoorbeeld **test-aanbieding-1**invoert, is het webadres van de aanbieding `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- De aanbiedings-ID kan niet worden gewijzigd nadat u **maken**hebt geselecteerd.

Voer een **alias**voor de aanbieding in. Dit is de naam die wordt gebruikt voor de aanbieding in Partner Center.

- Deze naam wordt niet gebruikt in Marketplace en wijkt af van de naam van de aanbieding en andere waarden die aan klanten worden weer gegeven.
- Dit kan niet worden gewijzigd nadat u **maken**hebt geselecteerd.

Selecteer **maken** om de aanbieding te genereren en door te gaan.

## <a name="offer-setup"></a>Installatie van aanbieding

Volg deze stappen om uw aanbieding in te stellen.

### <a name="how-do-you-want-potential-customers-to-interact-with-this-listing-offer"></a>Hoe wilt u potentiële klanten interactie laten doen met deze aanbieding?

Selecteer de optie die u wilt gebruiken voor deze aanbieding.

#### <a name="get-it-now-free"></a>Nu downloaden (gratis)

Bied uw aanbod aan klanten gratis aan door een geldige URL op te geven (te beginnen met *http* of *https*), waar ze toegang hebben tot uw app.  Bijvoorbeeld `https://contoso.com/my-app`.

#### <a name="free-trial-listing"></a>Gratis proef versie (lijst)

Bied uw aanbieding aan klanten aan met een koppeling naar een gratis proef versie door een geldige URL op te geven (beginnend met `http` of `https` ), waar een proef versie kan worden verkregen.  Bijvoorbeeld `https://contoso.com/trial/my-app`. Gratis proef versies van aanbieding worden gemaakt, beheerd en geconfigureerd door uw service en er zijn geen abonnementen die door micro soft worden beheerd.

> [!NOTE]
> De tokens die uw toepassing via uw proef koppeling ontvangt, kunnen alleen worden gebruikt voor het verkrijgen van gebruikers gegevens via Azure Active Directory (Azure AD) om het maken van accounts in uw app te automatiseren. Micro soft-accounts worden niet ondersteund voor verificatie met behulp van dit token.

#### <a name="contact-me"></a>Contact opnemen

Contact gegevens van klanten verzamelen door verbinding te maken met uw CRM-systeem (Customer Relationship Management). De klant wordt gevraagd om toestemming te krijgen om hun gegevens te delen. Deze klant gegevens, samen met de naam van de aanbieding, ID en Marketplace-bron waar ze uw aanbieding vinden, worden verzonden naar het CRM-systeem dat u hebt geconfigureerd. Zie [leads van klanten](#customer-leads)voor meer informatie over het configureren van uw CRM.

Selecteer **concept opslaan** voordat u doorgaat.

### <a name="test-drive"></a>Station testen

Een test drive is een fantastische manier om uw aanbieding aan potentiële klanten te laten presen teren door hen de mogelijkheid te bieden om te kopen voordat u aan de slag gaat, wat resulteert in een verhoogde conversie en de generatie van uiterst gekwalificeerde leads. Meer [informatie over test stations](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive).

Als u een test drive voor een bepaalde periode wilt inschakelen, schakelt u het selectie vakje **een test drive inschakelen** in. Als u test drive uit uw aanbieding wilt verwijderen, schakelt u dit selectie vakje uit. Configureer de test drive omgeving in het gedeelte [technische configuratie van het test station](#test-drive-technical-configuration) verderop in dit onderwerp.

Zie [Test uw aanbieding in de commerciële Marketplace](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive)voor meer informatie.

#### <a name="type-of-test-drive"></a>Type test drive

Maak een keuze uit de volgende opties:

- **[Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)** : een implementatie sjabloon die alle Azure-resources bevat waaruit uw oplossing bestaat. Voor producten die in dit scenario passen, worden alleen Azure-resources gebruikt.
- **[Dynamics 365 voor bedrijven Central](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)** : micro soft host en onderhoudt de Test Drive-service (met inbegrip van inrichting en implementatie) voor een zakelijk centraal Enter prise resource planning System (Financiën, bedrijfs activiteiten, toeleverings keten, CRM, enzovoort).  
- **[Dynamics 365 voor klant betrokkenheid](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)** : micro soft host en onderhoudt de Test Drive service (inclusief inrichting en implementatie) voor een klant engagementsysteem (verkoop, service, project service, Field Service, enzovoort).  
- **[Dynamics 365 voor bewerkingen](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)** : micro soft host en onderhoudt de Test Drive-service (inclusief inrichting en implementatie) voor een financieel en operationeel systeem voor de planning van bedrijfs middelen (Financiën, bedrijfs activiteiten, productie, toeleverings keten, enzovoort). 
- **[Logische app](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)** : een implementatie sjabloon met alle complexe oplossings architecturen. Aangepaste producten moeten dit type test station gebruiken.
- **[Power bi](https://docs.microsoft.com/power-bi/service-template-apps-overview)** : een Inge sloten koppeling naar een aangepast dash board. Producten die een interactief Power BI visueel element willen demonstreren, moeten dit type test station gebruiken. Alles wat u moet uploaden, is uw Inge sloten Power BI URL.

#### <a name="additional-test-drive-resources"></a>Aanvullende test drive resources

- [Technische best practices](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Best practices voor marketing](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Overzicht van test stations](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) PDF (zorg ervoor dat de pop-upblokkering is uitgeschakeld)

### <a name="customer-leads"></a>Leads van klanten

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Zie [Lead Management Overview](./commercial-marketplace-get-customer-leads.md)voor meer informatie.

Selecteer **concept opslaan** voordat u doorgaat.

## <a name="properties"></a>Eigenschappen

Op deze pagina kunt u de categorieën en industrieën definiëren die worden gebruikt voor het groeperen van uw aanbieding op Marketplace, uw app-versie en de juridische contracten die uw aanbieding ondersteunen.

### <a name="category"></a>Categorie

Selecteer ten minste één en Maxi maal drie categorieën. Deze worden gebruikt om uw aanbieding te plaatsen in de juiste Zoek gebieden voor Marketplace. Zorg ervoor dat u beschrijft hoe uw aanbod deze categorieën ondersteunt in de beschrijving van de aanbieding.

### <a name="industry"></a>Branche

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="applicable-dynamics-365-products"></a>Toepasselijke Dynamics 365-producten

Selecteer alle Dynamics 365-producten waarop deze aanbieding van toepassing is.

### <a name="app-version"></a>App-versie

Voer het versie nummer van uw aanbieding in. Klanten zien dat deze versie wordt weer gegeven op de detail pagina van de aanbieding. Als u het versie nummer alleen bijwerkt als gevolg van marketing/beschrijvende wijzigingen, schakelt u het selectie vakje **alleen marketing** in. Met deze optie kan de aanbieding de certificerings-en inrichtings fases omzeilen.

### <a name="terms-and-conditions"></a>Voorwaarden

Geef hier uw eigen juridische voor waarden op. U kunt ook het adres opgeven waar uw voor waarden kunnen worden gevonden. Klanten moeten deze voor waarden accepteren voordat ze uw aanbieding kunnen proberen.

Selecteer **concept opslaan** voordat u doorgaat.

## <a name="offer-listing"></a>Aanbieding weer geven

Op deze pagina worden de talen weer gegeven waarin uw aanbieding wordt weer gegeven. **Engels (Verenigde Staten)** is momenteel de enige beschik bare optie.

Geef hier Marketplace-Details op voor elke taal/markt, zoals aanbiedings naam, beschrijving en installatie kopieën. Selecteer de naam van de taal/markt om deze informatie op te geven.

> [!NOTE]
> De inhoud van het aanbiedings aanbod (zoals de beschrijving, documenten, scherm afbeeldingen en gebruiks voorwaarden) is niet vereist voor het Engels, zolang de beschrijving van het aanbod begint met de woord groep ' deze toepassing is alleen beschikbaar in [niet-Engelse taal] '. Het is ook acceptabel om een *nuttige koppelings-URL* te bieden om inhoud te bieden in een andere taal dan de versie die wordt gebruikt in de inhoud van de aanbieding.

Hier volgt een voor beeld van hoe de aanbiedings gegevens worden weer gegeven in Microsoft AppSource (alle prijzen zijn bijvoorbeeld alleen bedoeld als voor beeld van de werkelijke kosten):

:::image type="content" source="media/example-azure-marketplace-d365-customer-engagement.png" alt-text="Illustreert hoe deze aanbieding wordt weer gegeven in Microsoft AppSource.":::

#### <a name="call-out-descriptions"></a>Beschrijvingen van aanroepen

1. Logo
2. Producten
3. Categorieën
4. Ondersteunings adres (koppeling)
5. Gebruiksvoorwaarden adres (koppeling)
6. Naam van aanbieding
7. Beschrijving
8. Scherm afbeeldingen/Video's

### <a name="name"></a>Name

De naam die u hier invoert, wordt aan klanten weer gegeven als de titel van de aanbieding. Dit veld is vooraf ingevuld met de tekst die u hebt ingevoerd voor de **aanbiedings alias** tijdens het maken van de aanbieding, maar u kunt deze waarde wijzigen. Deze naam kan worden aangemerkt (en u kunt symbolen van het handels merk of copyright bevatten). De naam mag niet langer zijn dan 50 tekens en mag geen emojis bevatten.

### <a name="short-description"></a>Korte beschrijving

Geef een korte beschrijving van uw aanbieding, Maxi maal 100 tekens. Deze beschrijving kan worden gebruikt in Zoek resultaten voor Marketplace.

### <a name="description"></a>Beschrijving

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

### <a name="search-keywords"></a>Tref woorden zoeken

U kunt eventueel Maxi maal drie Zoek trefwoorden invoeren om klanten te helpen uw aanbieding op Marketplace te vinden. Voor de beste resultaten gebruikt u deze tref woorden ook in uw beschrijving.

### <a name="products-your-app-works-with"></a>Producten waarmee uw app werkt

Als u klanten wilt laten weten dat uw app werkt met specifieke producten, voert u hier Maxi maal drie product namen in.

### <a name="support-urls"></a>Url's voor ondersteuning

In deze sectie vindt u koppelingen waarmee klanten meer inzicht kunnen krijgen in uw aanbieding.

#### <a name="help-link"></a>Help-koppeling

Voer het adres in waar klanten meer kunnen weten over uw aanbieding.

#### <a name="privacy-policy-url"></a>URL van privacybeleid

Voer het adres in voor het privacybeleid van uw organisatie. U bent verantwoordelijk om ervoor te zorgen dat uw app voldoet aan de wetten en voor schriften van de privacy en om een geldig privacybeleid te bieden.

### <a name="contacts"></a>Contactpersonen

Geef de naam, het e-mail adres en het telefoon nummer op voor een **ondersteunings contact** en een **technische contact persoon**. Deze informatie wordt niet weer gegeven aan klanten, maar is beschikbaar voor micro soft en kan worden verschaft aan CSP-partners.

In het gedeelte **ondersteuning voor contact opnemen** moet u ook de **ondersteunings-URL** opgeven waar CSP-partners ondersteuning voor uw aanbieding kunnen vinden.

### <a name="supporting-documents"></a>Ondersteunende documenten

Geef hier ten minste één (en Maxi maal drie) gerelateerde marketing documenten op, zoals witboeken, brochures, controle lijsten of presentaties, in PDF-indeling.

### <a name="marketplace-images"></a>Marketplace-installatie kopieën

Bied logo's en installatie kopieën voor uw aanbieding. Alle installatie kopieën moeten de PNG-indeling hebben.

>[!NOTE]
>Als u een probleem hebt met het uploaden van bestanden, moet u ervoor zorgen dat uw lokale netwerk de door https://upload.xboxlive.com Partner Center gebruikte service niet blokkeert.

#### <a name="store-logos"></a>Winkel logo's

Geef het logo van uw aanbod op in drie pixel grootten:
- **Klein** (vereist; 48 x 48)
- **Groot** (vereist; 216 x 216)
- **Breed** (optioneel; 255 x 115)

#### <a name="hero"></a>Hero

De afbeelding van de held is optioneel. Als u een waarde opgeeft, moet deze 815 x 290 pixels meten.

#### <a name="screenshots"></a>Schermopnamen

Scherm afbeeldingen toevoegen die laten zien hoe uw aanbieding werkt. Ten minste één scherm opname is vereist en u kunt Maxi maal vijf toevoegen. Alle scherm afbeeldingen moeten 1280 x 720 pixels zijn.

#### <a name="videos"></a>Video's

U kunt optioneel Maxi maal vier Video's toevoegen die uw aanbieding aantonen. Deze Video's moeten worden gehost op YouTube en/of Vimeo. Voer voor elke video de naam, de URL en een miniatuur afbeelding van de video in (1280 x 720 pixels)

#### <a name="additional-marketplace-listing-resources"></a>Aanvullende bronnen voor Marketplace-lijst

[Best practices voor Marketplace-aanbiedingen](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

Selecteer **concept opslaan** voordat u doorgaat.

## <a name="availability"></a>Beschikbaarheid

Op deze pagina kunt u definiëren waar en hoe uw aanbieding beschikbaar moet worden gesteld.

### <a name="markets"></a>Landen

Geef de markten op waarin uw aanbieding beschikbaar moet zijn. Als u dit wilt doen, selecteert u **markten bewerken**om het pop-upvenster **markt selectie** weer te geven.

Standaard zijn er geen markten geselecteerd, maar u moet ten minste één markt selecteren om uw aanbieding te publiceren. Kies **Alles selecteren** om uw aanbieding beschikbaar te stellen op elke mogelijke markt of selecteer de specifieke markten die u wilt toevoegen.

Uw selecties zijn hier alleen van toepassing op nieuwe verwervingen; Als iemand uw app al in een bepaalde markt heeft en u deze markt later verwijdert, kunnen de personen die de aanbieding al op die markt hebben, deze blijven gebruiken, maar kunnen er geen nieuwe klanten op deze markt worden aangeboden.

> [!IMPORTANT]
> Het is uw verantwoordelijkheid om te voldoen aan alle lokale wettelijke vereisten, zelfs als deze vereisten hier niet worden vermeld of in het partner centrum. Zelfs als u alle markten selecteert, kunnen lokale wetten en beperkingen of andere factoren verhinderen dat bepaalde aanbiedingen in sommige landen en regio's worden weer gegeven.

### <a name="preview-audience"></a>Voor beeld van doel groep

Voordat u uw aanbieding naar de bredere Marketplace-aanbieding publiceert, moet u deze eerst beschikbaar maken voor een beperkte preview- **doel groep**. Voer hier een **Hide-toets** (wille keurige teken reeks met alleen kleine letters en/of cijfers) in. Leden van uw preview-doel groep kunnen deze verbergen sleutel als een token gebruiken om een voor beeld van uw aanbieding op Marketplace te bekijken.

Wanneer u klaar bent om uw aanbieding beschikbaar te maken en de preview-beperking te verwijderen, moet u de **sleutel verbergen** verwijderen en opnieuw publiceren.

Selecteer **concept opslaan** voordat u doorgaat.

## <a name="technical-configuration"></a>Technische configuratie

Op deze pagina worden de technische details gedefinieerd die worden gebruikt om verbinding te maken met uw aanbieding. Met deze verbinding kan ons uw aanbieding voor de eind klant inrichten als deze ervoor kiezen deze te verkrijgen.

### <a name="base-license-model"></a>Basis licentie model

Het basis licentie model bepaalt hoe klanten uw toepassing toewijzen in het CRM-beheer centrum. Selecteer **resource** voor op exemplaren gebaseerde licentie verlening of **gebruiker** als er één per Tenant aan licenties wordt toegewezen.

### <a name="requires-s2s-outbound-and-crm-secure-store-access"></a>Vereist S2S-uitgaande en CRM Secure Store-toegang

Schakel dit selectie vakje in om de configuratie van de beveiligde Store van CRM of de server-naar-server (S2S) uitgaande toegang in te scha kelen. Deze functie vereist een specialistische overweging van het Dynamics 365-team tijdens de certificerings periode. Micro soft neemt contact met u op om extra stappen uit te voeren om deze functie te ondersteunen.

### <a name="application-configuration-url"></a>Configuratie-URL van de toepassing

Geef de URL op voor de configuratie webpagina waarmee de klant uw app kan configureren.

### <a name="crm-package"></a>CRM-pakket

Voer in het veld **URL van uw pakket locatie** de URL in van een Azure Blob Storage-account dat het geüploade bestand CRM package. zip bevat. Neem een alleen-lezen SAS-sleutel op in de URL, zodat micro soft uw pakket voor verificatie kan ophalen.

> [!IMPORTANT]
> Als u een publicatie blok wilt voor komen, moet u ervoor zorgen dat de verval datum in de URL van de Blob-opslag nog niet is verlopen. U kunt de datum wijzigen door toegang tot uw beleid te krijgen. We raden u aan de **verloop tijd** ten minste één maand in de toekomst te hebben.

Selecteer het vak met het label **Er is meer dan één CRM-pakket in het pakket bestand**, indien van toepassing. Als dat het geval is, moet u alle pakketten in uw zip-bestand toevoegen.

Zie [stap 3: een AppSource-pakket maken voor uw app](https://docs.microsoft.com/powerapps/developer/common-data-service/create-package-app-appsource)voor meer informatie over het bouwen van uw pakket en het bijwerken van de structuur.

### <a name="crm-package-availability"></a>Beschik baarheid van CRM-pakket

Selecteer in deze sectie **+ regio toevoegen** om de geografische regio's op te geven waarin uw CRM-pakket beschikbaar is voor klanten. Implementatie in de volgende soevereine regio's vereisen speciale machtigingen en validatie tijdens het certificerings proces: [Duitsland](https://docs.microsoft.com/azure/germany/), [Amerikaanse OVERHEIDS Cloud](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)en tip.

De **configuratie-URL** voor de toepassing die u hierboven hebt opgegeven, wordt standaard gebruikt voor elke regio. Als u wilt, kunt u een afzonderlijke configuratie-URL voor de toepassing invoeren voor een of meer specifieke regio's. 

Selecteer **concept opslaan** voordat u doorgaat.

## <a name="test-drive-technical-configuration"></a>Technische configuratie van test station

Op deze pagina kunt u een demonstratie ("test drive") instellen waarmee klanten uw aanbieding kunnen proberen voordat ze deze aanschaffen. Meer informatie in het artikel [Wat is een test station?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

Als u een test drive wilt inschakelen, schakelt u het selectie vakje een test drive inschakelen in op het tabblad installatie van de [aanbieding](#test-drive) . Als u test drive uit uw aanbieding wilt verwijderen, schakelt u dit selectie vakje uit.

Meer informatie in het artikel [Wat is een test station?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

De volgende typen test stations zijn beschikbaar, elk met hun eigen technische configuratie vereisten:

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [Logische app](#technical-configuration-for-logic-app-test-drive)
- [Power bi](#technical-configuration-not-required-for-power-bi-test-drives) (technische configuratie niet vereist)

### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Technische configuratie voor Azure Resource Manager test drive

Een implementatie sjabloon die alle Azure-resources bevat waaruit uw oplossing bestaat. Voor producten die in dit scenario passen, worden alleen Azure-resources gebruikt. Meer informatie over het instellen van een [Azure Resource Manager test drive](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

- **Regio's** (vereist): er zijn momenteel 26 door Azure ondersteunde regio's waar uw test drive beschikbaar kan worden gemaakt. Normaal gesp roken wilt u uw test drive beschikbaar maken in de regio's waar u het grootste aantal klanten verwacht, zodat ze de dichtstbijzijnde regio voor de beste prestaties kunnen selecteren. U moet ervoor zorgen dat uw abonnement alle resources mag implementeren die nodig zijn voor elk van de regio's die u selecteert.
- **Instanties** : Selecteer het type (warm of koud) en het aantal beschik bare instanties dat wordt vermenigvuldigd met het aantal regio's waar uw aanbieding beschikbaar is.

    **Dynamisch** : dit type exemplaar wordt geïmplementeerd en wacht op toegang per geselecteerde regio. Klanten hebben direct toegang tot *Hot* instances van een test drive in plaats van te wachten op een implementatie. De balans is dat deze instanties altijd worden uitgevoerd op uw Azure-abonnement, zodat de kosten voor de uptime groter worden. Het wordt ten zeerste aanbevolen om ten minste één *Hot* instance te hebben, aangezien de meeste klanten niet hoeven te wachten op volledige implementaties, wat leidt tot uitval van klant gebruik als er geen *Hot* instance beschikbaar is.

    **Koud** : dit type exemplaar vertegenwoordigt het totale aantal instanties dat mogelijk per regio kan worden geïmplementeerd. Voor koude instanties is het hele test station Resource Manager-sjabloon vereist om te implementeren wanneer een klant de test drive aanvraagt, zodat *koude* instanties veel langzamer worden geladen dan *Hot* instances. Het bedrag dat u alleen hoeft te betalen voor de duur van de test drive, wordt *niet* altijd uitgevoerd op uw Azure-abonnement, net als bij een *Hot* -instance.

- **Azure Resource Manager-sjabloon testen** : Upload de. zip met uw Azure Resource Manager sjabloon. Meer informatie over het maken van een Azure Resource Manager sjabloon in het Quick Start-artikel [Azure Resource Manager sjablonen maken en implementeren met behulp van de Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).

- **Duur van test drive** (vereist): Voer het aantal uren in dat het test drive actief blijft. De testdDrive wordt automatisch beëindigd nadat deze periode is verstreken. Deze duur mag alleen in hele uren worden ingesteld (bijvoorbeeld ' 2 ' uur is geldig; "1,5" is niet).

### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Technische configuratie voor Dynamics 365 test drive

Micro soft kan de complexiteit van het instellen van een test drive verwijderen door de service-inrichting en-implementatie te hosten en te onderhouden met dit type test drive. De configuratie voor dit type gehoste test drive is hetzelfde, ongeacht of de test drive is gericht op een zakelijke centrale, klant afspraak of bewerkings publiek.

- **Maxi maal aantal gelijktijdige test schijven** (vereist): Stel het maximum in voor de klanten die uw test drive tegelijk kunnen gebruiken. Elke gelijktijdige gebruiker gebruikt een Dynamics 365-licentie terwijl de test drive actief is. u moet er dus voor zorgen dat er voldoende licenties beschikbaar zijn voor de ondersteuning van de maximale limiet. De aanbevolen waarde is 3-5.

- **Duur van test drive** (vereist): Voer het aantal uren in dat het test drive actief blijft. Na deze periode wordt de sessie beëindigd en wordt een van uw licenties niet meer gebruikt. We raden een waarde van 2-24 uur aan, afhankelijk van de complexiteit van uw aanbieding. Deze duur mag alleen in hele uren worden ingesteld (bijvoorbeeld ' 2 ' uur is geldig; "1,5" is niet). De gebruiker kan een nieuwe sessie aanvragen als deze niet langer actief zijn en de test drive opnieuw willen gebruiken.

- **URL van exemplaar** (vereist): de URL waar de klant de test drive begint. Doorgaans de URL van uw Dynamics 365-exemplaar waarop uw app wordt uitgevoerd, waarbij voorbeeld gegevens zijn geïnstalleerd (bijvoorbeeld `https://testdrive.crm.dynamics.com` ).

- **URL** van de Web-API voor het exemplaar (vereist): Haal de Web-API-URL voor uw Dynamics 365-exemplaar op door u aan te melden bij uw Microsoft 365-account en te navigeren naar **instellingen**  >  **aanpassing**  >  **ontwikkelaars resources**  >  **instance Web API (Service root URL)**, kopieer de URL die u hier kunt vinden (bijvoorbeeld `https://testdrive.crm.dynamics.com/api/data/v9.0` ).

- **Rolnaam** (vereist): Geef de naam op van de beveiligingsrol die u hebt gedefinieerd in uw aangepaste Dynamics 365-test drive. Deze beveiligingsrol naam wordt toegewezen aan de gebruiker tijdens hun test drive (bijvoorbeeld test station-Role).

### <a name="technical-configuration-for-logic-app-test-drive"></a>Technische configuratie voor de test drive Logic-app

Aangepaste producten moeten gebruikmaken van dit type test drive-implementatie sjabloon, dat een verscheidenheid aan complexe oplossings architecturen omvat. Ga voor meer informatie over het instellen van de test schijven voor logische apps naar [activiteiten](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) en [klanten betrokkenheid](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) op github.

- **Regio** (vereist, vervolg keuzelijst met één selectie): er zijn momenteel 26 door Azure ondersteunde regio's waar uw test drive beschikbaar kan worden gemaakt. De resources voor uw logische app worden geïmplementeerd in de regio die u selecteert. Als uw logische app aangepaste resources heeft die zijn opgeslagen in een bepaalde regio, moet u ervoor zorgen dat de regio hier wordt geselecteerd. De beste manier om dit te doen is om uw logische app lokaal te implementeren in uw Azure-abonnement in de portal en te controleren of deze correct werkt voordat u deze selectie maakt.

- **Maxi maal aantal gelijktijdige test schijven** (vereist): Stel het maximum in voor de klanten die uw test drive tegelijk kunnen gebruiken. Deze test stations zijn al geïmplementeerd, zodat klanten deze direct kunnen openen zonder te hoeven wachten op een implementatie.

- **Test drive** (vereist): Voer de tijds duur in die het test station actief blijft, in aantal uren. De test drive wordt automatisch beëindigd nadat deze periode is verstreken.

- **Naam van de Azure-resource groep** (vereist): Voer de naam in van de [Azure-resource groep](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) waar uw logische app test drive wordt opgeslagen.

- **Naam van de Azure Logic-app** (vereist): Voer de naam in van de logische app die de test drive aan de gebruiker toewijst. Deze logische app moet worden opgeslagen in de bovenstaande Azure-resource groep.

- De naam van de **logische app** voor het ongedaan maken van de inrichting (vereist): Voer de naam in van de logische app die de test drive nadat de klant is voltooid. Deze logische app moet worden opgeslagen in de bovenstaande Azure-resource groep.

### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Technische configuratie niet vereist voor Power BI test stations

Producten die een interactief Power BI visueel element willen demonstreren, kunnen een Inge sloten koppeling gebruiken om een aangepast dash board te delen als hun test drive, maar er is geen verdere technische configuratie vereist. Meer informatie over het instellen van[Power bi](https://docs.microsoft.com/power-bi/service-template-apps-overview) sjabloon-apps.

### <a name="deployment-subscription-details"></a>Details van implementatie abonnement

Als u wilt dat micro soft de test drive namens u kan implementeren, maakt en verstrekt u een afzonderlijk, uniek Azure-abonnement (niet vereist voor Power BI test stations).

- **Azure-abonnements-id** (vereist voor Azure Resource Manager en Logic apps): Voer de abonnements-id in om toegang te verlenen tot uw Azure-account services voor rapportage en facturering van resource gebruik. Het is raadzaam om [een afzonderlijk Azure-abonnement te maken](https://docs.microsoft.com/azure/billing/billing-create-subscription) dat u voor test stations kunt gebruiken als u er nog geen hebt. U kunt uw Azure-abonnements-ID vinden door u aan te melden bij de [Azure Portal](https://portal.azure.com/) en te navigeren naar het tabblad **abonnementen** van het menu aan de linkerkant. Als u het tabblad selecteert, wordt uw abonnements-ID weer gegeven (bijvoorbeeld "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **Azure AD-Tenant-id** (vereist): voer uw Azure Active Directory (AD) [Tenant-id](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)in. Als u deze ID wilt vinden, meldt u zich aan bij de [Azure Portal](https://portal.azure.com/), selecteert u het tabblad Active Directory in het menu links, selecteert u **Eigenschappen**en zoekt u naar de weer gegeven **Directory-id** (bijvoorbeeld 50c464d3-4930-494c-963c-1e951d15360e). U kunt ook de Tenant-ID van uw organisatie opzoeken met de domein naam-URL op [https://www.whatismytenantid.com](https://www.whatismytenantid.com) .

- **Naam van Azure AD-Tenant** (vereist voor dynamische 365): Voer de naam van uw Azure Active Directory (AD) in. Als u deze naam wilt vinden, meldt u zich aan bij de [Azure Portal](https://portal.azure.com/). in de rechter bovenhoek wordt de naam van de Tenant vermeld onder uw account naam.

- **Azure AD-App-ID** (vereist): voer uw Azure Active Directory (AD) [-toepassings-id](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)in. Als u deze ID wilt vinden, meldt u zich aan bij de [Azure Portal](https://portal.azure.com/), selecteert u het tabblad Active Directory in het menu links, selecteert u **app-registraties**en zoekt u naar het **toepassings-id-** nummer dat wordt weer gegeven (bijvoorbeeld 50c464d3-4930-494c-963c-1e951d15360e).

- **Azure AD-App-client geheim** (vereist): Voer uw Azure AD-toepassings [client geheim](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)in. Als u deze waarde wilt vinden, meldt u zich aan bij de [Azure Portal](https://portal.azure.com/). Selecteer in het linkermenu het tabblad **Azure Active Directory** , selecteer **app-registraties**en selecteer vervolgens uw test drive-app. Selecteer vervolgens **certificaten en geheimen**, selecteer **Nieuw client geheim**, voer een beschrijving in, selecteer **nooit** onder **verlooptd**en kies vervolgens **toevoegen**. Zorg ervoor dat u de waarde kopieert. Navigeer niet weg van de pagina voordat u de waarde kopieert of u hebt geen toegang tot de waarde.)

### <a name="test-drive-marketplace-listings"></a>Marketplace-vermeldingen testen

Met de optie **Marketplace-vermelding** die is gevonden op het tabblad **test station** worden de talen weer gegeven waarin uw test drive beschikbaar is. **Engels (Verenigde Staten)** is momenteel de enige locatie die beschikbaar is. Selecteer de naam van de taal om informatie in te voeren die de test drive ervaring beschrijft.

- **Beschrijving** (vereist): Geef een beschrijving van uw test drive, wat wordt aangetoond, wat de gebruiker kan doen om te experimenteren met, functies om te verkennen en alle relevante informatie waarmee de gebruiker kan bepalen of u uw aanbieding kunt verkrijgen. In dit veld kunnen Maxi maal 3.000 tekens tekst worden ingevoerd. 

- **Toegangs gegevens** (vereist voor Azure Resource Manager en logische test stations): Leg uit wat een klant moet weten om deze test drive te kunnen openen en gebruiken. Door loop een scenario voor het gebruik van uw aanbieding en precies wat de klant moet weten voor toegang tot de functies in de test drive. In dit veld kunnen Maxi maal 10.000 tekens tekst worden ingevoerd.

- **Gebruikers handleiding** (vereist): een diep gaande uitleg van uw test drive-ervaring. De gebruikers handleiding moet betrekking hebben op precies wat u wilt dat de klant het test drive verkrijgt en zich als referentie voor eventuele vragen bevindt. Het bestand moet een PDF-indeling hebben en een naam hebben (255 tekens Max) na het uploaden.

- **Video's** (optioneel): Upload Video's naar een externe hosting site naar de koppeling en de miniatuur afbeelding (533 x 324 pixels). Zo kan de klant een overzicht van de informatie bekijken om hen te helpen beter inzicht te krijgen in de test drive, met inbegrip van de manier waarop u de functies van uw aanbieding kunt gebruiken en inzicht krijgt in scenario's die hun voor delen accentueren.
  - **Naam** (vereist)
  - **Adres** (vereist; Alleen YouTube of Vimeo)
  - **Miniatuur** afbeelding (afbeeldings bestand moet de PNG-indeling hebben en 533 x 324 pixels)

Selecteer **concept opslaan** voordat u doorgaat.

## <a name="supplemental-content"></a>Aanvullende inhoud

Op deze pagina kunt u aanvullende informatie over uw aanbieding opgeven zodat we uw aanbieding kunnen valideren. Deze informatie wordt niet weer gegeven aan klanten of gepubliceerd op Marketplace.

### <a name="key-usage-scenario"></a>Scenario voor sleutel gebruik

Upload een PDF-bestand met een lijst met belang rijke scenario's voor het gebruik van uw aanbieding. Alle scenario's kunnen door ons validatie team worden geverifieerd voordat we uw aanbieding voor Marketplace goed keuren.

Selecteer **concept opslaan** voordat u doorgaat.

## <a name="publish"></a>Publiceren

### <a name="submit-offer-to-preview"></a>Aanbieding verzenden naar Preview

Nadat u alle vereiste delen van de aanbieding hebt voltooid, selecteert u **controleren en publiceren** in de rechter bovenhoek van de portal.

Als dit de eerste keer is dat u deze aanbieding publiceert, kunt u het volgende doen:

- Bekijk de voltooiings status voor elke sectie van de aanbieding.
    - **Niet gestart** : de sectie is niet gerakend en moet worden voltooid.
    - **Onvolledig** : de sectie bevat fouten die moeten worden hersteld of waarvoor meer informatie moet worden verstrekt. Ga terug naar de sectie (s) en werk deze bij.
    - **Voltooid** : de sectie is voltooid, alle vereiste gegevens zijn opgegeven en er zijn geen fouten. Alle secties van de aanbieding moeten een volledige status hebben voordat u de aanbieding kunt indienen.
- Geef in de sectie **opmerkingen voor certificering** test instructies op voor het certificerings team om ervoor te zorgen dat uw app correct wordt getest, naast eventuele aanvullende notities die nuttig zijn voor de uitleg van uw app.
- Verzend de aanbieding voor publicatie door **verzenden**te selecteren. We sturen u een e-mail om u te laten weten wanneer een preview-versie van de aanbieding beschikbaar is om te controleren en goed te keuren. Ga terug naar het partner centrum en selecteer **Go-Live** voor de aanbieding om te publiceren naar het publiek (of als u een privé-aanbieding naar de privé doelgroep wilt).

## <a name="next-step"></a>Volgende stap

- [Een bestaande aanbieding bijwerken in Commerciële Marketplace](./update-existing-offer.md)
