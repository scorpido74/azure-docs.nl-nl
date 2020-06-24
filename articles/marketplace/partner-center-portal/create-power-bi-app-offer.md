---
title: Een Power BI-app-aanbieding maken in micro soft Commercial Marketplace
description: Meer informatie over het maken en publiceren van een Power BI-app-aanbieding naar Microsoft AppSource.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: 6ddf3db422a192b82b8d67e8e532a9ae0030d6f7
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85212514"
---
# <a name="create-a-power-bi-app-for-microsoft-appsource"></a>Een Power BI-app maken voor Microsoft AppSource

In dit artikel wordt beschreven hoe u een Power BI app-aanbieding maakt en publiceert naar micro soft [AppSource](https://appsource.microsoft.com/).

Voordat u begint, moet u [een commercieel Marketplace-account maken in Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) als u dit nog niet hebt gedaan. Zorg ervoor dat uw account is inge schreven in het Commercial Marketplace-programma.

## <a name="create-a-new-offer"></a>Een nieuwe aanbieding maken

1. Meld u aan bij [Partner Center](https://partner.microsoft.com/dashboard/home).
2. Selecteer in het menu links de optie **commerciële Marketplace**-  >  **overzicht**.
3. Selecteer op de pagina overzicht **+ nieuwe aanbieding**  >  **Power bi service-app**.

   ![Illustreert het navigatie menu.](./media/new-offer-power-bi-app.png)

> [!NOTE]
> Nadat een aanbieding is gepubliceerd, worden wijzigingen die in het partner centrum zijn aangebracht, alleen weer gegeven in de winkel wanneer u de aanbieding opnieuw publiceert. Zorg ervoor dat u altijd opnieuw publiceert nadat u wijzigingen hebt aangebracht.

> [!IMPORTANT]
> Als **Power bi service-app** niet wordt weer gegeven of ingeschakeld, is uw account niet gemachtigd om dit aanbiedings type te maken. Controleer of u voldoet aan alle [vereisten](create-power-bi-app-overview.md) voor dit aanbiedings type, inclusief registratie voor een ontwikkelaars account.

## <a name="new-offer"></a>Nieuwe aanbieding

Voer een **aanbiedings-id**in. Dit is een unieke id voor elke aanbieding in uw account.

- Deze ID is zichtbaar voor klanten in het webadres voor de Marketplace-aanbieding en Azure Resource Manager sjablonen, indien van toepassing.
- Gebruik alleen kleine letters en cijfers. Dit kan afbreek streepjes en onderstrepings tekens bevatten, maar mag niet langer zijn dan 50. Als u hier bijvoorbeeld **test-aanbieding-1** invoert, is het webadres van de aanbieding `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- De aanbiedings-ID kan niet worden gewijzigd nadat u **maken**hebt geselecteerd.

Voer een **alias**voor de aanbieding in. Dit is de naam die wordt gebruikt voor de aanbieding in Partner Center.

- Deze naam wordt niet gebruikt in Marketplace en wijkt af van de naam van de aanbieding en andere waarden die aan klanten worden weer gegeven.
- De aanbiedings alias kan niet worden gewijzigd nadat u **maken**hebt geselecteerd.

Selecteer **maken** om de aanbieding te genereren en door te gaan.

## <a name="offer-overview"></a>Overzicht van aanbieding

Op deze pagina ziet u een visuele weer gave van de stappen die nodig zijn om deze aanbieding (zowel voltooid als gepland) te publiceren en hoe lang elke stap moet worden voltooid.

Het bevat koppelingen voor het uitvoeren van bewerkingen op deze aanbieding op basis van de selectie die u maakt. Bijvoorbeeld:

- Als de aanbieding een concept is- [concept verwijderen](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- Als de aanbieding Live is: [de aanbieding wordt niet meer verkocht](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- Als de aanbieding in Preview- [Live](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval) staat
- Als u Publisher-afmelding niet hebt voltooid, kunt u het [Publiceren annuleren](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="offer-setup"></a>Installatie van aanbieding

### <a name="customer-leads"></a>Leads van klanten

Wanneer u uw aanbieding naar Marketplace publiceert met partner centrum, moet u deze verbinden met het CRM-systeem (Customer Relationship Management). Zo kunt u contact gegevens van klanten ontvangen zodra iemand interesse in of gebruikt voor uw product.

1. Selecteer een leadbestemming waarnaar wij de klantenleads moeten sturen. Het partner Centrum ondersteunt de volgende CRM-systemen:

    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) voor klant betrokkenheid
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Als uw CRM-systeem niet hierboven wordt vermeld, gebruikt u [Azure Table](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) of [https-eind punt](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) om klant lead gegevens op te slaan. Exporteer de gegevens vervolgens naar uw CRM-systeem.

2. Verbind uw aanbieding met de doel locatie van de lead wanneer u publiceert in het partner centrum.
3. Controleer of de verbinding met de doel locatie juist is geconfigureerd. Nadat u het in het partner centrum hebt gepubliceerd, valideren we de verbinding en sturen we u een test lead. Wanneer u een voor beeld van de aanbieding bekijkt voordat deze live gaat, kunt u ook uw lead verbinding testen door zelf te proberen de aanbieding aan te schaffen in de preview-omgeving.
4. Zorg ervoor dat de verbinding met de doel locatie van de lead bijgewerkt blijft, zodat u geen leads kwijtraakt.

Hier volgen enkele aanvullende bronnen voor het beheer van leads:

- [Overzicht Lead beheer](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [Veelgestelde vragen over leadmanagement](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Algemene leadconfiguratiefouten](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Overzicht Lead beheer](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (zorg ervoor dat de pop-upblokkering is uitgeschakeld)

Selecteer **concept opslaan** voordat u doorgaat.

## <a name="properties"></a>Eigenschappen

Op deze pagina kunt u de categorieën en industrieën definiëren die worden gebruikt voor het groeperen van uw aanbieding op Marketplace, uw app-versie en de juridische contracten die uw aanbieding ondersteunen.

### <a name="category"></a>Categorie

Selecteer mini maal één en Maxi maal drie categorieën. Deze categorieën worden gebruikt om uw aanbieding in de juiste Marketplace-Zoek gebieden te plaatsen en worden weer gegeven op de pagina met details van de aanbieding. In de beschrijving van de aanbieding wordt uitgelegd hoe uw aanbod deze categorieën ondersteunt.

### <a name="industry"></a>Branche

Selecteer eventueel Maxi maal twee branches en twee verticalen onder elke branche. Hoewel categorieën worden gebruikt voor het weer geven van uw aanbieding, worden de industrie en de verticale wereld gebruikt in zoek filters en toegepast op de winkel. Als uw aanbieding gericht is op een specifieke branche en/of verticaal, gebruikt u de beschrijving van de aanbieding om uit te leggen hoe uw aanbod de geselecteerde branches of verticale waarden ondersteunt. Als uw aanbieding niet specifiek is voor de branche, laat u deze sectie leeg.

> [!NOTE]
> We werken samen met het introduceren van nieuwe branches en verticalen om de ervaring voor de detectie van de aanbieding te verbeteren, maar sommige branches of verticalen zijn mogelijk nog niet zichtbaar op de winkel. Branches en verticale, gemarkeerd met een (*) zijn in de toekomst beschikbaar. Alle gepubliceerde aanbiedingen kunnen worden gedetecteerd via Zoek opdrachten met tref woorden.
<p>&nbsp;

| **Branche** | **Subsector** |
| --- | --- |
| * Automobiel | * Automobiel |
| Landbouw | * Overig-niet-gesegmenteerd |
| Distributie | * Groot handel<br>Pakje en pakket verzending |
| Onderwijs | *Hoger onderwijs <br> * Primair en secundair onderwijs/K-12<br>* Bibliotheken en musea |
| Financiële diensten | *Bank-en kapitaal <br> markten* Polis |
| Overheid | *Verdediging en intelligentie (wordt aangeduid met de naam National en open bare <br> veiligheid)* Open bare veiligheid en rechtvaardigheid<br>* Burger overheid |
| Gezondheids zorg (gebruikt voor de status) | *Status betaler <br> * Health-provider<br>* Farmaceutische producten |
| Productie en bronnen (gebruikt om productie te worden genoemd) | *Chemische en Agrochemical <br> * Discrete productie<br>* Energie |
| Handels-en consumenten artikelen (gebruikt voor de verkoop van Retail) | *Consumenten goederen <br> * Handelaren |
| * Media en communicatie (gebruikt voor het noemen van media en entertainment) | *Media en entertainment <br> * Telecom |
| Professionele services | *Juridisch <br> * Partner Professional-Services |
| * Architectuur en constructie (wordt gebruikt als architectuur techniek) | * Overig-niet-gesegmenteerd |
| * Horeca en reizen | *Hotels en vrije <br> * tijd Reizen en Trans Port<br>* Restaurants en voedsel Services |
| * Andere branches uit de open bare sector | *Bosbouw en visserij <br> * Profit organisaties |
| * Onroerend goed | * Overig-niet-gesegmenteerd |

### <a name="legal"></a>Juridisch

#### <a name="terms-and-conditions"></a>Voorwaarden

Als u uw eigen aangepaste voor waarden wilt opgeven, voert u Maxi maal 10.000 tekens in het vak **voor waarden** in. Als voor de voor waarden een langere beschrijving is vereist, voert u één webkoppeling in naar waar ze kunnen worden gevonden. Het wordt weer gegeven voor klanten als een actieve koppeling.

Klanten moeten deze voor waarden accepteren voordat ze uw aanbieding kunnen proberen.

Selecteer **concept opslaan** voordat u doorgaat naar de volgende sectie, aanbieding aanbieden.

## <a name="offer-listing"></a>Aanbieding weer geven

Hier definieert u de details van het aanbod dat op Marketplace wordt weer gegeven. Dit omvat de naam van de aanbieding, beschrijving, installatie kopieën, enzovoort.

### <a name="language"></a>Taal

Selecteer de taal waarin uw aanbieding wordt weer gegeven. **Engels (Verenigde Staten)** is momenteel de enige beschik bare optie.

Marketplace-gegevens (zoals aanbiedings naam, beschrijving en installatie kopieën) definiëren voor elke taal/markt. Selecteer de naam van de taal/markt om deze gegevens op te geven.

> [!NOTE]
> De details van de aanbieding hoeven niet in het Engels te zijn als de beschrijving van de aanbieding begint met de woord groep ' deze toepassing is alleen beschikbaar in [niet-Engelse taal] '. Het is ook handig om een nuttige koppeling te bieden om inhoud te bieden in een andere taal dan die wordt gebruikt in de aanbieding.

Hier volgt een voor beeld van hoe de aanbiedings gegevens worden weer gegeven in Microsoft AppSource (alle prijzen zijn bijvoorbeeld alleen bedoeld als voor beeld van de werkelijke kosten):

:::image type="content" source="media/example-power-bi-app.png" alt-text="Illustreert hoe deze aanbieding wordt weer gegeven in Microsoft AppSource.":::

#### <a name="call-out-descriptions"></a>Beschrijvingen van aanroepen

1. Logo
2. Producten
3. Categorieën
4. Bedrijfstakken
5. Ondersteunings adres (koppeling)
6. Gebruiksvoorwaarden
7. Privacybeleid
8. Naam van aanbieding
9. Samenvatting
10. Description
11. Scherm afbeeldingen/Video's

### <a name="name"></a>Name

De naam die u hier opgeeft, wordt weer gegeven als de titel van uw aanbieding. Dit veld is vooraf ingevuld met de tekst die u hebt ingevoerd in het vak **aanbiedings alias** wanneer u de aanbieding hebt gemaakt. U kunt deze naam later wijzigen.

De naam:

- Kan worden gemerkt (en u kunt symbolen van het handels merk of copyright bevatten).
- Mag niet langer zijn dan 50 tekens.
- Kan geen emojis bevatten.

### <a name="search-results-summary"></a>Samen vatting van zoek resultaten

Geef een korte beschrijving van uw aanbieding. Dit kan Maxi maal 100 tekens lang zijn en wordt gebruikt in Zoek resultaten voor Marketplace.

### <a name="description"></a>Description

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

### <a name="search-keywords"></a>Tref woorden zoeken

Voer Maxi maal drie optionele Zoek trefwoorden in om klanten te helpen uw aanbieding op Marketplace te vinden. Voor de beste resultaten gebruikt u deze tref woorden ook in uw beschrijving.

### <a name="helpprivacy-web-addresses"></a>Help/Privacy-webadressen

Geef koppelingen op waarmee klanten beter inzicht kunnen krijgen in uw aanbieding.

#### <a name="help-link"></a>Help-koppeling

Voer het webadres in waar klanten meer kunnen weten over uw aanbieding.

#### <a name="privacy-policy-url"></a>URL van privacybeleid

Voer het webadres in voor het privacybeleid van uw organisatie. U bent verantwoordelijk om ervoor te zorgen dat uw aanbieding voldoet aan de wetten en voor schriften van de privacy. U bent ook verantwoordelijk voor het posten van een geldig privacybeleid op uw website.

### <a name="contact-information"></a>Contactgegevens

U moet de naam, het e-mail adres en het telefoon nummer opgeven voor een **ondersteunings contact** en een **technische contact persoon**. Deze informatie wordt niet weer gegeven aan klanten. Het is beschikbaar voor micro soft en kan worden meegeleverd met de Cloud Solution Provider (CSP)-partners.

- Ondersteunings contact (vereist): voor algemene ondersteunings vragen.
- Technische contact persoon (vereist): voor technische vragen en certificerings problemen.
- CSP-programma: Neem contact op met de leverancier van het CSP-programma.

Geef in de sectie **ondersteuning voor contact persoon** het webadres op van de **ondersteunings website** waar partners ondersteuning voor uw aanbieding kunnen vinden.

### <a name="supporting-documents"></a>Ondersteunende documenten

Geef ten minste één en Maxi maal drie gerelateerde marketing documenten op in PDF-indeling. Bijvoorbeeld white papers, brochures, controle lijsten of presentaties.

### <a name="marketplace-images"></a>Marketplace-installatie kopieën

Bied logo's en installatie kopieën die u kunt gebruiken met uw aanbieding. Alle installatie kopieën moeten de PNG-indeling hebben. Vage afbeeldingen worden geweigerd.

>[!NOTE]
>Als u een probleem hebt met het uploaden van bestanden, moet u ervoor zorgen dat uw lokale netwerk de door `https://upload.xboxlive.com` Partner Center gebruikte service niet blokkeert.

#### <a name="store-logos"></a>Winkel logo's

Bied PNG-bestanden van het logo van uw aanbieding in twee pixel grootten:
- **Klein** (48 x 48)
- **Groot** (216 x 216)

Beide logo's zijn vereist en worden gebruikt op verschillende plaatsen in de Marketplace-vermelding.

#### <a name="screenshots"></a>Schermopnamen

Voeg minstens één en Maxi maal vijf scherm opnamen toe die laten zien hoe uw aanbieding werkt. Elk moet 1280 x 720 pixels groot en in PNG-indeling zijn.

#### <a name="videos-optional"></a>Video's (optioneel)

Voeg Maxi maal vijf Video's toe die uw aanbieding aantonen. Voer de naam van de video, het webadres en de PNG-afbeelding van de video in op een grootte van 1280 x 720 pixels.

#### <a name="additional-marketplace-listing-resources"></a>Aanvullende bronnen voor Marketplace-lijst

Zie [Aanbevolen procedures](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)voor aanbieding voor meer informatie over het maken van aanbiedingen.

## <a name="technical-configuration"></a>Technische configuratie

Promoot uw app in Power BI service voor productie en bied de installatie koppeling van het Power BI app waarmee klanten uw app kunnen installeren. Zie [apps publiceren met Dash boards en rapporten in Power bi](https://docs.microsoft.com/power-bi/service-create-distribute-apps)voor meer informatie.

## <a name="supplemental-content"></a>Aanvullende inhoud

Geef aanvullende informatie over uw aanbieding zodat wij deze kunnen valideren. Deze informatie wordt niet weer gegeven aan klanten of gepubliceerd op Marketplace.

### <a name="validation-assets"></a>Validatie-assets

Voeg desgewenst instructies (Maxi maal 3.000 tekens) toe om het micro soft-validatie team te helpen bij het configureren, verbinden en testen van uw app. Bevatten typische configuratie-instellingen, accounts, para meters of andere informatie die kan worden gebruikt voor het testen van de optie gegevens verbinding maken. Deze informatie is alleen zichtbaar voor het validatie team en wordt alleen voor validatie doeleinden gebruikt.

## <a name="review-and-publish"></a>Controleren en publiceren

Nadat u alle vereiste gedeelten van de aanbieding hebt voltooid, kunt u uw aanbieding verzenden om deze te controleren en te publiceren.

Selecteer in de rechter bovenhoek van de portal **controleren en publiceren**.

Op de pagina controleren kunt u het volgende doen:

- Bekijk de voltooiings status voor elke sectie van de aanbieding. U kunt pas publiceren als alle secties van de aanbieding zijn gemarkeerd als voltooid.
  - **Niet gestart** : de sectie is niet gestart en moet worden voltooid.
  - **Onvolledig** : de sectie bevat fouten die moeten worden hersteld of waarvoor u meer informatie moet opgeven. Zie de secties eerder in dit document voor meer informatie.
  - **Voltooid** : de sectie bevat alle vereiste gegevens en er zijn geen fouten. Alle onderdelen van de aanbieding moeten volledig zijn voordat u de aanbieding kunt indienen.
- Geef test instructies op het certificerings team om te controleren of uw app correct is getest. Geef ook aanvullende notities op die handig zijn voor het leren van uw aanbieding.

Selecteer **publiceren**om de aanbieding voor publicatie in te dienen.

We sturen u een e-mail om u te laten weten wanneer een preview-versie van de aanbieding beschikbaar is om te controleren en goed te keuren. Als u uw aanbieding naar het publiek (of als een privé-aanbieding, naar een privé-publiek) wilt publiceren, gaat u naar partner centrum en selecteert u **Go-Live**.
