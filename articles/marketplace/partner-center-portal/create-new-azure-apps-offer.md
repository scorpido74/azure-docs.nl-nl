---
title: Een nieuwe Azure Apps-aanbieding maken op de commerciële marktplaats
description: Een nieuwe Azure Apps-aanbieding maken voor vermelding of verkoop in het Azure Marketplace-, AppSource- of Via het CSP-programma (Cloud Solution Provider) met behulp van de Commercial Marketplace-portal in Microsoft Partner Center.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: a62551b31c51836dfa50534ee6db907c8a4e111a
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730622"
---
# <a name="create-an-azure-application-offer"></a>Een Azure-toepassingsaanbieding maken

De stappen voor het publiceren van een Azure-toepassingsaanbieding in commerciële marktplaatsen worden hier beschreven.

## <a name="azure-application-offer-type"></a>Aanbiedingstype Azure-toepassingen

In dit onderwerp worden basisprincipes beschreven over Azure-toepassingsaanbiedingen.  U moet bekend zijn met deze concepten voordat u begint met het proces van het publiceren van een nieuwe Azure-toepassingsaanbieding in de Marketplace.

### <a name="publishing-overview"></a>Publicatieoverzicht

De [video-oplossingen voor het bouwen van oplossingen en beheerde toepassingen voor de Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603) is een inleiding tot het type Azure-toepassingsaanbieding:

* Welke aanbiedingstypen zijn beschikbaar;
* Welke technische activa zijn vereist;
* Een Azure Resource Manager-sjabloon maken;
* De gebruikersinterface van de app ontwikkelen en testen;
* Hoe de app-aanbieding te publiceren;
* De aanvraagcontroleprocedure.

### <a name="types-of-azure-application-plans"></a>Typen Azure-toepassingsplannen

Er zijn twee soorten Azure-toepassingsplannen, beheerde toepassingen en oplossingssjablonen.

* **Oplossingssjabloon** is een van de belangrijkste manieren om een oplossing in de Marketplace te publiceren. Dit plantype wordt gebruikt wanneer uw oplossing extra implementatie- en configuratieautomatisering vereist buiten één virtuele machine (VM).  Met een oplossingssjabloon u het leveren van meer dan één resource automatiseren, waaronder VM's, netwerken en opslagbronnen om complexe IaaS-oplossingen te bieden.  Zie de azure [resource manager-documentatie](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) voor meer informatie over het maken van oplossingssjablonen.

