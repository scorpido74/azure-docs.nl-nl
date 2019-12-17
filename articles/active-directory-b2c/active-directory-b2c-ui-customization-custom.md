---
title: De gebruikers interface van uw app aanpassen met een aangepast beleid
titleSuffix: Azure AD B2C
description: Meer informatie over het aanpassen van een gebruikers interface met behulp van een aangepast beleid in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1ac0f59ea709e25f3d71a78ece5ebf40690bd3be
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2019
ms.locfileid: "74949623"
---
# <a name="customize-the-user-interface-of-your-application-using-a-custom-policy-in-azure-active-directory-b2c"></a>Pas de gebruikers interface van uw toepassing aan met behulp van een aangepast beleid in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Nadat u dit artikel hebt voltooid, hebt u een aangepast beleid voor aanmelden en aanmelden met uw merk en vormgeving. Met Azure Active Directory B2C (Azure AD B2C) krijgt u bijna volledige controle over de HTML-en CSS-inhoud die wordt gepresenteerd aan gebruikers. Wanneer u een aangepast beleid gebruikt, configureert u de UI-aanpassing in XML in plaats van besturings elementen in de Azure Portal.

## <a name="prerequisites"></a>Vereisten

Voer de stappen in aan de [slag met aangepast beleid](active-directory-b2c-get-started-custom.md). U moet beschikken over een werkend aangepast beleid voor het aanmelden en aanmelden met lokale accounts.

## <a name="page-ui-customization"></a>Aanpassing van de pagina gebruikers interface

Met de functie voor het aanpassen van de gebruikers interface van de pagina kunt u het uiterlijk van elk aangepast beleid aanpassen. U kunt er ook voor zorgen dat er visuele en merkconsistentie is tussen uw toepassing en Azure AD B2C.

