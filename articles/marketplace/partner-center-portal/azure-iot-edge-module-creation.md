---
title: Een Azure IoT Edge-moduleaanbieding maken met Partner Center - Azure Marketplace
description: Meer informatie over het maken van een IoT Edge-moduleaanbieding in Azure Marketplace met behulp van partnercentrum
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: 6b56b748ef31bcfd33893e55d3ea5f8d9851a3ff
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80674473"
---
# <a name="create-an-iot-edge-module-offer"></a>Aanbieding voor IoT Edge-module maken

> [!IMPORTANT]
> We verplaatsen het beheer van uw IoT Edge-moduleaanbiedingen van Cloud Partner Portal naar Partner Center. Totdat uw aanbiedingen zijn gemigreerd, volgt u de instructies in de [IoT Edge-module met publicatieoverzicht](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-offer-process-parts) voor Cloud Partner Portal om uw aanbiedingen te beheren.

In dit artikel wordt beschreven hoe u een IoT-moduleaanbieding (Internet of Things) voor Azure Marketplace maken en publiceren.

Voordat u een IoT Edge-moduleaanbieding maken, moet u een commercieel marktplaatsaccount hebben in Partner Center. Zie Een account voor commerciële [marktplaatsmaken in partnercentrum](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)als u er nog geen hebt gemaakt.

## <a name="create-a-new-offer"></a>Een nieuwe aanbieding maken

1. Meld u aan bij Partnercentrum.
2. Selecteer in het menu links navigatie de optie **Overzicht van commerciële marktplaatsen** > **.**

    ![Hiermee wordt het linkernavigatiemenu weergegeven.](./media/cs-menu-overview.png)

3. Selecteer **+ Nieuwe aanbieding** > **IoT Edge module**. Het dialoogvenster **Nieuwe aanbieding** wordt weergegeven.

> [!IMPORTANT]
> Nadat een aanbieding is gepubliceerd, worden bewerkingen die zijn aangebracht in het Partnercentrum alleen weergegeven in etalages nadat de aanbieding opnieuw is gepubliceerd. Zorg ervoor dat u altijd opnieuw publiceert nadat u wijzigingen hebt aangebracht.

### <a name="offer-id-and-alias"></a>Aanbiedings-ID en alias

Voer een **aanbiedings-id in**. Dit is een unieke id voor elke aanbieding in uw account.

- Deze id is zichtbaar voor klanten in het webadres voor de marketplace-aanbieding en Azure Resource Manager-sjablonen, indien van toepassing.
- Gebruik alleen kleine letters en cijfers. Het kan koppeltekens en underscores bevatten, maar geen spaties, en is beperkt tot 50 tekens. Als u bijvoorbeeld **test-aanbieding-1**invoert, wordt `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`het webadres van de aanbieding .
- De aanbiedings-id kan niet worden gewijzigd nadat u Maken hebt geselecteerd.

Voer een **aanbiedingsalias**in . Dit is de naam die wordt gebruikt om te verwijzen naar de aanbieding in Partner Center.

- Deze naam wordt niet gebruikt in de markt en verschilt van de aanbiedingsnaam en andere waarden die aan klanten worden weergegeven.
- Dit kan niet worden gewijzigd nadat u Maken hebt **geselecteerd.**

Nadat u deze twee waarden hebt ingevoerd, selecteert u **Maken** voordat u doorgaat naar de volgende pagina, Overzicht van Aanbieding.

## <a name="offer-overview"></a>Aanbiedingsoverzicht

De **overzichtspagina Aanbieding** toont een visuele weergave van de stappen die nodig zijn om deze aanbieding te publiceren (zowel voltooid als aanstaande) en hoe lang elke stap moet duren om te voltooien.

Deze pagina bevat koppelingen om bewerkingen uit te voeren op basis van de selectie die u maakt. Bijvoorbeeld:

