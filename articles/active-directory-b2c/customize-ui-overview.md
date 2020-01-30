---
title: De gebruikersinterface aanpassen
titleSuffix: Azure AD B2C
description: Meer informatie over het aanpassen van de gebruikers interface voor uw toepassingen die gebruikmaken van Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: d14e6f98f49f112c8b20abec573b48c3b12705db
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76841230"
---
# <a name="customize-the-user-interface-in-azure-active-directory-b2c"></a>De gebruikers interface in Azure Active Directory B2C aanpassen

De gebruikers interface die Azure Active Directory B2C (Azure AD B2C) aan uw klanten wordt weer gegeven, kunt u voorzien van een naadloze gebruikers ervaring in uw toepassing. Deze ervaring omvat het aanmelden, aanmelden, het bewerken van profielen en het opnieuw instellen van wacht woorden. In dit artikel worden de methoden voor het aanpassen van gebruikers interface (UI) geïntroduceerd voor gebruikers stromen en aangepaste beleids regels.

## <a name="ui-customization-in-different-scenarios"></a>UI-aanpassing in verschillende scenario's

Er zijn verschillende manieren voor het aanpassen van de gebruikers interface van de gebruiker, die voor verschillende scenario's van toepassing is.

### <a name="user-flows"></a>Gebruikersstromen

Als u gebruik maakt van [gebruikers stromen](user-flow-overview.md), kunt u het uiterlijk van de pagina's van de gebruikers stroom wijzigen met behulp van ingebouwde *sjablonen voor pagina-indeling*of door gebruik te maken van uw eigen HTML en CSS. Beide methoden worden verderop in dit artikel besproken.

U gebruikt de [Azure Portal](tutorial-customize-ui.md) voor het configureren van de UI-aanpassing voor gebruikers stromen.

### <a name="custom-policies"></a>Aangepast beleid

Als u [aangepaste beleids regels](custom-policy-overview.md) gebruikt voor het aanmelden of aanmelden, het opnieuw instellen van wacht woorden of het bewerken van profielen in uw toepassing, gebruikt u [beleids bestanden om de gebruikers interface aan te passen](custom-policy-ui-customization.md).

Als u dynamische inhoud op basis van de beslissing van een klant wilt opgeven, gebruikt u aangepaste beleids regels die de [pagina-inhoud dynamisch kunnen wijzigen](custom-policy-ui-customization-dynamic.md) , afhankelijk van een para meter die wordt verzonden in een query reeks. U kunt bijvoorbeeld de achtergrond afbeelding wijzigen op de Azure AD B2C registratie-of aanmeldings pagina op basis van een para meter die u doorgeeft vanuit uw web-of mobiele toepassing.

### <a name="javascript"></a>JavaScript

U kunt Java script-code aan de client zijde inschakelen in zowel [gebruikers stromen](user-flow-javascript-overview.md) als [aangepaste beleids regels](page-layout.md).

### <a name="sign-in-only-ui-customization"></a>Aangepaste UI-aanpassing

Als u zich alleen aanmeldt, samen met de bijbehorende pagina voor het opnieuw instellen van wacht woorden en verificatie-e-mails, gebruikt u dezelfde aanpassings stappen die worden gebruikt voor een [aanmeldings pagina van Azure AD](../active-directory/fundamentals/customize-branding.md).

Als klanten proberen hun profiel te bewerken voordat ze zich aanmelden, worden ze omgeleid naar een pagina die u aanpast door gebruik te maken van dezelfde stappen die worden gebruikt voor het aanpassen van de aanmeldings pagina van Azure AD.

## <a name="page-layout-templates"></a>Sjablonen voor pagina-indeling

Gebruikers stromen bieden verschillende ingebouwde sjablonen waaruit u kunt kiezen om uw gebruikers ervaring-pagina's een professioneel uiterlijk te geven. Deze indelings sjablonen kunnen ook worden gebruikt als uitgangs punt voor uw eigen aanpassing.

Onder **aanpassen** in het menu links selecteert u **pagina-indelingen** en selecteert u vervolgens **sjabloon**.

![Vervolg keuzelijst sjabloon selectie in de pagina gebruikers stroom van Azure Portal](media/customize-ui-overview/template-selection.png)

Selecteer vervolgens een sjabloon in de lijst. Hier volgen enkele voor beelden van de aanmeldings pagina's voor elke sjabloon:

