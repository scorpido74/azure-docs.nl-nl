---
title: De gebruikers interface (UI) dynamisch aanpassen met aangepast beleid
titleSuffix: Azure AD B2C
description: Ondersteuning voor meerdere huisstijl ervaringen met HTML5/CSS-inhoud die dynamisch wordt gewijzigd tijdens runtime.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ae29a068ef29898c3fa27d3620d1e6be0be4bf3b
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75931208"
---
# <a name="azure-active-directory-b2c-configure-the-ui-with-dynamic-content-by-using-custom-policies"></a>Azure Active Directory B2C: Configureer de gebruikers interface met dynamische inhoud door gebruik te maken van aangepaste beleids regels

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Door Azure Active Directory B2C (Azure AD B2C) aangepast beleid te gebruiken, kunt u een para meter verzenden in een query reeks. Door de parameter door te geven aan uw HTML-eindpunt, kunt u de pagina-inhoud dynamisch wijzigen. U kunt bijvoorbeeld de achtergrondafbeelding op de registratie- of aanmeldingspagina van Azure AD B2C wijzigen, op basis van een parameter die u doorgeeft vanuit uw web- of mobiele toepassing.

## <a name="prerequisites"></a>Vereisten
In dit artikel wordt uitgelegd hoe u de Azure AD B2C gebruikers interface kunt aanpassen met *dynamische inhoud* door gebruik te maken van aangepaste beleids regels. Zie [aanpassing van de gebruikers interface in een aangepast beleid](active-directory-b2c-ui-customization-custom.md)om aan de slag te gaan.

>[!NOTE]
>In het Azure AD B2C-artikel, de [aanpassing van gebruikers interface configureren in een aangepast beleid](active-directory-b2c-ui-customization-custom.md), worden de volgende basis principes besproken:
> * De aanpassings functie van de gebruikers interface (UI) van de pagina.
> * Essentiële hulpprogram ma's voor het testen van de functie voor het aanpassen van de pagina UI met behulp van onze voorbeeld inhoud.
> * De kern elementen van de gebruikers interface van elk pagina Type.
> * Aanbevolen procedures voor het Toep assen van deze functie.

## <a name="add-a-link-to-html5css-templates-to-your-user-journey"></a>Een koppeling naar HTML5/CSS-sjablonen toevoegen aan uw gebruikers traject

