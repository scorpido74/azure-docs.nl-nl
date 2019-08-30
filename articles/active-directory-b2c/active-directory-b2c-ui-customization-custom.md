---
title: Pas de gebruikers interface van uw toepassing aan met behulp van een aangepast beleid in Azure Active Directory B2C | Microsoft Docs
description: Meer informatie over het aanpassen van een gebruikers interface met behulp van een aangepast beleid in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/18/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: e79d7a4b97f010b035f5c864682b4d3882a21393
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70171920"
---
# <a name="customize-the-user-interface-of-your-application-using-a-custom-policy-in-azure-active-directory-b2c"></a>Pas de gebruikers interface van uw toepassing aan met behulp van een aangepast beleid in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Nadat u dit artikel hebt voltooid, hebt u een aangepast beleid voor aanmelden en aanmelden met uw merk en vormgeving. Met Azure Active Directory B2C (Azure AD B2C) krijgt u bijna volledige controle over de HTML-en CSS-inhoud die wordt gepresenteerd aan gebruikers. Wanneer u een aangepast beleid gebruikt, configureert u de UI-aanpassing in XML in plaats van besturings elementen in de Azure Portal. 

## <a name="prerequisites"></a>Vereisten

Voer de stappen in aan de [slag met aangepast beleid](active-directory-b2c-get-started-custom.md). U moet beschikken over een werkend aangepast beleid voor het aanmelden en aanmelden met lokale accounts.

## <a name="page-ui-customization"></a>De gebruikersinterface van de pagina aanpassen

Met de functie voor het aanpassen van de gebruikers interface van de pagina kunt u het uiterlijk van elk aangepast beleid aanpassen. U kunt er ook voor zorgen dat er visuele en merkconsistentie is tussen uw toepassing en Azure AD B2C.