| Oceaan blauw | Pastel grijs | Klassiek |
|:-:|:-:|:-:|
|![Voor beeld van de sjabloon oceaan blauw die wordt weer gegeven op de aanmeldings pagina voor aanmelden](media/customize-ui-overview/template-ocean-blue.png)|![Voor beeld van de sjabloon pastel grijs weer gegeven op de aanmeldings pagina voor aanmelden](media/customize-ui-overview/template-slate-gray.png)|![Voor beeld van de klassieke sjabloon die wordt weer gegeven op de aanmeldings pagina voor aanmelden](media/customize-ui-overview/template-classic.png)|

Wanneer u een sjabloon kiest, wordt de geselecteerde indeling toegepast op alle pagina's in uw gebruikers stroom en de URI voor elke pagina wordt weer gegeven in het veld **aangepaste pagina-URI** .

## <a name="custom-html-and-css"></a>Aangepaste HTML en CSS

Als u uw eigen beleids indeling wilt ontwerpen met uw aangepaste HTML en CSS, kunt u dit doen door de wissel knop ' aangepaste pagina-inhoud gebruiken ' in te scha kelen voor elk van de indelings namen die in uw beleid aanwezig zijn. Volg de onderstaande instructies met betrekking tot de aangepaste indelings configuraties:

