---
title: Aanmelding met Microsoft toevoegen aan een Python-web-app van Microsoft- identiteitsplatform | Azure
description: Meer informatie over het implementeren van Microsoft Sign-In op een Python Web App met OAuth2
services: active-directory
author: abhidnya13
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/25/2019
ms.author: abpati
ms.custom: aaddev
ms.openlocfilehash: a8ef0f172a8e9118eef2d2f8a11f3efbce665171
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80473533"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-python-web-app"></a>Snelstart: aanmelden met Microsoft toevoegen aan een Python-web-app

In deze quickstart leert u hoe u een Python-webtoepassing integreren met het Microsoft-identiteitsplatform. Uw app meldt zich aan bij een gebruiker, krijgt een toegangstoken om de Microsoft Graph-API aan te roepen en doet een verzoek voor de Microsoft Graph API.

Wanneer u de handleiding hebt voltooid, accepteert uw toepassing aanmeldingen van persoonlijke Microsoft-accounts (waaronder outlook.com, live.com en anderen) en werk- of schoolaccounts van bedrijven of organisaties die Azure Active Directory gebruiken. (Zie [hoe het voorbeeld werkt](#how-the-sample-works) voor een illustratie.)


## <a name="prerequisites"></a>Vereisten

Als u dit voorbeeld wilt uitvoeren, moet u het volgende doen:

- [Python 2.7+](https://www.python.org/downloads/release/python-2713) of [Python 3+](https://www.python.org/downloads/release/python-364/)
- [Kolf](http://flask.pocoo.org/), [Kolf-Sessie](https://pythonhosted.org/Flask-Session/), [verzoeken](https://requests.kennethreitz.org/en/master/)
- [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)

> [!div renderon="docs"]
>
> ## <a name="register-and-download-your-quickstart-app"></a>De snelstart-app registreren en downloaden
>
> Je hebt twee opties om je quickstart-applicatie te starten: express (optie 1) en handleiding (optie 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Optie 1: de app registreren en automatisch configureren, en vervolgens de voorbeeldcode downloaden
>
> 1. Ga naar de [Azure-portal - App-registraties](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps).
> 1. Selecteer **Nieuwe registratie**.
> 1. Voer een naam in voor de toepassing en selecteer **Registreren**.
> 1. Volg de instructies om uw nieuwe toepassing te downloaden en automatisch te configureren.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Optie 2: registreer de toepassing en configureer handmatig de toepassing en het codevoorbeeld
>
> #### <a name="step-1-register-your-application"></a>Stap 1: Uw toepassing registreren
>
> Volg deze stappen om de toepassing te registreren en de registratiegegevens van de app handmatig toe te voegen aan uw oplossing:
>
> 1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
> 1. Als u via uw account toegang tot meer dan één tenant hebt, selecteert u uw account in de rechterbovenhoek en stelt u uw portalsessie in op de gewenste Azure Active Directory-tenant.
> 1. Navigeer naar de pagina Microsoft-identiteitsplatform voor ontwikkelaars [App-registraties.](https://go.microsoft.com/fwlink/?linkid=2083908)
> 1. Selecteer **Nieuwe registratie**.
> 1. Wanneer de pagina **Een toepassing registreren** verschijnt, voert u de registratiegegevens van de toepassing in:
>      - Voer in de sectie **Naam** een beschrijvende toepassingsnaam in die zichtbaar is voor gebruikers van de app. Bijvoorbeeld: `python-webapp`.
>      - Selecteer onder **Ondersteunde accounttypen** de optie **Accounts in een organisatieadreslijst en persoonlijke Microsoft-account**.
>      - Selecteer onder de sectie **URI omleiden** in de vervolgkeuzelijst het **webplatform** en stel de waarde in op `http://localhost:5000/getAToken`.
>      - Selecteer **Registreren**. Noteer op de pagina **Overzicht** van de app de waarde **van de id-toepassing (client)** voor later gebruik.
> 1. Kies in het linkermenu **Certificaten & geheimen** en klik op Nieuw **clientgeheim** in de sectie **Clientgeheimen:**
>
>      - Typ een sleutelbeschrijving (van instantie-app-geheim).
>      - Selecteer een sleutelduur van **in 1 jaar.**
>      - Wanneer u op **Toevoegen**klikt, wordt de waarde van de sleutel weergegeven.
>      - Kopieer de waarde van de sleutel. U hebt dit later nodig.
> 1. De sectie **API-machtigingen** selecteren
>
>      - Klik **op** de knop Een machtiging toevoegen en vervolgens op de knop Een machtiging toevoegen en vervolgens
>      - Controleren of het tabblad **Microsoft API's** is geselecteerd
>      - Klik in de sectie *Veelgebruikte Microsoft API's* op **Microsoft Graph**
>      - Controleer in de sectie **Gedelegeerde machtigingen** of de juiste machtigingen zijn ingeschakeld: **User.ReadBasic.All**. Gebruik indien nodig het zoekvak.
>      - De knop **Machtigingen toevoegen selecteren**
>
> [!div class="sxs-lookup" renderon="portal"]
>
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Stap 1: Uw toepassing configureren in Azure Portal
>
> Voor het codevoorbeeld voor deze snelle start om te werken, moet u:
>
> 1. Een antwoord-URL `http://localhost:5000/getAToken`toevoegen als .
> 1. Maak een clientgeheim.
> 1. De gebruiker van de Microsoft Graph API toevoegen.ReadBasic.Alle gedelegeerde machtiging.
>
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Breng deze wijzigingen voor mij aan]()
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Al geconfigureerd](media/quickstart-v2-aspnet-webapp/green-check.png) Uw toepassing is al geconfigureerd met dit kenmerk

#### <a name="step-2-download-your-project"></a>Stap 2: Uw project downloaden
> [!div renderon="docs"]
> [Het voorbeeld van de code downloaden](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Download het project en haal het zip-bestand naar een lokale map dichter bij de hoofdmap - bijvoorbeeld **C:\Azure-Samples**
> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [Het codevoorbeeld downloaden](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-application"></a>Stap 3: De toepassing configureren
> 
> 1. Pak het zip-bestand uit in een lokale map dichter bij de hoofdmap (bijvoorbeeld **C:\Azure-Samples**)
> 1. Als u een geïntegreerde ontwikkelomgeving gebruikt, opent u het voorbeeld in uw favoriete IDE (optioneel).
> 1. Open het **app_config.py-bestand,** dat in de hoofdmap te vinden is en vervang het volgende codefragment:
> 
> ```python
> CLIENT_ID = "Enter_the_Application_Id_here"
> CLIENT_SECRET = "Enter_the_Client_Secret_Here"
> AUTHORITY = "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here"
> ```
> Waar:
>
> - `Enter_the_Application_Id_here`: de toepassings-id voor de toepassing die u hebt geregistreerd.
> - `Enter_the_Client_Secret_Here`- is het **clientgeheim dat** u hebt gemaakt in **Certificaten & Geheimen** voor de door u geregistreerde toepassing.
> - `Enter_the_Tenant_Name_Here`- is de **directory (tenant) ID-waarde** van de toepassing die u hebt geregistreerd.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-run-the-code-sample"></a>Stap 3: Het codevoorbeeld uitvoeren

> [!div renderon="docs"]
> #### <a name="step-4-run-the-code-sample"></a>Stap 4: Het codevoorbeeld uitvoeren

1. U moet MSAL Python library, Flask framework, Flask-Sessions installeren voor server-side sessiebeheer en aanvragen met pip als volgt:

    ```Shell
    pip install -r requirements.txt
    ```

2. Voer app.py uit van de shell- of opdrachtregel:

    ```Shell
    python app.py
    ```
   > [!IMPORTANT]
   > Deze quickstarttoepassing gebruikt een clientgeheim om zichzelf te identificeren als vertrouwelijke client. Omdat het clientgeheim als platte tekst aan uw projectbestanden wordt toegevoegd, wordt u om veiligheidsredenen aangeraden een certificaat te gebruiken in plaats van een clientgeheim voordat u de toepassing als productietoepassing beschouwt. Zie [deze instructies](https://docs.microsoft.com/azure/active-directory/develop/active-directory-certificate-credentials)voor meer informatie over het gebruik van een certificaat.

## <a name="more-information"></a>Meer informatie

### <a name="how-the-sample-works"></a>Hoe het voorbeeld werkt
![Laat zien hoe de voorbeeld-app die door deze quickstart wordt gegenereerd, werkt](media/quickstart-v2-python-webapp/python-quickstart.svg)

### <a name="getting-msal"></a>MsAL krijgen
MSAL is de bibliotheek die wordt gebruikt om gebruikers aan te melden en tokens aan te vragen die worden gebruikt om toegang te krijgen tot een API die wordt beschermd door het Microsoft-identiteitsplatform.
U MSAL Python aan uw toepassing toevoegen met Pip.

```Shell
pip install msal
```

### <a name="msal-initialization"></a>MSAL initialiseren
U de verwijzing naar MSAL Python toevoegen door de volgende code toe te voegen aan de bovenkant van het bestand waar u MSAL gaat gebruiken:

```Python
import msal
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over web-apps die zich aanmelden bij gebruikers en vervolgens web-API's oproepen:

> [!div class="nextstepaction"]
> [Scenario: web-apps die gebruikers aanmelden](scenario-web-app-sign-user-overview.md)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
