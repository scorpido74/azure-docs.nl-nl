---
title: 'Zelfstudie: Verificatie inschakelen in een app van één pagina'
titleSuffix: Azure AD B2C
description: In deze zelfstudie leert u hoe u Azure Active Directory B2C gebruikt om gebruikers aan te geven voor een op JavaScript gebaseerde toepassing (SPA).
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/14/2019
ms.custom: mvc, seo-javascript-september2019
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 435800d9c6bfd9131d50681a9808f9836104fac0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78183334"
---
# <a name="tutorial-enable-authentication-in-a-single-page-application-using-azure-active-directory-b2c-azure-ad-b2c"></a>Zelfstudie: Verificatie inschakelen in één pagina met Azure Active Directory B2C (Azure AD B2C)

In deze zelfstudie ziet u hoe u Azure Active Directory B2C (Azure AD B2C) gebruiken om gebruikers aan te melden en aan te melden in een enkele pagina-toepassing (SPA). Met Azure AD B2C kunnen uw toepassingen zich met behulp van open-standaardprotocollen verifiëren bij sociale accounts, Enterprise-accounts en Azure Active Directory-accounts.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Bijwerken van de toepassing in Azure AD B2C
> * Het voorbeeld configureren voor gebruik van de toepassing
> * Aanmelden met behulp van de gebruikersstroom

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

U hebt de volgende Azure AD B2C-resources nodig voordat u verdergaat met de stappen in deze zelfstudie:

* [Azure AD B2C-tenant](tutorial-create-tenant.md)
* [Aanvraag geregistreerd](tutorial-register-applications.md) in uw tenant
* [Gebruikersstromen die](tutorial-create-user-flows.md) in uw tenant zijn gemaakt

Daarnaast hebt u het volgende nodig in uw lokale ontwikkelingsomgeving:

