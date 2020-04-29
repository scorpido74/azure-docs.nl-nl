---
title: 'Snelstartgids: aanmelden instellen voor een app met één pagina (SPA)'
titleSuffix: Azure AD B2C
description: In deze Snelstartgids voert u een voor beeld van een toepassing met één pagina uit die gebruikmaakt van Azure Active Directory B2C om de aanmelding van het account te bieden.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: quickstart
ms.date: 04/04/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 0a754873aeafe8d4e7b48d49647469874ff40f7e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80875868"
---
# <a name="quickstart-set-up-sign-in-for-a-single-page-app-using-azure-active-directory-b2c"></a>Snelstartgids: aanmelden instellen voor een app met één pagina met Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) biedt Cloud Identity Management om uw toepassing, zakelijke en klanten te beveiligen. Met Azure AD B2C zijn uw toepassingen in staat om zich met behulp van open-standaardprotocollen te verifiëren bij sociaalnetwerk- en Enterprise-accounts. In deze snelstart gebruikt u een toepassing met één pagina. Deze toepassing wordt gebruikt voor het aanmelden via een id-provider voor sociale netwerken en voor het aanroepen van een met Azure AD B2C beveiligde web-API.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

- [Visual Studio Code](https://code.visualstudio.com/)
- [Node.js](https://nodejs.org/en/download/)
- Sociaal account van Facebook, Google of micro soft
- Code voorbeeld van GitHub: [Active-Directory-B2C-java script-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)

    U kunt [het zip-archief downloaden](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) of de opslag plaats klonen:

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
    ```

## <a name="run-the-application"></a>De toepassing uitvoeren

1. Start de server door de volgende opdrachten uit te voeren vanaf de Node.js-opdrachtprompt:

    ```console
    cd active-directory-b2c-javascript-msal-singlepageapp
    npm install && npm update
    npm start
    ```

    Op de server die door *server. js* wordt gestart, wordt de poort weer gegeven waarop wordt geluisterd:

    ```console
    Listening on port 6420...
    ```

1. Blader naar de URL van de toepassing. Bijvoorbeeld `http://localhost:6420`.

    ![Voor beeld-app met één pagina die wordt weer gegeven in de browser](./media/quickstart-single-page-app/sample-app-spa.png)

## <a name="sign-in-using-your-account"></a>Aanmelden met uw account

1. Selecteer **Aanmelden** om de gebruikers traject te starten.
1. Azure AD B2C geeft een aanmeldings pagina voor een fictief bedrijf met de naam Fabrikam voor de voor beeld-webtoepassing. Als u zich wilt aanmelden met een sociale ID-provider, selecteert u de knop van de ID-provider die u wilt gebruiken.

    ![De pagina aanmelden of registreren met de knoppen van de ID-provider](./media/quickstart-single-page-app/sign-in-or-sign-up-spa.png)

    U kunt zich verifiëren (aanmelden) met behulp van de referenties van uw sociale account en de toepassing autoriseren om informatie uit uw sociale account te lezen. Door toegang te verlenen, kan de toepassing profielgegevens van het sociaalnetwerkaccount ophalen, zoals uw naam en plaats.

1. Voltooi het aanmeldingsproces voor de id-provider.

## <a name="access-a-protected-api-resource"></a>Toegang tot een beveiligde API-resource

Selecteer **call API** om uw weergave naam te retour neren van de Web-API als een JSON-object.

![Voorbeeld toepassing in browser met de reactie van de Web-API](./media/quickstart-single-page-app/call-api-spa.png)

De voorbeeldtoepassing met één pagina bevat het toegangstoken van Azure AD in de aanvraag voor de beveiligde web-API-resource.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een voor beeld van een toepassing met één pagina gebruikt voor het volgende:

- Aanmelden met een sociale ID-provider
- Een Azure AD B2C gebruikers account maken (automatisch gemaakt bij het aanmelden)
- Een web-API aanroepen die wordt beveiligd door Azure AD B2C

Aan de slag met het maken van uw eigen Azure AD B2C-tenant.

> [!div class="nextstepaction"]
> [Een Azure Active Directory B2C-tenant maken in Azure Portal](tutorial-create-tenant.md)
