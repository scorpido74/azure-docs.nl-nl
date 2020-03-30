---
title: Microsoft-identiteitsplatform Python daemon | Azure
description: Meer informatie over hoe een Python-proces een toegangstoken kan krijgen en een API kan aanroepen die is beschermd door het eindpunt van het Microsoft-identiteitsplatform, met behulp van de eigen identiteit van de app
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/22/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:Python
ms.openlocfilehash: c0967c411aa10d046caee13441b046bf0f462442
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78274332"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-python-console-app-using-apps-identity"></a>Snelstart: een token en aanroepen van Microsoft Graph API vanuit een Python-console-app met behulp van de identiteit van de app

Schrijf in deze quickstart een Python-toepassing die een toegangstoken krijgt met de identiteit van de app en roept vervolgens de Microsoft Graph API aan om een [lijst met gebruikers](https://docs.microsoft.com/graph/api/user-list) in de directory weer te geven. Dit scenario is nuttig in situaties waar een headless taak zonder toezicht of een Windows-service moet worden uitgevoerd met een toepassings-id in plaats van de identiteit van een gebruiker.

> [!div renderon="docs"]
> ![Laat zien hoe de voorbeeld-app die door deze quickstart wordt gegenereerd, werkt](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)

## <a name="prerequisites"></a>Vereisten

Als u dit voorbeeld wilt uitvoeren, moet u het volgende doen:

- [Python 2.7+](https://www.python.org/downloads/release/python-2713) of [Python 3+](https://www.python.org/downloads/release/python-364/)
- [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>De snelstart-app registreren en downloaden

> [!div renderon="docs" class="sxs-lookup"]
>
> Je hebt twee opties om je snelle starttoepassing te starten: Express (optie 1 hieronder) en Manual (optie 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Optie 1: de app registreren en automatisch configureren, en vervolgens de voorbeeldcode downloaden
>
> 1. Ga naar de nieuwe [Azure-portal - deelvenster App-registraties.](https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/PythonDaemonQuickstartPage/sourceType/docs)
> 1. Voer een naam in voor de toepassing en selecteer **Registreren**.
> 1. Volg de instructies om de nieuwe toepassing met slechts één klik te downloaden en automatisch te configureren.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Optie 2: registreer de toepassing en configureer handmatig de toepassing en het codevoorbeeld

> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Stap 1: Uw toepassing registreren
> Volg deze stappen om de toepassing te registreren en de registratiegegevens van de app handmatig toe te voegen aan uw oplossing:
>
> 1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
> 1. Als u via uw account toegang tot meer dan één tenant hebt, selecteert u uw account in de rechterbovenhoek en stelt u uw portalsessie in op de gewenste Azure Active Directory-tenant.
> 1. Navigeer naar de pagina Microsoft-identiteitsplatform voor ontwikkelaars [App-registraties.](https://go.microsoft.com/fwlink/?linkid=2083908)
> 1. Selecteer **Nieuwe registratie**.
> 1. Wanneer de pagina **Een aanvraag registreren** wordt weergegeven, voert u de registratiegegevens van uw aanvraag in. 
> 1. Voer in de sectie **Naam** een beschrijvende toepassingsnaam in die wordt weergegeven voor gebruikers van de app, zoals `Daemon-console`. Selecteer vervolgens **Registreren** om de toepassing te maken.
> 1. Na het registreren opent u het menu **Certificaten en geheimen**.
> 1. Onder **Clientgeheimen** selecteert u **+ Nieuw clientgeheim**. Geef het clientgeheim een naam en selecteer **Toevoegen**. Kopieer het geheim naar een veilige locatie. U hebt dit nodig voor gebruik in uw code.
> 1. Open nu het menu **API-machtigingen**, selecteer de knop **+ Een geheim toevoegen** en selecteer **Microsoft Graph**.
> 1. Selecteer **Toepassingsmachtigingen**.
> 1. Selecteer onder het knooppunt **Gebruiker** de optie **User.Read.All** en selecteer vervolgens **Machtigingen toevoegen**

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-your-quickstart-app"></a>Uw snelstart-app downloaden en configureren
> 
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Stap 1: Uw toepassing configureren in Azure Portal
> Om ervoor te zorgen dat het codevoorbeeld voor deze quickstart werkt, moet u een clientgeheim maken en de toepassingstoestemming **User.Read.All** van Graph API toevoegen.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Breng deze wijzigingen voor mij aan]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Al geconfigureerd](media/quickstart-v2-netcore-daemon/green-check.png) Uw toepassing is al geconfigureerd met deze kenmerken.

#### <a name="step-2-download-your-python-project"></a>Stap 2: Download je Python-project

> [!div renderon="docs"]
> [Download het Python daemon project](https://github.com/Azure-Samples/ms-identity-python-daemon/archive/master.zip)

> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [Het codevoorbeeld downloaden](https://github.com/Azure-Samples/ms-identity-python-daemon/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Enter_the_Supported_Account_Info_Here


> [!div renderon="docs"]
> #### <a name="step-3-configure-your-python-project"></a>Stap 3: Uw Python-project configureren
> 
> 1. Pak het zip-bestand uit in een lokale map dicht bij de hoofdmap van de schijf, bijvoorbeeld **C:\Azure-Samples**.
> 1. Navigeer naar de submap **1-Call-MsGraph-WithSecret"**.
> 1. **Parameters.json** bewerken en de waarden `authority`van `client_id`de `secret` velden vervangen en het volgende fragment:
>
>    ```json
>    "authority": "https://login.microsoftonline.com/Enter_the_Tenant_Id_Here",
>    "client_id": "Enter_the_Application_Id_Here",
>    "secret": "Enter_the_Client_Secret_Here"
>    ```
>    Waar:
>    - `Enter_the_Application_Id_Here`: is de **toepassings-id (client-id)** voor de toepassing die u hebt geregistreerd.
>    - `Enter_the_Tenant_Id_Here`: vervang deze waarde door de **Tenant-id** of **tenantnaam** (bijvoorbeeld contoso.microsoft.com)
>    - `Enter_the_Client_Secret_Here`: vervang deze waarde door het clientgeheim dat is gemaakt in stap 1.
>
> > [!TIP]
> > Om de waarden van **Toepassings-id (client-id)** en **Map-id (tenant-id)** te achterhalen, gaat u naar de **Overzichtspagina** van de app in de Azure-portal. Voor het genereren van een nieuwe sleutel gaat u naar de pagina **Certificaten en geheimen**.
    
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>Stap 3: Toestemming van de beheerder

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>Stap 4: Toestemming van de beheerder

Als u de toepassing op dit punt probeert uit te voeren, ontvangt u *HTTP 403 - Verboden* fout: `Insufficient privileges to complete the operation`. Deze fout treedt op omdat voor elke *app-only toestemming* toestemming vereist: een globale beheerder van uw directory moet toestemming geven voor uw toepassing. Selecteer een van de onderstaande opties, afhankelijk van uw rol:

##### <a name="global-tenant-administrator"></a>Globale tenantbeheerder

> [!div renderon="docs"]
> Als u een globale tenantbeheerder bent, gaat u naar de pagina **API-machtigingen** in de registratie van toepassingen (preview) in de Azure-portal en selecteer **Beheerder toestemming verlenen voor {tenantnaam}** (waarbij {tenantnaam} de naam van uw map is).

> [!div renderon="portal" class="sxs-lookup"]
> Als u een globale beheerder bent, gaat u naar de pagina **API-machtigingen** en selecteert u **Beheerder toestemming verlenen voor Enter_the_Tenant_Name_Here**
> > [!div id="apipermissionspage"]
> > [Ga naar de pagina API-machtigingen]()

##### <a name="standard-user"></a>Standaardgebruiker

Als u een standaardgebruiker van uw tenant bent, moet u een globale beheerder vragen om beheerderstoestemming te verlenen voor uw toepassing. Daarvoor verstrekt u de volgende URL aan uw beheerder:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
>> Waar:
>> * `Enter_the_Tenant_Id_Here`: vervang deze waarde door de **Tenant-id** of **tenantnaam** (bijvoorbeeld contoso.microsoft.com)
>> * `Enter_the_Application_Id_Here`: is de **toepassings-id (client-id)** voor de toepassing die u hebt geregistreerd.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>Stap 4: De toepassing uitvoeren

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>Stap 5: De toepassing uitvoeren

U moet de afhankelijkheden van dit voorbeeld één keer installeren

```console
pip install -r requirements.txt
```

Voer de toepassing vervolgens uit via opdrachtprompt of console:

```console
python confidential_client_secret_sample.py parameters.json
```

U moet op de console-uitvoer een aantal Json-fragment zien dat een lijst met gebruikers vertegenwoordigt in uw Azure AD-map.

> [!IMPORTANT]
> Deze quickstarttoepassing gebruikt een clientgeheim om zichzelf te identificeren als vertrouwelijke client. Omdat het clientgeheim als platte tekst aan uw projectbestanden wordt toegevoegd, wordt u om veiligheidsredenen aangeraden een certificaat te gebruiken in plaats van een clientgeheim voordat u de toepassing als productietoepassing beschouwt. Zie [deze instructies](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/2-Call-MsGraph-WithCertificate/README.md) in dezelfde GitHub-repository voor dit voorbeeld, maar in de tweede map **2-Call-MsGraph-WithCertificate** voor meer informatie over het gebruik van een certificaat.

## <a name="more-information"></a>Meer informatie

### <a name="msal-python"></a>MSAL Python

[MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) is de bibliotheek die wordt gebruikt om gebruikers aan te melden en tokens aan te vragen die worden gebruikt om toegang te krijgen tot een API die wordt beschermd door het identiteitsplatform van Microsoft. Zoals beschreven, vraagt deze quickstart tokens aan met behulp van de eigen identiteit van de toepassing in plaats van gedelegeerde machtigingen. De verificatiestroom die in dit voorbeeld wordt gebruikt, staat bekend als de *[oauth-stroom voor clientreferenties](v2-oauth2-client-creds-grant-flow.md)*. Zie [dit artikel](scenario-daemon-overview.md)voor meer informatie over het gebruik van MSAL Python met daemon-apps.

 U MSAL Python installeren door de volgende pip-opdracht uit te voeren.

```powershell
pip install msal
```

### <a name="msal-initialization"></a>MSAL initialiseren

U kunt de verwijzing voor MSAL toevoegen door de volgende code toe te voegen:

```Python
import msal
```

Vervolgens initialiseert u MSAL met de volgende code:

```Python
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential=config["secret"])
```

> | Waar: ||
> |---------|---------|
> | `config["secret"]` | Het clientgeheim is dat voor de toepassing in Azure-portal wordt gemaakt. |
> | `config["client_id"]` | Is de **Toepassings-id (client-id)** voor de toepassing die is geregistreerd in de Azure-portal. U vindt deze waarde op de pagina **Overzicht** in de Azure-portal. |
> | `config["authority"]`    | Het STS-eindpunt voor de gebruiker voor verificatie. Dat is meestal <https://login.microsoftonline.com/{tenant}> voor de openbare cloud, waarbij {tenant} de naam van uw tenant of uw tenant-id is.|

Zie de [naslagdocumentatie voor `ConfidentialClientApplication`](https://msal-python.readthedocs.io/en/latest/#confidentialclientapplication) voor meer informatie

### <a name="requesting-tokens"></a>Tokens aanvragen

Als u een token wilt aanvragen met behulp van de identiteit van de app, gebruikt u de `AcquireTokenForClient`-methode:

```Python
result = None
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])
```

> |Waar:| |
> |---------|---------|
> | `config["scope"]` | De aangevraagde bereiken bevat. Voor vertrouwelijke clients moet hiervoor de indeling worden gebruikt die vergelijkbaar is met `{Application ID URI}/.default` om aan te geven dat de aangevraagde bereiken dezelfde zijn die statisch zijn gedefinieerd in het app-object dat is ingesteld in de Azure-portal (voor Microsoft Graph verwijst `{Application ID URI}` naar `https://graph.microsoft.com`). Voor aangepaste Web-API's wordt `{Application ID URI}` gedefinieerd in de sectie **Een API beschikbaar maken** in de registratie van toepassingen (preview) van de Azure-portal. |

Zie de [naslagdocumentatie voor `AcquireTokenForClient`](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_for_client) voor meer informatie

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Volgende stappen

Zie de bestemmingspagina voor scenario's voor meer informatie over daemon-toepassingen

> [!div class="nextstepaction"]
> [Daemon-toepassing die web-API's aanroept](scenario-daemon-overview.md)

Zie voor de zelfstudie van de daemon-toepassing:

> [!div class="nextstepaction"]
> [Zelfstudie van Daemon Python-console](https://github.com/Azure-Samples/ms-identity-python-daemon)

Meer informatie over machtigingen en toestemming:

> [!div class="nextstepaction"]
> [Machtigingen en toestemming](v2-permissions-and-consent.md)

Zie de Oauth 2.0-clientreferentiestroom voor meer informatie over de auth-stroom voor dit scenario:

> [!div class="nextstepaction"]
> [Oauth-clientreferentiestroom](v2-oauth2-client-creds-grant-flow.md)

Help ons het Microsoft-identiteitsplatform te verbeteren. Vertel ons wat u ervan vindt door een korte enquête met twee vragen in te vullen.

> [!div class="nextstepaction"]
> [Enquête van microsoft-identiteitsplatform](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