* Codeeditor, bijvoorbeeld [Visual Studio Code](https://code.visualstudio.com/) of Visual Studio [2019](https://www.visualstudio.com/downloads/)
* [.NET Core SDK 2.2](https://dotnet.microsoft.com/download) of hoger
* [Node.js](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>De toepassing bijwerken

In de tweede zelfstudie die u hebt voltooid als onderdeel van de vereisten, hebt u een webtoepassing geregistreerd in Azure AD B2C. Om te communiceren met het voorbeeld in deze zelfstudie, moet u een omleidings-URI toevoegen aan de toepassing in Azure AD B2C.

U de huidige **ervaring met toepassingen** of onze nieuwe uniforme **app-registratie (Preview)** gebruiken om de toepassing bij te werken. [Meer informatie over de nieuwe ervaring](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Toepassingen](#tab/applications/)

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Zorg ervoor dat u de map met uw Azure AD B2C-tenant gebruikt door het **filter Directory + abonnement** in het bovenste menu te selecteren en de map te kiezen die uw tenant bevat.
1. Selecteer **Alle services** in de linkerbovenhoek van de Azure-portal en zoek en selecteer Azure AD **B2C**.
1. Selecteer **Toepassingen** en selecteer vervolgens de toepassing *webapp1*.
1. Voeg onder **Antwoord-URL**`http://localhost:6420` toe.
1. Selecteer **Opslaan**.
1. Noteer op de pagina Eigenschappen de **toepassings-id**. U gebruikt de app-id in een latere stap wanneer u de code in de webtoepassing met één pagina bijwerkt.

#### <a name="app-registrations-preview"></a>[App-registraties (voorbeeld)](#tab/app-reg-preview/)

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer het **filter Directory + abonnement** in het bovenste menu en selecteer vervolgens de map met uw Azure AD B2C-tenant.
1. Selecteer Azure AD **B2C**in het linkermenu . Selecteer Ook **Alle services** en zoek naar Azure AD **B2C**en selecteer deze .
1. Selecteer **App-registraties (Voorbeeld)**, selecteer het tabblad **Bezeten toepassingen** en selecteer vervolgens de *webapp1-toepassing.*
1. Selecteer **Verificatie**en selecteer **Vervolgens Probeer de nieuwe ervaring uit** (indien weergegeven).
1. Selecteer **onder Web**de koppeling URI **toevoegen,** voer `http://localhost:6420`enter en selecteer **Opslaan**.
1. Selecteer **Overzicht**.
1. Neem de **toepassings-id (client) op** voor gebruik in een latere stap wanneer u de code in de webtoepassing met één pagina bijwerkt.

* * *

## <a name="get-the-sample-code"></a>De voorbeeldcode halen

In deze zelfstudie configureert u een codevoorbeeld dat u downloadt van GitHub. Het voorbeeld laat zien hoe een toepassing van één pagina Azure AD B2C kan gebruiken voor aanmelding en aanmelding door gebruikers en een beveiligde web-API kan aanroepen.

[Download een ZIP-bestand ](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) of kloon de voorbeeld-web-app vanuit GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

## <a name="update-the-sample"></a>Het voorbeeld bijwerken

Nu u het voorbeeld hebt verkregen, werkt u de code bij met de naam van uw Azure AD B2C-tenant en de toepassings-id die u in een eerdere stap hebt geregistreerd.

1. Open `index.html` het bestand in de hoofdmap van de voorbeeldmap.
1. Wijzig `msalConfig` in de definitie de **clientId-waarde** met de toepassings-id die u in een eerdere stap hebt geregistreerd. Werk vervolgens de **URI-waarde van de instantie** bij met de naam van uw Azure AD B2C-tenant. Werk ook de URI bij met de naam van de aanmeldings-/aanmeldingsgebruikersstroom die u hebt gemaakt in een van de vereisten (bijvoorbeeld *B2C_1_signupsignin1).*

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "00000000-0000-0000-0000-000000000000", //This is your client ID
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi", //This is your tenant info
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    ```

    De naam van de gebruikersstroom die in deze zelfstudie wordt gebruikt, is **B2C_1_signupsignin1**. Als u een andere gebruikersnaam gebruikt, geeft u `authority` die naam op in de waarde.

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

1. Open een consolevenster en wijzig de map met het voorbeeld. Bijvoorbeeld:

    ```console
    cd active-directory-b2c-javascript-msal-singlepageapp
    ```
1. Voer de volgende opdrachten uit:

    ```
    npm install && npm update
    node server.js
    ```

    In het consolevenster wordt het poortnummer weergegeven van de lokaal draaiende Node.js-server:

    ```
    Listening on port 6420...
    ```

1. Ga `http://localhost:6420` naar in uw browser om de toepassing te bekijken.

Het voorbeeld biedt ondersteuning voor registratie, aanmelding, het bewerken van een profiel en het opnieuw instellen van een wachtwoord. In deze zelfstudie leest u hoe gebruikers zich kunnen registreren met behulp van een e-mailadres.

### <a name="sign-up-using-an-email-address"></a>Aanmelden met een e-mailadres

> [!WARNING]
> Na het aanmelden of aanmelden ziet u mogelijk een [fout met onvoldoende machtigingen.](#error-insufficient-permissions) Vanwege de huidige implementatie van het codevoorbeeld wordt deze fout verwacht. Dit probleem wordt opgelost in een toekomstige versie van het codevoorbeeld, waarna deze waarschuwing wordt verwijderd.

1. Selecteer **Aanmelden** om de *B2C_1_signupsignin1* gebruikersstroom te starten die u in een eerdere stap hebt opgegeven.
1. Azure AD B2C toont een aanmeldingspagina met een koppeling voor registratie. Omdat je nog geen account hebt, selecteer je de koppeling **Nu aanmelden.**
1. Tijdens de aanmeldingswerkstroom wordt een pagina weergegeven waarmee de identiteit wordt opgehaald en gecontroleerd van de gebruiker die een e-mailadres heeft gebruikt. Tijdens de aanmeldingswerkstroom worden ook het wachtwoord van de gebruiker en de aangevraagde kenmerken opgehaald die in de gebruikersstroom zijn gedefinieerd.

    Gebruik een geldig e-mailadres en voer de verificatie uit met de verificatiecode. Stel een wachtwoord in. Geef waarden voor de aangevraagde kenmerken op.

    ![Aanmeldingspagina weergegeven door de gebruikersstroom aanmelding/aanmelding](./media/tutorial-single-page-app/azure-ad-b2c-sign-up-workflow.png)

1. Selecteer **Maken** om een lokaal account te maken in de Azure AD B2C-map.

Wanneer u **Maken**selecteert, wordt de aanmeldingspagina gesloten en wordt de aanmeldingspagina opnieuw weergegeven.

U nu uw e-mailadres en wachtwoord gebruiken om u aan te melden bij de toepassing.

### <a name="error-insufficient-permissions"></a>Fout: onvoldoende machtigingen

Nadat u zich hebt aangemeld, kan de toepassing een fout met onvoldoende machtigingen retourneren:

```Output
ServerError: AADB2C90205: This application does not have sufficient permissions against this web resource to perform the operation.
Correlation ID: ce15bbcc-0000-0000-0000-494a52e95cd7
Timestamp: 2019-07-20 22:17:27Z
```

U ontvangt deze fout omdat de webtoepassing toegang probeert te krijgen tot een web-API die wordt beschermd door de *demomap, fabrikamb2c.* Omdat uw toegangstoken alleen geldig is voor uw Azure AD-map, is de API-aanroep ongeautoriseerd.

Als u deze fout wilt oplossen, gaat u verder met de volgende zelfstudie in de reeks (zie [Volgende stappen)](#next-steps)om een beveiligde web-API voor uw directory te maken.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u het volgende geleerd:

> [!div class="checklist"]
> * Bijwerken van de toepassing in Azure AD B2C
> * Het voorbeeld configureren voor gebruik van de toepassing
> * Aanmelden met behulp van de gebruikersstroom

Ga nu verder met de volgende zelfstudie in de serie om toegang te verlenen tot een beveiligde web-API van de SPA:

> [!div class="nextstepaction"]
> [Zelfstudie: Verleen toegang tot een ASP.NET Core-web-API vanuit een SPA met Azure AD B2C->](tutorial-single-page-app-webapi.md)