- Als de aanbieding een concept is - [Conceptvoorstel verwijderen](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- Als het aanbod live is - [Stop met de verkoop van het aanbod](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- Als de aanbieding in preview is - [Go-live](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- Als u de afmelding van uitgevers nog niet hebt voltooid, [kunt u publiceren annuleren.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="offer-setup"></a>Instelling voor aanbieding

Volg deze stappen om uw aanbieding in te stellen.

### <a name="connect-lead-management"></a>Leadbeheer verbinden

Wanneer u uw aanbieding op de marktplaats publiceert met Partner Center, u deze optioneel koppelen aan uw CRM-systeem (Customer Relationship Management). Hiermee u klantcontactgegevens ontvangen zodra iemand interesse toont in of uw product gebruikt.

1. Selecteer een leadbestemming waarnaar wij de klantenleads moeten sturen. Partnercenter ondersteunt de volgende CRM-systemen:

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
- [Overzicht van leadbeheer](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (Zorg ervoor dat uw pop-upblokkering is uitgeschakeld).

Selecteer **Concept opslaan** voordat u doorgaat naar de volgende sectie, Eigenschappen.

### <a name="properties"></a>Eigenschappen

Op deze pagina u de categorieën definiëren die worden gebruikt om uw aanbieding op de marktplaats te groeperen en de juridische contracten die uw aanbod ondersteunen.

#### <a name="category"></a>Categorie

Selecteer minimaal één en maximaal vijf categorieën. Deze categorieën worden gebruikt om uw aanbieding in de juiste zoekgebieden op marktplaats te plaatsen en worden weergegeven op uw pagina met aanbiedingsgegevens. Leg in de aanbiedingsbeschrijving uit hoe uw aanbieding deze categorieën ondersteunt. Op de bladerpagina's worden alle IoT Edge-modules weergegeven onder de categorie **Internet of Things > IoT Edge-module.** 

#### <a name="legal"></a>Juridisch

U moet algemene voorwaarden voor de aanbieding opgeven. U hebt hiervoor twee opties:

- Gebruik het standaardcontract voor de Microsoft Commercial Marketplace.
- Geef uw eigen algemene voorwaarden.

##### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Standaardcontract voor de Microsoft Commercial Marketplace

Wij bieden een standaard contract sjabloon om te helpen vergemakkelijken transacties in de commerciële markt. U ervoor kiezen om uw oplossing aan te bieden in het kader van het standaardcontract, dat klanten slechts één keer hoeven te controleren en accepteren. Dit is een goede optie als je niet wilt aangepaste voorwaarden ambachtelijke.

Zie [Standaardcontract voor de Microsoft Commercial Marketplace voor](https://docs.microsoft.com/azure/marketplace/standard-contract)meer informatie over het standaardcontract. U ook de [standaardcontractPDF](https://go.microsoft.com/fwlink/?linkid=2041178) downloaden (zorg ervoor dat uw pop-upblokkering is uitgeschakeld).

Als u het standaardcontract wilt gebruiken, schakelt u het selectievakje **Het standaardcontract voor de commerciële marktplaats van Microsoft gebruiken** in en klikt u op **Accepteren**.

> [!NOTE]
> Nadat u een aanbieding hebt gepubliceerd met behulp van het standaardcontract voor microsoft-commerciële marktplaats, u uw eigen aangepaste algemene voorwaarden niet gebruiken. Bied uw oplossing aan onder het standaardcontract of onder uw eigen voorwaarden.

![Illustreert het selectievakje Standaardcontract voor de commerciële marktplaats van Microsoft.](./media/iot-edge-module-creation/iot-edge-module-standard-contract-checkbox.png)

##### <a name="your-own-terms-and-conditions"></a>Uw eigen algemene voorwaarden

Als u uw eigen aangepaste algemene voorwaarden wilt bieden, voert u deze in het vak **Algemene Voorwaarden** in. U in dit vak een onbeperkt aantal tekens van tekst invoeren. Klanten moeten deze voorwaarden accepteren voordat ze uw aanbieding kunnen proberen.

Selecteer **Concept opslaan** voordat u doorgaat naar de volgende sectie, Aanbieding smaken.

## <a name="offer-listing"></a>Aanbieding aanbieden

Hier definieert u de aanbiedingsgegevens die op de markt worden weergegeven. Dit omvat de aanbiedingnaam, beschrijving, afbeeldingen, enzovoort. Volg het beleid op de beleidspagina van Microsoft tijdens het configureren van deze aanbieding.

> [!NOTE]
> Aanbiedingsgegevens hoeven niet in het Engels te zijn als de aanbiedingsbeschrijving begint met de zinsnede 'Deze toepassing is alleen beschikbaar in [niet-Engelse taal]." Het is ook goed om een nuttige koppeling te bieden om inhoud aan te bieden in een taal die verschilt van de taal die wordt gebruikt in de aanbiedingsgegevens van de aanbieding.

### <a name="name"></a>Name

De naam die u hier invoert, wordt weergegeven als de titel van uw aanbieding. Dit veld is vooraf gevuld met de tekst die u hebt ingevoerd in het vak **Alias aanbieden** toen u de aanbieding hebt gemaakt. U kunt deze naam later wijzigen.

De naam:

- Kan een handelsmerk zijn (en u handelsmerk- of auteursrechtsymbolen opnemen).
- Kan niet langer dan 50 tekens zijn.
- Kan geen emoji's bevatten.

### <a name="search-results-summary"></a>Overzicht van zoekresultaten

Geef een korte beschrijving van uw aanbod. Dit kan maximaal 100 tekens lang zijn en wordt gebruikt in de zoekresultaten van marktplaatsen.

### <a name="long-summary"></a>Lange samenvatting

Geef een meer gedetailleerde beschrijving van uw aanbod. Dit kan maximaal 256 tekens lang zijn en wordt gebruikt in de zoekresultaten van marktplaatsen.

### <a name="description"></a>Beschrijving

Geef een langere beschrijving van uw aanbieding, tot 3.000 tekens. Dit wordt weergegeven aan klanten in het overzicht van de marktplaatsvermelding.

Neem een of meer van de volgende gegevens op in uw beschrijving:

- De waarde en de belangrijkste voordelen van uw aanbod
- Categorie- of brancheorganisaties, of beide
- In-app aankoopmogelijkheden
- Eventuele vereiste informatie

IoT Edge-moduleaanbiedingen moeten een minimale hardwarevereistenalinea onder aan de beschrijving bevatten. Bijvoorbeeld:

*Minimale hardwarevereisten: Linux x64 en arm32 OS, 1 GB RAM, 500 Mb opslag*

Hier zijn enkele tips voor het schrijven van uw beschrijving:

- Beschrijf duidelijk de waarde van uw aanbieding in de eerste paar zinnen van uw beschrijving. Neem de volgende items op:
    - Beschrijving van het aanbod.
    - Het type gebruiker dat profiteert van het aanbod.
    - De behoeften van de klant of geeft de aanbiedingsadressen uit.
- Vergeet niet dat de eerste paar zinnen kunnen worden weergegeven in de zoekresultaten.
- Vertrouw niet op functies en functionaliteit om uw product te verkopen. Richt u in plaats daarvan op de waarde die uw aanbieding biedt.
- Probeer branchespecifieke woordenschat of op voordelen gebaseerde formuleringen te gebruiken.

Als u de **beschrijving van** uw aanbieding aantrekkelijker wilt maken, gebruikt u de editor voor rijke tekst om uw beschrijving op te maken. Met de rich text editor u getallen, opsommingstekens, vet, cursief en inspringingen toevoegen om uw beschrijving leesbaarder te maken.

:::image type="content" source="media/text-editor2.png" alt-text="Illustreert de rijke teksteditor." border="false":::

- Als u de indeling van uw inhoud wilt wijzigen, markeert u de tekst die u wilt opmaken en selecteert u een tekststijl, zoals in deze schermafbeelding wordt weergegeven:

     :::image type="content" source="media/text-editor3.png" alt-text="Hiermee wordt het besturingselement tekststijl geïllustreerd in de teksteditor met rijke tekst." border="false":::

- Als u een lijst met opsommingstekens of nummers aan de tekst wilt toevoegen, gebruikt u de opties in deze schermafbeelding:
  
    :::image type="content" source="media/text-editor4.png" alt-text="Hiermee worden de besturingselementen met opsommingstekens en nummerlijst weergegeven in de rich text editor." border="false":::

- Als u inspringing aan de tekst wilt toevoegen of verwijderen, gebruikt u de opties in deze schermafbeelding:

    :::image type="content" source="media/text-editor5.png" alt-text="Hiermee wordt de inspringingsbesturingselementen in de rich text editor weergegeven." border="false":::

#### <a name="privacy-policy-url"></a>URL van het privacybeleid

Voer het webadres van het privacybeleid van uw organisatie in. U bent verantwoordelijk om ervoor te zorgen dat uw aanbod voldoet aan de privacywet- en regelgeving. Je bent ook verantwoordelijk voor het plaatsen van een geldig privacybeleid op je website.

#### <a name="useful-links"></a>Handige koppelingen

Geef aanvullende online documenten over uw aanbod. U maximaal 25 links toevoegen. Als u een koppeling wilt toevoegen, selecteert u **+ Een koppeling toevoegen** en voltooit u de volgende velden:

- **Titel** - Klanten zien de titel op de pagina met details van uw aanbieding.
- **Link (URL)** - Voer een koppeling in voor klanten om uw online document te bekijken. De koppeling moet beginnen met http:// of https://.

Zorg ervoor dat u ten minste één koppeling toevoegt aan uw documentatie en één koppeling naar de compatibele IoT Edge-apparaten uit de [Azure IoT-apparaatcatalogus.](https://catalog.azureiotsolutions.com/)

### <a name="contact-information"></a>Contactgegevens

U moet de naam, e-mail en het telefoonnummer opgeven voor een **contactpersoon voor ondersteuning** en een contactpersoon voor **engineering.** Deze informatie wordt niet aan klanten getoond. Het is beschikbaar voor Microsoft en kan worden verstrekt aan Cloud Solution Provider (CSP) partners.

- Ondersteuningscontact (vereist): voor algemene ondersteuningsvragen.
- Technische contactpersoon (vereist): Voor technische vragen en certificeringsproblemen.
- CSP-programmacontact (optioneel): voor resellervragen met betrekking tot het CSP-programma.

Geef in de sectie **Contactpersoon voor ondersteuning** het webadres van de **ondersteuningswebsite** op waar partners ondersteuning voor uw aanbieding kunnen vinden op basis van de vraag of de aanbieding beschikbaar is in wereldwijde Azure, Azure Government of beide.

Geef in de **sectie Contact van het CSP-programma** de link **(CSP Program Marketing Materials)** op waar CSP-partners marketingmateriaal voor uw aanbieding kunnen vinden.

#### <a name="additional-marketplace-listing-resources"></a>Aanvullende bronnen voor marktplaatsvermeldingen

Zie Aanbevolen aanbiedingsaanbiedingen aanbieden voor meer informatie over het maken van [aanbiedingsaanbiedingen.](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

### <a name="marketplace-images"></a>Marketplace-afbeeldingen

Geef logo's en afbeeldingen om te gebruiken met uw aanbod. Alle afbeeldingen moeten in .png-indeling zijn. Wazige afbeeldingen worden afgewezen.

#### <a name="store-logos"></a>Winkellogo's

Geef png-bestanden van het logo van uw aanbieding op in elk van de volgende vier pixelformaten:

- **Klein (48 x 48)**
- **Gemiddeld (90 x 90)**
- **Groot (216 x 216)**
- **Breed (255 x 115)**

Alle vier de logo's zijn vereist en worden gebruikt op verschillende plaatsen in de marktplaats vermelding.

#### <a name="screenshots-optional"></a>Schermafbeeldingen (optioneel)

Voeg maximaal vijf screenshots toe die laten zien hoe uw aanbieding werkt. Elk moet 1280 x 720 pixels groot en in .png-formaat hebben.

#### <a name="videos-optional"></a>Video's (optioneel)

Voeg maximaal vijf video's toe die je aanbod demonstreren. Voer de naam van de video, het webadres en een miniatuur .png-afbeelding van de video in met een grootte van 1280 x 720 pixels.

#### <a name="offer-examples"></a>Voorbeelden van aanbieding

In de volgende voorbeelden ziet u hoe de aanbiedingsvelden op verschillende plaatsen van de aanbieding worden weergegeven.

In deze schermafbeelding wordt de pagina **Aanbiedingsvermelding** weergegeven in Azure Marketplace.

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-offer-listing-page.png" alt-text="Hiermee wordt de aanbiedingspagina in Azure Marketplace weergegeven.":::

In deze schermafbeelding worden de zoekresultaten in Azure Marketplace weergegeven:

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-marketplace-search-results.png" alt-text="Hiermee worden de zoekresultaten in Azure Marketplace geïllustreerd.":::

In deze schermafbeelding wordt de pagina **Aanbiedingsvermelding** weergegeven in de Azure-portal.

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-listing-page-azure-portal.png" alt-text="Hiermee wordt de pagina Aanbiedingsvermelding in Azure-portal weergegeven.":::

Deze schermafbeelding toont zoekresultaten in de Azure-portal.

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-azure-portal-search-results.png" alt-text="Hiermee wordt de pagina Aanbiedingsvermelding in Azure-portal weergegeven.":::

Selecteer **Concept opslaan** voordat u doorgaat naar de volgende sectie, Voorbeeld.

## <a name="preview"></a>Preview

Op het **tabblad Voorbeeld**kun je een beperkte **preview-doelgroep** kiezen voor het valideren van je aanbieding voordat je het live publiceert aan het bredere marktplaatspubliek.

> [!IMPORTANT]
> Nadat u uw aanbieding in Voorbeeld hebt bekeken, moet u **Live gaan** selecteren om uw aanbieding openbaar te maken.

Geef uw voorbeelddoelgroep op met AZURE-guids voor azure-abonnement- id's, samen met een optionele beschrijving voor elk. Geen van deze velden kan worden gezien door klanten.

> [!NOTE]
> U uw Azure-abonnements-id vinden op de pagina Abonnementen in de Azure-portal.

Voeg ten minste één Azure-abonnements-id toe, afzonderlijk (maximaal 10) of door een CSV-bestand te uploaden (maximaal 100). Door deze abonnements-id's toe te voegen, bepaalt u wie een voorbeeld van uw aanbieding kan bekijken voordat deze live wordt gepubliceerd. Als je aanbieding al live is, kun je een preview-doelgroep definiëren om wijzigingen of updates van je aanbieding te testen.

> [!NOTE]
> De preview-doelgroep verschilt van een privépubliek. Een **preview-doelgroep** kan alle aanbiedingsplannen zien en bevestigen voordat ze live zijn op de marktplaats, inclusief die welke alleen worden gepubliceerd voor een **privépubliek** (ingesteld op het tabblad Beschikbaarheid).

Selecteer **Concept opslaan** voordat u doorgaat naar de volgende sectie, Overzicht plannen.

### <a name="plan-overview"></a>Overzicht van plannen

Met dit tabblad u verschillende abonnementsopties bieden binnen dezelfde aanbieding in partnercentrum. Deze plannen, werden eerder aangeduid als SKU's, of voorraad houden eenheden. Plannen kunnen verschillen in termen van welke wolken beschikbaar zijn, zoals globale wolken, overheidswolken en het beeld waarnaar het plan verwijst. Als u uw aanbieding op de marktplaats wilt aanbieden, moet u ten minste één abonnement instellen.

Nadat u uw plannen hebt gemaakt, wordt op het tabblad **Overzicht van abonnementen** het volgende weergegeven:

- Namen plannen
- Prijsmodel
- Beschikbaarheid van de cloud (wereldwijd of overheid)
- Huidige publicatiestatus
- Alle beschikbare acties

De acties die beschikbaar zijn in het overzicht van het plan, zijn afhankelijk van de huidige status van uw abonnement. Deze omvatten:

- **Concept verwijderen:** als de planstatus een concept is.
- **Stop verkoopplan**: Als de status van het plan live wordt gepubliceerd.

#### <a name="create-new-plan"></a>Nieuw plan maken

Selecteer **Nieuw plan maken**. Het dialoogvenster **Nieuw plan** wordt weergegeven.

Maak in het vak **Plan-ID** een unieke plan-ID voor elk abonnement in deze aanbieding. Deze ID is zichtbaar voor klanten in het productwebadres. Gebruik alleen kleine letters en cijfers, streepjes of underscores en maximaal 50 tekens.

Voer **in** het vak Naam van het plan een naam voor dit plan in. Klanten zien deze naam wanneer ze beslissen welk plan ze willen selecteren binnen uw aanbieding. Maak een unieke naam voor elk plan in deze aanbieding. U bijvoorbeeld een aanbiedingsnaam van **Windows Server** gebruiken met abonnementen **Windows Server 2016** en Windows Server **2019.**

> [!NOTE]
> De plan-id kan niet worden gewijzigd nadat u Maken hebt **geselecteerd.**

Selecteer **Maken**.

### <a name="plan-setup"></a>Installatie plannen

Met dit tabblad u configureren in welke clouds het abonnement beschikbaar is. Uw antwoorden op dit tabblad zijn van invloed op welke velden op andere tabbladen worden weergegeven.

#### <a name="cloud-availability"></a>Beschikbaarheid in de cloud

Uw abonnement moet beschikbaar zijn in ten minste één cloud met Azure IoT Hub.

Selecteer de optie **Azure Global,** zodat uw abonnement kan worden gebruikt door klanten in alle algemene Azure-regio's die de marktplaats gebruiken. Zie Geografische [beschikbaarheid en valutaondersteuning](https://aka.ms/AzureGovCurrencies)voor meer informatie .

Selecteer hier de azure [government cloud-optie](https://aka.ms/WhatIsAzureGovernment) om uw oplossing te laten verschijnen. Dit is een overheidscommunitycloud met gecontroleerde toegang voor klanten uit Amerikaanse federale, staats- en lokale of tribale overheidsinstanties, evenals partners die in aanmerking komen om hen te bedienen. Als uitgever bent u verantwoordelijk voor nalevingscontroles, beveiligingsmaatregelen en aanbevolen procedures voor deze cloudcommunity. Azure Government maakt gebruik van fysiek geïsoleerde datacenters en netwerken (alleen in de VS). Voordat [u publiceert](https://aka.ms/azuregovpublish) naar Azure Government, test en bevestigt u uw oplossing binnen dat gebied, omdat de resultaten mogelijk verschillen. Als u uw oplossing wilt fasen en testen, vraagt u een proefaccount aan bij de proefversie van [Microsoft Azure Government.](https://aka.ms/AzureGovernmentTrial)

> [!NOTE]
> Nadat uw abonnement is gepubliceerd en beschikbaar is in een specifieke cloud, u die cloud niet verwijderen.

#### <a name="azure-government-cloud-certifications"></a>Azure Government Cloud-certificeringen

Deze optie is alleen zichtbaar als **Azure Government Cloud** is geselecteerd onder **Cloudbeschikbaarheid**.

Azure Government-services verwerken gegevens die onderworpen zijn aan bepaalde overheidsvoorschriften en -vereisten. Bijvoorbeeld FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 en CJIS. Om uw certificeringen voor deze programma's onder de aandacht te brengen, u maximaal 100 koppelingen aanbieden die uw certificeringen beschrijven. Dit kunnen links naar uw aanbiedingen op het programma direct of naar uw eigen website. Deze koppelingen zijn alleen zichtbaar voor azure-klanten.

## <a name="plan-listing"></a>Aanbieding plannen

Op dit tabblad worden specifieke informatie voor elk ander plan binnen dezelfde aanbieding weergegeven.

### <a name="plan-name"></a>Naam plannen

Dit is vooraf gevuld met de naam die u uw plan gaf toen u het maakte. U deze naam wijzigen, indien nodig. Het kan tot 50 tekens lang zijn. Deze naam wordt weergegeven als de titel van dit abonnement in Azure Marketplace en Azure-portal. Het wordt gebruikt als de standaardmodulenaam nadat het plan klaar is om te worden gebruikt.

### <a name="plan-summary"></a>Overzicht plannen

Geef een korte samenvatting van uw plan (niet de aanbieding). Dit overzicht wordt weergegeven in de zoekresultaten van Azure Marketplace en kan maximaal 100 tekens bevatten.

### <a name="plan-description"></a>Beschrijving van het plan

Beschrijf wat dit plan uniek maakt, evenals verschillen tussen plannen binnen uw aanbod. Beschrijf het aanbod niet, alleen het plan. Deze beschrijving wordt weergegeven in Azure Marketplace en in de Azure-portal op de pagina Aanbiedingsvermelding. Het kan dezelfde inhoud zijn die u in het planoverzicht hebt opgegeven en maximaal 2.000 tekens bevat.

Selecteer **Concept opslaan** nadat u deze velden hebt voltooid.

#### <a name="plan-examples"></a>Voorbeelden plannen

In de volgende voorbeelden ziet u hoe de velden met de planvermelding in verschillende weergaven worden weergegeven.

Dit zijn de velden in Azure Marketplace bij het bekijken van plandetails:

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-marketplace-plan-details.png" alt-text="Illustreert de velden die u ziet bij het weergeven van plandetails in Azure Marketplace.":::

Dit zijn plandetails op de Azure-portal:

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-azure-portal-plan-details.png" alt-text="Hiermee worden plandetails op de Azure-portal geïllustreerd.":::

## <a name="availability"></a>Beschikbaarheid

Als u uw gepubliceerde aanbieding wilt verbergen zodat klanten deze niet kunnen zoeken, bladeren of kopen op de marktplaats, schakelt u het selectievakje **Abonnement verbergen** in op het tabblad Beschikbaarheid in.

Dit veld wordt vaak gebruikt wanneer:

- De aanbieding is bedoeld om alleen indirect te worden gebruikt wanneer verwezen door een andere toepassing.
- Het aanbod mag niet afzonderlijk worden gekocht.
- Het plan werd gebruikt voor de eerste tests en is niet langer relevant.
- Het plan werd gebruikt voor tijdelijke of seizoensgebonden aanbiedingen en mocht niet langer worden aangeboden.

## <a name="technical-configuration"></a>Technische configuratie

Het type **IoT Edge-modulebiedt** een specifiek type container dat wordt uitgevoerd op een IoT Edge-apparaat. Op het tabblad **Technische configuratie** geeft u referentiegegevens voor uw containerimageopslagplaats in het Azure [Container Registry,](https://aka.ms/ContainerRegistry)samen met configuratie-instellingen waarmee klanten de module eenvoudig kunnen gebruiken.

Nadat de aanbieding is gepubliceerd, wordt uw IoT Edge-containerafbeelding gekopieerd naar Azure Marketplace in een specifiek openbaar containerregister. Alle aanvragen van Azure-gebruikers om uw module te gebruiken, worden weergegeven vanuit het openbare containerregister van Azure Marketplace, niet in het register van uw privécontainer.

U meerdere platforms targeten en verschillende versies van uw modulecontainerafbeelding bieden met behulp van tags. Zie Technische elementen van uw [IoT Edge-module voorbereiden](https://aka.ms/AzureIoTTechAsset)voor meer informatie over tags en versiebeheer.

### <a name="image-repository-details"></a>Details van de afbeeldingsopslagplaats

U geeft de volgende informatie op het tabblad Details van de **afbeeldingsopslagplaats.**

**Selecteer de afbeeldingsbron**: Selecteer de optie **Azure Container Registry.**

**Azure-abonnements-ID**: Geef de abonnements-id op waar resourcegebruik wordt gerapporteerd en services worden gefactureerd voor het Azure Container Registry met uw containerafbeelding. U vindt deze id op de [pagina Abonnementen](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) in de Azure-portal.

**Azure-brongroepnaam:** geef de naam van de [brongroep](https://aka.ms/ResourceManagerAzurePortal) op die het Azure Container Registry bevat met uw containerafbeelding. De resourcegroep moet toegankelijk zijn in de abonnements-ID (hierboven). U de naam vinden op de pagina [Resourcegroepen](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) in de Azure-portal.

**Azure-containerregisternaam:** geef de naam op van het [Azure Container Registry](https://aka.ms/DockerContainerRegistriesAzure) met uw containerafbeelding. Het containerregister moet aanwezig zijn in de Azure-brongroep die u eerder hebt opgegeven. Geef alleen de naam van het register op, niet de volledige naam van de loginserver. Zorg ervoor dat **azurecr.io** van de naam wordt weglaten. U vindt de registernaam op de [pagina Containerregisters](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries) in de Azure-portal.

**Gebruikersnaam beheerder voor het Azure Container Registry:** Geef de [gebruikersnaam van](https://aka.ms/AdminAccountContainerRegistry) de beheerder op die is gekoppeld aan het Azure Container Registry met uw containerafbeelding. De gebruikersnaam en het wachtwoord zijn vereist om ervoor te zorgen dat uw bedrijf toegang heeft tot het register. Als u de gebruikersnaam en het wachtwoord van de beheerder wilt achterhalen, stelt u de eigenschap **waar voor beheerders** in met behulp van de Azure Command Line Interface (CLI). **True** U de **beheerdergebruiker** optioneel instellen **in de** Azure-portal.

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-admin-user.png" alt-text="Hiermee wordt het dialoogvenster Containerregister bijwerken weergegeven.":::

**Wachtwoord voor het Azure Container Registry:** Geef het wachtwoord op voor de gebruikersnaam van de beheerder die is gekoppeld aan het Azure Container Registry en uw containerafbeelding heeft. De gebruikersnaam en het wachtwoord zijn vereist om ervoor te zorgen dat uw bedrijf toegang heeft tot het register. U het wachtwoord ophalen van de Azure-portal door naar **Container Registry** > **Access Keys** of met Azure CLI te gaan met de [opdracht Show.](https://aka.ms/azacrcredentialshow)

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-username-password.png" alt-text="Hiermee wordt het scherm met toegangssleutel in azure-portal weergegeven.":::

**Naam van opslagplaatsen in het Azure Container Registry**. Geef de naam op van de Azure Container Registry-opslagplaats met uw afbeelding. U geeft de naam van de opslagplaats op wanneer u de afbeelding naar het register duwt. U de naam van de opslagplaats vinden door naar de pagina [Container Registry](https://aka.ms/ContainerRegistry) > **Repositories**te gaan. Zie [Containerregisterrepositories weergeven in de Azure-portal](https://aka.ms/ContainerRegistryRepositoriesAzure)voor meer informatie. Houd er rekening mee dat nadat de naam is ingesteld, deze niet kan worden gewijzigd. Gebruik een unieke naam voor elke aanbieding in uw account.

### <a name="image-tags-for-new-versions-of-your-offer"></a>Afbeeldingstags voor nieuwe versies van uw aanbieding

Klanten moeten automatisch updates van de Azure Marketplace kunnen ontvangen wanneer u een update publiceert. Als ze niet willen updaten, moeten ze in staat zijn om op een specifieke versie van uw afbeelding te blijven. U dit doen door elke keer dat u een update van de afbeelding maakt, nieuwe afbeeldingstags toe te voegen.

**Afbeeldingstag**. Dit veld moet een **nieuwste** tag bevatten die verwijst naar de nieuwste versie van uw afbeelding op alle ondersteunde platforms. Het moet ook een versietag bevatten (bijvoorbeeld te beginnen met xx.xx.xx, waarbij xx een getal is). Klanten moeten [manifesttags](https://aka.ms/GitHubmanifest-tool) gebruiken om zich op meerdere platforms te richten. Alle tags waarnaar wordt verwezen door een manifesttag moeten ook worden toegevoegd, zodat we ze kunnen uploaden. Alle manifesttags (behalve de nieuwste tag) moeten beginnen met X.Y- of X.Y.Z- waarbij X, Y en Z gehele getallen zijn. Als een nieuwste tag bijvoorbeeld verwijst naar 1.0.1-linux-x64, 1.0.1-linux-arm32 en 1.0.1-windows-arm32, moeten deze zes tags aan dit veld worden toegevoegd. Zie [Technische elementen van uw IoT Edge-module voorbereiden](https://aka.ms/PrepareIoTEdgeModTechAssets) voor meer informatie over tags en versiebeheer.

### <a name="default-deployment-settings-optional"></a>Standaardimplementatie-instellingen (optioneel)

Definieer de meest voorkomende instellingen om uw IoT Edge-module te implementeren. Optimaliseer klantimplementaties door ze uw IoT Edge-module out-of-the-box te laten starten met deze standaardinstellingen.

**Standaardroutes**. De IoT Edge Hub beheert de communicatie tussen modules, de IoT Hub en apparaten. U routes instellen voor gegevensinvoer en -uitvoer tussen modules en de IoT Hub, waardoor u de flexibiliteit hebt om berichten te verzenden waar ze naartoe moeten zonder dat er extra services nodig zijn om berichten te verwerken of extra code te schrijven. Routes worden gemaakt met naam/waardeparen. U maximaal vijf standaardroutenamen definiëren, elk tot 512 tekens lang.

Zorg ervoor dat u de juiste [routesyntaxis](https://aka.ms/DeclareRoutesAzureIoT) in uw routewaarde gebruikt (meestal gedefinieerd als VAN/bericht/* IN $upstream). Dit betekent dat alle berichten die door modules worden verzonden, naar uw IoT Hub gaan. Als u naar uw module wilt verwijzen, gebruikt u de standaardmodulenaam, die uw **aanbiedingsnaam**is, zonder spaties of speciale tekens. Als u wilt verwijzen naar andere modules die nog niet bekend zijn, gebruikt u de <FROM_MODULE_NAME> conventie om uw klanten te laten weten dat ze deze informatie moeten bijwerken. Zie [Routes declareren voor](https://aka.ms/DeclareRoutesAzureIoT)meer informatie over IoT Edge-routes.

Als module ContosoModule bijvoorbeeld luistert naar ingangen op ContosoInput en uitvoergegevens bij ContosoOutput, is het zinvol om de volgende twee standaardroutes te definiëren:

- Naam #1: ToContosoModule
- Value #1: FROM /messages/modules/<FROM_MODULE_NAME>/outputs/* INTO BrokeredEndpoint("/modules/ContosoModule/inputs/ContosoInput")
- Naam #2: FromContosoModuleToCloud
- Value #2: FROM /messages/modules/ContonsoModule/outputs/ContosoOutput INTO $upstream

**Standaard module twin gewenste eigenschappen**. Een moduletwin is een JSON-document in de IoT-hub dat de statusinformatie voor een module-exemplaar opslaat, inclusief de gewenste eigenschappen. Gewenste eigenschappen worden gebruikt samen met gerapporteerde eigenschappen om de configuratie of voorwaarden van de module te synchroniseren. De backend van de oplossing kan de gewenste eigenschappen instellen en de module kan ze lezen. De module kan ook wijzigingsmeldingen ontvangen in de gewenste eigenschappen. Gewenste eigenschappen worden gemaakt met maximaal vijf naam/waardeparen en elke standaardwaarde moet minder dan 512 tekens hebben. U maximaal vijf naam/waarde twin gewenste eigenschappen definiëren. Waarden van dubbele gewenste eigenschappen moeten geldig JSON, niet-ontsnapt, zonder arrays met een maximale geneste hiërarchie van vier niveaus. In een scenario waarin een parameter die vereist is voor een standaardwaarde niet zinvol is (bijvoorbeeld het IP-adres van de server van een klant), u een parameter toevoegen als standaardwaarde. Zie [Gewenste eigenschappen definiëren of bijwerken](https://aka.ms/DefineUpdateProperties)voor meer informatie over de gewenste twee eigenschappen.

Als een module bijvoorbeeld een dynamisch configureerbare verversingsfrequentie ondersteunt met dubbele gewenste eigenschappen, is het zinvol om de volgende standaardgewenste eigenschap te definiëren:

- Naam #1: RefreshRate
- Waarde #1: 60

**Standaardomgevingsvariabelen**. Omgevingsvariabelen bieden aanvullende informatie aan een module die het configuratieproces helpt. Omgevingsvariabelen worden gemaakt met naam/waardeparen. Elke standaardomgevingsvariabelenaam en -waarde moeten minder dan 512 tekens bevatten en u maximaal vijf tekens definiëren. Wanneer een parameter die vereist is voor een standaardwaarde niet zinvol is (bijvoorbeeld het IP-adres van de server van een klant), u een parameter toevoegen als standaardwaarde.

Als een module bijvoorbeeld gebruiksvoorwaarden moet accepteren voordat deze wordt gestart, u de volgende omgevingsvariabele definiëren:

- Naam #1: ACCEPT_EULA
- Waarde #1: Y

**Opties voor het maken van standaardcontainers**. Opties voor het maken van containers sturen de creatie van de Docker-container van de IoT Edge-module. IoT Edge ondersteunt Docker engine API Create Container opties. Bekijk alle opties bij [Lijstcontainers.](https://aka.ms/ContainerList) Het veld Opties maken moet geldig JSON, niet-ontsnapte en minder dan 512 tekens zijn.

Als een module bijvoorbeeld poortbinding vereist, definieert u de volgende opties voor het maken:

"HostConfig":{"PortBindings":{"5012/tcp":[{"HostPort":"5012"}]}

## <a name="review-and-publish"></a>Bekijken en publiceren

Nadat u alle vereiste gedeelten van de aanbieding hebt voltooid, u deze indienen om deze te bekijken en te publiceren.

Selecteer in de rechterbovenhoek van de portal de optie **Controleren en publiceren**.

Op de beoordelingspagina ziet u de publicatiestatus:

- Zie de voltooiingsstatus voor elk gedeelte van de aanbieding. U niet publiceren totdat alle gedeelten van de aanbieding als voltooid zijn gemarkeerd.
    - **Niet gestart** - De sectie is nog niet gestart en moet worden voltooid.
    - **Onvolledig** - De sectie bevat fouten die moeten worden opgelost of waarvoor u meer informatie moet verstrekken. Zie de secties eerder in dit document voor richtlijnen.
    - **Compleet** - De sectie bevat alle vereiste gegevens en er zijn geen fouten. Alle onderdelen van de aanbieding moeten volledig zijn voordat u de aanbieding indienen.
- Geef het certificeringsteam testinstructies om ervoor te zorgen dat uw aanbieding correct wordt getest. Geef ook aanvullende notities die nuttig zijn voor het begrijpen van uw aanbod.

Als u de publicatieaanbieding wilt indienen, selecteert u **Publiceren**.

We sturen je een e-mail om je te laten weten wanneer een preview-versie van de aanbieding beschikbaar is om te bekijken en goed te keuren. Als u uw aanbieding wilt publiceren naar het publiek (of als een privé-aanbieding, naar een privépubliek) gaat, gaat u naar partnercentrum en selecteert **u Go-live.**

## <a name="next-steps"></a>Volgende stappen

- [Een bestaand aanbod bijwerken in de commerciële markt](https://docs.microsoft.com//azure/marketplace/partner-center-portal/update-existing-offer)