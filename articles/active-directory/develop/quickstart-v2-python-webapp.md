---
title: Aanmelden met micro soft toevoegen aan een micro soft Identity platform python-web-app | Azure
description: Meer informatie over het implementeren van micro soft-aanmelding op een Python-web-app met OAuth2
services: active-directory
author: abhidnya13
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/25/2019
ms.author: abpati
ms.custom: aaddev, scenarios:getting-started, languages:Python
ms.openlocfilehash: 549df101e2bab81766b7b40e659b31457c9994d3
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82592788"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-python-web-app"></a>Snelstartgids: aanmelden toevoegen met micro soft aan een Python-web-app

In deze Quick Start leert u hoe u een python-webtoepassing integreert met het micro soft Identity-platform. Uw app meldt zich aan bij een gebruiker, haalt een toegangs token op om de Microsoft Graph-API aan te roepen en brengt een aanvraag naar de Microsoft Graph-API.

Wanneer u de hand leiding hebt voltooid, accepteert uw toepassing aanmeldingen van persoonlijke micro soft-accounts (waaronder outlook.com, live.com en anderen) en werk-of school accounts van elk bedrijf of organisatie die gebruikmaakt van Azure Active Directory. (Zie [hoe het voor beeld werkt](#how-the-sample-works) voor een illustratie.)

## <a name="prerequisites"></a>Vereisten

Als u dit voor beeld wilt uitvoeren, hebt u het volgende nodig:

- [Python 2.7 +](https://www.python.org/downloads/release/python-2713) of [python 3 +](https://www.python.org/downloads/release/python-364/)
- [Kolf](http://flask.pocoo.org/), [kolf-sessie](https://pythonhosted.org/Flask-Session/), [aanvragen](https://requests.kennethreitz.org/en/master/)
- [MSAL python](https://github.com/AzureAD/microsoft-authentication-library-for-python)

> [!div renderon="docs"]
>
> ## <a name="register-and-download-your-quickstart-app"></a>De snelstart-app registreren en downloaden
>
> U hebt twee opties om uw Quick Start-toepassing te starten: Express (optie 1) en hand matig (optie 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Optie 1: de app registreren en automatisch configureren, en vervolgens de voorbeeldcode downloaden
>
> 1. Ga naar de [Azure Portal-app-registraties](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/PythonQuickstartPage/sourceType/docs).
> 1. Voer een naam in voor de toepassing en selecteer **Registreren**.
> 1. Volg de instructies om uw nieuwe toepassing te downloaden en automatisch te configureren.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Optie 2: registreer de toepassing en configureer handmatig de toepassing en het codevoorbeeld
>
> #### <a name="step-1-register-your-application"></a>Stap 1: Uw toepassing registreren
>
> Volg deze stappen om de toepassing te registreren en de registratiegegevens van de app handmatig toe te voegen aan uw oplossing:
>
> 1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
> 1. Als u via uw account toegang tot meer dan één tenant hebt, selecteert u uw account in de rechterbovenhoek en stelt u uw portalsessie in op de gewenste Azure Active Directory-tenant.
> 1. Navigeer naar de pagina micro soft-identiteits platform voor ontwikkel aars [app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) .
> 1. Selecteer **nieuwe registratie**.
> 1. Wanneer de pagina **Een toepassing registreren** verschijnt, voert u de registratiegegevens van de toepassing in:
>      - Voer in de sectie **Naam** een beschrijvende toepassingsnaam in die zichtbaar is voor gebruikers van de app. Bijvoorbeeld: `python-webapp`.
>      - Selecteer onder **Ondersteunde accounttypen** de optie **Accounts in een organisatieadreslijst en persoonlijke Microsoft-account**.
>      - Selecteer **Registreren**.
>      - Noteer de waarde van de **toepassing (client)** op de pagina app- **overzicht** voor later gebruik.
> 1. Selecteer de **verificatie** in het menu en voeg de volgende gegevens toe:
>    - Voeg de **Web** webplatform configuratie toe. Toevoegen `http://localhost:5000/getAToken` als **omleidings-uri's**.
>    - Selecteer **Opslaan**.
> 1. Kies in het menu links de optie **certificaten & geheimen** en klik in de sectie **client geheimen** op **Nieuw client geheim** :
>
>      - Typ een beschrijving voor de sleutel (van het app-geheim van de instantie).
>      - Selecteer een sleutel duur van **in één jaar**.
>      - Wanneer u op **toevoegen**klikt, wordt de waarde van de sleutel weer gegeven.
>      - Kopieer de waarde van de sleutel. U hebt dit later nodig.
> 1. De sectie **API-machtigingen** selecteren
>
>      - Klik op de knop **een machtiging toevoegen** en vervolgens op
>      - Zorg ervoor dat het tabblad **micro soft-api's** is geselecteerd
>      - Klik in de sectie *veelgebruikte micro soft-api's* op **Microsoft Graph**
>      - Controleer in de sectie **gedelegeerde machtigingen** of de juiste machtigingen zijn ingeschakeld: **User. ReadBasic. all**. Gebruik het zoekvak als dat nodig is.
>      - Selecteer de knop **machtigingen toevoegen**
>
> [!div class="sxs-lookup" renderon="portal"]
>
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Stap 1: Uw toepassing configureren in Azure Portal
>
> Het code voorbeeld voor deze Quick Start werkt alleen als u:
>
> 1. Voeg een antwoord-URL `http://localhost:5000/getAToken`toe als.
> 1. Maak een client geheim.
> 1. Voeg de Microsoft Graph-API user. ReadBasic. alle gedelegeerde machtigingen toe.
>
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Breng deze wijzigingen voor mij aan]()
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Al geconfigureerd](media/quickstart-v2-aspnet-webapp/green-check.png) Uw toepassing is al geconfigureerd met dit kenmerk

#### <a name="step-2-download-your-project"></a>Stap 2: Uw project downloaden
> [!div renderon="docs"]
> [Het code voorbeeld downloaden](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Down load het project en pak het zip-bestand uit naar een lokale map dichter bij de hoofdmap, bijvoorbeeld **C:\Azure-samples**
> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [Het code voorbeeld downloaden](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-application"></a>Stap 3: de toepassing configureren
>
> 1. Pak het zip-bestand uit in een lokale map dichter bij de hoofdmap (bijvoorbeeld **C:\Azure-Samples**)
> 1. Als u een Integrated Development Environment gebruikt, opent u het voor beeld in uw favoriete IDE (optioneel).
> 1. Open het bestand **app_config. py** , dat u kunt vinden in de hoofdmap en vervang door het volgende code fragment:
>
> ```python
> CLIENT_ID = "Enter_the_Application_Id_here"
> CLIENT_SECRET = "Enter_the_Client_Secret_Here"
> AUTHORITY = "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here"
> ```
> Waar:
>
> - `Enter_the_Application_Id_here`: de toepassings-id voor de toepassing die u hebt geregistreerd.
> - `Enter_the_Client_Secret_Here`-is het **client geheim** dat u in **certificaten & geheimen** hebt gemaakt voor de toepassing die u hebt geregistreerd.
> - `Enter_the_Tenant_Name_Here`-is de **ID-waarde van de directory (Tenant)** van de toepassing die u hebt geregistreerd.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-run-the-code-sample"></a>Stap 3: het code voorbeeld uitvoeren

> [!div renderon="docs"]
> #### <a name="step-4-run-the-code-sample"></a>Stap 4: het code voorbeeld uitvoeren

1. U moet de MSAL python-bibliotheek installeren, kolf Framework, kolf-sessies voor sessie beheer aan de server zijde en aanvragen met behulp van PIP als volgt:

    ```Shell
    pip install -r requirements.txt
    ```

2. Voer app.py uit vanuit de shell of de opdracht regel:

    ```Shell
    python app.py
    ```
   > [!IMPORTANT]
   > Deze quickstarttoepassing gebruikt een clientgeheim om zichzelf te identificeren als vertrouwelijke client. Omdat het clientgeheim als platte tekst aan uw projectbestanden wordt toegevoegd, wordt u om veiligheidsredenen aangeraden een certificaat te gebruiken in plaats van een clientgeheim voordat u de toepassing als productietoepassing beschouwt. Zie [deze instructies](https://docs.microsoft.com/azure/active-directory/develop/active-directory-certificate-credentials)voor meer informatie over het gebruik van een certificaat.

## <a name="more-information"></a>Meer informatie

### <a name="how-the-sample-works"></a>Hoe het voor beeld werkt
![Toont hoe de voor beeld-app die door deze Quick start is gegenereerd, werkt](media/quickstart-v2-python-webapp/python-quickstart.svg)

### <a name="getting-msal"></a>MSAL ophalen
MSAL is de bibliotheek die wordt gebruikt voor het aanmelden van gebruikers en het aanvragen van tokens die worden gebruikt voor toegang tot een API die wordt beveiligd door het micro soft Identity-platform.
U kunt MSAL python toevoegen aan uw toepassing met behulp van PIP.

```Shell
pip install msal
```

### <a name="msal-initialization"></a>MSAL initialiseren
U kunt de verwijzing toevoegen aan MSAL python door de volgende code toe te voegen aan de bovenkant van het bestand waarin u MSAL gaat gebruiken:

```Python
import msal
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over web-apps waarmee gebruikers zich kunnen aanmelden en die web-Api's aanroept:

> [!div class="nextstepaction"]
> [Scenario: Web-apps waarmee gebruikers zich aanmelden](scenario-web-app-sign-user-overview.md)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
