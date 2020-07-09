---
title: Een Azure-toepassings aanbieding maken-micro soft Commercial Marketplace
description: Meer informatie over de stappen en overwegingen voor het maken van een nieuwe Azure-toepassings aanbieding in de portal voor commerciële Marketplace in het partner centrum. U kunt uw Azure-toepassings aanbieding aanbieden of verkopen in azure Marketplace of via het programma Cloud Solution Provider (CSP).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: c5371b2d9379ca861addac07de50d7cdf9c34c8b
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86121964"
---
# <a name="create-an-azure-application-offer"></a>Een Azure-toepassingsaanbieding maken

In dit artikel worden de stappen en overwegingen voor het maken van een nieuwe Azure-toepassings aanbieding in de commerciële Marketplace beschreven. U moet bekend zijn met deze concepten voordat u een nieuwe Azure-toepassings aanbieding maakt.

Voordat u een nieuwe Azure-toepassings aanbieding kunt publiceren, [maakt u een commercieel Marketplace-account in partner centrum](create-account.md) en zorgt u ervoor dat uw account is inge schreven in het commerciële Marketplace-programma.

## <a name="before-you-begin"></a>Voordat u begint

Voor het ontwerpen, bouwen en testen van Azure Application-aanbiedingen is technische kennis nodig van zowel het Azure-platform als de technologieën die worden gebruikt voor het bouwen van de aanbieding. Uw technische team moet kennis hebben van de volgende micro soft-technologieën:

