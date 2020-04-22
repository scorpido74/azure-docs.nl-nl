---
title: Een Azure-aanbieding voor virtuele machines maken - Azure Marketplace
description: Meer informatie over het maken van een virtuele machine aanbieding in de commerciële markt.
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/10/2020
ms.openlocfilehash: d5626f00e9627338349d9b579bcf26256148b551
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731270"
---
# <a name="create-an-azure-virtual-machine-offer"></a>Een Azure-aanbieding voor virtuele machines maken

> [!IMPORTANT]
> We verplaatsen het beheer van uw Azure VM-aanbiedingen van Cloud Partner Portal naar Partner Center. Totdat uw aanbiedingen zijn gemigreerd, u de instructies in [De virtuele machine-aanbieding maken](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-offer) in Cloud Partner Portal blijven volgen om uw aanbiedingen te beheren.

In dit artikel wordt beschreven hoe u een Azure-aanbieding voor virtuele machines op [Azure Marketplace](https://azuremarketplace.microsoft.com/)maken en publiceren. Het richt zich op zowel Windows- als Linux-gebaseerde virtuele machines die een besturingssysteem, virtuele harde schijf (VHD) en maximaal 16 gegevensschijven bevatten.

## <a name="introduction"></a>Inleiding

### <a name="publishing-benefits"></a>Publicatievoordelen

Publiceren op Azure Marketplace heeft de volgende voordelen:

- Uw bedrijf promoten met het Microsoft-merk
- Bereik meer dan 100 miljoen Office 365- en Dynamics 365-gebruikers en meer dan 200.000 organisaties via Azure Marketplace
- Haal leads van hoge kwaliteit uit deze marktplaatsen
- Laat uw services promoten door de microsoft-veld- en televerkoopteams

### <a name="before-you-begin"></a>Voordat u begint

Als u dit nog niet hebt gedaan, bekijkt u de [publicatiehandleiding voor virtuele machines](https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines) van de virtuele machine van Virtual en dit azure-materiaal voor virtuele machines:

- Quickstart-hulplijnen
  - [Azure Quickstart-sjablonen](https://azure.microsoft.com/resources/templates/)
  - [GitHub Azure Quickstart-sjablonen](https://github.com/azure/azure-quickstart-templates)
- Zelfstudies
  - [Virtuele Linux-machines](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
  - [Virtuele Windows-machines](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm)
- Voorbeelden
  - [Azure CLI-samples voor Linux VM's](https://docs.microsoft.com/azure/virtual-machines/linux/cli-samples)
  - [Azure PowerShell voor Linux VM's](https://docs.microsoft.com/azure/virtual-machines/linux/powershell-samples)
  - [Azure CLI-voorbeelden voor Windows VM's](https://docs.microsoft.com/azure/virtual-machines/windows/cli-samples)
  - [Azure PowerShell voor Windows VM's](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-quick)

### <a name="fundamentals-in-technical-knowledge"></a>Fundamenten in technische kennis

Het ontwerpen, bouwen en testen van deze assets kost tijd en vereist technische kennis van zowel het Azure-platform als de technologieën die worden gebruikt om het aanbod te bouwen.

Uw engineeringteam moet de volgende Microsoft-technologieën begrijpen:

- Basiskennis van [Azure Services](https://azure.microsoft.com/services/)
- [Azure-toepassingen ontwerpen en ontwerpen](https://azure.microsoft.com/solutions/architecture/)
- Werkkennis van [Azure Virtual Machines,](https://azure.microsoft.com/services/virtual-machines/) [Azure Storage](https://azure.microsoft.com/services/?filter=storage#storage)en Azure [Networking](https://azure.microsoft.com/services/?filter=networking#networking)

## <a name="create-an-azure-virtual-machine-offer"></a>Een Azure-aanbieding voor virtuele machines maken

Voordat u een Azure-aanbieding voor virtuele machines maken, moet u een commercieel marktplaatsaccount hebben in Partnercenter. Zie Een account voor commerciële [marktplaatsmaken in partnercentrum](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)als u er nog geen hebt gemaakt.

1. Meld u aan bij [Partnercentrum](https://partner.microsoft.com/dashboard/home)en **selecteer**dashboard in het bovenste menu .
2. Selecteer in de linkernavigatiebalk **Commerciële marktplaats**en **vervolgens Overzicht**.
3. Selecteer **op** de pagina Overzicht de optie **+ Nieuwe aanbieding,** vervolgens **Azure Virtual Machine**. Het dialoogvenster **Nieuwe aanbieding** wordt weergegeven.

    ![Hiermee wordt de overzichtspagina in partnercentrum weergegeven met de knop Nieuwe aanbieding en azure virtual machine-aanbieding geselecteerd.](media/avm-create7.png)

## <a name="offer-id-and-alias"></a>Aanbiedings-ID en alias

Voer een **aanbiedings-id in**. Dit is een unieke id voor elke aanbieding in uw account.

- Deze id is zichtbaar voor klanten op het webadres voor de marktplaatsaanbieding en in Azure PowerShell en Azure CLI, indien van toepassing.
- Gebruik alleen kleine letters en cijfers. Het kan koppeltekens en underscores bevatten, maar geen spaties, en is beperkt tot 50 tekens. Als u hier bijvoorbeeld **test-aanbieding-1** invoert, is `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`het webadres van de aanbieding .
- De aanbiedings-id kan niet worden gewijzigd nadat u **Maken**hebt geselecteerd.

Voer een **aanbiedingsalias**in . Dit is de naam die wordt gebruikt voor de aanbieding in Partner Center. Deze naam wordt niet gebruikt in de markt en verschilt van de aanbiedingsnaam en andere waarden die aan klanten worden weergegeven.

Selecteer **Maken** om de aanbieding te genereren en verder te gaan.

## <a name="offer-setup"></a>Instelling voor aanbieding

### <a name="test-drive"></a>Proefrit

Stel een demonstratie (proefrit) in waarmee klanten uw aanbieding kunnen uitproberen voordat ze het kopen. Zie Test Drive uw [aanbieding op de commerciële marktplaats](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive)om een demonstratieomgeving te maken waarmee klanten uw aanbieding voor een bepaalde tijd kunnen uitproberen.

Als u een testrit wilt inschakelen, schakelt u het selectievakje **Een teststation inschakelen** in. Als u een proefrit uit uw aanbieding wilt verwijderen, schakelt u dit selectievakje uit.

Aanvullende bronnen voor teststations:

- [Technische best practices](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Best practices voor marketing](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Overzicht van teststations](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) PDF (zorg ervoor dat uw pop-upblokkering is uitgeschakeld).

### <a name="lead-management"></a>Leadbeheer

Wanneer u uw aanbieding publiceert op de commerciële marktplaats met Partner Center, koppelt u deze aan uw CRM-systeem (Customer Relationship Management). Hiermee u klantcontactgegevens ontvangen zodra iemand interesse toont in of uw product gebruikt. Verbinding maken met een CRM is vereist als u **Test Drive** inschakelt (zie vorige sectie), anders is het optioneel.

1. Selecteer een leadbestemming waarnaar wij de klantenleads moeten sturen. Partnercenter ondersteunt de volgende CRM-systemen:
    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) voor klantbetrokkenheid
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [SalesForce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Als uw CRM-systeem hierboven niet wordt vermeld, gebruikt u [Azure Table](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) of [Https Endpoint](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) om klantgegevens op te slaan. Exporteer de gegevens vervolgens naar uw CRM-systeem.

2. Verbind uw aanbieding met de hoofdbestemming wanneer u publiceert in partnercentrum.
3. Controleer of de verbinding met de hoofdbestemming correct is geconfigureerd. Nadat u deze hebt gepubliceerd in het Partnercenter, valideren we de verbinding en sturen we u een testlead. Terwijl u een voorbeeld van de aanbieding bekijkt voordat deze live gaat, u ook uw leadverbinding testen door te proberen het aanbod zelf te implementeren in de preview-omgeving.
4. Zorg ervoor dat de verbinding met de hoofdbestemming up-to-date blijft, zodat u geen leads verliest.

Selecteer **Concept opslaan** voordat u verdergaat.

## <a name="properties"></a>Eigenschappen

Op deze pagina u de categorieën en bedrijfstakken definiëren die worden gebruikt om uw aanbieding op de marktplaats, uw app-versie en de juridische contracten die uw aanbieding ondersteunen, te groeperen.

### <a name="categories"></a>Categorieën

Selecteer minimaal één en maximaal vijf categorieën. Deze categorieën worden gebruikt om uw aanbieding in de juiste zoekgebieden op marktplaats te plaatsen. Leg in de aanbiedingsbeschrijving uit hoe uw aanbieding deze categorieën ondersteunt. Aanbiedingen voor virtuele machines worden weergegeven onder de categorie **Compute** in Azure Marketplace.

### <a name="legal"></a>Juridisch

U moet algemene voorwaarden voor de aanbieding opgeven. U hebt hiervoor twee opties:

- Gebruik je eigen algemene voorwaarden
- Het standaardcontract gebruiken voor de commerciële marktplaats van Microsoft

#### <a name="use-your-own-terms-and-conditions"></a>Gebruik je eigen algemene voorwaarden

Als u uw eigen aangepaste algemene voorwaarden wilt opgeven, voert u maximaal 10.000 tekens tekst in het vak **Algemene voorwaarden** in. Als u een langere beschrijving nodig hebt, voert u één webadres in dat aangeeft waar uw algemene voorwaarden kunnen worden gevonden. Het zal worden weergegeven aan klanten als een actieve link.

Klanten moeten deze voorwaarden accepteren voordat ze uw aanbieding kunnen proberen.

#### <a name="use-the-standard-contract-for-the-microsoft-commercial-marketplace"></a>Het standaardcontract gebruiken voor de commerciële marktplaats van Microsoft

Om het inkoopproces voor klanten te vereenvoudigen en de juridische complexiteit voor softwareleveranciers te verminderen, biedt Microsoft een standaardcontract voor de commerciële marktplaats. Wanneer u uw software aanbiedt in het kader van het standaardcontract, hoeven klanten deze slechts één keer te lezen en te accepteren en hoeft u geen aangepaste algemene voorwaarden te maken.

Gebruik het standaardcontract door het selectievakje **Het standaardcontract voor de commerciële marktplaats van Microsoft gebruiken** in te schakelen en vervolgens het pop-upvenster **accepteren** (u moet mogelijk omhoog scrollen om het te zien).

![Hiermee wordt de overzichtspagina in partnercentrum geïllustreerd met de geselecteerde knop Nieuwe aanbieding en consultingserviceaanbieding.](media/use-standard-contract.png)

> [!NOTE]
> Nadat u een aanbieding hebt gepubliceerd met behulp van het standaardcontract voor commerciële marktplaats, u uw eigen aangepaste algemene voorwaarden niet gebruiken. Bied uw oplossing aan onder het standaardcontract **of** onder uw eigen voorwaarden.

Zie Standaardcontract voor de [commerciële marktplaats](https://docs.microsoft.com/azure/marketplace/standard-contract)van Microsoft voor meer informatie over het standaardcontract. U het [standaardcontract](https://go.microsoft.com/fwlink/?linkid=2041178) als PDF downloaden (zorg ervoor dat uw pop-upblokkering is uitgeschakeld).

##### <a name="standard-contract-amendments"></a>Standaardcontractwijzigingen

Met standaardcontractwijzigingen u de standaardcontractvoorwaarden voor eenvoud selecteren en de voorwaarden voor uw product of bedrijf maken. Klanten hoeven de wijzigingen in het contract alleen te bekijken als ze het Microsoft Standard-contract al hebben beoordeeld en geaccepteerd. Er zijn twee soorten amendementen beschikbaar: universeel en op maat.

**Universele wijzigingen** - Deze worden universeel toegepast op het standaardcontract voor alle klanten. Ze worden getoond aan elke klant van het aanbod in de aankoopstroom. Klanten moeten de voorwaarden van het standaardcontract en de wijziging(en) accepteren voordat ze uw aanbieding kunnen gebruiken. Per aanbieding u één universele wijziging indienen. U een onbeperkt aantal tekens invoeren in dit vak. Deze termen worden weergegeven aan klanten in AppSource, Azure Marketplace en/of Azure-portal tijdens de detectie- en aankoopstroom.

**Aangepaste wijzigingen** : dit zijn speciale wijzigingen in het standaardcontract die zijn gericht op specifieke klanten via Azure-tenant-id's. U de tenant kiezen die u wilt targeten. Alleen klanten van de tenant krijgen de aangepaste wijzigingsvoorwaarden in de aankoopstroom van de aanbieding te zien. Klanten moeten de voorwaarden van het standaardcontract en de wijziging(en) accepteren voordat ze uw aanbieding kunnen gebruiken.

Begin met het selecteren **van Aangepaste wijzigingstermen toevoegen (Max 10).** Per aanbieding u maximaal tien aangepaste wijzigingsvoorwaarden opgeven.

- **Aangepaste wijzigingsvoorwaarden** – Voer uw eigen wijzigingsvoorwaarden in het vak aangepaste wijzigingsvoorwaarden in. U een onbeperkt aantal tekens invoeren. Alleen klanten van de tenant-id's die u voor deze aangepaste voorwaarden opgeeft, zien deze in de aankoopstroom van de aanbieding in de Azure-portal.
- **Tenant-id's** (vereist) – Elke aangepaste wijziging kan worden gericht op maximaal 20 tenant-id's. Als u een aangepaste wijziging toevoegt, moet u ten minste één tenant-id opgeven, waarmee uw klant in Azure wordt geïdentificeerd. uw klant kan voor u zoeken in azure onder en vervolgens Eigenschappen. De directory-ID-waarde is de tenant-id (bijvoorbeeld 50c464d3-4930-494c-963c-1e951d15360e). U ook de tenant-id van uw klant van de organisatie vinden met behulp van het webadres van hun domeinnaam op [Wat is mijn Microsoft Azure- en Office 365-tenant-id?](https://www.whatismytenantid.com/).
- **Beschrijving** (optioneel) - Geef een vriendelijke beschrijving voor de tenant-id waarmee u de klant identificeren die u met de wijziging target.

> [!NOTE]
> Deze twee soorten amendementen stapelen op elkaar. Klanten die zijn gericht op aangepaste wijzigingen krijgen ook de universele wijziging van het standaardcontract tijdens de aankoop.

Selecteer **Concept opslaan** voordat u verdergaat.

## <a name="offer-listing"></a>Aanbieding aanbieden

Op deze pagina u aanbiedingsdetails definiëren, zoals aanbiedingsnaam, beschrijving, koppelingen en contactpersonen.

> [!NOTE]
> Aanbieding sominhoud (zoals de beschrijving, documenten, screenshots en gebruiksvoorwaarden) is niet verplicht om in het Engels te zijn, zolang de aanbiedingsbeschrijving begint met de zinsnede :"Deze toepassing is alleen beschikbaar in [niet-Engelstalige taal]." U ook een _nuttig link-webadres_ opgeven om inhoud aan te bieden in een andere taal dan die welke wordt gebruikt in de inhoud van de aanbiedingsvermelding.

### <a name="marketplace-details"></a>Marketplace-gegevens

#### <a name="name"></a>Naam

De naam die u hier invoert, wordt aan klanten weergegeven als de titel van uw aanbiedingsvermelding. Dit veld is vooraf gevuld met de tekst die u hebt ingevoerd in het vak **Alias aanbieden** toen u de aanbieding hebt gemaakt. U kunt deze naam later wijzigen.

De naam:

- Kan een handelsmerk hebben (en u handelsmerk- en auteursrechtsymbolen opnemen)
- Kan niet langer zijn dan 50 tekens
- Kan geen emoji's bevatten.

#### <a name="search-results-summary"></a>Overzicht van zoekresultaten

Een korte beschrijving van uw aanbod. Dit kan maximaal 100 tekens lang zijn en wordt gebruikt in de zoekresultaten van marktplaatsen.

#### <a name="long-summary"></a>Lange samenvatting

Geef een langere beschrijving van uw aanbod. Dit kan maximaal 256 tekens lang zijn en wordt gebruikt in de zoekresultaten van marktplaatsen.

#### <a name="description"></a>Beschrijving

Geef een gedetailleerde beschrijving van uw aanbieding, tot 3.000 tekens. Dit wordt weergegeven aan klanten in het overzicht van de commerciële marktplaatsvermelding.

Neem een of meer van de volgende gegevens op in uw beschrijving:

- De waarde en de belangrijkste voordelen van uw aanbod
- Categorie- of brancheorganisaties, of beide
- In-app aankoopmogelijkheden
- Eventuele vereiste informatie

Hier zijn enkele tips voor het schrijven van uw beschrijving:

- Beschrijf duidelijk de waardepropositie van uw aanbod in de eerste paar zinnen van uw beschrijving. Neem de volgende items op:
  - Beschrijving van uw aanbod.
  - Het type gebruiker dat profiteert van uw aanbod.
  - Behoeften van de klant of problemen die het aanbod aanpakt.
- Vergeet niet dat de eerste paar zinnen kunnen worden weergegeven in de resultaten van zoekmachines.
- Vertrouw niet op functies en functionaliteit om uw aanbod te verkopen. Richt u in plaats daarvan op de waarde die uw aanbieding biedt.
- Gebruik branchespecifieke of op baten gebaseerde woorden.

Als u de beschrijving van uw aanbieding aantrekkelijker wilt maken, gebruikt u de teksteditor met rijke tekst om uw beschrijving op te maken. Met de rich text editor u getallen, opsommingstekens, vet, cursief en inspringingen toevoegen om uw beschrijving leesbaarder te maken.

![Hiermee wordt de overzichtspagina in partnercentrum geïllustreerd met de geselecteerde knop Nieuwe aanbieding en consultingserviceaanbieding.](media/power-bi-rich-text-editor.png)

#### <a name="privacy-policy-link"></a>Koppeling Privacybeleid

Voer het webadres (URL) in voor het privacybeleid van uw organisatie. Zorg ervoor dat uw aanbod voldoet aan de privacywet- en regelgeving. U moet ook een geldig privacybeleid op uw website plaatsen.

### <a name="useful-links"></a>Handige koppelingen

Geef aanvullende online documenten over uw aanbod. Als u een koppeling wilt toevoegen, selecteert u **+ Een koppeling toevoegen** en voltooit u de volgende velden:

- **Naam** – Klanten zien de naam op de detailpagina.
- **Link (URL)** – Voer een koppeling in voor klanten om uw online document te bekijken.

### <a name="customer-support-links"></a>Koppelingen voor klantenondersteuning

Zorg voor de ondersteuningswebsite waar klanten uw ondersteuningsteam kunnen bereiken.

- Ondersteuningswebsite van Azure Global
- Ondersteuningswebsite van Azure Government

### <a name="partner-support-contact"></a>Contactpersoon voor partnerondersteuning

Geef contactgegevens op die Microsoft-partners kunnen gebruiken wanneer uw klanten een ondersteuningsticket openen. Dit zal niet worden vermeld in de markt.

- Naam
- Email
- Telefoon

### <a name="engineering-contact"></a>Technisch contact

Geef contactgegevens op die Microsoft kan gebruiken wanneer er problemen zijn met uw aanbieding, waaronder problemen met de certificering. Dit zal niet worden vermeld in de markt.

- Naam
- Email
- Telefoon

### <a name="marketplace-media"></a>Marktplaatsmedia

Geef logo's en afbeeldingen om te gebruiken met uw aanbod. Alle afbeeldingen moeten in PNG-formaat zijn. Onscherpe afbeeldingen zorgen ervoor dat uw inzending wordt afgewezen.

#### <a name="marketplace-logos"></a>Marketplace-logo's

Png-bestanden van het logo van uw aanbieding in de volgende vier pixelformaten optebieden:

- **Klein** (48 x 48)
- **Gemiddeld** (90 x 90)
- **Groot** (216 x 216)
- **Breed** (255 x 115)

Alle vier de logo's zijn vereist en worden gebruikt op verschillende plaatsen in de marktplaats vermelding.

#### <a name="screenshots"></a>Schermopnamen

Voeg maximaal vijf screenshots toe die laten zien hoe uw aanbieding werkt. Elke schermafbeelding moet 1280 x 720 pixels groot en in PNG-formaat hebben. Je moet ook een bijschrift toevoegen om je screenshot te beschrijven.

#### <a name="videos"></a>Video's

Voeg maximaal vijf video's toe die je aanbod demonstreren. Deze moeten worden gehost op een externe videodienst. Voer de naam, het webadres en een png-miniatuurafbeelding van de video in met een grootte van 1280 x 720 pixels in.

Zie [Aanbevolen procedures voor aanbiedingen voor marktplaatsaanbiedingen voor](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)extra bronnen voor marktplaatsvermeldingen .

Selecteer **Concept opslaan** voordat u verdergaat.

## <a name="preview"></a>Preview

Kies op het tabblad Voorbeeld een beperkte **preview-doelgroep** voor het valideren van je aanbieding voordat je het live publiceert naar de bredere doelgroep.

> [!IMPORTANT]
> Nadat u uw aanbieding in Preview hebt gecontroleerd, selecteert u **Live gaan** om uw aanbieding te publiceren naar het publiek op de commerciële marktplaats.

Uw preview-doelgroep wordt geïdentificeerd door AZURE-guid's voor azure-abonnementen, samen met een optionele beschrijving voor elk. Geen van deze velden kan worden gezien door klanten. U uw Azure-abonnements-id vinden op de pagina **Abonnementen** in azure-portal.

Voeg ten minste één Azure-abonnements-id toe, afzonderlijk (maximaal 10) of door een CSV-bestand te uploaden (maximaal 100). Door deze abonnements-id's toe te voegen, bepaalt u wie een voorbeeld van uw aanbieding kan bekijken voordat deze live wordt gepubliceerd. Als je aanbieding al live is, kun je nog steeds een preview-doelgroep definiëren voor het testen van wijzigingen in je aanbieding of updates van je aanbieding.

> [!NOTE]
> Een preview-publiek verschilt van een privépubliek. Een preview-doelgroep heeft toegang tot uw aanbieding _voordat_ deze live in de marketplaces wordt gepubliceerd. Ze kunnen alle plannen bekijken en valideren, inclusief plannen die alleen beschikbaar zijn voor een privépubliek nadat uw aanbieding volledig is gepubliceerd op de marktplaats. Een privépubliek (gedefinieerd in het tabblad **Prijzen en Beschikbaarheid** van het abonnement) heeft exclusieve toegang tot een bepaald abonnement.

Selecteer **Concept opslaan** voordat u doorgaat naar de volgende sectie, Overzicht plannen.

## <a name="plan-overview"></a>Overzicht van plannen

U verschillende abonnementsopties bieden binnen dezelfde aanbieding in partnercentrum. Deze plannen werden eerder aangeduid als SKU's. Een aanbieding vereist ten minste één abonnement, dat kan verschillen in termen van doelgroep voor het genereren van inkomsten, Azure-regio's, functies of VM-afbeeldingen.

Nadat u uw plannen hebt gemaakt, wordt op het tabblad **Overzicht van abonnementen** het volgende weergegeven:

- Namen plannen
- Licentiemodellen
- Publiek (openbaar of privé)
- Huidige publicatiestatus
- Beschikbare acties

Acties die beschikbaar zijn in het overzicht van het plan, zijn afhankelijk van de huidige status van uw abonnement. Deze omvatten:

- **Concept verwijderen** : als de status van het plan een concept is
- **Verkoopplan** stoppen of **privépubliek synchroniseren** : als de status van het plan live wordt gepubliceerd

### <a name="create-new-plan"></a>Nieuw plan maken

Selecteer **+ Maak bovenaan een nieuw plan.** Het dialoogvenster **Nieuw plan** wordt weergegeven.

Maak in het vak **Plan-ID** een unieke plan-ID voor elk abonnement in deze aanbieding. Deze ID is zichtbaar voor klanten in het productwebadres. Gebruik alleen kleine letters en cijfers, streepjes of underscores en maximaal 50 tekens.

> [!NOTE]
> De plan-id kan niet worden gewijzigd nadat u Maken hebt **geselecteerd.**

Voer **in** het vak Naam van het plan een naam voor dit plan in. Klanten zien deze naam wanneer ze beslissen welk plan ze willen selecteren binnen uw aanbieding. Maak een unieke naam die duidelijk wijst op de verschillen van elk plan. U bijvoorbeeld **Windows Server** gebruiken met abonnementen **Pay-as-you-go,** **BYOL,** **Advanced**en **Enterprise**.

Selecteer **Maken**.

### <a name="plan-setup"></a>Installatie plannen

Stel de configuratie op hoog niveau in voor het type abonnement, of het de technische configuratie van een ander abonnement opnieuw gebruikt en in welke Azure-regio's het abonnement beschikbaar moet zijn. Uw selecties hier bepalen welke velden worden weergegeven op andere tabbladen voor hetzelfde plan.

#### <a name="reuse-technical-configuration"></a>Technische configuratie opnieuw gebruiken

Als u meer dan één abonnement van hetzelfde type hebt en de pakketten onderling identiek zijn, u deze optie wijzigen als technische configuratie uit een ander abonnement wordt **gebruikt.** Met deze optie u een van de andere plannen van hetzelfde type voor deze aanbieding selecteren en de technische configuratie opnieuw gebruiken.

> [!NOTE]
> Wanneer u de technische configuratie van een ander abonnement opnieuw gebruikt, verdwijnt het volledige tabblad **Technische configuratie** uit dit plan. De technische configuratiedetails van het andere abonnement, inclusief eventuele updates die u in de toekomst maakt, worden ook voor dit plan gebruikt. Deze instelling kan niet worden gewijzigd nadat dit plan is gepubliceerd.

#### <a name="azure-regions"></a>Azure-regio's

Uw abonnement moet beschikbaar worden gesteld in ten minste één Azure-regio.

Selecteer de azure **global-optie** om uw abonnement beschikbaar te maken voor klanten in alle openbare Azure-regio's met commerciële marktintegratie. Zie Geografische [beschikbaarheid en valutaondersteuning](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies)voor meer informatie .

Selecteer de optie **Azure Government** om uw abonnement beschikbaar te maken in het [azure-overheidsgebied.](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome) Deze regio biedt gecontroleerde toegang voor klanten uit Amerikaanse federale, staats-, lokale of tribale entiteiten, evenals partners die in aanmerking komen om hen te bedienen. U, als uitgever, bent verantwoordelijk voor nalevingscontroles, beveiligingsmaatregelen en aanbevolen procedures. Azure Government maakt gebruik van fysiek geïsoleerde datacenters en netwerken (alleen in de VS).

Voordat u publiceert naar [Azure Government,](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)test en valideert u uw abonnement in de omgeving, omdat bepaalde eindpunten kunnen verschillen. Als u uw abonnement wilt instellen en testen, vraagt u een proefaccount aan bij de proefversie van [Microsoft Azure Government.](https://azure.microsoft.com/global-infrastructure/government/request/)

> [!NOTE]
> Nadat uw abonnement is gepubliceerd en beschikbaar is in een specifieke Azure-regio, u dat gebied niet verwijderen.

#### <a name="azure-government-certifications"></a>Azure Government-certificeringen

Deze optie is alleen zichtbaar als u **Azure Government** selecteert onder **Azure-gebieden.**

Azure Government-services verwerken gegevens die onderworpen zijn aan bepaalde overheidsvoorschriften en -vereisten. Bijvoorbeeld FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 en CJIS. Om uw certificeringen voor deze programma's onder de aandacht te brengen, u maximaal 100 koppelingen aanbieden die ze beschrijven. Dit kunnen links naar uw vermelding op het programma direct of links naar beschrijvingen van uw naleving met hen op uw eigen websites. Deze koppelingen die alleen zichtbaar zijn voor azure-klanten.

Selecteer **Concept opslaan** voordat u verdergaat.

### <a name="plan-listing"></a>Aanbieding plannen

Hier configureert u de vermeldingsdetails van het abonnement. Op dit tabblad worden specifieke informatie weergegeven die kan verschillen tussen plannen in dezelfde aanbieding.

#### <a name="plan-name"></a>Naam plannen

Dit is vooraf ingevuld met de naam die u uw plan hebt gegeven toen u het maakte. Deze naam wordt in de markt weergegeven als de titel van dit plan en is beperkt tot 100 tekens.

#### <a name="plan-summary"></a>Overzicht plannen

Geef een korte samenvatting van uw plan (niet de aanbieding). Deze samenvatting is beperkt tot 100 tekens.

#### <a name="plan-description"></a>Beschrijving van het plan

Beschrijf wat dit softwareplan uniek maakt, evenals verschillen tussen plannen binnen uw aanbod. Beschrijf het aanbod niet, alleen het plan. De beschrijving van het plan kan maximaal 2.000 tekens bevatten.

Selecteer **Concept opslaan** voordat u verdergaat.

### <a name="pricing-and-availability"></a>Prijzen en beschikbaarheid

Op dit tabblad configureert u het volgende:

- Markten dit plan zal beschikbaar zijn in
- De prijs per uur
- Of u het plan zichtbaar wilt maken voor iedereen of alleen voor specifieke klanten (een privépubliek)

#### <a name="markets"></a>Markten

Elk plan moet beschikbaar zijn in ten minste één markt. Schakel het selectievakje in voor elke marktlocatie waar dit abonnement beschikbaar moet zijn voor aankoop (gebruikers in deze markten kunnen de aanbieding nog steeds implementeren in alle Azure-regio's die zijn geselecteerd in **[De installatie van het abonnement).](#plan-setup)** De knop **Belasting opnieuw uitlaten** toont landen waarin Microsoft namens u btw overdraagt. Publiceren naar China is beperkt tot plannen die **gratis** zijn of **Breng je eigen licentie** (BYOL).

Als u al prijzen voor uw abonnement in Amerikaanse dollars (USD) hebt ingesteld en een andere marktlocatie hebt toegevoegd, wordt de prijs voor de nieuwe markt berekend op basis van de huidige wisselkoersen. Controleer altijd de prijs voor elke markt voordat u publiceert. Bekijk de prijzen via de link **Exportprijzen (xlsx)** na het opslaan van uw wijzigingen.

Wanneer u een markt verwijdert, kunnen klanten uit die markt geen actieve implementaties maken of hun bestaande implementaties opschalen. Bestaande implementaties worden niet beïnvloed.

#### <a name="pricing"></a>Prijzen

**Licentiemodel** : selecteer **maandelijks gefactureerd abonnement op basis van gebruik** om de prijzen voor dit abonnement te configureren of Breng uw eigen **licentie** mee om klanten dit abonnement te laten gebruiken met hun bestaande licentie.

Gebruik een van de volgende drie opties voor het invoeren van prijzen voor een maandelijks gefactureerd abonnement op basis van gebruik:

- **Per core** - Geef de prijs per core in Amerikaanse dollars (USD). We berekenen de prijzen per kerngrootte en zetten deze om in lokale valuta's met behulp van de huidige wisselkoers.
- **Per kerngrootte** – Geef prijzen per kerngrootte op in USD. We zetten de prijzen om in lokale valuta's met behulp van de huidige wisselkoers.
- **Per markt en kerngrootte** – Geef prijzen voor elke kerngrootte voor alle markten. U prijzen importeren uit een spreadsheet.

> [!NOTE]
> Prijswijzigingen opslaan om prijsgegevens te exporteren. Nadat een prijs voor een markt in uw plan is gepubliceerd, kan deze later niet meer worden gewijzigd. Zorg ervoor dat deze prijzen vlak zijn voordat ze worden gepubliceerd door de prijsspreadsheet te exporteren en de prijs in elke markt te bekijken.

#### <a name="free-trial"></a>Gratis proefversie

U uw klanten een gratis proefperiode van één of drie maanden aanbieden.

#### <a name="visibility"></a>Zichtbaarheid

Je elk plan ontwerpen om zichtbaar te zijn voor iedereen of voor alleen een vooraf geselecteerde doelgroep. Lidmaatschappen in deze beperkte doelgroep toewijzen met azure-abonnements-id's.

**Openbaar** - Uw plan kan door iedereen worden gezien.

**Privépubliek** : maak je abonnement alleen zichtbaar voor een vooraf geselecteerde doelgroep. Nadat het is gepubliceerd als een privé-abonnement, u het publiek bijwerken of wijzigen in het openbaar. Nadat u een plan openbaar hebt gemaakt, moet het openbaar blijven; Je het niet weer privé maken.

**Beperkt publiek (Azure-abonnements-id's)** : wijs de doelgroep toe die toegang heeft tot dit privé-abonnement met azure-abonnements-id's. Voeg eventueel een beschrijving op van elke Azure-abonnements-id die u hebt toegewezen. Voeg maximaal 10 abonnements-id's handmatig of 20.000 toe als u een CSV-spreadsheet importeert. Azure-abonnements-id's worden weergegeven als GUID's en letters moeten kleine letters zijn.

> [!NOTE]
> Een privé- of beperkte doelgroep verschilt van de preview-doelgroep die u hebt gedefinieerd op het tabblad **Voorbeeld.** Een preview-publiek heeft toegang tot uw aanbieding _voordat_ deze live op de markt worden gepubliceerd. Hoewel de keuze voor het privépubliek alleen van toepassing is op een specifiek plan, kan de preview-doelgroep alle plannen (privé of niet) bekijken voor validatiedoeleinden.

#### <a name="hide-plan"></a>Plan verbergen

Als het de bedoeling is dat uw virtuele machine alleen indirect wordt gebruikt wanneer u naar een andere oplossingssjabloon of beheerde toepassing verwijst, selecteert u dit vakom uw virtuele machine te publiceren, maar verbergt u deze voor klanten die er rechtstreeks naar zoeken en browsen.

> [!NOTE]
> Verborgen plannen bieden geen ondersteuning voor voorbeeldkoppelingen.

Selecteer **Concept opslaan** voordat u verdergaat.

### <a name="technical-configuration"></a>Technische configuratie

Geef de afbeeldingen en andere technische eigenschappen die aan dit plan zijn gekoppeld. Zie Een [technische asset van Azure VM maken](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-technical-assets)voor meer informatie.

> [!NOTE]
> Dit tabblad wordt niet weergegeven als u dit plan hebt geconfigureerd om pakketten van een ander plan op het tabblad **Plan-installatie** opnieuw te gebruiken.

#### <a name="operating-system"></a>Besturingssysteem

- **Besturingssysteemfamilie** – Kies uit **Windows-** of **Linux-besturingssysteem**
- **Release** of **leverancier** - Kies de Windows-release of Linux-leverancier
- **OS vriendelijke naam** - Kies een vriendelijke naam van het besturingssysteem. Deze naam is zichtbaar voor klanten

#### <a name="recommended-vm-sizes"></a>Aanbevolen VM-formaten

Selecteer maximaal zes aanbevolen virtuele machineformaten die u wilt weergeven in Azure Marketplace.

#### <a name="open-ports"></a>Poorten openen

Open openbare of private poorten op een geïmplementeerde virtuele machine.

#### <a name="storage-option-for-deployment"></a>Opslagoptie voor implementatie

**Schijfimplementatieoptie** : selecteer wat voor soort schijfimplementatie uw gebruikers kunnen gebruiken wanneer ze de virtuele machine gebruiken. Microsoft raadt aan de implementatie alleen te beperken tot beheerde schijfimplementatie.

#### <a name="properties"></a>Eigenschappen

**Ondersteuning voor versnelde netwerken** – Selecteer of uw VM [versnelde netwerken](https://go.microsoft.com/fwlink/?linkid=2124513)ondersteunt.

#### <a name="vm-images"></a>VM-installatiekopieën

Geef een schijfversie en de SAS URI voor de afbeeldingen van de virtuele machine. Voeg maximaal 16 gegevensschijven toe voor elke VM-afbeelding. Geef slechts één nieuwe afbeeldingsversie per abonnement in een bepaalde inzending. Nadat een afbeelding is gepubliceerd, u deze niet bewerken, maar wel verwijderen. Als u een versie schrapt, kunnen zowel nieuwe als bestaande gebruikers een nieuwe instantie van de verwijderde versie implementeren.

- **Disc versie** is de versie van de afbeelding die u verstrekt.
- **SAS URI** is de locatie in Azure Storage waar u het besturingssysteem VHD hebt opgeslagen.
- Gegevensschijfafbeeldingen zijn ook VHD SAS URI's die zijn opgeslagen in hun Azure-opslag.
- Voeg slechts één afbeelding per inzending toe in een abonnement.

Ongeacht welk besturingssysteem u gebruikt, voegt u alleen het minimum aantal gegevensschijven toe dat de oplossing nodig heeft. Klanten kunnen geen schijven verwijderen die deel uitmaken van een afbeelding op het moment van implementatie, maar ze kunnen altijd schijven toevoegen tijdens of na implementatie.

Selecteer **Concept opslaan** voordat u verdergaat en ga terug naar het overzicht Van **plan**.

## <a name="resell-through-csps"></a>Doorverkopen via CSP's

Vergroot het bereik van uw aanbod door het beschikbaar te maken voor partners in het [Cloud Solution Providers](https://azure.microsoft.com/offers/ms-azr-0145p/) (CSP)-programma. Alle BYOL-abonnementen (Bring Your Own License) worden automatisch aangemeld; u ervoor kiezen om u aan te melden voor uw niet-BYOL-plannen.

Selecteer **Concept opslaan** voordat u verdergaat.

## <a name="test-drive"></a>Proefrit

Stel een demonstratie (proefrit) in waarmee klanten uw aanbieding kunnen uitproberen voordat ze het kopen. Zie Test Drive uw [aanbieding op de commerciële marktplaats](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive)om een demonstratieomgeving te maken waarmee klanten uw aanbieding voor een bepaalde tijd kunnen uitproberen.

Als u een testrit wilt inschakelen, schakelt u het selectievakje Een teststation inschakelen in op het tabblad [Installatie aanbieden](#test-drive) in. Als u een proefrit uit uw aanbieding wilt verwijderen, schakelt u dit selectievakje uit.

Aanvullende bronnen voor teststations:

- Technische best practices
- Best practices voor marketing
- Overzicht van teststations (PDF; zorg ervoor dat uw pop-upblokkering is uitgeschakeld).

Selecteer **Concept opslaan** voordat u verdergaat.

## <a name="review-and-publish"></a>Bekijken en publiceren

Nadat u alle vereiste gedeelten van de aanbieding hebt voltooid, u deze indienen om deze te bekijken en te publiceren.

Selecteer in de rechterbovenhoek van de portal de optie **Controleren en publiceren**.

Op deze pagina u:

- Zie de voltooiingsstatus voor elk gedeelte van de aanbieding.
  - **Niet gestart** – De sectie is nog niet gestart en moet worden voltooid.
  - **Onvolledig** : de sectie bevat fouten die moeten worden opgelost of waarvoor u meer informatie moet verstrekken. Zie de secties eerder in dit document voor meer informatie over het bijwerken van deze sectie.
  - **Voltooid** – De sectie is voltooid en er zijn geen fouten. Alle onderdelen van de aanbieding moeten volledig zijn voordat u de aanbieding indienen.
- **Notities voor certificering** : geef het certificeringsteam testinstructies om ervoor te zorgen dat uw app correct is getest. Geef aanvullende notities die nuttig zijn om uw app te begrijpen.

Als u de publicatieaanbieding wilt indienen, selecteert u **Controleren en publiceren**.

We sturen je een e-mail om je te laten weten wanneer een preview-versie van de aanbieding beschikbaar is om te bekijken en goed te keuren. Als u uw aanbieding wilt publiceren naar het publiek (of als een privé-aanbieding, naar een privépubliek gaat), gaat u naar Partnercenter, zoekt u de **overzichtspagina** van uw aanbieding en selecteert u **Go-live.** De status van uw aanbieding wordt boven aan de pagina weergegeven wanneer het publiceren aan de gang is.

### <a name="errors-and-review-feedback"></a>Fouten en feedback bekijken

De **handmatige validatiestap** in het publicatieproces vertegenwoordigt een uitgebreide beoordeling van uw aanbieding en de bijbehorende technische activa. Als dit proces fouten met uw aanbieding aan het licht brengt, ontvangt u een certificeringsrapport met details over de problemen. Breng eenvoudig de vereiste correcties uit en publiceer uw aanbieding opnieuw.

## <a name="offer-overview"></a>Aanbiedingsoverzicht

De **overzichtspagina Aanbieding** toont een visuele weergave van de stappen die nodig zijn om deze aanbieding te publiceren (zowel voltooid als in uitvoering) en hoe lang elke stap moet duren om te voltooien.

Deze pagina bevat koppelingen om bewerkingen uit te voeren op basis van de selectie die u maakt. Bijvoorbeeld:

- Als de aanbieding een concept is - [Conceptvoorstel verwijderen](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- Als het aanbod live is - [Stop met de verkoop van het aanbod](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- Als de aanbieding in preview is - [Go-live](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- Als u de afmelding van uitgevers nog niet hebt voltooid, [kunt u publiceren annuleren](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="marketplace-examples"></a>Voorbeelden van Marktplaats

In deze voorbeelden ziet u hoe de aanbieding wordt weergegeven in Azure Marketplace.

### <a name="azure-marketplace-offer-details"></a>Azure Marketplace biedt details

![Azure Marketplace biedt detailschermvoorbeeld](media/avm-create1.png)

### <a name="azure-marketplace-search-results"></a>Zoekresultaten van Azure Marketplace

![Voorbeeld van azure Marketplace-zoekopdrachten](media/avm-create2.png)

### <a name="azure-marketplace-plan-details"></a>Azure Marketplace-plangegevens

![Azure Marketplace-abonnement details schermvoorbeeld](media/avm-create3.png)

### <a name="azure-portal-offer-details"></a>Azure-portal biedt details

![Azure-portal biedt voorbeeld van detailsscherm](media/avm-create4.png)

### <a name="azure-portal-search-results"></a>Zoekresultaten voor Azure-portalen

![Voorbeeld van het scherm zoekresultaten van Azure-portal](media/avm-create5.png)

### <a name="azure-portal-plan-details"></a>Details van azure-portalplannen

![Voorbeeld van het scherm scherm van Azure-portalplan](media/avm-create6.png)

## <a name="next-step"></a>Volgende stap

- [Een bestaand aanbod bijwerken in de commerciële markt](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
