---
title: De gebruikersinterface aanpassen
titleSuffix: Azure AD B2C
description: Meer informatie over het aanpassen van de gebruikersinterface voor uw toepassingen die Azure Active Directory B2C gebruiken.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 839e13dffc7d15b8cd258dd4b7dda6776223d052
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051738"
---
# <a name="customize-the-user-interface-in-azure-active-directory-b2c"></a>De gebruikersinterface aanpassen in Azure Active Directory B2C

Door de gebruikersinterface die Azure Active Directory B2C (Azure AD B2C) weergeeft aan uw klanten, te brandmerken en aan te passen, u een naadloze gebruikerservaring in uw toepassing bieden. Deze ervaringen omvatten aanmelden, aanmelden, profielbewerking en het opnieuw instellen van wachtwoorden. In dit artikel worden de methoden voor gebruikersinterface (UI) aangepast voor zowel gebruikersstromen als aangepaste beleidsregels.

## <a name="ui-customization-in-different-scenarios"></a>Aanpassing van de gebruikersinterface in verschillende scenario's

Er zijn verschillende manieren om de gebruikersinterface van de gebruikerservaringen van uw toepassing aan te passen, elk geschikt voor verschillende scenario's.

### <a name="user-flows"></a>Gebruikersstromen

Als u [gebruikersstromen](user-flow-overview.md)gebruikt, u het uiterlijk van uw gebruikersstroompagina's wijzigen met behulp van ingebouwde *pagina-indelingssjablonen*of met behulp van uw eigen HTML en CSS. Beide methoden worden later in dit artikel besproken.

U gebruikt de [Azure-portal](tutorial-customize-ui.md) om de aanpassing van de gebruikersinterface voor gebruikersstromen te configureren.

> [!TIP]
> Als u alleen het bannerlogo, de achtergrondafbeelding en de achtergrondkleur van uw gebruikersstroompagina's wilt wijzigen, u de functie [Bedrijfsbranding (voorbeeld)](#company-branding-preview) proberen die later in dit artikel wordt beschreven.

### <a name="custom-policies"></a>Aangepast beleid

Als u [aangepaste beleidsregels](custom-policy-overview.md) gebruikt om aanmelding of aanmelding, wachtwoordreset of profielbewerking in uw toepassing te bieden, gebruikt u [beleidsbestanden om de gebruikersinterface aan te passen.](custom-policy-ui-customization.md)

