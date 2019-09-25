---
title: Micro soft Identity platform python web app Quick Start | Azure
description: Meer informatie over het implementeren van micro soft-aanmelding op een Python-web-app met OAuth2
services: active-directory
documentationcenter: dev-center-name
author: abhidnya13
editor: ''
ms.assetid: 9551f0b5-04f2-44d7-87b5-756409180fe9
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/11/2019
ms.author: abpati
ms.custom: aaddev
ms.openlocfilehash: c5817427102bf10dcd1ece932b0f582d973efaf7
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71257888"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-python-web-app"></a>Quickstart: Aanmelden met micro soft toevoegen aan een Python-web-app

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

In deze Quick Start leert u hoe u een python-webtoepassing integreert met het micro soft Identity-platform. Uw app meldt zich aan bij een gebruiker, haalt een toegangs token op om de Microsoft Graph-API aan te roepen en brengt een aanvraag naar de Microsoft Graph-API.

Wanneer u de hand leiding hebt voltooid, accepteert uw toepassing aanmeldingen van persoonlijke micro soft-accounts (waaronder outlook.com, live.com en anderen) en werk-of school accounts van elk bedrijf of organisatie die gebruikmaakt van Azure Active Directory.

![Toont hoe de voor beeld-app die door deze Quick start is gegenereerd, werkt](media/quickstart-v2-python-webapp/python-quickstart.svg)

## <a name="prerequisites"></a>Vereisten

Als u dit voor beeld wilt uitvoeren, hebt u het volgende nodig:

