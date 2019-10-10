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
ms.date: 09/25/2019
ms.author: abpati
ms.custom: aaddev
ms.openlocfilehash: 85a1de992be7b5bbdcec8fd415f60ae10190c11a
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170046"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-python-web-app"></a>Snelstartgids: aanmelden toevoegen met micro soft aan een Python-web-app

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

In deze Quick Start leert u hoe u een python-webtoepassing integreert met het micro soft Identity-platform. Uw app meldt zich aan bij een gebruiker, haalt een toegangs token op om de Microsoft Graph-API aan te roepen en brengt een aanvraag naar de Microsoft Graph-API.

Wanneer u de hand leiding hebt voltooid, accepteert uw toepassing aanmeldingen van persoonlijke micro soft-accounts (waaronder outlook.com, live.com en anderen) en werk-of school accounts van elk bedrijf of organisatie die gebruikmaakt van Azure Active Directory.

![Toont hoe de voor beeld-app die door deze Quick start is gegenereerd, werkt](media/quickstart-v2-python-webapp/python-quickstart.svg)

## <a name="prerequisites"></a>Vereisten

Als u dit voor beeld wilt uitvoeren, hebt u het volgende nodig:

- [Python 2.7 +](https://www.python.org/downloads/release/python-2713) of [python 3 +](https://www.python.org/downloads/release/python-364/)
- [Kolf](http://flask.pocoo.org/), [kolf-sessie](https://pythonhosted.org/Flask-Session/), [aanvragen](https://requests.kennethreitz.org//en/master/)
- [MSAL python](https://github.com/AzureAD/microsoft-authentication-library-for-python) 
- Een Azure Active Directory-Tenant (Azure AD). Zie [een Azure AD-Tenant verkrijgen](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) voor meer informatie over het verkrijgen van een Azure AD-Tenant.

> [!div renderon="docs"]
>
> ## <a name="register-and-download-your-quickstart-app"></a>De quickstart-app registreren en downloaden
>
> U hebt twee opties om uw Quick Start-toepassing te starten: Express (optie 1) en hand matig (optie 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Optie 1: de app registreren en automatisch configureren, en vervolgens de voorbeeldcode downloaden
>
> 1. Ga naar de [Azure Portal-app-registraties](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps).
> 1. Selecteer **nieuwe registratie**.
> 1. Voer een naam in voor de toepassing en selecteer **Registreren**.
> 1. Volg de instructies om uw nieuwe toepassing te downloaden en automatisch te configureren.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Optie 2: de toepassing en voorbeeldcode registreren en handmatig configureren
>
> #### <a name="step-1-register-your-application"></a>Stap 1: Uw toepassing registreren
>
> Volg deze stappen om de toepassing te registreren en de registratiegegevens van de app handmatig toe te voegen aan uw oplossing:
>
> 1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
> 1. Als u via uw account toegang tot meer dan één tenant hebt, selecteert u uw account in de rechterbovenhoek en stelt u uw portalsessie in op de gewenste Azure Active Directory-tenant.
> 1. Navigeer naar de pagina micro soft-identiteits platform voor ontwikkel aars [app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) .
> 1. Selecteer **nieuwe registratie**.
> 1. Wanneer de pagina **Een toepassing registreren** verschijnt, voert u de registratiegegevens van de toepassing in:
>      - Voer in de sectie **Naam** een beschrijvende toepassingsnaam. Deze wordt zichtbaar voor gebruikers van de app. Bijvoorbeeld: `python-webapp`.
>      - Selecteer onder **Ondersteunde accounttypen** de optie **Accounts in een organisatieadreslijst en persoonlijke Microsoft-account**.
>      - Selecteer onder de sectie **omleidings-URI** in de vervolg keuzelijst het **webplatform en** stel vervolgens de waarde in op `http://localhost:5000/getAToken`.
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
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Stap 1: Uw toepassing configureren in Azure Portal
>
> Het code voorbeeld voor deze Quick Start werkt alleen als u:
>
> 1. Voeg een antwoord-URL toe als `http://localhost:5000/getAToken`.
> 1. Maak een client geheim.
>
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Deze wijziging voor mij maken]()
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Al geconfigureerd](media/quickstart-v2-aspnet-webapp/green-check.png) Uw toepassing is al geconfigureerd met dit kenmerk

#### <a name="step-2-download-your-project"></a>Stap 2: Uw project downloaden

[Het code voorbeeld downloaden](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)

#### <a name="step-3-configure-the-application"></a>Stap 3: de toepassing configureren

1. Pak het zip-bestand uit in een lokale map dichter bij de hoofdmap (bijvoorbeeld **C:\Azure-Samples**)
1. Als u een Integrated Development Environment gebruikt, opent u het voor beeld in uw favoriete IDE (optioneel).
1. Open het bestand **app_config. py** , dat u kunt vinden in de hoofdmap en vervang door het volgende code fragment:

```python
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
CLIENT_ID = "Enter_the_Application_Id_here"
```

> [!div renderon="docs"]
> Waar:
>
> - `Enter_the_Application_Id_here`: de toepassings-id voor de toepassing die u hebt geregistreerd.
> - `Enter_the_Client_Secret_Here`-is het **client geheim** dat u hebt gemaakt in **Certificaten & geheimen** voor de toepassing die u hebt geregistreerd.

#### <a name="step-4-run-the-code-sample"></a>Stap 4: het code voorbeeld uitvoeren

1. U moet de MSAL python-bibliotheek installeren, kolf Framework, kolf-sessies voor sessie beheer aan de server zijde en aanvragen met behulp van PIP als volgt:

   ```Shell
   pip install -r requirements.txt
   ```

2. Voer app.py uit vanuit de shell of de opdracht regel:

   ```Shell
   python app.py
   ```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over web-apps waarmee gebruikers zich kunnen aanmelden en die web-Api's aanroept:

> [!div class="nextstepaction"]
> [Scenario: Web-apps waarmee gebruikers zich aanmelden](scenario-web-app-sign-user-overview.md)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