Als u dynamische inhoud moet bieden op basis van de beslissing van een klant, gebruikt u aangepast beleid dat [pagina-inhoud dynamisch](custom-policy-ui-customization.md#configure-dynamic-custom-page-content-uri) kan wijzigen, afhankelijk van een parameter die in een querytekenreeks wordt verzonden. U bijvoorbeeld de achtergrondafbeelding op de aanmeldings- of aanmeldingspagina van Azure AD B2C wijzigen op basis van een parameter die u passeert vanaf uw web- of mobiele toepassing.

### <a name="javascript"></a>JavaScript

U JavaScript-code aan clientzijde inschakelen in zowel [gebruikersstromen](user-flow-javascript-overview.md) als [aangepaste beleidsregels.](page-layout.md)

### <a name="sign-in-only-ui-customization"></a>Alleen in-- en gebruikersinterface aanpassen ondertekenen

Als u alleen aanmeldingsmeldingen verstrekt, samen met de bijbehorende pagina voor het opnieuw instellen van wachtwoorden en verificatie-e-mails, gebruikt u dezelfde aanpassingsstappen die worden gebruikt voor een [aanmeldingspagina van Azure AD.](../active-directory/fundamentals/customize-branding.md)

Als klanten proberen hun profiel te bewerken voordat ze zich aanmelden, worden ze doorgestuurd naar een pagina die u aanpast met dezelfde stappen die worden gebruikt voor het aanpassen van de aanmeldingspagina van Azure AD.

## <a name="page-layout-templates"></a>Sjablonen voor pagina-indeling

Gebruikersstromen bieden verschillende ingebouwde sjablonen waaruit u kiezen om uw gebruikerservaringspagina's een professionele uitstraling te geven. Deze lay-outsjablonen kunnen ook als uitgangspunt dienen voor uw eigen aanpassing.

Selecteer **pagina-indelingen** **onder Aanpassen** in het linkermenu en selecteer **Sjabloon**.

![Vervolgkeuzelijst voor sjabloonselectie op de pagina gebruikersstromen van Azure-portal](media/customize-ui-overview/template-selection.png)

Selecteer vervolgens een sjabloon in de lijst. Hier volgen voorbeelden van de aanmeldingspagina's voor elke sjabloon:

| Oceaanblauw | Leigrijs | Klassiek |
|:-:|:-:|:-:|
|![Voorbeeld van de sjabloon Ocean Blue die wordt weergegeven op de aanmeldingspagina](media/customize-ui-overview/template-ocean-blue.png)|![Voorbeeld van de sjabloon Leisteengrijs die wordt weergegeven op de aanmeldingspagina](media/customize-ui-overview/template-slate-gray.png)|![Voorbeeld van de klassieke sjabloon die wordt weergegeven op de aanmeldingspagina](media/customize-ui-overview/template-classic.png)|

Wanneer u een sjabloon kiest, wordt de geselecteerde indeling toegepast op alle pagina's in uw gebruikersstroom en is de URI voor elke pagina zichtbaar in het veld **Aangepaste pagina URI.**

## <a name="custom-html-and-css"></a>Aangepaste HTML en CSS

Als u uw eigen beleidsindeling wilt ontwerpen met uw aangepaste HTML en CSS, u dit doen door de schakeloptie 'Aangepaste pagina-inhoud gebruiken' in te schakelen voor elk van de indelingsnamen die in uw beleid aanwezig zijn. Volg de onderstaande instructies met betrekking tot de aangepaste lay-out configuraties:

Azure AD B2C voert code uit in de browser van uw klant met behulp van een aanpak genaamd [Cross-Origin Resource Sharing (CORS).](https://www.w3.org/TR/cors/)

Bij runtime wordt inhoud geladen vanuit een URL die u opgeeft in uw gebruikersstroom of aangepaste beleid. Elke pagina in de gebruikerservaring laadt de inhoud van de URL die u voor die pagina opgeeft. Nadat de inhoud is geladen vanuit uw URL, wordt deze samengevoegd met een HTML-fragment dat is ingevoegd door Azure AD B2C en vervolgens wordt de pagina weergegeven aan uw klant.

Bekijk de volgende richtlijnen voordat u uw eigen HTML- en CSS-bestanden gebruikt om de gebruikersinterface aan te passen:

- Azure AD B2C voegt HTML-inhoud **samen** in uw pagina's. Kopieer en probeer de standaardinhoud die Azure AD B2C biedt niet te kopiëren en te proberen te wijzigen. Het is het beste om uw HTML-inhoud helemaal opnieuw te bouwen en de standaardinhoud als referentie te gebruiken.
- **JavaScript** kan worden opgenomen in uw aangepaste inhoud voor zowel [gebruikersstromen](user-flow-javascript-overview.md) als [aangepaste beleidsregels.](javascript-samples.md)
- Ondersteunde **browserversies** zijn:
  - Internet Explorer 11, 10 en Microsoft Edge
  - Beperkte ondersteuning voor Internet Explorer 9 en 8
  - Google Chrome 42.0 en hoger
  - Mozilla Firefox 38.0 en hoger
  - Safari voor iOS en macOS, versie 12 en hoger
- Neem geen **formuliertags** op in uw HTML. Formuliertags verstoren de POST-bewerkingen die worden gegenereerd door de HTML die is geïnjecteerd door Azure AD B2C.

### <a name="where-do-i-store-ui-content"></a>Waar kan ik ui-inhoud opslaan?

Wanneer u uw eigen HTML- en CSS-bestanden gebruikt om de gebruikersinterface aan te passen, u uw UI-inhoud hosten op een openbaar beschikbaar HTTPS-eindpunt dat CORS ondersteunt. Azure [Blob-opslag,](../storage/blobs/storage-blobs-introduction.md)webservers, CDN's, AWS S3 of systemen voor het delen van bestanden.

Het belangrijkste punt is dat u de inhoud host op een openbaar beschikbaar HTTPS-eindpunt met CORS ingeschakeld. U moet een absolute URL gebruiken wanneer u deze in uw inhoud opgeeft.

## <a name="get-started-with-custom-html-and-css"></a>Aan de slag met aangepaste HTML en CSS

Ga aan de slag met uw eigen HTML en CSS in uw gebruikerservaringspagina's door deze richtlijnen te volgen.

- Maak goed gevormde HTML-inhoud `<div id="api"></div>` met een `<body>`leeg element dat zich ergens in de . Dit element markeert waar de Azure AD B2C-inhoud wordt ingevoegd. In het volgende voorbeeld ziet u een minimale pagina:

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

- Gebruik CSS om de ui-elementen te stylen die Azure AD B2C in uw pagina invoegt. In het volgende voorbeeld ziet u een eenvoudig CSS-bestand met instellingen voor de indeinste HTML-elementen:

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

- Host uw inhoud op een HTTPS-eindpunt (met CORS toegestaan). Zowel de methoden voor het aanvragen van GET als opties moeten zijn ingeschakeld bij het configureren van CORS.
- Maak of bewerk een gebruikersstroom of aangepast beleid om de inhoud te gebruiken die u hebt gemaakt.

### <a name="html-fragments-from-azure-ad-b2c"></a>HTML-fragmenten uit Azure AD B2C

In de volgende tabel worden de HTML-fragmenten weergegeven `<div id="api"></div>` die Azure AD B2C samenvoegt tot het element in uw inhoud.

| Ingevoegde pagina | Beschrijving van HTML |
| ------------- | ------------------- |
| Selectie identiteitsprovider | Bevat een lijst met knoppen voor identiteitsproviders waaruit de klant kan kiezen tijdens het aanmelden of aanmelden. Deze knoppen omvatten aanbieders van sociale identiteit, zoals Facebook, Google of lokale accounts (op basis van e-mailadres of gebruikersnaam). |
| Aanmelden voor lokale account | Bevat een formulier voor lokale accountaanmelding op basis van een e-mailadres of een gebruikersnaam. Het formulier kan verschillende invoerbesturingselementen bevatten, zoals tekstinvoervak, wachtwoordinvoervak, keuzerondje, keuzelijst voor één keuzeen en selectievakjes voor meerdere knoppen. |
| Aanmelden voor een sociaal account | Kan worden weergegeven wanneer u zich aanmeldt met een bestaand account van een aanbieder van sociale identiteit, zoals Facebook of Google. Het wordt gebruikt wanneer aanvullende informatie moet worden verzameld van de klant met behulp van een aanmeldingsformulier. |
| Verenigd aanmelden of aanmelden | Hiermee verwerkt u zowel aanmeldingals aanmelding van klanten die aanbieders van sociale identiteit kunnen gebruiken, zoals Facebook, Google of lokale accounts. |
| Multi-Factor Authentication | Klanten kunnen hun telefoonnummers (met tekst of stem) verifiëren tijdens het aanmelden of aanmelden. |
| Fout | Biedt foutinformatie aan de klant. |

## <a name="company-branding-preview"></a>Bedrijfsbranding (preview)

U uw gebruikersstroompagina's aanpassen met een bannerlogo, achtergrondafbeelding en achtergrondkleur met azure active directory [bedrijfsbranding.](../active-directory/fundamentals/customize-branding.md)

Als u uw gebruikersstroompagina's wilt aanpassen, configureert u eerst bedrijfsbranding in Azure Active Directory en schakelt u deze in in de pagina-indelingen van uw gebruikersstromen in Azure AD B2C.

[!INCLUDE [preview note](../../includes/active-directory-b2c-public-preview.md)]

### <a name="configure-company-branding"></a>Bedrijfshuisstijl configureren

Begin met het instellen van het bannerlogo, de achtergrondafbeelding en de achtergrondkleur binnen **bedrijfsbranding.**

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer het **filter Directory + abonnement** in het bovenste menu en selecteer vervolgens de map met uw Azure AD B2C-tenant.
1. Zoek en selecteer **Azure AD B2C**in de Azure-portal .
1. Selecteer **onder Beheren**de optie **Bedrijfsbranding**.
1. Volg de stappen in [Branding toevoegen aan de aanmeldingspagina](../active-directory/fundamentals/customize-branding.md)van Azure Active Directory van uw organisatie .

Houd rekening met deze dingen wanneer u bedrijfsbranding configureert in Azure AD B2C:

* Bedrijfsbranding in Azure AD B2C is momenteel beperkt tot **achtergrondafbeelding,** **bannerlogo**en **aanpassing van de achtergrondkleur.** De andere eigenschappen in het bedrijfshuismerkvenster, bijvoorbeeld die in **geavanceerde instellingen,** *worden niet ondersteund*.
* In de pagina's van de gebruikersstroom wordt de achtergrondkleur weergegeven voordat de achtergrondafbeelding wordt geladen. We raden u aan een achtergrondkleur te kiezen die nauw aansluit bij de kleuren in uw achtergrondafbeelding voor een vloeiendere laadervaring.
* Het bannerlogo wordt weergegeven in de verificatie-e-mails die naar uw gebruikers worden verzonden wanneer ze een aanmeldingsgebruikersstroom starten.

### <a name="enable-branding-in-user-flow-pages"></a>Branding inschakelen op gebruikersstroompagina's

Zodra u bedrijfsbranding hebt geconfigureerd, schakelt u deze in in uw gebruikersstromen.

1. Selecteer **Azure AD B2C**in het linkermenu van de Azure-portal .
1. Selecteer **onder Beleid**de optie **Gebruikersstromen (beleidsregels)**.
1. Selecteer de gebruikersstroom waarvoor u bedrijfsbranding wilt inschakelen. Bedrijfsbranding **wordt niet ondersteund** voor de typen *V1-* en *Profielbewerking v1-gebruikersstroom.*
1. Selecteer onder **Pagina-indelingen**aanpassen en selecteer vervolgens de indeling die u wilt maken. **Customize** Selecteer bijvoorbeeld **Unified sign up or sign in page**.
1. Kies versie **1.2.0** of hoger voor de **versie pagina-indeling (voorbeeld).**
1. Selecteer **Opslaan**.

Als u alle pagina's in de gebruikersstroom wilt brandmerken, stelt u de pagina-indelingsversie in voor elke pagina-indeling in de gebruikersstroom.

![Selectie van pagina-indeling in Azure AD B2C in de Azure-portal](media/customize-ui-overview/portal-02-page-layout-select.png)

In dit geannoteerde voorbeeld wordt een aangepast bannerlogo en achtergrondafbeelding weergegeven op een *pagina Aanmelden en aanmelden voor* gebruikersstroom die de sjabloon Ocean Blue gebruikt:

![Aanmeldings-/aanmeldingspagina voor merkproducten die wordt weergegeven door Azure AD B2C](media/customize-ui-overview/template-ocean-blue-branded.png)

### <a name="use-company-branding-assets-in-custom-html"></a>Bedrijfsmerkelementen gebruiken in aangepaste HTML

Als u uw bedrijfsmerkelementen wilt gebruiken in `<div id="api">` aangepaste HTML, voegt u de volgende tags toe buiten de tag:

```HTML
<img data-tenant-branding-background="true" />
<img data-tenant-branding-logo="true" alt="Company Logo" />
```

De beeldbron wordt vervangen door die van de achtergrondafbeelding en het bannerlogo. Zoals beschreven in de sectie [Aan de slag met aangepaste HTML en CSS,](#get-started-with-custom-html-and-css) gebruikt u CSS-klassen om de elementen op de pagina te stylen en te positioneren.

## <a name="localize-content"></a>Inhoud lokaliseren

U lokaliseert uw HTML-inhoud door [taalaanpassing](user-flow-language-customization.md) in te schakelen in uw Azure AD B2C-tenant. Als u deze functie inschakelt, kan Azure `ui-locales` AD B2C de parameter OpenID Connect doorsturen naar uw eindpunt. Uw inhoudsserver kan deze parameter gebruiken om taalspecifieke HTML-pagina's te verstrekken.

Inhoud kan worden getrokken uit verschillende plaatsen op basis van de landinplaats die wordt gebruikt. In het eindpunt met CORS stelt u een mapstructuur in om inhoud voor specifieke talen te hosten. U belt de juiste als u de `{Culture:RFC5646}`waarde van de wildcard gebruikt.

Uw aangepaste pagina URI kan er bijvoorbeeld uitzien als:

```HTTP
https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html
```

U de pagina in het Frans laden door inhoud uit:

```HTTP
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="examples"></a>Voorbeelden

U verschillende voorbeeldsjabloonbestanden vinden in de [B2C-AzureBlobStorage-Client-repository](https://github.com/azureadquickstarts/b2c-azureblobstorage-client) op GitHub.

De voorbeeld-HTML- en CSS-bestanden in de sjablonen bevinden zich in de [map /sample_templates.](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates)

## <a name="next-steps"></a>Volgende stappen

- Als u **gebruikersstromen**gebruikt, u uw gebruikersinterface aanpassen met de zelfstudie:

    [Pas de gebruikersinterface van uw toepassingen aan in Azure Active Directory B2C](tutorial-customize-ui.md).
- Als u **aangepast beleid gebruikt,** u de gebruikersinterface aanpassen met het artikel:

    [Pas de gebruikersinterface van uw toepassing aan met een aangepast beleid in Azure Active Directory B2C](custom-policy-ui-customization.md).
