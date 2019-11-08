---
title: Een nieuwe SaaS-aanbieding maken in de commerciële Marketplace
description: Het maken van een nieuwe SaaS-aanbieding (Software as a Service) voor het aanbieden of verkopen van objecten in azure Marketplace, AppSource of via het programma Cloud Solution Provider (CSP) met behulp van de commerciële Marketplace-Portal in micro soft Partner Center.
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/04/2019
ms.openlocfilehash: 9eb283f538759f9591add4b04462de151f2cb014
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825552"
---
# <a name="create-a-new-saas-offer"></a>Een nieuwe SaaS-aanbieding maken

Als u wilt beginnen met het maken van SaaS-aanbiedingen (Software as a Service), moet u eerst [een partner centrum-account maken](./create-account.md) en het [dash board commerciële Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)openen, met het tabblad **overzicht** geselecteerd.

![Dash board voor commerciële Marketplace in het partner centrum](./media/new-offer-overview.png)

>[!Note]
> Zodra een aanbieding is gepubliceerd, worden wijzigingen die zijn aangebracht in het partner centrum, alleen in het systeem bijgewerkt en opgeslagen vóór het opnieuw publiceren. Zorg ervoor dat u de aanbieding voor publicatie verzendt nadat u wijzigingen hebt aangebracht.

Selecteer de + **nieuwe aanbieding...** en selecteert u vervolgens het menu-item **software als service** . 