Dit werkt als volgt: Azure AD B2C code uit te voeren in de browser van uw klant en gebruikmaakt van een moderne aanpak genaamd [Cross-Origin Resource Sharing (CORS)](https://www.w3.org/TR/cors/). Eerst geeft u een URL op in het aangepaste beleid met aangepaste HTML-inhoud. Azure AD B2C combineert elementen van de gebruikersinterface met de HTML-inhoud die vanaf de URL wordt geladen en geeft vervolgens de pagina weer aan de klant.

## <a name="create-your-html5-content"></a>Uw HTML5-inhoud maken

Maak HTML-inhoud met de merk naam van uw product in de titel.

1. Kopieer het volgende HTML-code fragment. Het is een goed gevormde HTML5 met een leeg element met de naam *\<div id = "API"\>\</div\>* bevindt zich in de *\<Body\>* Tags. Dit element geeft aan waar Azure AD B2C inhoud moet worden ingevoegd.

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

1. Plak het gekopieerde fragment in een tekst editor en sla het bestand op als *Customize-UI. html*.

> [!NOTE]
> HTML-formulier elementen worden verwijderd vanwege beveiligings beperkingen als u login.microsoftonline.com gebruikt. Gebruik b2clogin.com als u HTML-formulier elementen wilt gebruiken in uw aangepaste HTML-inhoud. Zie [B2clogin.com gebruiken](b2clogin.md) voor andere voor delen.

## <a name="create-an-azure-blob-storage-account"></a>Een Azure Blob storage-account maken

>[!NOTE]
> In dit artikel gebruiken we Azure Blob-opslag om onze inhoud te hosten. U kunt ervoor kiezen om uw inhoud op een webserver te hosten, maar u moet [CORS inschakelen op de webserver](https://enable-cors.org/server.html).

Als u deze HTML-inhoud in Blob Storage wilt hosten, voert u de volgende stappen uit:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer in het menu **hub** **nieuwe** > **Storage** - > **opslag account**.
1. Selecteer een **abonnement** voor uw opslag account.
1. Maak een **resource groep** of selecteer een bestaande.
1. Voer een unieke **naam** in voor uw opslag account.
1. Selecteer de **geografische locatie** voor uw opslag account.
1. **Implementatie model** kan **Resource Manager**blijven.
1. **Prestaties** kunnen **standaard**blijven.
1. Wijzig het **type account** in **Blob Storage**.
1. **Replicatie** kan **Ra-GRS**blijven.
1. De **toegangs laag** kan **Hot**blijven.
1. Klik op **beoordeling + maken** om het opslag account te maken.
    Nadat de implementatie is voltooid, wordt de pagina **opslag account** automatisch geopend.

## <a name="create-a-container"></a>Een container maken

Als u een open bare container in Blob Storage wilt maken, voert u de volgende stappen uit:

1. Onder **BLOB service** in het linkermenu selecteert u **blobs**.
1. Klik op **+ container**.
1. Voer *basis*in bij **naam**. Dit kan een naam zijn van uw keuze, bijvoorbeeld *wingtiptoys*, maar in dit voor beeld wordt de *basis* gebruikt voor eenvoud.
1. Selecteer **BLOB**voor **openbaar toegangs niveau**en klik vervolgens op **OK**.
1. Klik op **root** om de nieuwe container te openen.
1. Klik op **Uploaden**.
1. Klik op het mappictogram naast **een bestand selecteren**.
1. Navigeer naar en selecteer **Customize-UI. html** die u eerder hebt gemaakt in de sectie UI-aanpassing van de pagina.
1. Als u wilt uploaden naar een submap, vouwt u **Geavanceerd** uit en voert u de naam van een map in **uploaden naar map**in.
1. Selecteer **Uploaden**.
1. Selecteer de BLOB **Customize-UI. html** die u hebt geüpload.
1. Selecteer rechts van het tekstvak **URL** het pictogram **kopiëren naar klem bord** om de URL naar het klem bord te kopiëren.
1. Navigeer in webbrowser naar de URL die u hebt gekopieerd om te controleren of de blob die u hebt geüpload, toegankelijk is. Als het niet toegankelijk is, bijvoorbeeld als er een `ResourceNotFound` fout optreedt, controleert u of het toegangs type voor de container is ingesteld op **BLOB**.

## <a name="configure-cors"></a>CORS configureren

Configureer de Blob-opslag voor cross-Origin-resource delen door de volgende stappen uit te voeren:

1. Selecteer **CORS**in het menu.
1. Voer `https://your-tenant-name.b2clogin.com`in voor **toegestane oorsprongen**. Vervang `your-tenant-name` met de naam van uw Azure AD B2C-tenant. Bijvoorbeeld `https://fabrikam.b2clogin.com`. U moet alle kleine letters gebruiken bij het invoeren van de naam van uw Tenant.
1. Voor **toegestane methoden**selecteert u zowel `GET` als `OPTIONS`.
1. Voer een asterisk (*) in bij **toegestane headers**.
1. Voer een asterisk (*) in voor **weer gegeven headers**.
1. Voer 200 in als **maximum leeftijd**.
1. Klik op **Opslaan**.

## <a name="test-cors"></a>CORS testen

Controleer of u klaar bent door de volgende stappen uit te voeren:

1. Ga naar de [www.test-cors.org](https://www.test-cors.org/) -website en plak de URL in het vak **externe URL** .
1. Klik op **aanvraag verzenden**.
    Als u een fout bericht ontvangt, moet u ervoor zorgen dat de [CORS-instellingen](#configure-cors) juist zijn. Mogelijk moet u ook de cache van de browser wissen of een persoonlijke browser sessie openen door op CTRL + SHIFT + P te drukken.

## <a name="modify-the-extensions-file"></a>Het extensie bestand wijzigen

Als u de UI-aanpassing wilt configureren, kopieert u de **ContentDefinition** en de onderliggende elementen van het basis bestand naar het extensie bestand.

1. Open het basis bestand van uw beleid. Bijvoorbeeld <em>`SocialAndLocalAccounts/` **`TrustFrameworkBase.xml`** </em> . Dit is een van de beleids bestanden in het aangepaste beleids Starter Pack, die u in de vereiste moet hebben verkregen, aan de [slag met aangepast beleid](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom).
1. Zoek en kopieer de volledige inhoud van het **ContentDefinitions** -element.
1. Open het extensie bestand. Bijvoorbeeld *TrustFrameworkExtensions. XML*. Zoek het element **BuildingBlocks** . Als het element niet bestaat, voegt u het toe.
1. Plak de volledige inhoud van het **ContentDefinitions** -element dat u hebt gekopieerd als onderliggend element van het **Building Blocks** -object.
1. Zoek naar het **ContentDefinition** -element dat `Id="api.signuporsignin"` bevat in het XML-bestand dat u hebt gekopieerd.
1. Wijzig de waarde van **LoadUri** in de URL van het HTML-bestand dat u hebt geüpload naar opslag. Bijvoorbeeld `https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html`.

    Het aangepaste beleid moet er als volgt uitzien:

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <LoadUri>https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html</LoadUri>
          <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
          <Metadata>
            <Item Key="DisplayName">Signin and Signup</Item>
          </Metadata>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

1. Sla het bestand met extensies op.

## <a name="upload-your-updated-custom-policy"></a>Uw bijgewerkte aangepaste beleid uploaden

1. Zorg ervoor dat u de map met uw Azure AD B2C-Tenant gebruikt door het filter **Directory + abonnement** te selecteren in het bovenste menu en de map te kiezen die uw Tenant bevat.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer een **Framework voor identiteits ervaring**.
1. Klik op **alle beleids regels**.
1. Klik op **beleid uploaden**.
1. Upload het extensie bestand dat u eerder hebt gewijzigd.

## <a name="test-the-custom-policy-by-using-run-now"></a>Het aangepaste beleid testen met behulp van **nu uitvoeren**

1. Ga op de pagina **Azure AD B2C** naar **alle beleids regels**.
1. Selecteer het aangepaste beleid dat u hebt geüpload en klik op de knop **nu uitvoeren** .
1. U moet zich kunnen aanmelden met behulp van een e-mail adres.

## <a name="reference"></a>Referentie

### <a name="sample-templates"></a>Voorbeeldsjablonen
U kunt hier voorbeeld sjablonen voor UI-aanpassing vinden:

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

De map sample_templates/Wingtip bevat de volgende HTML-bestanden:

| HTML5-sjabloon | Beschrijving |
|----------------|-------------|
| *phonefactor.html* | Gebruik dit bestand als een sjabloon voor een multi-factor Authentication-pagina. |
| *resetpassword.html* | Dit bestand gebruiken als een sjabloon voor een verg eten wacht woord pagina. |
| *selfasserted.html* | Gebruik dit bestand als een sjabloon voor een aanmeldings pagina voor een sociaal account, een aanmeldings pagina voor een lokaal account of een aanmeldings pagina voor een lokaal account. |
| *Unified. html* | Gebruik dit bestand als een sjabloon voor een uniforme registratie-of aanmeldings pagina. |
| *updateprofile. html* | Gebruik dit bestand als een sjabloon voor een update pagina van een profiel. |

Hier volgen de stappen voor het gebruik van het voor beeld:

1. Kloon de opslag plaats op uw lokale machine. Kies een sjabloon map onder sample_templates. U kunt `wingtip` of `contoso`gebruiken.
1. Upload alle bestanden in de mappen `css`, `fonts`en `images` naar Blob-opslag, zoals beschreven in de vorige secties.
1. Open vervolgens elk \*. html-bestand in de hoofdmap van een `wingtip` of `contoso` (afhankelijk van wat u in de eerste stap hebt geselecteerd) en vervang alle exemplaren van 'http://localhost ' door de Url's van de CSS-, afbeeldings-en letter typen bestanden die u in stap 2 hebt geüpload.
1. Sla de \*. html-bestanden op en upload deze naar de Blob-opslag.
1. Wijzig nu het extensie bestand zoals eerder vermeld in [het bestand extensies wijzigen](#modify-the-extensions-file).
1. Als u ontbrekende letter typen, afbeeldingen of CSS ziet, controleert u uw referenties in het uitbrei ding beleid en de \*. html-bestanden.

### <a name="content-definition-ids"></a>Inhouds definitie-Id's

In het gedeelte uw aangepaste beleid voor registreren of aanmelden aanpassen hebt u de inhouds definitie voor `api.idpselections`geconfigureerd. De volledige set met inhouds definitie-Id's die worden herkend door het Azure AD B2C identiteits ervarings raamwerk en de bijbehorende beschrijvingen vindt u in de volgende tabel:

| ID van de inhouds definitie | Beschrijving |
|-----------------------|-------------|
| *api.error* | **Fout pagina**. Deze pagina wordt weer gegeven wanneer er een uitzonde ring of een fout wordt aangetroffen. |
| *api.idpselections* | Pagina voor het selecteren van de **identiteits provider**. Deze pagina bevat een lijst met id-providers waaruit de gebruiker kan kiezen tijdens het aanmelden. Deze opties zijn ondernemings-id-providers, sociale id-providers, zoals Facebook, Google + of lokale accounts. |
| *api.idpselections.signup* | **Selectie van ID-provider voor registratie**. Deze pagina bevat een lijst met id-providers waaruit de gebruiker kan kiezen tijdens het aanmelden. Deze opties zijn ondernemings-id-providers, sociale id-providers, zoals Facebook, Google + of lokale accounts. |
| *api.localaccountpasswordreset* | **Wachtwoord pagina verg eten**. Deze pagina bevat een formulier dat de gebruiker moet volt ooien om het opnieuw instellen van een wacht woord te initiëren.  |
| *api.localaccountsignin* | **Aanmeldings pagina voor het lokale account**. Deze pagina bevat een aanmeld formulier voor het aanmelden met een lokaal account dat is gebaseerd op een e-mail adres of een gebruikers naam. Het formulier kan een tekstvak voor tekst invoer en wacht woord bevatten. |
| *api.localaccountsignup* | De **registratie pagina van het lokale account**. Deze pagina bevat een aanmeld formulier voor het aanmelden voor een lokaal account dat is gebaseerd op een e-mail adres of een gebruikers naam. Het formulier kan verschillende invoer besturings elementen bevatten, zoals een tekstinvoervak, een vak voor het invoeren van een wacht woord, een keuze rondje, vervolg keuzelijsten met één selectie en selectie vakjes met meerdere selecties. |
| *api.phonefactor* | **Multi-factor Authentication-pagina**. Op deze pagina kunnen gebruikers hun telefoon nummers (met behulp van tekst of spraak) verifiëren tijdens het registreren of aanmelden. |
| *api.selfasserted* | De **registratie pagina voor het sociaal account**. Deze pagina bevat een aanmeldings formulier dat gebruikers moeten volt ooien wanneer ze zich aanmelden met een bestaand account van een id-provider voor sociale netwerken, zoals Facebook of Google +. Deze pagina is vergelijkbaar met de voor gaande aanmeldings pagina voor het sociaal account, met uitzonde ring van de velden voor het invoeren van wacht woorden. |
| *api.selfasserted.profileupdate* | **Pagina Profiel bijwerken**. Deze pagina bevat een formulier dat gebruikers kunnen gebruiken om hun profiel bij te werken. Deze pagina is vergelijkbaar met de aanmeldings pagina voor het sociaal account, met uitzonde ring van de velden voor het invoeren van wacht woorden. |
| *api.signuporsignin* | **Uniforme registratie-of aanmeldings pagina**. Op deze pagina worden zowel de registratie als het aanmelden van gebruikers verwerkt, wie ondernemings-id-providers, sociale id-providers, zoals Facebook of Google + of lokale accounts kunnen gebruiken.  |

## <a name="next-steps"></a>Volgende stappen

Zie [Naslag Gids voor UI-aanpassing voor gebruikers stromen](active-directory-b2c-reference-ui-customization.md)voor meer informatie over UI-elementen die kunnen worden aangepast.
