---
title: Uw toepassing instellen in de portal voor publiceren
description: Instructies voor het instellen van uw toepassing in de Cloud Publishing Portal.
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricardo.Villalobos
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 1bf89f94d91e0d809428cf8098db0fb37afdb47a
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73814681"
---
# <a name="setup-your-application-in-the-publishing-portal"></a>Uw toepassing instellen in de portal voor publiceren

U bent nu klaar om uw toepassing in te stellen in de portal voor publiceren.

## <a name="login-and-create-a-new-offer"></a>Meld u aan en maak een nieuwe aanbieding

1. Meld u aan bij de [Cloud Partner-Portal](https://cloudpartner.azure.com/).
2. Klik in de linkernavigatiebalk op "+ nieuwe aanbieding" en selecteer "Dynamics 365 voor klant betrokkenheid".

![Een nieuwe aanbieding selecteren](./media/CRMScreenShot14.png)

1. Er wordt nu een nieuwe weer gave ' editor ' voor de aanbieding geopend en we zijn klaar om te beginnen met ontwerpen.

![Nieuw scherm voor aanbieding](./media/CRMScreenShot15.png)

1. De ' formulieren ' die moeten worden ingevuld, worden weer gegeven aan de linkerkant in de weer gave ' editor '. Elk formulier bestaat uit een set velden die moeten worden ingevuld. De vereiste velden zijn gemarkeerd met een rood sterretje (\*).

Er zijn vier hoofd formulieren voor het ontwerpen van een Dynamics 365 voor de klant engagement-aanbieding

* Aanbiedings instellingen
* Technische informatie
* Details van de winkel
* Contactpersonen

## <a name="fill-out-the-offer-settings-form"></a>Vul het formulier voor de aanbiedings instellingen in

Het formulier instellingen voor de aanbieding is een basis formulier waarmee u de instellingen voor de aanbieding kunt opgeven. De verschillende velden worden hieronder beschreven.

### <a name="offer-id"></a>Aanbiedings-ID

Dit is een unieke id voor de aanbieding binnen een Publisher-profiel. Deze ID wordt weer gegeven in product-Url's. De id kan alleen bestaan uit alfanumerieke tekens in kleine letters en streepjes (-). De ID kan niet eindigen op een streepje en kan Maxi maal 50 tekens bevatten. Dit veld is vergrendeld wanneer een aanbieding live gaat.

Als bijvoorbeeld een uitgever **' Contoso '** uitgevers een aanbieding met de AANBIEDINGS-id **' voor beeld-webapp '** maakt, wordt deze in AppSource weer gegeven als ' https:\//appsource.Microsoft.com/Marketplace/apps/contoso.sample-webapp?tab=Overview '

### <a name="publisher-id"></a>Uitgevers-ID

In deze vervolg keuzelijst kunt u het Publisher-profiel kiezen waarvoor u deze aanbieding wilt publiceren. Dit veld is vergrendeld wanneer een aanbieding live gaat.

### <a name="name"></a>Naam

Dit is de weergave naam voor uw aanbieding. Dit is de naam die wordt weer gegeven in [AppSource](https://appsource.microsoft.com/). De naam mag maximaal 50 tekens bevatten.

Klik op Opslaan om de voortgang op te slaan. De volgende stap is om technische gegevens voor uw aanbieding toe te voegen.

## <a name="fill-out-the-technical-info-form"></a>Het formulier technische informatie invullen


Het formulier technische informatie is de plek waar u informatie moet invullen die specifiek is voor uw Dynamics 365 voor klant engagement-oplossing. Als u de muis aanwijzer op de plaatst, wordt er meer informatie weer gegeven. Zie het voorbeeld hieronder.

![Scherm technische informatie](./media/CRMScreenShot16.png)

### <a name="application-info"></a>Toepassings gegevens

De meeste uitgevers behouden deze velden met de standaard waarden, gebruiker, Nee, Nee en een lege toepassings configuratie-URL volgens de bovenstaande scherm afbeelding.

### <a name="crm-package"></a>CRM-pakket

![CRM-pakket gegevens](./media/CRMScreenShot17.png)

Hier volgt een uitleg voor deze velden:

* Bestands naam van het pakket: de bestands naam die u in de bovenstaande stap hebt gemaakt bij het maken van het zip-bestand dat uw CRM AppSource-pakket is. In het bovenstaande voor beeld is dit ' micro soft\_SamplePackage. zip '.
* URL van de pakket locatie: dit is de URL naar het Azure Storage-account dat de bestands naam voor het pakket bevat die hierboven is opgegeven. Dit is de URL die u in stap 9 van de bovenstaande sectie hebt gemaakt.
* Is er meer dan één CRM-pakket in uw pakket bestand: Selecteer **Ja als** u meerdere versies van CRM met verschillende pakketten ondersteunt. Voor de meeste partners is dit nee. Als u Ja selecteert, moet u AppSource-pakketten maken voor elke versie van uw oplossing. _Opmerking: er wordt niet gevraagd of u meerdere **zip** -bestanden hebt. Als u meerdere zip-bestanden van de oplossing hebt, maar slechts één versie hebt, moet u nog steeds ' nee ' selecteren. Het hulp programma voor het pakket brengt deze samen in automatisch._

### <a name="crm-package-availability"></a>Beschik baarheid van CRM-pakket

In deze sectie selecteert u welke regio's van CRM uw pakket beschikbaar wordt gemaakt. Ga voor meer informatie over welke CRM-regio's dienst doen op welke landen/regio's, de koppeling: [https://o365datacentermap.azurewebsites.net/](https://o365datacentermap.azurewebsites.net/)

Opmerking: het implementeren van een soevereine en US Gov Cloud in Duitsland vereist speciale machtigingen en validatie tijdens de certificering

## <a name="storefront-details"></a>Details van de winkel

### <a name="offer-summary"></a>Overzicht van aanbieding

Dit is een samen vatting van de toegevoegde waarde van uw aanbieding. Deze wordt weer gegeven op de zoek pagina van uw aanbieding. Dit mag Maxi maal 100 tekens lang zijn.

### <a name="offer-description"></a>Beschrijving van aanbieding

Dit is de beschrijving die wordt weer gegeven op de detail pagina van uw app. Maxi maal toegestaan aantal is 1300 tekens

### <a name="industries"></a>Bedrijfstakken

Selecteer de branche waarin uw app het beste is uitgelijnd. Als uw app is gekoppeld aan meerdere branches, kunt u dit veld leeg laten.

### <a name="categories"></a>Categorieën

Selecteer de categorieën die relevant zijn voor uw app. Selecteer een maximum van 3.

### <a name="app-type"></a>App-type

Selecteer het type proef versie dat door uw app wordt ingeschakeld op AppSource. ' Gratis ' betekent dat uw app gratis is. ' Proef versie ' betekent dat klanten uw app voor een korte periode op AppSource kunnen proberen. De aanvraag voor een proef versie wordt niet ondersteund voor Dynamics 365 voor klant engagement-apps. Selecteer deze optie niet.

### <a name="help-link-for-your-app"></a>Help-koppeling voor uw app

Voer de URL naar een pagina met Help-informatie voor uw app in.

### <a name="supported-countriesregions"></a>Ondersteunde landen/regio's

Dit veld bepaalt in welke landen/regio's uw aanbieding beschikbaar is voor proef versie.

### <a name="supported-languages"></a>Ondersteunde talen

Selecteer de talen die door uw app worden ondersteund. Als uw app ondersteuning biedt voor extra talen die niet in deze lijst staan, kunt u door gaan met het publiceren van uw aanbieding en ons een e-mail sturen naar: [appsource@microsoft.com](mailto:appsource@microsoft.com) om ons te laten weten.

### <a name="app-version"></a>App-versie

Het versie nummer voor uw app invoeren

### <a name="app-release-date"></a>Release datum van de app

Voer de release datum voor uw app in

### <a name="products-your-app-works-with-max-3"></a>Producten waarmee uw app werkt (Maxi maal 3)

Lijst met specifieke producten waarmee uw app werkt. U kunt Maxi maal drie producten weer geven. Als u een product wilt weer geven, klikt u op het plus teken (naast nieuw) en wordt er een nieuw veld voor open tekst gemaakt waarin u de naam van een product kunt invoeren waarmee uw app werkt.

### <a name="search-keywords-max-3"></a>Tref woorden zoeken (Maxi maal 3)

Met AppSource kan de klant zoeken op basis van tref woorden. U kunt de set tref woorden invoeren waarvoor uw toepassing wordt weer gegeven aan de klanten.

Als de toepassing bijvoorbeeld e-mail berichten van mijn E-mail verzendt, kan mail service enkele tref woorden zijn. Kies woorden die gebruikers waarschijnlijk zullen gebruiken om te zoeken naar uw app in het zoekvak van AppSource.

### <a name="hide-key"></a>Sleutel verbergen

Dit is een sleutel die wordt gecombineerd met de Preview-URL van de aanbieding om deze te verbergen in de open bare weer gave. Het is geen wacht woord. U kunt hier een wille keurige teken reeks invoeren.

### <a name="offer-logo-png-format-48x48"></a>Logo van aanbieding (PNG-indeling, 48x48)

Dit wordt weer gegeven op de zoek pagina van de app. **Alleen de PNG-indeling is toegestaan.** Een PNG-afbeelding uploaden met een resolutie van 48PX\*48PX

### <a name="offer-logo-png-format-216x216"></a>Logo van aanbieding (PNG-indeling, 216x216)

Dit wordt weer gegeven op de detail pagina van uw app. **Alleen de PNG-indeling is toegestaan.** Een PNG-afbeelding uploaden met een resolutie van 216PX\*216PX

### <a name="videos"></a>Video's

U kunt Maxi maal vier Video's uploaden. Voor elke video die u wilt uploaden, moet u de naam van de video, de URL (alleen YouTube of Vimeo) en de miniatuur die u aan de video wilt koppelen, invullen. De miniatuur moet de PNG-indeling hebben en moet 1280PX\*720PX zijn. Klik op het plus teken om nieuwe video (s) toe te voegen. Video miniaturen worden weer gegeven op de detail pagina van uw app.

### <a name="documents"></a>Documenten

U kunt Maxi maal drie documenten uploaden in PDF-indeling. Voor elk document dat u wilt uploaden, moet u de naam van het document invullen en het document uploaden. Het document moet een PDF-indeling hebben.

Klik op het plus teken om een nieuw (e) document (en) toe te voegen

### <a name="screenshots"></a>Schermopnamen

Dit zijn scherm afbeeldingen die worden weer gegeven op de detail pagina AppSource voor uw app.

### <a name="privacy-policy"></a>Privacybeleid

Voer de URL naar het privacybeleid van uw app in

### <a name="terms-of-use"></a>Gebruiksvoorwaarden

Voer de gebruiks voorwaarden van uw app in. AppSource-klanten moeten deze voor waarden accepteren voordat ze uw app kunnen proberen

### <a name="support-url"></a>Ondersteunings-URL

Voer de ondersteunings-URL voor uw app in.

### <a name="lead-destination"></a>Doel van de lead

Selecteer een CRM-systeem waar u een lead wilt opslaan. Selecteer hier ' Azure Table ' als u een van de volgende CRM-systemen hebt: Sales Force, Marketo, micro soft Dynamics CRM. Het CRM-systeem dat u hier selecteert, is informatie over de eind gebruikers die uw app proberen op AppSource (leads). Afhankelijk van het CRM-systeem dat u selecteert, klikt u op de bijbehorende URL hieronder voor informatie over het volt ooien van de volgende set velden

* [Azure-tabel](./cloud-partner-portal-lead-management-instructions-azure-table.md)
* [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md)
* [Micro soft Dynamics CRM](./cloud-partner-portal-lead-management-instructions-dynamics.md)
* [Salesforce](./cloud-partner-portal-lead-management-instructions-salesforce.md)

## <a name="storefront-details"></a>Details van de winkel

De contact gegevens worden alleen gebruikt voor interne communicatie tussen de partner en micro soft. Opmerking: het is belang rijk dat u een e-mail adres gebruikt dat in deze velden wordt gecontroleerd. Dit e-mail adres wordt gebruikt om met u te communiceren tijdens het publiceren naar AppSource. Alleen de ondersteunings-URL is zichtbaar voor de klanten.
