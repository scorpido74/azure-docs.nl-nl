---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 03/19/2020
ms.author: mimart
ms.openlocfilehash: af11283f9e9dbd925ec994dcb1d96393332b90fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80117048"
---
## <a name="use-custom-page-content"></a>Aangepaste pagina-inhoud gebruiken

Door de functie voor aanpassing van de pagina-gebruikersinterface te gebruiken, u het uiterlijk van elk aangepast beleid aanpassen. U kunt er ook voor zorgen dat er visuele en merkconsistentie is tussen uw toepassing en Azure AD B2C.

### <a name="how-it-works"></a>Hoe werkt het?

Azure AD B2C voert code uit in de browser van uw klant met behulp van [Cross-Origin Resource Sharing (CORS).](https://www.w3.org/TR/cors/) Bij runtime wordt inhoud geladen vanuit een URL die u opgeeft in uw gebruikersstroom of aangepast beleid. Elke pagina in de gebruikerservaring laadt de inhoud van de URL die u voor die pagina opgeeft. Nadat de inhoud is geladen vanuit uw URL, wordt deze samengevoegd met een HTML-fragment dat is ingevoegd door Azure AD B2C en vervolgens wordt de pagina weergegeven aan uw klant.

![Aangepaste pagina-inhoudsmarge](./media/active-directory-b2c-html-templates/html-content-merging.png)

## <a name="custom-html-page-content"></a>Aangepaste HTML-pagina-inhoud

Maak een HTML-pagina met uw eigen branding om uw aangepaste pagina-inhoud weer te geven. Deze pagina kan `*.html` een statische pagina zijn, of een dynamische pagina zoals .NET, Node.js of PHP.

Uw aangepaste pagina-inhoud kan HTML-elementen bevatten, waaronder CSS en JavaScript, maar kan geen onveilige elementen zoals iFrames bevatten. Het enige vereiste element is `id` een `api`div-element `<div id="api"></div>` met ingesteld op , zoals deze in uw HTML-pagina.

```html
<!DOCTYPE html>
<html>
<head>
    <title>My Product Brand Name</title>
</head>
<body>
    <div id="api"></div>
</body>
</html>
```

### <a name="customize-the-default-azure-ad-b2c-pages"></a>De standaard Azure AD B2C-pagina's aanpassen

In plaats van uw aangepaste pagina-inhoud helemaal opnieuw te maken, u de standaardpagina-inhoud van Azure AD B2C aanpassen.

In de volgende tabel worden de standaardpagina-inhoud weergegeven die wordt geleverd door Azure AD B2C. Download de bestanden en gebruik ze als uitgangspunt voor het maken van uw eigen aangepaste pagina's.

| Standaardpagina | Beschrijving | Inhoudsdefinitie-id<br/>(alleen aangepast beleid) |
|:-----------------------|:--------|-------------|
| [exception.html](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Foutpagina**. Deze pagina wordt weergegeven wanneer een uitzondering of een fout wordt aangetroffen. | *api.fout* |
| [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) |  **Zelfverklaarde pagina**. Gebruik dit bestand als aangepaste pagina-inhoud voor een aanmeldingspagina voor een sociaal account, een lokale aanmeldingspagina voor een lokaal account, een aanmeldingspagina voor een lokaal account, het opnieuw instellen van wachtwoorden en meer. Het formulier kan verschillende invoerbesturingselementen bevatten, zoals: een tekstinvoervak, een wachtwoordinvoervakje, een keuzerondje, vervolgkeuzevakken met één keuze optie en selectievakjes voor meerdere knoppen. | *api.localaccountsignin*, *api.localaccountsignup*, *api.localaccountpasswordreset*, *api.selfasserted* |
| [multifactor-1.0.0.html](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Pagina voor meervoudige verificatie**. Op deze pagina kunnen gebruikers hun telefoonnummers (met behulp van tekst of stem) verifiëren tijdens het aanmelden of aanmelden. | *api.phonefactor* |
| [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Pagina profielupdate**. Deze pagina bevat een formulier dat gebruikers kunnen openen om hun profiel bij te werken. Deze pagina is vergelijkbaar met de aanmeldingspagina voor sociale wachtwoorden, met uitzondering van de invoervelden voor wachtwoorden. | *api.selfasserted.profileupdate* |
| [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Uniforme aanmeldings- of aanmeldingspagina**. Op deze pagina wordt het aanmeldings- en aanmeldingsproces van de gebruiker verwerkt. Gebruikers kunnen zakelijke identiteitsproviders, aanbieders van sociale identiteit, zoals Facebook of Google+, of lokale accounts gebruiken. | *api.signuporsignin* |

## <a name="hosting-the-page-content"></a>De pagina-inhoud hosten

Wanneer u uw eigen HTML- en CSS-bestanden gebruikt om de gebruikersinterface aan te passen, host u uw UI-inhoud op een openbaar beschikbaar HTTPS-eindpunt dat CORS ondersteunt. Azure [Blob-opslag,](../articles/storage/blobs/storage-blobs-introduction.md) [Azure App Services,](/azure/app-service/)webservers, CDN's, AWS S3 of systemen voor het delen van bestanden.

## <a name="guidelines-for-using-custom-page-content"></a>Richtlijnen voor het gebruik van aangepaste pagina-inhoud

- Gebruik een absolute URL wanneer u externe bronnen zoals media, CSS en JavaScript-bestanden in uw HTML-bestand opneemt.
- Met [pagina-indelingversie](../articles/active-directory-b2c/page-layout.md) 1.2.0 en hoger `data-preload="true"` u het kenmerk in uw HTML-tags toevoegen om de belastingsvolgorde voor CSS en JavaScript te beheren. Met `data-preload=true`, de pagina is gebouwd voordat ze worden getoond aan de gebruiker. Dit kenmerk voorkomt dat de pagina 'flikkert' door het CSS-bestand vooraf te laden, zonder dat de html die niet is gestyled aan de gebruiker wordt weergegeven. In het volgende HTML-codefragment `data-preload` wordt het gebruik van de tag weergegeven.
  ```HTML
  <link href="https://path-to-your-file/sample.css" rel="stylesheet" type="text/css" data-preload="true"/>
  ```
- We raden u aan te beginnen met de standaardpagina-inhoud en er bovenop te bouwen.
- U JavaScript opnemen in uw aangepaste inhoud voor zowel [gebruikersstromen](../articles/active-directory-b2c/user-flow-javascript-overview.md) als [aangepaste beleidsregels.](../articles/active-directory-b2c/javascript-samples.md)
- Ondersteunde browserversies zijn:
  - Internet Explorer 11, 10 en Microsoft Edge
  - Beperkte ondersteuning voor Internet Explorer 9 en 8
  - Google Chrome 42.0 en hoger
  - Mozilla Firefox 38.0 en hoger
  - Safari voor iOS en macOS, versie 12 en hoger
- Vanwege beveiligingsbeperkingen biedt Azure AD B2C `frame` `iframe`geen `form` ondersteuning voor html-elementen of HTML-elementen.

## <a name="custom-page-content-walkthrough"></a>Aangepaste pagina-inhoud slopen

Hier is een overzicht van het proces:

1. Bereid een locatie voor om uw aangepaste pagina-inhoud te hosten (een openbaar toegankelijk, HTTPS-eindpunt met CORS).
1. Download en pas bijvoorbeeld `unified.html`een standaardpagina-inhoudsbestand aan.
1. Publiceer uw aangepaste pagina-inhoud uw openbaar beschikbare HTTPS-eindpunt.
1. Stel cross-origin resource sharing (CORS) in voor uw web-app.
1. Wijs uw beleid op uw aangepaste beleidsinhoud URI.

### <a name="1-create-your-html-content"></a>1. Uw HTML-inhoud maken

Maak een aangepaste pagina-inhoud met de merknaam van uw product in de titel.

1. Kopieer het volgende HTML-fragment. Het is goed gevormde HTML5 met een leeg element genaamd * \<div id ="api"\>\</ div\> * gelegen in de * \<body\> * tags. Dit element geeft aan waar Azure AD B2C-inhoud moet worden ingevoegd.

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>My Product Brand Name</title>
   </head>
   <body>
       <div id="api"></div>
   </body>
   </html>
   ```

1. Plak het gekopieerde fragment in een teksteditor en sla het bestand op als *customize-ui.html*.

> [!NOTE]
> HTML-formulierelementen worden verwijderd vanwege beveiligingsbeperkingen als u login.microsoftonline.com gebruikt. Als u HTML-formulierelementen wilt gebruiken in uw aangepaste HTML-inhoud, [gebruikt u b2clogin.com](../articles/active-directory-b2c/b2clogin.md).

### <a name="2-create-an-azure-blob-storage-account"></a>2. Een Azure Blob-opslagaccount maken

In dit artikel gebruiken we Azure Blob-opslag om onze inhoud te hosten. U ervoor kiezen om uw inhoud op een webserver te hosten, maar u moet [CORS inschakelen op uw webserver.](https://enable-cors.org/server.html)

Voer de volgende stappen uit om uw HTML-inhoud in Blob-opslag te hosten:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer **in** het menu Hub de optie **Nieuw** > **opslagaccount voor** > **opslag**.
1. Selecteer een **abonnement** op uw opslagaccount.
1. Maak een **resourcegroep** of selecteer een bestaande groep.
1. Voer een unieke **naam in** voor uw opslagaccount.
1. Selecteer de **geografische locatie** voor uw opslagaccount.
1. **Implementatiemodel** kan **Resourcebeheer**blijven .
1. **Prestaties** kunnen **standaard**blijven.
1. **Accountvriendelijk wijzigen** in **Blob-opslag**.
1. **Replicatie** kan **RA-GRS**blijven .
1. **De toegangslaag** kan **heet**blijven.
1. Selecteer **Controleren + maken** om het opslagaccount te maken.
    Nadat de implementatie is voltooid, wordt de pagina **Opslagaccount** automatisch geopend.

#### <a name="21-create-a-container"></a>2.1 Een container maken

Voer de volgende stappen uit om een openbare container in Blob-opslag te maken:

1. Selecteer **Blobs**onder **Blob-service** in het linkermenu .
1. Selecteer **+Container**.
1. Voer **voor Naam** *root*in . De naam kan een naam van uw keuze, bijvoorbeeld *contoso*, maar we gebruiken *wortel* in dit voorbeeld voor eenvoud.
1. Selecteer **Blob**voor **openbaar toegangsniveau**en **vervolgens OK**.
1. Selecteer **root** om de nieuwe container te openen.

#### <a name="22-upload-your-custom-page-content-files"></a>2.2 Uw aangepaste pagina-inhoudsbestanden uploaden

1. Selecteer **Uploaden**.
1. Selecteer het mappictogram naast **Een bestand selecteren**.
1. Navigeer naar en selecteer **customize-ui.html,** die u eerder hebt gemaakt in de sectie Pagina-gebruikersinterface.
1. Als u wilt uploaden naar een submap, vouwt u **Geavanceerd** uit en voert u een mapnaam in **Uploaden naar map**in.
1. Selecteer **Uploaden**.
1. Selecteer de **blob customize-ui.html** die u hebt geüpload.
1. Selecteer rechts van het **tekstvak URL** het pictogram **Kopiëren naar klembord** om de URL naar het klembord te kopiëren.
1. Navigeer in de webbrowser naar de URL die u hebt gekopieerd om te controleren of de blob die u hebt geüpload, toegankelijk is. Als deze niet toegankelijk is, bijvoorbeeld `ResourceNotFound` als er een fout optreedt, controleert u of het type containertoegangs is ingesteld op **blob.**

### <a name="3-configure-cors"></a>3. Cors configureren

Configureer Blob-opslag voor Cross-Origin Resource Sharing door de volgende stappen uit te voeren:

1. Selecteer **CORS**in het menu.
1. Voor **toegestane oorsprong,** `https://your-tenant-name.b2clogin.com`voer . Vervang `your-tenant-name` de naam van uw Azure AD B2C-tenant. Bijvoorbeeld `https://fabrikam.b2clogin.com`. Gebruik alle kleine letters bij het invoeren van uw tenantnaam.
1. Selecteer voor **toegestane methoden** `GET` beide `OPTIONS`en .
1. Voer **voor Toegestane kopteksten**een sterretje in (*).
1. Voer **voor Exposed Headers**een sterretje (*) in.
1. Voor **Max leeftijd,** voer 200.
1. Selecteer **Opslaan**.

#### <a name="31-test-cors"></a>3.1 Test CORS

Valideer dat u klaar bent door de volgende stappen uit te voeren:

1. Herhaal de stap CORS configureren. Voor **toegestane oorsprong,** voer`https://www.test-cors.org`
1. Naar [www.test-cors.org](https://www.test-cors.org/) 
1. Plak voor het vak **Externe URL** de URL van uw HTML-bestand. Bijvoorbeeld: `https://your-account.blob.core.windows.net/azure-ad-b2c/unified.html`
1. Selecteer **Verzoek verzenden**.
    Het resultaat `XHR status: 200`zou moeten zijn . 
    Als u een fout ontvangt, controleert u of uw CORS-instellingen correct zijn. Mogelijk moet u ook de cache van uw browser wissen of een privébrowsersessie openen door op Ctrl+Shift+P te drukken.
