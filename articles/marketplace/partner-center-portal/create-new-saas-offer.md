---
title: Maak een nieuwe SaaS-aanbieding voor de commerciële marktplaats van Microsoft
description: Een nieuwe Software as a Service-aanbieding (SaaS) maken voor vermelding of verkoop in Microsoft AppSource, Azure Marketplace of via het CSP-programma (Cloud Solution Provider) met behulp van het Microsoft-commerciële marktplaatsprogramma in Microsoft Partner Center.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: a39e1e19f65722b4b5ae809ca943da719a3c6e22
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869780"
---
# <a name="create-a-new-saas-offer"></a>Een nieuwe SaaS-aanbieding maken

Als u wilt beginnen met het maken van Software as a Service (SaaS) aanbiedingen, moet u ervoor zorgen dat u eerst [een Partner Center-account maakt](./create-account.md) en het dashboard Commercial [Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)opent, met het tabblad **Overzicht** geselecteerd.

![Commercieel Marketplace-dashboard in partnercentrum](./media/new-offer-overview.png)

>[!Note]
> Zodra een aanbieding is gepubliceerd, worden bewerkingen van de aanbieding in het Partnercenter alleen bijgewerkt in het systeem en worden de fronten na het opnieuw publiceren bijgewerkt. Zorg ervoor dat u de aanbieding voor publicatie indient nadat u wijzigingen hebt aangebracht.

Selecteer de + **Nieuwe aanbieding...** knop en selecteer vervolgens het **menu-item Software as a Service.**