* **Beheerde toepassing** is vergelijkbaar met oplossingssjablonen, met één belangrijk verschil. In een beheerde toepassing worden de resources geïmplementeerd vanuit een resourcegroep die wordt beheerd door de uitgever van de app. De resourcegroep is opgenomen in het abonnement van de consument, maar een identiteit in de tenant van de uitgever heeft toegang tot de resourcegroep. De uitgever bepaalt de kosten voor de voortdurende ondersteuning van de oplossing. Gebruik beheerde toepassingen om eenvoudig volledig beheerde, turnkey-applicaties aan uw klanten te bouwen en te leveren.  Zie het overzicht van [beheerde toepassingen](https://docs.microsoft.com/azure/managed-applications/overview)voor Azure voor meer informatie over de voordelen en typen beheerde toepassingen.

Alle Azure-toepassingen bevatten ten minste twee `.zip` bestanden in de hoofdmap van een archief:

* Een resourcebeheersjabloonbestand met de naam [mainTemplate.json](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  Dit is de sjabloon die de resources definieert die moeten worden geïmplementeerd in het Azure-abonnement van de klant.  Zie voor voorbeelden van Resource Manager-sjablonen de [azure quickstartsjablonengalerij](https://azure.microsoft.com/resources/templates/) of de bijbehorende [GitHub: Azure Resource Manager Quickstart Templates](https://github.com/azure/azure-quickstart-templates) repo.

* Een definitie van de gebruikersinterface voor de ervaring voor het maken van Azure-toepassingen met de naam [createUiDefinition.json](https://docs.microsoft.com/azure/managed-applications/create-uidefinition-overview).  In de gebruikersinterface geeft u elementen op waarmee consumenten parameterwaarden kunnen opgeven.

Alle nieuwe Azure-toepassingsaanbiedingen moeten een [AZURE-partnerklantgebruiksvermeldingGUID](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution)bevatten.

### <a name="before-you-begin"></a>Voordat u begint

Bekijk de volgende Azure-toepassingsdocumentatie, die Quickstarts, Tutorials en Voorbeelden bevat.

* [Azure Resource Manager-sjablonen begrijpen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)
* Quickstarts:

    * [Azure Quickstart-sjablonen](https://azure.microsoft.com/documentation/templates/)
    * [GitHub Azure Quickstart-sjablonen](https://github.com/azure/azure-quickstart-templates)
    * [Toepassingsdefinitie publiceren](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
    * [Servicecatalogus-app implementeren](https://docs.microsoft.com/azure/managed-applications/deploy-service-catalog-quickstart)

* Zelfstudies:

    * [Definitiebestanden maken](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
    * [Marketplace-toepassing publiceren](https://docs.microsoft.com/azure/managed-applications/publish-marketplace-app)

* Monsters:

    * [Azure-CLI](https://docs.microsoft.com/azure/managed-applications/cli-samples)
    * [Azure PowerShell](https://docs.microsoft.com/azure/managed-applications/powershell-samples)
    * [Beheerde toepassingsoplossingen](https://docs.microsoft.com/azure/managed-applications/sample-projects)

### <a name="fundamentals-in-technical-knowledge"></a>Fundamenten in technische kennis

Het ontwerpen, bouwen en testen van deze assets kost tijd en vereist technische kennis van zowel het Azure-platform als de technologieën die worden gebruikt om het aanbod te bouwen.

Uw engineeringteam moet kennis hebben over de volgende Microsoft-technologieën:

* Basiskennis van [Azure Services](https://azure.microsoft.com/services/).
* [Azure-toepassingen ontwerpen en ontwerpen.](https://azure.microsoft.com/solutions/architecture/)
* Werkkennis van [Azure Virtual Machines,](https://azure.microsoft.com/services/virtual-machines/) [Azure Storage](https://azure.microsoft.com/services/?filter=storage#storage)en Azure [Networking](https://azure.microsoft.com/services/?filter=networking#networking).
* Werkkennis van [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/).
* Werkkennis van [JSON](https://www.json.org/).

### <a name="suggested-tools"></a>Voorgestelde hulpprogramma's

Kies een of beide van de volgende scriptomgevingen om uw Azure-toepassing te beheren:

* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
* [Azure-CLI](https://docs.microsoft.com/cli/azure)

We raden u aan de volgende tools toe te voegen aan uw ontwikkelomgeving:

* [Azure Opslagverkenner](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* [Visual Studio Code](https://code.visualstudio.com/) met de volgende extensies:
    * Extensie: [Azure Resource Manager-hulpprogramma's](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    * Uitbreiding: [Verfraaien](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    * Uitbreiding: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

U de beschikbare hulpprogramma's bekijken op de pagina [Azure Developer Tools.](https://azure.microsoft.com/tools/)  Ook als u Visual Studio gebruikt, de [Visual Studio Marketplace.](https://marketplace.visualstudio.com/)

## <a name="create-an-azure-application-offer"></a>Een Azure-toepassingsaanbieding maken

Voordat u een Azure-toepassingsaanbieding maken, moet u eerst [een Partnercenter-account maken](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) en het [dashboard voor commerciële marktplaatsopenen](https://partner.microsoft.com/dashboard/commercial-marketplace/offers), waarbij het tabblad **Overzicht** is geselecteerd.

>[!Note]
>Zodra een aanbieding is gepubliceerd, worden bewerkingen van de aanbieding in het Partnercenter alleen bijgewerkt in het systeem en de etalages na het opnieuw publiceren.  Zorg ervoor dat u de aanbieding voor publicatie indient nadat u wijzigingen hebt aangebracht.

### <a name="create-a-new-offer"></a>Een nieuwe aanbieding maken

Selecteer de knop **+ Nieuwe aanbieding** en selecteer vervolgens het **menu-item Azure Application.** Het dialoogvenster **Nieuwe aanbieding** wordt weergegeven.

### <a name="offer-id-and-alias"></a>Aanbiedings-ID en alias

* **Aanbiedings-id:** een unieke id voor elke aanbieding in uw account. Deze id is zichtbaar voor klanten in het URL-adres voor de marketplace-aanbieding en Azure Resource Manager-sjablonen (indien van toepassing). <br> <br> Uw aanbiedings-id moet alfanumerieke tekens voor kleine letters zijn (inclusief koppeltekens en underscores, maar geen witruimte). Het is beperkt tot 50 tekens en kan niet worden gewijzigd nadat u Maken hebt geselecteerd. <br> <br> Als u hier `test-offer-1` bijvoorbeeld invoert, is `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`de URL van de aanbieding . 

* **Aanbiedingsalias**: De naam die wordt gebruikt om te verwijzen naar de aanbieding binnen het Partnercentrum. Deze naam wordt niet gebruikt in de markt en verschilt van de aanbiedingsnaam en andere waarden die aan klanten worden getoond. Deze waarde kan niet worden gewijzigd nadat u Maken hebt **geselecteerd.**

Zodra u de **aanbiedings-id** en **de alias Aanbieding**hebt ingevoerd, selecteert u **Maken**. U dan aan alle andere onderdelen van uw aanbod werken.

## <a name="offer-setup"></a>Instelling voor aanbieding

Op de pagina **Installatie aanbieding** wordt om de volgende informatie gevraagd. Zorg ervoor dat u **Opslaan** selecteert nadat u deze velden hebt voltooid.

### <a name="test-drive"></a>Proefrit

Een testrit is een geweldige manier om uw aanbod aan potentiële klanten te presenteren door hen de mogelijkheid te geven om 'proberen voordat u koopt', wat resulteert in een verhoogde conversie en het genereren van hooggekwalificeerde leads. [Meer informatie over testritten.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

Als u een testrit wilt inschakelen, schakelt u het selectievakje **Een teststation inschakelen in.** U moet dan een demonstratieomgeving configureren in de technische configuratie van de [testdrive,](#types-of-azure-application-plans) om klanten uw aanbieding gedurende een bepaalde periode te laten proberen. 

>[!Note]
>Omdat alle Azure-toepassingen zijn geïmplementeerd met behulp van een Azure Resource Manager-sjabloon, is het enige type teststation dat kan worden geconfigureerd voor een Azure-toepassing een [op Azure Resource Manager gebaseerdteststation](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

#### <a name="additional-test-drive-resources"></a>Aanvullende teststationbronnen

- [Technische best practices voor testdrive](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Best practices voor testdrive marketing](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Overzicht van teststation Één pager](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>Leadbeheer verbinden

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Zie [Overzicht van leadbeheer](./commercial-marketplace-get-customer-leads.md)voor meer informatie.

Vergeet niet om op te **slaan** voordat je doorgaat naar de volgende sectie!

## <a name="properties"></a>Eigenschappen

Op de pagina **Eigenschappen** u de categorieën en bedrijfstakken definiëren die worden gebruikt om uw aanbieding op de marktplaats, uw app-versie en de juridische contracten ter ondersteuning van uw aanbieding te groeperen. Selecteer **Opslaan** nadat u deze pagina hebt voltooid.

### <a name="category"></a>Categorie

Selecteer minimaal één en maximaal drie categorieën, die worden gebruikt om uw aanbieding in de juiste zoekgebieden op marktplaats te plaatsen. Zorg ervoor dat u in de aanbiedingsbeschrijving roept hoe uw aanbieding deze categorieën ondersteunt. 

### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Standaardcontract voor de commerciële marktplaats van Microsoft

[!INCLUDE [Commercial marketplace Standard Contract](./includes/marketplace-contract-text.md)]

## <a name="offer-listing"></a>Aanbieding aanbieden

Op de pagina Aanbiedingsaanbieding worden de talen weergegeven waarin uw aanbieding wordt weergegeven. Momenteel is **Engels (Verenigde Staten)** de enige beschikbare optie.

U moet marktplaatsdetails (aanbiedingsnaam, beschrijving, afbeeldingen, enz.) voor elke taal/markt definiëren. Selecteer de taal/marktnaam om deze informatie te verstrekken.

> [!NOTE]
> Aanbieding sominhoud (zoals de beschrijving, documenten, screenshots, gebruiksvoorwaarden, enz.) is niet verplicht om in het Engels te zijn, zolang de aanbiedingsbeschrijving begint met de zinsnede: "Deze toepassing is alleen beschikbaar in [niet-Engelse taal]." Het is ook aanvaardbaar om een *URL voor nuttige koppelingen* op te geven om inhoud aan te bieden in een andere taal dan die welke wordt gebruikt in de inhoud van de aanbiedingsvermelding.

### <a name="name"></a>Naam

De naam die u hier invoert, wordt aan klanten weergegeven als de titel van uw aanbiedingsvermelding. Dit veld wordt vooraf ingevuld met de tekst die u hebt ingevoerd voor **Alias Aanbieding** toen u de aanbieding maakte, maar u deze waarde wijzigen. Deze naam kan een handelsmerk hebben (en u handelsmerk- of auteursrechtsymbolen bevatten). De naam mag niet meer dan 50 tekens bevatten en kan geen emoji's bevatten.

### <a name="summary"></a>Samenvatting

Geef een korte beschrijving van uw aanbieding (maximaal 100 tekens), die kan worden gebruikt in de zoekresultaten van marketplaces.

### <a name="long-summary"></a>Lange samenvatting

Geef een langere beschrijving van uw aanbieding (tot 256 tekens). De beschrijving kan worden gebruikt in de zoekresultaten van marktplaatsen.

### <a name="description"></a>Beschrijving

Geef een langere beschrijving van uw aanbieding (tot 3.000 tekens). Deze beschrijving wordt weergegeven aan klanten in het overzicht van de marktplaatsvermelding. Neem de waardepropositie van uw aanbieding, belangrijke voordelen, categorie- en/of brancheorganisaties, in-app aankoopmogelijkheden en eventuele vereiste informatie op. 

Enkele tips voor het schrijven van uw beschrijving:  

- Beschrijf duidelijk de waardepropositie van uw aanbieding in de eerste paar zinnen van uw beschrijving. Neem de volgende items op in uw waardepropositie:
  - Beschrijving van het product
  - Het type gebruiker dat profiteert van het product
  - Behoeften van de klant of pijn die het product aanpakt
- Houd er rekening mee dat de eerste paar zinnen kunnen worden weergegeven in de resultaten van zoekmachines.  
- Vertrouw niet op functies en functionaliteit om uw product te verkopen. Richt u in plaats daarvan op de waarde die u levert.  
- Gebruik zoveel mogelijk gebruik van branchespecifieke woordenschat of op voordelen gebaseerde formuleringen. 
- Overweeg HTML-tags te gebruiken om uw beschrijving op te maken en deze aantrekkelijker te maken.

### <a name="search-keywords"></a>Zoektrefwoorden

U optioneel maximaal drie zoekzoekwoorden invoeren om klanten te helpen uw aanbieding op de marktplaats te vinden. Voor de beste resultaten, probeer deze zoekwoorden te gebruiken in uw beschrijving ook.

### <a name="support-urls"></a>URL's ondersteunen

In deze sectie u koppelingen aanbieden om klanten meer inzicht te geven in uw aanbieding.

#### <a name="privacy-policy-url"></a>URL van het privacybeleid

Voer de URL in voor het privacybeleid van uw organisatie. U bent verantwoordelijk voor het waarborgen dat uw app voldoet aan de privacywet- en regelgeving en voor het verstrekken van een geldig privacybeleid.

#### <a name="useful-links"></a>Handige koppelingen

Geef optionele aanvullende online documenten over uw oplossing.  Voeg extra handige koppelingen toe door op **+ Een koppeling toevoegen te**klikken .

### <a name="contacts"></a>Contactpersonen

In deze sectie moet u de naam, e-mail en het telefoonnummer opgeven voor een **contactpersoon voor ondersteuning** en een contactpersoon voor **engineering.** Deze informatie wordt niet aan klanten weergegeven, maar is beschikbaar voor Microsoft en kan worden verstrekt aan CSP-partners.

In de sectie **Contactpersoon voor ondersteuning** moet u ook de URL voor **ondersteuning** opgeven waar CSP-partners ondersteuning voor uw aanbieding kunnen vinden.

### <a name="marketplace-images"></a>Marketplace-afbeeldingen

In deze sectie u logo's en afbeeldingen verstrekken die worden gebruikt bij het weergeven van uw aanbieding aan de klant. Alle afbeeldingen moeten in .png-indeling zijn.

#### <a name="store-logos"></a>Winkellogo's

Geef het logo van uw aanbieding in drie maten: **klein (48 x 48)**, **medium (90 x 90)** en **Groot (216 x 216)**.

#### <a name="hero"></a>Held

De hero afbeelding is optioneel. Als u er een opgeeft, moet deze 815 x 290 pixels meten.

#### <a name="screenshots"></a>Schermopnamen

Voeg schermafbeeldingen toe die laten zien hoe uw aanbieding werkt. U maximaal vijf screenshots toevoegen. Alle screenshots moeten 1280 x 720 pixels zijn.

#### <a name="videos"></a>Video's

Je optioneel maximaal vijf video's toevoegen die je aanbod demonstreren. Deze video's moeten worden gehost op YouTube en/of Vimeo. Voer voor elke video de naam, de URL en een miniatuurafbeelding van de video (1280 x 720 pixels) in.

#### <a name="additional-marketplace-listing-resources"></a>Aanvullende bronnen voor marktplaatsvermeldingen

- [Aanbevolen procedures voor aanbiedingen voor marktplaatsaanbiedingen](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="preview"></a>Preview

Op het tabblad **Voorbeeld** u een beperkte **preview-doelgroep** definiëren voor het valideren van uw aanbieding voordat u uw aanbieding live publiceert aan de bredere doelgroep.

> [!IMPORTANT]
> Je moet **Live gaan** selecteren voordat je aanbieding live wordt gepubliceerd voor het publiek op de marktplaats nadat je je aanbieding hebt gecontroleerd in Preview.

Uw preview-doelgroep wordt geïdentificeerd door AZURE-guid's voor azure-abonnementen, gekoppeld aan een optionele beschrijving voor elk.  Geen van deze velden zijn zichtbaar voor klanten.

Voeg maximaal 10 Azure-abonnements-id's handmatig toe of maximaal 100 als u een CSV-bestand uploadt.  Door deze abonnementen toe te voegen, definieert u een doelgroep die toegang krijgt tot uw aanbieding voordat deze volledig wordt gepubliceerd.  Als je aanbieding al live is, kun je nog steeds een preview-doelgroep definiëren voor het testen van wijzigingen of updates van je aanbieding.

>[!Note]
>De preview-doelgroep verschilt van een privépubliek. Een preview-doelgroep krijgt toegang tot uw aanbieding *voordat* deze live in de marketplaces wordt gepubliceerd. U er ook voor kiezen om een abonnement te maken en het alleen beschikbaar te maken voor een privépubliek (via het tabblad Beschikbaarheid van het abonnement).  Je preview-doelgroep kan alle abonnementen bekijken en valideren, inclusief abonnementen die alleen beschikbaar zijn voor een privépubliek zodra je aanbieding volledig is gepubliceerd op de marktplaats.

## <a name="plan-overview"></a>Overzicht van plannen

Met het tabblad **Overzicht plannen** u binnen dezelfde aanbieding verschillende abonnementsopties bieden. Deze plannen (aangeduid als SKU's in de Cloud Partner Portal) kunnen verschillen in termen van plantype (oplossingssjabloon versus beheerde toepassing), inkomsten genereren of doelgroep.  Configureer ten minste één abonnement om uw aanbieding op de marktplaats weer te geven.

Zodra u is gemaakt, ziet u uw plannamen, id's, plantype, beschikbaarheid (Openbaar of Privé), de huidige publicatiestatus en alle beschikbare acties op dit tabblad.

**Acties** die beschikbaar zijn in het **planoverzicht** variëren afhankelijk van de huidige status van uw abonnement en kunnen het volgende omvatten:

* Als de planstatus **Concept** is – Concept verwijderen.
* Als de planstatus **Live** is : Stop met verkopen of Privépubliek synchroniseren.

### <a name="create-new-plan"></a>Nieuw plan maken

***Plan ID*** - Maak een unieke plan-ID voor elk plan in deze aanbieding. Deze ID is zichtbaar voor klanten in de product-URL.  Gebruik alleen kleine letters, alfanumerieke tekens, streepjes of underscores. Voor deze plan-ID zijn maximaal 50 tekens toegestaan. Deze id kan niet worden gewijzigd na het selecteren van maken.

***Naam van het abonnement*** - Klanten zien deze naam wanneer ze beslissen welk plan ze binnen uw aanbieding willen selecteren. Maak een unieke aanbiedingsnaam voor elk abonnement in deze aanbieding. De naam van het plan wordt gebruikt om softwareplannen te differentiëren die deel kunnen uitmaken van hetzelfde aanbod (bijv. Aanbiedingsnaam: Windows Server; abonnementen: Windows Server 2016, Windows Server 2019).

### <a name="plan-setup"></a>Installatie plannen

Met het tabblad **Plan-instelling** u de configuratie op hoog niveau instellen voor het type abonnement, of het pakketten uit een ander abonnement opnieuw gebruikt en in welke clouds het plan beschikbaar moet zijn.  Uw antwoorden op dit tabblad zijn van invloed op welke velden worden weergegeven op andere tabbladen voor hetzelfde plan.

#### <a name="plan-type"></a>Type plannen

Zoals beschreven in de [typen Azure-toepassingsplannen,](#types-of-azure-application-plans)selecteert u of uw abonnement een oplossingssjabloon of een beheerde toepassing bevat.

#### <a name="this-plan-reuses-packages"></a>Dit plan hergebruikt pakketten

Als u meer dan één abonnement van hetzelfde type hebt en de pakketten identiek zijn tussen deze plannen, u **dit abonnement opnieuw pakketten uit een ander abonnement**gebruiken.  Wanneer u deze optie selecteert, u een van de andere plannen van hetzelfde type selecteren voor deze aanbieding om pakketten van te hergebruiken. 

>[!Note]
>Wanneer u pakketten van een ander abonnement hergebruikt, verdwijnt het volledige tabblad Technische configuratie uit dit abonnement.  De technische configuratiedetails van het andere abonnement, inclusief eventuele updates die u in de toekomst maakt, worden ook voor dit plan gebruikt. <br> <br> Deze instelling kan ook niet worden gewijzigd zodra dit plan is gepubliceerd.

#### <a name="cloud-availability"></a>Beschikbaarheid in de cloud

Dit plan moet beschikbaar worden gesteld in ten minste één cloud. 

Selecteer de optie **Openbaar Azure** om uw oplossing beschikbaar te maken voor klanten in alle openbare Azure-regio's met Marketplace-integratie.  Meer informatie over [geografische beschikbaarheid](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies).

Selecteer de azure **government cloud-optie** om uw oplossing beschikbaar te maken in de [Azure Government Cloud](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome), een cloud voor overheidscommunity's met gecontroleerde toegang voor klanten uit de Amerikaanse federale, staats-, lokale of tribale en partners die in aanmerking komen om deze entiteiten te bedienen.  U, als uitgever, bent verantwoordelijk voor nalevingscontroles, beveiligingsmaatregelen en aanbevolen procedures om deze cloudcommunity te bedienen.  Azure Government maakt gebruik van fysiek geïsoleerde datacenters en netwerken (alleen in de VS).  Voordat u publiceert naar de [Azure Government,](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)raadt Microsoft u aan uw oplossing in de omgeving te testen en te valideren, omdat bepaalde eindpunten kunnen verschillen. Als u uw oplossing wilt fasen en testen, vraagt u een proefaccount aan via deze [link.](https://azure.microsoft.com/global-infrastructure/government/request/)

>[!Note]
>Zodra een abonnement is gepubliceerd als beschikbaar in een specifieke cloud, kan die cloud niet worden verwijderd.

**Azure Government Cloud-certificeringen**

Deze optie is alleen zichtbaar als **Azure Government Cloud** is geselecteerd onder **Cloudbeschikbaarheid**.

Azure Government-services verwerken gegevens die onderworpen zijn aan bepaalde overheidsvoorschriften en -vereisten, zoals FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 en CJIS.  Om uw certificeringen voor deze programma's onder de aandacht te brengen, u maximaal 100 koppelingen aanbieden waarin uw certificeringen worden beschreven.  Deze links kunnen direct links naar uw vermelding in het programma zijn, of een link naar beschrijvingen van uw naleving ervan op uw eigen websites.  Deze koppelingen zijn alleen zichtbaar voor Azure Government Cloud-klanten.

## <a name="plan-listing"></a>Aanbieding plannen

Op het tabblad **lijstmetjes** met abonnementen worden de planspecifieke aanbiedingsgegevens weergegeven die kunnen verschillen tussen verschillende abonnementen voor dezelfde aanbieding.

### <a name="name"></a>Naam

Vooraf ingevuld op basis van uw naam die u uw plan hebt toegewezen toen u het hebt gemaakt.  Deze naam wordt weergegeven als de titel van dit "Software-plan" dat in de markt wordt weergegeven.  Kan maximaal 100 tekens bevatten.

### <a name="summary"></a>Samenvatting

Geef een korte samenvatting van uw softwareplan.  Kan maximaal 100 tekens bevatten.

### <a name="description"></a>Beschrijving

Deze beschrijving is een kans om uit te leggen wat dit softwareplan uniek maakt en eventuele verschillen met andere softwareplannen binnen uw aanbod. Kan maximaal 2.000 tekens bevatten.

Selecteer **Opslaan** nadat u deze velden hebt voltooid.

## <a name="availability"></a>Beschikbaarheid

Het tabblad **Beschikbaarheid** is alleen zichtbaar voor sjabloonplannen voor oplossingen.  U het plan zichtbaar maken voor iedereen, alleen voor specifieke klanten (een privépubliek) en of u het plan verborgen wilt maken voor gebruik door andere oplossingssjabloon of beheerde toepassingen.

### <a name="plan-audience"></a>Doelgroep plannen

Je hebt de optie om elk abonnement te configureren om zichtbaar te zijn voor iedereen of voor alleen een specifieke doelgroep van je keuze. U lidmaatschap in deze beperkte doelgroep toewijzen met behulp van Azure-abonnements-id's.

**Privacy / Dit is een privé-abonnement** (optioneel selectievakje) - Schakel dit selectievakje in om uw abonnement privé en alleen zichtbaar te maken voor het beperkte publiek van uw keuze. Zodra je als privéplan is gepubliceerd, kun je het publiek bijwerken of ervoor kiezen om het abonnement voor iedereen beschikbaar te maken. Zodra een plan voor iedereen zichtbaar is, moet het voor iedereen zichtbaar blijven. (Het plan kan niet opnieuw als privé-abonnement worden geconfigureerd).

**Beperkt publiek (Azure-abonnements-id's)** - Wijs de doelgroep toe die toegang heeft tot dit privé-abonnement. Toegang wordt toegewezen met behulp van Azure-abonnements-id's met de optie om een beschrijving van elke toegewezen Azure-abonnements-id op te nemen. Er kunnen maximaal 10 abonnements-id's worden toegevoegd of 20.000 klanten abonnement-id's als u een CSV-spreadsheetbestand importeert.  Azure-abonnements-id's worden weergegeven als GUID's en letters moeten in een lager geval worden weergegeven.

>[!Note]
>De privédoelgroep (of beperkte doelgroep) verschilt van de preview-doelgroep die u hebt gedefinieerd op het tabblad [**Voorbeeld.**](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#preview)  Een preview-publiek krijgt toegang tot uw aanbieding *voordat* de aanbieding live op de markt wordt gepubliceerd. Hoewel de aanduiding voor privé-doelgroepen alleen van toepassing is op een specifiek plan, kan de preview-doelgroep alle plannen (privé of niet) bekijken voor validatiedoeleinden.

### <a name="hide-plan"></a>Plan verbergen

Als het de bedoeling is dat uw oplossingssjabloon alleen indirect wordt geïmplementeerd wanneer er naar wordt verwezen via een andere oplossingssjabloon of beheerde toepassing, schakelt u dit selectievakje in om uw oplossingssjabloon te publiceren, maar deze te verbergen voor klanten die er rechtstreeks naar zoeken en browsen.

## <a name="pricing-and-availability"></a>Prijzen en beschikbaarheid

Het tabblad **Prijzen en beschikbaarheid** is alleen zichtbaar voor beheerde toepassingsplannen.  U de markten configureren waarin dit plan beschikbaar zal zijn, de prijs per maand van het beheer van de oplossing en of u het plan zichtbaar wilt maken voor iedereen of alleen voor specifieke klanten (een privépubliek).

### <a name="markets"></a>Markten

Elk plan moet beschikbaar zijn in ten minste één markt. Schakel het selectievakje in voor elke marktlocatie waar u dit abonnement beschikbaar wilt maken. Een zoekvak en knop voor het selecteren van 'Tax Remitted'-landen, waarin Microsoft namens u btw overdraagt, zijn opgenomen om u te helpen.

Als u al prijzen voor uw abonnement in Amerikaanse dollars (USD) hebt ingesteld en een andere marktlocatie toevoegt, wordt de prijs voor de nieuwe markt berekend op basis van de huidige wisselkoersen. Controleer altijd de prijs voor elke markt voordat u publiceert. Prijzen kunnen worden beoordeeld met behulp van de link 'Exportprijzen (xlsx)' na het opslaan van uw wijzigingen.

### <a name="pricing"></a>Prijzen

Geef de prijs per maand op voor dit abonnement.  Deze prijs is een aanvulling op de kosten van een Azure-infrastructuur of pay-as-you-go-software die wordt gemaakt door de resources die door deze oplossing worden geïmplementeerd.

Prijzen in lokale valuta (USD = Amerikaanse dollar) worden omgerekend in de lokale valuta van alle geselecteerde markten met behulp van de huidige wisselkoersen die beschikbaar zijn tijdens de installatie. Valideer deze prijzen voordat u publiceert door de prijsspreadsheet te exporteren en de prijs in elke markt te bekijken. Als u aangepaste prijzen in een afzonderlijke markt wilt instellen, wijzigt en importeert u de prijsspreadsheet. 

>[!Note]
>U moet eerst uw prijswijzigingen opslaan om het exporteren van prijsgegevens mogelijk te maken.

Controleer uw prijzen zorgvuldig voordat u publiceert, omdat er enkele beperkingen zijn op wat kan veranderen nadat een plan is gepubliceerd.  

>[!Note]
>Zodra een prijs voor een markt in uw plan wordt gepubliceerd, kan deze later niet meer worden gewijzigd.

### <a name="plan-audience"></a>Doelgroep plannen

Je hebt de optie om elk abonnement te configureren om zichtbaar te zijn voor iedereen of voor alleen een specifieke doelgroep van je keuze. U lidmaatschap in deze beperkte doelgroep toewijzen met behulp van Azure-abonnements-id's.

**Privacy / Dit is een privé-abonnement** (optioneel selectievakje) - Schakel dit selectievakje in om uw abonnement privé en alleen zichtbaar te maken voor het beperkte publiek van uw keuze. Zodra je als privéplan is gepubliceerd, kun je het publiek bijwerken of ervoor kiezen om het abonnement voor iedereen beschikbaar te maken. Zodra een plan voor iedereen zichtbaar is, moet het voor iedereen zichtbaar blijven. (Het plan kan niet opnieuw als privé-abonnement worden geconfigureerd).

**Beperkt publiek (Azure-abonnements-id's)** - Wijs de doelgroep toe die toegang heeft tot dit privé-abonnement. Toegang wordt toegewezen met behulp van Azure-abonnements-id's met de optie om een beschrijving van elke toegewezen Azure-abonnements-id op te nemen. Er kunnen maximaal 10 abonnements-id's worden toegevoegd of 20.000 klanten abonnement-id's als u een CSV-spreadsheetbestand importeert.  Azure-abonnements-id's worden weergegeven als GUID's en letters moeten in een lager geval worden weergegeven.

>[!Note]
>De privédoelgroep (of beperkte doelgroep) verschilt van de preview-doelgroep die u hebt gedefinieerd op het tabblad [**Voorbeeld.**](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#preview)  Een preview-publiek krijgt toegang tot uw aanbieding *voordat* de aanbieding live op de markt wordt gepubliceerd. Hoewel de aanduiding voor privé-doelgroepen alleen van toepassing is op een specifiek plan, kan de preview-doelgroep alle plannen (privé of niet) bekijken voor validatiedoeleinden.

## <a name="technical-configuration"></a>Technische configuratie 

Met het tabblad **technische configuratie** u het implementatiepakket uploaden waarmee klanten uw abonnement kunnen implementeren.

>[!Note]
>Dit tabblad is niet zichtbaar als u dit plan hebt geconfigureerd om pakketten van een ander plan te hergebruiken op het tabblad **Plan-installatie.**

### <a name="package-details"></a>Pakketgegevens

Met het subtabblad **Pakketdetails** u de conceptversie van uw technische configuratie bewerken.

***Versie*** - Wijs de huidige versie van de technische configuratie toe.  Wijzig deze versie telkens wanneer u een wijziging op deze pagina publiceert. Versie moet in `{integer}.{integer}.{integer}`het formaat .

***Pakketbestand*** `.zip`( ) - Dit pakket bevat alle sjabloonbestanden die nodig zijn voor `.zip` dit plan, evenals eventuele extra bronnen, verpakt als een bestand.

Alle azure-toepassingsplanpakketten moeten deze twee bestanden `.zip` in de hoofdmap van een archief bevatten:

* Een resourcebeheersjabloonbestand met de naam [mainTemplate.json](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  Deze sjabloon automatiseert de implementatie van resources in het Azure-abonnement van klanten.  Zie voor voorbeelden van Resource Manager-sjablonen de [azure quickstartsjablonengalerij](https://azure.microsoft.com/documentation/templates/) of de bijbehorende [GitHub: Azure Resource Manager Quickstart Templates](https://github.com/azure/azure-quickstart-templates) repo.

* Een definitie van de gebruikersinterface voor de ervaring voor het maken van Azure-toepassingen met de naam [createUiDefinition.json](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).

Alle nieuwe Azure-toepassingsaanbiedingen moeten ook een [AZURE-partner-guid voor het gebruik van klanten](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution) bevatten.

### <a name="previously-published-packages"></a>Eerder gepubliceerde pakketten 

Met het subtabblad **Eerder gepubliceerde pakketten** u alle gepubliceerde versies van uw technische configuratie bekijken.

## <a name="technical-configuration-managed-application-plans-only"></a>Technische configuratie (alleen beheerde toepassingsplannen)

Beheerde toepassingsplannen hebben extra complexiteit op het tabblad **Technische configuratie** buiten de hierboven beschreven bestandsvelden **Versie** en **Pakket.** 

### <a name="enable-just-in-time-jit-access"></a>Just-in-time (JIT)-toegang inschakelen

Selecteer deze optie om Just-in-time (JIT) toegang voor dit abonnement in te schakelen.  Met JIT-toegang u verhoogde toegang tot de bronnen van een beheerde toepassing aanvragen voor probleemoplossing of onderhoud. U hebt altijd alleen-lezen toegang tot de bronnen, maar voor een bepaalde periode u meer toegang hebben.  Zie [Just-in-time toegang inschakelen en aanvragen voor Azure Managed Applications voor](https://docs.microsoft.com/azure/managed-applications/request-just-in-time-access)meer informatie.  Als u wilt eisen dat consumenten van uw beheerde toepassing uw account permanente toegang verlenen, laat u deze optie onaangevinkt.

>[!Note]
>Zorg ervoor dat `createUiDefinition.json` u uw bestand bijwerkt om deze functie te ondersteunen.  

### <a name="deployment-mode"></a>Implementatiemodus

Selecteer of u **de modus Voltooien** of **Incrementele implementatie** wilt configureren bij het implementeren van dit abonnement: 

* In **de volledige modus**zal een herschikking van de toepassing door de klant resulteren in verwijdering van `mainTemplate.json`resources in de beheerde resourcegroep als de resources niet zijn gedefinieerd in de . 
* In **de incrementele modus**laat een herschikking van de toepassing bestaande resources ongewijzigd.

Zie [Azure Resource Manager-implementatiemodi](https://docs.microsoft.com/azure/azure-resource-manager/deployment-modes)voor meer informatie over implementatiemodi.

### <a name="notification-endpoint-url"></a>URL van eindpunt van meldingen

Geef een HTTPS Webhook-eindpunt op om meldingen te ontvangen over alle CRUD-bewerkingen op beheerde toepassingsinstanties van deze abonnementsversie.

### <a name="customize-allowed-customer-actions"></a>Toegestane acties van klanten aanpassen

Selecteer deze optie om op te geven welke acties klanten`*/read`kunnen uitvoeren op de beheerde resources naast de ' acties die standaard beschikbaar zijn. 

Vermeld de extra acties die u uw klant in staat wilt stellen hier uit te voeren, gescheiden door puntkomma's.  Zie [Weigeringstoewijzingen voor Azure-resources begrijpen voor](https://docs.microsoft.com/azure/role-based-access-control/deny-assignments)meer informatie.  Zie [Azure Resource Manager resource provider operations](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations)voor beschikbare acties. Bijvoorbeeld, om consumenten toe te staan `Microsoft.Compute/virtualMachines/restart/action` om virtuele machines opnieuw op te starten, toe te voegen aan de toegestane acties.

### <a name="global-azure--azure-government-cloud"></a>Globale Azure / Azure Government Cloud

Geef aan wie beheertoegang moet hebben tot deze beheerde toepassing in elke ondersteunde cloud.  Gebruikers, groepen of toepassingen waaraan u toestemming wilt krijgen voor de beheerde brongroep, worden geïdentificeerd met azure active directory -identiteiten (AAD).

***Azure Active Directory Tenant ID*** - De AAD Tenant ID (ook wel directory ID genoemd) met de identiteiten van de gebruikers, groepen of toepassingen waaraan u machtigingen wilt verlenen.  U uw AAD-tenant-id vinden op de Azure-portal in [Eigenschappen voor Azure Active Directory.](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties)

***Autorisaties*** : voeg de Azure Active Directory-object-id toe van de gebruiker, groep of toepassing waaraan u toestemming wilt krijgen voor de beheerde brongroep. Identificeer de gebruiker met zijn hoofd-id, die kan worden gevonden op het [Azure Active Directory-gebruikersblad op de Azure-portal.](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers)

Selecteer voor elke principal een van de ingebouwde azure AD-rollen in de lijst (Eigenaar of Inzender). De rol die u selecteert, beschrijft de machtigingen die de opdrachtgever heeft voor de resources in het klantabonnement. Zie [Ingebouwde rollen voor Azure-resources](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) voor meer informatie.  Zie [Aan de slag met RBAC in de Azure-portal](https://docs.microsoft.com/azure/role-based-access-control/overview)voor meer informatie over op rollen gebaseerde toegangscontrole (RBAC).

>[!Note]
>Hoewel u maximaal 100 autorisaties per cloud toevoegen, is het over het algemeen eenvoudiger om een Active Directory-gebruikersgroep te maken en de id op te geven in de hoofd-id.  Hierdoor u meer gebruikers toevoegen aan de beheergroep nadat het plan is geïmplementeerd en de noodzaak verminderen om het plan bij te werken om meer autorisaties toe te voegen.

### <a name="policy-settings"></a>Beleidsinstellingen

Pas [Azure-beleid](https://docs.microsoft.com/azure/governance/policy/overview) toe op uw beheerde toepassing om nalevingsvereisten voor de geïmplementeerde oplossing op te geven.  Zie [Voorbeelden van Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/index) voor beleidsdefinities en de indeling van de parameterwaarden.  U maximaal vijf beleidsregels configureren en slechts één exemplaar van elke beleidsoptie.  Voor sommige beleidsregels zijn aanvullende parameters vereist.  De standaard SKU is vereist voor controlebeleid.  Beleidsnaam is beperkt tot 50 tekens.

## <a name="co-sell"></a>Co-Sell

Het verstrekken van informatie op het tabblad Cosell is volledig optioneel voor het publiceren van uw aanbieding. Het is vereist om co-sell ready en IP Co-sell Ready status te bereiken. De informatie die u verstrekt, wordt door Microsoft-verkoopteams gebruikt om meer te weten te komen over uw oplossing bij het beoordelen van de behoeften van de klant. Het is niet rechtstreeks beschikbaar voor klanten.

Zie [Optie Co-verkoop in Partnercentrum](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-co-sell)voor meer informatie over het invullen van dit tabblad.

## <a name="test-drive"></a>Proefrit

Met het tabblad **Teststation** u een demonstratie (of testrit) instellen waarmee klanten uw aanbieding kunnen proberen voordat ze deze willen kopen. Meer informatie in het artikel [Wat is Test Drive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)  Als u geen testrit meer wilt geven voor uw aanbieding, gaat u terug naar de pagina **Installatie aanbieden** en schakelt u **de testrit inschakelen uit.**

### <a name="technical-configuration"></a>Technische configuratie

Azure Applications maken inherent gebruik van het type Azure Resource Manager-teststation.  Zie [Technische configuratie voor Azure Resource Manager-teststation](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-customer-engagement-offer#technical-configuration-for-azure-resource-manager-test-drive) voor meer informatie.

### <a name="deployment-subscription-details"></a>Details van het implementatieabonnement

Als u de testdrive namens u wilt implementeren, maakt en verstrekt u een afzonderlijk, uniek Azure-abonnement. (Niet vereist voor Power BI-testritten).

- **Azure-abonnements-ID** (vereist voor Azure Resource Manager- en Logic-apps): Voer de abonnements-id in om toegang te verlenen tot uw Azure-accountservices voor rapportage en facturering van resourcegebruik. We raden u aan om [een apart Azure-abonnement](https://docs.microsoft.com/azure/billing/billing-create-subscription) te maken dat u gebruiken voor testritten als u er nog geen hebt. U uw Azure-abonnements-id vinden door u aan te melden bij de [Azure-portal](https://portal.azure.com/) en te navigeren naar het tabblad **Abonnementen** van het linkermenu. Als u het tabblad selecteert, wordt uw abonnements-ID weergegeven (bijvoorbeeld "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **Azure AD-tenant-id** (vereist): voer uw Azure Active Directory (AD) [tenant ID in](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in). Als u deze id wilt vinden, meldt u zich aan bij de [Azure-portal,](https://portal.azure.com/)selecteert u het tabblad Active Directory in het linkermenu, selecteert u **Eigenschappen en zoekt u vervolgens naar het vermelde **Directory-id-nummer** (bijvoorbeeld 50c464d3-4930-494c-963c-1e951d15360e). U ook de tenant-id van uw organisatie [https://www.whatismytenantid.com](https://www.whatismytenantid.com)opzoeken met uw URL van uw domeinnaam op:.

- **Azure AD-tenantnaam** (vereist voor Dynamic 365): voer de naam van uw Azure Active Directory (AD) in. Als u deze naam wilt vinden, meldt u zich aan bij de [Azure-portal,](https://portal.azure.com/)in de rechterbovenhoek wordt uw tenantnaam vermeld onder uw accountnaam.

- **Azure AD-app-id** (vereist): voer uw Azure Active Directory (AD) [toepassings-id in](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in). Als u deze id wilt vinden, meldt u zich aan bij de [Azure-portal,](https://portal.azure.com/)selecteert u het tabblad Active Directory in het linkermenu, selecteert u **App-registraties**en zoekt u vervolgens naar het vermelde **toepassings-id-nummer** (bijvoorbeeld 50c464d3-4930-494c-963c-1e951d15360e).

- **Azure AD-app client geheim** (vereist): Voer het geheim van uw Azure AD-clientclient [in](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets). Als u deze waarde wilt vinden, meldt u zich aan bij de [Azure-portal.](https://portal.azure.com/) Selecteer het tabblad **Azure Active Directory** in het linkermenu, selecteer **App-registraties**en selecteer vervolgens de app voor een teststation. Selecteer vervolgens **Certificaten en geheimen,** selecteer **Nieuw clientgeheim,** voer een beschrijving in, selecteer **Nooit** onder **Verloopt**en kies **Vervolgens Toevoegen**. Zorg ervoor dat u de waarde naar beneden kopieert. (Navigeer niet weg van de pagina voordat u de waarde hebt gekopieerd, anders hebt u geen toegang tot de waarde.)

Vergeet niet om op te **slaan** voordat je doorgaat naar de volgende sectie!

### <a name="test-drive-listings-optional"></a>Aanbiedingen voor teststations (optioneel)

De optie **Testdrive-vermeldingen** onder het tabblad **Teststation** geeft de talen (en markten) weer waar uw testrit beschikbaar is, momenteel is Engels (Verenigde Staten) de enige beschikbare locatie. Bovendien geeft deze pagina de status weer van de taalspecifieke vermelding en de datum/tijd die is toegevoegd. U moet de testdrive details (beschrijving, handleiding, video's, enz.) voor elke taal / markt te definiëren.

- **Beschrijving** (vereist): Beschrijf uw testrit, wat wordt aangetoond, doelstellingen voor de gebruiker om mee te experimenteren, functies om te verkennen en alle relevante informatie om de gebruiker te helpen bepalen of hij uw aanbieding wil verwerven. In dit veld kunnen maximaal 3.000 tekens tekst worden ingevoerd. 

- **Toegangsgegevens** (vereist voor Azure Resource Manager- en Logic-teststations): leg uit wat een klant moet weten om toegang te krijgen tot deze testrit en deze te gebruiken. Loop door een scenario voor het gebruik van uw aanbieding en precies wat de klant moet weten om toegang te krijgen tot functies tijdens de testrit. In dit veld kunnen maximaal 10.000 tekens tekst worden ingevoerd.

- **Handleiding** (vereist): een diepgaande doorloop van uw testritervaring. De gebruikershandleiding moet precies betrekking hebben op wat u wilt dat de klant te winnen bij het ervaren van de proefrit en dienen als een referentie voor eventuele vragen die ze kunnen hebben. Het bestand moet in PDF-formaat zijn en worden benoemd (maximaal 255 tekens) na het uploaden.

- **Video's: Video's toevoegen** (optioneel): video's kunnen worden geüpload naar YouTube of Vimeo en hier worden verwezen met een link en miniatuurafbeelding (533 x 324 pixels), zodat een klant een overzicht van informatie kan bekijken om de testrit beter te begrijpen, inclusief hoe ze de functies van uw aanbieding met succes kunnen gebruiken en scenario's kunnen begrijpen die hun voordelen benadrukken.
  - **Naam** (vereist)
  - **URL (alleen YouTube of Vimeo)** (vereist)
  - **Miniatuur (533 x 324 px)**: Afbeeldingsbestand moet in PNG-indeling zijn.

Selecteer **Opslaan** nadat u deze velden hebt voltooid.

## <a name="publish"></a>Publiceren

### <a name="submit-offer-to-preview"></a>Voorstel indienen om een voorbeeld te bekijken

Zodra u alle vereiste gedeelten van de aanbieding hebt voltooid, selecteert u **publiceren** in de rechterbovenhoek van de portal. U wordt doorgestuurd naar de **pagina Beoordeling en publicatie.** 

Als het de eerste keer is dat u deze aanbieding publiceert, u het als volgt doen:

- Zie de voltooiingsstatus voor elk gedeelte van de aanbieding.
    - *Niet gestart* - betekent dat de sectie niet is aangeraakt en moet worden voltooid.
    - *Onvolledig* - betekent dat de sectie fouten bevat die moeten worden opgelost of dat er meer informatie moet worden verstrekt. Ga terug naar de sectie(s) en werk deze bij.
    - *Voltooid* - betekent dat de sectie volledig is, alle vereiste gegevens zijn verstrekt en er geen fouten zijn. Alle onderdelen van de aanbieding moeten volledig zijn voordat u de aanbieding indienen.
- Geef het certificeringsteam testinstructies om ervoor te zorgen dat uw app correct is getest, naast eventuele aanvullende notities die nuttig zijn om uw app te begrijpen.
- Dien de publicatieaanbieding in door **Verzenden te selecteren.** We sturen je een e-mail om je te laten weten wanneer een preview-versie van de aanbieding beschikbaar is om te bekijken en goed te keuren. Keer terug naar partnercentrum en selecteer **Go-live** voor de aanbieding om uw aanbieding aan het publiek te publiceren (of als een privé-aanbieding, aan het particuliere publiek).

### <a name="errors-and-review-feedback"></a>Fouten en feedback bekijken

De **handmatige validatiestap** in het publicatieproces vertegenwoordigt een uitgebreide beoordeling van uw aanbieding en de bijbehorende technische elementen (met name de Azure Resource Manager-sjabloon), problemen worden meestal gepresenteerd als koppelingen naar pull request (PR). Een uitleg over het bekijken en reageren op deze PR's, zie [Feedback over beoordeling afhandelen](./azure-apps-review-feedback.md).

Als u fouten hebt ondervonden in een of meer van de publicatiestappen, moet u deze corrigeren en uw aanbieding opnieuw publiceren.

## <a name="next-steps"></a>Volgende stappen

- [Een bestaande aanbieding bijwerken in Commerciële Marketplace](./update-existing-offer.md)
