---
title: Een Dynamics 365 Business Central-aanbieding maken-micro soft Commercial Marketplace
description: Meer informatie over de stappen en overwegingen voor het maken van een nieuwe Dynamics 365 Business Central-aanbieding in de portal voor commerciële Marketplace in het partner centrum. U kunt uw aanbieding aanbieden of verkopen in azure Marketplace of via het programma Cloud Solution Provider (CSP).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: navits09
ms.author: navits
ms.date: 06/17/2020
ms.openlocfilehash: 456764ae2b08b9852e56b2ab3f7cd9238b540b58
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86121942"
---
# <a name="create-a-dynamics-365-business-central-offer"></a>Een Dynamics 365 Business Central-aanbieding maken

In dit artikel wordt uitgelegd hoe u een nieuwe aanbieding voor Dynamics 365 Business Central maakt. [Micro soft Dynamics 365 Business Central](https://dynamics.microsoft.com/business-central) is een ERP-systeem (Enter prise resource planning) dat een breed scala aan bedrijfs processen verwerkt, waaronder financiën, bedrijfs activiteiten, toeleverings keten, CRM en project beheer en elektronische handel. Premium-pakketten bieden ook ondersteuning voor het klassieke implementatie model en productie. Alle aanbiedingen voor Dynamics 365 Business Central moeten het certificerings proces door lopen.

Voordat u begint, moet u [een commercieel Marketplace-account maken in Partner Center](create-account.md) als u dit nog niet hebt gedaan. Zorg ervoor dat uw account is inge schreven in het Commercial Marketplace-programma.

## <a name="create-a-new-offer"></a>Een nieuwe aanbieding maken

1. Meld u aan bij [Partner Center](https://partner.microsoft.com/dashboard/home).
2. Selecteer in het menu links de optie **commerciële Marketplace**-  >  **overzicht**.
3. Selecteer op de pagina overzicht **+ nieuwe aanbieding**  >  **Dynamics 365 Business Central**.

    ![Illustreert het navigatie menu.](./media/new-offer-dynamics-365-business-central.png)

> [!NOTE]
> Nadat een aanbieding is gepubliceerd, worden wijzigingen die in het partner centrum zijn aangebracht, alleen weer gegeven in de winkel wanneer u de aanbieding opnieuw publiceert. Zorg ervoor dat u altijd opnieuw publiceert nadat u wijzigingen hebt aangebracht.

## <a name="new-offer"></a>Nieuwe aanbieding

Voer een **aanbiedings-id**in. Dit is een unieke id voor elke aanbieding in uw account.

- Deze ID is zichtbaar voor klanten in het webadres voor de Marketplace-aanbieding en Azure Resource Manager sjablonen, indien van toepassing.
- Gebruik alleen kleine letters en cijfers. Dit kan afbreek streepjes en onderstrepings tekens bevatten, maar mag niet langer zijn dan 50. Als u bijvoorbeeld **test-aanbieding-1**invoert, is het webadres van de aanbieding `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- De aanbiedings-ID kan niet worden gewijzigd nadat u **maken**hebt geselecteerd.

Voer een **alias**voor de aanbieding in. Dit is de naam die wordt gebruikt voor de aanbieding in Partner Center.

- Deze naam wordt niet gebruikt in Marketplace en wijkt af van de naam van de aanbieding en andere waarden die aan klanten worden weer gegeven.
- De aanbiedings alias kan niet worden gewijzigd nadat u **maken**hebt geselecteerd.

Selecteer **maken** om de aanbieding te genereren en door te gaan.

## <a name="offer-setup"></a>Installatie van aanbieding

Volg deze stappen om uw aanbieding in te stellen.

### <a name="how-do-you-want-potential-customers-to-interact-with-this-listing-offer"></a>Hoe wilt u potentiële klanten interactie laten doen met deze aanbieding?

Selecteer de optie die u wilt gebruiken voor deze aanbieding.

#### <a name="get-it-now-free"></a>Nu downloaden (gratis)

Bied uw aanbod aan klanten gratis aan door een geldige URL op te geven (te beginnen met *http* of *https*), waar ze toegang hebben tot uw app.  Bijvoorbeeld `https://contoso.com/my-app`.

#### <a name="free-trial-listing"></a>Gratis proef versie (lijst)

Bied uw aanbieding aan klanten aan met een koppeling naar een gratis proef versie door een geldige URL op te geven (beginnend met `http` of `https` ), waar een proef versie kan worden verkregen.  Bijvoorbeeld `https://contoso.com/trial/my-app`. Gratis proef versies van aanbieding worden gemaakt, beheerd en geconfigureerd door uw service en er zijn geen abonnementen die door micro soft worden beheerd.

> [!NOTE]
> De tokens die uw toepassing via uw proef koppeling ontvangt, kunnen alleen worden gebruikt voor het verkrijgen van gebruikers gegevens via Azure Active Directory (Azure AD) om het maken van accounts in uw app te automatiseren. Micro soft-accounts worden niet ondersteund voor verificatie met behulp van dit token.

#### <a name="contact-me"></a>Contact opnemen

Contact gegevens van klanten verzamelen door verbinding te maken met uw CRM-systeem (Customer Relationship Management). De klant wordt gevraagd om toestemming te krijgen om hun gegevens te delen. Deze klant gegevens, samen met de naam van de aanbieding, ID en Marketplace-bron waar ze uw aanbieding vinden, worden verzonden naar het CRM-systeem dat u hebt geconfigureerd. Zie [leads van klanten](#customer-leads)voor meer informatie over het configureren van uw CRM.

### <a name="test-drive"></a>Station testen

Een test drive is een fantastische manier om uw aanbieding aan potentiële klanten te laten presen teren door hen de mogelijkheid te bieden om te kopen voordat u aan de slag gaat, wat resulteert in een verhoogde conversie en de generatie van uiterst gekwalificeerde leads. Begin met [Wat is test drive](../what-is-test-drive.md)voor meer informatie.

Als u een test drive voor een bepaalde periode wilt inschakelen, schakelt u het selectie vakje **een test drive inschakelen** in. Als u test drive uit uw aanbieding wilt verwijderen, schakelt u dit selectie vakje uit.

### <a name="customer-leads"></a>Leads van klanten

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Zie [Lead Management Overview](./commercial-marketplace-get-customer-leads.md)voor meer informatie.

Selecteer **concept opslaan** voordat u doorgaat.

## <a name="properties"></a>Eigenschappen

Op deze pagina kunt u de categorieën en industrieën definiëren die worden gebruikt voor het groeperen van uw aanbieding op Marketplace, uw app-versie en de juridische contracten die uw aanbieding ondersteunen.

### <a name="category"></a>Categorie

Selecteer categorieën en subcategorieën om uw aanbieding te plaatsen in de juiste Zoek gebieden voor Marketplace. Zorg ervoor dat u beschrijft hoe uw aanbod deze categorieën ondersteunt in de beschrijving van de aanbieding. Selecteer:

- Ten minste één en Maxi maal twee categorieën, met inbegrip van een primaire en secundaire categorie (optioneel).
- Maxi maal twee subcategorieën voor elke primaire en/of secundaire categorie. Als er geen subcategorie van toepassing is op uw aanbieding, selecteert u **niet van toepassing**.

Bekijk de volledige lijst met categorieën en subcategorieën in [Aanbevolen procedures voor aanbiedingen](../gtm-offer-listing-best-practices.md).

### <a name="industry"></a>Branche

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="app-version"></a>App-versie

Voer het versie nummer van uw aanbieding in. Klanten zien dat deze versie wordt weer gegeven op de detail pagina van de aanbieding.

### <a name="terms-and-conditions"></a>Voorwaarden

Geef uw eigen juridische voor waarden op in het veld voor **waarden** . U kunt ook de URL opgeven waar uw voor waarden kunnen worden gevonden. Klanten moeten deze voor waarden accepteren voordat ze uw aanbieding kunnen proberen.

Selecteer **concept opslaan** voordat u doorgaat.

## <a name="offer-listing"></a>Aanbieding weer geven

Hier definieert u Details voor uw aanbieding, zoals naam, beschrijving en installatie kopieën.

> [!NOTE]
> U kunt de details van de aanbiedings vermelding alleen in één taal opgeven. Het is niet nodig om in het Engels te zijn, zolang de beschrijving van het aanbod begint met de woord groep ' deze toepassing is alleen beschikbaar in [niet-Engelse taal] '. Het is ook toegestaan een URL voor de *Help-koppeling* op te geven om inhoud te bieden in een andere taal dan die in de inhoud van de aanbieding.

Hier volgt een voor beeld van hoe de aanbiedings gegevens worden weer gegeven in Microsoft AppSource (alle prijzen zijn bijvoorbeeld alleen bedoeld als voor beeld van de werkelijke kosten):

:::image type="content" source="media/example-d365-business-central.png" alt-text="Illustreert hoe deze aanbieding wordt weer gegeven in Microsoft AppSource.":::

#### <a name="call-out-descriptions"></a>Beschrijvingen van aanroepen

1. Logo
2. Producten
3. Categorieën
4. Ondersteunings adres (koppeling)
5. Gebruiksvoorwaarden
6. Privacybeleid
7. Naam van aanbieding
8. Samenvatting
9. Description
10. Scherm afbeeldingen/Video's

### <a name="name"></a>Name

De naam die u hier invoert, wordt aan klanten weer gegeven als de titel van de aanbieding. Dit veld is vooraf ingevuld met de tekst die u hebt ingevoerd voor de **aanbiedings alias** tijdens het maken van de aanbieding, maar u kunt deze waarde wijzigen. Deze naam kan worden aangemerkt (en u kunt symbolen van het handels merk of copyright bevatten). De naam mag niet langer zijn dan 50 tekens en mag geen emojis bevatten.

### <a name="short-description"></a>Korte beschrijving

Geef een korte beschrijving van uw aanbieding, Maxi maal 100 tekens. Deze beschrijving kan worden gebruikt in Zoek resultaten voor Marketplace.

### <a name="description"></a>Description

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

### <a name="search-keywords"></a>Tref woorden zoeken

U kunt eventueel Maxi maal drie Zoek trefwoorden invoeren om klanten te helpen uw aanbieding op Marketplace te vinden. Probeer deze tref woorden ook in uw beschrijving te gebruiken voor de beste resultaten.

### <a name="products-your-app-works-with"></a>Producten waarmee uw app werkt

Als u klanten wilt laten weten dat uw app werkt met specifieke producten, voert u hier Maxi maal drie product namen in.

### <a name="helpprivacy-urls"></a>Help/Privacy-Url's

In deze sectie vindt u koppelingen waarmee klanten meer inzicht kunnen krijgen in uw aanbieding.

#### <a name="help-link"></a>Help-koppeling

Voer de URL in waar klanten meer kunnen weten over uw aanbieding. De **Help-koppeling** mag niet hetzelfde zijn als uw **ondersteunings-URL** (zie hieronder).

#### <a name="privacy-policy-link"></a>Koppeling Privacybeleid

Voer de URL in voor het privacybeleid van uw organisatie. U bent verantwoordelijk om ervoor te zorgen dat uw app voldoet aan de wetten en voor schriften van de privacy en om een geldig privacybeleid te bieden.

### <a name="contact-information"></a>Contactgegevens

In deze sectie moet u de naam, het e-mail adres en het telefoon nummer opgeven voor een **ondersteunings contact** en een **technische contact persoon**. Deze informatie wordt niet weer gegeven aan klanten, maar is beschikbaar voor micro soft en kan worden verschaft aan CSP-partners.

In het gedeelte **ondersteuning voor contact opnemen** moet u ook de **ondersteunings-URL** opgeven waar CSP-partners ondersteuning voor uw aanbieding kunnen vinden. De ondersteunings-URL mag niet hetzelfde zijn als de **Help-koppeling**.

### <a name="supporting-documents"></a>Ondersteunende documenten

Geef hier ten minste één (en Maxi maal drie) gerelateerde marketing documenten op, zoals witboeken, brochures, controle lijsten of presentaties. Deze documenten moeten de PDF-indeling hebben.

### <a name="marketplace-images"></a>Marketplace-installatie kopieën

Bied logo's en installatie kopieën voor uw aanbieding. Alle installatie kopieën moeten de PNG-indeling hebben. Upload uw aanbiedings logo in twee grootten:

* **Klein** (48 x 48 pixels)
* **Groot** (216 x 216 pixels)

>[!NOTE]
>Als u een probleem hebt met het uploaden van bestanden, moet u ervoor zorgen dat uw lokale netwerk de door `https://upload.xboxlive.com` Partner Center gebruikte service niet blokkeert.

#### <a name="screenshots"></a>Schermopnamen

Scherm afbeeldingen toevoegen die laten zien hoe uw aanbieding werkt. Er zijn ten minste drie scherm afbeeldingen vereist en u kunt Maxi maal vijf toevoegen. Alle scherm afbeeldingen moeten 1280 x 720 pixels zijn.

#### <a name="videos"></a>Video's

U kunt optioneel Maxi maal vijf Video's toevoegen die uw aanbieding aantonen. Deze Video's moeten worden gehost op YouTube en/of Vimeo. Voer voor elke video de naam, de URL en een miniatuur afbeelding van de video in (1280 x 720 pixels).

#### <a name="additional-marketplace-listing-resources"></a>Aanvullende bronnen voor Marketplace-lijst

[Best practices voor Marketplace-aanbiedingen](../gtm-offer-listing-best-practices.md)

Selecteer **concept opslaan** voordat u doorgaat.

## <a name="availability"></a>Beschikbaarheid

Op deze pagina vindt u opties waar en hoe u uw aanbieding beschikbaar kunt stellen.

### <a name="markets"></a>Landen

In deze sectie kunt u de markten opgeven waarin uw aanbieding beschikbaar moet zijn. Als u dit wilt doen, selecteert u **markten bewerken**om het pop-upvenster **markt selectie** weer te geven.

Selecteer ten minste één markt om uw aanbieding te publiceren. Kies **Alles selecteren** om uw aanbieding beschikbaar te stellen op elke mogelijke markt of selecteer de specifieke markten die u wilt toevoegen.

Uw selecties zijn hier alleen van toepassing op nieuwe verwervingen; Als iemand uw app al in een bepaalde markt heeft en u deze markt later verwijdert, kunnen de personen die de aanbieding al op die markt hebben, deze blijven gebruiken, maar kunnen er geen nieuwe klanten op deze markt worden aangeboden.

> [!IMPORTANT]
> Het is uw verantwoordelijkheid om te voldoen aan alle lokale wettelijke vereisten, zelfs als deze vereisten hier niet worden vermeld of in het partner centrum.

Houd er rekening mee dat zelfs als u alle markten, lokale wetten, beperkingen of andere factoren selecteert, kan worden voor komen dat bepaalde aanbiedingen in sommige landen en regio's worden weer gegeven.

### <a name="preview-audience"></a>Voor beeld van doel groep

Voordat u uw aanbieding naar de bredere Marketplace-aanbieding publiceert, moet u deze eerst beschikbaar maken voor een beperkte preview- **doel groep**. Voer hier een **Hide-toets** (wille keurige teken reeks met alleen kleine letters en/of cijfers) in. Leden van uw preview-doel groep kunnen deze verbergen sleutel als een token gebruiken om een voor beeld van uw aanbieding op Marketplace te bekijken.

Wanneer u klaar bent om uw aanbieding beschikbaar te maken en de preview-beperking te verwijderen, moet u de **sleutel verbergen** verwijderen en opnieuw publiceren.

Selecteer **concept opslaan** voordat u doorgaat.

## <a name="technical-configuration"></a>Technische configuratie

Op deze pagina worden de technische details gedefinieerd die worden gebruikt om verbinding te maken met uw aanbieding. Met deze verbinding kan ons uw aanbieding voor de eind klant inrichten als deze ervoor kiezen deze te verkrijgen.

### <a name="package-type"></a>Pakkettype

Selecteer de optie die van toepassing is op uw aanbieding:

* **Toevoegen aan** : een invoeg toepassings toepassing breidt de ervaring en de bestaande functionaliteit van Dynamics 365 Business Central uit. Zie [add-on apps](https://docs.microsoft.com/dynamics365/business-central/dev-itpro/developer/readiness/readiness-add-on-apps)voor meer informatie.
* **Connect** : een Connect-app kan worden gebruikt in het scenario waarin een Point-to-point-verbinding tussen Dynamics 365 Business Central en een oplossing of service van derden tot stand moet worden gebracht. Zie [apps verbinden](https://docs.microsoft.com/dynamics365/business-central/dev-itpro/developer/readiness/readiness-connect-apps)voor meer informatie.

### <a name="file-upload"></a>Bestand uploaden

Als u hierboven **invoeg toepassing** hebt geselecteerd, waar u het pakket bestand van uw aanbieding uploadt, samen met de pakket bestanden voor elke uitbrei ding waarvoor afhankelijkheden gelden.

#### <a name="extensions-package-file"></a>Extensie pakket bestand

Upload het bestand met het extensie pakket (. app) voor uw aanbieding.

#### <a name="library-package-file"></a>Bibliotheek pakket bestand

Vereist als uw aanbieding moet worden geïnstalleerd samen met een andere extensie die niet op Marketplace wordt gepubliceerd. Als u het app-bestand hier uploadt.

#### <a name="dependency-package-file"></a>Afhankelijkheids pakket bestand

Vereist als uw aanbieding moet worden geïnstalleerd samen met een andere extensie die al op de Marketplace is gepubliceerd. Als dit het geval is, uploadt u het `.app` of `.zip` bestand hier.

### <a name="url-to-app-installation"></a>URL voor installatie van de app

Als u hierboven **verbinding maken** hebt geselecteerd, geeft u hier het adres voor uw app-installatie op. Voor verbonden services die niet hoeven te worden geïnstalleerd, geeft u het adres voor uw service-landings pagina of de registratie pagina op.

Selecteer **concept opslaan** voordat u doorgaat.

## <a name="test-drive-technical-configuration"></a>Technische configuratie van test drive

Op deze pagina kunt u een demonstratie ("test drive") instellen waarmee klanten uw aanbieding kunnen proberen voordat ze deze aanschaffen. Meer informatie vindt u in [Wat is test drive](../what-is-test-drive.md).

Als u een test drive wilt inschakelen, schakelt u het selectie vakje **een test drive inschakelen** in op het tabblad installatie van de [aanbieding](#test-drive) . Als u test drive uit uw aanbieding wilt verwijderen, schakelt u dit selectie vakje uit.

Wanneer u klaar bent met het instellen van uw test drive, selecteert u **concept opslaan** voordat u doorgaat.

## <a name="supplemental-content"></a>Aanvullende inhoud

Op deze pagina kunt u aanvullende informatie over uw aanbieding opgeven zodat we uw aanbieding kunnen valideren. Deze informatie wordt niet weer gegeven aan klanten of gepubliceerd op Marketplace.

### <a name="target-release"></a>Doel release

Geef aan welke release van micro soft Dynamics business Central uw oplossingen doelen: **Current**, **Next Major**of **Next minor**. Met deze informatie kunnen we uw oplossing op de juiste manier testen.

### <a name="supported-editions"></a>Ondersteunde versies

Als voor uw aanbieding de Premium-editie van micro soft Dynamics 365 Business Central is vereist, selecteert u alleen **Premium** . Als dat niet het geval is, selecteert u zowel **kernen** als **Premium**.

### <a name="key-usage-scenario"></a>Scenario voor sleutel gebruik

U moet een PDF-bestand uploaden met een lijst met de scenario's voor het gebruik van sleutels die worden vermeld in een document (PDF-indeling). Alle scenario's die hier worden vermeld, kunnen door ons validatie team worden geverifieerd voordat we uw aanbieding voor Marketplace goed keuren.

### <a name="app-tests-automation"></a>Automatisering van app-tests

Als uw aanbieding een invoeg toepassings toepassing is, moet u een **automatiserings** bestand voor app-tests uploaden (. app). Dit bestand is niet van toepassing op het verbinden van apps.

### <a name="test-accounts"></a>Test accounts

Als u een test account nodig hebt zodat het certificerings team uw aanbieding correct kan controleren, uploadt u een PDF-, doc-of DOCX-bestand met de gegevens van uw **test accounts** .

## <a name="publish"></a>Publiceren

### <a name="submit-offer-to-preview"></a>Aanbieding verzenden naar Preview

Zodra u alle vereiste delen van de aanbieding hebt voltooid, selecteert u **publiceren** in de rechter bovenhoek van de portal. U wordt omgeleid naar de pagina **controleren en publiceren** . 

Als dit de eerste keer is dat u deze aanbieding publiceert, kunt u het volgende doen:

- Bekijk de voltooiings status voor elke sectie van de aanbieding.
    - *Niet gestart* : de sectie is niet gerakend en moet worden voltooid.
    - *Onvolledig* : de sectie bevat fouten die moeten worden hersteld of waarvoor meer informatie moet worden verstrekt. Ga terug naar de sectie (s) en werk deze bij.
    - *Voltooid* : de sectie is voltooid, alle vereiste gegevens zijn opgegeven en er zijn geen fouten. Alle secties van de aanbieding moeten een volledige status hebben voordat u de aanbieding kunt indienen.
- Geef in de sectie **opmerkingen voor certificering** test instructies op voor het certificerings team om ervoor te zorgen dat uw app correct wordt getest, naast eventuele aanvullende notities die nuttig zijn voor de uitleg van uw app.
- Verzend de aanbieding voor publicatie door **verzenden**te selecteren. We sturen u een e-mail wanneer een preview-versie van de aanbieding beschikbaar is om te controleren en goed te keuren. Ga terug naar het partner centrum en selecteer **Go-Live** voor de aanbieding om uw aanbieding naar het publiek te publiceren.

## <a name="next-steps"></a>Volgende stappen

- [Een bestaande aanbieding bijwerken in Commerciële Marketplace](./update-existing-offer.md)