Als u een ander aanbiedingstype selecteert, wordt u mogelijk doorgestuurd naar de oudere [Cloud Partner Portal.](https://cloudpartner.azure.com/) Op dit moment zijn alleen SaaS- en Dynamics 365-aanbiedingen beschikbaar in het Commercial Marketplace-portaal op Partner Center.

![Aanbiedingsvenster maken in partnercentrum](./media/new-offer-click.png)

Het dialoogvenster **Nieuwe aanbieding** wordt weergegeven.

![Dialoogvenster Nieuwe aanbieding](./media/new-offer-popup.png)

## <a name="offer-id-and-alias"></a>Aanbiedings-ID en alias

- **Aanbiedings-id:** unieke id voor elke aanbieding in uw account. Deze id is zichtbaar voor klanten in het URL-adres voor de marketplace-aanbieding en Azure Resource Manager-sjablonen (indien van toepassing). Aanbiedings-ID moet kleine letters, alfanumerieke (inclusief koppeltekens en underscores, maar geen witruimte). De **aanbiedings-id** is beperkt tot 50 tekens en kan niet worden gewijzigd nadat u *Maken hebt geselecteerd.*  
Voorbeeld: test-aanbieding-1
<br>Met de URL als resultaat:`https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`

- **Aanbiedingsalias**: De naam die wordt gebruikt om te verwijzen naar de aanbieding binnen het partnercentrumportaal. Deze naam wordt niet gebruikt in de marktplaats en is anders dan de *aanbiedingsnaam* en andere waarden die aan klanten worden weergegeven. Deze waarde kan niet worden gewijzigd nadat u Maken hebt *geselecteerd.*

<br>Voorbeeld: Testaanbieding 1&#8482;

Selecteer **Maken**.  Voor deze aanbieding wordt een **overzichtspagina** voor aanbiedingen gemaakt.  

<!---
![Offer overview on Partner Center](./media/commercial-marketplace-offer-overview.png)
-->

## <a name="offer-overview"></a>Aanbiedingsoverzicht

De **overzichtspagina aanbieding** bevat:

- De **publicatiestatus** geeft een visuele weergave weer van de stappen die nodig zijn om deze aanbieding te publiceren en hoe lang het duurt voordat elke stap is voltooid. Onvolledige pictogrammen voor het publiceren van stappen worden grijs weergegeven.

- Het **overzichtsmenu Aanbieding** bevat een lijst met koppelingen voor het uitvoeren van bewerkingen op deze aanbieding. Deze lijst met bewerkingen wordt gewijzigd op basis van de selectie die u maakt voor uw aanbieding.  
    - Als de aanbieding een concept is - Concept verwijderen
    - Als de aanbieding live is - Stop met verkopen
    - Als de aanbieding in preview is - Go-live
    - Als u zich nog niet hebt afmelden voor uitgevers, u publiceren annuleren

## <a name="offer-setup"></a>Instelling voor aanbieding

Op het tabblad **Instelling van aanbieding** wordt om de volgende informatie gevraagd. Selecteer **Opslaan** nadat u deze velden hebt voltooid.

- **Wilt u verkopen via Microsoft?** (Ja/Nee)
    - **Ja,** u wilt uw aanbieding verkopen via Microsoft, waarbij Microsoft namens u marktplaatstransacties host; Of 
    - **Nee,** u wilt uw aanbieding liever gewoon via de marktplaatsen vermelden en eventuele monetaire transacties onafhankelijk van Microsoft verwerken.

### <a name="sell-through-microsoft"></a>Verkopen via Microsoft

Verkopen via Microsoft biedt betere klantdetectie en -acquisitie, stelt Microsoft in staat marktplaatstransacties namens u te hosten en maakt gebruik van de wereldwijd beschikbare commercemogelijkheden van Microsoft.

#### <a name="saas-offer-requirements"></a>SaaS biedt eisen

Om Software as a Service (SaaS) aanbiedingen met Commercial Marketplace op Partner Center te vermelden, moet aan de volgende criteria worden voldaan:

- Uw aanbieding moet [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) gebruiken voor identiteitsbeheer en verificatie.
- Uw aanbieding moet [SaaS Fulfillment API's](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) gebruiken om te integreren met de Azure Marketplace.
- Voor uitgebreidere vereisten, zie de [SaaS Offer Publishing Guide](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide).

#### <a name="saas-pricing-and-billing-options"></a>SaaS-prijs- en factureringsopties

Met SaaS-oplossingen die worden uitgevoerd in het Azure-abonnement van de uitgever, omvatten licentiekosten die door klanten worden betaald, de kosten van de infrastructuur waarop de software wordt geïmplementeerd. Het gebruik van azure-infrastructuur wordt rechtstreeks beheerd en gefactureerd aan u, de partner. Werkelijke gebruikskosten voor infrastructuur worden niet gezien door de klant. Uitgevers moeten gebruikskosten voor Azure-infrastructuur bundelen in hun softwarelicentieprijzen. 

SaaS biedt ondersteuning voor maandelijkse of jaarlijkse facturering op basis van een vast bedrag per gebruiker of verbruikskosten met behulp van de factureringsservice met datameter. De commerciële marktplaats van Microsoft werkt volgens een bureaumodel, waarbij uitgevers prijzen vaststellen, Microsoft klanten factureert en Microsoft inkomsten aan de uitgever betaalt terwijl ze een bemiddelingskosten inhouden.

In de volgende tabel ziet u een voorbeeld van kosten en uitbetalingen om het bureaumodel aan te tonen.

|**Uw licentiekosten**|**$100 per maand**|
|:---|:---|
|Azure-gebruikskosten (D1/1-Core)|Rechtstreeks gefactureerd aan de uitgever, niet aan de klant|
|Klant wordt gefactureerd door Microsoft|$ 100,00 per maand (Uitgever moet rekening houden met gemaakte of doorgeefinfrastructuurkosten in de licentiekosten)|

|**Microsoft rekeningen**|**$100 per maand**|
|:---|:---|
|Microsoft betaalt u 80% van uw licentiekosten <br>**Voor gekwalificeerde SaaS-apps betaalt Microsoft 90% van uw licentiekosten*|$ 80,00 per maand <br>*$* 90,00 per maand*|

- In dit voorbeeld factureert Microsoft $ 100,00 aan de klant voor uw softwarelicentie en betaalt het $ 80,00 uit aan de uitgever.
- Partners die in aanmerking zijn gekomen voor de **gereduceerde Marketplace-servicekosten,** krijgen van mei 2019 tot juni 2020 een gereduceerde transactievergoeding te zien op de SaaS-aanbiedingen. In dit scenario factureert Microsoft $ 100,00 voor uw softwarelicentie en betaalt het $ 90,00 uit aan de uitgever.

> [!NOTE]
> **Lagere Marketplace-servicekosten**: Voor bepaalde SaaS-aanbiedingen die u op onze commerciële marktplaats hebt gepubliceerd, verlaagt Microsoft de Marketplace-servicekosten van 20% (zoals beschreven in de Microsoft Publisher Agreement) tot 10%. Om uw aanbod in aanmerking te laten komen, moet ten minste één van uw aanbiedingen door Microsoft zijn aangewezen als ip-co-sell ready of IP co-sell.  Om deze verlaagde Marketplace-servicekosten voor de maand te ontvangen, moet ten minste vijf (5) werkdagen voor het einde van elke kalendermaand in aanmerking komen.  De gereduceerde Marketplace-servicekosten zijn niet van toepassing op VM's, beheerde apps of andere producten die beschikbaar worden gesteld via onze commerciële marktplaats.  De gereduceerde Marketplace-servicekosten zijn alleen beschikbaar voor gekwalificeerde aanbiedingen voor licentiekosten die microsoft tussen 1 mei 2019 en 30 juni 2020 heeft verzameld.  Na deze tijd worden de Marketplace-servicekosten teruggegeven naar het normale bedrag.

### <a name="list-through-microsoft"></a>Lijst via Microsoft

Promoot uw bedrijf met Microsoft door een marktplaatsvermelding te maken. Als u ervoor kiest om uw aanbieding alleen te vermelden en niet via Microsoft te handelen, betekent dit dat Microsoft niet rechtstreeks deelneemt aan softwarelicentietransacties. Er zijn geen transactiekosten verbonden en de uitgever houdt 100% van de softwarelicentiekosten bij die van de klant worden geïnd. De uitgever is echter verantwoordelijk voor het ondersteunen van alle aspecten van de softwarelicentietransactie, inclusief maar niet beperkt tot: orderafhandeling, meting, facturering, facturering, betaling en incasso.

- **Hoe wilt u dat potentiële klanten interactie hebben met deze aanbiedingsaanbieding?**

#### <a name="get-it-now-free"></a>Krijg het nu (gratis)

Bied uw aanbieding gratis aan klanten aan door een geldige URL (te beginnen met *http* of *https)* op te geven waar ze een proefversie kunnen krijgen via [verificatie met één klik met Azure AD](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide#using-azure-active-directory-to-enable-trials).  Bijvoorbeeld: `https://contoso.com/saas-app`

#### <a name="free-trial-listing"></a>Gratis proefperiode (aanbieding)

Bied uw aanbieding aan klanten met een koppeling naar een gratis proefperiode door een geldige URL op te geven (te beginnen met *http* of *https),* waar ze een proefversie kunnen krijgen via [verificatie met één klik met behulp van Azure Active Directory (Azure AD).](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide#using-azure-active-directory-to-enable-trials)  Bijvoorbeeld: `https://contoso.com/trial/saas-app`. Aanbiedingsvrije proefversies worden gemaakt, beheerd en geconfigureerd door uw service en hebben geen abonnementen die door Microsoft worden beheerd.

> [!NOTE]
> De tokens die uw toepassing ontvangt via uw testkoppeling, kunnen alleen worden gebruikt om gebruikersgegevens te verkrijgen via Azure AD om het maken van accounts in uw app te automatiseren. Microsoft-accounts (MSA) worden niet ondersteund voor verificatie met behulp van dit token.

#### <a name="contact-me"></a>Neem contact met mij op

Verzamel klantcontactgegevens door uw CRM-systeem (Customer Relationship Management) met elkaar te verbinden. De klant wordt om toestemming gevraagd om zijn informatie te delen. Deze klantgegevens, samen met de naam van de aanbieding, id en marktplaatsbron waar ze uw aanbieding hebben gevonden, worden verzonden naar het CRM-systeem dat u hebt geconfigureerd. Zie [Leadmanagement](#connect-lead-management)verbinden voor meer informatie over het configureren van uw CRM.

## <a name="example-marketplace-offer-listing"></a>Voorbeeld van een lijst met Marketplace-aanbiedingen

![Voorbeeldaanbieding voor marktplaatsaanbiedingen met notities](./media/marketplace-offer.svg)

## <a name="enable-a-test-drive"></a>Een proefrit inschakelen

Een testrit is een geweldige manier om uw aanbod aan potentiële klanten te presenteren door hen de mogelijkheid te geven om 'proberen voordat u koopt', wat resulteert in een verhoogde conversie en het genereren van hooggekwalificeerde leads. Zie [Uw klanten een proefrit laten maken in uw aanbieding](./test-drive.md)voor meer informatie.

- **Een teststation inschakelen** (selectievakje)

Door een proefrit in te schakelen, wordt u gevraagd een demonstratieomgeving te configureren voor klanten om uw aanbieding voor een bepaalde periode uit te proberen. 

### <a name="test-drive-resources"></a>Bronnen voor teststations

- [Test Drive marketing best practices](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)- [Test Drive technische best practices](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Overzicht van teststation (PDF-download)](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>Leadbeheer verbinden

[!INCLUDE [Connect lead management](./includes/connect-lead-management-a.md)]

#### <a name="additional-lead-management-resources"></a>Aanvullende bronnen voor leadbeheer
- [Veelgestelde vragen over leadmanagement](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Algemene leadconfiguratiefouten](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Overzicht van leadbeheer Één pager](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)

Vergeet niet op te **slaan** voordat u naar de volgende sectie gaat.

## <a name="properties"></a>Eigenschappen

Op het tabblad **Eigenschappen** wordt u gevraagd de categorieën en bedrijfstakken te definiëren die worden gebruikt om uw aanbieding op de marktplaatsen, de juridische contracten ter ondersteuning van uw aanbieding en uw app-versie te groeperen.

Selecteer **Opslaan** nadat u deze velden hebt voltooid.

### <a name="category"></a>Categorie

Selecteer minimaal één (1) en maximaal drie (3) categorieën die worden gebruikt voor het groeperen van uw aanbieding in de juiste zoekgebieden op de markt. Roep in de aanbiedingsbeschrijving hoe uw aanbieding deze categorieën ondersteunt.

### <a name="industry"></a>Branche

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="app-version"></a>App-versie

Dit veld is optioneel en wordt gebruikt in de AppSource-marktplaats om het versienummer van uw aanbieding te identificeren.

### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Standaardcontract voor de commerciële marktplaats van Microsoft

Microsoft biedt een sjabloon voor standaardcontracten.

- **Het standaardcontract gebruiken voor de commerciële marktplaats van Microsoft?**

Om het inkoopproces voor klanten te vereenvoudigen en de juridische complexiteit voor softwareleveranciers te verminderen, biedt Microsoft een standaardcontract voor de commerciële marktplaats van Microsoft om transacties op de markt te vergemakkelijken. In plaats van aangepaste algemene voorwaarden te maken, kunnen commerciële marktplaatsuitgevers ervoor kiezen om hun software aan te bieden in het kader van het standaardcontract, dat klanten slechts één keer hoeven te controleren en accepteren. Het standaardcontract is hier https://go.microsoft.com/fwlink/?linkid=2041178te vinden: .

U ervoor kiezen om het standaardcontract te gebruiken in plaats van uw eigen aangepaste voorwaarden op te geven door het selectievakje 'Gebruik het standaardcontract voor de commerciële marktplaats' in te schakelen.

![Het selectievakje Standaardcontract gebruiken](./media/use-standard-contract.png)

> [!NOTE]
> Zodra u een aanbieding publiceert met behulp van het standaardcontract voor microsoft-commerciële marktplaats, u uw eigen aangepaste algemene voorwaarden niet meer gebruiken. Het is een "of" scenario. U biedt uw oplossing onder het standaardcontract **of** uw eigen algemene voorwaarden. Als u de voorwaarden van het standaardcontract wilt wijzigen, u dit doen via standaardcontractwijzigingen.

#### <a name="standard-contract-amendments"></a>Standaardcontractwijzigingen

Met standaardcontractwijzigingen kunnen uitgevers de standaardcontractvoorwaarden voor eenvoud selecteren en de voorwaarden voor hun product of bedrijf aanpassen. Klanten hoeven de wijzigingen in het contract alleen te bekijken als ze het Microsoft Standard-contract al hebben beoordeeld en geaccepteerd.

Er zijn twee soorten wijzigingen beschikbaar voor commerciële marktplaatsuitgevers:

- Universele wijzigingen: Deze wijzigingen worden universeel toegepast op het standaardcontract voor alle klanten. Universele wijzigingen worden getoond aan elke klant van het aanbod in de aankoopstroom. Klanten moeten de voorwaarden van het standaardcontract en de wijziging accepteren voordat ze uw aanbieding kunnen gebruiken.
- Aangepaste wijzigingen: deze wijzigingen zijn speciale wijzigingen in het standaardcontract die alleen zijn gericht op specifieke klanten via Azure-tenant-id's. Uitgevers kunnen de tenant kiezen die ze willen targeten. Alleen klanten van de tenant krijgen de aangepaste wijzigingsvoorwaarden in de aankoopstroom van de aanbieding te zien.  Klanten moeten de voorwaarden van het standaardcontract en de wijziging(en) accepteren voordat ze uw aanbieding kunnen gebruiken.

>[!NOTE]
> Deze twee soorten amendementen stapelen op elkaar. Klanten die zijn gericht op aangepaste wijzigingen krijgen ook de universele wijziging van het standaardcontract tijdens de aankoop.

**Universele wijzigingsvoorwaarden voor het standaardcontract voor de commerciële marktplaats van Microsoft**: Voer de universele wijzigingsvoorwaarden in dit vak in. Per aanbieding u één universele wijziging indienen. U een onbeperkt aantal tekens invoeren in dit vak. Deze termen worden weergegeven aan klanten in AppSource, Azure Marketplace en/of Azure-portal tijdens de detectie- en aankoopstroom.

**Aangepaste wijzigingsvoorwaarden voor het standaardcontract voor de commerciële marktplaats van Microsoft:** Begin met het selecteren **van Aangepaste wijzigingsvoorwaarden toevoegen.** Per aanbieding u maximaal 10 aangepaste wijzigingsvoorwaarden opgeven.

- **Aangepaste wijzigingsvoorwaarden**: Voer uw aangepaste wijzigingsvoorwaarden in het vak aangepaste wijzigingsvoorwaarden in. U een onbeperkt aantal tekens invoeren in dit vak. Alleen klanten van de tenant-id's die u opgeeft voor deze aangepaste voorwaarden, krijgen de aangepaste wijzigingsvoorwaarden in de aankoopstroom van de aanbieding in de Azure-portal te zien.  
- **Tenant-id's** (vereist): Elke aangepaste wijziging kan worden gericht op maximaal 20 tenant-id's. Als u een aangepaste wijziging toevoegt, moet u ten minste één tenant-id opgeven. De tenant-id identificeert uw klant in Azure. U uw klant om deze id vragen en deze kunnen deze vinden door te navigeren naar portal.azure.com > Azure Active Directory > Eigenschappen. De directory-ID-waarde is de tenant-id (bijvoorbeeld 50c464d3-4930-494c-963c-1e951d15360e). U ook de tenant-id van uw klant opzoeken met behulp van de URL van de domeinnaam bij [Wat is mijn Microsoft Azure- en Office 365-tenant-id?](https://www.whatismytenantid.com).
- **Beschrijving** (optioneel): Geef optioneel een vriendelijke beschrijving voor de tenant-id waarmee u de klant identificeren die u met de wijziging target.

#### <a name="terms-and-conditions"></a>Voorwaarden

Als u uw eigen aangepaste algemene voorwaarden wilt bieden, u ervoor kiezen deze in te voeren in het veld Algemene voorwaarden. U in dit veld maximaal 10.000 tekens tekst invoeren. Als uw algemene voorwaarden een langere beschrijving vereisen, voert u één URL-koppeling in dit veld in waar uw algemene voorwaarden kunnen worden gevonden. Het zal worden weergegeven aan klanten als een actieve link.

Klanten zijn verplicht om deze voorwaarden te accepteren voordat ze uw aanbieding kunnen proberen.

Vergeet niet op te **slaan** voordat u naar de volgende sectie gaat.

## <a name="offer-listing"></a>Aanbieding aanbieden

Op het tabblad Aanbiedingsvermelding worden de talen (en markten) weergegeven waar uw aanbieding beschikbaar is, momenteel is Engels (Verenigde Staten) de enige beschikbare locatie. Bovendien geeft deze pagina de status weer van de taalspecifieke vermelding en de datum/tijd die is toegevoegd. U moet de plaatsdetails (aanbiedingsnaam, beschrijving, onderzoekstermijnen, enz.) voor elke taal/markt bepalen.

> [!NOTE]
> Aanbiedingsinhoud (zoals aanbiedingsbeschrijving, documenten, screenshots, gebruiksvoorwaarden en privacybeleid) is niet verplicht om in het Engels te zijn, zolang de aanbiedingsbeschrijving begint met de zinsnede :"Deze toepassing is alleen beschikbaar in [niet-Engelse taal]." Het is ook aanvaardbaar om een *URL voor nuttige koppelingen* op te geven om inhoud aan te bieden in een andere taal dan die welke wordt gebruikt in de inhoud van de aanbiedingsvermelding.

### <a name="offer-listings"></a>Aanbiedingen aanbieden

Geef details op die in de markt moeten worden weergegeven, inclusief beschrijvingen van uw aanbieding en marketingmiddelen.

- **Naam** (vereist): de hier gedefinieerde naam wordt weergegeven als de titel van uw aanbiedingsvermelding op de door u gekozen marktplaats(en). De naam is vooraf ingevuld op basis van uw vorige **nieuwe aanbiedingsvermelding.** De naam kan een handelsmerk zijn. Het mag geen emoji's bevatten (tenzij het handelsmerk- en auteursrechtsymbolen zijn) en moet worden beperkt tot 50 tekens.
- **Samenvatting** (vereist): geef een korte beschrijving van uw aanbieding die moet worden gebruikt in zoekresultaten met marketplace-vermeldingen. In dit veld kunnen maximaal 100 tekens tekst worden ingevoerd.
- **Beschrijving** (vereist): geef een beschrijving van uw aanbieding die moet worden weergegeven in het overzicht van de marktplaatsvermelding(en). Overweeg het opnemen van een waardepropositie, belangrijke voordelen, elke categorie of brancheorganisaties, in-app aankoopmogelijkheden, eventuele vereiste openbaarmakingen en een link om meer te weten te komen.
In dit veld kunnen maximaal 3.000 tekens tekst worden ingevoerd, inclusief markeringen. Voor meer tips, zie het artikel [Schrijf een grote app beschrijving](https://docs.microsoft.com/windows/uwp/publish/write-a-great-app-description).
- **Zoekzoekwoorden**: Voer maximaal drie zoekzoekwoorden in die klanten kunnen gebruiken om uw aanbieding in de marketplace(s) te vinden.
- **Instructies aan de slag** (vereist): Leg uit hoe u uw app configureert en gebruikt voor potentiële klanten.  Deze quickstart kan links bevatten naar meer gedetailleerde online documentatie. In dit veld kunnen maximaal 3.000 tekens tekst worden ingevoerd.

#### <a name="description"></a>**Beschrijving**

Dit veld is een vereiste. Items die in **beschrijving**worden opgenomen:

* Beschrijf duidelijk de waardepropositie van uw aanbieding in de eerste paar zinnen van uw beschrijving.  
* Houd er rekening mee dat de eerste paar zinnen kunnen worden weergegeven in de resultaten van zoekmachines.  
* Vertrouw niet op functies en functionaliteit om uw product te verkopen. Richt u in plaats daarvan op de waarde die u levert.  
* Gebruik zoveel mogelijk gebruik van branchespecifieke woordenschat of op voordelen gebaseerde formuleringen.

Kerncomponenten van uw waardepropositie moeten de volgende informatie bevatten:

* Beschrijving van het product.
* Type gebruiker dat profiteert van het product.
* De behoeften van de klant of pijn die het product aanpakt.

Als u de **beschrijving van** uw aanbieding aantrekkelijker wilt maken, gebruikt u de editor voor rijke tekst om uw beschrijving op te maken.

![De teksteditor met rijke tekst gebruiken](./media/text-editor2.png)

Gebruik de volgende instructies om de teksteditor met rijke tekst te gebruiken:

- Als u de indeling van uw inhoud wilt wijzigen, markeert u de tekst die u wilt opmaken en selecteert u een tekststijl, zoals hieronder wordt weergegeven:

     ![De teksteditor met rijke tekst gebruiken om de tekstnotatie te wijzigen](./media/text-editor3.png)

- Als u een lijst met opsommingstekens of nummers aan de tekst wilt toevoegen, gebruikt u de onderstaande opties:

     ![De teksteditor met rijke tekst gebruiken om lijsten toe te voegen](./media/text-editor4.png)

- Als u inspringing aan de tekst wilt toevoegen of verwijderen, gebruikt u de onderstaande opties:

     ![De teksteditor met rijke tekst gebruiken om in te springen](./media/text-editor5.png)

#### <a name="links"></a>Koppelingen

- **Privacybeleid** (vereist): Koppeling naar het privacybeleid van uw organisatie. U bent verantwoordelijk voor het waarborgen dat uw app voldoet aan de privacywet- en regelgeving en voor het verstrekken van een geldig privacybeleid
- **CSP Program Marketing Materials** (optioneel): Geef een link naar marketingmateriaal als u ervoor kiest om uw aanbod uit te breiden naar het [CSP-programma (Cloud Solution Provider).](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers) CSP breidt uw aanbod uit naar een breder scala aan gekwalificeerde klanten door CSP-partners in staat te stellen uw aanbod te bundelen, op de markt te brengen en door te verkopen. Deze resellers hebben toegang nodig tot materialen voor het op de markt brengen van uw aanbod. Zie [Go-To-Market Services](https://partner.microsoft.com/reach-customers/gtm)voor meer informatie.
- **Nuttige koppelingen** (optioneel): optionele aanvullende online documenten over uw app of gerelateerde services die worden vermeld door een **titel** en **URL**op te geven. Voeg extra nuttige koppelingen toe door op **+ Een URL toevoegen te**klikken .

#### <a name="contact-information"></a>Contactgegevens

- **Contactpersonen:** Geef voor elke klantcontactpersoon een **naam**van een werknemer, **telefoonnummer**en **e-mailadres** op.  (Deze *worden niet* openbaar weergegeven). Er is ook **een URL voor ondersteuning** vereist voor de groep **Ondersteuningscontactpersonen.**  (Deze informatie *wordt* openbaar weergegeven).

**Ondersteuningscontact** (vereist): voor algemene ondersteuningsvragen.

**Technisch contact** (vereist): Voor technische vragen.

**Channel Manager contact** (vereist): Voor reseller vragen met betrekking tot het CSP-programma.

#### <a name="files-and-images"></a>Bestanden en afbeeldingen

- **Documenten** (vereist): Voeg gerelateerde marketingdocumenten toe voor uw aanbieding, in PDF-formaat, met minimaal één (1) en maximaal drie (3) documenten per aanbieding.
- **Afbeeldingen** (optioneel): er zijn meerdere plaatsen waar de logoafbeeldingen van uw aanbieding in de hele marktplaats(en) kunnen worden weergegeven, waarvoor de volgende formaten nodig zijn : Klein: 48 x 48 pixels _(vereist),_ Gemiddeld: 90 x 90 pixels _(vereist),_ Groot: 216 x 216 pixels _(vereist),_ Breed: 255 x 115 pixels en Hero: 815 x 290 pixels. Alle afbeeldingen moeten in . PNG-indeling.
- **Screenshots** (vereist): Voeg screenshots toe die uw aanbieding demonstreren. Er mogen maximaal vijf (5) screenshots worden toegevoegd met een grootte van 1280 x 720 pixels. Alle afbeeldingen moeten in . PNG-indeling.
- **Video's** (optioneel): Voeg koppelingen toe aan video's die uw aanbieding weergeven. Je links naar YouTube- en/of Vimeo-video's gebruiken, die samen met je aanbieding aan klanten worden getoond. U moet ook een miniatuurafbeelding van de video invoeren, met een formaat tot 1280 x 720 pixels in PNG-indeling. U maximaal vier video's per aanbieding weergeven.

Vergeet niet op te **slaan** voordat u naar de volgende sectie gaat.

>[!Note]
>Als u een probleem hebt met het uploaden van https://upload.xboxlive.com bestanden, controleert u of uw lokale netwerk de service die door het Partnercentrum wordt gebruikt, niet blokkeert.

#### <a name="additional-marketplace-listing-resources"></a>Aanvullende bronnen voor marktplaatsvermeldingen

- [Aanbevolen procedures voor aanbiedingen voor marktplaatsaanbiedingen](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="preview"></a>Preview

Op het tabblad **Voorbeeld** u een beperkte **preview-doelgroep** definiëren voor het vrijgeven van uw aanbieding voordat u uw aanbieding live publiceert aan de bredere doelgroep.

> [!IMPORTANT]
> Nadat je je aanbieding hebt gecontroleerd in Preview, selecteer **je Live gaan,** zodat je aanbieding live kan worden gepubliceerd voor het publiek op de marktplaats.

- **Een voorbeelddoelgroep definiëren: voeg één AAD/MSA-accounte-mail per regel toe, samen met een optionele beschrijving.**

Voeg maximaal 10 e-mailadressen handmatig toe, of 20 als u een CSV-bestand uploadt voor bestaande Microsoft-account (MSA) of Azure Active Directory-accounts om uw aanbieding te valideren voordat u live publiceert. Door deze accounts toe te voegen, definieert u een doelgroep die toegang krijgt tot uw aanbieding voordat deze wordt gepubliceerd op de marktplaats(en). Als je aanbieding al live is, kun je nog steeds een preview-doelgroep definiëren voor het testen van wijzigingen of updates van je aanbieding.

> [!NOTE]
> De preview-doelgroep verschilt van een privépubliek. Een preview-doelgroep krijgt toegang tot uw aanbieding _voordat_ deze live in de marketplaces wordt gepubliceerd. U er ook voor kiezen om een plan te maken en het alleen beschikbaar te maken voor een privépubliek. Op het tabblad **Lijst met abonnement** u een privédoelgroep definiëren met het selectievakje Dit is een **privéabonnement.** U vervolgens een privédoelgroep van maximaal 20.000 klanten definiëren met azure tenant-id's.

## <a name="technical-configuration"></a>Technische configuratie

Op het tabblad **Technische configuratie** worden de technische details (URL-pad, webhook, tenant-id en app-id) gedefinieerd die worden gebruikt om verbinding te maken met uw aanbieding. Deze verbinding stelt ons in staat om uw aanbod voor de eindklant in te richten als ze ervoor kiezen om het te verwerven. Diagrammen die het gebruik van de verzamelde velden beschrijven, zijn beschikbaar in documentatie voor [SaaS-fulfillment-API's.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2)

- **URL op bestemmingspagina** (vereist): definieer de URL van de site waarop klanten zullen landen nadat ze uw aanbieding van de marktplaats hebben ontvangen. Deze URL is het eindpunt dat een token ontvangt wanneer een klant naar de pagina wordt doorgestuurd. Dat token kan worden ingewisseld voor het inrichten van gegevens met behulp van resolve in de fulfillment API's. Deze gegevens en alle andere die u verzamelt, kunnen worden gebruikt als onderdeel van een klantinteractieve webpagina die is ingebouwd in uw ervaring om de registratie te voltooien en hun aankoop te activeren.

- **Verbinding webhook** (vereist): Voor alle asynchrone gebeurtenissen die Microsoft u namens de klant moet sturen (bijvoorbeeld: SaaS-abonnement is ongeldig geworden), vereisen we dat u een webhook voor verbinding aanbiedt. Als u nog geen webhook-systeem hebt, is de eenvoudigste configuratie om een HTTP Endpoint Logic App te hebben die luistert naar gebeurtenissen\/die erop worden geplaatst en deze vervolgens op de juiste manier behandelen (bijvoorbeeld https: /prod-1westus.logic.azure.com:443/work). Zie [Werkstromen bellen, activeren of nesten met HTTP-eindpunten in logische apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint)voor meer informatie.

- **Azure AD-tenant-id** (vereist): in azure-portal moeten we [een Azure Active Directory-app (AD) maken,](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) zodat we de verbinding tussen onze twee services achter een geverifieerde communicatie kunnen valideren. Als u de [tenant-id wilt](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)vinden, gaat u naar uw Azure Active Directory en selecteert u **Eigenschappen**en zoekt u vervolgens naar het vermelde **directory-id-nummer** (bijvoorbeeld 50c464d3-4930-494c-963c-1e951d15360e).

- **Azure AD-app-id** (vereist): U hebt ook uw [toepassings-id](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) en een verificatiesleutel nodig. Als u deze waarden wilt krijgen, gaat u naar uw Azure Active Directory en selecteert u **App-registraties**en zoekt u vervolgens naar het vermelde **toepassings-id-nummer** (bijvoorbeeld 50c464d3-4930-494c-963c-1e951d15360e). Als u de verificatiesleutel wilt vinden, gaat u naar **Instellingen** en selecteert u **Toetsen**. U moet een beschrijving en duur geven en krijgt dan een getalwaarde.

>[!Note]
>De Azure-toepassings-id is gekoppeld aan uw uitgever-id, dus zorg ervoor dat dezelfde toepassings-id wordt gebruikt in al uw aanbiedingen.

## <a name="plan-overview"></a>Overzicht van plannen

Met het tabblad **Overzicht plannen** u binnen dezelfde aanbieding verschillende abonnementsopties bieden. Deze plannen (soms sku's genoemd) kunnen verschillen in termen van versie, inkomsten genereren of serviceniveaus. U moet ten minste één abonnement instellen om uw aanbieding op de markt te kunnen verkopen.

Zodra u is gemaakt, ziet u uw plannamen, id's, prijsmodellen, beschikbaarheid (openbaar of privé), de huidige publicatiestatus en alle beschikbare acties.

**Acties** die beschikbaar zijn in het **planoverzicht** variëren afhankelijk van de huidige status van uw abonnement en kunnen het volgende omvatten:

- Als de planstatus **Concept** is - Concept verwijderen
- Als de planstatus **Live** is - Verkoopplan stoppen of privédoelgroepen synchroniseren

**Nieuw abonnement maken** (minimaal één abonnement voor degenen die ervoor kiezen om via Microsoft te verkopen)

- **Plan-id:** Maak een unieke plan-ID voor elk plan in deze aanbieding. Deze id is zichtbaar voor klanten in de product-URL en Azure Resource Manager-sjablonen (indien van toepassing). Gebruik alleen kleine letters, alfanumerieke tekens, streepjes of underscores. Voor deze plan-ID zijn maximaal 50 tekens toegestaan. De id kan niet worden gewijzigd na het selecteren van maken.
- **Naam van het plan:** Klanten zien deze naam wanneer ze beslissen welk plan ze binnen uw aanbieding willen selecteren. Maak een unieke aanbiedingsnaam voor elk abonnement in deze aanbieding. De naam van het abonnement wordt gebruikt om softwareplannen te onderscheiden die deel kunnen uitmaken van dezelfde aanbieding (bijvoorbeeld Naam aanbieden: Windows Server; abonnementen: Windows Server 2016, Windows Server 2019).

### <a name="plan-listing"></a>Aanbieding plannen

Op het tabblad **lijstmetjes** wordt de talen (en markten) weergegeven waar uw abonnement beschikbaar is, momenteel is Engels (Verenigde Staten) de enige beschikbare locatie. Bovendien geeft deze pagina de status weer van de taalspecifieke vermelding en de datum/tijd die is toegevoegd. U moet de plaatsdetails (aanbiedingsnaam, beschrijving, onderzoekstermijnen, enz.) voor elke taal/markt bepalen.

#### <a name="plan-listing-details"></a>Details van aanbieding plannen

Als u een van de plantalen selecteert, worden de gegevens van de **abonnementsvermelding** weergegeven, waaronder **Naam** en **beschrijving.**

- **Naam:** Vooraf ingevuld op basis van uw voorbeeld **Nieuwe abonnement** entry en wordt weergegeven als de titel van uw aanbieding "Software plan" weergegeven in de markt.
- **Beschrijving:** Deze beschrijving is een kans om uit te leggen wat dit softwareplan uniek maakt en eventuele verschillen met andere softwareplannen binnen uw aanbod. Kan maximaal 500 tekens bevatten.

Selecteer **Opslaan** nadat u deze velden hebt voltooid.

#### <a name="plan-pricing-and-availability"></a>Prijsen en beschikbaarheid plannen

Op het tabblad **Prijzen en beschikbaarheid** u de markten configureren waarin dit abonnement beschikbaar is, het gewenste verdienmodel, de prijs en de factureringstermijn. Daarnaast u aangeven of u het plan zichtbaar wilt maken voor iedereen of alleen voor specifieke klanten (een privépubliek).

##### <a name="enabling-free-trials"></a>Gratis proefversies inschakelen

SaaS biedt via de commerciële marktplaats u een gratis proefperiode van een maand bieden bij de verkoop via Microsoft. Voor alle factureringsmodellen en -voorwaarden, met uitzondering van abonnementen met een datameter, worden gratis proefversies ondersteund. Deze optie stelt klanten in staat om een lage drempel voor toegang te hebben tot een maand gratis toegang.  Als u ervoor kiest om een gratis proefversie in te schakelen voor abonnementen binnen uw aanbieding, kan de klant niet voor het einde van de eerste periode van één maand converteren naar een betaald abonnement.  Gedurende deze periode kunnen klanten die uw aanbieding kopen, een van de ondersteunde abonnementen uitproberen die de gratis proefperiode hebben ingeschakeld en converteren tussen hen.  De conversie naar een betaald abonnement gebeurt automatisch aan het einde van de looptijd.

>[!Note]
>Als de klant ervoor kiest om te converteren naar een abonnement zonder gratis proefversies, zal de conversie plaatsvinden, maar de gratis proefperiode gaat onmiddellijk verloren.  Ook, zodra een klant begint te betalen voor een abonnement, kunnen ze niet langer gratis proefperiode op hetzelfde abonnement weer, zelfs als ze converteren naar een SKU die gratis proeven ondersteunt.

De mogelijkheid om een gratis proefversie te configureren is beschikbaar voor elk abonnement in uw aanbieding. Navigeer naar de prijzen en beschikbaarheid voor elke aanbieding en schakel het selectievakje in om een proefperiode van één maand toe te staan.

![Een maand gratis proefcontrolebox](./media/free-trial-enable.png)

>[!Note]
>Zodra uw transactable aanbieding met een vrije proef is gepubliceerd, kan het niet voor dat plan worden onbruikbaar gemaakt. Zorg ervoor dat deze instelling correct is voor de eerste publicatie om te voorkomen dat u het plan opnieuw moet maken.

Als u informatie wilt verkrijgen over klantabonnementen die momenteel `isFreeTrial`deelnemen aan een gratis proefperiode, gebruikt u de nieuwe API-eigenschap , die wordt gemarkeerd als waar of onwaar. Zie de [SaaS Get Subscription API](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2#get-subscription)voor meer informatie.

>[!Note]
>Gratis proefversies worden niet ondersteund voor plannen die gebruikmaken van de marketplace-meetservice.

#### <a name="markets"></a>Markten

- **Markten bewerken** (optioneel)

Elk plan moet beschikbaar zijn in ten minste één markt. Schakel het selectievakje in voor elke marktlocatie waar u dit abonnement beschikbaar wilt maken. Een zoekvak en knop voor het selecteren van 'Tax Remitted'-landen, waarin Microsoft namens u btw overdraagt, zijn opgenomen om u te helpen.

Als u al prijzen voor uw abonnement in Amerikaanse dollars (USD) hebt ingesteld en een andere marktlocatie toevoegt, wordt de prijs voor de nieuwe markt berekend op basis van de huidige wisselkoersen. Bekijk de prijs voor elke markt voordat u publiceert. Prijzen kunnen worden beoordeeld met behulp van de link 'Exportprijzen (xlsx)' na het opslaan van uw wijzigingen.

#### <a name="pricing"></a>Prijzen

- **Prijsmodel**: Vast tarief of Seat based

**Vast tarief:** Maak toegang tot uw aanbieding mogelijk met één maandelijkse of jaarlijkse prijs vaste prijs. Dit wordt soms aangeduid als site-gebaseerde prijzen. Met dit prijsmodel u optioneel plannen met een gemeten metingsservice definiëren om klanten in rekening te brengen volgens niet-standaardeenheden.  Zie facturering met [datametermeting met datameting met behulp van de marketplace-meetservice](./saas-metered-billing.md)voor meer informatie over facturering met datameter.

**Per gebruiker:** Toegang tot uw aanbieding inschakelen met de prijs op basis van het aantal gebruikers dat toegang heeft tot de aanbieding of zitplaatsen bezet. Met dit op gebruikers gebaseerde model u het minimum en het maximum aantal gebruikers instellen dat is toegestaan op basis van de prijs. Op deze manier kunnen verschillende prijspunten worden geconfigureerd op basis van het aantal gebruikers door meerdere abonnementen te configureren.  Deze velden zijn optioneel. Als het niet geselecteerd blijft, wordt het aantal gebruikers geïnterpreteerd als het niet hebben van een limiet (min van 1 en max van zoveel als het systeem kan ondersteunen). Deze velden kunnen worden bewerkt als onderdeel van een update van uw abonnement.

Na publicatie kan de keuze voor factureringsprijsmodellen niet worden gewijzigd. Bovendien moeten alle abonnementen voor hetzelfde aanbod hetzelfde prijsmodel hebben.

- **Factureringstermijn**: Maandelijks of jaarlijks

Selecteer de frequentie die klanten de vermelde prijs moeten betalen. Ten minste één maandelijkse of jaarlijkse prijs moet worden opgegeven, of beide opties kunnen beschikbaar worden gesteld aan klanten.

- **Prijs**: USD per maand of USD per jaar

Prijzen in USD (USD = Amerikaanse dollar) worden omgerekend in de lokale valuta van alle geselecteerde markten met behulp van de huidige wisselkoersen wanneer opgeslagen. Valideer deze prijzen voordat u publiceert door de prijsspreadsheet te exporteren en de prijs in elke markt te bekijken. Als u aangepaste prijzen in een afzonderlijke markt wilt instellen, wijzigt en importeert u de prijsspreadsheet. U bent verantwoordelijk voor het valideren van deze prijzen en bent eigenaar van deze instellingen.
*\*U moet eerst uw prijswijzigingen opslaan om het exporteren van prijsgegevens mogelijk te maken.*

Controleer uw prijzen zorgvuldig voordat u publiceert, omdat er enkele beperkingen zijn op wat kan veranderen nadat een plan is gepubliceerd:

- Zodra een plan is gepubliceerd, kan het prijsmodel niet meer worden gewijzigd.
- Zodra een factureringstermijn voor een abonnement is gepubliceerd, kan deze niet later worden verwijderd.
- Zodra een prijs voor een markt in uw plan wordt gepubliceerd, kan deze later niet meer worden gewijzigd.

### <a name="plan-audience"></a>Doelgroep plannen

Je hebt de optie om elk abonnement te configureren om zichtbaar te zijn voor iedereen of voor alleen een specifieke doelgroep van je keuze. U lidmaatschap in deze beperkte doelgroep toewijzen met behulp van Azure AD-tenant-id's.

#### <a name="privacy"></a>Privacy

- **Dit is een privéabonnement** (optioneel selectievakje)

Schakel dit selectievakje in om uw abonnement privé en alleen zichtbaar te maken voor het beperkte publiek van uw keuze. Zodra je als privéplan is gepubliceerd, kun je het publiek bijwerken of ervoor kiezen om het abonnement voor iedereen beschikbaar te maken. Zodra een plan voor iedereen zichtbaar is, moet het voor iedereen zichtbaar blijven. (Het plan kan niet opnieuw als privé-abonnement worden geconfigureerd).

- **Beperkt publiek (tenant---id's)**

Wijs de doelgroep toe die toegang heeft tot dit privéplan. Toegang wordt toegewezen met behulp van tenant-id's met de optie om een beschrijving van elke toegewezen tenant-id op te nemen. Er kunnen maximaal 10 tenant-id's worden toegevoegd of 20.000 tenant-id's als u een CSV-spreadsheetbestand importeert.

Een tenant is een weergave van een organisatie, met een ID die wordt weergegeven als een GUID (Globally Unique Identifier, een 128-bits geheel getal dat wordt gebruikt om resources te identificeren). Het is een speciaal exemplaar van Azure AD dat een organisatie of app-ontwikkelaar ontvangt wanneer de organisatie of app-ontwikkelaar een relatie met Microsoft maakt, bijvoorbeeld wanneer hij zich aanmeldt voor Azure, Microsoft Intune of Microsoft 365. Elke Azure AD-tenant is uniek en werkt afzonderlijk van andere Azure AD-tenants. Om te controleren of u al een tenant hebt, meld u zich aan bij Azure Portal met het account dat u wilt gebruiken voor het beheren van uw toepassing. Als u een tenant hebt, wordt u automatisch aangemeld en ziet u de naam van de tenant direct onder de accountnaam. Houd de muisaanwijzer op uw accountnaam rechtsboven in Azure Portal om uw naam, e-mailadres, directory en tenant-id (een GUID), en uw domein te zien. Als uw account is gekoppeld aan meerdere tenants, kunt u de accountnaam selecteren om een menu te openen waarmee u kunt schakelen tussen de tenants. Elke tenant heeft zijn eigen tenant-id. U ook de tenant-id van uw organisatie [https://www.whatismytenantid.com](https://www.whatismytenantid.com)opzoeken met behulp van een URL van de domeinnaam op:.

Hoewel SaaS-aanbiedingen tenant--cd's gebruiken om een privédoelgroep te definiëren, kunnen andere aanbiedingstypen azure-abonnements-iD's gebruiken (die ook worden weergegeven als GUID's).

> [!NOTE]
> Het privépubliek (of beperkt publiek) verschilt van een voorvertoningspubliek. Op het tabblad **[Voorbeeld](#preview)** u een voorbeelddoelgroep definiëren. Een preview-publiek krijgt toegang tot uw aanbieding *voordat* de aanbieding live op de markt wordt gepubliceerd. Hoewel de aanduiding voor privédoelgroepen alleen van toepassing is op een specifiek plan, kan de preview-doelgroep alle plannen bekijken (privé of niet), maar alleen tijdens de beperkte voorbeeldperiode terwijl het plan wordt getest en gevalideerd.

## <a name="example-list-of-plans-within-a-marketplace-offer"></a>Voorbeeldlijst met plannen binnen een marktplaatsaanbieding

![Voorbeeld aanbieding van marktplaatsplan met notities](./media/marketplace-plan.svg)

## <a name="cloud-solution-provider-csp-reseller-audience"></a>Cloud Solution Provider (CSP) Reseller Audience

Door ervoor te kiezen om uw aanbod beschikbaar te maken in het CSP-programma kunnen Cloud Solution Providers uw product verkopen als onderdeel van een gebundelde oplossing aan hun klanten. Zie [Cloud Solution Providers](https://go.microsoft.com/fwlink/?linkid=2111109)voor meer informatie.

## <a name="publish"></a>Publiceren

Zodra u alle vereiste gedeelten van de aanbieding hebt voltooid, selecteert u **publiceren** in de rechterbovenhoek van de portal. U wordt doorgestuurd naar de **pagina Beoordeling en publicatie.**

### <a name="submit-offer-to-preview"></a>Voorstel indienen om een voorbeeld te bekijken

Als dit de eerste keer is dat u deze aanbieding publiceert, u het als volgt doen:

- Zie de voltooiingsstatus voor elk gedeelte van de aanbieding.
    - *Niet gestart* - betekent dat de sectie niet is aangeraakt en moet worden voltooid.
    - *Onvolledig* - betekent dat de sectie fouten bevat die moeten worden opgelost of dat er meer informatie moet worden verstrekt. Je moet teruggaan naar de sectie en het bijwerken.
    - *Voltooid* - betekent dat de sectie volledig is, alle vereiste gegevens zijn verstrekt en er geen fouten zijn. Alle onderdelen van de aanbieding moeten volledig zijn voordat u de aanbieding indienen.
- Geef het certificeringsteam testinstructies om ervoor te zorgen dat uw app correct is getest, naast eventuele aanvullende notities die nuttig zijn om uw app te begrijpen.
- Dien de publicatieaanbieding in door **Verzenden te selecteren.** We sturen je een e-mail om je te laten weten wanneer een preview-versie van de aanbieding beschikbaar is om te bekijken en goed te keuren. U moet terugkeren naar partnercentrum en **Go-live** selecteren voor de aanbieding om uw aanbieding aan het publiek te publiceren (of als een privé-aanbieding, aan het particuliere publiek).

## <a name="next-steps"></a>Volgende stappen

- [Een bestaande aanbieding bijwerken in Commerciële Marketplace](./update-existing-offer.md)
