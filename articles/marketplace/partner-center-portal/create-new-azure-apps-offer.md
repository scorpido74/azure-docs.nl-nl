---
title: Een nieuwe Azure apps-aanbieding maken in de commerciële Marketplace
description: Het maken van een nieuwe Azure apps-aanbieding voor het aanbieden of verkopen in azure Marketplace, AppSource of via het programma Cloud Solution Provider (CSP) met behulp van de commerciële Marketplace-Portal in micro soft Partner Center.
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: d7e05f12c04136c8394dbcb27b7a950fc5ce85d9
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74281270"
---
# <a name="create-an-azure-application-offer"></a>Een Azure-toepassingsaanbieding maken

De stappen voor het publiceren van een Azure-toepassings aanbieding in commerciële Marketplace worden hier beschreven.

## <a name="azure-application-offer-type"></a>Type Azure-toepassings aanbieding

In dit onderwerp vindt u een overzicht van de grond beginselen van Azure-toepassingen.  U moet bekend zijn met deze concepten voordat u het proces voor het publiceren van een nieuwe Azure-toepassings aanbieding in Marketplace kunt starten.

### <a name="publishing-overview"></a>Overzicht van publiceren

De video [buil ding Solution-sjablonen en beheerde toepassingen voor Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603) is een inleiding tot het type Azure-toepassings aanbieding:

* Welke typen aanbieding beschikbaar zijn,
* Welke technische middelen zijn vereist;
* Een Azure Resource Manager sjabloon ontwerpen;
* De gebruikers interface van de App ontwikkelen en testen;
* Het publiceren van de app-aanbieding;
* Het controle proces van de toepassing.

### <a name="types-of-azure-application-plans"></a>Typen Azure-toepassings abonnementen

Er zijn twee soorten Azure-toepassings abonnementen, beheerde toepassingen en oplossings sjablonen.

* **Oplossings sjabloon** is een van de belangrijkste manieren om een oplossing op Marketplace te publiceren. Dit type abonnement wordt gebruikt wanneer voor uw oplossing extra implementatie-en configuratie automatisering is vereist dan één virtuele machine (VM).  Met een oplossings sjabloon kunt u het leveren van meer dan één resource automatiseren, met inbegrip van Vm's, netwerken en opslag resources om complexe IaaS-oplossingen te bieden.  Zie de [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) -documentatie voor meer informatie over het bouwen van oplossings sjablonen.