Als u een ander type aanbieding selecteert, wordt u mogelijk omgeleid naar de oudere [Cloud Partner-Portal](https://cloudpartner.azure.com/). Op dit moment zijn alleen SaaS-en Dynamics 365-aanbiedingen beschikbaar in de commerciële Marketplace-Portal op het partner centrum.

![Aanbiedings venster maken in Partner Center](./media/new-offer-click.png)

Het dialoog venster **nieuwe aanbieding** wordt weer gegeven. 

![Dialoog venster nieuwe aanbieding](./media/new-offer-popup.png)


## <a name="offer-id-and-alias"></a>Aanbiedings-ID en alias

- **Aanbiedings-id**: unieke id voor elke aanbieding in uw account. Deze ID is zichtbaar voor klanten in het URL-adres voor de Marketplace-aanbieding en Azure Resource Manager sjablonen (indien van toepassing). De aanbiedings-ID moet kleine letters, alfanumerieke tekens (inclusief afbreek streepjes en onderstrepings tekens, maar geen witruimte) zijn. Dit is beperkt tot 50 tekens en kan niet worden gewijzigd nadat u *maken*hebt geselecteerd.  
Voor beeld: test-aanbieding-1
<br>Wat resulteert in de URL: `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`

- **Aanbiedings alias**: de naam die wordt gebruikt om te verwijzen naar de aanbieding in de Partner Center-Portal. Deze naam wordt niet gebruikt op de Marketplace en wijkt af van de *naam* van de aanbieding en andere waarden die aan klanten worden weer gegeven. Deze waarde kan niet worden gewijzigd nadat u *maken*hebt geselecteerd.

<br>Voor beeld: test aanbieding 1&#8482;

Selecteer **Maken**.  Er wordt een **overzichts** pagina voor de aanbieding gemaakt voor deze aanbieding.  

<!---
![Offer overview on Partner Center](./media/commercial-marketplace-offer-overview.png)
-->

## <a name="offer-overview"></a>Overzicht van aanbieding

De pagina overzicht van de **aanbieding** bevat: 

- De **publicatie status** geeft een visuele weer gave van de stappen die nodig zijn voor het publiceren van deze aanbieding en hoe lang elke stap zal worden voltooid. Onvolledige publicatie stap pictogrammen worden grijs weer gegeven. 

- Het menu overzicht van de **aanbieding** bevat een lijst met koppelingen voor het uitvoeren van bewerkingen op deze aanbieding. Deze lijst met bewerkingen wordt gewijzigd op basis van de selectie die u voor uw aanbieding maakt.  
    - Als de aanbieding een concept is-concept verwijderen 
    - Als het aanbod een live-stop verkoop aanbod is 
    - Als de aanbieding in Preview-Live staat 
    - Als u de uitgever niet hebt voltooid, kunt u de publicatie annuleren

## <a name="offer-setup"></a>Installatie van aanbieding

Op het tabblad voor het instellen van de **aanbieding** wordt de volgende informatie gevraagd. Selecteer **Opslaan** na het volt ooien van deze velden.

- **Wilt u door micro soft verkopen?** (Ja/Nee)
    - **Ja**, u wilt uw aanbieding via micro soft verkopen met micro soft-hosting van Marketplace-trans acties namens u; of 
    - **Nee**, u wilt liever uw aanbieding via de Marketplace aanbieden, waardoor monetaire trans acties onafhankelijk van micro soft worden verwerkt.    

### <a name="sell-through-microsoft"></a>Verkopen via micro soft

Door te verkopen via micro soft biedt betere klant detectie en-overname, kan micro soft Marketplace-trans acties namens u hosten en profiteren van de wereld wijd beschik bare commerce mogelijkheden van micro soft.

#### <a name="saas-offer-requirements"></a>Vereisten voor SaaS-aanbiedingen

Als u SaaS-aanbiedingen (Software as a Service) met commerciële Marketplace op partner centrum wilt weer geven, moet aan de volgende criteria worden voldaan:

- Uw aanbieding moet [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) gebruiken voor identiteits beheer en-verificatie.
- Uw aanbieding moet [SaaS-fulfillment-api's](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) gebruiken voor integratie met Azure Marketplace.
- Zie de [inleidende hand leiding voor SaaS-aanbiedingen](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide)voor uitgebreidere vereisten.

#### <a name="saas-pricing-and-billing-options"></a>SaaS-prijzen en facturerings opties
Met SaaS-oplossingen die worden uitgevoerd in het Azure-abonnement van de uitgever, zijn de kosten van de infra structuur waarop de software wordt geïmplementeerd, inbegrepen in de licentie kosten die door klanten worden betaald. Het gebruik van de infra structuur van Azure wordt beheerd en wordt gefactureerd aan u, de partner, direct. De werkelijke gebruiks kosten voor de infra structuur zijn niet zichtbaar voor de klant. Uitgevers moeten gebruiks kosten voor Azure-infra structuur in hun software licentie prijzen bundelen. 

SaaS biedt ondersteuning voor maandelijkse of jaarlijkse facturering op basis van een vast bedrag, per gebruiker of verbruiks kosten met behulp van de service voor facturering via data limiet. De commerciële Marketplace van micro soft werkt op een agentuur model, waardoor uitgevers prijzen instellen, micro soft billt klanten en micro soft betaalt de inkomsten aan de uitgever terwijl er kosten in rekening worden gebracht.

De volgende tabel toont een voor beeld van de kosten en uitbetalingen om het model van de instantie te demonstreren.

|**De licentie kosten**|**$100 per maand**|
|:---|:---|
|Kosten voor Azure-gebruik (D1/1-core)|Rechtstreeks aan de uitgever gefactureerd, niet de klant|
|Klant wordt gefactureerd door micro soft|$100,00 per maand (uitgever moet rekening worden gehouden met de kosten voor het ontstaan of door geven van infra structuur in de licentie kosten)|

|**Micro soft-facturen**|**$100 per maand**|
|:---|:---|
|Micro soft betaalt u 80% van uw licentie kosten <br>**voor gekwalificeerde SaaS-apps betaalt micro soft 90% van uw licentie kosten*|$80,00 per maand <br>*$* 90,00 per maand *|

- In dit voor beeld wordt micro soft billt $100,00 aan de klant voor uw software licentie en betaalt $80,00 de uitgever.
- Partners die in aanmerking komen voor de **lagere kosten voor Marketplace-service** , zien een gereduceerde transactie kosten voor de SaaS-aanbiedingen van mei 2019 tot juni 2020. In dit scenario betaalt micro soft de $100,00 voor uw software licentie en wordt $90,00 op de Publisher uitgewisseld.

> [!NOTE]
> **Lagere kosten voor Marketplace-service**: voor bepaalde SaaS-aanbiedingen die u hebt gepubliceerd op onze commerciële Marketplace, verlaagt micro soft de kosten voor Marketplace-service van 20% (zoals beschreven in de overeenkomst voor micro soft Publisher) tot 10%. Om uw aanbieding in aanmerking te komen, moet u ten minste één van uw aanbiedingen hebben aangewezen door micro soft als IP-mede-verkoop gereed of IP co-sell priority.  Voor het einde van elke kalender maand moet aan de geschiktheid ten minste vijf (5) werk dagen worden voldaan om dit gereduceerde service-tarief voor de maand te ontvangen.  De lagere kosten voor Marketplace-service zijn niet van toepassing op Vm's, beheerde apps of andere producten die beschikbaar worden gesteld via onze commerciële Marketplace.  De lagere kosten voor Marketplace-service zijn alleen beschikbaar voor aanbiedingen met een licentie die door micro soft zijn verzameld tussen 1 mei 2019 en 30 juni 2020.  Na deze periode wordt de kosten voor de Marketplace-service weer gegeven in het normale bedrag. 




#### <a name="csp-program-opt-in"></a>Opt-in voor het CSP-programma
Met het programma [Cloud Solution Provider (CSP)](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers) kunnen software aanbiedingen miljoenen gekwalificeerde micro soft-klanten bereiken met minimale marketing-en verkoop investeringen.

- **Kanalen: mijn aanbod beschikbaar maken in het CSP-programma** (selectie vakje)

Als u ervoor kiest om uw aanbieding beschikbaar te maken in het CSP-programma, kunnen Cloud solution providers uw product verkopen als onderdeel van een gebundelde oplossing voor hun klanten. 

### <a name="list-through-microsoft"></a>Lijst via micro soft

Promoot uw bedrijf met micro soft door een Marketplace-vermelding te maken. Als u een lijst wilt maken met alleen uw aanbieding en niet via micro soft, betekent dit dat micro soft niet rechtstreeks deelneemt aan software licentie transacties. Er zijn geen kosten verbonden aan de trans actie en de uitgever houdt 100% van alle software licentie kosten bij die van de klant zijn verzameld. De uitgever is echter verantwoordelijk voor de ondersteuning van alle aspecten van de software licentie transactie, inclusief, maar niet beperkt tot: order verwerking, meting, facturering, facturering, betaling en incasso. 

- **Hoe wilt u potentiële klanten interactie laten doen met deze aanbieding?**

##### <a name="get-it-now-free"></a>Nu downloaden (gratis)
Bied uw aanbod aan klanten gratis aan door een geldige URL op te geven (te beginnen met *http* of *https*), waar ze toegang hebben tot uw app.  Bijvoorbeeld: `https://contoso.com/saas-app`

##### <a name="free-trial-listing"></a>Gratis proef versie (lijst)
Bied uw aanbieding aan klanten aan met een koppeling naar een gratis proef versie door een geldige URL op te geven (te beginnen met *http* of *https*), waar u een proef versie kunt verkrijgen met [één klik op verificatie met behulp van Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide#using-azure-active-directory-to-enable-trials).  Bijvoorbeeld: `https://contoso.com/trial/saas-app`. Gratis proef versies van aanbieding worden gemaakt, beheerd en geconfigureerd door uw service en er zijn geen abonnementen die door micro soft worden beheerd.

> [!NOTE]
> De tokens die uw toepassing via uw proef koppeling ontvangt, kunnen alleen worden gebruikt voor het verkrijgen van gebruikers gegevens via Azure AD voor het automatiseren van het maken van accounts in uw app. Micro soft-accounts (MSA) worden niet ondersteund voor verificatie met behulp van dit token.

##### <a name="contact-me"></a>Contact opnemen
Contact gegevens van klanten verzamelen door verbinding te maken met uw CRM-systeem (Customer Relationship Management). De klant wordt gevraagd om toestemming te krijgen om hun gegevens te delen. Deze klant gegevens, samen met de naam van de aanbieding, ID en Marketplace-bron waar ze uw aanbieding vinden, worden verzonden naar het CRM-systeem dat u hebt geconfigureerd. Zie [Connect lead management](#connect-lead-management)(Engelstalig) voor meer informatie over het configureren van uw CRM. 

## <a name="example-marketplace-offer-listing"></a>Voor beeld van een lijst met Marketplace-aanbiedingen

![Voor beeld van een Marketplace-aanbieding met notities](./media/marketplace-offer.svg)

## <a name="enable-a-test-drive"></a>Een test drive inschakelen

Een test drive is een fantastische manier om uw aanbieding aan potentiële klanten te laten presen teren door hen de mogelijkheid te geven voor ' voor u ' te kopen, wat resulteert in een verhoogde conversie en de generatie van uiterst gekwalificeerde leads. [Meer informatie over test stations.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

- **Een test drive inschakelen** (selectie vakje) 

Als u test drive inschakelt, wordt u gevraagd om een demonstratie omgeving te configureren voor klanten om een vaste periode te proberen. 

### <a name="type-of-test-drive"></a>Type test drive

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

[!INCLUDE [Connect lead management](./includes/connect-lead-management-a.md)]

#### <a name="additional-lead-management-resources"></a>Aanvullende resources voor lead beheer
- [Veelgestelde vragen over Lead beheer](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Veelvoorkomende fouten bij de configuratie van de lead](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Overzicht Lead beheer één pager](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)

Vergeet niet om te **slaan** voordat u verdergaat met de volgende sectie.

## <a name="properties"></a>Eigenschappen
Op het tabblad **Eigenschappen** wordt u gevraagd om de categorieën en industrieën te definiëren die worden gebruikt voor het groeperen van uw aanbieding op Marketplace, de juridische contracten die uw aanbieding ondersteunen en uw app-versie. 

Selecteer **Opslaan** na het volt ooien van deze velden. 

### <a name="category"></a>Category
Selecteer mini maal één (1) en een maximum van drie (3) categorieën die worden gebruikt voor het groeperen van uw aanbieding in de juiste Zoek gebieden voor Marketplace. Neem contact op met de manier waarop uw aanbod deze categorieën ondersteunt in de beschrijving van de aanbieding. 

### <a name="industry"></a>Industrie

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="app-version"></a>App-versie
Dit is een optioneel veld dat in de AppSource Marketplace wordt gebruikt om het versie nummer van uw aanbieding te identificeren. 

### <a name="standard-contract"></a>Standaardcontract

- **Standaard contract gebruiken?**

Om het aankoop proces voor klanten te vereenvoudigen en de juridische complexiteit voor software leveranciers te verminderen, biedt micro soft een standaard contract sjabloon om een trans actie in de Marketplace te vergemakkelijken. 

In plaats van aangepaste voor waarden te gebruiken, kunnen uitgevers van Azure Marketplace ervoor kiezen hun software aan te bieden onder het Standard-contract, wat klanten alleen hoeven te bevestigen en één keer te accepteren. 

Het standaard contract vindt u hier: https://go.microsoft.com/fwlink/?linkid=2041178.

#### <a name="terms-of-use"></a>Gebruiksvoorwaarden

Als uw licentie voorwaarden verschillen van het standaard contract, kunt u hier uw eigen juridische voor waarden opgeven. Deze kunnen worden ingevoerd als tekst zonder opmaak of als één URL-koppeling naar uw licentie voorwaarden.

Klanten moeten deze voor waarden accepteren voordat ze uw app kunnen proberen. 

Vergeet niet om te **slaan** voordat u verdergaat met de volgende sectie.

## <a name="offer-listing"></a>Aanbieding weer geven

Op het tabblad aanbiedings vermelding worden de talen (en markten) weer gegeven waar uw aanbieding beschikbaar is. momenteel is Engels (Verenigde Staten) de enige locatie beschikbaar. Daarnaast wordt op deze pagina de status weer gegeven van de taalspecifieke vermelding en de datum/tijd waarop deze is toegevoegd. U moet de Marketplace-Details (naam, beschrijving, zoek termen, enz.) definiëren voor elke taal/markt.

> [!NOTE]
> De inhoud van het aanbiedings aanbod (zoals beschrijving van aanbieding, documenten, scherm afbeeldingen, gebruiks voorwaarden en privacybeleid) is niet in het Engels vereist, zolang de beschrijving van het aanbod begint met de woord groep ' deze toepassing is alleen beschikbaar in [niet-Engelse taal] '. Het is ook acceptabel om een *nuttige koppelings-URL* te bieden om inhoud te bieden in een andere taal dan de versie die wordt gebruikt in de inhoud van de aanbieding.

### <a name="offer-listings"></a>Aanbiedings vermeldingen

Geef de details op die in Marketplace moeten worden weer gegeven, inclusief beschrijvingen van uw aanbieding en marketing-assets.

- **Naam** (vereist): de naam die hier is opgegeven, wordt weer gegeven als de titel van uw aanbieding op de Marketplace (s) die u hebt gekozen. De naam wordt vooraf ingevuld op basis van uw vorige **nieuwe aanbieding** .  Dit kan een handels merk zijn.  Dit mag geen emojis bevatten (tenzij ze de symbolen van het handels merk en copyright zijn) en moet beperkt zijn tot 50 tekens.
- **Samen vatting** (vereist): Geef een korte beschrijving van uw aanbieding die moet worden gebruikt in de zoek resultaten voor Marketplace-aanbiedingen. In dit veld kunnen Maxi maal 100 tekens tekst worden ingevoerd.
- **Beschrijving** (vereist): Geef een beschrijving op van uw aanbieding die moet worden weer gegeven in het overzicht van Marketplace-lijst (en). Overweeg het opnemen van een toegevoegde waarde, belang rijke voor delen, categorie-of branche koppelingen, in-app aankoop kansen, eventueel vereiste informatie en een koppeling voor meer informatie.
In dit veld kunnen Maxi maal 3.000 tekens tekst worden ingevoerd. Zie het artikel [een fantastische app-beschrijving schrijven](https://docs.microsoft.com/windows/uwp/publish/write-a-great-app-description)voor aanvullende tips.
- **Tref woorden zoeken**: Voer Maxi maal drie Zoek trefwoorden in die klanten kunnen gebruiken om uw aanbieding te vinden in Marketplace (s).
- **Aan** de slag-instructies (vereist): in dit gedeelte wordt uitgelegd hoe u uw app kunt configureren en gebruiken voor potentiële klanten.  Deze Snelstartgids kan koppelingen bevatten naar gedetailleerde online documentatie. In dit veld kunnen Maxi maal 3.000 tekens tekst worden ingevoerd. 

#### <a name="description"></a>**Beschrijving**

Dit is een verplicht veld. Items die moeten worden meegenomen in de beschrijving: 

* Beschrijf duidelijk de toegevoegde waarde van uw aanbieding in de eerste paar zinnen van uw beschrijving.  
* Houd er rekening mee dat de eerste paar zinnen kunnen worden weer gegeven in de resultaten van de zoek machine.  
* Vertrouw niet op functies en functionaliteit om uw product te verkopen. Richt u in plaats daarvan op de waarde die u levert.  
* Gebruik zo veel mogelijk branchespecifieke woorden lijst of op basis van voor delen. 

De belangrijkste onderdelen van de toegevoegde waarde moeten de volgende informatie bevatten: 

* Beschrijving van het product. 
* Het type gebruiker dat voor deel is van het product. 
* Klanten wensen of pijn dat de product adressen. 

Als u de beschrijving van uw aanbieding aantrekkelijker wilt maken, kunt u HTML-tags gebruiken om de beschrijving op te maken. 

1. Als u alinea's wilt maken, moet u `<p>` toevoegen aan de Begging van de tekst en `</p>` aan het einde toevoegen.

    **Voorbeeld**: 

    `<p>` dit is mijn eerste alinea. `</p>` <br>
    `<p>` dit is mijn tweede alinea. `</p>` <br>

    Het bovenstaande ziet er als volgt uit:

    <p> Dit is mijn eerste alinea. </p>
    <p> Dit is mijn tweede alinea. </p>

1. Als u een **lijst met opsommings tekens**wilt toevoegen aan items, plaatst u de tekst in de `<li>` tags hieronder. U kunt meer items met opsommings tekens (items tussen de `<li>` en `</li>` Tags) binnen de labels `<ul>` en `</ul>` kopiëren en plakken. Zorg ervoor dat u de `<ul></ul>`toevoegt. 

    **Voorbeeld**:

    ```
    <ul> 
        <li>add text here</li> 
        <li> add text here </li> 
        <li> add text here </li> 
    </ul> 
    ```

    Het bovenstaande ziet er als volgt uit:
    <ul> 
        <li>Voeg hier tekst toe</li> 
        <li> Voeg hier tekst toe </li> 
        <li> Voeg hier tekst toe </li> 
    </ul> 

1. Voeg `<b>` **toe aan het** begin van de tekst die u wilt vet en voeg `</b>` toe aan het einde van de tekst die u wilt vet weer gegeven. 

    **Voor beeld**: `<b>` gratis proef versie `</b>`
    
    De bovenstaande term zou ertoe leiden dat de woorden gratis proef versie vet worden weer gegeven in de beschrijving van de aanbieding op de winkel. 

    **GRATIS PROEF VERSIE**

1. Als u **regel einden** wilt toevoegen tussen uw inhoud, voegt u `<br>` toe vóór de inhoud die u op een nieuwe regel wilt starten. Als u een ruimte wilt houden en wilt controleren of inhoud op een nieuwe regel begint, voegt u `<br><br>` toe vóór de inhoud. 

    **Voorbeeld**:

    Dit is een tekst regel. `<br>` dit is een tekst regel die wordt gestart op een nieuwe regel. `<br><br>` dit is een regel waarmee twee regels worden gestart. 

    Het bovenstaande ziet er als volgt uit:

    Dit is een tekst regel. <br> Dit is een tekst regel die wordt gestart op een nieuwe regel. <br><br> Dit is een regel waarmee twee regels worden gestart. 

1. Als u de **tekst**groter wilt maken, kiest u eerst hoe groot de tekst moet zijn. Gebruik de onderstaande voor beelden. Nadat u de grootte van de tekst hebt geselecteerd, voegt u de bijbehorende `<H*></H*>`-tags toe aan het begin en het einde van de tekst. 

    **Voorbeeld**:

    `<h1>`dit is Kop 1`</h1>` <br>
    `<h2>`dit is Kop 2`</h2>` <br>
    `<h3>`dit is kop 3`</h3>` <br>
    `<h4>`dit is kop 4`</h4>` <br>
    `<h5>`dit is koptekst 5`</h5>` <br>
    `<h6>`dit is kop 6`</h6>` 

    Het bovenstaande ziet er als volgt uit:

    ![Voorbeeld koppen](./media/heading.png)

#### <a name="links"></a>Koppelingen

- **Privacybeleid** (vereist): koppeling naar het privacybeleid van uw organisatie. U bent verantwoordelijk om ervoor te zorgen dat uw app voldoet aan de wetten en voor schriften van de privacy en voor het bieden van een geldig privacybeleid
- **Software** voor het CSP-programma (optioneel): u moet een koppeling naar marketing materialen opgeven als u ervoor kiest om uw aanbieding uit te breiden naar het [CSP-programma (Cloud Solution Provider)](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers) . CSP breidt uw aanbieding uit naar een breder scala aan gekwalificeerde klanten door CSP-partners in staat te stellen om uw aanbieding te bundelen, te verhandelen en te verkopen. Deze wederverkopers moeten toegang hebben tot materialen om uw aanbieding in de handel te brengen. Zie [Go-to-Market Services](https://partner.microsoft.com/reach-customers/gtm)(Engelstalig) voor meer informatie.
- **Nuttige koppelingen** (optioneel): optionele aanvullende online documenten over uw app of gerelateerde services die worden vermeld door een **titel** en **URL**op te geven. Voeg extra nuttige koppelingen toe door op **+ een URL toevoegen**te klikken.

#### <a name="contact-information"></a>Contactgegevens

- **Contacts**: voor elke klant contactpersoon geeft u de **naam** , het **telefoon nummer**en het **e-mail** adres van de werk nemer op.  (Deze worden *niet* openbaar weer gegeven). Er is ook een **ondersteunings-URL** vereist voor de contact groep van de **ondersteuning** .  ( *Deze gegevens worden* openbaar weer gegeven).

**Ondersteunings contact** (vereist): voor algemene ondersteunings vragen.

**Technisch contact** (vereist): voor technische vragen.

**Channel Manager-contact persoon** (vereist): voor wederverkoper-vragen met betrekking tot het CSP-programma.

#### <a name="files-and-images"></a>Bestanden en installatie kopieën

- **Documenten** (vereist): Voeg gerelateerde marketing documenten toe voor uw aanbieding, in PDF-indeling, die mini maal één (1) en Maxi maal drie (3) documenten per aanbieding biedt.
- **Installatie kopieën** (optioneel): er zijn meerdere locaties waar de logo afbeeldingen van uw aanbieding kunnen worden weer gegeven op de Marketplace (s), zodat de volgende grootten worden vereist: kleine letter 48 x 48 pixels _(vereist),_ Medium: 90 x 90 pixels, groot: 216 x 216 pixels _( vereist),_ breed: 255 x 115 pixels en held: 815 x 290 pixels. Alle installatie kopieën moeten zich in hebben. PNG-indeling.
- **Scherm afbeeldingen** (vereist): Voeg scherm afbeeldingen toe die uw aanbieding aantonen. U kunt Maxi maal vijf (5) scherm opnamen toevoegen en de grootte van 1280 x 720 pixels aanpassen. Alle installatie kopieën moeten zich in hebben. PNG-indeling.
- **Video's** (optioneel): voeg links toe aan Video's die uw aanbieding aantonen. U kunt links gebruiken naar YouTube-en/of Vimeo-Video's, die samen met uw aanbieding aan klanten worden weer gegeven. U moet ook een miniatuur afbeelding van de video invoeren, met een grootte van 1280 x 720 pixels in PNG-indeling. U kunt Maxi maal vier Video's per aanbieding weer geven.

Vergeet niet om te **slaan** voordat u verdergaat met de volgende sectie.

#### <a name="additional-marketplace-listing-resources"></a>Aanvullende bronnen voor Marketplace-lijst

- [Best practices voor Marketplace-aanbiedingen](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)


## <a name="preview"></a>Preview

Op het tabblad **voor beeld** kunt u een beperkte **Preview-doel groep** definiëren voor het vrijgeven van uw aanbieding voordat u uw aanbieding Live publiceert naar het bredere publiek van de Marketplace.

> [!IMPORTANT]
> U moet **Live go** selecteren voordat uw aanbieding naar de open bare doel groep van Marketplace wordt gepubliceerd nadat u uw aanbieding in Preview hebt gecontroleerd.

- **Een preview-doel groep definiëren: Voeg per regel één AAD/MSA-account toe, samen met een optionele beschrijving.**

Voeg Maxi maal tien (10) e-mail adressen hand matig toe, of eenentwintig (20) als u een CSV-bestand uploadt voor bestaande micro soft-account (MSA) of Azure Active Directory accounts die u helpen bij het valideren van uw aanbieding voordat u live publiceert. Door deze accounts toe te voegen, definieert u een doel groep waarvoor preview-toegang tot uw aanbieding is toegestaan voordat deze wordt gepubliceerd op Marketplace ('s). Als uw aanbieding al Live is, kunt u nog steeds een preview-doel definiëren voor het testen van eventuele wijzigingen of updates voor uw aanbieding.

> [!NOTE]
> De preview-doel groep wijkt af van een persoonlijke doel groep. Een preview-doel groep is toegang tot uw aanbieding toegestaan _voordat_ Live in de Marketplace wordt gepubliceerd. U kunt er ook voor kiezen om een plan te maken en dit alleen beschikbaar te maken voor een privé doel groep. Op het tabblad **plan vermelding** kunt u een privé-doel groep definiëren met het selectie vakje **Dit is een privé plan** . U kunt vervolgens een persoonlijke doel groep van Maxi maal 20.000 klanten definiëren met behulp van Azure-Tenant-Id's.

## <a name="technical-configuration"></a>Technische configuratie

Het tabblad **technische configuratie** definieert de technische details (URL-pad, webhook, Tenant-id en app-id) die wordt gebruikt om verbinding te maken met uw aanbieding. Met deze verbinding kan ons uw aanbieding voor de eind klant inrichten als deze ervoor kiezen deze te verkrijgen. Diagrammen die het gebruik van de verzamelde velden beschrijven, zijn beschikbaar in de documentatie voor [SaaS-fulfillment-api's](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2).

- **URL van de landings pagina** (vereist): Definieer de site-URL waar klanten op worden gegrond na het verkrijgen van uw aanbieding van de Marketplace. Deze URL is het eind punt dat een token ontvangt wanneer een klant wordt doorgestuurd naar de pagina. Dit token kan worden uitgewisseld voor inrichtings gegevens met behulp van de oplossing in de fulfillment-Api's. Deze gegevens en andere personen die u verzamelt, kunnen worden gebruikt als onderdeel van een klant-interactieve webpagina die in uw ervaring is gemaakt om de registratie te volt ooien en hun aankoop te activeren.

- **Verbindings-webhook** (vereist): voor alle asynchrone gebeurtenissen die micro soft namens de klant moet verzenden (bijvoorbeeld: Saas-abonnement is ongeldig), moet u een verbindings-webhook opgeven. Als u nog geen webhooksysteem hebt, is de eenvoudigste configuratie een HTTP-eindpunt logische app waarmee wordt geluisterd naar gebeurtenissen die erop worden geplaatst en deze vervolgens op de juiste wijze afhandelen (bijvoorbeeld https:\//prod-1westus.logic.azure.com:443/work). Zie [werk stromen aanroepen, activeren of nesten met HTTP-eind punten in Logic apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint)voor meer informatie.

- **Azure AD-Tenant-id** (vereist): in azure Portal moet u [een Azure Active Directory (AD)-app maken](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) zodat we kunnen valideren dat de verbinding tussen de twee services zich achter een geverifieerde communicatie bevindt. Als u de [Tenant-id](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)wilt vinden, gaat u naar uw Azure Active Directory en selecteert u **Eigenschappen**. vervolgens zoekt u naar de weer gegeven **Directory-id** (bijvoorbeeld 50c464d3-4930-494c-963c-1e951d15360e).

- **Azure AD-App-ID** (vereist): u hebt ook uw [toepassings-id](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) en een verificatie sleutel nodig. Als u deze waarden wilt ophalen, gaat u naar uw Azure Active Directory en selecteert u **app-registraties**en vervolgens zoekt u naar het **toepassings-id-** nummer dat wordt weer gegeven (bijvoorbeeld 50c464d3-4930-494c-963c-1e951d15360e). Ga naar **instellingen** en selecteer **sleutels**om de verificatie sleutel te vinden. U moet een beschrijving en een duur opgeven, en vervolgens een numerieke waarde.

 Houd er rekening mee dat de Azure-toepassings-ID is gekoppeld aan uw uitgevers-ID. Zorg er dus voor dat dezelfde toepassings-ID wordt gebruikt in al uw aanbiedingen.

## <a name="plan-overview"></a>Overzicht van plan

Op het tabblad Overzicht van het **abonnement** kunt u een verscheidenheid aan plan opties bieden binnen dezelfde aanbieding. Deze plannen (ook wel Sku's genoemd) kunnen verschillen qua versie, verdiensten maximaliseren of service lagen. U moet ten minste één abonnement instellen om uw aanbieding in Marketplace te verkopen.

Zodra u een abonnement hebt gemaakt, ziet u de namen, Id's, prijs modellen, Beschik baarheid (openbaar of privé), de huidige publicatie status en eventuele beschik bare acties.

**Acties** die beschikbaar zijn in het **plan overzicht** variëren afhankelijk van de huidige status van uw abonnement en kunnen het volgende omvatten:

- Als de plan status **concept** is-concept verwijderen
- Als de plan status **Live** -stop verkoop plan is of persoonlijke doel groep synchroniseren

**Nieuw plan maken** (mini maal één abonnement voor degenen die via micro soft willen verkopen)

- **Plan-id:** Maak een unieke plan-ID voor elk abonnement in deze aanbieding. Deze ID is zichtbaar voor klanten in de product-URL en Azure Resource Manager sjablonen (indien van toepassing). Gebruik alleen kleine letters, alfanumerieke tekens, streepjes of onderstrepingen. Voor deze abonnement-ID zijn Maxi maal 50 tekens toegestaan. Houd er rekening mee dat de ID niet kan worden gewijzigd nadat het maken is geselecteerd.
- **Naam van abonnement:** Klanten zien deze naam wanneer ze bepalen welk abonnement binnen uw aanbieding moet worden geselecteerd. Maak in deze aanbieding een unieke aanbiedings naam voor elk abonnement. De naam van het abonnement wordt gebruikt om software-abonnementen te onderscheiden die deel kunnen uitmaken van dezelfde aanbieding (bijvoorbeeld Naam van aanbieding: Windows Server; plannen: Windows Server 2016, Windows Server 2019).

### <a name="plan-listing"></a>Aanbieding plannen

Op het tabblad **plan vermelding** worden de talen (en de markten) weer gegeven waar uw abonnement beschikbaar is. momenteel is engels (Verenigde Staten) de enige locatie beschikbaar. Daarnaast wordt op deze pagina de status weer gegeven van de taalspecifieke vermelding en de datum/tijd waarop deze is toegevoegd. U moet de Marketplace-Details (naam, beschrijving, zoek termen, enz.) definiëren voor elke taal/markt.

#### <a name="plan-listing-details"></a>Details van plan vermelding

Als u een van de plan talen selecteert, worden de gegevens van het **plan vermelding** weer gegeven, inclusief **naam** en **Beschrijving.**

- **Naam**: vooraf ingevuld op basis van uw preview **nieuwe plan** vermelding en wordt weer gegeven als de titel van het software-abonnement van uw aanbieding dat wordt weer gegeven in de Marketplace.
- **Beschrijving:** Deze beschrijving is een kans om uit te leggen wat dit software plan uniek is en eventuele verschillen tussen andere software plannen binnen uw aanbieding. Mag Maxi maal 500 tekens bevatten.

Selecteer **Opslaan** na het volt ooien van deze velden.

#### <a name="plan-pricing-and-availability"></a>Prijzen en beschik baarheid plannen

Op het tabblad **prijzen en beschik baarheid** kunt u de markten configureren waarop dit plan beschikbaar is, het gewenste verdiensten maximaliseren model, de prijs en de facturerings termijn. Daarnaast kunt u aangeven of het plan zichtbaar moet worden voor iedereen of alleen voor specifieke klanten (een privé-publiek).

##### <a name="enabling-free-trials"></a>Gratis proef versies inschakelen

Met SaaS-aanbiedingen via de commerciële Marketplace kunt u een gratis proef versie van één maand bieden bij de verkoop via micro soft. Voor alle facturerings modellen en voor waarden behalve plannen met een Data limiet worden gratis proef versies ondersteund. Met deze optie kunnen klanten een lage barrière hebben om een maand gratis toegang te geven.  Als u ervoor kiest een gratis proef versie in te scha kelen voor abonnementen binnen uw aanbieding, kan de klant niet converteren naar een betaald abonnement vóór het einde van de eerste periode van één maand.  Gedurende deze periode kunnen klanten die uw aanbieding kopen, een van de ondersteunde abonnementen proberen waarvoor de gratis proef versie is ingeschakeld en ertussen worden geconverteerd.  De conversie naar een betaald abonnement wordt automatisch uitgevoerd aan het einde van de termijn.

>[!Note]
>Als de klant kiest om te converteren naar een plan zonder gratis proef versies, wordt de conversie uitgevoerd, maar de gratis proef versie gaat onmiddellijk verloren.  Zodra een klant begint met de betaling voor een abonnement, kunnen ze niet opnieuw een gratis proef versie ontvangen voor dezelfde licentie, zelfs niet als ze zijn geconverteerd naar een SKU die ondersteuning biedt voor gratis proef versies.

De mogelijkheid om een gratis proef versie te configureren, is beschikbaar voor elk abonnement in uw aanbieding. Ga naar de prijs en beschik baarheid voor elke aanbieding en schakel het selectie vakje in om een proef versie van één maand toe te staan.

![Selectie vakje voor een gratis proef versie van één maand](./media/free-trial-enable.png)

>[!Note]
>Zodra uw aanbieding is gepubliceerd met een gratis proef versie, kan deze niet worden uitgeschakeld voor dat abonnement. Zorg ervoor dat deze instelling juist is voor de eerste publicatie om te voor komen dat u het abonnement opnieuw moet maken.

Als u informatie wilt verkrijgen over klant abonnementen die momenteel deel nemen aan een gratis proef versie, gebruikt u de nieuwe API-eigenschap `isFreeTrial`, die wordt gemarkeerd als waar of onwaar. Zie de [API voor SaaS Get-abonnementen](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2#get-subscription) voor meer informatie.

>[!Note]
>Gratis proef versies worden niet ondersteund voor abonnementen die gebruikmaken van de Marketplace-meter service.

#### <a name="markets"></a>Landen

- **Markten bewerken** (optioneel)

Elk plan moet beschikbaar zijn op ten minste één markt. Schakel het selectie vakje in voor elke markt locatie waar u dit plan beschikbaar wilt maken. Een zoekvak en een knop voor het selecteren van de landen ' BTW geremitteerd ', waarin micro soft namens u de omzet en het gebruik van BTW verdeelt, zijn opgenomen om u te helpen. 


Als u al prijzen voor uw abonnement hebt ingesteld in Verenigde Staten dollars (USD) en een andere markt locatie toevoegt, wordt de prijs voor de nieuwe markt berekend op basis van de huidige wissel koersen. U moet de prijs voor elke markt altijd controleren voordat u deze publiceert. U kunt de prijzen controleren met behulp van de koppeling export prijzen (XLSX) nadat u uw wijzigingen hebt opgeslagen.

#### <a name="pricing"></a>Prijzen

- **Prijs model**: vast tarief of Seat gebaseerd

**Vast aantal:** Schakel toegang tot uw aanbieding in met een enkelvoudige prijs per maand of per jaar. Dit wordt soms ook wel site-gebaseerde prijzen genoemd. Met dit prijs model kunt u optioneel plannen met gecontroleerde licenties definiëren die gebruikmaken van de API voor de Marketplace-meter service om klanten in rekening te brengen op basis van niet-standaard eenheden.  Zie voor meer informatie over het gebruik van facturen [met data limieten met behulp van de Marketplace-meter service](./saas-metered-billing.md).

**Per gebruiker:** U kunt toegang tot uw aanbieding bieden met de prijs op basis van het aantal gebruikers dat toegang heeft tot de aanbieding of voor het innemen van seats. Met dit op gebruikers gebaseerd model kunt u het minimum en maximum aantal gebruikers instellen dat is toegestaan op basis van de prijs. Op deze manier kunnen verschillende prijs punten worden geconfigureerd op basis van het aantal gebruikers door het configureren van meerdere plannen.  Deze velden zijn optioneel. Als deze optie niet is geselecteerd, wordt het aantal gebruikers geïnterpreteerd als een limiet van Mini maal 1 en Maxi maal zo lang het systeem kan ondersteunen. Deze velden kunnen worden bewerkt als onderdeel van een update voor uw abonnement.

Na publicatie kan de prijs model keuze van facturering niet worden gewijzigd. Daarnaast moeten alle abonnementen voor hetzelfde aanbod hetzelfde prijs model delen.

- **Facturerings termijn**: maandelijks of jaarlijks

Selecteer de frequentie waarmee klanten de vermelde prijs moeten betalen. Er moet mini maal één maand of jaar worden opgegeven, of beide opties kunnen aan klanten beschikbaar worden gesteld.

- **Prijs**: USD per maand of USD per jaar

Prijzen die in de lokale valuta zijn ingesteld (USD = Verenigde Staten dollar), worden omgezet in de lokale valuta van alle geselecteerde markten met de huidige wissel koersen die beschikbaar zijn tijdens de installatie. Valideer deze prijzen vóór de publicatie door het prijs werk blad te exporteren en de prijs op elke markt te bekijken. Als u aangepaste prijzen wilt instellen op een afzonderlijke markt, wijzigt en importeert u de prijs informatie in het werk blad. U bent verantwoordelijk voor het valideren van deze prijzen en de eigenaar van deze instellingen.
*\*U moet uw prijs wijzigingen eerst opslaan om het exporteren van prijs gegevens in te scha kelen.*

Controleer uw prijzen zorgvuldig voordat u deze publiceert. er zijn enkele beperkingen voor wat er kan worden gewijzigd nadat een plan is gepubliceerd:

- Zodra een plan is gepubliceerd, kan het prijs model niet worden gewijzigd.
- Zodra een facturerings termijn voor een abonnement is gepubliceerd, kan deze later niet meer worden verwijderd.
- Zodra een prijs voor een markt in uw abonnement wordt gepubliceerd, kunt u deze later niet meer wijzigen.

### <a name="plan-audience"></a>Doel groep plannen

U kunt elk plan zodanig configureren dat het zichtbaar is voor iedereen of alleen voor een specifieke doel groep van uw keuze. U kunt lidmaatschap van deze beperkte doel groep toewijzen met behulp van Azure AD-Tenant-Id's.

#### <a name="privacy"></a>Privacy

- **Dit is een privé-abonnement** (optioneel selectie vakje)

Schakel dit selectie vakje in om uw abonnement privé en zichtbaar te maken voor de beperkte doel groep van uw keuze. Zodra het is gepubliceerd als een persoonlijk abonnement, kunt u de doel groep bijwerken of ervoor kiezen om het plan voor iedereen beschikbaar te stellen. Zodra een plan wordt gepubliceerd als zichtbaar voor iedereen, moet het zijn zichtbaar voor iedereen. (Het plan kan niet opnieuw worden geconfigureerd als een privé-abonnement).

- **Beperkte doel groep (Tenant-Id's)**

Wijs de doel groep toe die toegang heeft tot dit privé-abonnement. Toegang wordt toegewezen met behulp van Tenant-Id's met de optie om een beschrijving op te geven van elke Tenant-ID die is toegewezen. U kunt Maxi maal 10 Tenant-Id's toevoegen, of de Tenant-Id's van 20.000-klanten als u een CSV-werk blad bestand importeert.

Een Tenant is een representatie van een organisatie met een ID die wordt weer gegeven als een GUID (Globally Unique Identifier, een 128-bits geheel getal dat wordt gebruikt voor het identificeren van resources). Een tenant is een toegewezen exemplaar van Azure AD dat een organisatie of app-ontwikkelaar ontvangt wanneer deze een relatie start met Microsoft, door zich bijvoorbeeld aan te melden voor Azure, Microsoft Intune of Microsoft 365. Elke Azure AD-tenant is uniek en werkt afzonderlijk van andere Azure AD-tenants. Als u de Tenant wilt controleren, meldt u zich aan bij de Azure Portal met het account dat u wilt gebruiken voor het beheren van uw toepassing. Als u een tenant hebt, wordt u automatisch aangemeld en ziet u de naam van de tenant direct onder de accountnaam. Houd de muisaanwijzer op uw accountnaam rechtsboven in Azure Portal om uw naam, e-mailadres, directory en tenant-id (een GUID), en uw domein te zien. Als uw account is gekoppeld aan meerdere tenants, kunt u de accountnaam selecteren om een menu te openen waarmee u kunt schakelen tussen de tenants. Elke tenant heeft zijn eigen tenant-id. U kunt ook de Tenant-ID van uw organisatie opzoeken met behulp van een domein naam-URL op: [https://www.whatismytenantid.com](https://www.whatismytenantid.com).

Hoewel SaaS voorziet in Tenant-Id's voor het definiëren van een persoonlijke doel groep, kunnen andere aanbiedings typen gebruikmaken van Azure-abonnement-Id's (die ook worden weer gegeven als GUID'S).

> [!NOTE]
> De persoonlijke doel groep (of een beperkt publiek) wijkt af van een preview-doel groep. Op het tabblad **[Preview](#preview)** kunt u een voor beeld van een doel groep definiëren. Een preview-doel groep is toegang tot uw aanbieding toegestaan *voordat* de aanbieding Live op Marketplace wordt gepubliceerd. Hoewel de aanduiding van een privé-doel groep alleen van toepassing is op een specifiek abonnement, kan de preview-doel groep alle plannen (privé of niet) weer geven, maar alleen tijdens de beperkte preview-periode terwijl het plan wordt getest en gevalideerd.

## <a name="example-list-of-plans-within-a-marketplace-offer"></a>Voor beeld van een lijst met abonnementen in een Marketplace-aanbieding

![Voor beeld Marketplace-plan lijst met notities](./media/marketplace-plan.svg)

## <a name="test-drive"></a>Test drive

[!INCLUDE [Test drive content](./includes/commercial-marketplace-test-drive.md)]

## <a name="publish"></a>Publiceren

#### <a name="submit-offer-to-preview"></a>Aanbieding verzenden naar Preview

Zodra u alle vereiste delen van de aanbieding hebt voltooid, selecteert u **publiceren** in de rechter bovenhoek van de portal. U wordt opnieuw omgeleid naar de pagina **controleren en publiceren** . 

Als dit de eerste keer is dat u deze aanbieding publiceert, kunt u het volgende doen:

- Bekijk de voltooiings status voor elke sectie van de aanbieding.
    - *Niet gestart* : de sectie is niet gerakend en moet worden voltooid.
    - *Onvolledig* : de sectie bevat fouten die moeten worden hersteld of waarvoor meer informatie moet worden verstrekt. Ga terug naar de sectie (s) en werk deze bij.
    - *Voltooid* : de sectie is voltooid, alle vereiste gegevens zijn opgegeven en er zijn geen fouten. Alle secties van de aanbieding moeten een volledige status hebben voordat u de aanbieding kunt indienen.
- Geef test instructies op het certificerings team om ervoor te zorgen dat uw app correct wordt getest, naast eventuele aanvullende notities die nuttig zijn voor de uitleg van uw app.
- Verzend de aanbieding voor publicatie door **verzenden**te selecteren. We sturen u een e-mail om u te laten weten wanneer een preview-versie van de aanbieding beschikbaar is om te controleren en goed te keuren. Ga terug naar het partner centrum en selecteer **Go-Live** voor de aanbieding om uw aanbieding te publiceren naar het open bare (of als u een privé-aanbieding naar de persoonlijke doel groep hebt).

## <a name="next-steps"></a>Volgende stappen

- [Een bestaande aanbieding bijwerken in de commerciële Marketplace](./update-existing-offer.md)
