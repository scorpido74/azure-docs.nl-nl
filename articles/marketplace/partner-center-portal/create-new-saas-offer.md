---
title: Een nieuwe SaaS-aanbieding maken voor de micro soft Commercial Marketplace
description: Het maken van een nieuwe SaaS-aanbieding (Software as a Service) voor het aanbieden of verkopen van Microsoft AppSource, Azure Marketplace of via het programma Cloud Solution Provider (CSP) met behulp van het micro soft-programma voor commerciële Marketplace in micro soft partner centrum.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 3393fb6e33cbf80db68c49ac31edb54de35bae64
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/06/2020
ms.locfileid: "85980593"
---
# <a name="create-a-new-saas-offer-in-the-commercial-marketplace"></a>Een nieuwe SaaS-aanbieding maken in de commerciële Marketplace

Als u wilt beginnen met het maken van SaaS-aanbiedingen (Software as a Service) in de commerciële Marketplace, moet u eerst [een partner centrum-account maken](./create-account.md) en het [dash board commerciële Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)openen, met het tabblad **overzicht** geselecteerd.

## <a name="create-a-new-offer"></a>Een nieuwe aanbieding maken

1. Meld u aan bij [Partner Center](https://partner.microsoft.com/dashboard/home).
2. Selecteer in het menu links de optie **commerciële Marketplace**-  >  **overzicht**.
3. Selecteer op de pagina overzicht **+ nieuwe aanbieding**  >  **software als een service**.

   ![Illustreert het navigatie menu.](./media/new-offer-saas.png)

> [!NOTE]
> Nadat een aanbieding is gepubliceerd, worden wijzigingen die in het partner centrum zijn aangebracht, alleen weer gegeven in de winkel wanneer u de aanbieding opnieuw publiceert. Zorg ervoor dat u altijd opnieuw publiceert nadat u wijzigingen hebt aangebracht.

## <a name="new-offer"></a>Nieuwe aanbieding

Voer een **aanbiedings-id**in. Dit is een unieke id voor elke aanbieding in uw account.

- Deze ID is zichtbaar voor klanten in het webadres voor de Marketplace-aanbieding en Azure Resource Manager sjablonen, indien van toepassing.
- Gebruik alleen kleine letters en cijfers. Dit kan afbreek streepjes en onderstrepings tekens bevatten, maar mag niet langer zijn dan 50. Als u hier bijvoorbeeld **test-aanbieding-1** invoert, is het webadres van de aanbieding `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- De aanbiedings-ID kan niet worden gewijzigd nadat u **maken**hebt geselecteerd.

Voer een **alias**voor de aanbieding in. Dit is de naam die wordt gebruikt voor de aanbieding in Partner Center.

- Deze naam wordt niet gebruikt in Marketplace en wijkt af van de naam van de aanbieding en andere waarden die aan klanten worden weer gegeven.
- De aanbiedings alias kan niet worden gewijzigd nadat u **maken**hebt geselecteerd.

<!---
![Offer overview on Partner Center](./media/commercial-marketplace-offer-overview.png)
-->

Selecteer **maken** om de aanbieding te genereren en door te gaan.

## <a name="offer-overview"></a>Overzicht van aanbieding

Bij de **publicatie status** ziet u een visuele weer gave van de stappen die nodig zijn voor het publiceren van deze aanbieding en hoe lang elke stap moet worden voltooid. Onvolledige publicatie stap pictogrammen worden grijs weer gegeven.

Het menu overzicht van de **aanbieding** bevat een lijst met koppelingen voor het uitvoeren van bewerkingen op deze aanbieding. Deze lijst met bewerkingen wordt gewijzigd op basis van de selectie die u voor uw aanbieding maakt.  

- Als de aanbieding een concept is-concept verwijderen
- Als het aanbod een live-stop verkoop aanbod is
- Als de aanbieding in Preview-Live staat
- Als u Publisher-afmelding niet hebt voltooid, kunt u Publiceren annuleren

## <a name="offer-setup"></a>Installatie van aanbieding

Op deze pagina wordt u gevraagd om de volgende informatie op te vragen.

- **Wilt u door micro soft verkopen?** (Ja/Nee)
    - **Ja**, ik wil graag door micro soft verkopen en micro soft host-trans acties voor mijn naam hebben
    - **Nee**, ik wil liever alleen mijn aanbieding via de marketplaces en trans acties afzonderlijk verwerken.

### <a name="sell-through-microsoft"></a>Verkopen via micro soft

Door te verkopen via micro soft biedt betere klant detectie en-overname, kan micro soft Marketplace-trans acties namens u hosten en profiteren van de wereld wijd beschik bare commerce mogelijkheden van micro soft.

#### <a name="saas-offer-requirements"></a>Vereisten voor SaaS-aanbiedingen

Als u SaaS-aanbiedingen (Software as a Service) met commerciële Marketplace op partner centrum wilt weer geven, moet aan de volgende criteria worden voldaan:

- Uw aanbieding moet [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) gebruiken voor identiteits beheer en-verificatie.
- Uw aanbieding moet [SaaS-fulfillment-api's](pc-saas-fulfillment-api-v2.md) gebruiken voor integratie met Azure Marketplace.

#### <a name="saas-pricing-and-billing-options"></a>SaaS-prijzen en facturerings opties

Met SaaS-oplossingen die worden uitgevoerd in het Azure-abonnement van de uitgever, zijn de kosten van de infra structuur waarop de software wordt geïmplementeerd, inbegrepen in de licentie kosten die door klanten worden betaald. Het gebruik van de infra structuur van Azure wordt beheerd en wordt gefactureerd aan u, de partner, direct. De werkelijke gebruiks kosten voor de infra structuur zijn niet zichtbaar voor de klant. Uitgevers moeten gebruiks kosten voor Azure-infra structuur in hun software licentie prijzen bundelen.

SaaS biedt ondersteuning voor maandelijkse of jaarlijkse facturering op basis van een vast bedrag, per gebruiker of verbruiks kosten met behulp van de service voor facturering via data limiet. De commerciële Marketplace van micro soft werkt op een agentuur model, waardoor uitgevers prijzen instellen, micro soft billt klanten en micro soft betaalt de inkomsten aan de uitgever terwijl er kosten in rekening worden gebracht.

Dit is een voor beeld van een uitsplitsing van kosten en uitbetalingen om het agentuur model te demonstreren (alle prijzen zijn alleen bedoeld als voor beeld van de werkelijke kosten):

|**De licentie kosten**|**$100 per maand**|
|:---|:---|
|Kosten voor Azure-gebruik (D1/1-core)|Rechtstreeks aan de uitgever gefactureerd, niet de klant|
|Klant wordt gefactureerd door micro soft|$100,00 per maand (uitgever moet rekening worden gehouden met de kosten voor het ontstaan of door geven van infra structuur in de licentie kosten)|

|**Micro soft-facturen**|**$100 per maand**|
|:---|:---|
|Micro soft betaalt u 80% van uw licentie kosten <br>**Voor gekwalificeerde SaaS-apps betaalt micro soft 90% van uw licentie kosten*|$80,00 per maand <br>*$* 90,00 per maand *|

- In dit voor beeld wordt micro soft billt $100,00 aan de klant voor uw software licentie en betaalt $80,00 de uitgever.

> [!NOTE]
> **Lagere kosten voor Marketplace-service** : voor bepaalde SaaS-aanbiedingen die u hebt gepubliceerd op de commerciële Marketplace, verlaagt micro soft de kosten voor Marketplace-service van 20% (zoals beschreven in de overeenkomst voor micro soft Publisher) tot 10%. Uw aanbieding (en) voor uw aanbieding (en) die u wilt kwalificeren, moeten zijn toegewezen door micro soft als IP-mede-verkoop gereed of IP co-sell priority. Voor het einde van elke kalender maand moet aan de geschiktheid ten minste vijf (5) werk dagen worden voldaan om de lagere kosten voor Marketplace-service voor de maand te ontvangen. De lagere kosten voor Marketplace-service zijn niet van toepassing op Vm's, beheerde apps of andere producten die beschikbaar worden gesteld via de commerciële Marketplace.

### <a name="list-through-microsoft"></a>Lijst via micro soft

Promoot uw bedrijf met micro soft door een Marketplace-vermelding te maken. Als u een lijst wilt maken met alleen uw aanbieding en niet via micro soft, betekent dit dat micro soft niet rechtstreeks deelneemt aan software licentie transacties. Er zijn geen kosten verbonden aan de trans actie en de uitgever houdt 100% van alle software licentie kosten bij die van de klant zijn verzameld. De uitgever is echter verantwoordelijk voor de ondersteuning van alle aspecten van de software licentie transactie, inclusief, maar niet beperkt tot: order verwerking, meting, facturering, facturering, betaling en incasso.

<!-- - **How do you want potential customers to interact with this listing offer?** -->

#### <a name="get-it-now-free"></a>Nu downloaden (gratis)

Bied uw aanbod aan klanten gratis aan door een geldig adres op te geven (te beginnen met *http* of *https*), waar ze een proef versie kunnen krijgen met [één klik op verificatie met behulp van Azure Active Directory (Azure AD)](../marketplace-saas-applications-technical-publishing-guide.md#using-azure-active-directory-to-enable-trials)). Bijvoorbeeld `https://contoso.com/saas-app`.

#### <a name="free-trial-listing"></a>Gratis proef versie (lijst)

Bied uw aanbieding aan klanten aan met een koppeling naar een gratis proef versie door een geldig adres op te geven (te beginnen met *http* of *https*), waar u een proef versie kunt verkrijgen met [één klik op verificatie met behulp van Azure Active Directory (Azure AD)](../marketplace-saas-applications-technical-publishing-guide.md#using-azure-active-directory-to-enable-trials)). Bijvoorbeeld `https://contoso.com/trial/saas-app`. Gratis proef versies van aanbieding worden gemaakt, beheerd en geconfigureerd door uw service en er zijn geen abonnementen die door micro soft worden beheerd.

> [!NOTE]
> De tokens die uw toepassing via uw proef koppeling ontvangt, kunnen alleen worden gebruikt voor het verkrijgen van gebruikers gegevens via Azure AD voor het automatiseren van het maken van accounts in uw app. Micro soft-accounts (MSA) worden niet ondersteund voor verificatie met behulp van dit token.

#### <a name="contact-me"></a>Contact opnemen

Contact gegevens van klanten verzamelen door verbinding te maken met uw CRM-systeem (Customer Relationship Management). De klant wordt gevraagd om toestemming te krijgen om hun gegevens te delen. Deze klant gegevens, samen met de naam van de aanbieding, ID en Marketplace-bron waar ze uw aanbieding vinden, worden verzonden naar het CRM-systeem dat u hebt geconfigureerd. Zie [leads van klanten](#customer-leads)voor meer informatie over het configureren van uw CRM.

#### <a name="example-marketplace-offer-listing"></a>Voorbeeld van een lijst met Marketplace-aanbiedingen

<!-- ![Example marketplace offer listing with notes](./media/marketplace-offer.svg) -->

Hier volgt een voor beeld van hoe informatie over aanbiedingen wordt weer gegeven in Microsoft AppSource:

:::image type="content" source="media/example-appsource-saas.png" alt-text="Illustreert hoe deze aanbieding wordt weer gegeven in Microsoft AppSource.":::

#### <a name="call-out-descriptions"></a>Beschrijvingen van aanroepen

1. Groot logo
2. Categorieën
3. Bedrijfstakken
4. Ondersteunings adres (koppeling)
5. Gebruiksvoorwaarden
6. Privacybeleid
7. Naam van aanbieding
8. Samenvatting
9. Description
10. Scherm afbeeldingen/Video's
11. Documenten

<br>Hier volgt een voor beeld van hoe de aanbiedings gegevens worden weer gegeven in de Azure Portal:

:::image type="content" source="media/example-virtual-machine-container-iot-edge-saas.png" alt-text="Illustreert hoe deze aanbieding wordt weer gegeven in de Azure Portal.":::

#### <a name="call-out-descriptions"></a>Beschrijvingen van aanroepen

1. Titel
2. Beschrijving
3. Handige koppelingen
4. Schermopnamen

## <a name="enable-a-test-drive"></a>Een test drive inschakelen

Een test drive is een fantastische manier om uw aanbieding aan potentiële klanten te laten presen teren door hen de mogelijkheid te bieden om te kopen voordat u aan de slag gaat, wat resulteert in een verhoogde conversie en de generatie van uiterst gekwalificeerde leads. Meer [informatie over test stations](../what-is-test-drive.md).

Als u een test drive voor een bepaalde periode wilt inschakelen, schakelt u het selectie vakje **een test drive inschakelen** in. Als u test drive uit uw aanbieding wilt verwijderen, schakelt u dit selectie vakje uit.

Zie [Test uw aanbieding in de commerciële Marketplace](test-drive.md)voor meer informatie.

### <a name="test-drive-resources"></a>Bronnen van schijven testen

- [Wat is een test drive?](../what-is-test-drive.md)
- [Technische best practices](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Overzicht](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (PDF; Controleer of de pop-upblokkering is uitgeschakeld)

### <a name="customer-leads"></a>Leads van klanten

[!INCLUDE [Connect lead management](./includes/connect-lead-management-a.md)]

#### <a name="additional-lead-management-resources"></a>Aanvullende resources voor lead beheer
- [Veelgestelde vragen over Lead beheer](../lead-management-for-cloud-marketplace.md#frequently-asked-questions))
- [Veelvoorkomende fouten van de lead configuratie](../lead-management-for-cloud-marketplace.md#publishing-config-errors))
- [Overzicht Lead beheer één pager](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)

Selecteer **concept opslaan** voordat u doorgaat.

## <a name="properties"></a>Eigenschappen

Op deze pagina wordt u gevraagd om de categorieën en industrieën te definiëren die worden gebruikt voor het groeperen van uw aanbieding op Marketplace, de juridische contracten die uw aanbieding ondersteunen en uw app-versie.

### <a name="category"></a>Categorie

Uw aanbieding wordt gepubliceerd op AppSource of Azure Marketplace, afhankelijk van de transactie mogelijkheden die zijn gekoppeld aan uw aanbieding en de selectie van uw categorie. Zie [Microsoft AppSource en Azure Marketplace vergelijken](../comparing-appsource-azure-marketplace.md) voor meer informatie. Selecteer categorieën en subcategorieën die het beste aansluiten bij uw aanbieding en uw beoogde doel groep. Selecteer:

- Ten minste één en Maxi maal twee categorieën, met inbegrip van een primaire en secundaire categorie (optioneel).
- Maxi maal twee subcategorieën voor elke primaire en/of secundaire categorie. Als er geen subcategorie van toepassing is op uw aanbieding, selecteert u **niet van toepassing**.

Bekijk de volledige lijst met categorieën en subcategorieën die van toepassing zijn op elke winkel in [Aanbevolen procedures voor aanbiedingen](../gtm-offer-listing-best-practices.md).

### <a name="industries"></a>Bedrijfstakken

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

Branche selectie geldt alleen voor aanbiedingen die zijn gepubliceerd op AppSource.

### <a name="app-version"></a>App-versie

Dit veld is optioneel en wordt gebruikt in de AppSource Marketplace om het versie nummer van uw aanbieding te identificeren.

### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Standard-contract voor micro soft Commercial Marketplace

Micro soft biedt een standaard contract sjabloon.

- **Het standaard contract voor de micro soft Commercial Marketplace gebruiken?**

Micro soft biedt een standaard contract voor de micro soft Commercial Marketplace om trans acties op Marketplace te vergemakkelijken, om het aankoop proces voor klanten te vereenvoudigen en de juridische complexiteit voor software leveranciers te reduceren. In plaats van aangepaste voor waarden te gebruiken, kunnen uitgevers van commerciële markt plaatsen hun software aanbieden onder het Standard-contract, wat klanten alleen hoeven te bevestigen en één keer te accepteren. Het Standard-contract kan worden gevonden op https://go.microsoft.com/fwlink/?linkid=2041178 .

U kunt het standaard contract gebruiken in plaats van uw eigen aangepaste voor waarden op te geven door het selectie vakje het standaard contract voor commerciële Marketplace gebruiken in te scha kelen.

![Het selectie vakje standaard contract gebruiken](./media/use-standard-contract.png)

> [!NOTE]
> Zodra u een aanbieding publiceert met het standaard contract voor micro soft Commercial Marketplace, kunt u uw eigen aangepaste voor waarden niet gebruiken. Het is een ' or '-scenario. U kunt uw oplossing aanbieden onder het standaard contract **of** uw eigen voor waarden. Als u de voor waarden van het standaard contract wilt wijzigen, kunt u dit doen via de standaard wijzigingen in contracten.

#### <a name="standard-contract-amendments"></a>Wijzigingen in het standaard contract

Met de aanpassingen van het standaard contract kunnen uitgevers de standaard contract voorwaarden voor eenvoud selecteren en de voor waarden voor hun product of bedrijf aanpassen. Klanten hoeven alleen de wijzigingen in het contract door te nemen als ze het micro soft Standard-contract al hebben gecontroleerd en geaccepteerd.

Er zijn twee soorten wijzigingen beschikbaar voor uitgevers van commerciële Marketplace:

- Universele wijzigingen: deze wijzigingen worden universeel toegepast op het Standard-contract voor alle klanten. Universele wijzigingen worden weer gegeven aan elke klant van de aanbieding in de inkoop stroom. Klanten moeten akkoord gaan met de voor waarden van het standaard contract en de wijziging voordat ze uw aanbieding kunnen gebruiken.
- Aangepaste wijzigingen: deze wijzigingen zijn speciale wijzigingen in het standaard contract die alleen voor specifieke klanten zijn gericht via Azure-Tenant-Id's. Uitgevers kunnen de Tenant kiezen die ze willen instellen. Alleen klanten van de Tenant worden weer gegeven met de aangepaste wijzigings voorwaarden in de inkoop stroom van de aanbieding.  Klanten moeten akkoord gaan met de voor waarden van het standaard contract en de wijziging (en) voordat ze uw aanbieding kunnen gebruiken.

>[!NOTE]
> Deze twee typen wijzigingen worden boven op elkaar gestapeld. Klanten die zijn gericht op aangepaste wijzigingen, krijgen ook de universele wijziging in het standaard contract tijdens de aankoop.

Voor **waarden voor universele wijzigingen in het Standard-contract voor de commerciële Marketplace van micro soft** – Voer de voor waarden voor universele wijzigingen in dit vak in. U kunt één universele wijziging per aanbieding opgeven. U kunt een onbeperkt aantal tekens in dit vak invoeren. Deze voor waarden worden weer gegeven aan klanten in AppSource, Azure Marketplace en/of de Azure Portal tijdens de detectie-en aankoop stroom.

Voor **waarden voor aangepaste aanpassingen aan het Standard-contract voor de commerciële Marketplace van micro soft** – begin door **aangepaste wijzigings voorwaarden toevoegen**te selecteren. U kunt Maxi maal 10 aangepaste voor waarden per aanbieding opgeven.

- **Voor waarden voor aangepaste aanpassingen** : Voer uw aangepaste wijzigings voorwaarden in het vak Aangepaste wijzigings voorwaarden in. U kunt een onbeperkt aantal tekens in dit vak invoeren. Alleen klanten van de Tenant-Id's die u opgeeft voor deze aangepaste voor waarden worden weer gegeven met de aangepaste wijzigings voorwaarden in de inkoop stroom van de aanbieding in de Azure Portal.  
- **Tenant-id's** (vereist): elke aangepaste wijziging kan worden gericht op Maxi maal 20 Tenant-id's. Als u een aangepaste wijziging toevoegt, moet u ten minste één Tenant-ID opgeven. De Tenant-ID identificeert uw klant in Azure. U kunt uw klant vragen voor deze ID en ze kunnen deze vinden door te navigeren naar portal.azure.com > Azure Active Directory > eigenschappen. De waarde van de Directory-ID is de Tenant-ID (bijvoorbeeld 50c464d3-4930-494c-963c-1e951d15360e). U kunt ook de Tenant-ID van de organisatie zoeken met behulp van de domein naam-URL op [Wat is mijn Microsoft Azure en Office 365-Tenant-id?](https://www.whatismytenantid.com)
- **Beschrijving** (optioneel): Geef eventueel een beschrijvende beschrijving op voor de Tenant-id waarmee u de klant kunt identificeren die u wilt richten op de wijziging.

#### <a name="terms-and-conditions"></a>Voorwaarden

Als u uw eigen aangepaste voor waarden wilt opgeven, kunt u ervoor kiezen deze in het veld voor waarden in te voeren. U kunt Maxi maal 10.000 tekens tekst in dit veld invoeren. Als voor de voor waarden een langere beschrijving is vereist, voert u in dit veld een koppeling naar een enkele URL in waarin u de voor waarden kunt vinden. Het wordt weer gegeven voor klanten als een actieve koppeling.

Klanten moeten deze voor waarden accepteren voordat ze uw aanbieding kunnen proberen.

Selecteer **concept opslaan** voordat u doorgaat.

## <a name="offer-listing"></a>Aanbieding weer geven

Op deze pagina worden de talen (en markten) weer gegeven waar uw aanbieding beschikbaar is. in het Engels (Verenigde Staten) is momenteel alleen de locatie beschikbaar. Daarnaast wordt op deze pagina de status weer gegeven van de taalspecifieke vermelding en de datum/tijd waarop deze is toegevoegd. U moet de Marketplace-Details (naam, beschrijving, zoek termen, enz.) definiëren voor elke taal/markt.

> [!NOTE]
> De inhoud van het aanbiedings aanbod (zoals beschrijving van aanbieding, documenten, scherm afbeeldingen, gebruiks voorwaarden en privacybeleid) is niet in het Engels vereist, zolang de beschrijving van het aanbod begint met de woord groep ' deze toepassing is alleen beschikbaar in [niet-Engelse taal] '. Het is ook acceptabel om een *nuttige koppelings-URL* te bieden om inhoud te bieden in een andere taal dan de versie die wordt gebruikt in de inhoud van de aanbieding.

### <a name="offer-listings"></a>Aanbiedings vermeldingen

Geef de details op die in Marketplace moeten worden weer gegeven, inclusief beschrijvingen van uw aanbieding en marketing-assets.

- **Naam** (vereist): de naam die hier is opgegeven, wordt weer gegeven als de titel van uw aanbieding op Marketplace ('s) die u hebt gekozen. De naam wordt vooraf ingevuld op basis van uw vorige **nieuwe aanbieding** . De naam kan worden aangemerkt. Het mag geen emojis bevatten (tenzij ze de symbolen van het handels merk en copyright zijn) en moet beperkt zijn tot 50 tekens.
- **Samen vatting** (vereist): Geef een korte beschrijving van uw aanbieding die moet worden gebruikt in de zoek resultaten voor Marketplace-aanbiedingen. In dit veld kunnen Maxi maal 100 tekens tekst worden ingevoerd.
- **Beschrijving** (vereist): Geef een beschrijving op van uw aanbieding die moet worden weer gegeven in het overzicht van Marketplace-lijst (en). Overweeg het opnemen van een toegevoegde waarde, belang rijke voor delen, categorie-of branche koppelingen, in-app aankoop kansen, eventueel vereiste informatie en een koppeling voor meer informatie. U kunt Maxi maal 3.000 tekens in dit veld invoeren, inclusief de opmaak van de tekst. Zie [een fantastische app-beschrijving schrijven](/windows/uwp/publish/write-a-great-app-description)voor aanvullende tips.
- **Zoek trefwoorden** : Voer Maxi maal drie Zoek trefwoorden in die klanten kunnen gebruiken om uw aanbieding te vinden in Marketplace ('s).
- **Aan de slag-instructies** (vereist): in dit gedeelte wordt uitgelegd hoe u uw app kunt configureren en gebruiken voor potentiële klanten.  Deze Snelstartgids kan koppelingen bevatten naar gedetailleerde online documentatie. In dit veld kunnen Maxi maal 3.000 tekens tekst worden ingevoerd.

#### <a name="description"></a>Description

Dit veld is vereist.

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

#### <a name="links"></a>Koppelingen

- **Privacybeleid** (vereist): koppeling naar het privacybeleid van uw organisatie. U bent verantwoordelijk om ervoor te zorgen dat uw app voldoet aan de wetten en voor schriften van de privacy en voor het bieden van een geldig privacybeleid
- **Software** voor het CSP-programma (optioneel): Geef een koppeling op naar marketing materiaal als u ervoor kiest uw aanbieding uit te breiden naar het [CSP-programma (Cloud Solution Provider)](../cloud-solution-providers.md) . CSP breidt uw aanbieding uit naar een breder scala aan gekwalificeerde klanten door CSP-partners in staat te stellen om uw aanbieding te bundelen, te verhandelen en te verkopen. Deze wederverkopers moeten toegang hebben tot materialen om uw aanbieding in de handel te brengen. Zie [Go-to-Market Services](https://partner.microsoft.com/reach-customers/gtm)(Engelstalig) voor meer informatie.
- **Nuttige koppelingen** (optioneel): optionele aanvullende online documenten over uw app of gerelateerde services die worden vermeld door een **titel** en **URL**op te geven. Voeg extra nuttige koppelingen toe door op **+ een URL toevoegen**te klikken.

#### <a name="contact-information"></a>Contactgegevens

- **Contact personen** : voor elke contact persoon van de klant geeft u de **naam**, het **telefoon nummer**en het **e-mail** adres van de werk nemer op (deze worden *niet* openbaar weer gegeven). Een **ondersteunings-URL** is vereist voor de **contact persoon van de ondersteunings** groep (dit *wordt* openbaar weer gegeven).

    - **Ondersteunings contact** (vereist) – voor algemene ondersteunings vragen.
    - Technisch **contact** (vereist) – voor technische vragen.
    - **Channel Manager-contact** (vereist): voor wederverkoper-vragen met betrekking tot het CSP-programma.

#### <a name="files-and-images"></a>Bestanden en installatie kopieën

- **Documenten** (vereist): Voeg gerelateerde marketing documenten toe voor uw aanbieding, in PDF-indeling, van ten minste één en Maxi maal drie documenten per aanbieding.
- **Afbeeldingen** (optioneel): er zijn meerdere locaties waar de logo afbeeldingen van uw aanbieding kunnen worden weer gegeven in de Marketplace ('s), zodat de volgende pixel grootten in de PNG-indeling zijn vereist:

    - **Klein** (48 x 48, vereist)
    - **Gemiddeld** (90 x 90, vereist)
    - **Groot** (216 x 216, vereist)
    - **Breed** (255 x 115)

- **Scherm afbeeldingen** (vereist): Voeg een maximum van vijf scherm opnamen toe, met een grootte van 1280 x 720 pixels. Alle installatie kopieën moeten zich in hebben. PNG-indeling.
- **Video's** (optioneel): voeg links toe aan Video's die uw aanbieding aantonen. U kunt links gebruiken naar YouTube-en/of Vimeo-Video's, die samen met uw aanbieding aan klanten worden weer gegeven. U moet ook een miniatuur afbeelding van de video invoeren, met een grootte van 1280 x 720 pixels in PNG-indeling. U kunt Maxi maal vier Video's per aanbieding weer geven.

>[!NOTE]
>Als u een probleem hebt met het uploaden van bestanden, moet u ervoor zorgen dat uw lokale netwerk de door https://upload.xboxlive.com Partner Center gebruikte service niet blokkeert.

#### <a name="additional-marketplace-listing-resources"></a>Aanvullende bronnen voor Marketplace-lijst

- [Best practices voor Marketplace-aanbiedingen](../gtm-offer-listing-best-practices.md)

Selecteer **concept opslaan** voordat u doorgaat.

## <a name="preview-audience"></a>Voor beeld van doel groep

Op deze pagina kunt u een beperkte **Preview-doel groep** definiëren voor het vrijgeven van uw aanbieding voordat u uw aanbieding Live publiceert naar de bredere Marketplace-doel groep.

> [!IMPORTANT]
> Nadat u uw aanbieding in Preview hebt gecontroleerd, selecteert u **Live gaan** zodat uw aanbieding Live kan worden gepubliceerd voor de open bare doel groep van Marketplace.

Voeg per regel een e-mail met één AAD/MSA-account toe, samen met een optionele beschrijving.

Voeg Maxi maal 10 e-mail adressen hand matig toe, of 20 als u een CSV-bestand uploadt voor bestaande micro soft-account (MSA) of Azure Active Directory accounts om te helpen bij het valideren van uw aanbieding voordat u live publiceert. Door deze accounts toe te voegen, definieert u een doel groep waarvoor preview-toegang tot uw aanbieding is toegestaan voordat deze wordt gepubliceerd op Marketplace ('s). Als uw aanbieding al Live is, kunt u nog steeds een preview-doel definiëren voor het testen van eventuele wijzigingen of updates voor uw aanbieding.

> [!NOTE]
> De preview-doel groep wijkt af van een persoonlijke doel groep. Een preview-doel groep is toegang tot uw aanbieding toegestaan _voordat_ Live in de Marketplace wordt gepubliceerd. U kunt er ook voor kiezen om een plan te maken en dit alleen beschikbaar te maken voor een privé doel groep. Op het tabblad **plan vermelding** kunt u een privé-doel groep definiëren met het selectie vakje **Dit is een privé plan** . U kunt vervolgens een persoonlijke doel groep van Maxi maal 20.000 klanten definiëren met behulp van Azure-Tenant-Id's.

Selecteer **concept opslaan** voordat u doorgaat.

## <a name="technical-configuration"></a>Technische configuratie

Het tabblad **technische configuratie** definieert de technische details die door Marketplace worden gebruikt om te communiceren met uw SaaS-service. Met deze verbinding kan ons uw aanbieding voor de eind klant inrichten als ze ervoor kiezen om deze te verkrijgen en te beheren. 

>[!Note]
>U moet integratie met [SaaS-fulfillment-api's](./pc-saas-fulfillment-api-v2.md) implementeren voordat u deze details in de details van de aanbieding configureert.

Diagrammen en gedetailleerde uitleg die het gebruik van de verzamelde velden beschrijven, zijn beschikbaar in de documentatie voor [de api's](./pc-saas-fulfillment-api-v2.md).

- **URL van de landings pagina** (vereist): DEFINIEER de URL van de SaaS-site (bijvoorbeeld: `https://contoso.com/signup` ) waarmee eind klanten hun aanbieding van de Marketplace kunnen binnenhalen en het configuratie proces vanuit het zojuist gemaakte SaaS-abonnement kunnen activeren.  Deze URL wordt aangeroepen met de para meter voor het kopen van id-tokens voor Marketplace waarmee de specifieke SaaS-aankoop van de eind klant wordt geïdentificeerd.  U moet dit token omruilen voor de bijbehorende details van het SaaS-abonnement met behulp van de API voor [omzetten](./pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription) .  Deze details en andere personen die u wilt verzamelen, moeten worden gebruikt als onderdeel van een klant-interactieve webpagina die in uw ervaring is gebouwd om de eind klant te registreren en hun aankoop te activeren.  Op deze pagina moet de gebruiker zich aanmelden met één klik om te verifiëren met behulp van Azure Active Directory (Azure AD). <br> <br> Deze URL met Marketplace-aankoop identificatie token para meter wordt ook aangeroepen wanneer de eind klant beheerde SaaS-ervaring vanuit Azure Portal of M365-beheer centrum start. U moet beide stromen afhandelen wanneer het token de eerste keer wordt ingevoerd na aankoop voor nieuwe klanten en wanneer het wordt verschaft voor een bestaande klant die zijn SaaS beheert. <br> <br> De landings pagina die u hier configureert, moet 24/7 zijn. Dit is de enige manier waarop u wordt gewaarschuwd over nieuwe aankopen van uw SaaS-aanbiedingen op Marketplace of configuratie aanvragen van een actief abonnement op een aanbieding.

- **Verbindings-webhook** (vereist): voor alle asynchrone gebeurtenissen die micro soft naar u moet sturen (bijvoorbeeld SaaS-abonnement is geannuleerd), moet u een URL voor de verbindings-webhook opgeven. Deze URL wordt gebeld om u op de hoogte te stellen van de gebeurtenis. <br> <br> De webhook die u opgeeft, moet 24/7 zijn, omdat dit de enige manier is waarop u wordt gewaarschuwd over updates van de SaaS-abonnementen van uw klanten die zijn gekocht via Marketplace.  Als u nog geen webhooksysteem hebt, is de eenvoudigste configuratie een HTTP-eindpunt logische app waarmee wordt geluisterd naar gebeurtenissen die erop worden geplaatst en deze vervolgens op de juiste wijze afhandelen (bijvoorbeeld `https://prod-1westus.logic.azure.com:443/work` ). Zie [werk stromen aanroepen, activeren of nesten met HTTP-eind punten in Logic apps](../../logic-apps/logic-apps-http-endpoint.md)voor meer informatie.

- **Azure AD-Tenant-id** (vereist): in het Azure Portal moet u [een Azure Active Directory (AD)-app maken](../../active-directory/develop/howto-create-service-principal-portal.md) zodat de verbinding tussen de twee services achter een geverifieerde communicatie kan worden gevalideerd. Als u wilt zoeken naar de [Tenant-id](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)), gaat u naar uw Azure Active Directory en selecteert u **Eigenschappen**. vervolgens zoekt u naar de weer gegeven **Directory-id** (zoals 50c464d3-4930-494c-963c-1e951d15360e).

- **Azure AD-App-ID** (vereist): u hebt de [toepassings-id](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)ook nodig). Als u de waarde wilt ophalen, gaat u naar uw Azure Active Directory en selecteert u **app-registraties**en vervolgens zoekt u naar het id-nummer van de **toepassing** die wordt weer gegeven (bijvoorbeeld `50c464d3-4930-494c-963c-1e951d15360e` ).

>[!Note]
>De Azure AD-App-ID is gekoppeld aan uw uitgevers-ID in uw partner centrum-account.  Zorg ervoor dat dezelfde toepassings-ID wordt gebruikt in al uw aanbiedingen.

>[!Note]
>Als de uitgever twee of meer verschillende accounts in het partner centrum heeft, moeten er twee of meer verschillende Azure AD-App-Id's worden gebruikt, elk voor een van de accounts. Elk partner account in het partner centrum moet een unieke Azure AD-App-ID gebruiken voor alle SaaS-aanbiedingen die via dit account worden gepubliceerd.

Selecteer **concept opslaan** voordat u doorgaat.

## <a name="plan-overview"></a>Overzicht van plan

Op deze pagina kunt u een verscheidenheid aan plan opties bieden binnen dezelfde aanbieding. Deze plannen (ook wel Sku's genoemd) kunnen verschillen qua versie, verdiensten maximaliseren of service lagen. U moet ten minste één abonnement instellen om uw aanbieding in Marketplace te verkopen.

Zodra u een abonnement hebt gemaakt, ziet u de namen, Id's, prijs modellen, Beschik baarheid (openbaar of privé), de huidige publicatie status en eventuele beschik bare acties.

**Acties** die beschikbaar zijn in het **plan overzicht** variëren afhankelijk van de huidige status van uw abonnement en kunnen het volgende omvatten:

- Als de plan status **concept** is-concept verwijderen
- Als de plan status **Live** -stop verkoop plan is of persoonlijke doel groep synchroniseren

**Nieuw plan maken** (mini maal één abonnement voor degenen die via micro soft willen verkopen)

- **Plan-id:** Maak een unieke plan-ID voor elk abonnement in deze aanbieding. Deze ID is zichtbaar voor klanten in de product-URL en Azure Resource Manager sjablonen (indien van toepassing). Gebruik alleen kleine letters, alfanumerieke tekens, streepjes of onderstrepingen. Voor deze abonnement-ID zijn Maxi maal 50 tekens toegestaan. De ID kan niet worden gewijzigd nadat de maken is geselecteerd.
- **Naam van abonnement:** Klanten zien deze naam wanneer ze bepalen welk abonnement binnen uw aanbieding moet worden geselecteerd. Maak in deze aanbieding een unieke aanbiedings naam voor elk abonnement. De naam van het abonnement wordt gebruikt om software-abonnementen te onderscheiden die deel kunnen uitmaken van dezelfde aanbieding (bijvoorbeeld aanbiedings naam: Windows Server; abonnementen: Windows Server 2016, Windows Server 2019).

### <a name="plan-listing"></a>Aanbieding plannen

Op deze pagina kunt u de naam en beschrijving van een plan definiëren. <!-- displays the languages (and markets) where your plan is available, currently English (United States) is the only location available. Additionally, this page displays the status of the language-specific listing and the date/time that it was added. You will need to define the marketplace details (offer name, description, search terms, etc.) for each language / market.-->

<!--#### Plan listing details-->

<!--Selecting one of the plan languages will display the **plan listing** information, including **Name** and **Description.** -->

- **Naam** : vooraf ingevuld op basis van uw preview **nieuwe plan** vermelding en wordt weer gegeven als de titel van het software-abonnement van uw aanbieding dat wordt weer gegeven in de Marketplace.
- **Beschrijving** : deze beschrijving is een kans om uit te leggen wat dit software plan uniek is en eventuele verschillen tussen andere software plannen binnen uw aanbieding. Mag Maxi maal 500 tekens bevatten.

Selecteer **concept opslaan** voordat u doorgaat.

#### <a name="pricing-and-availability"></a>Prijzen en beschikbaarheid

Op deze pagina kunt u de markten configureren waarop dit abonnement beschikbaar is, het gewenste verdiensten maximaliseren model, de prijs en de facturerings termijn. Daarnaast kunt u aangeven of het plan zichtbaar moet worden voor iedereen of alleen voor specifieke klanten (een privé-publiek).

#### <a name="markets-optional"></a>Markten (optioneel)

Elk plan moet beschikbaar zijn op ten minste één markt. Selecteer **markten bewerken** en schakel het selectie vakje in voor elke markt locatie waar u dit plan beschikbaar wilt maken. Deze pagina bevat een zoekvak en een optie voor het selecteren van de landen/regio's waarvoor ' BTW is geremitteerd ', waarbij micro soft namens u verkoop-en gebruiks belasting verkent.

Als u al prijzen voor uw abonnement hebt ingesteld in Verenigde Staten dollars (USD) en een andere markt locatie toevoegt, wordt de prijs voor de nieuwe markt berekend op basis van de huidige wissel koersen. Bekijk de prijs voor elke markt voordat u deze publiceert. Bekijk de prijzen op basis van de koppeling export prijzen (XLSX) nadat u uw wijzigingen hebt opgeslagen.

Selecteer **Opslaan** voordat u doorgaat.

#### <a name="pricing"></a>Prijzen

##### <a name="pricing-model"></a>Prijsmodel

**Vast** : Stel toegang tot uw aanbieding in met een enkelvoudige prijs per maand of per jaar. Dit wordt soms ook wel site-gebaseerde prijzen genoemd. Met dit prijs model kunt u optioneel plannen met gecontroleerde licenties definiëren die gebruikmaken van de API voor de Marketplace-meter service om klanten in rekening te brengen op basis van niet-standaard eenheden.  Zie voor meer informatie over het gebruik van facturen [met data limieten met behulp van de Marketplace-meter service](./saas-metered-billing.md).  U moet deze optie ook gebruiken als het gebruiks gedrag zich in bursts bevindt voor uw SaaS-service.  We raden u aan de klant vaak niet regel matig te scha kelen tussen plannen op basis van dagelijks of per uur.

**Per gebruiker** : de toegang tot uw aanbieding inschakelen met de prijs op basis van het aantal gebruikers dat toegang tot de aanbieding heeft of stoelen innemen. Met dit op gebruikers gebaseerd model kunt u het minimum en maximum aantal gebruikers instellen dat is toegestaan op basis van de prijs. Op deze manier kunnen verschillende prijs punten worden geconfigureerd op basis van het aantal gebruikers door het configureren van meerdere plannen.  Deze velden zijn optioneel. Als deze optie niet is geselecteerd, wordt het aantal gebruikers geïnterpreteerd als een limiet van Mini maal 1 en Maxi maal zo lang het systeem kan ondersteunen. Deze velden kunnen worden bewerkt als onderdeel van een update voor uw abonnement.

Na publicatie kan de prijs model keuze van facturering niet worden gewijzigd. Daarnaast moeten alle abonnementen voor hetzelfde aanbod hetzelfde prijs model delen.

##### <a name="user-limits"></a>Gebruikerslimieten

Selecteer, indien van toepassing, de minimale en maximale gebruikers limieten en stel deze in.

##### <a name="billing-term-and-price"></a>Facturerings termijn en-prijs

Selecteer de **periode** en **prijs** waarop klanten de vermelde prijs moeten betalen. Er moet mini maal één maand of jaar worden opgegeven, of beide opties kunnen aan klanten beschikbaar worden gesteld.

Prijzen die zijn ingesteld in USD (USD = Verenigde Staten dollar) worden omgezet in de lokale valuta van alle geselecteerde markten met de huidige wissel koersen wanneer deze worden opgeslagen. Valideer deze prijzen vóór de publicatie door het prijs werk blad te exporteren en de prijs op elke markt te bekijken. Als u aangepaste prijzen wilt instellen op een afzonderlijke markt, wijzigt en importeert u de prijs informatie in het werk blad. U bent verantwoordelijk voor het valideren van deze prijzen en de eigenaar van deze instellingen.
*\*U moet uw prijs wijzigingen eerst opslaan om het exporteren van prijs gegevens in te scha kelen.*

Controleer uw prijzen zorgvuldig voordat u deze publiceert. er zijn enkele beperkingen voor wat er kan worden gewijzigd nadat een plan is gepubliceerd:

- Zodra een plan is gepubliceerd, kan het prijs model niet worden gewijzigd.
- Zodra een facturerings termijn voor een abonnement is gepubliceerd, kan deze later niet meer worden verwijderd.
- Zodra een prijs voor een markt in uw abonnement wordt gepubliceerd, kunt u deze later niet meer wijzigen.

#### <a name="free-trial"></a>Gratis proefversie

Met SaaS-aanbiedingen via de commerciële Marketplace kunt u een gratis proef versie van één maand bieden bij het verkopen via micro soft. Voor alle facturerings modellen en voor waarden behalve plannen met een Data limiet worden gratis proef versies ondersteund. Met deze optie kunnen klanten een lage barrière hebben om een maand gratis toegang te geven.  Als u ervoor kiest een gratis proef versie in te scha kelen voor abonnementen binnen uw aanbieding, kan de klant niet converteren naar een betaald abonnement vóór het einde van de eerste periode van één maand.  Gedurende deze periode kunnen klanten die uw aanbieding kopen, een van de ondersteunde abonnementen proberen waarvoor de gratis proef versie is ingeschakeld en ertussen worden geconverteerd.  De conversie naar een betaald abonnement wordt automatisch uitgevoerd aan het einde van de termijn.

>[!NOTE]
>Als de klant kiest om te converteren naar een plan zonder gratis proef versies, wordt de conversie uitgevoerd, maar de gratis proef versie gaat onmiddellijk verloren. Zodra een klant begint met de betaling voor een abonnement, kunnen ze niet opnieuw een gratis proef versie ontvangen voor dezelfde licentie, zelfs niet als ze zijn geconverteerd naar een SKU die ondersteuning biedt voor gratis proef versies.

U kunt hier een gratis proef versie configureren voor elk abonnement in uw aanbieding. Schakel het selectie vakje in als u een proef versie van één maand wilt toestaan.

![Selectie vakje voor een gratis proef versie van één maand](./media/free-trial-enable.png)

>[!NOTE]
>Zodra uw aanbieding met een gratis proef versie is gepubliceerd, kan deze niet worden uitgeschakeld voor dat abonnement. Zorg ervoor dat deze instelling juist is voor de eerste publicatie om te voor komen dat u het abonnement opnieuw moet maken.

Als u informatie wilt verkrijgen over klant abonnementen die momenteel deel nemen aan een gratis proef versie, gebruikt u de nieuwe API `isFreeTrial` -eigenschap, die wordt gemarkeerd als waar of onwaar. Zie de [API voor SaaS Get-abonnementen](pc-saas-fulfillment-api-v2.md#get-subscription)voor meer informatie.

>[!NOTE]
>Gratis proef versies worden niet ondersteund voor abonnementen die gebruikmaken van de Marketplace-meter service.

#### <a name="plan-visibility"></a>Zicht baarheid van plan

U kunt elk plan zodanig configureren dat het zichtbaar is voor iedereen of alleen voor een specifieke doel groep van uw keuze. U kunt lidmaatschap van deze beperkte doel groep toewijzen met behulp van Azure AD-Tenant-Id's.

##### <a name="privacy"></a>Privacy

Selecteer **Dit is een privé-abonnement** dat ervoor zorgt dat uw plan privé is en alleen zichtbaar is voor de beperkte doel groep van uw keuze. Zodra het is gepubliceerd als een persoonlijk abonnement, kunt u de doel groep bijwerken of ervoor kiezen om het plan voor iedereen beschikbaar te stellen. Zodra een plan wordt gepubliceerd als zichtbaar voor iedereen, moet het worden weer gegeven voor iedereen (het kan niet opnieuw worden geconfigureerd als een persoonlijk abonnement).

##### <a name="restricted-audience-tenant-ids"></a>**Beperkte doel groep (Tenant-Id's)**

Wijs de doel groep toe die toegang heeft tot dit privé-abonnement. Toegang wordt toegewezen met behulp van Tenant-Id's met de optie om een beschrijving op te geven van elke Tenant-ID die is toegewezen. U kunt Maxi maal 10 Tenant-Id's toevoegen, of de Tenant-Id's van 20.000-klanten als u een CSV-werk blad bestand importeert.

Een Tenant is een representatie van een organisatie met een ID die wordt weer gegeven als een GUID (Globally Unique Identifier, een 128-bits geheel getal dat wordt gebruikt voor het identificeren van resources). Het is een toegewezen exemplaar van Azure AD dat een organisatie of app-ontwikkelaar ontvangt wanneer de organisatie of de ontwikkelaar van de app een relatie met micro soft maakt, bijvoorbeeld wanneer u zich aanmeldt voor Azure, Microsoft Intune of Microsoft 365. Elke Azure AD-tenant is uniek en werkt afzonderlijk van andere Azure AD-tenants. Om te controleren of u al een tenant hebt, meld u zich aan bij Azure Portal met het account dat u wilt gebruiken voor het beheren van uw toepassing. Als u een tenant hebt, wordt u automatisch aangemeld en ziet u de naam van de tenant direct onder de accountnaam. Houd de muisaanwijzer op uw accountnaam rechtsboven in Azure Portal om uw naam, e-mailadres, directory en tenant-id (een GUID), en uw domein te zien. Als uw account is gekoppeld aan meerdere tenants, kunt u de accountnaam selecteren om een menu te openen waarmee u kunt schakelen tussen de tenants. Elke tenant heeft zijn eigen tenant-id. U kunt ook de Tenant-ID van uw organisatie opzoeken met behulp van een domein naam-URL op [https://www.whatismytenantid.com](https://www.whatismytenantid.com) .

Hoewel SaaS voorziet in Tenant-Id's voor het definiëren van een persoonlijke doel groep, kunnen andere aanbiedings typen gebruikmaken van Azure-abonnement-Id's (die ook worden weer gegeven als GUID'S).

> [!NOTE]
> De persoonlijke doel groep (of een beperkt publiek) wijkt af van een preview-doel groep. Op de **[voorbeeld](#preview-audience)** pagina kunt u een voor beeld van een doel groep definiëren. Een preview-doel groep is toegang tot uw aanbieding toegestaan *voordat* de aanbieding Live op Marketplace wordt gepubliceerd. Hoewel de aanduiding van een privé-doel groep alleen van toepassing is op een specifiek abonnement, kan de preview-doel groep alle plannen (privé of niet) weer geven, maar alleen tijdens de beperkte preview-periode terwijl het plan wordt getest en gevalideerd.

Selecteer **concept opslaan** voordat u doorgaat.

## <a name="example-list-of-plans-within-a-marketplace-offer"></a>Voor beeld van een lijst met abonnementen in een Marketplace-aanbieding

:::image type="content" source="media/marketplace-plan.png" alt-text="Voor beeld Marketplace-plan aanbiedingen met notities.":::

#### <a name="call-out-descriptions"></a>Beschrijvingen van aanroepen

1. Plan naam
2. Plan beschrijving

<br>

## <a name="cloud-solution-provider-csp-reseller-audience"></a>Dealer-doel groep van Cloud Solution Provider (CSP)

Als u ervoor kiest om uw aanbieding beschikbaar te maken in het CSP-programma, kunnen Cloud solution providers uw product verkopen als onderdeel van een gebundelde oplossing voor hun klanten. Zie [Cloud Solution Providers](https://go.microsoft.com/fwlink/?linkid=2111109)(Engelstalig) voor meer informatie.

## <a name="publish"></a>Publiceren

Zodra u alle vereiste delen van de aanbieding hebt voltooid, selecteert u **controleren en publiceren** in de rechter bovenhoek van de portal.

### <a name="submit-offer-to-preview"></a>Aanbieding verzenden naar Preview

Als dit de eerste keer is dat u deze aanbieding publiceert, kunt u het volgende doen:

- Bekijk de voltooiings status voor elke sectie van de aanbieding.
    - **Niet gestart** : de sectie is niet gerakend en moet worden voltooid.
    - **Onvolledig** : de sectie bevat fouten die moeten worden hersteld of waarvoor meer informatie moet worden verstrekt. U moet terugkeren naar de sectie en deze bijwerken.
    - **Voltooid** : de sectie is voltooid, alle vereiste gegevens zijn opgegeven en er zijn geen fouten. Alle secties van de aanbieding moeten een volledige status hebben voordat u de aanbieding kunt indienen.
- Geef test instructies op het certificerings team om ervoor te zorgen dat uw app correct wordt getest, naast eventuele aanvullende notities die nuttig zijn voor de uitleg van uw app.
- Verzend de aanbieding voor publicatie door **verzenden**te selecteren. We sturen u een e-mail om u te laten weten wanneer een preview-versie van de aanbieding beschikbaar is om te controleren en goed te keuren. Ga terug naar het partner centrum en selecteer **Go-Live** om uw aanbieding te publiceren naar het open bare (of als u een privé-aanbieding naar de persoonlijke doel groep hebt).

## <a name="next-step"></a>Volgende stap

- [Een bestaande aanbieding bijwerken in Commerciële Marketplace](./update-existing-offer.md)