Het werkt als volgt: Azure AD B2C code wordt uitgevoerd in de browser van uw klant en maakt gebruik van een moderne aanpak [(CORS (cross-Origin Resource Sharing))](https://www.w3.org/TR/cors/). Eerst geeft u een URL op in het aangepaste beleid met aangepaste HTML-inhoud. Azure AD B2C combineert elementen van de gebruikersinterface met de HTML-inhoud die vanaf de URL wordt geladen en geeft vervolgens de pagina weer aan de klant.

## <a name="create-your-html5-content"></a>Uw HTML5-inhoud maken

Maak HTML-inhoud met de merk naam van uw product in de titel.

1. Kopieer het volgende HTML-code fragment. Het is een goed gevormde HTML5 met een leeg element met de naam  *\<div id =\>"\> API"\</div* die zich in de *\<hoofd\>* code bevindt. Dit element geeft aan waar Azure AD B2C inhoud moet worden ingevoegd.

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

2. Plak het gekopieerde fragment in een tekst editor en sla het bestand op als *Customize-UI. html*.

> [!NOTE]
> HTML-formulier elementen worden verwijderd vanwege beveiligings beperkingen als u login.microsoftonline.com gebruikt. Gebruik b2clogin.com als u HTML-formulier elementen wilt gebruiken in uw aangepaste HTML-inhoud. Zie [B2clogin.com gebruiken](b2clogin.md) voor andere voor delen.

## <a name="create-an-azure-blob-storage-account"></a>Een Azure Blob storage-account maken

>[!NOTE]
> In dit artikel gebruiken we Azure Blob-opslag om onze inhoud te hosten. U kunt ervoor kiezen om uw inhoud op een webserver te hosten, maar u moet [CORS inschakelen op de webserver](https://enable-cors.org/server.html).

Ga als volgt te werk om deze HTML-inhoud in Blob Storage te hosten:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer in het **hub** -menu de optie **Nieuw** > **opslag** > **opslag account**.
3. Selecteer een **abonnement** voor uw opslag account.
4. Maak een **resource groep** of selecteer een bestaande.
5. Voer een unieke **naam** in voor uw opslag account.
6. Selecteer de **geografische locatie** voor uw opslag account. 
7. **Implementatie model** kan **Resource Manager**blijven.
8. **Prestaties** kunnen **standaard**blijven.
9. Wijzig het **type account** in **Blob Storage**.
10. **Replicatie** kan **Ra-GRS**blijven.
11. De **toegangs laag** kan **Hot**blijven. 
12. Klik op **beoordeling + maken** om het opslag account te maken.  
    Nadat de implementatie is voltooid, wordt de Blade **opslag account** automatisch geopend.

## <a name="create-a-container"></a>Een container maken

Als u een open bare container in Blob Storage wilt maken, gaat u als volgt te werk:

1. Onder **BLOB service** in het linkermenu selecteert u blobs.
2. Klik op **+ container**.
3. Voer *basis*in bij **naam**. Dit kan een naam zijn van uw keuze, bijvoorbeeld *wingtiptoys*, maar in dit voor beeld wordt de *basis* gebruikt voor eenvoud.
4. Selecteer **BLOB**voor **openbaar toegangs niveau**en klik vervolgens op **OK**.
5. Klik op **root** om de nieuwe container te openen.
6. Klik op **Uploaden**.
7. Klik op het mappictogram naast **een bestand selecteren**.
8. Navigeer naar en selecteer **Customize-UI. html** die u eerder hebt gemaakt in de sectie UI-aanpassing van de pagina.
9. Als u wilt uploaden naar een submap, vouwt u **Geavanceerd** uit en voert u de naam van een map in **uploaden naar map**in.
10. Selecteer **Uploaden**.
11. Selecteer de BLOB **Customize-UI. html** die u hebt geüpload.
12. Selecteer rechts van het tekstvak **URL** het pictogram **kopiëren naar klem bord** om de URL naar het klem bord te kopiëren.
13. Navigeer in webbrowser naar de URL die u hebt gekopieerd om te controleren of de blob die u hebt geüpload, toegankelijk is. Als het niet toegankelijk is, bijvoorbeeld als er een `ResourceNotFound` fout optreedt, controleert u of het toegangs type voor de container is ingesteld op **BLOB**.

## <a name="configure-cors"></a>CORS configureren

Configureer de Blob-opslag voor cross-Origin-resource delen door het volgende te doen:

1. Selecteer **CORS**in het menu.
2. Voer`https://your-tenant-name.b2clogin.com`in voor **toegestane oorsprongen**. Vervang `your-tenant-name` met de naam van uw Azure AD B2C-tenant. Bijvoorbeeld `https://fabrikam.b2clogin.com`. U moet alle kleine letters gebruiken bij het invoeren van de naam van uw Tenant.
3. Voor **toegestane methoden**selecteert u beide `GET` en `OPTIONS`.
4. Voer een asterisk (*) in bij **toegestane headers**.
5. Voer een asterisk (*) in voor **weer gegeven headers**.
6. Voer 200 in als **maximum leeftijd**.
7. Klik op **Opslaan**.

## <a name="test-cors"></a>CORS testen

Controleer als volgt of u klaar bent:

1. Ga naar de [www.test-cors.org](https://www.test-cors.org/) -website en plak de URL in het vak **externe URL** .
2. Klik op **aanvraag verzenden**.  
    Als u een fout bericht ontvangt, moet u ervoor zorgen dat de [CORS-instellingen](#configure-cors) juist zijn. Mogelijk moet u ook de cache van de browser wissen of een persoonlijke browser sessie openen door op CTRL + SHIFT + P te drukken.

## <a name="modify-the-extensions-file"></a>Het extensie bestand wijzigen

Als u de UI-aanpassing wilt configureren, kopieert u de **ContentDefinition** en de onderliggende elementen van het basis bestand naar het extensie bestand.

1. Open het basis bestand van uw beleid. Bijvoorbeeld *TrustFrameworkBase. XML*.
2. Zoek en kopieer de volledige inhoud van het **ContentDefinitions** -element.
3. Open het extensie bestand. Bijvoorbeeld *TrustFrameworkExtensions. XML*. Zoek het element **BuildingBlocks** . Als het element niet bestaat, voegt u het toe.
4. Plak de volledige inhoud van het **ContentDefinitions** -element dat u hebt gekopieerd als onderliggend element van het **Building Blocks** -object. 
5. Zoek naar het **ContentDefinition** -element dat `Id="api.signuporsignin"` zich in de XML bevinden die u hebt gekopieerd.
6. Wijzig de waarde van **LoadUri** in de URL van het HTML-bestand dat u hebt geüpload naar opslag. Bijvoorbeeld `https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html`.
    
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

7. Sla het bestand met extensies op.

## <a name="upload-your-updated-custom-policy"></a>Uw bijgewerkte aangepaste beleid uploaden

1. Zorg ervoor dat u de map met uw Azure AD B2C-tenant door te klikken op de **map- en abonnementsfilter** in het bovenste menu en de map waarin uw tenant te kiezen.
3. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
4. Selecteer een **Framework voor identiteits ervaring**.
2. Klik op **alle beleids regels**.
3. Klik op **beleid uploaden**.
4. Upload het extensie bestand dat u eerder hebt gewijzigd.

## <a name="test-the-custom-policy-by-using-run-now"></a>Het aangepaste beleid testen met behulp van **nu uitvoeren**

1. Ga op de Blade **Azure AD B2C** naar **alle beleids regels**.
2. Selecteer het aangepaste beleid dat u hebt geüpload en klik op de knop **nu uitvoeren** .
3. U moet zich kunnen aanmelden met behulp van een e-mail adres.

## <a name="reference"></a>Referentie

### <a name="sample-templates"></a>Voorbeeldsjablonen
U kunt hier voorbeeld sjablonen voor UI-aanpassing vinden:

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

De map sample_templates/Wingtip bevat de volgende HTML-bestanden:

| HTML5-sjabloon | Description |
|----------------|-------------|
| *phonefactor.html* | Gebruik dit bestand als een sjabloon voor een multi-factor Authentication-pagina. |
| *resetpassword.html* | Dit bestand gebruiken als een sjabloon voor een verg eten wacht woord pagina. |
| *selfasserted.html* | Gebruik dit bestand als een sjabloon voor een aanmeldings pagina voor een sociaal account, een aanmeldings pagina voor een lokaal account of een aanmeldings pagina voor een lokaal account. |
| *Unified. html* | Gebruik dit bestand als een sjabloon voor een uniforme registratie-of aanmeldings pagina. |
| *updateprofile. html* | Gebruik dit bestand als een sjabloon voor een update pagina van een profiel. |

Hier volgen de stappen voor het gebruik van het voor beeld. 
1. Kloon de opslag plaats op uw lokale machine. Kies een sjabloon map onder sample_templates. U kunt of `wingtip` `contoso`gebruiken.
2. Upload alle bestanden in de `css`mappen, `fonts`en en `images` naar Blob Storage, zoals beschreven in de vorige secties. 
3. Open vervolgens elk \*HTML-bestand in de hoofdmap van ofwel `wingtip` of `contoso` (afhankelijk van wat u in de eerste stap hebt geselecteerd) en vervang alle exemplaren http://localhost van ' ' door de url's van de CSS-, afbeeldings-en letter typen bestanden die u in stap 2 hebt geüpload.
4. Sla de \*HTML-bestanden op en upload deze naar de Blob-opslag.
5. Wijzig nu het extensie bestand zoals eerder vermeld in [het bestand extensies wijzigen](#modify-the-extensions-file).
6. Als u ontbrekende letter typen, afbeeldingen of CSS ziet, controleert u uw referenties in het uitbrei ding beleid \*en de. html-bestanden.

### <a name="content-defintion-ids"></a>Definitie-Id's van inhoud

In de sectie uw eigen registratie-of aanmeldings beleid aanpassen hebt u de inhouds definitie voor `api.idpselections`geconfigureerd. De volledige set met inhouds definitie-Id's die worden herkend door het Azure AD B2C identiteits ervarings raamwerk en de bijbehorende beschrijvingen vindt u in de volgende tabel:

| ID van de inhouds definitie | Description | 
|-----------------------|-------------|
| *api.error* | **Fout pagina**. Deze pagina wordt weer gegeven wanneer er een uitzonde ring of een fout wordt aangetroffen. |
| *api.idpselections* | **Pagina**voor het selecteren van de identiteits provider. Deze pagina bevat een lijst met id-providers waaruit de gebruiker kan kiezen tijdens het aanmelden. Deze opties zijn ondernemings-id-providers, sociale id-providers, zoals Facebook, Google + of lokale accounts. |
| *api.idpselections.signup* | **Selectie van ID-provider voor registratie**. Deze pagina bevat een lijst met id-providers waaruit de gebruiker kan kiezen tijdens het aanmelden. Deze opties zijn ondernemings-id-providers, sociale id-providers, zoals Facebook, Google + of lokale accounts. |
| *api.localaccountpasswordreset* | **Wachtwoord pagina verg eten**. Deze pagina bevat een formulier dat de gebruiker moet volt ooien om het opnieuw instellen van een wacht woord te initiëren.  |
| *api.localaccountsignin* | **Aanmeldings pagina voor het lokale account**. Deze pagina bevat een aanmeld formulier voor het aanmelden met een lokaal account dat is gebaseerd op een e-mail adres of een gebruikers naam. Het formulier kan een tekstvak voor tekst invoer en wacht woord bevatten. |
| *api.localaccountsignup* | De **registratie pagina van het lokale account**. Deze pagina bevat een aanmeld formulier voor het aanmelden voor een lokaal account dat is gebaseerd op een e-mail adres of een gebruikers naam. Het formulier kan verschillende invoer besturings elementen bevatten, zoals een tekstinvoervak, een vak voor het invoeren van een wacht woord, een keuze rondje, vervolg keuzelijsten met één selectie en selectie vakjes met meerdere selecties. |
| *api.phonefactor* | **Multi-factor Authentication-pagina**. Op deze pagina kunnen gebruikers hun telefoon nummers (met behulp van tekst of spraak) verifiëren tijdens het registreren of aanmelden. |
| *api.selfasserted* | De **registratie pagina voor het sociaal account**. Deze pagina bevat een aanmeldings formulier dat gebruikers moeten volt ooien wanneer ze zich aanmelden met een bestaand account van een id-provider voor sociale netwerken, zoals Facebook of Google +. Deze pagina is vergelijkbaar met de voor gaande aanmeldings pagina voor het sociaal account, met uitzonde ring van de velden voor het invoeren van wacht woorden. |
| *api.selfasserted.profileupdate* | **Pagina Profiel bijwerken**. Deze pagina bevat een formulier dat gebruikers kunnen gebruiken om hun profiel bij te werken. Deze pagina is vergelijkbaar met de aanmeldings pagina voor het sociaal account, met uitzonde ring van de velden voor het invoeren van wacht woorden. |
| *api.signuporsignin* | **Uniforme registratie-of aanmeldings pagina**. Op deze pagina worden zowel de registratie als het aanmelden van gebruikers verwerkt, wie ondernemings-id-providers, sociale id-providers, zoals Facebook of Google + of lokale accounts kunnen gebruiken.  |

## <a name="next-steps"></a>Volgende stappen

Zie [Naslag Gids voor UI-aanpassing voor ingebouwde beleids regels](active-directory-b2c-reference-ui-customization.md)voor meer informatie over UI-elementen die kunnen worden aangepast.