* **Beheerde toepassing** is vergelijkbaar met oplossings sjablonen, met één belang rijk verschil. In een beheerde toepassing worden de resources geïmplementeerd vanuit een resourcegroep die wordt beheerd door de uitgever van de app. De resourcegroep is opgenomen in het abonnement van de consument, maar een identiteit in de tenant van de uitgever heeft toegang tot de resourcegroep. De uitgever bepaalt de kosten voor de voortdurende ondersteuning van de oplossing. Gebruik beheerde toepassingen om eenvoudig volledig beheerde, kant-en-klare toepassingen te bouwen en te leveren aan uw klanten.  Zie het overzicht van door [Azure beheerde toepassingen](https://docs.microsoft.com/azure/managed-applications/overview)voor meer informatie over de voor-en andere typen beheerde toepassingen.

Alle Azure-toepassingen bevatten ten minste twee bestanden in de hoofdmap van een `.zip` archief:

* Een resource manager-sjabloon bestand met de naam [mainTemplate. json](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  Dit is de sjabloon waarmee de resources worden gedefinieerd die moeten worden geïmplementeerd in het Azure-abonnement van de klant.  Voor voor beelden van Resource Manager-sjablonen raadpleegt u de [Azure Quick Start-sjablonen galerie](https://azure.microsoft.com/resources/templates/) of de bijbehorende [github: Azure Resource Manager Quick](https://github.com/azure/azure-quickstart-templates) start-sjablonen opslag plaats.

* Een gebruikersinterface definitie voor de Azure-app voor het maken van toepassingen met de naam [createUiDefinition. json](https://docs.microsoft.com/azure/managed-applications/create-uidefinition-overview).  In de gebruikersinterface geeft u elementen op waarmee consumenten parameterwaarden kunnen opgeven.

Alle nieuwe aanbiedingen van Azure Application moeten een [GUID voor gebruiks toewijzing](??)van een Azure-partner bevatten.

### <a name="before-you-begin"></a>Voordat u begint

Raadpleeg de volgende documentatie voor Azure-toepassingen, die Quick starts, zelf studies en voor beelden biedt.

* [Azure Resource Manager sjablonen begrijpen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)
* Quickstarts:

    * [Sjablonen voor Azure Quick Start](https://azure.microsoft.com/documentation/templates/)
    * [GitHub Azure Quick Start-sjablonen](https://github.com/azure/azure-quickstart-templates)
    * [Toepassings definitie publiceren](https://docs.microsoft.com/azure/managed-applications/publish-managed-app-definition-quickstart)
    * [Service catalogus-app implementeren](https://docs.microsoft.com/azure/managed-applications/deploy-service-catalog-quickstart)

* Zelfstudies:

    * [Definitie bestanden maken](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
    * [Een Marketplace-toepassing publiceren](https://docs.microsoft.com/azure/managed-applications/publish-marketplace-app)

* Voor beelden

    * [Azure CLI](https://docs.microsoft.com/azure/managed-applications/cli-samples)
    * [Azure PowerShell](https://docs.microsoft.com/azure/managed-applications/powershell-samples)
    * [Oplossingen voor beheerde toepassingen](https://docs.microsoft.com/azure/managed-applications/sample-projects)

### <a name="fundamentals-in-technical-knowledge"></a>Grond beginselen van technische kennis

Het ontwerpen, bouwen en testen van deze assets vergt tijd en vereist technische kennis van het Azure-platform en de technologieën die worden gebruikt om de aanbieding te bouwen.

Uw technische team moet kennis hebben van de volgende micro soft-technologieën:

* Basis informatie over [Azure-Services](https://azure.microsoft.com/services/).
* Het [ontwerpen en ontwikkelen van Azure-toepassingen](https://azure.microsoft.com/solutions/architecture/).
* Werk kennis van [azure virtual machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage#storage)en [Azure-netwerken](https://azure.microsoft.com/services/?filter=networking#networking).
* Werk ervaring van [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/).
* Werk kennis van [JSON](https://www.json.org/).

### <a name="suggested-tools"></a>Aanbevolen hulpprogram ma's

Kies een of beide van de volgende script omgevingen om uw Azure-toepassing te beheren:

* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
* [Azure CLI](https://docs.microsoft.com/cli/azure)

Het is raadzaam om de volgende hulpprogram ma's toe te voegen aan uw ontwikkel omgeving:

* [Azure-opslagverkenner](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* [Visual Studio code](https://code.visualstudio.com/) met de volgende extensies:
    * Extensie: [Azure Resource Manager-Hulpprogram ma's](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    * Extensie: [beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    * Extensie: [PRETTIFY JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

U kunt de beschik bare hulpprogram ma's bekijken op de pagina [Azure Ontwikkelhulpprogramma's](https://azure.microsoft.com/tools/) .  Ook als u Visual Studio, de [Visual Studio Marketplace](https://marketplace.visualstudio.com/), gebruikt.

## <a name="create-an-azure-application-offer"></a>Een Azure-toepassingsaanbieding maken

Voordat u een Azure-toepassings aanbieding kunt maken, moet u eerst [een partner centrum-account maken](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) en het [dash board commerciële Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)openen, met het tabblad **overzicht** geselecteerd.

>[!Note]
>Zodra een aanbieding is gepubliceerd, worden wijzigingen die zijn aangebracht in het partner centrum, alleen bijgewerkt in het systeem en in de winkel als u het opnieuw publiceert.  Zorg ervoor dat u de aanbieding voor publicatie verzendt nadat u wijzigingen hebt aangebracht.

### <a name="create-a-new-offer"></a>Een nieuwe aanbieding maken

Selecteer de knop **+ nieuwe aanbieding** en selecteer vervolgens het menu-item **Azure-toepassing** . Het dialoog venster **nieuwe aanbieding** wordt weer gegeven.

### <a name="offer-id-and-alias"></a>Aanbiedings-ID en alias

* **Aanbiedings-id**: een unieke id voor elke aanbieding in uw account. Deze ID is zichtbaar voor klanten in het URL-adres voor de Marketplace-aanbieding en Azure Resource Manager sjablonen (indien van toepassing). <br> <br> De aanbiedings-ID moet kleine letters en alfanumerieke tekens bevatten (inclusief afbreek streepjes en onderstrepingen, maar geen witruimte). De waarde is beperkt tot 50 tekens en kan niet worden gewijzigd nadat u maken hebt geselecteerd. <br> <br> Als u hier bijvoorbeeld `test-offer-1` invoert, wordt de URL van de aanbieding `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`. 

* **Aanbiedings alias**: de naam die wordt gebruikt om te verwijzen naar de aanbieding in het partner centrum. Deze naam wordt niet gebruikt op de Marketplace en wijkt af van de naam van de aanbieding en andere waarden die worden weer gegeven voor klanten. Deze waarde kan niet worden gewijzigd nadat u **maken**hebt geselecteerd.

Wanneer u de **aanbiedings-id** en **aanbiedings alias**hebt ingevoerd, selecteert u **maken**. U kunt vervolgens aan alle andere delen van uw aanbieding werken.

## <a name="offer-setup"></a>Installatie van aanbieding

Op de pagina **aanbieding instellen** wordt u gevraagd om de volgende informatie op te vragen. Zorg ervoor dat u **Opslaan** selecteert na het volt ooien van deze velden.

### <a name="test-drive"></a>Test drive

Een test drive is een fantastische manier om uw aanbieding aan potentiële klanten te laten presen teren door hen de mogelijkheid te geven voor ' voor u ' te kopen, wat resulteert in een verhoogde conversie en de generatie van uiterst gekwalificeerde leads. [Meer informatie over test stations.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

Als u een test drive wilt inschakelen, schakelt u het selectie vakje **een test drive inschakelen** in. Vervolgens moet u een demonstratie omgeving configureren in de [technische configuratie van het test station](#types-of-azure-application-plans) configureren zodat klanten uw aanbieding voor een bepaalde periode kunnen uitproberen. 

>[!Note]
>Omdat alle Azure-toepassingen worden geïmplementeerd met een Azure Resource Manager-sjabloon, is het enige type test drive dat kan worden geconfigureerd voor een Azure-toepassing een [Azure Resource Manager test drive](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

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

### <a name="standard-marketplace-terms-and-conditions"></a>Standard Marketplace-voor waarden

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

### <a name="name"></a>Naam

De naam die u hier invoert, wordt aan klanten weer gegeven als de titel van de aanbieding. Dit veld wordt vooraf ingevuld met de tekst die u hebt ingevoerd voor de **aanbiedings alias** tijdens het maken van de aanbieding, maar u kunt deze waarde wijzigen. Deze naam kan worden aangemerkt (en u kunt symbolen van het handels merk of copyright bevatten). De naam mag niet langer zijn dan 50 tekens en mag geen emojis bevatten.

### <a name="summary"></a>Samenvatting

Geef een korte beschrijving van uw aanbieding (Maxi maal 100 tekens), die kan worden gebruikt in Zoek resultaten voor Marketplace.

### <a name="long-summary"></a>Lange samen vatting

Geef een langere beschrijving van uw aanbieding (Maxi maal 256 tekens). De beschrijving kan worden gebruikt in Zoek resultaten voor Marketplace.

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

### <a name="search-keywords"></a>Sleutelwoorden zoeken

U kunt eventueel Maxi maal drie Zoek trefwoorden invoeren om klanten te helpen uw aanbieding op Marketplace te vinden. Probeer deze tref woorden ook in uw beschrijving te gebruiken voor de beste resultaten.

### <a name="support-urls"></a>Url's voor ondersteuning

In deze sectie vindt u koppelingen waarmee klanten meer inzicht kunnen krijgen in uw aanbieding.

#### <a name="privacy-policy-url"></a>URL van privacybeleid

Voer de URL in voor het privacybeleid van uw organisatie. U bent verantwoordelijk om ervoor te zorgen dat uw app voldoet aan de wetten en voor schriften van de privacy en om een geldig privacybeleid te bieden.

#### <a name="useful-links"></a>Handige koppelingen

Bied optionele aanvullende online documenten over uw oplossing.  Voeg extra nuttige koppelingen toe door te klikken op **+ een koppeling toevoegen**.

### <a name="contacts"></a>Contactpersonen

In deze sectie moet u de naam, het e-mail adres en het telefoon nummer opgeven voor een **ondersteunings contact** en een **technische contact persoon**. Deze informatie wordt niet weer gegeven aan klanten, maar is beschikbaar voor micro soft en kan worden verschaft aan CSP-partners.

In het gedeelte **ondersteuning voor contact opnemen** moet u ook de **ondersteunings-URL** opgeven waar CSP-partners ondersteuning voor uw aanbieding kunnen vinden.

### <a name="marketplace-images"></a>Marketplace-installatie kopieën

In deze sectie kunt u logo's en installatie kopieën opgeven die worden gebruikt wanneer uw aanbieding wordt weer gegeven aan de klant. Alle installatie kopieën moeten de PNG-indeling hebben.

#### <a name="store-logos"></a>Winkel logo's

Geef het logo van uw aanbod op in drie grootten: **klein (48 x 48)** , **Normaal (90 x 90)** en **groot (216 x 216)** .

#### <a name="hero"></a>Hero

De afbeelding van de held is optioneel. Als u een waarde opgeeft, moet deze 815 x 290 pixels meten.

#### <a name="screenshots"></a>Schermopnamen

Scherm afbeeldingen toevoegen die laten zien hoe uw aanbieding werkt. U kunt Maxi maal vijf scherm opnamen toevoegen. Alle scherm afbeeldingen moeten 1280 x 720 pixels zijn.

#### <a name="videos"></a>Video's

U kunt optioneel Maxi maal vijf Video's toevoegen die uw aanbieding aantonen. Deze Video's moeten worden gehost op YouTube en/of Vimeo. Voer voor elke video de naam, de URL en een miniatuur afbeelding van de video in (1280 x 720 pixels).

#### <a name="additional-marketplace-listing-resources"></a>Aanvullende bronnen voor Marketplace-lijst

- [Best practices voor Marketplace-aanbiedingen](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="preview"></a>Preview

Op het tabblad **voor beeld** kunt u een beperkte **Preview-doel groep** definiëren voor het valideren van uw aanbieding voordat u uw aanbieding Live publiceert naar het bredere publiek van de Marketplace.

> [!IMPORTANT]
> U moet **Live go** selecteren voordat uw aanbieding naar de open bare doel groep van Marketplace wordt gepubliceerd nadat u uw aanbieding in Preview hebt gecontroleerd.

Uw preview-doel groep wordt geïdentificeerd door de ID-GUID'S van het Azure-abonnement, gekoppeld aan een optionele beschrijving voor elke.  Geen van deze velden is zichtbaar voor klanten.

Voeg hand matig Maxi maal 10 Azure-abonnements-Id's toe, of 100 bij het uploaden van een CSV-bestand.  Door deze abonnementen toe te voegen, definieert u een doel groep waarvoor preview-toegang tot uw aanbieding is toegestaan voordat deze volledig is gepubliceerd.  Als uw aanbieding al Live is, kunt u nog steeds een preview-doel definiëren voor het testen van eventuele wijzigingen of updates voor uw aanbieding.

>[!Note]
>De preview-doel groep wijkt af van een persoonlijke doel groep. Een preview-doel groep is toegang tot uw aanbieding toegestaan *voordat* Live in de Marketplace wordt gepubliceerd. U kunt er ook voor kiezen om een plan te maken en dit alleen beschikbaar te maken voor een privé doel groep (met behulp van het tabblad plan beschikbaarheid).  Uw preview-doel groep kan alle plannen bekijken en valideren, met inbegrip van abonnementen die alleen beschikbaar zijn voor een privé-doel groep zodra uw aanbieding volledig is gepubliceerd naar de Marketplace.

## <a name="plan-overview"></a>Overzicht van plan

Op het tabblad Overzicht van het **plan** kunt u binnen dezelfde aanbieding verschillende plan opties opgeven. Deze plannen (zoals Sku's in de Cloud Partner-portal) kunnen verschillen qua plan type (oplossings sjabloon versus beheerde toepassing), verdiensten maximaliseren of doel groep.  Configureer ten minste één abonnement om uw aanbieding in Marketplace te vermelden.

Nadat u hebt gemaakt, ziet u de namen van uw plannen, Id's, plan type, Beschik baarheid (openbaar of privé), huidige publicatie status en eventuele beschik bare acties op dit tabblad.

**Acties** die beschikbaar zijn in het **plan overzicht** variëren afhankelijk van de huidige status van uw abonnement en kunnen het volgende omvatten:

* Als de plan status **concept** -concept verwijderen is.
* Als de status van het abonnement **Live** is: het verkoop plan stoppen of privé publiek synchroniseren.

### <a name="create-new-plan"></a>Nieuw plan maken

***Plan-id*** : Maak een unieke plan-id voor elk abonnement in deze aanbieding. Deze ID is zichtbaar voor klanten in de product-URL.  Gebruik alleen kleine letters, alfanumerieke tekens, streepjes of onderstrepingen. Voor deze abonnement-ID zijn Maxi maal 50 tekens toegestaan. Deze ID kan niet worden gewijzigd nadat de maken is geselecteerd.

***Plan naam*** : klanten zien deze naam wanneer ze bepalen welk abonnement binnen uw aanbieding moet worden geselecteerd. Maak in deze aanbieding een unieke aanbiedings naam voor elk abonnement. De naam van het abonnement wordt gebruikt om software-abonnementen te onderscheiden die deel kunnen uitmaken van dezelfde aanbieding (bijvoorbeeld Naam van aanbieding: Windows Server; plannen: Windows Server 2016, Windows Server 2019).

### <a name="plan-setup"></a>Installatie plannen

Op het tabblad **installatie plannen** kunt u de high-level configuratie voor het type abonnement instellen, of deze pakketten van een ander abonnement opnieuw gebruikt en op welke Clouds het plan beschikbaar moet zijn.  Uw antwoorden op dit tabblad beïnvloeden welke velden worden weer gegeven op andere tabbladen voor hetzelfde abonnement.

#### <a name="plan-type"></a>Type abonnement

Zoals beschreven in de [typen Azure-toepassings abonnementen](#types-of-azure-application-plans), geeft u aan of uw plan een oplossings sjabloon of een beheerde toepassing zal bevatten.

#### <a name="this-plan-reuses-packages"></a>In dit plan worden pakketten opnieuw gebruikt

Als u meer dan één abonnement van hetzelfde type hebt en de pakketten identiek zijn, kunt u het abonnement selecteren om **pakketten van een ander abonnement**opnieuw te gebruiken.  Wanneer u deze optie selecteert, kunt u een van de andere plannen van hetzelfde type voor deze aanbieding selecteren om pakketten opnieuw te gebruiken. 

>[!Note]
>Wanneer u pakketten uit een ander abonnement opnieuw gebruikt, verdwijnt het hele tabblad technische configuratie van dit abonnement.  De technische configuratie details van het andere abonnement, inclusief eventuele updates die u in de toekomst maakt, worden ook voor dit abonnement gebruikt. <br> <br> Deze instelling kan ook niet worden gewijzigd nadat dit abonnement is gepubliceerd.

#### <a name="cloud-availability"></a>Beschik baarheid in de Cloud

Dit abonnement moet beschikbaar worden gesteld in ten minste één Cloud. 

Selecteer de **open bare Azure** -optie om ervoor te zorgen dat uw oplossing kan worden geïmplementeerd voor klanten in alle open bare Azure-regio's die Marketplace-integratie hebben.  Meer informatie over [geografische Beschik baarheid](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies).

Selecteer de optie **Azure Government Cloud** om uw oplossing te implementeren in de [Azure Government Cloud](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome), een Cloud community met gecontroleerde toegang voor klanten van de Amerikaanse federale, staats-, lokale of tribale en partners die in aanmerking komen voor deze entiteiten.  Als uitgever bent u verantwoordelijk voor nalevings controles, beveiligings maatregelen en aanbevolen procedures voor het leveren van deze Cloud-community.  Azure Government maakt gebruik van fysiek geïsoleerde data centers en netwerken (alleen in de Verenigde Staten).  Voordat u naar de [Azure Government](https://aka.ms/azuregovpublish)publiceert, raadt micro soft u aan uw oplossing in de omgeving te testen en te valideren, omdat bepaalde eind punten kunnen verschillen. Als u uw oplossing wilt klaarzetten en testen, vraagt u een proef account aan bij deze [koppeling](https://azure.microsoft.com/global-infrastructure/government/request/).

>[!Note]
>Zodra een plan wordt gepubliceerd als beschikbaar in een specifieke Cloud, kan deze Cloud niet worden verwijderd.

**Cloud certificeringen Azure Government**

Deze optie is alleen zichtbaar als **Azure Government Cloud** is geselecteerd onder **Beschik baarheid**in de Cloud.

Azure Government Services verwerkt gegevens die onderworpen zijn aan bepaalde wettelijke voor schriften en vereisten, zoals FedRAMP, NIST 800,171 (DIB), ITAR, IRS 1075, DoD N4 en CJIS.  Als u uw certificeringen voor deze Program ma's wilt behalen, kunt u Maxi maal 100 koppelingen opgeven die uw certificeringen beschrijven.  Deze koppelingen kunnen rechtstreeks links naar uw vermelding in het programma zijn, of een koppeling naar beschrijvingen van uw naleving met hen op uw eigen websites.  Deze koppelingen zijn alleen zichtbaar voor klanten in de cloud van Azure Government.

## <a name="plan-listing"></a>Aanbieding plannen

Op het tabblad **plan vermelding** worden de plannings gegevens weer gegeven die kunnen verschillen tussen verschillende abonnementen voor dezelfde aanbieding.

### <a name="name"></a>Naam

Ingevuld op basis van uw naam die u bij het maken van het abonnement hebt ingesteld.  Deze naam wordt weer gegeven als de titel van dit ' software-abonnement ' dat wordt weer gegeven in de Marketplace.  Mag Maxi maal 100 tekens bevatten.

### <a name="summary"></a>Samenvatting

Geef een korte samen vatting van het software abonnement.  Mag Maxi maal 100 tekens bevatten.

### <a name="description"></a>Beschrijving

Deze beschrijving is een kans om uit te leggen wat dit software plan uniek is en eventuele verschillen tussen andere software plannen binnen uw aanbieding. Mag Maxi maal 2.000 tekens bevatten.

Selecteer **Opslaan** na het volt ooien van deze velden.

## <a name="availability"></a>Beschikbaarheid

Het tabblad **Beschik baarheid** is alleen zichtbaar voor oplossings sjabloon plannen.  U kunt het plan zichtbaar maken voor iedereen, alleen voor specifieke klanten (een persoonlijke doel groep) en of het plan verborgen moet worden voor gebruik door andere oplossings sjablonen of alleen beheerde toepassingen.

### <a name="plan-audience"></a>Doel groep plannen

U kunt elk plan zodanig configureren dat het zichtbaar is voor iedereen of alleen voor een specifieke doel groep van uw keuze. U kunt lidmaatschap van deze beperkte doel groep toewijzen met behulp van Azure-abonnement-Id's.

**Privacy/dit is een privé-abonnement** (optioneel selectie vakje): Schakel dit selectie vakje in om uw abonnement privé en alleen zichtbaar te maken voor de beperkte doel groep van uw keuze. Zodra het is gepubliceerd als een persoonlijk abonnement, kunt u de doel groep bijwerken of ervoor kiezen om het plan voor iedereen beschikbaar te stellen. Zodra een plan wordt gepubliceerd als zichtbaar voor iedereen, moet het zijn zichtbaar voor iedereen. (Het plan kan niet opnieuw worden geconfigureerd als een privé-abonnement).

**Beperkte doel groep (Azure-abonnement-id's)** : wijs de doel groep toe die toegang heeft tot dit privé-abonnement. Toegang wordt toegewezen met Azure-abonnement-Id's met de optie om een beschrijving op te nemen van elk Azure-abonnements-ID toegewezen. U kunt Maxi maal 10 abonnements-Id's toevoegen, of de abonnements-Id's van 20.000-klanten als u een CSV-werk blad bestand importeert.  Azure-abonnements-Id's worden weer gegeven als GUID'S en de letters moeten een kleine letter bevatten.

>[!Note]
>De persoonlijke doel groep (of een beperkt publiek) wijkt af van de preview-doel groep die u hebt gedefinieerd op het tabblad [**Preview**](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#preview) .  Een preview-doel groep is toegang tot uw aanbieding toegestaan *voordat* de aanbieding Live op Marketplace wordt gepubliceerd. Hoewel de aanduiding van een privé-doel groep alleen van toepassing is op een specifiek abonnement, kan de preview-doel groep alle plannen (privé of niet) voor validatie doeleinden bekijken.

### <a name="hide-plan"></a>Plan verbergen

Als uw oplossings sjabloon alleen indirect moet worden geïmplementeerd als er een andere oplossings sjabloon of beheerde toepassing wordt verwezen, schakelt u dit selectie vakje in om de oplossings sjabloon te publiceren, maar deze te verbergen bij klanten die ze rechtstreeks zoeken en bezoeken.

## <a name="pricing-and-availability"></a>Prijzen en beschik baarheid

Het tabblad **prijzen en beschik baarheid** is alleen zichtbaar voor beheerde toepassings abonnementen.  U kunt de markten configureren waarop dit plan beschikbaar is, de prijs per maand van het beheer van de oplossing en of het plan zichtbaar moet worden voor iedereen of alleen voor specifieke klanten (een privé-publiek).

### <a name="markets"></a>Landen

Elk plan moet beschikbaar zijn op ten minste één markt. Schakel het selectie vakje in voor elke markt locatie waar u dit plan beschikbaar wilt maken. Een zoekvak en een knop voor het selecteren van de landen ' BTW geremitteerd ', waarin micro soft namens u de omzet en het gebruik van BTW verdeelt, zijn opgenomen om u te helpen.

Als u al prijzen voor uw abonnement hebt ingesteld in Verenigde Staten dollars (USD) en een andere markt locatie toevoegt, wordt de prijs voor de nieuwe markt berekend op basis van de huidige wissel koersen. De prijs voor elke markt altijd controleren voordat deze wordt gepubliceerd. U kunt de prijzen controleren met behulp van de koppeling export prijzen (XLSX) nadat u uw wijzigingen hebt opgeslagen.

### <a name="pricing"></a>Prijzen

Geef de prijs per maand voor dit abonnement op.  Deze prijs is een aanvulling op de kosten van een Azure-infra structuur of een betalen per gebruik-software die wordt gemaakt door de resources die door deze oplossing worden geïmplementeerd.

Prijzen die in de lokale valuta zijn ingesteld (USD = Verenigde Staten dollar), worden omgezet in de lokale valuta van alle geselecteerde markten met de huidige wissel koersen die beschikbaar zijn tijdens de installatie. Valideer deze prijzen vóór de publicatie door het prijs werk blad te exporteren en de prijs op elke markt te bekijken. Als u aangepaste prijzen wilt instellen op een afzonderlijke markt, wijzigt en importeert u de prijs informatie in het werk blad. 

>[!Note]
>U moet uw prijs wijzigingen eerst opslaan om het exporteren van prijs gegevens in te scha kelen.

Controleer uw prijzen zorgvuldig voordat u deze publiceert. er zijn enkele beperkingen voor wat er kan worden gewijzigd nadat een plan is gepubliceerd.  

>[!Note]
>Zodra een prijs voor een markt in uw abonnement wordt gepubliceerd, kunt u deze later niet meer wijzigen.

### <a name="plan-audience"></a>Doel groep plannen

U kunt elk plan zodanig configureren dat het zichtbaar is voor iedereen of alleen voor een specifieke doel groep van uw keuze. U kunt lidmaatschap van deze beperkte doel groep toewijzen met behulp van Azure-abonnement-Id's.

**Privacy/dit is een privé-abonnement** (optioneel selectie vakje): Schakel dit selectie vakje in om uw abonnement privé en alleen zichtbaar te maken voor de beperkte doel groep van uw keuze. Zodra het is gepubliceerd als een persoonlijk abonnement, kunt u de doel groep bijwerken of ervoor kiezen om het plan voor iedereen beschikbaar te stellen. Zodra een plan wordt gepubliceerd als zichtbaar voor iedereen, moet het zijn zichtbaar voor iedereen. (Het plan kan niet opnieuw worden geconfigureerd als een privé-abonnement).

**Beperkte doel groep (Azure-abonnement-id's)** : wijs de doel groep toe die toegang heeft tot dit privé-abonnement. Toegang wordt toegewezen met Azure-abonnement-Id's met de optie om een beschrijving op te nemen van elk Azure-abonnements-ID toegewezen. U kunt Maxi maal 10 abonnements-Id's toevoegen, of de abonnements-Id's van 20.000-klanten als u een CSV-werk blad bestand importeert.  Azure-abonnements-Id's worden weer gegeven als GUID'S en de letters moeten een kleine letter bevatten.

>[!Note]
>De persoonlijke doel groep (of een beperkt publiek) wijkt af van de preview-doel groep die u hebt gedefinieerd op het tabblad [**Preview**](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#preview) .  Een preview-doel groep is toegang tot uw aanbieding toegestaan *voordat* de aanbieding Live op Marketplace wordt gepubliceerd. Hoewel de aanduiding van een privé-doel groep alleen van toepassing is op een specifiek abonnement, kan de preview-doel groep alle plannen (privé of niet) voor validatie doeleinden bekijken.

## <a name="technical-configuration"></a>Technische configuratie 

Op het tabblad **technische configuratie** kunt u het implementatie pakket uploaden dat klanten in staat stelt om uw abonnement te implementeren.

>[!Note]
>Dit tabblad wordt niet weer gegeven als u dit plan hebt geconfigureerd om pakketten opnieuw te gebruiken van een ander abonnement op het tabblad **installatie plannen** .

### <a name="package-details"></a>Pakket Details

Met de **pakket gegevens** subtabblad kunt u de concept versie van uw technische configuratie bewerken.

***Versie*** : wijs de huidige versie van de technische configuratie toe.  Verhoog deze versie telkens wanneer u een wijziging op deze pagina publiceert. De versie moet de indeling `{integer}.{integer}.{integer}`hebben.

***Pakket bestand*** (`.zip`): dit pakket bevat alle sjabloon bestanden die nodig zijn voor dit plan en eventuele extra resources, verpakt als een `.zip` bestand.

Alle Azure Application plan-pakketten moeten deze twee bestanden in de hoofdmap van een `.zip` archief bevatten:

* Een resource manager-sjabloon bestand met de naam [mainTemplate. json](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  Met deze sjabloon wordt de implementatie van resources in het Azure-abonnement van klanten geautomatiseerd.  Voor voor beelden van Resource Manager-sjablonen raadpleegt u de [Azure Quick Start-sjablonen galerie](https://azure.microsoft.com/documentation/templates/) of de bijbehorende [github: Azure Resource Manager Quick](https://github.com/azure/azure-quickstart-templates) start-sjablonen opslag plaats.

* Een gebruikersinterface definitie voor de Azure-app voor het maken van toepassingen met de naam [createUiDefinition. json](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).

Alle nieuwe aanbiedingen van Azure Application moeten ook een GUID voor [klant gebruik van Azure-partners](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution) bevatten.

### <a name="previously-published-packages"></a>Eerder gepubliceerde pakketten 

Met de **eerder gepubliceerde pakketten** subtabblad kunt u alle gepubliceerde versies van uw technische configuratie weer geven.

## <a name="technical-configuration-managed-application-plans-only"></a>Technische configuratie (alleen beheerde toepassings abonnementen)

Beheerde toepassings abonnementen hebben meer complexiteit op het tabblad **technische configuratie** , naast de velden voor de **versie** en **pakket bestanden** die hierboven worden beschreven. 

### <a name="enable-just-in-time-jit-access"></a>Just-in-time-toegang inschakelen

Selecteer deze optie om just-in-time-toegang (JIT) in te scha kelen voor dit abonnement.  Met JIT-toegang kunt u verhoogde toegang tot de resources van een beheerde toepassing aanvragen voor het oplossen van problemen of onderhoud. U hebt altijd alleen-lezen toegang tot de resources, maar gedurende een bepaalde periode kunt u meer toegang hebben.  Zie [just-in-time-toegang inschakelen en aanvragen voor Azure Managed Applications](https://docs.microsoft.com/azure/managed-applications/request-just-in-time-access)voor meer informatie.  Als u wilt dat gebruikers van uw beheerde toepassing uw account permanente toegang geven, laat u deze optie uitgeschakeld.

>[!Note]
>Zorg ervoor dat u uw `createUiDefinition.json`-bestand bijwerkt om deze functie te kunnen ondersteunen.  

### <a name="deployment-mode"></a>Implementatie modus

Selecteer of u de **volledige** of **incrementele implementatie modus** wilt configureren wanneer u dit plan implementeert: 

* In de **volledige modus**leidt een herimplementatie van de toepassing door de klant tot het verwijderen van resources in de beheerde resource groep als de resources niet zijn gedefinieerd in de `mainTemplate.json`. 
* In de **incrementele modus**blijven bestaande resources ongewijzigd wanneer de toepassing opnieuw wordt geïmplementeerd.

Zie [Azure Resource Manager-implementatie modi](https://docs.microsoft.com/azure/azure-resource-manager/deployment-modes)voor meer informatie over de implementatie modi.

### <a name="notification-endpoint-url"></a>URL van meldings eindpunt

Geef een HTTPS-webhook-eind punt op om meldingen over alle ruwe bewerkingen op beheerde toepassings exemplaren van deze plan versie te ontvangen.

### <a name="customize-allowed-customer-actions"></a>Toegestane klant acties aanpassen

Selecteer deze optie om op te geven welke acties klanten kunnen uitvoeren op de beheerde resources naast de acties voor '`*/read`' die standaard beschikbaar zijn. 

Geef een lijst van de aanvullende acties die u uw klant wilt laten uitvoeren, gescheiden door punt komma's.  Zie voor meer informatie [over het weigeren van toewijzingen voor Azure-resources](https://docs.microsoft.com/azure/role-based-access-control/deny-assignments).  Zie Azure Resource Manager-bewerkingen voor de [resource provider](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations)voor beschik bare acties. Als u bijvoorbeeld wilt toestaan dat gebruikers virtuele machines opnieuw opstarten, moet u `Microsoft.Compute/virtualMachines/restart/action` toevoegen aan de toegestane acties.

### <a name="global-azure--azure-government-cloud"></a>Wereld wijd Azure/Azure Government-Cloud

Geef aan wie beheer toegang moet hebben tot deze beheerde toepassing in elke ondersteunde Cloud.  Gebruikers, groepen of toepassingen waaraan u machtigingen wilt verlenen voor de beheerde resource groep, worden geïdentificeerd aan de hand van Azure Active Directory (AAD)-identiteiten.

***Azure Active Directory Tenant*** -id: de Aad-Tenant-id (ook wel Directory-id genoemd) die de identiteiten bevat van de gebruikers, groepen of toepassingen waaraan u machtigingen wilt verlenen.  U vindt uw AAD-Tenant-ID op het Azure Portal in [Eigenschappen voor Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties).

***Autorisaties*** : voeg de Azure Active Directory object-id toe van de gebruiker, groep of toepassing waaraan u machtigingen wilt verlenen voor de beheerde resource groep. Identificeer de gebruiker op basis van de principal-ID, die u kunt vinden op de [blade Azure Active Directory gebruikers op de Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers).

Selecteer voor elke principal een van de ingebouwde Azure AD-rollen uit de lijst (eigenaar of bijdrager). De rol die u selecteert, beschrijft de machtigingen die de principal heeft op de resources in het abonnement van de klant. Zie [Ingebouwde rollen voor Azure-resources](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) voor meer informatie.  Zie [aan de slag met RBAC in de Azure Portal](https://docs.microsoft.com/azure/role-based-access-control/overview)voor meer informatie over op rollen gebaseerd toegangs beheer (RBAC).

>[!Note]
>Hoewel u Maxi maal 100 autorisaties per Cloud kunt toevoegen, is het over het algemeen eenvoudiger om een Active Directory gebruikers groep te maken en de ID op te geven in de principal-ID.  Hiermee kunt u meer gebruikers toevoegen aan de beheer groep nadat het plan is geïmplementeerd en de nood zaak voor het bijwerken van het plan beperken om meer autorisaties toe te voegen.

### <a name="policy-settings"></a>Beleidsinstellingen

Pas [Azure-beleid](https://docs.microsoft.com/azure/governance/policy/overview) toe op uw beheerde toepassing om nalevings vereisten voor de geïmplementeerde oplossing op te geven.  Zie [Voorbeelden van Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/index) voor beleidsdefinities en de indeling van de parameterwaarden.  U kunt Maxi maal vijf beleids regels configureren, en slechts één exemplaar van elke beleids optie.  Voor sommige beleids regels zijn aanvullende para meters vereist.  De standaard-SKU is vereist voor controle beleid.  De naam van het beleid is beperkt tot 50 tekens.

## <a name="co-sell"></a>Gezamenlijk verkopen

Het is volledig optioneel om informatie op het tabblad verkopen op te geven voor het publiceren van uw aanbieding. Het is vereist voor het vergemakkelijkt van de verkoop van Ready en de status van de voor bereidingen op de IP-mede verkoop. De gegevens die u verstrekt, worden door micro soft Sales teams gebruikt om meer te weten te komen over uw oplossing bij de evaluatie van de geschiktheid voor klanten behoeften. Het is niet rechtstreeks beschikbaar voor klanten.

Zie voor meer informatie over het invullen van dit tabblad de [optie co-sell in Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-co-sell).

## <a name="test-drive"></a>Test drive

Op het tabblad **test station** kunt u een demonstratie (of "test drive") instellen waarmee klanten uw aanbieding kunnen proberen voordat deze zich aanmeldt. Meer informatie in het artikel [Wat is een test drive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)  Als u geen test drive voor uw aanbieding wilt opgeven, keert u terug naar de pagina voor het instellen van de **aanbieding** en schakelt u **Test Drive inschakelen**in.

### <a name="technical-configuration"></a>Technische configuratie

Azure-toepassingen gebruiken inherent het type Azure Resource Manager test drive.  Zie [technische configuratie voor Azure Resource Manager test drive](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-customer-engagement-offer#technical-configuration-for-azure-resource-manager-test-drive) voor meer informatie.

### <a name="deployment-subscription-details"></a>Details van implementatie abonnement

Als u het test station namens u wilt implementeren, moet u een afzonderlijk, uniek Azure-abonnement maken en opgeven. (Niet vereist voor Power BI test stations).

- **Azure-abonnements-id** (vereist voor Azure Resource Manager en Logic apps): Voer de abonnements-id in om toegang te verlenen tot uw Azure-account services voor rapportage en facturering van resource gebruik. Het is raadzaam om [een afzonderlijk Azure-abonnement te maken](https://docs.microsoft.com/azure/billing/billing-create-subscription) dat u voor test stations kunt gebruiken als u er nog geen hebt. U kunt uw Azure-abonnements-ID vinden door u aan te melden bij de [Azure Portal](https://portal.azure.com/) en te navigeren naar het tabblad **abonnementen** van het menu aan de linkerkant. Als u het tabblad selecteert, wordt uw abonnements-ID weer gegeven (bijvoorbeeld "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **Azure AD-Tenant-id** (vereist): voer uw Azure Active Directory (AD) [Tenant-id](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)in. Als u deze ID wilt vinden, meldt u zich aan bij de [Azure Portal](https://portal.azure.com/), selecteert u het tabblad Active Directory in het menu aan de linkerkant, selecteert u * * eigenschappen en zoekt u naar de weer gegeven **Directory-id** (bijvoorbeeld 50c464d3-4930-494c-963c-1e951d15360e). U kunt ook de Tenant-ID van uw organisatie opzoeken met uw domein naam-URL op: [https://www.whatismytenantid.com](https://www.whatismytenantid.com).

- **Naam van Azure AD-Tenant** (vereist voor dynamische 365): Voer de naam van uw Azure Active Directory (AD) in. Als u deze naam wilt vinden, meldt u zich aan bij de [Azure Portal](https://portal.azure.com/). in de rechter bovenhoek wordt de naam van de Tenant vermeld onder uw account naam.

- **Azure AD-App-ID** (vereist): voer uw Azure Active Directory (AD) [-toepassings-id](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)in. Als u deze ID wilt vinden, meldt u zich aan bij de [Azure Portal](https://portal.azure.com/), selecteert u het tabblad Active Directory in het menu aan de linkerkant, selecteert u **app-registraties**en zoekt u naar het weer gegeven **toepassings-id** -nummer (bijvoorbeeld 50c464d3-4930-494c-963c-1e951d15360e).

- **Azure AD-App-client geheim** (vereist): Voer uw Azure AD-toepassings [client geheim](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)in. Als u deze waarde wilt vinden, meldt u zich aan bij de [Azure Portal](https://portal.azure.com/). Selecteer in het linkermenu het tabblad **Azure Active Directory** , selecteer **app-registraties**en selecteer vervolgens uw test drive-app. Selecteer vervolgens **certificaten en geheimen**, selecteer **Nieuw client geheim**, voer een beschrijving in, selecteer **nooit** onder **verlooptd**en kies vervolgens **toevoegen**. Zorg ervoor dat u de waarde kopieert. (Verlaat de pagina niet voordat u de waarde hebt gekopieerd, anders hebt u geen toegang tot de waarde.)

Vergeet niet om te **slaan** voordat u verdergaat met de volgende sectie.

### <a name="test-drive-listings-optional"></a>Vermeldingen voor het test station (optioneel)

De optie voor het **testen van stations** die is gevonden op het tabblad **test station** bevat de talen (en markten) waar uw test drive beschikbaar is. op Verenigde Staten dit moment is de enige locatie beschikbaar. Daarnaast wordt op deze pagina de status weer gegeven van de taalspecifieke vermelding en de datum/tijd waarop deze is toegevoegd. U moet de test drive Details (beschrijving, gebruikers handleiding, Video's, enzovoort) definiëren voor elke taal/markt.

- **Beschrijving** (vereist): Geef een beschrijving van uw test drive, wat wordt aangetoond, wat de gebruiker kan doen om te experimenteren met, functies om te verkennen en alle relevante informatie om de gebruiker te helpen bepalen of u uw aanbieding moet verkrijgen. In dit veld kunnen Maxi maal 3.000 tekens tekst worden ingevoerd. 

- **Toegangs gegevens** (vereist voor Azure Resource Manager en logische test stations): Leg uit wat een klant moet weten om deze test drive te kunnen openen en gebruiken. Door loop een scenario voor het gebruik van uw aanbieding en precies wat de klant moet weten voor toegang tot de functies in de test drive. In dit veld kunnen Maxi maal 10.000 tekens tekst worden ingevoerd.

- **Gebruikers handleiding** (vereist): een diep gaande overzicht van uw test drive ervaring. De gebruikers handleiding moet betrekking hebben op precies wat u wilt dat de klant het test drive verkrijgt en zich als referentie voor eventuele vragen bevindt. Het bestand moet een PDF-indeling hebben en een naam hebben (255 tekens Max) na het uploaden.

- **Video's: Video's toevoegen** (optioneel): Video's kunnen worden geüpload naar YouTube of Vimeo en hiernaar wordt verwezen met een koppeling en een miniatuur afbeelding (533 x 324 pixels), zodat een klant een overzicht van de informatie kan bekijken om hen meer inzicht te geven in de test drive, waaronder hoe u de functies van uw aanbieding kunt gebruiken en inzicht krijgt in scenario's die hun voor delen accentueren.
  - **Naam** (vereist)
  - **URL (alleen YouTube of Vimeo)** (vereist)
  - **Miniatuur (533 x 324 px)** : het afbeeldings bestand moet de PNG-indeling hebben.

Selecteer **Opslaan** na het volt ooien van deze velden.

## <a name="publish"></a>Publiceren

### <a name="submit-offer-to-preview"></a>Aanbieding verzenden naar Preview

Zodra u alle vereiste delen van de aanbieding hebt voltooid, selecteert u **publiceren** in de rechter bovenhoek van de portal. U wordt omgeleid naar de pagina **controleren en publiceren** . 

Als dit de eerste keer is dat u deze aanbieding publiceert, kunt u het volgende doen:

- Bekijk de voltooiings status voor elke sectie van de aanbieding.
    - *Niet gestart* : de sectie is niet gerakend en moet worden voltooid.
    - *Onvolledig* : de sectie bevat fouten die moeten worden hersteld of waarvoor meer informatie moet worden verstrekt. Ga terug naar de sectie (s) en werk deze bij.
    - *Voltooid* : de sectie is voltooid, alle vereiste gegevens zijn opgegeven en er zijn geen fouten. Alle secties van de aanbieding moeten een volledige status hebben voordat u de aanbieding kunt indienen.
- Geef test instructies op het certificerings team om ervoor te zorgen dat uw app correct wordt getest, naast eventuele aanvullende notities die nuttig zijn voor de uitleg van uw app.
- Verzend de aanbieding voor publicatie door **verzenden**te selecteren. We sturen u een e-mail om u te laten weten wanneer een preview-versie van de aanbieding beschikbaar is om te controleren en goed te keuren. Ga terug naar het partner centrum en selecteer **Go-Live** voor de aanbieding om uw aanbieding te publiceren naar het open bare (of als u een privé-aanbieding naar de persoonlijke doel groep hebt).

### <a name="errors-and-review-feedback"></a>Fouten en feedback bekijken

De **hand matige validatie** stap in het publicatie proces vertegenwoordigt een uitgebreide beoordeling van uw aanbieding en de bijbehorende technische activa (met name de Azure Resource Manager-sjabloon), problemen worden doorgaans gepresenteerd als pull-aanvraag (PR)-koppelingen. Zie [afhandeling van feedback](./azure-apps-review-feedback.md)bekijken voor meer informatie over het weer geven en reageren op deze pull.

Als er fouten zijn opgetreden in een of meer van de publicatie stappen, moet u deze corrigeren en uw aanbieding opnieuw publiceren.

## <a name="next-steps"></a>Volgende stappen

- [Een bestaande aanbieding bijwerken in Commerciële Marketplace](./update-existing-offer.md)
