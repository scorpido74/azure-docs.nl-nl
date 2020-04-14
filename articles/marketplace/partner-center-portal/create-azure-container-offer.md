---
title: Een Azure-containeraanbieding maken in partnercentrum - Azure Marketplace
description: In dit artikel wordt beschreven hoe u een containeraanbieding voor Azure Marketplace maakt en publiceert.
author: mingshen
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: b96fe12ccc0292bb5f689fbecabd53d2af54846e
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266316"
---
# <a name="create-an-azure-container-offer"></a>Een Azure Container-aanbieding maken

> [!IMPORTANT]
> We verplaatsen het beheer van uw Azure Container-aanbiedingen van Cloud Partner Portal naar Partner Center. Totdat uw aanbiedingen zijn gemigreerd, volgt u de instructies in [Containers](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-containers-offer) for Cloud Partner Portal om uw aanbiedingen te beheren.

In dit artikel wordt beschreven hoe u een containeraanbieding voor Azure Marketplace maakt en publiceert. Maak [voordat u begint een Commercieel Marktplaats-account aan in het Partnercentrum.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)

## <a name="create-a-new-offer"></a>Een nieuwe aanbieding maken

1. Log in bij [Partnercenter](https://partner.microsoft.com/dashboard/home)en **selecteer**dashboard in het bovenste menu .
2. Selecteer in het linkermenu **Commerciële marktplaats**en **vervolgens Overzicht**.
3. Selecteer **op** de pagina Overzicht de optie **+ Nieuwe aanbieding,** vervolgens **Azure Container**. Het dialoogvenster **Nieuwe aanbieding** wordt weergegeven.

:::image type="content" source="media/azure-create-container-offer-images/azure-create-new-container.png" alt-text="Illustreerde de overzichtspagina in partnercentrum. De nieuwe aanbiedingsknop en consultingserviceaanbieding worden gemarkeerd.":::

> [!TIP]
> Nadat een aanbieding is gepubliceerd, worden bewerkingen die zijn aangebracht in het Partnercentrum alleen weergegeven in etalages nadat de aanbieding opnieuw is gepubliceerd. Zorg ervoor dat u altijd opnieuw publiceert nadat u wijzigingen hebt aangebracht.

### <a name="offer-id-and-alias"></a>Aanbiedings-ID en alias

Voer een **aanbiedings-id in**. Dit is een unieke id voor elke aanbieding in uw account.

- Deze id kan worden gezien door klanten in het webadres voor de marketplace-aanbieding en Azure Resource Manager-sjablonen, indien van toepassing.
- Gebruik alleen kleine letters en cijfers. Het kan koppeltekens en underscores bevatten, maar geen spaties, en is beperkt tot 50 tekens. Als u bijvoorbeeld **test-aanbieding-1**invoert, wordt `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`het webadres van de aanbieding .
- De aanbiedings-id kan niet worden gewijzigd nadat u **Maken**hebt geselecteerd.

**Voer een** **aanbiedingsalias**in . Dit is de naam die wordt gebruikt om te verwijzen naar de aanbieding in Partner Center.

- Deze naam wordt niet gebruikt in de markt en verschilt van de aanbiedingsnaam en andere waarden die aan klanten worden weergegeven.
- Dit kan niet worden gewijzigd nadat u Maken hebt **geselecteerd.**

Selecteer **Maken** voordat u verdergaat.

## <a name="offer-overview"></a>Aanbiedingsoverzicht

De **overzichtspagina Aanbieding** toont een visuele weergave van de stappen die nodig zijn om deze aanbieding te publiceren (zowel voltooid als aanstaande) en hoe lang elke stap moet duren om te voltooien.

Deze pagina toont verschillende links op basis van de huidige status van de aanbieding. Bijvoorbeeld:

- Als de aanbieding een concept is - [Conceptvoorstel verwijderen](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- Als het aanbod live is - [Stop met de verkoop van het aanbod](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- Als de aanbieding in preview is - [Go-live](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- Als u de afmelding van uitgevers nog niet hebt voltooid, [kunt u publiceren annuleren](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="offer-setup"></a>Instelling voor aanbieding

Volg deze stappen om uw aanbieding in te stellen.

### <a name="connect-lead-management--optional"></a>Leadbeheer verbinden – optioneel

Wanneer u uw aanbieding op de markt publiceert met Partner Center, u deze koppelen aan uw CRM-systeem (Customer Relationship Management). Hiermee u klantcontactgegevens ontvangen zodra iemand interesse toont in of uw product gebruikt.

1. **Selecteer een leadbestemming waar u wilt dat we klantleads verzenden.** Partnercenter ondersteunt de volgende CRM-systemen:

- [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) voor customer engagement
- [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
- [SalesForce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

> [!NOTE]
> Als uw CRM-systeem hierboven niet wordt vermeld, gebruikt u [Azure Table](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) of [Https Endpoint](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) om klantgegevens op te slaan en exporteert u de gegevens naar uw CRM-systeem.

2. Verbind uw aanbieding met de hoofdbestemming wanneer u publiceert in partnercentrum.
3. Controleer of de verbinding met de hoofdbestemming correct is geconfigureerd. Nadat u deze hebt gepubliceerd in het Partnercenter, valideren we de verbinding en sturen we u een testlead. Terwijl u een voorbeeld van de aanbieding bekijkt voordat deze live gaat, u ook uw leadverbinding testen door te proberen het aanbod zelf te kopen in de preview-omgeving.
4. Zorg ervoor dat de verbinding met de hoofdbestemming up-to-date blijft, zodat u geen leads verliest.

Hier volgen enkele aanvullende bronnen voor leadbeheer:

- [Overzicht van leadbeheer](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [Veelgestelde vragen over leadmanagement](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Algemene leadconfiguratiefouten](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Overzicht van leadbeheer](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (Zorg ervoor dat uw pop-upblokkering is uitgeschakeld)

Selecteer **Concept opslaan** voordat u doorgaat naar de volgende sectie, Eigenschappen.

### <a name="properties"></a>Eigenschappen

Op deze pagina u de categorieën definiëren die worden gebruikt om uw aanbieding op de marktplaats te groeperen en de juridische contracten die uw aanbod ondersteunen.

#### <a name="category"></a>Categorie

Selecteer minimaal één en maximaal vijf categorieën. Deze categorieën worden gebruikt om uw aanbieding in de juiste zoekgebieden op marktplaats te plaatsen en worden weergegeven op de pagina met aanbiedingsgegevens. Leg in de aanbiedingsbeschrijving uit hoe uw aanbieding deze categorieën ondersteunt. Containers worden weergegeven onder **Containers** en vervolgens de categorie **Containerafbeeldingen.**

#### <a name="legal"></a>Juridisch

U moet algemene voorwaarden voor de aanbieding opgeven. Er zijn twee opties:

- Gebruik het standaardcontract voor de commerciële marktplaats van Microsoft.
- Geef uw eigen algemene voorwaarden.

##### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Standaardcontract voor de commerciële marktplaats van Microsoft

Wij bieden een standaard contract sjabloon om te helpen vergemakkelijken transacties in de commerciële markt. U ervoor kiezen om uw oplossing aan te bieden in het kader van het standaardcontract, dat klanten slechts één keer hoeven te controleren en accepteren. Dit is een goede optie als u geen aangepaste algemene voorwaarden wilt maken.

Zie [Standaardcontract voor de commerciële marktplaats van Microsoft voor](https://docs.microsoft.com/azure/marketplace/standard-contract)meer informatie over het standaardcontract. U ook de [standaardcontractPDF](https://go.microsoft.com/fwlink/?linkid=2041178) downloaden (zorg ervoor dat uw pop-upblokkering is uitgeschakeld).

Als u het standaardcontract wilt gebruiken, schakelt u het selectievakje **Het standaardcontract voor de commerciële marktplaats van Microsoft gebruiken** in en klikt u op **Accepteren**.

> [!NOTE]
> Nadat u een aanbieding hebt gepubliceerd met behulp van het standaardcontract voor microsoft-commerciële marktplaats, u uw eigen aangepaste algemene voorwaarden niet gebruiken. Bied uw oplossing aan onder het standaardcontract of onder uw eigen voorwaarden.

:::image type="content" source="media/azure-create-container-offer-images/azure-create-1-standard-contract.png" alt-text="Illustreert het selectievakje Het standaardcontract voor de commerciële marktplaats van Microsoft gebruiken.":::

##### <a name="your-own-terms-and-conditions"></a>Uw eigen algemene voorwaarden

Als u uw eigen aangepaste algemene voorwaarden wilt bieden, voert u deze in het vak **Algemene voorwaarden** in. U in dit vak een onbeperkt aantal tekens van tekst invoeren. Klanten moeten deze voorwaarden accepteren voordat ze uw aanbieding kunnen proberen.

Selecteer **Concept opslaan** voordat u doorgaat naar de volgende sectie, Aanbieding smaken.

## <a name="offer-listing"></a>Aanbieding aanbieden

Op deze pagina u de aanbiedingsgegevens definiëren die worden weergegeven in de commerciële marktplaats. Dit omvat de naam, beschrijving en afbeeldingen van de aanbieding.

> [!NOTE]
> Aanbiedingsgegevens hoeven niet in het Engels te zijn als de aanbiedingsbeschrijving begint met de woordgroep,,Deze toepassing is alleen beschikbaar in [niet-Engelse taal]. Het is ook goed om een nuttige koppeling te bieden om inhoud aan te bieden in een taal die verschilt van de taal die wordt gebruikt in de aanbiedingsgegevens van de aanbieding.

### <a name="name"></a>Name

De naam die u hier invoert, wordt weergegeven als de titel van uw aanbieding. Dit veld is vooraf gevuld met de tekst die u hebt ingevoerd in het vak **Alias aanbieden** toen u de aanbieding hebt gemaakt. U kunt deze naam later wijzigen.

De naam:

- Kan een handelsmerk zijn (en u handelsmerk- en auteursrechtsymbolen bevatten).
- Kan niet langer dan 50 tekens zijn.
- Kan geen emoji's bevatten.

### <a name="search-results-summary"></a>Overzicht van zoekresultaten

Een korte beschrijving van uw aanbod. Dit kan maximaal 100 tekens lang zijn en wordt gebruikt in de zoekresultaten van marktplaatsen.

### <a name="long-summary"></a>Lange samenvatting

Een meer gedetailleerde beschrijving van uw aanbod. Dit kan maximaal 256 tekens lang zijn en wordt gebruikt in de zoekresultaten van marktplaatsen.

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
  - Het type gebruiker dat profiteert van het aanbod
  - De behoeften van de klant of geeft de aanbiedingsadressen uit.
- Vergeet niet dat de eerste paar zinnen kunnen worden weergegeven in de zoekresultaten.
- Vertrouw niet op functies en functionaliteit om uw product te verkopen. Richt u in plaats daarvan op de waarde die uw aanbieding biedt.
- Probeer branchespecifieke woordenschat of op voordelen gebaseerde formuleringen te gebruiken.

Als u de **beschrijving** van uw aanbieding aantrekkelijker wilt maken, gebruikt u de teksteditor met rijke tekst om uw beschrijving op te maken. met nummering, kogels, vet, cursief en inspringingen om uw beschrijving leesbaarder te maken.

:::image type="content" source="media/text-editor2.png" alt-text="Illustreert de rijke teksteditor." border="false" :::

- Gebruik deze vervolgkeuzelijst om een alineastijl op tekst toe te passen.

    :::image type="content" source="media/text-editor3.png" alt-text="Hiermee wordt het besturingselement tekststijl geïllustreerd in de teksteditor met rijke tekst." border="false":::

- Gebruik deze pictogrammen om nummering of opsommingstekens toe te passen op tekst.

     :::image type="content" source="media/text-editor4.png" alt-text="Hiermee worden de besturingselementen met opsommingstekens en nummerlijst weergegeven in de rich text editor." border="false":::

- Gebruik deze pictogrammen om inspringing toe te voegen aan of te verwijderen aan of uit tekst.

    :::image type="content" source="media/text-editor5.png" alt-text="Hiermee wordt de inspringingsbesturingselementen in de rich text editor weergegeven." border="false":::

#### <a name="privacy-policy-link"></a>Koppeling Privacybeleid

Voer het webadres van het privacybeleid van uw organisatie in. U bent verantwoordelijk om ervoor te zorgen dat uw aanbod voldoet aan de privacywet- en regelgeving. Je bent ook verantwoordelijk voor het plaatsen van een geldig privacybeleid op je website.

#### <a name="useful-links"></a>Handige koppelingen

Geef aanvullende online documenten over uw aanbod. U maximaal 25 links toevoegen. Als u een koppeling wilt toevoegen, selecteert u **+ Een koppeling toevoegen** en voltooit u de volgende velden:

- **Titel** : klanten zien dit op de pagina met details van uw aanbieding.
- **Link (URL)** – Voer een koppeling in voor klanten om uw online document te bekijken. De koppeling moet beginnen met http:// of https://.

### <a name="contact-information"></a>Contactgegevens

U moet de naam, e-mail en het telefoonnummer opgeven voor een **contactpersoon voor ondersteuning** en een contactpersoon voor **engineering.** Deze informatie wordt niet aan klanten weergegeven, maar is wel beschikbaar voor Microsoft. Het kan ook worden verstrekt aan CSP-partners (Cloud Solution Provider).

- Ondersteuningscontact (vereist): voor algemene ondersteuningsvragen.
- Technische contactpersoon (vereist): Voor technische vragen en certificeringsproblemen.
- CSP-programmacontact (optioneel): voor resellervragen met betrekking tot het CSP-programma.

Geef in de sectie **Contactpersoon voor ondersteuning** de **ondersteuningswebsite** op waar partners ondersteuning voor uw aanbieding kunnen vinden op basis van de vraag of de aanbieding beschikbaar is in wereldwijde Azure, Azure Government of beide.

Geef in de **sectie Contact van het CSP-programma** de link **(CSP Program Marketing Materials)** op waar CSP-partners marketingmateriaal voor uw aanbieding kunnen vinden.

#### <a name="additional-marketplace-listing-resources"></a>Aanvullende bronnen voor marktplaatsvermeldingen

Zie Aanbevolen [aanbiedingsaanbiedingen](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices) aanbieden voor meer informatie over het maken van aanbiedingen

### <a name="marketplace-images"></a>Marketplace-afbeeldingen

Geef logo's en afbeeldingen om te gebruiken met uw aanbod. Alle afbeeldingen moeten in PNG-formaat zijn. Wazige afbeeldingen worden afgewezen.

#### <a name="store-logos"></a>Winkellogo's

 Geef PNG-bestanden op van het logo van uw aanbieding in elk van de volgende vier pixelformaten:

- **Klein** (48 x 48)
- **Medium** (90 X 90)
- **Groot** (216 x 216)
- **Breed** (255 x 115)

Alle vier de logo's zijn vereist en worden gebruikt op verschillende plaatsen in de marktplaats vermelding.

#### <a name="screenshots-optional"></a>Schermafbeeldingen (optioneel)

Voeg maximaal vijf screenshots toe die laten zien hoe uw aanbieding werkt. Elk moet 1280 x 720 pixels groot en in PNG-formaat hebben.

#### <a name="videos-optional"></a>Video's (optioneel)

Voeg maximaal vijf video's toe die je aanbod demonstreren. Voer de naam van de video, het webadres en een PNG-miniatuurafbeelding van de video in met een grootte van 1280 x 720 pixels in.

#### <a name="offer-examples"></a>Voorbeelden van aanbieding

In de volgende voorbeelden ziet u hoe de aanbiedingsvelden op verschillende plaatsen van de aanbieding worden weergegeven.

Dit toont de pagina **Aanbiedingsvermelding** in Azure Marketplace:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-6-offer-listing-mkt-plc.png" alt-text="Hiermee wordt de aanbiedingspagina in Azure Marketplace weergegeven." :::

Hier worden zoekresultaten weergegeven in Azure Marketplace:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-7-search-results-mkt-plc.png" alt-text="Hiermee worden de zoekresultaten in Azure Marketplace geïllustreerd.":::

Dit toont de pagina **Aanbiedingsvermelding** in Azure-portal:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-8-offer-listing-portal.png" alt-text="Hiermee wordt de pagina Aanbiedingsvermelding in Azure-portal weergegeven.":::

Dit toont zoekresultaten in Azure-portal:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-9-search-results-portal.png" alt-text="Hiermee worden de zoekresultaten in azure-portal geïllustreerd.":::

## <a name="preview"></a>Preview

Op het tabblad Voorbeeld kun je een beperkte **preview-doelgroep** kiezen voor het valideren van je aanbieding voordat je het live publiceert.

> [!IMPORTANT]
> Nadat u uw aanbieding in **Voorbeeld hebt**bekeken, moet u **Live gaan** selecteren om uw aanbieding openbaar te maken.

Geef uw voorbeelddoelgroep op met AZURE-guids voor azure-abonnement- id's, samen met een optionele beschrijving voor elk. Geen van deze velden kan worden gezien door klanten.

> [!NOTE]
> U uw Azure-abonnements-id vinden op de pagina Abonnementen in azure-portal.

Voeg ten minste één Azure-abonnements-id toe, afzonderlijk (maximaal 10) of door een CSV-bestand te uploaden (maximaal 100). Door deze abonnements-id's toe te voegen, bepaalt u wie een voorbeeld van uw aanbieding kan bekijken voordat deze live wordt gepubliceerd. Als je aanbieding al live is, kun je een preview-doelgroep kiezen om wijzigingen of updates van je aanbieding te testen.

> [!NOTE]
> De preview-doelgroep verschilt van een privépubliek. Een **preview-doelgroep** kan alle aanbiedingsplannen zien en bevestigen voordat ze live zijn op de marktplaats, inclusief die welke alleen worden gepubliceerd voor een **privépubliek** (ingesteld op het tabblad Beschikbaarheid).

Selecteer **Concept opslaan** voordat u verdergaat.

### <a name="plan-overview"></a>Overzicht van plannen

Met dit tabblad u verschillende abonnementsopties binnen dezelfde aanbieding aanbieden. Deze plannen werden eerder aangeduid als SKU's, of voorraad houden eenheden. Plannen kunnen verschillen in termen van welke wolken beschikbaar zijn, zoals globale wolken, overheidswolken en het beeld waarnaar het plan verwijst. Als u uw aanbieding op de commerciële marktplaats wilt aanbieden, moet u ten minste één abonnement instellen.

Nadat u uw plannen hebt gemaakt, wordt op het tabblad **Overzicht van abonnementen** het volgende weergegeven:

- Namen plannen
- Prijsmodel
- Beschikbaarheid van de cloud (wereldwijd of overheid)
- Huidige publicatiestatus
- Alle beschikbare acties

De acties die beschikbaar zijn in het overzicht van het plan, zijn afhankelijk van de huidige status van uw abonnement. Deze omvatten:

- **Concept verwijderen** : als de planstatus een concept is.
- **Verkoopplan stoppen** - Als de status van het plan live wordt gepubliceerd.

#### <a name="create-new-plan"></a>Nieuw plan maken

Selecteer **Nieuw plan maken**. Het dialoogvenster **Nieuw plan** wordt weergegeven.

Maak in het vak **Plan-ID** een unieke plan-id voor elk plan in deze aanbieding. Deze ID is zichtbaar voor klanten in het webadres van het product. Gebruik alleen kleine letters en cijfers, streepjes of underscores en maximaal 50 tekens.

> [!NOTE]
> De plan-id kan niet worden gewijzigd nadat u Maken hebt **geselecteerd.**

Voer **in** het vak Naam van het plan een naam voor dit plan in. Klanten zien deze naam wanneer ze beslissen welk plan ze willen selecteren binnen uw aanbieding. Maak een unieke naam voor elk plan in deze aanbieding. U bijvoorbeeld een aanbiedingsnaam van **Windows Server** gebruiken met abonnementen **Windows Server 2016** en Windows Server **2019.**

### <a name="plan-setup"></a>Installatie plannen

Met dit tabblad u kiezen in welke clouds het abonnement beschikbaar is. Uw antwoorden op dit tabblad zijn van invloed op welke velden op andere tabbladen worden weergegeven.

#### <a name="cloud-availability"></a>Beschikbaarheid in de cloud

Uw abonnement moet beschikbaar zijn in ten minste één cloud.

Selecteer de optie **Azure Global,** zodat uw abonnement kan worden gebruikt door klanten in alle wereldwijde Azure-regio's die de commerciële marktplaats gebruiken. Zie Geografische [beschikbaarheid en valutaondersteuning](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies)voor meer informatie .

Selecteer hier de azure [**government cloud-optie**](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome) om uw oplossing te laten verschijnen. Dit is een overheidscommunitycloud met gecontroleerde toegang voor klanten uit Amerikaanse federale, staats- en lokale of tribale overheidsinstanties, evenals partners die in aanmerking komen om hen te bedienen. Als uitgever bent u verantwoordelijk voor nalevingscontroles, beveiligingsmaatregelen en aanbevolen procedures voor deze cloudcommunity. Azure Government maakt gebruik van fysiek geïsoleerde datacenters en netwerken (alleen in de VS).

Voordat [u publiceert](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners) naar Azure Government, test en bevestigt u uw oplossing binnen dat gebied, omdat de resultaten mogelijk verschillen. Als u uw oplossing wilt maken en testen, vraagt u een proefaccount aan bij de proefversie van [Microsoft Azure Government.](https://azure.microsoft.com/global-infrastructure/government/request/)

> [!NOTE]
> Nadat uw abonnement is gepubliceerd en beschikbaar is in een specifieke cloud, u die cloud niet verwijderen.

#### <a name="azure-government-cloud-certifications"></a>Azure Government Cloud-certificeringen

Deze optie kan alleen worden gezien als **Azure Government Cloud** is geselecteerd onder Cloud **beschikbaarheid**.

Azure Government-services verwerken gegevens die onderworpen zijn aan bepaalde overheidsvoorschriften en -vereisten. Bijvoorbeeld FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 en CJIS.

Als u uw certificeringen voor deze programma's wilt weergeven, u maximaal 100 koppelingen opgeven die ze beschrijven. Dit kunnen links naar uw aanbiedingen op het programma direct of naar uw eigen website. Deze koppelingen zijn alleen zichtbaar voor azure-klanten.

## <a name="plan-listing"></a>Aanbieding plannen

Op dit tabblad worden specifieke informatie voor elk ander plan binnen de huidige aanbieding weergegeven.

### <a name="plan-name"></a>Naam plannen

Dit is vooraf gevuld met de naam die u uw plan gaf toen u het maakte. U deze naam zo nodig wijzigen. Het kan tot 50 tekens lang zijn. Deze naam wordt weergegeven als de titel van dit abonnement in Azure Marketplace en Azure-portal. Het wordt gebruikt als de standaardmodulenaam nadat het plan klaar is om te worden gebruikt.

### <a name="plan-summary"></a>Overzicht plannen

Een korte samenvatting van uw softwareplan (niet de aanbieding). Dit overzicht wordt weergegeven in de zoekresultaten van Azure Marketplace en kan maximaal 100 tekens bevatten.

### <a name="plan-description"></a>Beschrijving van het plan

Beschrijf wat dit softwareplan uniek maakt, evenals verschillen tussen plannen binnen uw aanbod. Beschrijf het aanbod niet, alleen het plan. Deze beschrijving wordt weergegeven in Azure Marketplace en in de Azure-portal op de pagina **Aanbiedingsvermelding.** Het kan dezelfde inhoud zijn die u in het planoverzicht hebt opgegeven en maximaal 2.000 tekens bevat.

Selecteer **Opslaan** nadat u deze velden hebt voltooid.

#### <a name="plan-examples"></a>Voorbeelden plannen

In de volgende voorbeelden ziet u hoe de velden met de planvermelding in verschillende weergaven worden weergegeven.

Dit zijn de velden in Azure Marketplace bij het bekijken van plandetails:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-10-plan-details-mtplc.png" alt-text="Illustreert de velden die u ziet bij het weergeven van plandetails in Azure Marketplace.":::

Dit zijn plandetails op de Azure-portal:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-11-plan-details-portal.png" alt-text="Hiermee worden plandetails op de Azure-portal geïllustreerd.":::

## <a name="plan-availability"></a>Beschikbaarheid plannen

Als u uw gepubliceerde aanbieding wilt verbergen zodat klanten deze niet kunnen zoeken, bladeren of kopen op de marktplaats, schakelt u het selectievakje **Abonnement verbergen** in op het tabblad **Beschikbaarheid** in.

Dit veld wordt gebruikt wanneer:

- De aanbieding is bedoeld om indirect te worden gebruikt wanneer verwezen door een andere toepassing.
- Het aanbod mag niet afzonderlijk worden gekocht.
- Het plan werd gebruikt voor de eerste tests en is niet langer relevant.
- Het plan werd gebruikt voor tijdelijke of seizoensgebonden aanbiedingen en mocht niet langer worden aangeboden.

## <a name="technical-configuration"></a>Technische configuratie

Containerafbeeldingen moeten worden gehost in een privé [Azure-containerregister](https://azure.microsoft.com/services/container-registry/). Geef op het tabblad **Technische configuratie** referentiegegevens op voor uw containerimageopslagplaats in het Azure Container Registry.

Nadat de aanbieding is gepubliceerd, wordt uw containerafbeelding gekopieerd naar Azure Marketplace in een specifiek openbaar containerregister. Alle aanvragen om uw containerafbeelding te gebruiken, worden weergegeven vanuit het openbare containerregister van Azure Marketplace, niet in uw privé-containerregister. Zie Technische [assets van Azure Container voorbereiden](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-technical-assets)voor meer informatie.

### <a name="image-repository-details"></a>Details van de afbeeldingsopslagplaats

Geef de volgende informatie op het tabblad Details van de **afbeeldingsopslagplaats.**

**Azure-abonnements-ID** : geef de abonnements-id op waar het gebruik wordt gerapporteerd en services worden gefactureerd voor het Azure Container Registry met uw containerafbeelding. U vindt deze id op de [pagina Abonnementen](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) in de Azure-portal.

**Azure-brongroepnaam** : geef de naam van de [brongroep](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal) op die het Azure Container Registry bevat met uw containerafbeelding. De resourcegroep moet toegankelijk zijn in de abonnements-ID (hierboven). U de naam vinden op de pagina [Resourcegroepen](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) in de Azure-portal.

**Azure Container Registry name** – Geef de naam op van het [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-intro) met uw containerafbeelding. Het containerregister moet zich in de Azure-brongroep bevinden die u eerder hebt opgegeven. Neem alleen de naam van het register op, niet de volledige naam van de inlogserver. Zorg ervoor dat **azurecr.io** van de naam wordt weglaten. U vindt de registernaam op de [pagina Containerregisters](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries) in de Azure-portal.

**Gebruikersnaam beheerder voor het Azure Container Registry** : geef de gebruikersnaam voor [beheerders](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account) op die is gekoppeld aan het Azure Container Registry met uw containerafbeelding. De gebruikersnaam en het wachtwoord zijn vereist om ervoor te zorgen dat uw bedrijf toegang heeft tot het register. Als u de gebruikersnaam en het wachtwoord van de beheerder wilt achterhalen, stelt u de eigenschap **waar voor beheerders** in met behulp van de Azure Command Line Interface (CLI). **True** U de **beheerdergebruiker** optioneel instellen **op Inschakelen** in Azure-portal.

 :::image type="content" source="media/azure-create-container-offer-images/azure-create-12-update-container-registry-edit.png" alt-text="Hiermee wordt het dialoogvenster Containerregister bijwerken weergegeven.":::

**Wachtwoord voor het Azure Container Registry** : geef het wachtwoord op voor de gebruikersnaam van de beheerder die is gekoppeld aan het Azure Container Registry en uw containerafbeelding heeft. De gebruikersnaam en het wachtwoord zijn vereist om ervoor te zorgen dat uw bedrijf toegang heeft tot het register. U het wachtwoord ophalen van de Azure-portal door naar **Container Registry** > **Access Keys** of met Azure CLI te gaan met de opdracht [Show](https://docs.microsoft.com/cli/azure/acr/credential?view=azure-cli-latest#az-acr-credential-show).

:::image type="content" source="media/azure-create-container-offer-images/azure-create-13-access-keys.png" alt-text="Hiermee wordt het menu Toegangssleutel weergegeven.":::

**Naam van opslagplaatsen in het Azure Container Registry**. Geef de naam op van de Azure Container Registry-opslagplaats met uw afbeelding. Voeg de naam van de opslagplaats toe wanneer u de afbeelding naar het register duwt. U de naam van de opslagplaats vinden door naar de pagina [Container Registry](https://azure.microsoft.com/services/container-registry/) > **Repositories** te gaan. Zie [Containerregisterrepositories weergeven in Azure-portal](https://docs.microsoft.com/azure/container-registry/container-registry-repositories)voor meer informatie.

> [!NOTE]
> Nadat de naam is ingesteld, kan deze niet meer worden gewijzigd. Gebruik een unieke naam voor elke aanbieding in uw account.

### <a name="image-tags-for-new-versions-of-your-offer"></a>Afbeeldingstags voor nieuwe versies van uw aanbieding

Klanten moeten automatisch updates van de Azure Marketplace kunnen ontvangen wanneer u een update publiceert. Als ze niet willen updaten, moeten ze in staat zijn om op een specifieke versie van uw afbeelding te blijven. U dit doen door elke keer dat u een update van de afbeelding maakt, nieuwe afbeeldingstags toe te voegen.

### <a name="image-tag"></a>Afbeeldingstag

Dit veld moet een **nieuwste** tag bevatten die verwijst naar de nieuwste versie van uw afbeelding op alle ondersteunde platforms. Het moet ook een versietag bevatten (bijvoorbeeld te beginnen met xx.xx.xx, waarbij xx een getal is). Klanten moeten [manifesttags](https://github.com/estesp/manifest-tool) gebruiken om zich op meerdere platforms te richten. Alle tags waarnaar wordt verwezen door een manifesttag moeten ook worden toegevoegd, zodat we ze kunnen uploaden.

Alle manifesttags (behalve de nieuwste tag) moeten **-** beginnen met X.Y of X.Y.Z- waar X, Y en Z gehele getallen zijn. Als een **nieuwste** tag bijvoorbeeld verwijst naar 1.0.1-linux-x64, 1.0.1-linux-arm32 en 1.0.1-windows-arm32, moeten deze zes tags aan dit veld worden toegevoegd. Zie Technische [assets van Azure Container voorbereiden](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-technical-assets)voor meer informatie.

> [!NOTE]
> Vergeet niet om een testtag aan uw afbeelding toe te voegen, zodat u de afbeelding tijdens het testen identificeren.

## <a name="review-and-publish"></a>Bekijken en publiceren

Nadat u alle vereiste gedeelten van de aanbieding hebt voltooid, u deze indienen om deze te bekijken en te publiceren.

Selecteer in de rechterbovenhoek van de portal de optie **Controleren en** **publiceren**.

Op de review pagina u:

- Zie de voltooiingsstatus voor elk gedeelte van de aanbieding. U niet publiceren totdat alle gedeelten van de aanbieding als voltooid zijn gemarkeerd.
  - **Niet gestart** – is nog niet gestart en moet worden voltooid.
  - **Onvolledig** – Heeft fouten die moeten worden opgelost of vereist dat u meer informatie. Zie de secties eerder in dit document voor hulp.
  - **Compleet** : bevat alle vereiste gegevens zonder fouten. Alle onderdelen van de aanbieding moeten volledig zijn voordat u de aanbieding indienen.
- Geef het certificeringsteam testinstructies om ervoor te zorgen dat uw aanbieding correct wordt getest. Geef ook aanvullende notities die nuttig zijn voor het begrijpen van uw aanbod.

Als u de publicatieaanbieding wilt indienen, selecteert u **Publiceren**.

We sturen je een e-mail om je te laten weten wanneer een preview-versie van de aanbieding beschikbaar is om te bekijken en goed te keuren.

Als u uw aanbieding wilt publiceren naar het publiek (of als een privé-aanbieding, naar een privépubliek) gaat, gaat u naar partnercentrum en selecteert **u Go-live.**

## <a name="next-step"></a>Volgende stap

- [Een bestaande aanbieding bijwerken in Commerciële Marketplace](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