- [Python 2.7 +](https://www.python.org/downloads/release/python-2713) of [python 3 +](https://www.python.org/downloads/release/python-364/)
- [Kolf](http://flask.pocoo.org/), [kolf-sessie](https://pythonhosted.org/Flask-Session/), [aanvragen](https://2.python-requests.org/en/master/)
- [MSAL python](https://github.com/AzureAD/microsoft-authentication-library-for-python) 
- Een Azure Active Directory-Tenant (Azure AD). Zie [een Azure AD-Tenant verkrijgen](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) voor meer informatie over het verkrijgen van een Azure AD-Tenant.

> [!div renderon="docs"]
>
> ## <a name="register-and-download-your-quickstart-app"></a>De quickstart-app registreren en downloaden
>
> U hebt twee opties om uw Quick Start-toepassing te starten: Express (optie 1) en hand matig (optie 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Optie 1: registreer de toepassing en laat deze automatisch configureren. Download vervolgens het codevoorbeeld
>
> 1. Ga naar de [Azure Portal-app-registraties](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps).
> 1. Selecteer **nieuwe registratie**.
> 1. Voer een naam in voor de toepassing en selecteer **Registreren**.
> 1. Volg de instructies om uw nieuwe toepassing te downloaden en automatisch te configureren.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Optie 2: registreer de toepassing en configureer handmatig de toepassing en het codevoorbeeld
>
> #### <a name="step-1-register-your-application"></a>Stap 1: Uw toepassing registreren
>
> Volg deze stappen om de toepassing te registreren en de registratiegegevens van de app handmatig toe te voegen aan uw oplossing:
>
> 1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
> 1. Als u via uw account toegang hebt tot meer dan één tenant, selecteert u uw account in de rechterbovenhoek en stelt u de portalsessie in op de gewenste Azure Active Directory-tenant.
> 1. Navigeer naar de pagina micro soft-identiteits platform voor ontwikkel aars [app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) .
> 1. Selecteer **nieuwe registratie**.
> 1. Wanneer de pagina **Een toepassing registreren** verschijnt, voert u de registratiegegevens van de toepassing in:
>      - Voer in de sectie **Naam** een beschrijvende toepassingsnaam. Deze wordt zichtbaar voor gebruikers van de app. Bijvoorbeeld: `python-webapp`.
>      - Selecteer onder **Ondersteunde accounttypen** de optie **Accounts in een organisatieadreslijst en persoonlijke Microsoft-account**.
>      - Selecteer onder de sectie omleidings- **URI** in de vervolg keuzelijst het **webplatform en** stel vervolgens de waarde in op `http://localhost:5000/getAToken`.
>      - Selecteer **Registreren**. Noteer de waarde van de **toepassing (client)** op de pagina app- **overzicht** voor later gebruik.
> 1. Kies in het menu links de optie **certificaten & geheimen** en klik in de sectie **client geheimen** op **Nieuw client geheim** :
>
>      - Typ een beschrijving voor de sleutel (van het app-geheim van de instantie).
>      - Selecteer een sleutel duur van **in één jaar**.
>      - Wanneer u op **toevoegen**klikt, wordt de waarde van de sleutel weer gegeven.
>      - Kopieer de waarde van de sleutel. U hebt dit later nodig.
>
> [!div class="sxs-lookup" renderon="portal"]
>
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Stap 1: uw toepassing configureren in Azure Portal
>
> Het code voorbeeld voor deze Quick Start werkt alleen als u:
>
> 1. Voeg een antwoord-URL `http://localhost:5000/getAToken`toe als.
> 1. Maak een client geheim.
>
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Deze wijziging voor mij maken]()
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Al geconfigureerd](media/quickstart-v2-aspnet-webapp/green-check.png) Uw toepassing is al geconfigureerd met dit kenmerk

#### <a name="step-2-download-your-project"></a>Stap 2: uw project downloaden

[Het code voorbeeld downloaden](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)

#### <a name="step-3-configure-the-application"></a>Stap 3: De toepassing configureren

1. Pak het zip-bestand uit in een lokale map dichter bij de hoofdmap (bijvoorbeeld **C:\Azure-Samples**)
1. Als u een Integrated Development Environment gebruikt, opent u het voor beeld in uw favoriete IDE (optioneel).
1. Open het bestand **app_config. py** , dat u kunt vinden in de hoofdmap en vervang door het volgende code fragment:

```python
AUTHORITY = "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here"
CLIENT_ID = "Enter_the_Application_Id_here"
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
SCOPE = ["https://graph.microsoft.com/User.Read"]
REDIRECT_URI = "http://localhost:5000/getAToken"
```

> [!div renderon="docs"]
> Waar:
>
> - `Enter_the_Application_Id_here`: de toepassings-id voor de toepassing die u hebt geregistreerd.
> - `Enter_the_Tenant_Info_Here`: is een van de onderstaande opties:
>   - Als uw toepassing **alleen mijn organisatie**ondersteunt, vervangt u deze waarde door de **Tenant-id** of **Tenant naam** (bijvoorbeeld contoso.onmicrosoft.com)
>   - Als uw toepassing **Accounts in elke organisatiemap** ondersteunt, vervang deze waarde dan door `organizations`
>   - Als uw toepassing **Alle Microsoft-accountgebruikers** ondersteunt, vervang deze waarde dan door `common`
> - `Enter_the_Client_Secret_Here`-is het **client geheim** dat u in **Certificaten & geheimen** hebt gemaakt voor de toepassing die u hebt geregistreerd.

#### <a name="step-4-run-the-code-sample"></a>Stap 4: Het code voorbeeld uitvoeren

- U moet de MSAL python-bibliotheek installeren, kolf Framework, kolf-sessies voor sessie beheer aan de server zijde en aanvragen met behulp van PIP als volgt:

```Shell
pip install msal
pip install flask
pip install Flask-Session
pip install requests
```

- Als de omgevings variabele voor de fles al is ingesteld: Voer app.py uit vanuit de shell of de opdracht regel:

```Shell
python app.py
```

- Als de omgevings variabele voor de fles niet is ingesteld:

    1. Typ de volgende opdrachten op de shell of de opdracht regel door te navigeren naar de projectmap:

```Shell
export FLASK_APP=app.py
export FLASK_DEBUG=1
flask run
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