In een aangepast beleid definieert een inhouds definitie de HTML5-pagina-URI die wordt gebruikt voor een opgegeven UI-stap (bijvoorbeeld de aanmeld-of registratie pagina's). Het basis beleid definieert de standaard vormgeving door een URI van HTML5-bestanden (in de CSS) aan te wijzen. In het uitbrei ding beleid kunt u het uiterlijk wijzigen door de LoadUri voor het HTML5-bestand te overschrijven. Inhouds definities bevatten Url's naar externe inhoud die is gedefinieerd door HTML5/CSS-bestanden te bestellen, indien van toepassing.

De sectie `ContentDefinitions` bevat een reeks `ContentDefinition` XML-elementen. Met het kenmerk ID van het `ContentDefinition` element wordt het type pagina opgegeven dat is gekoppeld aan de inhouds definitie. Dat wil zeggen dat het element de context definieert waarvoor een aangepaste HTML5/CSS-sjabloon moet worden toegepast. In de volgende tabel wordt de set met inhouds definitie-Id's beschreven die worden herkend door de IEF-engine en de pagina typen die aan hen zijn gerelateerd.

| ID van de inhouds definitie | Standaard HTML5-sjabloon| Beschrijving |
|-----------------------|--------|-------------|
| *api.error* | [exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Fout pagina**. Deze pagina wordt weer gegeven wanneer er een uitzonde ring of een fout wordt aangetroffen. |
| *api.idpselections* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | Pagina voor het selecteren van de **identiteits provider**. Op deze pagina vindt u de id-providers waaruit gebruikers kunnen kiezen tijdens het aanmelden. De opties zijn doorgaans ondernemings-id-providers, sociale id-providers zoals Facebook en Google + of lokale accounts. |
| *api.idpselections.signup* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Selectie van ID-provider voor registratie**. Op deze pagina vindt u de id-providers waaruit gebruikers kunnen kiezen tijdens het aanmelden. De opties zijn ondernemings-id-providers, sociale id-providers, zoals Facebook, Google + of lokale accounts. |
| *api.localaccountpasswordreset* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Wachtwoord pagina verg eten**. Deze pagina bevat een formulier dat gebruikers moeten volt ooien om het opnieuw instellen van een wacht woord te initiëren.  |
| *api.localaccountsignin* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Aanmeldings pagina voor het lokale account**. Deze pagina bevat een formulier voor het aanmelden met een lokaal account dat is gebaseerd op een e-mail adres of een gebruikers naam. Het formulier kan een tekstvak voor tekst invoer en wacht woord bevatten. |
| *api.localaccountsignup* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | De **registratie pagina van het lokale account**. Deze pagina bevat een formulier voor het aanmelden voor een lokaal account dat is gebaseerd op een e-mail adres of een gebruikers naam. Het formulier kan verschillende invoer besturings elementen bevatten, zoals een tekstinvoervak, een vak voor het invoeren van een wacht woord, een keuze rondje, vervolg keuze vakjes en meervoudige selectie vakjes. |
| *api.phonefactor* | [multifactor-1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Multi-factor Authentication-pagina**. Op deze pagina kunnen gebruikers hun telefoon nummers (met behulp van tekst of spraak) verifiëren tijdens het registreren of aanmelden. |
| *api.selfasserted* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | De **registratie pagina voor het sociaal account**. Deze pagina bevat een formulier dat gebruikers moeten volt ooien wanneer ze zich aanmelden met behulp van een bestaand account van een sociale ID-provider. Deze pagina is vergelijkbaar met de voor gaande aanmeldings pagina voor het sociaal account, met uitzonde ring van de velden voor het invoeren van wacht woorden. |
| *api.selfasserted.profileupdate* | [updateprofile. html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Pagina Profiel bijwerken**. Deze pagina bevat een formulier dat gebruikers kunnen gebruiken om hun profiel bij te werken. Deze pagina is vergelijkbaar met de aanmeldings pagina voor het sociaal account, met uitzonde ring van de velden voor het invoeren van wacht woorden. |
| *api.signuporsignin* | [Unified. html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Uniforme registratie-of aanmeldings pagina**. Op deze pagina worden de gebruikers registratie en het aanmeldings proces afgehandeld. Gebruikers kunnen ondernemings-id-providers, sociale id-providers, zoals Facebook of Google + of lokale accounts gebruiken.  |

## <a name="serving-dynamic-content"></a>Ophalen van dynamische inhoud
In de [gebruikers interface aanpassing configureren in een aangepast beleids](active-directory-b2c-ui-customization-custom.md) artikel uploadt u HTML5-bestanden naar Azure Blob-opslag. Deze HTML5-bestanden zijn statisch en genereren dezelfde HTML-inhoud voor elke aanvraag.

In dit artikel gebruikt u een ASP.NET-Web-app, waarmee query reeks parameters kunnen worden geaccepteerd en dienovereenkomstig kan worden gereageerd.

In dit scenario kunt u het volgende doen:
* Maak een ASP.NET Core-webtoepassing die uw HTML5-sjablonen host.
* Voeg een aangepaste HTML5-sjabloon, _Unified. cshtml_, toe.
* Publiceer uw web-app naar Azure App Service.
* CORS (cross-Origin Resource Sharing) instellen voor uw web-app.
* Overschrijf de `LoadUri` elementen zodat deze verwijzen naar uw HTML5-bestand.

## <a name="step-1-create-an-aspnet-web-app"></a>Stap 1: een ASP.NET-Web-app maken

1. Maak in Visual Studio een project door **bestand** > **Nieuw** > **project**te selecteren.

2. Selecteer in het venster **Nieuw project** de **optie C# Visual** > **Web** > **ASP.net core Web Application (.net core)** .

3. Geef de toepassing een naam (bijvoorbeeld *contoso. AADB2C. UI*) en selecteer **OK**.

    ![Nieuw Visual Studio-project maken](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project1.png)

4. Selecteer de sjabloon voor de **Webtoepassing** .

5. Stel de verificatie in op **geen verificatie**.

    ![Sjabloon Webtoepassing selecteren](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project2.png)

6. Selecteer **OK** om het project te maken.

## <a name="step-2-create-mvc-view"></a>Stap 2: MVC-weer gave maken
### <a name="step-21-download-the-b2c-built-in-html5-template"></a>Stap 2,1: de ingebouwde HTML5-sjabloon van B2C downloaden
Uw aangepaste HTML5-sjabloon is gebaseerd op de Azure AD B2C ingebouwde HTML5-sjabloon. U kunt het [Unified. html-bestand](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) downloaden of de sjabloon downloaden van het [Starter Pack](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates/wingtip). U gebruikt dit HTML5-bestand om een uniforme registratie-of aanmeldings pagina te maken.

### <a name="step-22-add-the-mvc-view"></a>Stap 2,2: de MVC-weer gave toevoegen
1. Klik met de rechter muisknop op de weer gaven/basismap en voeg > **Nieuw item** **toe** .

    ![Menu-item nieuw item toevoegen in Visual Studio](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view1.png)

2. In het venster **Nieuw item toevoegen-contoso. AADB2C. UI** selecteert u **web > ASP.net**.

3. Selecteer de **pagina MVC-weer gave**.

4. Wijzig in het vak **naam** de naam in **Unified. cshtml**.

5. Selecteer **Toevoegen**.

    ![Dialoog venster Nieuw item toevoegen in Visual Studio met MVC-weergave pagina gemarkeerd](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view2.png)

6. Als het *Unified. cshtml* -bestand nog niet is geopend, dubbelklikt u op het bestand om het te openen en wist u vervolgens de bestands inhoud.

7. Voor dit scenario verwijdert u de verwijzing naar de indelings pagina. Voeg het volgende code fragment toe aan _Unified. cshtml_:

    ```csharp
    @{
        Layout = null;
    }
    ```

8. Open het _Unified. cshtml_ -bestand dat u hebt gedownload van Azure AD B2C ingebouwde HTML5-sjabloon.

9. Vervang de enkele apen Staartjes (@) door een dubbele teken (@ @).

10. Kopieer de inhoud van het bestand en plak het onder de indelings definitie. De code moet er als volgt uitzien:

    ![Unified. cshtml-bestand na het toevoegen van de HTML5](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-edit-view1.png)

### <a name="step-23-change-the-background-image"></a>Stap 2,3: de achtergrond afbeelding wijzigen

Zoek het `<img>`-element dat de `ID` waarde *background_background_image*bevat en vervang vervolgens de `src` waarde door **https://kbdevstorage1.blob.core.windows.net/asset-blobs/19889_en_1** of een andere achtergrond afbeelding die u wilt gebruiken.

![IMG-element met aangepaste background_background_image src-waarde](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-static-background.png)

### <a name="step-24-add-your-view-to-the-mvc-controller"></a>Stap 2,4: Voeg uw weer gave toe aan de MVC-controller

1. Open **Controllers\HomeController.cs**en voeg de volgende methode toe:

    ```C
    public IActionResult unified()
    {
        return View();
    }
    ```
    Deze code geeft aan dat de methode een *weergave* sjabloon bestand moet gebruiken om een reactie op de browser weer te geven. Omdat we de naam van het *weergave* sjabloon bestand niet expliciet opgeeft, wordt standaard het bestand _Unified. cshtml_ weer gegeven in de map */views/Home* .

    Nadat u de _uniforme_ methode hebt toegevoegd, moet uw code er als volgt uitzien:

    ![De controller wijzigen om de weer gave te renderen](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-controller-view.png)

2. Spoor fouten op in uw web-app en zorg ervoor dat de _uniforme_ pagina toegankelijk is (bijvoorbeeld `http://localhost:<Port number>/Home/unified`).

### <a name="step-25-publish-to-azure"></a>Stap 2,5: publiceren naar Azure
1. Klik in **Solution Explorer**met de rechter muisknop op het project **contoso. AADB2C. UI** en selecteer vervolgens **publiceren**.

    ![Publiceren naar Microsoft Azure App Service](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish1.png)

2. Selecteer de tegel **Microsoft Azure app service** en selecteer vervolgens **publiceren**.

    ![Nieuwe Microsoft Azure maken App Service](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish2.png)

    Het venster **app service maken** wordt geopend. In kunt u beginnen met het maken van alle benodigde Azure-resources om de ASP.NET-Web-app in azure uit te voeren.

    > [!NOTE]
    > Zie [een ASP.net-Web-app maken in azure](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-dotnet)voor meer informatie over publiceren.

3. Typ in het vak **Web-app-naam** een unieke app-naam (geldige tekens zijn a-z, a-z, 0-9 en het koppel teken (-). De URL van de web-app is `http://<app_name>.azurewebsites.NET`, waarbij `<app_name>` de naam van uw web-app is. U kunt de automatisch gegenereerde naam accepteren, die uiteraard uniek is.

4. Selecteer **Maken** om de Azure-resources te gaan maken.

    ![App Service eigenschappen opgeven](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish3.png)

    Nadat het proces is gemaakt, publiceert de wizard de ASP.NET-Web-app naar Azure en wordt de app vervolgens gestart in de standaard browser.

5. Kopieer de URL van de _gecombineerde_ pagina (bijvoorbeeld _https://< app_name >. azurewebsites. net/Home/Unified_).

## <a name="step-3-configure-cors-in-azure-app-service"></a>Stap 3: CORS configureren in Azure App Service
1. Selecteer in de [Azure Portal](https://portal.azure.com/) **app Services**en selecteer vervolgens de naam van uw API-app.

    ![Selecteer API-app in de Azure Portal](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS1.png)

2. Ga naar de sectie **instellingen** en selecteer **CORS**in het gedeelte **API** .

    ![Het menu-item CORS is gemarkeerd in App Service menu in Azure Portal](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS2.png)

3. Voer een van de volgende handelingen uit in het venster **CORS** in het vak **toegestane oorsprongen** :

    * Voer de URL of Url's in die u wilt toestaan dat Java script-aanroepen afkomstig zijn van. U moet alle kleine letters gebruiken in de Url's die u invoert.
    * Voer een asterisk (*) in om op te geven dat alle oorspronkelijke domeinen worden geaccepteerd.

4. Selecteer **Opslaan**.

    ![Pagina CORS-instellingen met sterretje gemarkeerd in toegestane oorsprongen](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS3.png)

    Nadat u **Opslaan**hebt geselecteerd, accepteert de API-app java script-aanroepen vanuit de opgegeven url's.

## <a name="step-4-html5-template-validation"></a>Stap 4: validatie van HTML5-sjabloon
Uw HTML5-sjabloon is klaar voor gebruik. Het is echter niet beschikbaar in de `ContentDefinition` code. Voordat u `ContentDefinition` aan uw aangepaste beleid kunt toevoegen, moet u het volgende doen:
* Uw inhoud is compatibel met HTML5 en is toegankelijk.
* De inhouds server is ingeschakeld voor CORS.

    >[!NOTE]
    >Ga naar de website van [test-cors.org](https://test-cors.org/) om te controleren of de site waar u de inhoud host, cors heeft ingeschakeld en cors-aanvragen kan testen.

* Uw aangeboden inhoud is beveiligd via **https**.
* U gebruikt *absolute url's*, zoals `https://yourdomain/content`, voor alle koppelingen, CSS-inhoud en afbeeldingen.

## <a name="step-5-configure-your-content-definition"></a>Stap 5: de inhouds definitie configureren
Ga als volgt te werk om `ContentDefinition`te configureren:
1. Open het basis bestand van uw beleid (bijvoorbeeld *TrustFrameworkBase. XML*).

2. Zoek het `<ContentDefinitions>`-element en kopieer de volledige inhoud van het knoop punt `<ContentDefinitions>`.

3. Open het extensie bestand (bijvoorbeeld *TrustFrameworkExtensions. XML*) en zoek vervolgens naar het `<BuildingBlocks>`-element. Als het element niet bestaat, voegt u het toe.

4. Plak de volledige inhoud van het `<ContentDefinitions>` knoop punt dat u als onderliggend item van het `<BuildingBlocks>` element hebt gekopieerd.

5. Zoek naar het `<ContentDefinition>` knoop punt dat `Id="api.signuporsignin"` bevat in de XML die u hebt gekopieerd.

6. Wijzig de waarde van `LoadUri` van _~/Tenant/default/Unified_ in _https://< app_name >. azurewebsites. net/Home/Unified_.
    Het aangepaste beleid moet er als volgt uitzien:

    ![Voor beeld van een XML-fragment met LoadUri-element gemarkeerd](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-content-definition.png)

## <a name="step-6-upload-the-policy-to-your-tenant"></a>Stap 6: het beleid uploaden naar uw Tenant
1. Selecteer in de [Azure Portal](https://portal.azure.com)het pictogram voor het adres van de map en het **abonnement** op de werk balk van de portal en selecteer vervolgens de map die de Azure AD B2C Tenant bevat.

1. Zoek in het Azure Portal naar en selecteer **Azure AD B2C**.

1. Selecteer een **Framework voor identiteits ervaring**.

1. Selecteer **alle beleids regels**.

1. Selecteer **beleid uploaden**.

1. Selecteer het selectie vakje het **beleid overschrijven als dit bestaat** .

1. Upload het bestand *TrustFrameworkExtensions. XML* en zorg ervoor dat het validatie wordt door gegeven.

## <a name="step-7-test-the-custom-policy-by-using-run-now"></a>Stap 7: het aangepaste beleid testen met behulp van nu uitvoeren
1. Selecteer **Azure AD B2C instellingen**en selecteer vervolgens **Framework voor identiteits ervaring**.

    >[!NOTE]
    >Voor het uitvoeren van nu moet ten minste één toepassing vooraf worden geregistreerd op de Tenant. Zie het artikel Azure AD B2C [aan de slag](active-directory-b2c-get-started.md) of het artikel over het registreren van [toepassingen](active-directory-b2c-app-registration.md) voor meer informatie over het registreren van toepassingen.

2. Open **B2C_1A_signup_signin**, het aangepaste beleid RELYING Party (RP) dat u hebt geüpload en selecteer **nu uitvoeren**.
    U moet uw aangepaste HTML5 kunnen zien met de achtergrond die u eerder hebt gemaakt.

    ![Uw registratie-of aanmeldings beleid](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo1.png)

## <a name="step-8-add-dynamic-content"></a>Stap 8: dynamische inhoud toevoegen
Wijzig de achtergrond op basis van de query teken reeks parameter met de naam _campaignId_. Uw RP-toepassing (web-en mobiele apps) verzendt de para meter naar Azure AD B2C. Met uw beleid wordt de para meter gelezen en wordt de waarde ervan naar uw HTML5-sjabloon verzonden.

### <a name="step-81-add-a-content-definition-parameter"></a>Stap 8,1: een inhouds definitie parameter toevoegen

Voeg het `ContentDefinitionParameters`-element toe door het volgende te doen:
1. Open het *SignUpOrSignin* -bestand van uw beleid (bijvoorbeeld *SignUpOrSignin. XML*).

2. Voeg onder het knoop punt `<DefaultUserJourney>` het knoop punt `UserJourneyBehaviors` toe:

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <UserJourneyBehaviors>
        <ContentDefinitionParameters>
          <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
        </ContentDefinitionParameters>
      </UserJourneyBehaviors>
      ...
    </RelyingParty>
    ```

### <a name="step-82-change-your-code-to-accept-a-query-string-parameter-and-replace-the-background-image"></a>Stap 8,2: Wijzig uw code zodat deze een query teken reeks parameter accepteert en vervang de achtergrond afbeelding
Wijzig de HomeController-methode `unified` om de campaignId-para meter te accepteren. De methode controleert vervolgens de waarde van de para meter en stelt de `ViewData["background"]` variabele dienovereenkomstig in.

1. Open het *Controllers\HomeController.cs* -bestand en wijzig de `unified` methode door het volgende code fragment toe te voegen:

    ```csharp
    public IActionResult unified(string campaignId)
    {
        // If campaign ID is Hawaii, show Hawaii background
        if (campaignId != null && campaignId.ToLower() == "hawaii")
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/19889_en_1";
        }
        // If campaign ID is Tokyo, show Tokyo background
        else if (campaignId != null && campaignId.ToLower() == "tokyo")
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/19666_en_1";
        }
        // Default background
        else
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/18983_en_1";
        }

        return View();
    }

    ```

2. Zoek het `<img>`-element met ID `background_background_image`en vervang de `src` waarde door `@ViewData["background"]`.

    ![IMG-element met src-waarde gemarkeerd ](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-dynamic-background.png)

### <a name="83-upload-the-changes-and-publish-your-policy"></a>8,3: de wijzigingen uploaden en uw beleid publiceren
1. Publiceer uw Visual Studio-project naar Azure App Service.

2. Upload het *SignUpOrSignin. XML-* beleid naar Azure AD B2C.

3. Open **B2C_1A_signup_signin**, het aangepaste RP-beleid dat u hebt geüpload en selecteer **nu uitvoeren**.
    U ziet nu dezelfde achtergrond afbeelding die eerder werd weer gegeven.

4. Kopieer de URL uit de adres balk van de browser.

5. Voeg de query reeks parameter _campaignId_ toe aan de URI. Voeg bijvoorbeeld `&campaignId=hawaii`toe, zoals wordt weer gegeven in de volgende afbeelding:

    ![URI met de query teken reeks parameter campaignId gemarkeerd](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-campaignId-param.png)

6. Selecteer **Enter** om de achtergrond afbeelding van de Hawaï weer te geven.

    ![Aanmeldings pagina voor aanmelding met Hawaï aangepaste achtergrond](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo2.png)

7. Wijzig de waarde in *Tokio*en selecteer vervolgens **Enter**.
    In de browser wordt de Tokyo-achtergrond weer gegeven.

    ![Aanmeldings pagina voor aanmelding met Tokyo-afbeeldings achtergrond](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo3.png)

## <a name="step-9-change-the-rest-of-the-user-journey"></a>Stap 9: de rest van de reis van de gebruiker wijzigen
Als u de koppeling **nu registreren** op de aanmeldings pagina selecteert, wordt in de browser de standaard achtergrond afbeelding weer gegeven, niet de installatie kopie die u hebt gedefinieerd. Dit probleem doet zich voor omdat u alleen de registratie-of aanmeldings pagina hebt gewijzigd. De rest van de definities van de Self-assert-inhoud wijzigen:
1. Ga terug naar stap 2 en Ga als volgt te werk:

    a. Down load het *selfasserted* -bestand.

    b. Kopieer de bestands inhoud.

    c. Maak een nieuwe weer gave *selfasserted*.

    d. Voeg *selfasserted* toe aan de **Start** controller.

2. Ga terug naar ' stap 4 ' en voer de volgende handelingen uit:

    a. Zoek in uw uitbrei ding beleid het `<ContentDefinition>` knoop punt dat `Id="api.selfasserted"`, `Id="api.localaccountsignup"`en `Id="api.localaccountpasswordreset"`bevat.

    b. Stel het kenmerk `LoadUri` in op uw *selfasserted* -URI.

3. Ga terug naar stap 8,2 en wijzig uw code zodat de query teken reeks parameters worden geaccepteerd, maar deze keer naar de functie *selfasserted* .

4. Upload het beleid *TrustFrameworkExtensions. XML* en zorg ervoor dat het validatie wordt door gegeven.

5. Voer de beleids test uit en selecteer **nu registreren** om het resultaat weer te geven.

## <a name="optional-download-the-complete-policy-files-and-code"></a>Beschrijving De volledige beleids bestanden en code downloaden
* Nadat u de walkthrough aan de [slag met aangepast beleid](active-directory-b2c-get-started-custom.md) hebt voltooid, wordt u aangeraden om uw scenario te bouwen met behulp van uw eigen aangepaste beleids bestanden. Voor uw referentie hebben we [voorbeeld beleids bestanden](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization)gegeven.
* U kunt de volledige code downloaden van een [voor beeld van een Visual Studio-oplossing voor referentie](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization).
