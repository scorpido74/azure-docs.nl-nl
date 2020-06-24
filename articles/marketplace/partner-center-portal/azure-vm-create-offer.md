---
title: Een Azure virtual machine-aanbieding maken op Azure Marketplace
description: Meer informatie over het maken van een aanbieding voor een virtuele machine op Azure Marketplace met de vereiste SKU.
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: 7d32e4100d00a4ecff9f8bd5017d8bc5ab74105e
ms.sourcegitcommit: 4ac596f284a239a9b3d8ed42f89ed546290f4128
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/12/2020
ms.locfileid: "84752421"
---
# <a name="create-an-azure-virtual-machine-offer-on-azure-marketplace"></a>Een Azure virtual machine-aanbieding maken op Azure Marketplace

In dit artikel wordt beschreven hoe u een Azure virtual machine-aanbieding maakt en publiceert naar [Azure Marketplace](https://azuremarketplace.microsoft.com/). Het biedt zowel Windows-als op Linux gebaseerde virtuele machines die een besturings systeem, een virtuele harde schijf (VHD) en Maxi maal 16 gegevens schijven bevatten. 

Voordat u begint, moet u [een commercieel Marketplace-account maken in het partner centrum](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account). Zorg ervoor dat uw account is inge schreven in het Commercial Marketplace-programma.

## <a name="introduction"></a>Introductie

### <a name="the-benefits-of-publishing-to-azure-marketplace"></a>De voor delen van publiceren naar Azure Marketplace

Wanneer u uw aanbiedingen op Azure Marketplace publiceert, kunt u het volgende doen:

- Promoot uw bedrijf met behulp van het merk van micro soft.
- Bereik meer dan 100.000.000 Office 365-en Dynamics 365-gebruikers en meer dan 200.000 organisaties.
- Krijg leads van hoge kwaliteit van deze markt plaatsen.
- Zorg dat uw services worden bevorderd door het veld Sales en televerkoop teams van micro soft.

### <a name="before-you-begin"></a>Voordat u begint

Als u dit nog niet hebt gedaan, bekijkt u de [publicatie handleiding voor de virtuele machine-aanbieding](https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines) en dit materiaal voor virtuele machines van Azure:

- Quick start-gidsen
  - [Sjablonen voor Azure Quick Start](https://azure.microsoft.com/resources/templates/)
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

Het proces voor het ontwerpen, bouwen en testen van aanbiedingen vergt tijd en vereist expertise in zowel het Azure-platform als de technologieën die worden gebruikt om uw aanbieding te bouwen.

Uw technische team moet een basis kennis hebben van de volgende micro soft-technologieën:

- [Azure-services](https://azure.microsoft.com/services/)
- [Ontwerp en architectuur van Azure-toepassingen](https://azure.microsoft.com/solutions/architecture/)
- [Azure-virtual machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage#storage)en [Azure-netwerken](https://azure.microsoft.com/services/?filter=networking#networking)

## <a name="create-a-new-offer"></a>Een nieuwe aanbieding maken

1. Meld u aan bij [Partner Center](https://partner.microsoft.com/dashboard/home).
2. Selecteer in het linkerdeel venster de optie **Commercial Marketplace**-  >  **overzicht**.
3. Selecteer op de pagina **overzicht** de optie **nieuwe**  >  **Azure virtual machine**aanbieden.

    ![Scherm afbeelding met de opties in het linkerdeel venster en de knop Nieuw aanbod.](./media/new-offer-azure-virtual-machine.png)

> [!NOTE]
> Nadat uw aanbieding is gepubliceerd, worden alle wijzigingen die u in het partner centrum aanbrengt, alleen op Azure Marketplace weer gegeven nadat u de aanbieding opnieuw hebt gepubliceerd. Zorg ervoor dat u een aanbieding altijd opnieuw publiceert nadat u de wijzigingen hebt aangebracht.

## <a name="new-offer"></a>Nieuwe aanbieding

Voer een **aanbiedings-id**in. Dit is een unieke id voor elke aanbieding in uw account.

- Deze ID is zichtbaar voor klanten in het webadres voor de Azure Marketplace-aanbieding en in Azure PowerShell en de Azure CLI, indien van toepassing.
- Gebruik alleen kleine letters en cijfers. De ID kan afbreek streepjes en onderstrepings tekens bevatten, maar mag niet langer zijn dan 50. Als u bijvoorbeeld **test-aanbieding-1**invoert, is het webadres van de aanbieding `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- De aanbiedings-ID kan niet worden gewijzigd nadat u **maken**hebt geselecteerd.

Voer een **alias**voor de aanbieding in. De aanbiedings alias is de naam die wordt gebruikt voor de aanbieding in Partner Center.

- Deze naam wordt niet gebruikt op Azure Marketplace. Dit wijkt af van de naam van de aanbieding en andere waarden die aan klanten worden weer gegeven.

Selecteer **maken** om de aanbieding te genereren en door te gaan.

## <a name="offer-setup"></a>Installatie van aanbieding

### <a name="test-drive"></a>Test Drive

Een *test station* is een uitstekende manier om uw aanbieding te presen teren aan potentiële klanten. Het biedt de mogelijkheid om te ' proberen voordat u koopt ', waarmee u uw conversies kunt verg Roten en zeer gekwalificeerde leads kan genereren. Zie [Wat is een test station?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)voor meer informatie.

Als u een test station voor een bepaalde periode wilt inschakelen, schakelt u het selectie vakje **een test drive inschakelen** in. Als u het test station uit uw aanbieding wilt verwijderen, schakelt u het selectie vakje uit.

Aanvullende bronnen voor test stations:

- [Technische best practices](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Best practices voor marketing](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Het overzicht van het test station downloaden](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) PDF-bestand (zorg ervoor dat pop-upblokkering is uitgeschakeld).

### <a name="customer-leads"></a>Leads van klanten

Wanneer u uw aanbieding publiceert naar de commerciële Marketplace met het partner centrum, sluit u deze aan op het CRM-systeem (Customer Relationship Management). Zo kunt u contact gegevens van klanten ontvangen zodra iemand interesse in of gebruikt voor uw product. Verbinding maken met een CRM is vereist als u een test station wilt inschakelen (Zie de voor gaande sectie). Anders is het maken van een verbinding met een CRM optioneel.

1. Selecteer een leadbestemming waarnaar wij de klantenleads moeten sturen. Het partner Centrum ondersteunt de volgende CRM-systemen:
    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) voor klant betrokkenheid
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Als uw CRM-systeem hier niet wordt vermeld, gebruikt u [Azure Table Storage](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) of een [https-eind punt](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) om uw klant lead gegevens op te slaan. Exporteer de gegevens vervolgens naar uw CRM-systeem.

1. Verbind uw aanbieding met de doel locatie van de lead wanneer u publiceert in het partner centrum.
1. Controleer of de verbinding met de doel locatie juist is geconfigureerd. Nadat u het in het partner centrum hebt gepubliceerd, valideert micro soft de verbinding en stuurt hij u een test lead. Wanneer u een voor beeld bekijkt van de aanbieding voordat deze live gaat, kunt u ook uw lead verbinding testen door zelf te proberen de aanbieding in de voorbeeld omgeving te implementeren.
1. Zorg ervoor dat de verbinding met de doel locatie van de lead bijgewerkt blijft, zodat u geen leads kwijtraakt.

1. Selecteer **concept opslaan** voordat u doorgaat.

## <a name="properties"></a>Eigenschappen

Op de pagina **Eigenschappen** definieert u de categorieën en sectoren die worden gebruikt voor het groeperen van uw aanbieding op Azure Marketplace, de versie van uw toepassing en de juridische contracten die uw aanbieding ondersteunen.

### <a name="categories"></a>Categorieën

Selecteer mini maal één en Maxi maal vijf categorieën. U gebruikt deze categorieën om uw aanbieding te plaatsen in de juiste Azure Marketplace-Zoek gebieden. In de beschrijving van de aanbieding wordt uitgelegd hoe uw aanbod deze categorieën ondersteunt. De aanbiedingen van de virtuele machine worden weer gegeven onder de **reken** categorie op Azure Marketplace.

### <a name="legal"></a>Juridisch

U moet de voor waarden van de aanbieding voor uw klanten opgeven. U hebt hiervoor twee opties:

- **Gebruik uw eigen voor waarden**

   Als u uw eigen aangepaste voor waarden wilt opgeven, voert u Maxi maal 10.000 tekens tekst in het vak **voor waarden** in. Als u een langere beschrijving nodig hebt, voert u één webadres in voor uw voor waarden. Het wordt weer gegeven voor klanten als een actieve koppeling.

   Uw klanten moeten deze voor waarden accepteren voordat ze uw aanbieding kunnen proberen.

- **Het standaard contract voor de micro soft Commercial Marketplace gebruiken**

   Micro soft biedt een standaard contract voor de commerciële Marketplace om het aankoop proces voor klanten te vereenvoudigen en de juridische complexiteit voor software leveranciers te reduceren. Wanneer u uw software onder het standaard contract aanbiedt, moeten klanten deze slechts eenmaal lezen en accepteren en hoeft u geen aangepaste voor waarden te maken.

   Gebruik het standaard contract door het selectie vakje **het standaard contract voor commerciële Marketplace van micro soft gebruiken** in te scha kelen en selecteer in het pop-upvenster de optie **accepteren** (mogelijk moet u omhoog schuiven om deze weer te geven).

   ![Scherm opname van het deel venster juridisch in het partner centrum met het selectie vakje ' het standaard contract gebruiken voor de commerciële Marketplace van micro soft '.](media/use-standard-contract.png)

  > [!NOTE]
  > Nadat u een aanbieding hebt gepubliceerd met behulp van het standaard contract voor de commerciële Marketplace, kunt u uw eigen aangepaste voor waarden niet gebruiken. U kunt een oplossing aanbieden onder het standaard contract of uw eigen voor waarden.

  Zie voor meer informatie [het standaard contract voor de micro soft Commercial Marketplace](https://docs.microsoft.com/azure/marketplace/standard-contract). Down load het [standaard](https://go.microsoft.com/fwlink/?linkid=2041178) -PDF-bestand (zorg ervoor dat pop-upblokkering is uitgeschakeld).

  **Wijzigingen in het standaard contract**

  Met de aanpassingen van het standaard contract kunt u de standaard contract voorwaarden voor eenvoud selecteren en de voor waarden voor uw product of bedrijf maken. Klanten moeten de wijzigingen in het contract alleen door nemen als ze het micro soft Standard-contract al hebben gecontroleerd en geaccepteerd. Er zijn twee soorten wijzigingen:

  * **Universele wijzigingen**: deze wijzigingen worden universeel toegepast op het Standard-contract voor alle klanten. Ze worden weer gegeven aan elke klant van de aanbieding in de inkoop stroom. Klanten moeten de voor waarden van het standaard contract en de wijzigingen accepteren voordat ze uw aanbieding kunnen gebruiken. U kunt één universele wijziging per aanbieding opgeven. U kunt een onbeperkt aantal tekens in dit vak invoeren. Deze voor waarden worden weer gegeven aan klanten in AppSource, Azure Marketplace en/of de Azure Portal tijdens de detectie-en aankoop stroom.

  * **Aangepaste wijzigingen**: deze speciale wijzigingen in het Standard-contract zijn gericht op specifieke klanten via Azure-Tenant-id's. U kunt kiezen welke Tenant u wilt instellen. Alleen klanten van de Tenant worden weer gegeven met de aangepaste wijzigings voorwaarden in de inkoop stroom van de aanbieding. Klanten moeten de voor waarden van het standaard contract en de wijzigingen accepteren voordat ze uw aanbieding kunnen gebruiken.

    1. Begin met het selecteren van **aangepaste wijzigings voorwaarden toevoegen (Maxi maal 10)**. U kunt Maxi maal tien aangepaste voor waarden per aanbieding opgeven. Ga als volgt te werk:

       a. Voer uw eigen wijzigings voorwaarden in het vak **aangepaste wijzigings voorwaarden** in. U kunt een onbeperkt aantal tekens opgeven. Alleen klanten van de Tenant-Id's die u opgeeft voor deze aangepaste voor waarden worden ze weer gegeven in de inkoop stroom van de aanbieding in de Azure Portal.

       b. Lang **Tenant-id's**opgeven. Elke aangepaste wijziging kan worden gericht op Maxi maal 20 Tenant-Id's. Als u een aangepaste wijziging toevoegt, moet u ten minste één Tenant-ID opgeven, waarmee uw klant in azure wordt geïdentificeerd. Uw klant kan dit in azure vinden door **Azure Active Directory**  >  **Eigenschappen**te selecteren. De waarde van de Directory-ID is de Tenant-ID (bijvoorbeeld 50c464d3-4930-494c-963c-1e951d15360e). U kunt ook de Tenant-ID van de organisatie van uw klant vinden met behulp van hun domein naam webadres op [Wat is mijn Microsoft Azure en Office 365-Tenant-id?](https://www.whatismytenantid.com/).

       c. Beschrijving Geef een **Beschrijving** op voor de Tenant-id, een die u helpt bij het identificeren van de klant met wie u de wijziging wilt aanrichten.

        > [!NOTE]
        > Deze twee typen wijzigingen zijn gekoppeld aan elkaar. Klanten die zijn gericht op aangepaste wijzigingen, krijgen ook de universele wijzigingen in het standaard contract tijdens de aankoop.

    1. Selecteer **concept opslaan** voordat u doorgaat.

## <a name="offer-listing"></a>Aanbieding weer geven

Op de **aanbiedings** pagina kunt u de details van het voor stel opgeven, zoals aanbiedings naam, beschrijving, koppelingen en contact personen.

> [!NOTE]
> Uw aanbiedings inhoud, zoals de beschrijving, documenten, scherm afbeeldingen en gebruiks voorwaarden, hoeft niet in het Engels te zijn, zolang de beschrijving van het aanbod begint met de woord groep ' deze toepassing is alleen beschikbaar in \<non-English language> '. U kunt ook een URL opgeven om een koppeling te maken naar een site die inhoud levert in een andere taal dan die wordt gebruikt in de aanbiedings inhoud van de aanbieding.

### <a name="marketplace-details"></a>Marketplace-gegevens

#### <a name="name"></a>Name

De naam die u hier invoert, wordt aan klanten weer gegeven als de titel van de aanbieding. Dit veld wordt aangevuld met de naam die u hebt ingevoerd in het vak **aanbiedings alias** tijdens het maken van de aanbieding. U kunt deze naam later wijzigen. De naam:

- Kan worden gehandelsd. U kunt symbolen voor het handels merk en copyright toevoegen.
- Mag niet langer zijn dan 50 tekens.
- Kan geen emojis bevatten.

#### <a name="search-results-summary"></a>Samen vatting van zoek resultaten

Geef een korte beschrijving van uw aanbieding, die wordt weer gegeven in de zoek resultaten voor Azure Marketplace. De naam mag Maxi maal 100 tekens bevatten.

#### <a name="long-summary"></a>Lange samen vatting

Geef een langere beschrijving van uw aanbieding op, zodat deze wordt weer gegeven in de zoek resultaten van Azure Marketplace. De naam mag Maxi maal 256 tekens bevatten.

#### <a name="description"></a>Beschrijving

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

#### <a name="privacy-policy-link"></a>Koppeling Privacybeleid

Voer het webadres (URL) in van het privacybeleid van uw organisatie. Zorg ervoor dat uw aanbieding voldoet aan de wetten en voor schriften van de privacy. U moet ook een geldig privacybeleid op uw website plaatsen.

### <a name="useful-links"></a>Handige koppelingen

Bied aanvullende online documenten over uw aanbieding. Selecteer **een koppeling toevoegen**en vul de volgende velden in om een koppeling toe te voegen:

- **Naam**: klanten zien de naam op de pagina Details.
- **Koppeling (URL)**: Geef een koppeling op waarmee klanten uw online document kunnen bekijken.

### <a name="customer-support-links"></a>Koppelingen voor klant ondersteuning

Geef de ondersteunings website op waar klanten uw ondersteunings team kunnen bereiken.

- Website over wereld wijde ondersteuning voor Azure
- Ondersteunings website voor Azure Government

### <a name="partner-support-contact"></a>Contact opnemen met partner ondersteuning

Geef contact gegevens op voor micro soft-partners die kunnen worden gebruikt wanneer uw klanten een ondersteunings ticket openen. Deze informatie wordt niet vermeld op Azure Marketplace.

- Name
- E-mail
- Telefoon

### <a name="engineering-contact"></a>Technische contact persoon

Geef contact gegevens op die micro soft kan gebruiken wanneer er problemen zijn met uw aanbieding, met inbegrip van problemen met certificering. Deze informatie wordt niet vermeld op Azure Marketplace.

- Name
- E-mail
- Telefoon

### <a name="azure-marketplace-media"></a>Azure Marketplace-media

Bied logo's en installatie kopieën die u kunt gebruiken met uw aanbieding. Alle installatie kopieën moeten de PNG-indeling hebben. Vage afbeeldingen zorgen ervoor dat uw inzending wordt afgewezen.

>[!NOTE]
>Als u een probleem hebt met het uploaden van bestanden, moet u ervoor zorgen dat uw lokale netwerk de https://upload.xboxlive.com service die wordt gebruikt door het partner centrum niet blokkeert.

#### <a name="azure-marketplace-logos"></a>Azure Marketplace-logo's

Geef PNG-bestanden van het logo van uw aanbieding op met de volgende vier afbeeldings dimensies:

- **Klein** (48 &times; 48 pixels)
- **Gemiddeld** (90 &times; 90 pixels)
- **Groot** (216 &times; 216 pixels)
- **Breed** (255 &times; 115 pixels)

Alle vier de logo's zijn vereist en worden weer gegeven in verschillende Azure Marketplace-vermeldingen.

#### <a name="screenshots"></a>Schermopnamen

Voeg Maxi maal vijf scherm opnamen toe die laten zien hoe uw aanbieding werkt. Elke scherm afbeelding moet 1280 &times; 720 pixels groot en in PNG-indeling zijn. Elke scherm opname moet een bijschrift bevatten.

#### <a name="videos"></a>Video's

Voeg Maxi maal vijf Video's toe die uw aanbieding aantonen. De Video's moeten worden gehost op een externe video service. Voer de naam, het webadres en een miniatuur van de video-afbeelding van de video in op 1280 &times; 720 pixels.

Zie [Best Practices for Marketplace](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)List voor aanvullende bronnen voor Marketplace-aanbiedingen.

Selecteer **concept opslaan** voordat u doorgaat.

## <a name="preview"></a>Preview

Selecteer het tabblad **voor beeld** en selecteer vervolgens een beperkte **Preview-doel groep** voor het valideren van uw aanbieding voordat u deze live publiceert naar het bredere publiek van de commerciële Marketplace.

> [!IMPORTANT]
> Nadat u uw aanbieding in het **voorbeeld** venster hebt gecontroleerd, selecteert u **Live bezoeken** om uw aanbieding te publiceren voor de open bare doel groep van de commerciële markt.

Uw preview-doel groep wordt geïdentificeerd door de Azure-abonnements-ID-GUID'S, samen met een optionele beschrijving voor elk. Geen van deze velden kan worden gezien door klanten. U kunt uw Azure-abonnements-ID vinden op de pagina **abonnementen** in de Azure Portal.

Voeg ten minste één Azure-abonnements-ID toe (Maxi maal 10 Id's) of door een CSV-bestand te uploaden (Maxi maal 100 Id's). Door deze abonnement-Id's toe te voegen, definieert u wie een voor beeld van uw aanbieding kan bekijken voordat deze Live wordt gepubliceerd. Als uw aanbieding al Live is, kunt u nog steeds een preview-doel opgeven voor het testen van wijzigingen in de aanbieding of updates voor uw aanbieding.

> [!NOTE]
> Een preview-doel groep wijkt af van een persoonlijke doel groep. Een preview-doel groep heeft toegang tot uw aanbieding _voordat_ deze Live wordt gepubliceerd op Azure Marketplace. De preview-doel groep kan alle plannen bekijken en valideren, met inbegrip van de abonnementen die alleen beschikbaar zijn voor een privé publiek nadat uw aanbieding volledig is gepubliceerd naar Azure Marketplace. Een persoonlijke doel groep (gedefinieerd in het deel venster **prijs en beschik baarheid** van abonnementen) heeft exclusieve toegang tot een bepaald abonnement.

Selecteer **concept opslaan** voordat u doorgaat naar de volgende sectie.

## <a name="plan-overview"></a>Overzicht van plan

U kunt in Partner Center diverse opties voor het plannen bieden binnen dezelfde aanbieding. Deze plannen werden voorheen Sku's genoemd. Voor een aanbieding is ten minste één abonnement vereist. Dit kan variëren per verdiensten maximaliseren-doel, Azure-regio,-functies of VM-installatie kopieën.

Nadat u uw plannen hebt gemaakt, selecteert u het tabblad Overzicht van het **plan** dat u wilt weer geven:

- Namen van plannen
- Licentie modellen
- Doel groep (openbaar of privé)
- Huidige publicatie status
- Beschikbare acties

De acties die beschikbaar zijn in het deel venster **plan overzicht** variëren, afhankelijk van de huidige status van uw abonnement.

- Als de plan status een concept is, selecteert u **concept verwijderen**.
- Als de status van het plan Live is gepubliceerd, selecteert u **stoppen verkoop plan beëindigen** of **persoonlijke doel groep synchroniseren**.

### <a name="create-a-new-plan"></a>Een nieuw abonnement maken

Selecteer bovenaan **Nieuw abonnement maken** . Het dialoog venster **nieuw plan** wordt weer gegeven.

Maak in het vak **abonnement-id** een unieke plan-id voor elk abonnement in deze aanbieding. Deze ID is zichtbaar voor klanten in het webadres van het product. Gebruik alleen kleine letters en cijfers, streepjes of onderstrepings tekens en Maxi maal 50.

> [!NOTE]
> De plan-ID kan niet worden gewijzigd nadat u **maken**hebt geselecteerd.

Voer in het vak **naam van abonnement** een naam in voor dit abonnement. Klanten zien deze naam wanneer ze bepalen welk abonnement binnen uw aanbieding moet worden geselecteerd. Maak een unieke naam waarmee duidelijk de verschillen tussen de plannen worden aangegeven. U kunt bijvoorbeeld **Windows Server** met *betalen per gebruik*-, *BYOL*-, *Geavanceerd*-en *Enter prise* -abonnementen invoeren.

Selecteer **Maken**.

### <a name="plan-setup"></a>Installatie plannen

Stel de configuratie op hoog niveau in voor het type abonnement, geef op of er een technische configuratie van een ander abonnement wordt gebruikt en Identificeer de Azure-regio's waar het plan beschikbaar moet zijn. Uw selecties bepalen welke velden worden weer gegeven op andere deel Vensters voor hetzelfde abonnement.

#### <a name="reuse-a-technical-configuration"></a>Een technische configuratie opnieuw gebruiken

Als u meer dan één abonnement van hetzelfde type hebt en de pakketten identiek zijn, kunt u de **technische configuratie van een ander abonnement selecteren met dit abonnement**. Met deze optie kunt u een van de andere plannen van hetzelfde type voor deze aanbieding selecteren en kunt u de technische configuratie ervan opnieuw gebruiken.

> [!NOTE]
> Wanneer u de technische configuratie van een ander abonnement opnieuw gebruikt, verdwijnt het hele tabblad **technische configuratie** uit dit abonnement. De technische configuratie details van het andere abonnement, inclusief eventuele updates die u in de toekomst maakt, worden ook voor dit abonnement gebruikt. Deze instelling kan niet worden gewijzigd nadat het plan is gepubliceerd.

#### <a name="azure-regions"></a>Azure-regio's

Uw abonnement moet beschikbaar worden gesteld in ten minste één Azure-regio.

Selecteer de optie **Azure Global** om uw plan beschikbaar te maken voor klanten in alle wereld wijde Azure-regio's die een commerciële Marketplace-integratie hebben. Zie [geografische Beschik baarheid en valuta ondersteuning](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies)voor meer informatie.

Selecteer de optie **Azure Government** om uw plan beschikbaar te maken in de regio [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome) . Deze regio voorziet in gecontroleerde toegang voor klanten van Amerikaanse federale, staats-, lokale of tribalee entiteiten, en voor partners die in aanmerking komen voor hen. Als uitgever bent u verantwoordelijk voor nalevings controles, beveiligings maatregelen en aanbevolen procedures. Azure Government maakt gebruik van fysiek geïsoleerde data centers en netwerken (alleen in de VS).

Voordat u naar [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)publiceert, moet u uw abonnement testen en valideren in de omgeving, omdat bepaalde eind punten kunnen verschillen. Als u uw abonnement wilt instellen en testen, vraagt u een proef account aan op de pagina met de [Microsoft Azure Government proef versie](https://azure.microsoft.com/global-infrastructure/government/request/) .

> [!NOTE]
> Nadat uw abonnement is gepubliceerd en beschikbaar is in een specifieke Azure-regio, kunt u die regio niet verwijderen.

#### <a name="azure-government-certifications"></a>Azure Government-certificeringen

Deze optie is alleen zichtbaar als u **Azure Government** als de Azure-regio hebt geselecteerd in de vorige sectie.

Azure Government services verwerken gegevens die onderworpen zijn aan bepaalde wettelijke voor schriften en vereisten. Bijvoorbeeld FedRAMP, NIST 800,171 (DIB), ITAR, IRS 1075, DoD N4 en CJIS. Als u uw certificeringen voor deze Program ma's wilt controleren, kunt u Maxi maal 100 koppelingen opgeven. Deze kunnen links naar uw vermelding in het programma staan, of koppelingen naar beschrijvingen van uw naleving met hen op uw eigen websites. Deze koppelingen zijn alleen zichtbaar voor Azure Government klanten.

Selecteer **concept opslaan** voordat u doorgaat.

### <a name="plan-listing"></a>Aanbieding plannen

In deze sectie configureert u de details van het overzicht van het plan. Dit deel venster geeft specifieke informatie weer, die kan verschillen van andere abonnementen in dezelfde aanbieding.

#### <a name="plan-name"></a>Plan naam

Dit veld wordt aangevuld met de naam die u hebt opgegeven bij het maken van het abonnement. Deze naam wordt op Azure Marketplace weer gegeven als de titel van dit abonnement. De waarde is beperkt tot 100 tekens.

#### <a name="plan-summary"></a>Samen vatting plannen

Geef een korte samen vatting van uw abonnement, niet de aanbieding. Deze samen vatting is beperkt tot 100 tekens.

#### <a name="plan-description"></a>Plan beschrijving

Beschrijf wat dit software plan uniek maakt en Beschrijf eventuele verschillen tussen abonnementen binnen uw aanbieding. Beschrijf het abonnement alleen, niet de aanbieding. De beschrijving van het abonnement kan Maxi maal 2.000 tekens bevatten.

Selecteer **concept opslaan** voordat u doorgaat.

### <a name="pricing-and-availability"></a>Prijzen en beschikbaarheid

In dit deel venster configureert u het volgende:

- De markten waar dit abonnement beschikbaar is.
- De prijs per uur.
- Hiermee wordt aangegeven of het plan zichtbaar moet worden voor iedereen of alleen voor specifieke klanten (een persoonlijke doel groep).

#### <a name="markets"></a>Landen

Elk plan moet beschikbaar zijn op ten minste één markt. Schakel het selectie vakje in voor elke markt locatie waar dit abonnement beschikbaar moet zijn voor aankoop. (Gebruikers op deze markten kunnen de aanbieding nog steeds implementeren voor alle Azure-regio's die zijn geselecteerd in de rubriek [installatie plannen](#plan-setup) .) De knop **BTW-overgeschreven** bevat landen/regio's waarin micro soft verkoop-en gebruiks belasting voor u verkrijgt. Publiceren naar China is beperkt tot abonnementen die *gratis* zijn of gebruikmaken van *uw eigen licentie* (BYOL).

Als u al prijzen hebt ingesteld voor uw abonnement in Amerikaanse dollar (USD) valuta en een andere markt locatie toevoegt, wordt de prijs voor de nieuwe markt berekend op basis van de huidige wissel koersen. Controleer altijd de prijs voor elke markt voordat u publiceert. Controleer uw prijzen door **export prijzen (XLSX)** te selecteren nadat u uw wijzigingen hebt opgeslagen.

Wanneer u een markt verwijdert, kunnen klanten van die markt die gebruikmaken van actieve implementaties geen nieuwe implementaties maken of de bestaande implementaties opschalen. Bestaande implementaties worden niet beïnvloed.

#### <a name="pricing"></a>Prijzen

Selecteer voor het **licentie model**op **gebruik gebaseerd maandelijks gefactureerd plan** om de prijzen voor dit abonnement te configureren of selecteer **Bring your own License** zodat klanten dit plan met hun bestaande licentie kunnen gebruiken.

Voor een op gebruik gebaseerd maandelijks gefactureerd abonnement gebruikt u een van de volgende drie opties voor prijs invoer:

- **Per kern**: prijs per kern in USD opgeven. Micro soft berekent de prijzen per kern grootte en converteert deze in lokale valuta's met behulp van de huidige wissel koers.
- **Per kern grootte**: Geef prijzen per kern grootte op in USD. De prijzen worden door micro soft berekend en omgezet in lokale valuta's met behulp van de huidige wissel koers.
- **Per markt en kern formaat**: Geef prijzen voor elke kern grootte op voor alle markten. U kunt de prijzen importeren vanuit een spread sheet.

> [!NOTE]
> Sla de prijs wijzigingen op om de export van prijs gegevens in te scha kelen. Nadat een prijs voor een markt in uw abonnement is gepubliceerd, kunt u deze later niet meer wijzigen. Om ervoor te zorgen dat de prijzen goed zijn voordat u ze publiceert, exporteert u het werk blad prijzen en bekijkt u de prijzen op elke markt.

#### <a name="free-trial"></a>Gratis proefversie

U kunt uw klanten een *gratis proef versie* van één maand of drie maanden aanbieden.

#### <a name="visibility"></a>Zichtbaarheid

U kunt elk plan ontwerpen dat zichtbaar is voor iedereen of alleen voor een vooraf geselecteerde doel groep. Lidmaatschappen in deze beperkte doel groep toewijzen met behulp van Azure-abonnement-Id's.

**Openbaar**: uw abonnement kan worden gezien door iedereen.

**Persoonlijke doel groep**: Maak uw plan alleen zichtbaar voor een voorgeselecteerde doel groep. Nadat het is gepubliceerd als een persoonlijk abonnement, kunt u de doel groep bijwerken of wijzigen in openbaar. Nadat u een plan openbaar hebt gemaakt, moet het openbaar blijven. Het kan niet worden teruggezet naar een privé-abonnement.

**Beperkte doel groep (Azure-abonnement-id's)**: wijs de doel groep toe die toegang heeft tot dit privé schema met behulp van Azure-abonnement-id's. Voeg eventueel een beschrijving toe van elk Azure-abonnements-ID dat u hebt toegewezen. U kunt Maxi maal 10 abonnements-Id's hand matig of Maxi maal 20.000 Id's toevoegen als u een CSV-werk blad importeert. Azure-abonnements-Id's worden weer gegeven als GUID'S en alle letters moeten kleine letters zijn.

> [!NOTE]
> Een persoonlijke of beperkte doel groep wijkt af van de preview-doel groep die u hebt gedefinieerd in het **voorbeeld** venster. Een preview-doel groep heeft toegang tot uw aanbieding _voordat_ deze live naar Azure Marketplace wordt gepubliceerd. Hoewel de keuze van de privé doelgroep alleen van toepassing is op een specifiek abonnement, kan de preview-doel groep alle persoonlijke en open bare plannen voor validatie doeleinden bekijken.

#### <a name="hide-a-plan"></a>Een abonnement verbergen

Als uw virtuele machine alleen indirect moet worden gebruikt wanneer ernaar wordt verwezen via een andere oplossings sjabloon of beheerde toepassing, schakelt u dit selectie vakje in om de virtuele machine te publiceren, maar deze te verbergen bij klanten die mogelijk rechtstreeks worden gezocht of hiernaar kunnen worden gebladerd.

> [!NOTE]
> Verborgen abonnementen bieden geen ondersteuning voor preview-koppelingen.

Selecteer **concept opslaan** voordat u doorgaat.

### <a name="technical-configuration"></a>Technische configuratie

Geef de installatie kopieën en andere technische eigenschappen op die aan dit plan zijn gekoppeld. Zie [een technische Asset voor Azure VM maken](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-technical-assets)voor meer informatie.

> [!NOTE]
> Het tabblad **technische configuratie** wordt niet weer gegeven als u dit plan hebt geconfigureerd voor het opnieuw gebruiken van pakketten van een ander abonnement op het tabblad **installatie plannen** .

#### <a name="operating-system"></a>Besturingssysteem

Voer de volgende handelingen uit in het deel venster van het **besturings systeem** :

- Voor **besturingssysteem familie**selecteert u het besturings systeem **Windows** of **Linux** .
- Voor **release** of **leverancier**selecteert u de Windows-versie of Linux-leverancier.
- Voer een beschrijvende naam voor het besturings systeem in voor de **beschrijvende naam**van het systeem. Deze naam is zichtbaar voor klanten.

#### <a name="recommended-vm-sizes"></a>Aanbevolen VM-grootten

Selecteer Maxi maal zes aanbevolen grootten voor virtuele machines die u wilt weer geven op Azure Marketplace.

#### <a name="open-ports"></a>Poorten openen

Open open bare of particuliere poorten op een geïmplementeerde virtuele machine.

#### <a name="storage-option-for-deployment"></a>Opslag optie voor implementatie

Selecteer bij **schijf implementatie optie**het type schijf implementatie dat door uw klanten kan worden gebruikt voor de virtuele machine. Micro soft raadt aan de implementatie alleen te beperken tot de **implementatie van Managed disks** .

#### <a name="properties"></a>Eigenschappen

Voor **ondersteuning van versneld netwerken**selecteert u of uw virtuele machine [versneld netwerken](https://go.microsoft.com/fwlink/?linkid=2124513)ondersteunt.

#### <a name="vm-images"></a>VM-installatiekopieën

Geef een schijf versie en de SAS-URI (Shared Access Signature) voor de installatie kopieën van de virtuele machine op. Voeg Maxi maal 16 gegevens schijven toe voor elke VM-installatie kopie. Geef slechts één nieuwe versie van de installatie kopie op per abonnement in een opgegeven inzending. Nadat een installatie kopie is gepubliceerd, kunt u deze niet meer bewerken, maar wel verwijderen. Als u een versie verwijdert, kunnen zowel nieuwe als bestaande gebruikers een nieuw exemplaar van de verwijderde versie implementeren.

- **Schijf versie**: de versie van de installatie kopie die u opgeeft.
- **SAS-URI**: de locatie in uw Azure Storage-account waar u de virtuele harde schijf van het besturings systeem hebt opgeslagen.
- Installatie kopieën van de gegevens schijf zijn ook Uri's van gedeelde Access-hand tekeningen voor VHD die zijn opgeslagen in hun Azure-opslag accounts.
- Voeg slechts één afbeelding per inzending toe aan een plan.

Ongeacht welk besturings systeem u gebruikt, voegt u alleen het minimum aantal gegevens schijven toe dat voor de oplossing nodig is. Tijdens de implementatie kunnen klanten geen schijven verwijderen die deel uitmaken van een installatie kopie, maar ze mogen altijd schijven toevoegen tijdens of na de implementatie.

Selecteer **concept opslaan** voordat u doorgaat en terug naar **overzicht**van het plan.

## <a name="resell-through-csps"></a>Door de Csp's door verkopen

Breid het bereik van uw aanbieding uit door deze beschikbaar te maken voor partners in het [Cloud Solution Provider-programma (CSP)](https://azure.microsoft.com/offers/ms-azr-0145p/) . Alle BYOL-abonnementen (voor het maken van uw eigen licentie) worden automatisch aangemeld bij het programma. U kunt er ook voor kiezen om uw keuze te maken in uw niet-BYOL plannen.

Selecteer **concept opslaan** voordat u doorgaat.

## <a name="test-drive"></a>Test Drive

Stel een demonstratie of *test station*in waarmee klanten uw aanbieding voor een bepaalde periode kunnen uitproberen voordat ze deze kopen. Als u een demonstratie omgeving voor uw klanten wilt maken, raadpleegt u [test drive aanbiedingen in de commerciële Marketplace](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive).

Als u een test station wilt inschakelen, schakelt u het selectie vakje **een test drive inschakelen** in het deel venster **instellingen voor aanbieding** in. Als u het test station uit uw aanbieding wilt verwijderen, schakelt u het selectie vakje uit.

Aanvullende bronnen voor test stations:

- [Best practices voor marketing](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Technische best practices](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Overzicht van test drive](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) PDF-bestand (zorg ervoor dat de pop-upblokkering is uitgeschakeld)

Selecteer **concept opslaan** voordat u doorgaat.

## <a name="review-and-publish"></a>Controleren en publiceren

Nadat u alle vereiste delen van de aanbieding hebt voltooid, kunt u deze voor beoordeling en publicatie indienen.

Selecteer in de rechter bovenhoek de optie **controleren en publiceren**.

In dit deel venster kunt u het volgende doen:

- Bekijk de voltooiings status voor elke sectie van de aanbieding:

  - **Niet gestart**: de sectie is niet gestart en moet worden voltooid.
  - **Onvolledig**: de sectie bevat fouten die moeten worden opgelost of waarvoor u meer informatie moet opgeven. Zie de eerdere secties in dit artikel voor hulp bij het bijwerken van de onvolledige informatie.
  - **Voltooid**: de sectie is voltooid en er zijn geen fouten. Alle onderdelen van de aanbieding moeten volledig zijn voordat u de aanbieding kunt indienen.
- Geef **notities voor certificering** aan het certificerings team op om ervoor te zorgen dat uw toepassing correct wordt getest. Neem instructies voor het testen op en eventuele aanvullende notities die het team kunnen helpen bij het begrijpen van uw toepassing.

Selecteer **controleren en publiceren**om de aanbieding voor publicatie in te dienen.

Micro soft stuurt een e-mail bericht om u te laten weten wanneer een preview-versie van de aanbieding beschikbaar is om te controleren en goed te keuren. Als u uw aanbieding naar het publiek wilt publiceren (of als het een privé-aanbieding is, publiceert u deze naar een privé-publiek), gaat u naar partner centrum en gaat u naar de pagina **overzicht** van uw aanbieding en selecteert u vervolgens **Go-Live**. De status van uw aanbieding wordt boven aan de pagina weer gegeven wanneer het publiceren wordt uitgevoerd.

### <a name="errors-and-review-feedback"></a>Fouten en feedback bekijken

De **hand matige validatie** stap in het publicatie proces vertegenwoordigt een uitgebreide beoordeling van uw aanbieding en de bijbehorende technische activa. Als dit proces fouten met uw aanbieding detecteert, ontvangt u een certificerings rapport met details over de problemen. U hoeft alleen de vereiste correcties aan te brengen en uw aanbieding opnieuw te publiceren.

## <a name="offer-overview"></a>Overzicht van aanbieding

Op de **overzichts** pagina van de aanbieding ziet u een visuele weer gave van de stappen die zijn voltooid en worden uitgevoerd, die nodig zijn om uw aanbieding te publiceren en hoe lang elke stap moet worden voltooid.

Deze pagina bevat ook koppelingen waarmee u kunt samen werken met de aanbieding, afhankelijk van de status:

- Als de aanbieding een concept is: [concept aanbieding verwijderen](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- Als de aanbieding Live is: de [aanbieding niet meer verkopen](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- Als de aanbieding in preview is: [Go-Live](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- Als u Publisher-afmelding niet hebt voltooid: [Publicatie annuleren](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="marketplace-examples"></a>Marketplace-voor beelden

Hier volgt een voor beeld van hoe de aanbiedings gegevens worden weer gegeven in azure Marketplace:

:::image type="content" source="media/example-azure-marketplace-virtual-machine-offer.png" alt-text="Illustreert hoe deze aanbieding wordt weer gegeven in azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Beschrijvingen van aanroepen

1. Groot logo
2. Prijs
3. Categorieën
4. Voorwaarden
5. Adres van privacybeleid (koppeling)
6. Naam van aanbieding
7. Beschrijving
8. Handige koppelingen
9. Scherm afbeeldingen/Video's

<br>Hier volgt een voor beeld van hoe de aanbiedings gegevens worden weer gegeven in de zoek resultaten van Azure Marketplace:

:::image type="content" source="media/example-azure-marketplace-virtual-machine-search-results.png" alt-text="Illustreert hoe deze aanbieding wordt weer gegeven in de zoek resultaten voor Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Beschrijvingen van aanroepen

1. Klein logo
2. Naam van aanbieding
3. Samen vatting van zoek resultaten
4. Proefversie

<br>Hier volgt een voor beeld van Details van het Azure Marketplace-abonnement:

:::image type="content" source="media/example-azure-marketplace-virtual-machine-plan-details.png" alt-text="Illustreert Details van het Azure Marketplace-abonnement.":::

#### <a name="call-out-descriptions"></a>Beschrijvingen van aanroepen

1. Naam en samen vatting van plan
2. VM-grootten aanbevelen
3. Prijzen plannen

<br>Hier volgt een voor beeld van hoe de aanbiedings gegevens worden weer gegeven in de Azure Portal:

:::image type="content" source="media/example-azure-portal-virtual-machine-offer.png" alt-text="Illustreert hoe deze aanbieding wordt weer gegeven in de Azure Portal.":::

#### <a name="call-out-descriptions"></a>Beschrijvingen van aanroepen

1. Naam
2. Beschrijving
3. Handige koppelingen
4. Scherm afbeeldingen/Video's

<br>Hier volgt een voor beeld van hoe de aanbiedings gegevens worden weer gegeven in de Azure Portal Zoek resultaten:

:::image type="content" source="media/example-azure-portal-virtual-machine-search-results.png" alt-text="Illustreert hoe deze aanbieding wordt weer gegeven in de Azure Portal Zoek resultaten.":::

#### <a name="call-out-descriptions"></a>Beschrijvingen van aanroepen

1. Klein logo
2. Naam van aanbieding
3. Samen vatting van zoek resultaten

<br>Hier volgt een voor beeld van de details van het Azure Portal plan:

:::image type="content" source="media/example-azure-portal-virtual-machine-plan-details.png" alt-text="Illustreert de details van het Azure Portal plan.":::

#### <a name="call-out-descriptions"></a>Beschrijvingen van aanroepen

1. Plan naam
2. Plan beschrijving

## <a name="next-step"></a>Volgende stap

- [Een bestaande aanbieding bijwerken in de commerciële Marketplace](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