Azure AD B2C code wordt uitgevoerd in de browser van uw klant door gebruik te maken van een aanpak genaamd [Cross-Origin Resource Sharing (CORS)](https://www.w3.org/TR/cors/).

Tijdens runtime wordt inhoud geladen vanuit een URL die u opgeeft in uw gebruikers stroom of aangepast beleid. Elke pagina in de gebruikers ervaring laadt de inhoud van de URL die u voor die pagina opgeeft. Nadat de inhoud is geladen vanuit uw URL, wordt deze samengevoegd met een HTML-fragment dat is ingevoegd door Azure AD B2C, waarna de pagina wordt weer gegeven aan uw klant.

Lees de volgende richt lijnen voordat u uw eigen HTML-en CSS-bestanden gebruikt om de gebruikers interface aan te passen:

- Azure AD B2C HTML-inhoud in uw pagina's **samenvoegt** . Kopieer en probeer de standaard inhoud die Azure AD B2C levert niet te worden gewijzigd. Het is het beste om uw HTML-inhoud helemaal zelf te bouwen en de standaard inhoud als referentie te gebruiken.
- **Java script** kan worden opgenomen in uw aangepaste inhoud voor [gebruikers stromen](user-flow-javascript-overview.md) en [aangepaste beleids regels](javascript-samples.md).
- Ondersteunde **browser versies** zijn:
  - Internet Explorer 11, 10 en micro soft Edge
  - Beperkte ondersteuning voor Internet Explorer 9 en 8
  - Google Chrome 42,0 en hoger
  - Mozilla Firefox 38,0 en hoger
- Voeg geen **formulier Tags** toe aan uw HTML-bestand. Formulier Tags veroorzaken een conflict met de POST-bewerkingen die zijn gegenereerd door de HTML die is geïnjecteerd door Azure AD B2C.

### <a name="where-do-i-store-ui-content"></a>Waar kan ik de inhoud van de gebruikers interface opslaan?

Wanneer u uw eigen HTML-en CSS-bestanden gebruikt om de gebruikers interface aan te passen, kunt u de inhoud van de gebruikers interface hosten op een openbaar beschikbaar HTTPS-eind punt dat CORS ondersteunt. Bijvoorbeeld [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md), webservers, CDN'S, AWS S3 of het delen van bestanden.

Het belang rijk punt is dat u de inhoud host op een openbaar beschik bare HTTPS-eind punt waarvoor CORS is ingeschakeld. U moet een absolute URL gebruiken wanneer u deze in uw inhoud opgeeft.

## <a name="get-started-with-custom-html-and-css"></a>Aan de slag met aangepaste HTML en CSS

Ga aan de slag met uw eigen HTML en CSS op uw pagina's voor gebruikers ervaring door deze richt lijnen te volgen.

- Maak goed opgemaakte HTML-inhoud met een leeg `<div id="api"></div>`-element ergens in de `<body>`. Dit element markeert waar de Azure AD B2C inhoud wordt ingevoegd. In het volgende voor beeld ziet u een minimale pagina:

    ```html
    <!DOCTYPE html>
    <html>
      <head>
        <title>!Add your title here!</title>
        <link rel="stylesheet" href="https://mystore1.blob.core.windows.net/b2c/style.css">
      </head>
      <body>
        <h1>My B2C Application</h1>
        <div id="api"></div>   <!-- Leave this element empty because Azure AD B2C will insert content here. -->
      </body>
    </html>
    ```

- Gebruik CSS om de UI-elementen die Azure AD B2C invoegen in uw pagina, te opmaken. In het volgende voor beeld ziet u een eenvoudig CSS-bestand dat ook instellingen bevat voor de door aanmeldingen geïnjecteerde HTML-elementen:

    ```css
    h1 {
      color: blue;
      text-align: center;
    }
    .intro h2 {
      text-align: center;
    }
    .entry {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    .divider h2 {
      text-align: center;
    }
    .create {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    ```

- Host uw inhoud op een HTTPS-eind punt (met CORS toegestaan). De GET-en OPTIONS-aanvraag methoden moeten zijn ingeschakeld bij het configureren van CORS.
- Een gebruikers stroom of aangepast beleid maken of bewerken om de inhoud te gebruiken die u hebt gemaakt.

### <a name="html-fragments-from-azure-ad-b2c"></a>HTML-fragmenten van Azure AD B2C

De volgende tabel bevat de HTML-fragmenten die Azure AD B2C worden samengevoegd in het `<div id="api"></div>`-element dat zich in uw inhoud bevindt.

| Pagina ingevoegd | Beschrijving van HTML |
| ------------- | ------------------- |
| ID-provider selecteren | Bevat een lijst met knoppen voor id-providers die de klant kan kiezen tijdens het registreren of aanmelden. Deze knoppen bevatten sociale id-providers zoals Facebook, Google of lokale accounts (op basis van e-mail adres of gebruikers naam). |
| Lokaal account registreren | Bevat een formulier voor het registreren van een lokaal account op basis van een e-mail adres of een gebruikers naam. Het formulier kan verschillende invoer besturings elementen bevatten, zoals een tekstinvoervak, een vak voor het invoeren van een wacht woord, keuze rondje, vervolg keuze vakjes en selectie vakjes met meerdere selecties. |
| Aanmelding voor sociaal account | Kan worden weer gegeven wanneer u zich aanmeldt met een bestaand account van een id-provider voor sociale netwerken, zoals Facebook of Google. Dit wordt gebruikt wanneer aanvullende informatie van de klant moet worden verzameld met behulp van een aanmeldings formulier. |
| Unified Sign-up of aanmelden | Hiermee worden zowel registratie als gebruikers met sociale id-providers, zoals Facebook-, Google-of lokale accounts, afgehandeld. |
| Multi-Factor Authentication | Klanten kunnen hun telefoon nummers (met behulp van tekst of spraak) verifiëren tijdens het registreren of aanmelden. |
| Fout | Geeft fout informatie aan de klant. |

## <a name="localize-content"></a>Inhoud lokaliseren

U kunt uw HTML-inhoud lokaliseren door [taal aanpassing](user-flow-language-customization.md) in te scha kelen in uw Azure AD B2C-Tenant. Als u deze functie inschakelt, kunnen Azure AD B2C de OpenID Connect Connect-para meter `ui-locales` naar uw eind punt door sturen. De inhouds server kan deze para meter gebruiken om taalspecifieke HTML-pagina's op te geven.

Inhoud kan vanaf verschillende locaties worden opgehaald op basis van de land instelling die wordt gebruikt. In het eind punt waarvoor CORS is ingeschakeld, stelt u een mapstructuur in om inhoud voor specifieke talen te hosten. U roept de juiste waarde aan als u de Joker teken `{Culture:RFC5646}`gebruikt.

Uw aangepaste pagina-URI kan er bijvoorbeeld als volgt uitzien:

```HTTP
https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html
```

U kunt de pagina in het Frans laden door inhoud op te halen uit:

```HTTP
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="examples"></a>Voorbeelden

U kunt verschillende voorbeeld sjabloon bestanden vinden in de [B2C-AzureBlobStorage-client](https://github.com/azureadquickstarts/b2c-azureblobstorage-client) opslagplaats op github.

De voorbeeld HTML-en CSS-bestanden in de sjablonen bevinden zich in de map [/sample_templates](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates) .

## <a name="next-steps"></a>Volgende stappen

- Als u gebruik maakt van **gebruikers stromen**, kunt u uw gebruikers interface aanpassen met behulp van de zelf studie:

    [Pas de gebruikers interface van uw toepassingen aan in azure Active Directory B2C](tutorial-customize-ui.md).
- Als u **aangepast beleid**gebruikt, kunt u de gebruikers interface nu aanpassen met het artikel:

    [Pas de gebruikers interface van uw toepassing aan met behulp van een aangepast beleid in azure Active Directory B2C](custom-policy-ui-customization.md).
