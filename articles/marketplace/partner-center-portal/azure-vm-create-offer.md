---
title: Een Azure virtual machine-aanbieding maken in azure Marketplace
description: Meer informatie over het maken van een virtuele-machine aanbieding op de Azure Marketplace met de vereiste SKU.
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: 25b7a619f6d3e308de966ead5925133f6094d9c8
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83701074"
---
# <a name="create-an-azure-virtual-machine-offer-in-the-azure-marketplace"></a>Een Azure virtual machine-aanbieding maken in azure Marketplace

> [!IMPORTANT]
> We verplaatsen het beheer van uw Azure VM-aanbiedingen van Cloud Partner-portal naar het partner centrum. Totdat uw aanbiedingen zijn gemigreerd, volgt u de instructies in de [aanbieding voor het maken van virtuele machines](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-offer) in Cloud Partner-Portal om uw aanbiedingen te beheren.

In dit artikel wordt beschreven hoe u een Azure virtual machine-aanbieding maakt en publiceert naar [Azure Marketplace](https://azuremarketplace.microsoft.com/). Het behandelt virtuele Windows-en Linux-machines die een besturings systeem, virtuele harde schijf (VHD) en Maxi maal 16 gegevens schijven bevatten. Voordat u begint, moet u [een commercieel Marketplace-account maken in Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) als u dit nog niet hebt gedaan. Zorg ervoor dat uw account is inge schreven in het Commercial Marketplace-programma.

## <a name="introduction"></a>Inleiding

### <a name="publishing-benefits"></a>Publicatie voordelen

Publiceren naar Azure Marketplace heeft de volgende voor delen:

- Uw bedrijf promoten met het merk van micro soft
- Bereik meer dan 100.000.000 Office 365-en Dynamics 365-gebruikers en meer dan 200.000 organisaties via Azure Marketplace
- Haal leads van hoge kwaliteit van deze markt plaatsen op
- Zorg dat uw services worden bevorderd door het veld en de Televerkoop teams van micro soft

### <a name="before-you-begin"></a>Voordat u begint

Als u dit nog niet hebt gedaan, bekijkt u de [publicatie handleiding voor de virtuele machine-aanbieding](https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines) en dit materiaal voor virtuele machines van Azure:

- Quick start-gidsen
  - [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/)
  - [GitHub Azure Quick Start-sjablonen](https://github.com/azure/azure-quickstart-templates)
- Zelfstudies
  - [Virtuele Linux-machines](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
  - [Virtuele Windows-machines](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm)
- Voorbeelden
  - [Azure CLI-voor beelden voor Linux Vm's](https://docs.microsoft.com/azure/virtual-machines/linux/cli-samples)
  - [Azure PowerShell voor Linux-Vm's](https://docs.microsoft.com/azure/virtual-machines/linux/powershell-samples)
  - [Azure CLI-voor beelden voor Windows-Vm's](https://docs.microsoft.com/azure/virtual-machines/windows/cli-samples)
  - [Azure PowerShell voor Windows-Vm's](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-quick)

### <a name="fundamentals-in-technical-knowledge"></a>Grond beginselen van technische kennis

Het ontwerpen, bouwen en testen van deze assets vergt tijd en vereist technische kennis van het Azure-platform en de technologieën die worden gebruikt om de aanbieding te bouwen.

Uw technische team moet inzicht hebben in de volgende micro soft-technologieën:

- Basis informatie over [Azure-Services](https://azure.microsoft.com/services/)
- Azure- [toepassingen ontwerpen en ontwikkelen](https://azure.microsoft.com/solutions/architecture/)
- Praktische kennis van [Azure-virtual machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage#storage)en [Azure-netwerken](https://azure.microsoft.com/services/?filter=networking#networking)

## <a name="create-a-new-offer"></a>Een nieuwe aanbieding maken

1. Meld u aan bij [Partner Center](https://partner.microsoft.com/dashboard/home).
2. Selecteer in het menu links de optie **commerciële Marketplace**-  >  **overzicht**.
3. Selecteer op de pagina overzicht **+ nieuwe**  >  **Azure virtual machine**aanbieden.

    ![Illustreert het navigatie menu.](./media/new-offer-azure-vm.png)

> [!NOTE]
> Nadat een aanbieding is gepubliceerd, worden wijzigingen die in het partner centrum zijn aangebracht, alleen weer gegeven in de winkel wanneer u de aanbieding opnieuw publiceert. Zorg ervoor dat u altijd opnieuw publiceert nadat u wijzigingen hebt aangebracht.

## <a name="new-offer"></a>Nieuwe aanbieding

Voer een **aanbiedings-id**in. Dit is een unieke id voor elke aanbieding in uw account.

- Deze ID is zichtbaar voor klanten in het webadres voor de Marketplace-aanbieding en in Azure PowerShell en Azure CLI, indien van toepassing.
- Gebruik alleen kleine letters en cijfers. Dit kan afbreek streepjes en onderstrepings tekens bevatten, maar mag niet langer zijn dan 50. Als u hier bijvoorbeeld **test-aanbieding-1** invoert, is het webadres van de aanbieding `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- De aanbiedings-ID kan niet worden gewijzigd nadat u **maken**hebt geselecteerd.

Voer een **alias**voor de aanbieding in. Dit is de naam die wordt gebruikt voor de aanbieding in Partner Center.

- Deze naam wordt niet gebruikt in Marketplace en wijkt af van de naam van de aanbieding en andere waarden die aan klanten worden weer gegeven.

Selecteer **maken** om de aanbieding te genereren en door te gaan.

## <a name="offer-setup"></a>Installatie van aanbieding

### <a name="test-drive"></a>Station testen

Een test drive is een fantastische manier om uw aanbieding aan potentiële klanten te laten presen teren door hen de mogelijkheid te bieden om te kopen voordat u aan de slag gaat, wat resulteert in een verhoogde conversie en de generatie van uiterst gekwalificeerde leads. Meer [informatie over test stations](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive).

Als u een test drive voor een bepaalde periode wilt inschakelen, schakelt u het selectie vakje **een test drive inschakelen** in. Als u test drive uit uw aanbieding wilt verwijderen, schakelt u dit selectie vakje uit.

Aanvullende test drive resources:

- [Technische best practices](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Best practices voor marketing](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Overzicht van test stations](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) PDF (zorg ervoor dat de pop-upblokkering is uitgeschakeld)

### <a name="lead-management"></a>Leadbeheer

Wanneer u uw aanbieding naar de commerciële Marketplace met het partner centrum publiceert, verbindt u deze met uw CRM-systeem (Customer Relationship Management). Zo kunt u contact gegevens van klanten ontvangen zodra iemand interesse in of gebruikt voor uw product. Verbinding maken met een CRM is vereist als u **test station** inschakelt (zie vorige sectie), anders is het optioneel.

1. Selecteer een leadbestemming waarnaar wij de klantenleads moeten sturen. Het partner Centrum ondersteunt de volgende CRM-systemen:
    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) voor klant betrokkenheid
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Als uw CRM-systeem niet hierboven wordt vermeld, gebruikt u [Azure Table](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) of [https-eind punt](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) om klant lead gegevens op te slaan. Exporteer de gegevens vervolgens naar uw CRM-systeem.

2. Verbind uw aanbieding met de doel locatie van de lead wanneer u publiceert in het partner centrum.
3. Controleer of de verbinding met de doel locatie juist is geconfigureerd. Nadat u het in het partner centrum hebt gepubliceerd, valideren we de verbinding en sturen we u een test lead. Wanneer u een voor beeld van de aanbieding bekijkt voordat deze live gaat, kunt u ook uw lead verbinding testen door zelf te proberen de aanbieding in de voorbeeld omgeving te implementeren.
4. Zorg ervoor dat de verbinding met de doel locatie van de lead bijgewerkt blijft, zodat u geen leads kwijtraakt.

Selecteer **concept opslaan** voordat u doorgaat.

## <a name="properties"></a>Eigenschappen

Op deze pagina kunt u de categorieën en industrieën definiëren die worden gebruikt voor het groeperen van uw aanbieding op Marketplace, uw app-versie en de juridische contracten die uw aanbieding ondersteunen.

### <a name="categories"></a>Categorieën

Selecteer mini maal één en Maxi maal vijf categorieën. Deze categorieën worden gebruikt om uw aanbieding te plaatsen in de juiste Zoek gebieden voor Marketplace. In de beschrijving van de aanbieding wordt uitgelegd hoe uw aanbod deze categorieën ondersteunt. De aanbiedingen van de virtuele machine worden weer gegeven onder de categorie **berekenen** in azure Marketplace.

### <a name="legal"></a>Juridisch

U moet voor waarden voor de aanbieding opgeven. U hebt hiervoor twee opties:

- Gebruik uw eigen voor waarden
- Het standaard contract voor de micro soft Commercial Marketplace gebruiken

#### <a name="use-your-own-terms-and-conditions"></a>Gebruik uw eigen voor waarden

Als u uw eigen aangepaste voor waarden wilt opgeven, voert u Maxi maal 10.000 tekens tekst in het vak **voor waarden** in. Als u een langere beschrijving nodig hebt, voert u één webadres in dat verwijst naar waar uw voor waarden kunnen worden gevonden. Het wordt weer gegeven voor klanten als een actieve koppeling.

Klanten moeten deze voor waarden accepteren voordat ze uw aanbieding kunnen proberen.

#### <a name="use-the-standard-contract-for-the-microsoft-commercial-marketplace"></a>Het standaard contract voor de micro soft Commercial Marketplace gebruiken

Micro soft biedt een standaard contract voor de commerciële Marketplace om het aankoop proces voor klanten te vereenvoudigen en de juridische complexiteit voor software leveranciers te reduceren. Wanneer u uw software onder het standaard contract aanbiedt, hoeven klanten deze slechts eenmaal te lezen en te accepteren. u hoeft dus geen aangepaste voor waarden te maken.

Gebruik het standaard contract door het selectie vakje **het standaard contract voor commerciële Marketplace van micro soft gebruiken** in te scha kelen en vervolgens in het pop-upvenster te **accepteren** (mogelijk moet u omhoog schuiven om het te zien).

![Illustreert de overzichts pagina in partner centrum met de nieuwe aanbiedings knop en advies service aanbieding geselecteerd.](media/use-standard-contract.png)

> [!NOTE]
> Nadat u een aanbieding hebt gepubliceerd met het standaard contract voor commerciële Marketplace, kunt u uw eigen aangepaste voor waarden niet gebruiken. U kunt uw oplossing aanbieden onder het Standard-contract **of** onder uw eigen voor waarden.

Zie voor meer informatie over het standaard contract Standard-contract voor de micro soft [Commercial Marketplace](https://docs.microsoft.com/azure/marketplace/standard-contract). U kunt het [standaard contract](https://go.microsoft.com/fwlink/?linkid=2041178) downloaden als een PDF-bestand (zorg ervoor dat de pop-upblokkering is uitgeschakeld).

##### <a name="standard-contract-amendments"></a>Wijzigingen in het standaard contract

Met de aanpassingen van het standaard contract kunt u de standaard contract voorwaarden voor eenvoud selecteren en de voor waarden voor uw product of bedrijf maken. Klanten hoeven alleen de wijzigingen in het contract door te nemen als ze het micro soft-standaard contract al hebben gecontroleerd en geaccepteerd. Er zijn twee soorten wijzigingen beschikbaar: universeel en aangepast.

**Universele wijzigingen** : deze worden universeel toegepast op het standaard contract voor alle klanten. Ze worden weer gegeven aan elke klant van de aanbieding in de inkoop stroom. Klanten moeten akkoord gaan met de voor waarden van het standaard contract en de wijziging (en) voordat ze uw aanbieding kunnen gebruiken. U kunt één universele wijziging per aanbieding opgeven. U kunt een onbeperkt aantal tekens in dit vak invoeren. Deze voor waarden worden weer gegeven aan klanten in AppSource, Azure Marketplace en/of Azure Portal tijdens de detectie-en aankoop stroom.

**Aangepaste wijzigingen** – dit zijn speciale wijzigingen in het standaard contract die gericht zijn op specifieke klanten via Azure-Tenant-id's. U kunt kiezen welke Tenant u wilt instellen. Alleen klanten van de Tenant worden weer gegeven met de aangepaste wijzigings voorwaarden in de inkoop stroom van de aanbieding. Klanten moeten akkoord gaan met de voor waarden van het standaard contract en de wijziging (en) voordat ze uw aanbieding kunnen gebruiken.

Begin met het selecteren van **aangepaste wijzigings voorwaarden toevoegen (Maxi maal 10)**. U kunt Maxi maal tien aangepaste voor waarden per aanbieding opgeven.

- **Voor waarden voor aangepaste aanpassingen** : Voer uw eigen wijzigings voorwaarden in in het vak Aangepaste wijzigings voorwaarden. U kunt een onbeperkt aantal tekens opgeven. Alleen klanten van de Tenant-Id's die u opgeeft voor deze aangepaste voor waarden worden deze weer gegeven in de inkoop stroom van de aanbieding in de Azure Portal.
- **Tenant-id's** (vereist): elke aangepaste wijziging kan worden gericht op Maxi maal 20 Tenant-id's. Als u een aangepaste wijziging toevoegt, moet u ten minste één Tenant-ID opgeven, waarmee uw klant in azure wordt geïdentificeerd. uw klant kan u vinden in azure onder en vervolgens op Eigenschappen. De waarde van de Directory-ID is de Tenant-ID (bijvoorbeeld 50c464d3-4930-494c-963c-1e951d15360e). U kunt ook de Tenant-ID van de organisatie van uw klant vinden met behulp van hun domein naam webadres op [Wat is mijn Microsoft Azure en Office 365-Tenant-id?](https://www.whatismytenantid.com/).
- **Beschrijving** (optioneel): Geef een beschrijving op voor de Tenant-id die u helpt bij het identificeren van de klant met wie u de wijziging wilt aanrichten.

> [!NOTE]
> Deze twee typen wijzigingen worden boven op elkaar gestapeld. Klanten die zijn gericht op aangepaste wijzigingen, krijgen ook de universele wijziging in het standaard contract tijdens de aankoop.

Selecteer **concept opslaan** voordat u doorgaat.

## <a name="offer-listing"></a>Aanbieding weer geven

Op deze pagina kunt u details van het aanbod opgeven, zoals de naam, beschrijving, koppelingen en contact personen van de aanbieding.

> [!NOTE]
> De inhoud van het aanbiedings aanbod (zoals de beschrijving, documenten, scherm afbeeldingen en gebruiks voorwaarden) is niet vereist voor het Engels, zolang de beschrijving van het aanbod begint met de woord groep ' deze toepassing is alleen beschikbaar in [niet-Engelse taal] '. U kunt ook een _handig webadres_ voor de koppeling opgeven om inhoud te bieden in een andere taal dan die in de inhoud van de aanbieding.

### <a name="marketplace-details"></a>Marketplace-gegevens

#### <a name="name"></a>Name

De naam die u hier invoert, wordt aan klanten weer gegeven als de titel van de aanbieding. Dit veld is vooraf ingevuld met de tekst die u hebt ingevoerd in het vak **aanbiedings alias** wanneer u de aanbieding hebt gemaakt. U kunt deze naam later wijzigen.

De naam:

- Kan worden gemerkt (en u kunt symbolen van het handels merk en copyright toevoegen)
- Mag niet meer dan 50 tekens bevatten
- Kan geen emojis bevatten.

#### <a name="search-results-summary"></a>Samen vatting van zoek resultaten

Een korte beschrijving van uw aanbieding. Dit kan Maxi maal 100 tekens lang zijn en wordt gebruikt in Zoek resultaten voor Marketplace.

#### <a name="long-summary"></a>Lange samen vatting

Geef een langere beschrijving van uw aanbieding. Dit kan Maxi maal 256 tekens lang zijn en wordt gebruikt in Zoek resultaten voor Marketplace.

#### <a name="description"></a>Beschrijving

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

#### <a name="privacy-policy-link"></a>Koppeling Privacybeleid

Voer het webadres (URL) in voor het privacybeleid van uw organisatie. Zorg ervoor dat uw aanbieding voldoet aan de wetten en voor schriften van de privacy. U moet ook een geldig privacybeleid op uw website plaatsen.

### <a name="useful-links"></a>Handige koppelingen

Bied aanvullende online documenten over uw aanbieding. Selecteer **+ een koppeling toevoegen** en vul vervolgens de volgende velden in om een koppeling toe te voegen:

- **Naam** : klanten zien de naam op de pagina Details.
- **Koppeling (URL)** : Voer een koppeling in voor klanten om uw online document weer te geven.

### <a name="customer-support-links"></a>Koppelingen voor klant ondersteuning

Geef de ondersteunings website op waar klanten uw ondersteunings team kunnen bereiken.

- Website over wereld wijde ondersteuning voor Azure
- Ondersteunings website voor Azure Government

### <a name="partner-support-contact"></a>Contact opnemen met partner ondersteuning

Geef contact gegevens op voor micro soft-partners die kunnen worden gebruikt wanneer uw klanten een ondersteunings ticket openen. Dit wordt niet weer gegeven in de Marketplace.

- Name
- Email
- Telefoon

### <a name="engineering-contact"></a>Technische contact persoon

Geef contact gegevens op die micro soft kan gebruiken wanneer er problemen zijn met uw aanbieding, met inbegrip van problemen met certificering. Dit wordt niet weer gegeven in de Marketplace.

- Name
- Email
- Telefoon

### <a name="marketplace-media"></a>Media voor Marketplace

Bied logo's en installatie kopieën die u kunt gebruiken met uw aanbieding. Alle installatie kopieën moeten de PNG-indeling hebben. Vage afbeeldingen zorgen ervoor dat uw inzending wordt afgewezen.

>[!Note]
>Als u een probleem hebt met het uploaden van bestanden, moet u ervoor zorgen dat uw lokale netwerk de https://upload.xboxlive.com service die wordt gebruikt door het partner centrum niet blokkeert.

#### <a name="marketplace-logos"></a>Marketplace-logo's

Geef in de volgende vier pixel formaten PNG-bestanden van het logo van uw aanbieding op:

- **Klein** (48 x 48)
- **Gemiddeld** (90 x 90)
- **Groot** (216 x 216)
- **Breed** (255 x 115)

Alle vier de logo's zijn vereist en worden gebruikt op verschillende plaatsen in de Marketplace-vermelding.

#### <a name="screenshots"></a>Schermopnamen

Voeg Maxi maal vijf scherm opnamen toe die laten zien hoe uw aanbieding werkt. Elke scherm afbeelding moet 1280 x 720 pixels groot en in PNG-indeling zijn. Elke scherm opname moet een bijschrift bevatten.

#### <a name="videos"></a>Video's

Voeg Maxi maal vijf Video's toe die uw aanbieding aantonen. Deze moeten worden gehost op een externe video service. Voer de naam, het webadres en de PNG-afbeelding van de video in op 1280 x 720 pixels.

Zie [Best Practices for Marketplace](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)List voor aanvullende bronnen voor Marketplace-aanbiedingen.

Selecteer **concept opslaan** voordat u doorgaat.

## <a name="preview"></a>Preview

Kies op het tabblad voor beeld een beperkt voor beeld van een **doel groep** voor het valideren van uw aanbieding voordat u deze live publiceert naar de bredere Marketplace-doel groep.

> [!IMPORTANT]
> Nadat u uw aanbieding in de preview-versie hebt gecontroleerd, selecteert u **Live** om uw aanbieding naar de open bare doel groep voor commerciële Marketplace te publiceren.

Uw preview-doel groep wordt geïdentificeerd door de Azure-abonnements-ID-GUID'S, samen met een optionele beschrijving voor elk. Geen van deze velden kan worden gezien door klanten. U kunt uw Azure-abonnements-ID vinden op de pagina **abonnementen** in azure Portal.

Voeg ten minste één Azure-abonnements-ID afzonderlijk toe (Maxi maal 10) of door een CSV-bestand te uploaden (Maxi maal 100). Door deze abonnement-Id's toe te voegen, definieert u wie een voor beeld van uw aanbieding kan bekijken voordat deze Live wordt gepubliceerd. Als uw aanbieding al Live is, kunt u nog steeds een preview-doel opgeven voor het testen van wijzigingen in de aanbieding of updates voor uw aanbieding.

> [!NOTE]
> Een preview-doel groep wijkt af van een persoonlijke doel groep. Een preview-doel groep heeft toegang tot uw aanbieding _voordat_ deze Live wordt gepubliceerd in de Marketplace. Ze kunnen alle plannen bekijken en valideren, met inbegrip van de abonnementen die alleen beschikbaar zijn voor een privé publiek nadat uw aanbieding volledig naar de Marketplace is gepubliceerd. Een persoonlijke doel groep (gedefinieerd in het tabblad **prijs en beschik baarheid** van abonnementen) heeft exclusieve toegang tot een bepaald abonnement.

Selecteer **concept opslaan** voordat u doorgaat naar de volgende sectie, plan overzicht.

## <a name="plan-overview"></a>Overzicht van plan

U kunt in het partner centrum verschillende plan opties bieden in dezelfde aanbieding. Deze plannen werden voorheen Sku's genoemd. Voor een aanbieding is ten minste één abonnement vereist, wat kan verschillen met de voor waarden van verdiensten maximaliseren, Azure-regio's,-functies of VM-installatie kopieën.

Nadat u uw plannen hebt gemaakt, wordt op het tabblad Overzicht van het **plan** het volgende weer gegeven:

- Namen van plannen
- Licentie modellen
- Doel groep (openbaar of privé)
- Huidige publicatie status
- Beschikbare acties

Acties die beschikbaar zijn in het plan overzicht variëren, afhankelijk van de huidige status van uw abonnement. Deze omvatten:

- **Concept verwijderen** : als de plan status een concept is
- **Verkoop plan stoppen** of **privé-publiek synchroniseren** : als de plan status Live is gepubliceerd

### <a name="create-new-plan"></a>Nieuw plan maken

Selecteer **+ Nieuw abonnement bovenaan maken** . Het dialoog venster **nieuw plan** wordt weer gegeven.

Maak in het vak **abonnement-id** een unieke plan-id voor elk abonnement in deze aanbieding. Deze ID is zichtbaar voor klanten in het webadres van het product. Gebruik alleen kleine letters en cijfers, streepjes of onderstrepings tekens en Maxi maal 50.

> [!NOTE]
> De plan-ID kan niet worden gewijzigd nadat u **maken**hebt geselecteerd.

Voer in het vak **naam van abonnement** een naam in voor dit abonnement. Klanten zien deze naam wanneer ze bepalen welk abonnement binnen uw aanbieding moet worden geselecteerd. Maak een unieke naam waarmee duidelijk de verschillen van elk plan worden aangegeven. U kunt bijvoorbeeld **Windows Server** gebruiken met abonnementen **betalen per gebruik**, **BYOL**, **Geavanceerd**en **Enter prise**.

Selecteer **Maken**.

### <a name="plan-setup"></a>Installatie plannen

Stel de configuratie op hoog niveau in voor het type abonnement, of het opnieuw gebruikmaakt van technische configuratie van een ander abonnement en in welke Azure-regio's het plan beschikbaar moet zijn. Uw selecties bepalen welke velden worden weer gegeven op andere tabbladen voor hetzelfde abonnement.

#### <a name="re-use-technical-configuration"></a>Technische configuratie opnieuw gebruiken

Als u meer dan één abonnement van hetzelfde type hebt en de pakketten identiek zijn, kunt u de **technische configuratie van een ander abonnement selecteren met dit abonnement**. Met deze optie kunt u een van de andere plannen van hetzelfde type voor deze aanbieding selecteren en de technische configuratie ervan opnieuw gebruiken.

> [!NOTE]
> Wanneer u de technische configuratie van een ander abonnement opnieuw gebruikt, verdwijnt het hele tabblad **technische configuratie** uit dit abonnement. De technische configuratie details van het andere abonnement, inclusief eventuele updates die u in de toekomst maakt, worden ook voor dit abonnement gebruikt. Deze instelling kan niet worden gewijzigd nadat dit abonnement is gepubliceerd.

#### <a name="azure-regions"></a>Azure-regio's

Uw abonnement moet beschikbaar worden gesteld in ten minste één Azure-regio.

Selecteer de optie **Azure Global** om uw plan beschikbaar te maken voor klanten in alle wereld wijde Azure-regio's die een commerciële Marketplace-integratie hebben. Zie [geografische Beschik baarheid en valuta ondersteuning](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies)voor meer informatie.

Selecteer de optie **Azure Government** om uw plan beschikbaar te maken in de regio [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome) . Deze regio voorziet in gecontroleerde toegang voor klanten van Amerikaanse federale, staats-, lokale of tribalee entiteiten, evenals partners die in aanmerking komen voor hen. Als uitgever bent u verantwoordelijk voor nalevings controles, beveiligings maatregelen en aanbevolen procedures. Azure Government maakt gebruik van fysiek geïsoleerde data centers en netwerken (alleen in de Verenigde Staten).

Voordat u naar [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)publiceert, moet u het abonnement testen en valideren in de omgeving, omdat bepaalde eind punten kunnen verschillen. Als u uw abonnement wilt instellen en testen, vraagt u een proef account aan bij [Microsoft Azure Government proef versie](https://azure.microsoft.com/global-infrastructure/government/request/).

> [!NOTE]
> Nadat uw abonnement is gepubliceerd en beschikbaar is in een specifieke Azure-regio, kunt u die regio niet verwijderen.

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

### <a name="pricing-and-availability"></a>Prijzen en beschikbaarheid

Op dit tabblad configureert u het volgende:

- De markt van dit abonnement is beschikbaar in
- De prijs per uur
- Hiermee wordt aangegeven of het plan zichtbaar moet worden voor iedereen of alleen voor specifieke klanten (een persoonlijke doel groep)

#### <a name="markets"></a>Landen

Elk plan moet beschikbaar zijn op ten minste één markt. Schakel het selectie vakje in voor elke markt locatie waar dit abonnement beschikbaar moet zijn voor aankoop (gebruikers op deze markten kunnen nog steeds de aanbieding implementeren voor alle Azure-regio's die zijn geselecteerd in de **[installatie van plannen](#plan-setup)**). De knop te **betalen BTW** bevat landen waarin micro soft verkoop-en gebruiks belasting verkrijgt voor u. Publiceren naar China is beperkt tot abonnementen die **gratis** of **Bring your own License** (BYOL) zijn.

Als u de prijzen voor uw abonnement al hebt ingesteld in Verenigde Staten dollars (USD) en een andere markt locatie toevoegt, wordt de prijs voor de nieuwe markt berekend op basis van de huidige wissel koersen. De prijs voor elke markt altijd controleren voordat deze wordt gepubliceerd. Bekijk de prijzen met de koppeling **prijzen exporteren (XLSX)** nadat u uw wijzigingen hebt opgeslagen.

Wanneer u een markt verwijdert, kunnen klanten van die markt die gebruikmaken van actieve implementaties geen nieuwe implementaties maken of de bestaande implementaties opschalen. Bestaande implementaties worden niet beïnvloed.

#### <a name="pricing"></a>Prijzen

**Licentie model** : Selecteer **maandelijks gefactureerd abonnement** dat u wilt gebruiken voor het configureren van de prijzen voor dit abonnement of **Bring your own License** zodat klanten dit plan met hun bestaande licentie kunnen laten werken.

Voor een op gebruik gebaseerd maandelijks gefactureerd abonnement gebruikt u een van de volgende drie opties voor prijs invoer:

- **Per kern** : Geef de prijs per kern op in Verenigde Staten dollars (USD). De prijzen worden berekend op basis van de kern grootte en worden omgezet in lokale valuta's met de huidige wissel koers.
- **Per kern grootte** – Geef prijzen per kern grootte op in USD. De prijzen worden in lokale valuta's omgezet op basis van de huidige wissel koers.
- **Per markt en kern grootte** – Geef prijzen voor elke kern grootte op voor alle markten. U kunt prijzen importeren uit een spread sheet.

> [!NOTE]
> Sla de prijs wijzigingen op om de export van prijs gegevens in te scha kelen. Nadat een prijs voor een markt in uw abonnement is gepubliceerd, kunt u deze later niet meer wijzigen. Zorg ervoor dat deze prijzen goed zijn voordat u publiceert door de prijzen spread sheet te exporteren en de prijs op elke markt te bekijken.

#### <a name="free-trial"></a>Gratis proefversie

U kunt uw klanten een gratis proef versie van één maand of drie maanden aanbieden.

#### <a name="visibility"></a>Zichtbaarheid

U kunt elk plan ontwerpen dat zichtbaar is voor iedereen of alleen voor een vooraf geselecteerde doel groep. Lidmaatschappen in deze beperkte doel groep toewijzen met behulp van Azure-abonnement-Id's.

**Openbaar** : uw abonnement kan worden gezien door iedereen.

**Privé publiek** : Maak uw plan alleen zichtbaar voor een voorgeselecteerde doel groep. Nadat het is gepubliceerd als een persoonlijk abonnement, kunt u de doel groep bijwerken of wijzigen in openbaar. Nadat u een plan openbaar hebt gemaakt, moet het openbaar blijven; u kunt de back-up niet weer wijzigen in persoonlijk.

**Beperkte doel groep (Azure-abonnement-id's)** : wijs de doel groep toe die toegang heeft tot dit privé plan met behulp van Azure-abonnement-id's. U kunt eventueel een beschrijving toevoegen van elke ID van het Azure-abonnement dat u hebt toegewezen. Voeg Maxi maal 10 abonnements-Id's hand matig of 20.000 toe als u een CSV-werk blad importeert. Azure-abonnements-Id's worden weer gegeven als GUID'S en letters moeten kleine letters zijn.

> [!NOTE]
> Een persoonlijke of beperkte doel groep wijkt af van de preview-doel groep die u hebt gedefinieerd op het tabblad **Preview** . Een preview-doel groep heeft toegang tot uw aanbieding _voordat_ deze live op Marketplace wordt gepubliceerd. Hoewel de keuze van de privé doelgroep alleen van toepassing is op een specifiek abonnement, kan de preview-doel groep alle plannen (privé of niet) voor validatie doeleinden bekijken.

#### <a name="hide-plan"></a>Plan verbergen

Als uw virtuele machine alleen indirect moet worden gebruikt wanneer ernaar wordt verwezen via een andere oplossings sjabloon of beheerde toepassing, schakelt u dit selectie vakje in om uw virtuele machine te publiceren, maar deze te verbergen bij klanten die ze rechtstreeks zoeken en bezoeken.

> [!NOTE]
> Verborgen abonnementen bieden geen ondersteuning voor preview-koppelingen.

Selecteer **concept opslaan** voordat u doorgaat.

### <a name="technical-configuration"></a>Technische configuratie

Geef de installatie kopieën en andere technische eigenschappen op die aan dit plan zijn gekoppeld. Zie [een technische Asset voor Azure VM maken](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-technical-assets)voor meer informatie.

> [!NOTE]
> Dit tabblad wordt niet weer gegeven als u dit plan hebt geconfigureerd voor het opnieuw gebruiken van pakketten van een ander abonnement op het tabblad **installatie plannen** .

#### <a name="operating-system"></a>Besturingssysteem

- **Besturingssysteem familie** : Selecteer een **Windows** -of **Linux** -besturings systeem
- **Release** of **leverancier** : Kies de Windows-release of Linux-leverancier
- **Beschrijvende naam** van het besturings systeem: Kies een beschrijvende naam voor het besturingssysteem. Deze naam is zichtbaar voor klanten

#### <a name="recommended-vm-sizes"></a>Aanbevolen VM-grootten

Selecteer Maxi maal zes aanbevolen grootten voor virtuele machines die u wilt weer geven in azure Marketplace.

#### <a name="open-ports"></a>Poorten openen

Open open bare of particuliere poorten op een geïmplementeerde virtuele machine.

#### <a name="storage-option-for-deployment"></a>Opslag optie voor implementatie

**Schijf implementatie optie** : Selecteer welk type schijf implementatie uw gebruikers kunnen gebruiken bij het gebruik van de virtuele machine. Micro soft raadt aan de implementatie alleen te beperken tot de implementatie van Managed disks.

#### <a name="properties"></a>Eigenschappen

**Ondersteuning voor versneld netwerken** : Selecteer deze optie als uw virtuele machine [versneld netwerken](https://go.microsoft.com/fwlink/?linkid=2124513)ondersteunt.

#### <a name="vm-images"></a>VM-installatiekopieën

Geef een schijf versie en de SAS-URI op voor de installatie kopieën van de virtuele machine. Voeg Maxi maal 16 gegevens schijven toe voor elke VM-installatie kopie. Geef slechts één nieuwe versie van de installatie kopie op per abonnement in een bepaalde verzen ding. Nadat een installatie kopie is gepubliceerd, kunt u deze niet meer bewerken, maar wel verwijderen. Als u een versie verwijdert, voor komt u dat zowel nieuwe als bestaande gebruikers een nieuw exemplaar van de verwijderde versie kunnen implementeren.

- De versie van de **schijf** is de versie van de installatie kopie die u opgeeft.
- **SAS URI** is de locatie in azure Storage waar u de virtuele harde schijf van het besturings systeem hebt opgeslagen.
- Installatie kopieën van de gegevens schijf zijn ook SAS-Uri's in de VHD opgeslagen in hun Azure-opslag.
- Voeg slechts één afbeelding per inzending toe aan een plan.

Ongeacht welk besturings systeem u gebruikt, voegt u alleen het minimum aantal gegevens schijven toe dat nodig is voor de oplossing. Klanten kunnen geen schijven verwijderen die deel uitmaken van een installatie kopie op het moment van de implementatie, maar ze kunnen altijd schijven toevoegen tijdens of na de implementatie.

Selecteer **concept opslaan** om door te gaan en terug te keren naar overzicht van het **plan**.

## <a name="resell-through-csps"></a>Door de Csp's door verkopen

Breid het bereik van uw aanbieding uit door deze beschikbaar te maken voor partners in het programma voor [Cloud Solution Providers](https://azure.microsoft.com/offers/ms-azr-0145p/) (CSP). Alle BYOL-abonnementen (uw eigen licentie) worden automatisch aangemeld. u kunt kiezen voor uw deelname aan niet-BYOL plannen.

Selecteer **concept opslaan** voordat u doorgaat.

## <a name="test-drive"></a>Station testen

Stel een demonstratie (test drive) in waarmee klanten uw aanbieding kunnen uitproberen voordat ze deze aanschaffen. Voor het maken van een demonstratie omgeving waarmee klanten uw aanbieding voor een bepaalde periode kunnen uitproberen, raadpleegt u [uw aanbieding in de commerciële Marketplace testen](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive).

Als u een test drive wilt inschakelen, schakelt u het selectie vakje een test drive inschakelen in op het tabblad installatie van de [aanbieding](#test-drive) . Als u test drive uit uw aanbieding wilt verwijderen, schakelt u dit selectie vakje uit.

Aanvullende test drive resources:

- [Best practices voor marketing](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Technische best practices](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Overzicht](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (PDF; Controleer of de pop-upblokkering is uitgeschakeld)

Selecteer **concept opslaan** voordat u doorgaat.

## <a name="review-and-publish"></a>Controleren en publiceren

Nadat u alle vereiste delen van de aanbieding hebt voltooid, kunt u deze verzenden om te controleren en te publiceren.

Selecteer in de rechter bovenhoek van de portal **controleren en publiceren**.

Op deze pagina kunt u het volgende doen:

- Bekijk de voltooiings status voor elke sectie van de aanbieding.
  - **Niet gestart** : de sectie is niet gestart en moet worden voltooid.
  - **Onvolledig** : de sectie bevat fouten die moeten worden opgelost of waarvoor u meer informatie moet opgeven. Zie de secties eerder in dit document voor meer informatie over het bijwerken van deze sectie.
  - **Voltooid** : de sectie is voltooid en er zijn geen fouten. Alle onderdelen van de aanbieding moeten volledig zijn voordat u de aanbieding kunt indienen.
- **Opmerkingen voor certificering** : Geef test instructies op het certificerings team om te controleren of uw app correct is getest. Geef aanvullende notities op die nuttig zijn voor het leren van uw app.

Selecteer **controleren en publiceren**om de aanbieding voor publicatie in te dienen.

We sturen u een e-mail om u te laten weten wanneer een preview-versie van de aanbieding beschikbaar is om te controleren en goed te keuren. Als u uw aanbieding naar het publiek (of als een privé-aanbieding, naar een privé-publiek) wilt publiceren, gaat u naar partner centrum, zoekt u de pagina **overzicht** van uw aanbieding en selecteert u **Go-Live**. De status van uw aanbieding wordt boven aan de pagina weer gegeven wanneer het publiceren wordt uitgevoerd.

### <a name="errors-and-review-feedback"></a>Fouten en feedback bekijken

De **hand matige validatie** stap in het publicatie proces vertegenwoordigt een uitgebreide beoordeling van uw aanbieding en de bijbehorende technische activa. Als dit proces fouten met uw aanbieding detecteert, ontvangt u een certificerings rapport waarin de problemen worden beschreven. U hoeft alleen de vereiste correcties aan te brengen en uw aanbieding opnieuw te publiceren.

## <a name="offer-overview"></a>Overzicht van aanbieding

De **overzichts** pagina van de aanbieding bevat een visuele weer gave van de stappen die nodig zijn voor het publiceren van deze aanbieding (zowel voltooid als in uitvoering) en hoe lang elke stap moet worden voltooid.

Deze pagina bevat koppelingen voor het uitvoeren van bewerkingen op deze aanbieding op basis van de selectie die u maakt. Bijvoorbeeld:

- Als de aanbieding een concept is- [concept verwijderen](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- Als de aanbieding Live is: [de aanbieding wordt niet meer verkocht](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- Als de aanbieding in Preview- [Live](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval) staat
- Als u Publisher-afmelding niet hebt voltooid, kunt u het [Publiceren annuleren](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="marketplace-examples"></a>Marketplace-voor beelden

In deze voor beelden ziet u hoe de aanbieding wordt weer gegeven in azure Marketplace.

### <a name="azure-marketplace-offer-details"></a>Details van aanbieding voor Azure Marketplace

![Voor beeld van het scherm Details van Azure Marketplace-aanbieding](media/avm-create1.png)

### <a name="azure-marketplace-search-results"></a>Zoek resultaten voor Azure Marketplace

![Voor beeld van het scherm Azure Marketplace-Zoek Details](media/avm-create2.png)

### <a name="azure-marketplace-plan-details"></a>Details van Azure Marketplace-abonnement

![Voor beeld van het scherm Details van Azure Marketplace-abonnement](media/avm-create3.png)

### <a name="azure-portal-offer-details"></a>Details van Azure Portal-aanbieding

![Voor beeld van het scherm Details van Azure Portal aanbieding](media/avm-create4.png)

### <a name="azure-portal-search-results"></a>Zoek resultaten Azure Portal

![Voorbeeld scherm Azure Portal Zoek resultaten](media/avm-create5.png)

### <a name="azure-portal-plan-details"></a>Details van Azure Portal plan

![Voor beeld van het scherm Details van Azure Portal plan](media/avm-create6.png)

## <a name="next-step"></a>Volgende stap

- [Een bestaande aanbieding bijwerken in de commerciële Marketplace](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
