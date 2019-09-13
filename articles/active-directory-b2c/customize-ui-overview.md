---
title: Over het aanpassen van de gebruikers interface in Azure Active Directory B2C | Microsoft Docs
description: Meer informatie over het aanpassen van de gebruikers interface voor uw toepassingen die gebruikmaken van Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 5ae12742c2ad50d5bf1caaf14ae2f6d34bd6d3a2
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70880781"
---
# <a name="about-user-interface-customization-in-azure-active-directory-b2c"></a>Over het aanpassen van de gebruikers interface in Azure Active Directory B2C

De mogelijkheid om u aan te merken en de gebruikers interface (UI) te aanpassen die door Azure Active Directory B2C (Azure AD B2C) wordt gebruikt voor uw toepassingen is belang rijk voor een naadloze ervaring met uw klant. Deze ervaring omvat het aanmelden, aanmelden, profiel bewerking en het opnieuw instellen van het wacht woord. Dit artikel bevat informatie om u te helpen bij het aanpassen van de gebruikers interface van uw toepassingen.

Afhankelijk van uw behoeften op het gebied van deze ervaringen, past u de gebruikers interface van uw toepassing op verschillende manieren aan. Bijvoorbeeld:

- Als u gebruik maakt van [gebruikers stromen](active-directory-b2c-reference-policies.md) voor registratie, het opnieuw instellen van wacht woorden of het bewerken van profielen in uw toepassing, gebruikt u de [Azure Portal om de gebruikers interface aan te passen](tutorial-customize-ui.md).
- Als u een v2-gebruikers stroom gebruikt, kunt u een [sjabloon voor pagina-indeling](#page-layout-templates) gebruiken om het uiterlijk van uw gebruikers stroom pagina's te wijzigen zonder verdere aanpassingen. U kunt bijvoorbeeld een blauw of Pastel-grijs thema van Oceaan Toep assen op alle pagina's in uw gebruikers stroom.
- Als u zich alleen aanmeldt, de bijbehorende pagina voor het opnieuw instellen van wacht woorden en verificatie-e-mails, gebruikt u dezelfde aanpassings stappen die worden gebruikt voor een [aanmeldings pagina van Azure AD](../active-directory/fundamentals/customize-branding.md).
- Als klanten proberen hun profiel te bewerken voordat ze zich aanmelden, worden ze omgeleid naar een pagina die u aanpast met dezelfde stappen die worden gebruikt voor het aanpassen van de aanmeldings pagina voor Azure AD.
- Als u [aangepaste beleids regels](active-directory-b2c-overview-custom.md) gebruikt voor het aanmelden of aanmelden, het opnieuw instellen van wacht woorden of het bewerken van profielen in uw toepassing, gebruikt u [beleids bestanden om de gebruikers interface aan te passen](active-directory-b2c-ui-customization-custom.md).
- Als u dynamische inhoud op basis van de beslissing van een klant wilt opgeven, gebruikt u [aangepaste beleids regels die pagina-inhoud kunnen wijzigen](active-directory-b2c-ui-customization-custom-dynamic.md) , afhankelijk van een para meter die in een query reeks wordt verzonden. Zo wordt de achtergrond afbeelding op de Azure AD B2C registratie-of aanmeldings pagina gewijzigd, op basis van een para meter die u van uw web-of mobiele toepassing doorgeeft.
- U kunt Java script-client-side code inschakelen in uw Azure AD B2C [gebruikers stromen](user-flow-javascript-overview.md) of [aangepaste beleids regels](page-layout.md).

Azure AD B2C code wordt uitgevoerd in de browser van uw klant en maakt gebruik van een moderne aanpak [(CORS (cross-Origin Resource Sharing))](https://www.w3.org/TR/cors/). Tijdens runtime wordt inhoud geladen vanuit een URL die u opgeeft in een gebruikers stroom of beleid. U geeft verschillende Url's voor verschillende pagina's op. Nadat de inhoud is geladen vanuit uw URL, wordt deze samengevoegd met een HTML-fragment ingevoegd vanuit Azure AD B2C en vervolgens weer gegeven aan uw klant.

Wanneer u uw eigen HTML-en CSS-bestanden gebruikt om de gebruikers interface aan te passen, raadpleegt u de volgende richt lijnen voordat u begint:

- Azure AD B2C HTML-inhoud in uw pagina's samenvoegt. Kopieer en probeer de standaard inhoud die Azure AD B2C levert niet te worden gewijzigd. Het is het beste om uw HTML-inhoud helemaal zelf te bouwen en de standaard inhoud als referentie te gebruiken.
- Java script kan nu worden opgenomen in de aangepaste inhoud.
- Ondersteunde browser versies zijn:
    - Internet Explorer 11, 10 en micro soft Edge
    - Beperkte ondersteuning voor Internet Explorer 9 en 8
    - Google Chrome 42,0 en hoger
    - Mozilla Firefox 38,0 en hoger
- Zorg ervoor dat u geen formulier Tags opneemt in uw HTML, omdat deze de POST-bewerkingen veroorzaakt door de geïnjecteerde HTML van Azure AD B2C.

## <a name="page-layout-templates"></a>Sjablonen voor pagina-indeling

Voor v2-gebruikers stromen kunt u een vooraf ontworpen sjabloon kiezen waarmee uw standaard pagina's beter te zien zijn en als goede basis fungeren voor uw eigen aanpassingen.

Selecteer in het menu links onder **aanpassen**de optie **pagina-indelingen**. Selecteer vervolgens **sjabloon (preview)** .

![Vervolg keuzelijst sjabloon selectie in de pagina gebruikers stroom van Azure Portal](media/customize-ui-overview/template.png)

Selecteer een sjabloon in de lijst. Zo past de sjabloon **blauw** de volgende indeling toe op de pagina's van de gebruikers stroom:

![Voor beeld van de sjabloon oceaan blauw die wordt weer gegeven op de aanmeldings pagina voor aanmelden](media/customize-ui-overview/ocean-blue.png)

Wanneer u een sjabloon kiest, wordt de geselecteerde indeling toegepast op alle pagina's in uw gebruikers stroom en de URI voor elke pagina wordt weer gegeven in het veld **aangepaste pagina-URI** .

## <a name="where-do-i-store-ui-content"></a>Waar kan ik de inhoud van de gebruikers interface opslaan?

Wanneer u uw eigen HTML-en CSS-bestanden gebruikt om de gebruikers interface aan te passen, kunt u uw gebruikers interface-inhoud overal hosten, zoals op [Azure Blob-opslag](../storage/blobs/storage-blobs-introduction.md), webservers, CDN'S, AWS S3 of systemen voor het delen van bestanden. Het belang rijk punt is dat u de inhoud host op een openbaar beschik bare HTTPS-eind punt waarvoor CORS is ingeschakeld. U moet een absolute URL gebruiken wanneer u deze in uw inhoud opgeeft.

## <a name="how-do-i-get-started"></a>Hoe ga ik aan de slag?

U kunt het volgende doen om de gebruikers interface aan te passen:

- Maak goed opgemaakte HTML-inhoud met `<div id="api"></div>` een leeg element ergens in `<body>`de. Dit element markeert waar de Azure AD B2C inhoud wordt ingevoegd. In het volgende voor beeld ziet u een minimale pagina:

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

- Host uw inhoud op een HTTPS-eind punt (met CORS toegestaan). De GET-en OPTIONS-aanvraag methoden moeten zijn ingeschakeld bij het configureren van CORS.
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

- Een beleid maken of bewerken om de inhoud te gebruiken die u hebt gemaakt.

De volgende tabel bevat de HTML-fragmenten die Azure AD B2C worden samengevoegd in het `<div id="api"></div>` element dat zich in uw inhoud bevindt.

| Pagina ingevoegd | Beschrijving van HTML |
| ------------- | ------------------- |
| ID-provider selecteren | Bevat een lijst met knoppen voor id-providers die de klant kan kiezen tijdens het registreren of aanmelden. Deze knoppen bevatten sociale id-providers zoals Facebook, Google of lokale accounts (op basis van e-mail adres of gebruikers naam). |
| Lokaal account registreren | Bevat een formulier voor het registreren van een lokaal account op basis van een e-mail adres of een gebruikers naam. Het formulier kan verschillende invoer besturings elementen bevatten, zoals een tekstinvoervak, een vak voor het invoeren van een wacht woord, keuze rondje, vervolg keuze vakjes en selectie vakjes met meerdere selecties. |
| Aanmelding voor sociaal account | Kan worden weer gegeven wanneer u zich aanmeldt met een bestaand account van een id-provider voor sociale netwerken, zoals Facebook of Google. Dit wordt gebruikt wanneer aanvullende informatie van de klant moet worden verzameld met behulp van een aanmeldings formulier. |
| Unified Sign-up of aanmelden | Hiermee worden zowel registratie als gebruikers met sociale id-providers, zoals Facebook-, Google-of lokale accounts, afgehandeld. |
| Multi-Factor Authentication | Klanten kunnen hun telefoon nummers (met behulp van tekst of spraak) verifiëren tijdens het registreren of aanmelden. |
| Fout | Geeft fout informatie aan de klant. |


## <a name="how-do-i-localize-content"></a>Hoe kan ik inhoud lokaliseren?

U kunt uw HTML-inhoud lokaliseren door [taal aanpassing](active-directory-b2c-reference-language-customization.md) in te scha kelen in uw Azure AD B2C-Tenant. Als u deze functie inschakelt, kunnen Azure AD B2C de `ui-locales` OpenID Connect Connect-para meter naar uw eind punt door sturen. De inhouds server kan deze para meter gebruiken om taalspecifieke HTML-pagina's op te geven.

Inhoud kan vanaf verschillende locaties worden opgehaald op basis van de land instelling die wordt gebruikt. In het eind punt waarvoor CORS is ingeschakeld, stelt u een mapstructuur in om inhoud voor specifieke talen te hosten. U roept de juiste naam aan als u de Joker waarde {Culture: RFC5646} gebruikt. Uw aangepaste pagina-URI kan er bijvoorbeeld als volgt `https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html`uitzien. U kunt de pagina in het Frans laden door inhoud op te halen uit`https://contoso.blob.core.windows.net/fr/myHTML/unified.html`

## <a name="examples"></a>Voorbeelden

Voor voor beelden van aanpassing kunt u deze [voorbeeld sjabloon bestanden](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip)downloaden en bekijken.

## <a name="next-steps"></a>Volgende stappen

- Als u gebruik maakt van **gebruikers stromen**, kunt u uw gebruikers interface aanpassen met behulp van de zelf studie:

    [Pas de gebruikers interface van uw toepassingen aan in azure Active Directory B2C](tutorial-customize-ui.md).
- Als u **aangepast beleid**gebruikt, kunt u de gebruikers interface nu aanpassen met het artikel:

    [Pas de gebruikers interface van uw toepassing aan met behulp van een aangepast beleid in azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md).
