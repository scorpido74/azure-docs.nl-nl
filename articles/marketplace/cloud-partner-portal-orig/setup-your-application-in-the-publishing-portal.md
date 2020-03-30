---
title: Uw toepassing instellen in de publicatieportal
description: Instructies voor het instellen van uw toepassing in de Cloud Publishing Portal.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 2676dbf72309eeb51be1f08e7bae2c1502cc671e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280198"
---
# <a name="setup-your-application-in-the-publishing-portal"></a>Uw toepassing instellen in de publicatieportal

U bent nu klaar om uw aanvraag in te stellen in de publicatieportal.

## <a name="login-and-create-a-new-offer"></a>Inloggen en een nieuwe aanbieding maken

1. Meld u aan bij de [Cloud Partner Portal](https://cloudpartner.azure.com/).
2. Klik op de linkernavigatiebalk op '+ Nieuwe aanbieding' en selecteer 'Dynamics 365 voor klantbetrokkenheid'.

![Een nieuw aanbod selecteren](./media/CRMScreenShot14.png)

1. Een nieuwe aanbieding "Editor" weergave is nu geopend voor u, en we zijn klaar om te beginnen met authoring.

![Nieuw aanbiedingsscherm](./media/CRMScreenShot15.png)

1. De "formulieren" die moeten worden ingevuld zijn links zichtbaar in de weergave 'Editor'. Elk "formulier" bestaat uit een reeks velden die moeten worden ingevuld. Vereiste velden zijn gemarkeerd met een\*rood sterretje ( ).

Er zijn vier belangrijke formulieren voor het ontwerpen van een Dynamics 365 voor Customer Engagement-aanbieding

* Aanbiedingsinstellingen
* Technische informatie
* Storefront-gegevens
* Contactpersonen

## <a name="fill-out-the-offer-settings-form"></a>Het formulier Aanbiedingsinstellingen invullen

Het formulier aanbiedingsinstellingen is een basisformulier om de aanbiedingsinstellingen op te geven. De verschillende velden worden hieronder beschreven.

### <a name="offer-id"></a>Aanbiedings-id

Dit is een unieke id voor de aanbieding binnen een uitgeversprofiel. Deze ID is zichtbaar in product-URL's. De id kan alleen bestaan uit alfanumerieke tekens in kleine letters en streepjes (-). De ID kan niet eindigen in een streepje en kan maximaal 50 tekens hebben. Dit veld is vergrendeld zodra een aanbieding live gaat.

Als een uitgever uitgevers **'contoso'** bijvoorbeeld een aanbieding maakt met aanbiedings-ID **'sample-WebApp',**\/wordt deze in AppSource weergegeven als 'https: /appsource.microsoft.com/marketplace/apps/contoso.sample-WebApp?tab=Overview'

### <a name="publisher-id"></a>Publisher-id

Met deze vervolgkeuzelijst u het uitgeversprofiel kiezen waarop u deze aanbieding wilt publiceren. Dit veld is vergrendeld zodra een aanbieding live gaat.

### <a name="name"></a>Name

Dit is de weergavenaam voor uw aanbieding. Dit is de naam die wordt weergegeven in [AppSource.](https://appsource.microsoft.com/) De naam mag maximaal 50 tekens bevatten.

Klik op 'Opslaan' om je voortgang op te slaan. De volgende stap zou zijn om technische informatie toe te voegen voor uw aanbieding.

## <a name="fill-out-the-technical-info-form"></a>Het formulier Technische info invullen


Het technische infoformulier is waar u informatie invult die specifiek is voor uw Dynamics 365 for Customer Engagement-oplossing. Zweven over de zal u presenteren met meer informatie. Zie het voorbeeld hieronder.

![Scherm technische informatie](./media/CRMScreenShot16.png)

### <a name="application-info"></a>Toepassingsgegevens

De meeste uitgevers verlaten deze velden met de standaardwaarden, Gebruiker, Nee, Nee en een lege URL voor toepassingsconfiguratie volgens de bovenstaande schermafbeelding.

### <a name="crm-package"></a>CRM-pakket

![CRM-pakketgegevens](./media/CRMScreenShot17.png)

Hier is een verklaring voor deze velden:

* Bestandsnaam van uw pakket: de bestandsnaam die u in de bovenstaande stap hebt gemaakt bij het maken van het zip-bestand dat uw CRM AppSource-pakket is. In het bovenstaande voorbeeld is\_dit "Microsoft SamplePackage.zip".
* Url van uw pakketlocatie: dit is de URL naar het Azure Storage-account met de hierboven opgegeven pakketbestandsnaam. Het is de URL die is gemaakt in stap 9 van de sectie hierboven.
* Is er meer dan één crm-pakket in uw pakketbestand: Selecteer **Alleen** ja als u meerdere versies van crm ondersteunt met verschillende pakketten. Voor de meeste partners zal dit "Nee" zijn. Als u Ja selecteert, moet u appsourcepakketten maken voor elke versie van uw oplossing. _Opmerking: Dit is niet vragen of je meerdere **zip-bestanden** hebt. Als u meerdere solution.zip-bestanden hebt, maar slechts één versie, moet u nog steeds 'Nee' selecteren. De verpakkingstool brengt deze automatisch samen._

### <a name="crm-package-availability"></a>Beschikbaarheid CRM-pakket

Selecteer in deze sectie welke regio's van CRM uw pakket beschikbaar worden gesteld. Zie de link voor informatie over welke CRM-regio's welke landen/regio's dienen:[https://o365datacentermap.azurewebsites.net/](https://o365datacentermap.azurewebsites.net/)

Opmerking: Implementeren naar Duitsland "Sovereign and US Gov Cloud" Sovereign vereist speciale toestemming en validatie tijdens certificering

## <a name="storefront-details"></a>Storefront-gegevens

### <a name="offer-summary"></a>Overzicht van aanbiedingen

Dit is een samenvatting van de waardepropositie van uw aanbod. Deze wordt weergegeven op de zoekpagina van uw aanbieding. Het moet een maximum van 100 tekens.

### <a name="offer-description"></a>Aanbiedingsbeschrijving

Dit is de beschrijving die wordt weergegeven op de pagina met app-details. Maximaal toegestaan is 1300 tekens

### <a name="industries"></a>Bedrijfstakken

Selecteer de branche waarop uw app het best is uitgelijnd. Als uw app betrekking heeft op meerdere bedrijfstakken, u dit leeg laten.

### <a name="categories"></a>Categorieën

Selecteer de categorieën die relevant zijn voor uw app. Selecteer maximaal 3.

### <a name="app-type"></a>App-type

Selecteer het type proefversie dat uw app inschakelt op AppSource. 'Gratis' betekent dat uw app gratis is. 'Proef' betekent dat klanten uw app voor een korte periode kunnen uitproberen op AppSource. 'Request for trial' wordt niet ondersteund voor Dynamics 365 voor Customer Engagement-apps. Selecteer deze optie niet.

### <a name="help-link-for-your-app"></a>Help-koppeling voor uw app

Voer URL in op een pagina met help-gerelateerde informatie voor uw app.

### <a name="supported-countriesregions"></a>Ondersteunde landen/regio's

Dit veld bepaalt de landen/regio's waarin uw aanbieding beschikbaar is voor proefversie.

### <a name="supported-languages"></a>Ondersteunde talen

Selecteer de talen die uw app ondersteunt. Als uw app extra talen ondersteunt die niet in deze lijst staan, blijft u uw aanbieding publiceren en ons een e-mail sturen op: [appsource@microsoft.com](mailto:appsource@microsoft.com) om ons dit te laten weten.

### <a name="app-version"></a>App-versie

Voer het versienummer voor uw app in

### <a name="app-release-date"></a>Releasedatum app

Voer de releasedatum voor uw app in

### <a name="products-your-app-works-with-max-3"></a>Producten waarmee uw app werkt (Max 3)

Lijstspecifieke producten waarmee uw app werkt. U maximaal drie producten vermelden. Als u een product wilt aanbieden, klikt u op het plusteken (naast nieuw) en wordt er een nieuw open tekstveld gemaakt waarmee u de naam van een product invoeren waarmee uw app werkt.

### <a name="search-keywords-max-3"></a>Zoekzoekwoorden (Max 3)

AppSource stelt klanten in staat om te zoeken op basis van zoekwoorden. U de set zoekwoorden invoeren waarvoor uw toepassing aan de klanten wordt getoond.

Als de toepassing bijvoorbeeld e-mails 'Mijn e-mailservice' is, kunnen e-mailservice, e-mailservice enkele zoekwoorden zijn. Kies woorden die gebruikers waarschijnlijk gebruiken om naar uw app te zoeken in het zoekvak AppSource.

### <a name="hide-key"></a>Sleutel verbergen

Dit is een sleutel die wordt gecombineerd met de URL van de aanbiedingsvoorbeeldom deze te verbergen voor de openbare weergave. Het is geen wachtwoord. U hier elk touwtje invoeren.

### <a name="offer-logo-png-format-48x48"></a>Aanbiedingslogo (png-indeling, 48x48)

Dit wordt weergegeven op de zoekpagina van uw app. **Alleen png-indeling is toegestaan.** Een png-afbeelding uploaden met\*een resolutie van 48PX 48PX

### <a name="offer-logo-png-format-216x216"></a>Aanbiedingslogo (png-indeling, 216x216)

Dit wordt weergegeven op de detailpagina van uw app. **Alleen png-indeling is toegestaan.** Een png-afbeelding uploaden met\*een resolutie van 216PX 216PX

### <a name="videos"></a>Video's

Je maximaal vier video's uploaden. Voor elke video die je wilt uploaden, moet je de videonaam, URL (alleen YouTube of Vimeo) en Thumbnail invullen om aan de video te koppelen. Thumbnail moet in png-indeling zijn en\*moet 1280PX 720PX zijn. Als u nieuwe video's wilt toevoegen, klikt u op het plusteken. De miniatuur(s) van video's worden weergegeven op de detailpagina van uw app.

### <a name="documents"></a>Documenten

U maximaal drie documenten uploaden in PDF-formaat. Voor elk document dat u wilt uploaden, moet u de naam van het document invullen en het document uploaden. Het document moet in pdf-formaat zijn.

Als u nieuwe documenten wilt toevoegen, klikt u op het plusteken

### <a name="screenshots"></a>Schermopnamen

Dit zijn screenshots die worden weergegeven op de detailpagina AppSource voor uw app.

### <a name="privacy-policy"></a>Privacybeleid

URL invoeren in het privacybeleid van uw app

### <a name="terms-of-use"></a>Gebruiksvoorwaarden

Voer de gebruiksvoorwaarden van uw app in. AppSource-klanten moeten deze voorwaarden accepteren voordat ze uw app kunnen proberen

### <a name="support-url"></a>URL voor ondersteuning

Voer de ondersteunings-URL voor uw app in.

### <a name="lead-destination"></a>Hoofdbestemming

Selecteer een CRM-systeem waar u lead zal worden opgeslagen. Selecteer hier 'Azure Table' als u een van de volgende CRM-systemen hebt: Salesforce, Marketo, Microsoft Dynamics CRM. Het CRM-systeem dat u hier selecteert, is waar we details schrijven van eindgebruikers die uw app op AppSource (leads) proberen. Afhankelijk van het CRM-systeem dat u selecteert, klikt u op de bijbehorende URL hieronder voor informatie over het invullen van de volgende reeks velden

* [Azure-tabel](./cloud-partner-portal-lead-management-instructions-azure-table.md)
* [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md)
* [Microsoft Dynamics CRM](./cloud-partner-portal-lead-management-instructions-dynamics.md)
* [Salesforce](./cloud-partner-portal-lead-management-instructions-salesforce.md)

## <a name="storefront-details"></a>Storefront-gegevens

De contactgegevens worden alleen gebruikt voor interne communicatie tussen de partner en Microsoft. Opmerking: Het is belangrijk om een e-mailadres te gebruiken dat in deze velden wordt gecontroleerd. We gebruiken deze e-mail om met u te communiceren over uw voortgang bij het publiceren naar AppSource. Alleen de ondersteunings-URL is zichtbaar voor de klanten.