* Basis informatie over [Azure-Services](https://azure.microsoft.com/services/).
* Het [ontwerpen en ontwikkelen van Azure-toepassingen](https://azure.microsoft.com/solutions/architecture/).
* Werk kennis van [azure virtual machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage#storage)en [Azure-netwerken](https://azure.microsoft.com/services/?filter=networking#networking).
* Werk ervaring van [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/).
* Werk kennis van [JSON](https://www.json.org/).

### <a name="technical-documentation-and-resources"></a>Technische documentatie en bronnen

Bekijk de volgende bronnen wanneer u uw Azure-toepassings aanbieding voorbereidt voor de commerciële Marketplace.

* [Azure Resource Manager sjablonen begrijpen](../../azure-resource-manager/resource-group-authoring-templates.md)

* Quickstarts:

    * [Azure-quickstart-sjablonen](https://azure.microsoft.com/documentation/templates/)
    * [Hand leiding voor best practices voor Azure-sjablonen](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md)
    * [Toepassingsdefinitie publiceren](../../managed-applications/publish-service-catalog-app.md)
    * [Servicecatalogus-app implementeren](../../managed-applications/deploy-service-catalog-quickstart.md)

* Zelfstudies:

    * [Definitiebestanden maken](../../managed-applications/publish-service-catalog-app.md)
    * [Marketplace-toepassing publiceren](../../managed-applications/publish-marketplace-app.md)

* Voor beelden

    * [Azure-CLI](../../managed-applications/cli-samples.md)
    * [Azure PowerShell](../../managed-applications/powershell-samples.md)
    * [Beheerde toepassingsoplossingen](../../managed-applications/sample-projects.md)

De video [buil ding Solution-sjablonen en beheerde toepassingen voor Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603) bieden een uitgebreide inleiding tot het type Azure-toepassings aanbieding:

* Welke typen aanbieding beschikbaar zijn,
* Welke technische middelen zijn vereist;
* Een Azure Resource Manager sjabloon ontwerpen;
* De gebruikers interface van de App ontwikkelen en testen;
* Het publiceren van de app-aanbieding;
* Het controle proces van de toepassing.

### <a name="suggested-tools"></a>Aanbevolen hulpprogram ma's

Kies een of beide van de volgende script omgevingen om uw Azure-toepassing te beheren:

* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
* [Azure-CLI](https://docs.microsoft.com/cli/azure)

Het is raadzaam om de volgende hulpprogram ma's toe te voegen aan uw ontwikkel omgeving:

* [Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Visual Studio Code](https://code.visualstudio.com/) met de volgende extensies:
    * Extensie: [Azure Resource Manager-Hulpprogram ma's](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    * Extensie: [beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    * Extensie: [PRETTIFY JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

U kunt de beschik bare hulpprogram ma's bekijken op de pagina [Azure Ontwikkelhulpprogramma's](https://azure.microsoft.com/tools/) . Ook als u Visual Studio, de [Visual Studio Marketplace](https://marketplace.visualstudio.com/), gebruikt.

## <a name="types-of-azure-application-plans"></a>Typen Azure-toepassings abonnementen

Er zijn twee soorten Azure-toepassings abonnementen: oplossings sjablonen en beheerde toepassingen.

* **Oplossings sjabloon** is een van de belangrijkste manieren om een oplossing op Marketplace te publiceren. Gebruik dit type abonnement als voor uw oplossing extra implementatie-en configuratie automatisering is vereist dan één virtuele machine (VM). Met een oplossings sjabloon kunt u het leveren van meer dan één resource automatiseren, met inbegrip van Vm's, netwerken en opslag resources om complexe IaaS-oplossingen te bieden.  Zie [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)voor meer informatie over het maken van oplossings sjablonen.

* **Beheerde toepassing** is vergelijkbaar met oplossings sjablonen, met één belang rijk verschil. In een beheerde toepassing worden de resources geïmplementeerd vanuit een resourcegroep die wordt beheerd door de uitgever van de app. De resourcegroep is opgenomen in het abonnement van de consument, maar een identiteit in de tenant van de uitgever heeft toegang tot de resourcegroep. De uitgever bepaalt de kosten voor de voortdurende ondersteuning van de oplossing. Gebruik beheerde toepassingen om eenvoudig volledig beheerde, kant-en-klare toepassingen te bouwen en te leveren aan uw klanten.  Zie het overzicht van door [Azure beheerde toepassingen](../../managed-applications/overview.md)voor meer informatie over de voor-en andere typen beheerde toepassingen.

## <a name="technical-requirements"></a>Technische vereisten

Alle Azure-toepassingen bevatten ten minste twee bestanden in de hoofdmap van een `.zip` Archief:

* Een resource manager-sjabloon bestand [met de naammainTemplate.jsop](../../azure-resource-manager/resource-group-overview.md).  Met deze sjabloon worden de resources gedefinieerd die moeten worden geïmplementeerd in het Azure-abonnement van de klant. Voor voor beelden van Resource Manager-sjablonen raadpleegt u de [Azure Quick Start-sjablonen galerie](https://azure.microsoft.com/resources/templates/) of de bijbehorende [github: Azure Resource Manager Quick](https://github.com/azure/azure-quickstart-templates) start-sjablonen opslag plaats.

* Een definitie van de gebruikers interface voor de Azure-toepassing voor het maken van toepassingen met de naam [createUiDefinition.jsop](../../managed-applications/create-uidefinition-overview.md).  In de gebruikersinterface geeft u elementen op waarmee consumenten parameterwaarden kunnen opgeven.

Alle nieuwe aanbiedingen van Azure Application moeten een [GUID voor gebruiks toewijzing](../azure-partner-customer-usage-attribution.md)van een Azure-partner bevatten. 

Zie voor meer informatie over publicatie vereisten voor elk toepassings plan [oplossings sjabloon bieden publicatie vereisten](../marketplace-solution-templates.md) en de [publicatie vereisten voor beheerde toepassingen](../marketplace-managed-apps.md).

## <a name="create-a-new-offer"></a>Een nieuwe aanbieding maken

>[!NOTE]
>Nadat u een aanbieding hebt gepubliceerd, worden wijzigingen die u aanbrengt in het partner centrum pas weer gegeven in de winkel als u de aanbieding opnieuw publiceert. Zorg ervoor dat u de aanbieding altijd opnieuw publiceert nadat u wijzigingen hebt aangebracht.

1. Meld u aan bij [Partner Center](https://partner.microsoft.com/dashboard/home).

1. Selecteer in het menu links de optie **commerciële Marketplace**-  >  **overzicht**.

1. Selecteer op de pagina overzicht **+ nieuwe aanbieding**  >  **Azure-toepassing**.

    ![Illustreert het navigatie menu.](./media/new-offer-azure-app.png)

1. Voer op de pagina **nieuwe aanbieding** een **aanbiedings-id**in. Dit is een unieke id voor elke aanbieding in uw account.

     * Deze ID is zichtbaar voor klanten in het webadres voor de Marketplace-aanbieding en Azure Resource Manager sjablonen, indien van toepassing.
     * Gebruik alleen kleine letters en cijfers. Dit kan afbreek streepjes en onderstrepings tekens bevatten, maar mag niet langer zijn dan 50. Als u bijvoorbeeld **test-aanbieding-1**invoert, is het webadres van de aanbieding `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
     * De aanbiedings-ID kan niet worden gewijzigd nadat u **maken**hebt geselecteerd.

1. Voer een **alias**voor de aanbieding in. Dit is de naam die wordt gebruikt voor de aanbieding in Partner Center.

     * Deze naam wordt niet gebruikt in Marketplace en wijkt af van de naam van de aanbieding en andere waarden die aan klanten worden weer gegeven.
     * De aanbiedings alias kan niet worden gewijzigd nadat u **maken**hebt geselecteerd.

1. Selecteer **maken** om de aanbieding te genereren en door te gaan.

## <a name="offer-setup"></a>Installatie van aanbieding

Op de pagina **aanbieding instellen** kunt u een test drive en Lead beheer configureren voor uw aanbieding.

### <a name="test-drive"></a>Station testen

Een test drive is een fantastische manier om uw aanbieding aan potentiële klanten te laten presen teren door hen de mogelijkheid te bieden om te kopen voordat u aan de slag gaat, wat resulteert in een verhoogde conversie en de generatie van uiterst gekwalificeerde leads. Meer [informatie over test stations](../what-is-test-drive.md).

Als u een test drive voor een bepaalde periode wilt inschakelen, schakelt u het selectie vakje **een test drive inschakelen** in. Als u test drive uit uw aanbieding wilt verwijderen, schakelt u dit selectie vakje uit. Configureer de test drive omgeving in het gedeelte [technische configuratie van het test station](#test-drive-technical-configuration) verderop in dit onderwerp.

Zie [Test uw aanbieding in de commerciële Marketplace](test-drive.md)voor meer informatie. U kunt ook lezen over [test drive best practices](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices) en de [overzicht van de test stations-PDF](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) downloaden (zorg ervoor dat de pop-upblokkering is uitgeschakeld)

>[!Note]
>Omdat alle Azure-toepassingen worden geïmplementeerd met behulp van een Azure Resource Manager sjabloon, is het enige type test drive dat beschikbaar is voor een Azure-toepassing een [Azure Resource Manager test drive](../azure-resource-manager-test-drive.md).

### <a name="customer-leads"></a>Leads van klanten

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Zie [Lead Management Overview](./commercial-marketplace-get-customer-leads.md)voor meer informatie.

Selecteer **concept opslaan** voordat u doorgaat.

## <a name="properties"></a>Eigenschappen

Op de pagina **Eigenschappen** definieert u de categorieën die worden gebruikt voor het groeperen van uw aanbieding op Marketplace, uw app-versie en de juridische contracten die uw aanbieding ondersteunen.

### <a name="category"></a>Categorie

Selecteer categorieën en subcategorieën om uw aanbieding te plaatsen in de juiste Zoek gebieden voor Marketplace. Zorg ervoor dat u beschrijft hoe uw aanbod deze categorieën ondersteunt in de beschrijving van de aanbieding. Selecteer:

- Ten minste één en Maxi maal twee categorieën, met inbegrip van een primaire en secundaire categorie (optioneel).
- Maxi maal twee subcategorieën voor elke primaire en/of secundaire categorie. Als er geen subcategorie van toepassing is op uw aanbieding, selecteert u **niet van toepassing**.

Bekijk de volledige lijst met categorieën en subcategorieën in [Aanbevolen procedures voor aanbiedingen](../gtm-offer-listing-best-practices.md).

### <a name="legal"></a>Juridisch

[!INCLUDE [Commercial marketplace Standard Contract](./includes/marketplace-contract-text.md)]

## <a name="offer-listing"></a>Aanbieding weer geven

Op deze pagina kunt u de kopie en afbeeldingen voor uw aanbieding voor commerciële Marketplace beheren.

### <a name="marketplace-details"></a>Marketplace-gegevens

> [!NOTE]
> De inhoud van het aanbiedings aanbod (zoals de beschrijving, documenten, scherm afbeeldingen en gebruiks voorwaarden) is niet vereist voor het Engels, zolang de beschrijving van het aanbod begint met de woord groep ' deze toepassing is alleen beschikbaar in [niet-Engelse taal] '. Het is ook acceptabel om een *nuttige koppelings-URL* te bieden om inhoud te bieden in een andere taal dan de versie die wordt gebruikt in de inhoud van de aanbieding.

Hier volgt een voor beeld van hoe de aanbiedings gegevens worden weer gegeven in azure Marketplace (alle prijzen zijn bijvoorbeeld alleen bedoeld voor de werkelijke kosten):

:::image type="content" source="media/example-azure-marketplace-app.png" alt-text="Illustreert hoe deze aanbieding wordt weer gegeven in azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Beschrijvingen van aanroepen

1. Logo
2. Categorieën
3. Ondersteunings adres (koppeling)
4. Gebruiksvoorwaarden
5. Adres van privacybeleid (koppeling)
6. Naam van aanbieding
7. Samenvatting
8. Description
9. Scherm afbeeldingen/Video's

<br>Hier volgt een voor beeld van hoe de aanbiedings gegevens worden weer gegeven in de Azure Portal:

:::image type="content" source="media/example-virtual-machine-container-iot-edge-saas.png" alt-text="Illustreert hoe deze aanbieding wordt weer gegeven in de Azure Portal.":::

#### <a name="call-out-descriptions"></a>Beschrijvingen van aanroepen

1. Titel
2. Beschrijving
3. Handige koppelingen
4. Schermopnamen

#### <a name="name"></a>Name

De naam die u hier invoert, wordt aan klanten weer gegeven als de titel van de aanbieding. Dit veld is vooraf ingevuld met de tekst die u hebt ingevoerd voor de **aanbiedings alias** tijdens het maken van de aanbieding, maar u kunt deze waarde wijzigen. Deze naam kan worden aangemerkt (en u kunt symbolen van het handels merk of copyright bevatten). De naam mag niet langer zijn dan 50 tekens en mag geen emojis bevatten.

#### <a name="search-results-summary"></a>Samen vatting van zoek resultaten

Geef een korte beschrijving van uw aanbieding, Maxi maal 100 tekens. Deze beschrijving kan worden gebruikt in Zoek resultaten.

#### <a name="long-summary"></a>Lange samen vatting

Geef een langere beschrijving van uw aanbieding, Maxi maal 256 tekens. Deze beschrijving kan worden gebruikt in Zoek resultaten.

#### <a name="description"></a>Description

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

#### <a name="search-keywords"></a>Tref woorden zoeken

U kunt eventueel Maxi maal drie Zoek trefwoorden invoeren om klanten te helpen uw aanbieding op Marketplace te vinden. Voor de beste resultaten gebruikt u deze tref woorden ook in uw beschrijving.

#### <a name="privacy-policy-link"></a>Koppeling Privacybeleid

Voer de URL in voor het privacybeleid van uw organisatie. U bent verantwoordelijk om ervoor te zorgen dat uw app voldoet aan de wetten en voor schriften van de privacy en om een geldig privacybeleid te bieden.

### <a name="useful-links"></a>Handige koppelingen

Voeg koppelingen toe aan optionele aanvullende online documenten over uw oplossing door **+ een koppeling toe te voegen**.

### <a name="contact-information"></a>Contactgegevens

Geef de naam, het e-mail adres en het telefoon nummer van een contact persoon voor **ondersteunings contact**, **technisch contact**persoon en **CSP-programma op**. Deze informatie wordt niet weer gegeven aan klanten, maar is beschikbaar voor micro soft en kan worden verschaft aan CSP-partners. Voor sommige contact personen is mogelijk aanvullende informatie vereist.

### <a name="marketplace-media"></a>Media voor Marketplace

Bied logo's en installatie kopieën die u kunt gebruiken met uw aanbieding. Alle installatie kopieën moeten de PNG-indeling hebben. Vage afbeeldingen zorgen ervoor dat uw inzending wordt afgewezen.

>[!Note]
>Als u een probleem hebt met het uploaden van bestanden, moet u ervoor zorgen dat uw lokale netwerk de https://upload.xboxlive.com service die wordt gebruikt door het partner centrum niet blokkeert.

#### <a name="store-logos"></a>Winkel logo's

Geef in de volgende drie pixel grootten PNG-bestanden van het logo van uw aanbieding op:

- **Klein** (48 x 48)
- **Gemiddeld** (90 x 90)
- **Groot** (216 x 216)
- **Breed** (255 x 115)

Alle drie de logo's zijn vereist en worden op verschillende plaatsen in de lijst gebruikt.

#### <a name="screenshots"></a>Schermopnamen

Voeg Maxi maal vijf scherm opnamen toe die laten zien hoe uw aanbieding werkt. Elke scherm afbeelding moet 1280 x 720 pixels groot en in PNG-indeling zijn. Elke scherm opname moet een bijschrift bevatten.

#### <a name="videos"></a>Video's

Voeg Maxi maal vijf Video's toe die uw aanbieding aantonen. Deze moeten worden gehost op een externe video service. Voer de naam, het webadres en de PNG-afbeelding van de video in op 1280 x 720 pixels.

#### <a name="additional-marketplace-listing-resources"></a>Aanvullende bronnen voor Marketplace-lijst

- [Best practices voor Marketplace-aanbiedingen](../gtm-offer-listing-best-practices.md)

Selecteer **concept opslaan** voordat u doorgaat.

## <a name="preview-audience"></a>Voor beeld van doel groep

Kies op het tabblad voor beeld een beperkt voor beeld van een **doel groep** voor het valideren van uw aanbieding voordat u deze live publiceert naar de bredere Marketplace-doel groep.

> [!IMPORTANT]
> Nadat u uw aanbieding in de preview-versie hebt gecontroleerd, selecteert u **Live** om uw aanbieding naar de open bare doel groep voor commerciële Marketplace te publiceren.

Uw preview-doel groep wordt geïdentificeerd door de Azure-abonnements-ID-GUID'S, samen met een optionele beschrijving voor elk. Geen van deze velden kan worden gezien door klanten. U kunt uw Azure-abonnements-ID vinden op de pagina **abonnementen** in azure Portal.

Voeg ten minste één Azure-abonnements-ID afzonderlijk toe (Maxi maal 10) of door een CSV-bestand te uploaden (Maxi maal 100). Door deze abonnement-Id's toe te voegen, definieert u wie een voor beeld van uw aanbieding kan bekijken voordat deze Live wordt gepubliceerd. Als uw aanbieding al Live is, kunt u nog steeds een preview-doel opgeven voor het testen van wijzigingen in de aanbieding of updates voor uw aanbieding.

> [!NOTE]
> Een preview-doel groep wijkt af van een persoonlijke doel groep. Een preview-doel groep heeft toegang tot uw aanbieding _voordat_ deze Live wordt gepubliceerd in de Marketplace. Ze kunnen alle plannen bekijken en valideren, met inbegrip van de abonnementen die alleen beschikbaar zijn voor een privé publiek nadat uw aanbieding volledig naar de Marketplace is gepubliceerd. Een persoonlijke doel groep (gedefinieerd in het tabblad **prijs en beschik baarheid** van abonnementen) heeft exclusieve toegang tot een bepaald abonnement.

Selecteer **concept opslaan** voordat u doorgaat.

## <a name="technical-configuration"></a>Technische configuratie

Voltooi deze sectie alleen als uw aanbieding een beheerde toepassing bevat die meter gebeurtenissen gaat verzenden met behulp van de Marketplace meter Service-API. Voer de **Azure Active Directory Tenant-id** en **Azure Active Directory toepassings-id** in die door uw service wordt gebruikt voor het verzenden van meter gebeurtenissen.

Selecteer **concept opslaan** voordat u doorgaat.

## <a name="technical-configuration-offer-level"></a>Technische configuratie (aanbiedings niveau)

>[!Note]
>Technische Details voor het niveau van de aanbieding zijn optioneel.  U hoeft deze gegevens alleen te configureren als u een beheerde toepassing publiceert met facturering met data limiet en een service hebt die wordt geverifieerd met een Azure AD-beveiligings token. Zie [verificatie strategieën](./marketplace-metering-service-authentication.md) voor de verschillende verificatie opties voor meer informatie.

De technische configuratie definieert de Details (Tenant-ID en App-ID) die worden gebruikt voor het identificeren van uw service, waardoor er metings gebeurtenissen voor een beheerde toepassing worden gemaakt met behulp van de [Marketplace meter service-api's](./marketplace-metering-service-apis.md).  Geef de identiteit op die uw service gebruikt voor het verzenden van meter gebeurtenissen.

* **Azure AD-Tenant-id** (vereist): in het Azure Portal moet u [een Azure Active Directory (AD)-app maken](../../active-directory/develop/howto-create-service-principal-portal.md) zodat de verbinding tussen de twee services achter een geverifieerde communicatie kan worden gevalideerd. Als u de [Tenant-id](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)wilt vinden, gaat u naar uw Azure Active Directory en selecteert u **Eigenschappen**. vervolgens zoekt u naar de weer gegeven **Directory-id** (bijvoorbeeld 50c464d3-4930-494c-963c-1e951d15360e).
* **Azure AD-App-ID** (vereist): u hebt ook uw [toepassings-id](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)en een verificatie sleutel nodig. Als u deze waarden wilt ophalen, gaat u naar uw Azure Active Directory en selecteert u **app-registraties**en vervolgens zoekt u naar het **toepassings-id-** nummer dat wordt weer gegeven (bijvoorbeeld 50c464d3-4930-494c-963c-1e951d15360e). Ga naar **instellingen** en selecteer **sleutels**om de verificatie sleutel te vinden. U moet een beschrijving en een duur opgeven, en vervolgens een numerieke waarde.

>[!Note]
>De Azure-toepassings-ID wordt gekoppeld aan uw uitgevers-ID en kan alleen opnieuw worden gebruikt binnen dit uitgevers account.

>[!Note]
>Deze configuratie is vereist als u een batch- [gebruiks gebeurtenis](marketplace-metering-service-apis.md#metered-billing-batch-usage-event)wilt gebruiken).  Als u een [gebruiks gebeurtenis](marketplace-metering-service-apis.md#metered-billing-single-usage-event)wilt verzenden), kunt u ook de [meta gegevens service](../../active-directory/managed-identities-azure-resources/overview.md) gebruiken om het [JWT-Bearer-token (JSON Web token](pc-saas-registration.md#how-to-get-the-publishers-authorization-token)) op te halen.

## <a name="plan-overview"></a>Overzicht van plan

Op dit tabblad kunt u binnen dezelfde aanbieding verschillende plan opties opgeven. Deze plannen (zoals Sku's in de Cloud Partner-portal) kunnen verschillen qua plan type (oplossings sjabloon versus beheerde toepassing), verdiensten maximaliseren of doel groep.  Configureer ten minste één abonnement om uw aanbieding in Marketplace te vermelden.

Nadat u hebt gemaakt, ziet u de namen van uw plannen, Id's, plan type, Beschik baarheid (openbaar of privé), huidige publicatie status en eventuele beschik bare acties op dit tabblad.

**Acties** die beschikbaar zijn in het **plan overzicht** variëren afhankelijk van de huidige status van uw abonnement en kunnen het volgende omvatten:

* Als de plan status **concept** -concept verwijderen is.
* Als de status van het abonnement **Live** is: het verkoop plan stoppen of privé publiek synchroniseren.

### <a name="create-new-plan"></a>Nieuw plan maken

***Plan-id*** : Maak een unieke plan-id voor elk abonnement in deze aanbieding. Deze ID is zichtbaar voor klanten in de product-URL.  Gebruik alleen kleine letters, alfanumerieke tekens, streepjes of onderstrepingen. Voor deze abonnement-ID zijn Maxi maal 50 tekens toegestaan. Deze ID kan niet worden gewijzigd nadat de maken is geselecteerd.

***Plan naam*** : klanten zien deze naam wanneer ze bepalen welk abonnement binnen uw aanbieding moet worden geselecteerd. Maak in deze aanbieding een unieke aanbiedings naam voor elk abonnement. De naam van het abonnement wordt gebruikt om software-abonnementen te onderscheiden die deel kunnen uitmaken van dezelfde aanbieding (bijvoorbeeld aanbiedings naam: Windows Server; abonnementen: Windows Server 2016, Windows Server 2019).

### <a name="plan-setup"></a>Installatie plannen

Op dit tabblad kunt u de configuratie op hoog niveau voor het type abonnement instellen, ongeacht of de pakketten van een ander abonnement opnieuw worden gebruikt en op welke Clouds het plan beschikbaar moet zijn. Uw antwoorden op dit tabblad beïnvloeden welke velden worden weer gegeven op andere tabbladen voor hetzelfde abonnement.

#### <a name="plan-type"></a>Type abonnement
Selecteer het type abonnement voor uw aanbieding. Een **oplossings sjabloon** plan wordt volledig door de klant beheerd. Met een **beheerd toepassings** abonnement kunnen uitgevers de toepassing namens de klant beheren. Zie [typen Azure-toepassings plannen](#types-of-azure-application-plans)voor meer informatie.

#### <a name="re-use-technical-configuration"></a>Technische configuratie opnieuw gebruiken

Als u meer dan één abonnement van hetzelfde type hebt en de pakketten identiek zijn, kunt u het abonnement selecteren om **pakketten van een ander abonnement**opnieuw te gebruiken.  Wanneer u deze optie selecteert, kunt u een van de andere plannen van hetzelfde type voor deze aanbieding selecteren om pakketten opnieuw te gebruiken. 

>[!Note]
>Wanneer u pakketten uit een ander abonnement opnieuw gebruikt, verdwijnt het hele tabblad technische configuratie van dit abonnement. De technische configuratie details van het andere abonnement, inclusief eventuele updates die u in de toekomst maakt, worden ook voor dit abonnement gebruikt.<br><br>Deze instelling kan niet worden gewijzigd nadat dit abonnement is gepubliceerd.

#### <a name="azure-regions-cloud"></a>Azure-regio's (Cloud)

Uw abonnement moet beschikbaar worden gesteld in ten minste één Azure-regio.

Selecteer de optie **Azure Global** om uw plan beschikbaar te maken voor klanten in alle wereld wijde Azure-regio's die een commerciële Marketplace-integratie hebben. Zie [geografische Beschik baarheid en valuta ondersteuning](../marketplace-geo-availability-currencies.md)voor meer informatie.

Selecteer de optie **Azure Government** om uw plan beschikbaar te maken in de regio [Azure Government](../../azure-government/documentation-government-welcome.md) . Deze regio voorziet in gecontroleerde toegang voor klanten van Amerikaanse federale, staats-, lokale of tribalee entiteiten, evenals partners die in aanmerking komen voor hen. Als uitgever bent u verantwoordelijk voor nalevings controles, beveiligings maatregelen en aanbevolen procedures. Azure Government maakt gebruik van fysiek geïsoleerde data centers en netwerken (alleen in de Verenigde Staten).

Voordat u naar [Azure Government](../../azure-government/documentation-government-manage-marketplace-partners.md)publiceert, moet u het abonnement testen en valideren in de omgeving, omdat bepaalde eind punten kunnen verschillen. Als u uw abonnement wilt instellen en testen, vraagt u een proef account aan bij [Microsoft Azure Government proef versie](https://azure.microsoft.com/global-infrastructure/government/request/).

>[!NOTE]
>Nadat uw abonnement is gepubliceerd en beschikbaar is in een specifieke Azure-regio, kunt u die regio niet verwijderen.

#### <a name="azure-government-certifications"></a>Azure Government-certificeringen

Deze optie is alleen zichtbaar als u **Azure Government**hebt geselecteerd.

Azure Government services verwerken gegevens die onderworpen zijn aan bepaalde wettelijke voor schriften en vereisten. Bijvoorbeeld FedRAMP, NIST 800,171 (DIB), ITAR, IRS 1075, DoD N4 en CJIS. Als u uw certificeringen voor deze Program ma's wilt controleren, kunt u Maxi maal 100 koppelingen opgeven. Deze kunnen links naar uw vermelding in het programma staan, of koppelingen naar beschrijvingen van uw naleving met hen op uw eigen websites. Deze koppelingen zijn alleen zichtbaar voor Azure Government klanten.

Selecteer **concept opslaan** voordat u doorgaat.

### <a name="plan-listing"></a>Aanbieding plannen

Hier kunt u de vermeldings gegevens van het plan configureren. Dit tabblad bevat specifieke informatie die kan verschillen tussen plannen in dezelfde aanbieding.

#### <a name="plan-name"></a>Plan naam

Dit is vooraf ingevuld met de naam die u hebt opgegeven bij het maken van het abonnement. Deze naam wordt weer gegeven in Marketplace als de titel van dit plan en is beperkt tot 100 tekens.

#### <a name="plan-summary"></a>Samen vatting plannen

Geef een korte samen vatting van uw abonnement (niet de aanbieding). Deze samen vatting is beperkt tot 100 tekens.

#### <a name="plan-description"></a>Plan beschrijving

Beschrijf wat dit software plan uniek is, evenals verschillen tussen de plannen binnen uw aanbieding. Beschrijf de aanbieding niet, alleen het abonnement. De beschrijving van het abonnement kan Maxi maal 2.000 tekens bevatten.

Selecteer **concept opslaan** voordat u doorgaat.

### <a name="availability-solution-template-plans-only"></a>Beschik baarheid (alleen plannen voor oplossings sjablonen)

U kunt het plan zichtbaar maken voor iedereen, alleen voor specifieke klanten (een persoonlijke doel groep) en of het plan verborgen moet worden voor gebruik door andere oplossings sjablonen of alleen beheerde toepassingen.

#### <a name="plan-visibility"></a>Zicht baarheid van plan

U kunt elk plan zodanig configureren dat het zichtbaar is voor iedereen of alleen voor een specifieke doel groep van uw keuze. U kunt lidmaatschap van deze beperkte doel groep toewijzen met behulp van Azure-abonnement-Id's.

Selecteer **Dit is een privé-abonnement** dat ervoor zorgt dat uw plan privé is en alleen zichtbaar is voor de beperkte doel groep van uw keuze. Zodra het is gepubliceerd als een persoonlijk abonnement, kunt u de doel groep bijwerken of ervoor kiezen om het plan voor iedereen beschikbaar te stellen. Zodra een plan wordt gepubliceerd als zichtbaar voor iedereen, moet het worden weer gegeven voor iedereen. het kan niet opnieuw worden geconfigureerd als een persoonlijk abonnement.

Als u het abonnement privé maakt, voert u een **Azure-abonnement-id** en de beschrijving in. Elk is een doel groep die toegang heeft tot dit privé-abonnement. Toegang wordt toegewezen met Azure-abonnement-Id's met de optie om een beschrijving op te nemen van elk Azure-abonnements-ID toegewezen. Voeg Maxi maal 10 abonnements-Id's afzonderlijk toe, of 20.000 door een CSV-bestand te importeren. Azure-abonnements-Id's worden weer gegeven als GUID'S en letters moeten kleine letters zijn.

>[!Note]
>Een persoonlijke of beperkte doel groep wijkt af van de preview-doel groep die u hebt gedefinieerd op het tabblad **Preview** . Een preview-doel groep heeft toegang tot uw aanbieding _voordat_ deze live op Marketplace wordt gepubliceerd. Hoewel de keuze van de privé doelgroep alleen van toepassing is op een specifiek abonnement, kan de preview-doel groep alle plannen (privé of niet) voor validatie doeleinden bekijken.

#### <a name="hide-plan"></a>Plan verbergen

Als uw oplossings sjabloon alleen indirect moet worden geïmplementeerd als er een andere oplossings sjabloon of beheerde toepassing wordt verwezen, schakelt u dit selectie vakje in om de oplossings sjabloon te publiceren, maar deze te verbergen bij klanten die ze rechtstreeks zoeken en bezoeken.

Selecteer **concept opslaan** voordat u doorgaat.

### <a name="pricing-and-availability-managed-application-plans-only"></a>Prijzen en beschik baarheid (alleen beheerde toepassings abonnementen)

Gebruik deze voor het configureren van de **markten** waar dit abonnement beschikbaar is, de **prijs** per maand van het beheer van de oplossing en de **zicht baarheid** van het abonnement als alleen specifieke klanten het moeten zien (een persoonlijke doel groep).

Selecteer **concept opslaan** voordat u doorgaat.

#### <a name="markets"></a>Landen

Elk plan moet beschikbaar zijn op ten minste één markt. Schakel het selectie vakje in voor elke markt locatie waar u dit plan beschikbaar wilt maken. Een zoekvak en een knop voor het selecteren van de landen/regio's waarvoor ' BTW is geremitteerd ', waarin micro soft verkoop en het gebruik van BTW namens u verdeelt, zijn opgenomen om u te helpen.

Als u al prijzen voor uw abonnement hebt ingesteld in Verenigde Staten dollars (USD) en een andere markt locatie toevoegt, wordt de prijs voor de nieuwe markt berekend op basis van de huidige wissel koersen. De prijs voor elke markt altijd controleren voordat deze wordt gepubliceerd. U kunt de prijzen controleren met behulp van de koppeling export prijzen (XLSX) nadat u uw wijzigingen hebt opgeslagen.

#### <a name="pricing"></a>Prijzen

Geef de prijs per maand voor dit abonnement op.  Deze prijs is een aanvulling op de kosten van een Azure-infra structuur of een betalen per gebruik-software die wordt gemaakt door de resources die door deze oplossing worden geïmplementeerd.

Naast de prijs per maand kunt u ook prijzen voor het verbruik van niet-standaard eenheden instellen met behulp van [facturering via een Data limiet](./azure-app-metered-billing.md).  U kunt de prijs per maand instellen op nul en kosten in rekening gebracht als u wilt. 

Prijzen die zijn ingesteld in USD (USD = Verenigde Staten dollar) worden omgezet in de lokale valuta van alle geselecteerde markten met de huidige wissel koersen wanneer deze worden opgeslagen. Valideer deze prijzen vóór de publicatie door het prijs werk blad te exporteren en de prijs op elke markt te bekijken. Als u aangepaste prijzen wilt instellen op een afzonderlijke markt, wijzigt en importeert u de prijs informatie in het werk blad. 

>[!Note]
>U moet uw prijs wijzigingen eerst opslaan om het exporteren van prijs gegevens in te scha kelen.

Controleer uw prijzen zorgvuldig voordat u deze publiceert. er zijn enkele beperkingen voor wat er kan worden gewijzigd nadat een plan is gepubliceerd.  

>[!Note]
>Zodra een prijs voor een markt in uw abonnement wordt gepubliceerd, kunt u deze later niet meer wijzigen.

#### <a name="plan-visibility"></a>Zicht baarheid van plan

U kunt elk plan zodanig configureren dat het zichtbaar is voor iedereen of alleen voor een specifieke doel groep van uw keuze. U kunt lidmaatschap van deze beperkte doel groep toewijzen met behulp van Azure-abonnement-Id's.

Selecteer **Dit is een privé-abonnement** dat ervoor zorgt dat uw plan privé is en alleen zichtbaar is voor de beperkte doel groep van uw keuze. Zodra het is gepubliceerd als een persoonlijk abonnement, kunt u de doel groep bijwerken of ervoor kiezen om het plan voor iedereen beschikbaar te stellen. Zodra een plan wordt gepubliceerd als zichtbaar voor iedereen, moet het worden weer gegeven voor iedereen. het kan niet opnieuw worden geconfigureerd als een persoonlijk abonnement.

>[!Note]
>Een persoonlijke of beperkte doel groep wijkt af van de preview-doel groep die u hebt gedefinieerd op het tabblad **Preview** . Een preview-doel groep heeft toegang tot uw aanbieding _voordat_ deze live op Marketplace wordt gepubliceerd. Hoewel de keuze van de privé doelgroep alleen van toepassing is op een specifiek abonnement, kan de preview-doel groep alle plannen (privé of niet) voor validatie doeleinden bekijken.

Als u het abonnement privé maakt, voert u een **Azure-abonnement-id** en de beschrijving in. Elk is een doel groep die toegang heeft tot dit privé-abonnement. Toegang wordt toegewezen met Azure-abonnement-Id's met de optie om een beschrijving op te nemen van elk Azure-abonnements-ID toegewezen. Voeg Maxi maal 10 abonnements-Id's afzonderlijk toe, of 20.000 door een CSV-bestand te importeren. Azure-abonnements-Id's worden weer gegeven als GUID'S en letters moeten kleine letters zijn.

>[!Note]
>Persoonlijke aanbiedingen worden niet ondersteund met Azure-abonnementen die zijn gemaakt via een wederverkoper van het Cloud Solution Provider-programma (CSP).


### <a name="technical-configuration"></a>Technische configuratie 

Op dit tabblad kunt u het implementatie pakket uploaden waarmee klanten uw plan kunnen implementeren.

>[!Note]
>Dit tabblad wordt niet weer gegeven als u dit plan hebt geconfigureerd om pakketten opnieuw te gebruiken van een ander abonnement op het tabblad **installatie plannen** .

#### <a name="package-details"></a>Pakket Details

Op dit tabblad kunt u de concept versie van uw technische configuratie bewerken.

**Versie** : wijs de huidige versie van de technische configuratie toe.  Verhoog deze versie telkens wanneer u een wijziging op deze pagina publiceert. De versie moet de indeling hebben `{integer}.{integer}.{integer}` .

**Pakket bestand** (. zip): dit pakket bevat alle sjabloon bestanden die nodig zijn voor dit plan en eventuele extra resources, verpakt als een `.zip` bestand.

Alle Azure Application plan-pakketten moeten deze twee bestanden in de hoofdmap van een `.zip` Archief bevatten:

* Een resource manager-sjabloon bestand [met de naammainTemplate.jsop](../../azure-resource-manager/resource-group-overview.md). Met deze sjabloon wordt de implementatie van resources in het Azure-abonnement van klanten geautomatiseerd.  Voor voor beelden van Resource Manager-sjablonen raadpleegt u de [Azure Quick Start-sjablonen galerie](https://azure.microsoft.com/documentation/templates/) of de bijbehorende [github: Azure Resource Manager Quick](https://github.com/azure/azure-quickstart-templates) start-sjablonen opslag plaats.
* Een definitie van de gebruikers interface voor de Azure-toepassing voor het maken van toepassingen met de naam [createUiDefinition.jsop](../../azure-resource-manager/managed-application-createuidefinition-overview.md).

De maximale bestands grootte die wordt ondersteund zijn:

* Tot 1 GB in totale gecomprimeerde `.zip` Archief grootte
* Maxi maal 1 GB voor elk afzonderlijk ongecomprimeerd bestand in het `.zip` Archief  

Alle nieuwe aanbiedingen van Azure Application moeten ook een GUID voor [klant gebruik van Azure-partners](../azure-partner-customer-usage-attribution.md) bevatten.

>[!Note]
>Als u een probleem hebt met het uploaden van bestanden, moet u ervoor zorgen dat uw lokale netwerk de door https://upload.xboxlive.com Partner Center gebruikte service niet blokkeert.

Voor beheerde toepassings abonnementen is aanvullende informatie op dit tabblad vereist.

#### <a name="previously-published-packages"></a>Eerder gepubliceerde pakketten

Op het tabblad **eerder gepubliceerde pakketten** kunt u alle gepubliceerde versies van uw technische configuratie weer geven.

#### <a name="enable-just-in-time-jit-access"></a>Just-in-time-toegang inschakelen

Selecteer deze optie om just-in-time-toegang (JIT) in te scha kelen voor dit abonnement.  Met JIT-toegang kunt u verhoogde toegang tot de resources van een beheerde toepassing aanvragen voor het oplossen van problemen of onderhoud. U hebt altijd alleen-lezen toegang tot de resources, maar gedurende een bepaalde periode kunt u meer toegang hebben.  Zie [just-in-time-toegang inschakelen en aanvragen voor Azure Managed Applications](../../managed-applications/request-just-in-time-access.md)voor meer informatie.  Als u wilt dat gebruikers van uw beheerde toepassing uw account permanente toegang geven, laat u deze optie uitgeschakeld.

>[!Note]
>Zorg ervoor dat u het bestand bijwerkt `createUiDefinition.json` om deze functie te kunnen ondersteunen.  

#### <a name="deployment-mode"></a>Implementatie modus

Selecteer of u de **volledige** of **incrementele implementatie modus** wilt configureren wanneer u dit plan implementeert: 

* In de **volledige modus**leidt een herimplementatie van de toepassing door de klant tot het verwijderen van resources in de beheerde resource groep als de resources niet zijn gedefinieerd in de `mainTemplate.json` . 
* In de **incrementele modus**blijven bestaande resources ongewijzigd wanneer de toepassing opnieuw wordt geïmplementeerd.

Zie [Azure Resource Manager-implementatie modi](../../azure-resource-manager/deployment-modes.md)voor meer informatie over de implementatie modi.

#### <a name="notification-endpoint-url"></a>URL van meldings eindpunt

Geef een HTTPS-webhook-eind punt op om meldingen over alle ruwe bewerkingen op beheerde toepassings exemplaren van deze plan versie te ontvangen.

#### <a name="customize-allowed-customer-actions"></a>Toegestane klant acties aanpassen

Selecteer deze optie om op te geven welke acties klanten kunnen uitvoeren op de beheerde resources naast de `*/read` acties die standaard beschikbaar zijn. 

Geef een lijst van de aanvullende acties die u uw klant wilt laten uitvoeren, gescheiden door punt komma's.  Zie voor meer informatie [over het weigeren van toewijzingen voor Azure-resources](../../role-based-access-control/deny-assignments.md).  Zie Azure Resource Manager-bewerkingen voor de [resource provider](../../role-based-access-control/resource-provider-operations.md)voor beschik bare acties. Als u bijvoorbeeld wilt toestaan dat gebruikers virtuele machines opnieuw opstarten, `Microsoft.Compute/virtualMachines/restart/action` moet u de toegestane acties toevoegen.

#### <a name="global-azure--azure-government-cloud"></a>Wereld wijd Azure/Azure Government-Cloud

Geef aan wie beheer toegang moet hebben tot deze beheerde toepassing in elke ondersteunde Cloud. Gebruikers, groepen of toepassingen waaraan u machtigingen wilt verlenen voor de beheerde resource groep, worden geïdentificeerd aan de hand van Azure Active Directory (AAD)-identiteiten.

**Azure Active Directory TENANT-** id: de Aad-Tenant-id (ook wel Directory-id genoemd) die de identiteiten bevat van de gebruikers, groepen of toepassingen waaraan u machtigingen wilt verlenen. U vindt uw AAD-Tenant-ID op het Azure Portal in [Eigenschappen voor Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties).

**Autorisaties** : voeg de Azure Active Directory object-id toe van de gebruiker, groep of toepassing waaraan u machtigingen wilt verlenen voor de beheerde resource groep. Identificeer de gebruiker op basis van de principal-ID, die u kunt vinden op de [blade Azure Active Directory gebruikers op de Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers).

Selecteer voor elke principal een van de ingebouwde Azure AD-rollen uit de lijst (eigenaar of bijdrager). De rol die u selecteert, beschrijft de machtigingen die de principal heeft op de resources in het abonnement van de klant. Zie [Ingebouwde rollen voor Azure-resources](../../role-based-access-control/built-in-roles.md) voor meer informatie. Zie [aan de slag met RBAC in de Azure Portal](../../role-based-access-control/overview.md)voor meer informatie over op rollen gebaseerd toegangs beheer (RBAC).

>[!Note]
>Hoewel u Maxi maal 100 autorisaties per Cloud kunt toevoegen, is het over het algemeen eenvoudiger om een Active Directory gebruikers groep te maken en de ID op te geven in de principal-ID. Hierdoor kunt u meer gebruikers toevoegen aan de beheer groep nadat het plan is geïmplementeerd en de nood zaak voor het bijwerken van het plan beperken om meer autorisaties toe te voegen.

#### <a name="policy-settings"></a>Beleidsinstellingen

Pas [Azure-beleid](../../governance/policy/overview.md) toe op uw beheerde toepassing om nalevings vereisten voor de geïmplementeerde oplossing op te geven. Zie [Voorbeelden van Azure Policy](../../governance/policy/samples/index.md) voor beleidsdefinities en de indeling van de parameterwaarden. U kunt Maxi maal vijf beleids regels configureren, en slechts één exemplaar van elke beleids optie. Voor sommige beleids regels zijn aanvullende para meters vereist. De standaard-SKU is vereist voor controle beleid. De naam van het beleid is beperkt tot 50 tekens.

Selecteer **concept opslaan** voordat u doorgaat.

## <a name="co-sell-with-microsoft"></a>Collectieve verkoop met Microsoft

Het is volledig optioneel om informatie op het tabblad samen verkopen op te geven voor het publiceren van uw aanbieding. Het is vereist voor het vergemakkelijkt van de verkoop van Ready en de status van de voor bereidingen op de IP-mede verkoop. De gegevens die u verstrekt, worden door micro soft Sales teams gebruikt om meer te weten te komen over uw oplossing bij de evaluatie van de geschiktheid voor klanten behoeften. Het is niet rechtstreeks beschikbaar voor klanten.

Zie voor meer informatie over dit tabblad [co-sell optie in Partner Center](commercial-marketplace-co-sell.md).

## <a name="resell-through-csps"></a>Door de Csp's door verkopen

Breid het bereik van uw aanbieding uit door deze beschikbaar te maken voor partners in het programma voor [Cloud Solution Providers](https://azure.microsoft.com/offers/ms-azr-0145p/) (CSP). Zo kunnen wederverkopers uw aanbieding verkopen aan hun klanten en gebundelde oplossingen maken.

Selecteer **concept opslaan** voordat u doorgaat.

## <a name="test-drive"></a>Station testen

Stel een demonstratie (test drive) in waarmee klanten uw aanbieding kunnen uitproberen voordat ze deze aanschaffen. Voor het maken van een demonstratie omgeving waarmee klanten uw aanbieding voor een bepaalde periode kunnen uitproberen, raadpleegt u [uw aanbieding in de commerciële Marketplace testen](test-drive.md).

Als u een test drive wilt inschakelen, schakelt u het selectie vakje **een test drive inschakelen** in op het tabblad installatie van de [aanbieding](#test-drive) . Als u test drive uit uw aanbieding wilt verwijderen, schakelt u dit selectie vakje uit.

### <a name="test-drive-technical-configuration"></a>Technische configuratie van test drive

- **Azure AD-App-ID** (vereist): voer uw Azure Active Directory (AD) [-toepassings-id](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)) in. Als u deze ID wilt vinden, meldt u zich aan bij de [Azure Portal](https://portal.azure.com/), selecteert u het tabblad Active Directory in het menu aan de linkerkant, selecteert u **app-registraties**en zoekt u naar het id-nummer van de **toepassing** die wordt weer gegeven (bijvoorbeeld 50c464d3-4930-494c-963c-1e951d15360e).

#### <a name="deployment-subscription-details"></a>Details van implementatie abonnement

Als u wilt dat het test station namens u kan worden geïmplementeerd, maakt en geeft u een afzonderlijk, uniek Azure-abonnement (niet vereist voor Power BI test stations).

* **Azure-abonnements-id** (vereist voor Azure Resource Manager en Logic apps): Voer de abonnements-id in om toegang te verlenen tot uw Azure-account services voor rapportage en facturering van resource gebruik. Het is raadzaam om [een afzonderlijk Azure-abonnement te maken](../../billing/billing-create-subscription.md) dat u voor test stations kunt gebruiken als u er nog geen hebt. U kunt uw Azure-abonnements-ID vinden door u aan te melden bij de [Azure Portal](https://portal.azure.com/) en te navigeren naar het tabblad **abonnementen** van het menu aan de linkerkant. Als u het tabblad selecteert, wordt uw abonnements-ID weer gegeven (bijvoorbeeld "a83645ac-1234-5ab6-6789-1h234g764ghty").
* **Azure AD-Tenant-id** (vereist): voer uw Azure Active Directory (AD) [Tenant-id](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)) in. Als u deze ID wilt vinden, meldt u zich aan bij de [Azure Portal](https://portal.azure.com/), selecteert u het tabblad Active Directory in het menu links, selecteert u **Eigenschappen**en zoekt u naar de weer gegeven **Directory-id** (zoals 50c464d3-4930-494c-963c-1e951d15360e). U kunt ook de Tenant-ID van uw organisatie opzoeken met uw domein naam-URL op: [https://www.whatismytenantid.com](https://www.whatismytenantid.com) .
* **Naam van Azure AD-Tenant** (vereist voor dynamische 365): Voer de naam van uw Azure Active Directory (AD) in. Als u deze naam wilt vinden, meldt u zich aan bij de [Azure Portal](https://portal.azure.com/). in de rechter bovenhoek wordt de naam van de Tenant vermeld onder uw account naam.
* **Azure AD-App-ID** (vereist): voer uw Azure Active Directory (AD) [-toepassings-id](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)) in. Als u deze ID wilt vinden, meldt u zich aan bij de [Azure Portal](https://portal.azure.com/), selecteert u het tabblad Active Directory in het menu links Navigator, selecteert u **app-registraties**en zoekt u naar het **toepassings-id** -nummer dat wordt weer gegeven (bijvoorbeeld 50c464d3-4930-494c-963c-1e951d15360e).
* **Azure Active Directory toepassings client geheim** (vereist): Voer uw Azure AD-toepassings [client geheim](../../active-directory/develop/howto-create-service-principal-portal.md#create-a-new-application-secret)in. Als u deze waarde wilt vinden, meldt u zich aan bij de [Azure Portal](https://portal.azure.com/). Selecteer in het menu links het tabblad **Azure Active Directory** , selecteer **app-registraties**en selecteer vervolgens uw test drive-app. Selecteer vervolgens **certificaten en geheimen**, selecteer **Nieuw client geheim**, voer een beschrijving in, selecteer **nooit** onder **verlooptd**en kies vervolgens **toevoegen**. Zorg ervoor dat u de waarde kopieert voordat u deze pagina verlaat.)

Selecteer **concept opslaan** voordat u doorgaat.

### <a name="marketplace-listing-optional"></a>Marketplace-vermelding (optioneel)

Beschrijf de test drive ervaring.

<!-- The **Test Drive listings** option found under the **Test drive** tab displays the languages (and markets) where your test drive is available (currently English (United States) is the only location available). Additionally, this page displays the status of the language-specific listing and the date/time that it was added. You will need to define the test drive details (description, user manual, videos, etc.) for each language/market. -->

* **Beschrijving** (vereist): Geef een beschrijving van uw test drive, wat wordt aangetoond, wat de gebruiker kan doen om te experimenteren met, functies om te verkennen en alle relevante informatie waarmee de gebruiker kan bepalen of u uw aanbieding kunt verkrijgen. In dit veld kunnen Maxi maal 3.000 tekens tekst worden ingevoerd. 
* **Toegangs gegevens** (vereist voor Azure Resource Manager en logische test stations): Leg uit wat een klant moet weten om deze test drive te kunnen openen en gebruiken. Door loop een scenario voor het gebruik van uw aanbieding en precies wat de klant moet weten voor toegang tot de functies in de test drive. In dit veld kunnen Maxi maal 10.000 tekens tekst worden ingevoerd.
* **Gebruikers handleiding** (vereist): een diep gaande uitleg van uw test drive-ervaring. De gebruikers handleiding moet betrekking hebben op precies wat u wilt dat de klant het test drive verkrijgt en zich als referentie voor eventuele vragen bevindt. Het bestand moet een PDF-indeling hebben en een naam hebben (255 tekens Max) na het uploaden.
* **Video's: Video's toevoegen** (optioneel): Video's kunnen worden geüpload naar YouTube of Vimeo en hiernaar wordt verwezen met een koppeling en een miniatuur afbeelding (533 x 324 pixels), zodat een klant een overzicht van de informatie kan bekijken om hen meer inzicht te geven in de test drive, met inbegrip van de manier waarop u de functies van uw aanbieding kunt gebruiken en inzicht kunt krijgen in de voor delen.
  * **Naam** (vereist)
  * **Adres** (alleen YouTube of Vimeo; vereist)
  * **Miniatuur** (het afbeeldings bestand moet de PNG-indeling hebben en 533 x 324 pixels).

Selecteer **concept opslaan** voordat u doorgaat.

## <a name="publish"></a>Publiceren

Wanneer u alle vereiste secties van de aanbieding hebt ingevuld, selecteert u **controleren en publiceren** in de rechter bovenhoek van de portal.

Bekijk de voltooiings status voor elke sectie van de aanbieding.
    - *Niet gestart* : de sectie is niet gerakend en moet worden voltooid.
    - *Onvolledig* : de sectie bevat fouten die moeten worden hersteld of waarvoor meer informatie moet worden verstrekt. Ga terug naar de sectie (s) en werk deze bij.
    - *Voltooid* : de sectie is voltooid, alle vereiste gegevens zijn opgegeven en er zijn geen fouten. Alle secties van de aanbieding moeten een volledige status hebben voordat u de aanbieding kunt indienen.

Als dit de eerste keer is dat u deze aanbieding publiceert, kunt u het certificerings team voorzien van test instructies om ervoor te zorgen dat uw app correct wordt getest, naast eventuele aanvullende notities die nuttig zijn voor de uitleg van uw app.

Selecteer **verzenden** om uw aanbieding voor publicatie te verzenden. We sturen u een e-mail om u te laten weten wanneer een preview-versie van de aanbieding beschikbaar is om te controleren en goed te keuren.

Ga terug naar het partner centrum en selecteer **Go-Live** voor de aanbieding om uw aanbieding te publiceren naar het open bare (of als u een privé-aanbieding naar de persoonlijke doel groep hebt).

### <a name="errors-and-review-feedback"></a>Fouten en feedback bekijken

De **hand matige validatie** stap in het publicatie proces vertegenwoordigt een uitgebreide beoordeling van uw aanbieding en de bijbehorende technische activa (met name de Azure Resource Manager-sjabloon), problemen worden doorgaans gepresenteerd als pull-aanvraag (PR)-koppelingen. Zie [afhandeling van feedback](./azure-apps-review-feedback.md)bekijken voor meer informatie over het weer geven en reageren op deze pull.

Als er fouten zijn opgetreden in een of meer van de publicatie stappen, corrigeert u deze voordat u uw aanbieding opnieuw publiceert.

## <a name="next-steps"></a>Volgende stappen

* [Een bestaande aanbieding bijwerken in Commerciële Marketplace](./update-existing-offer.md)
