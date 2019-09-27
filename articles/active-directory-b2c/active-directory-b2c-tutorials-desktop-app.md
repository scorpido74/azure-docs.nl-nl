---
title: 'Zelfstudie: Verificatie inschakelen in een systeemeigen clienttoepassing - Azure Active Directory B2C | Microsoft Docs'
description: Zelfstudie over het gebruik van Azure Active Directory B2C voor het opgeven van gebruikersaanmeldingsreferenties voor een .NETdesktop-app.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 3740a032db6ca9fd0fb88ce348610684d9f895bc
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326334"
---
# <a name="tutorial-enable-authentication-in-a-native-client-application-using-azure-active-directory-b2c"></a>Zelfstudie: Verificatie inschakelen in een systeemeigen clienttoepassing met behulp van Azure Active Directory B2C

In deze zelf studie wordt uitgelegd hoe u Azure Active Directory B2C (Azure AD B2C) gebruikt om u aan te melden en gebruikers te registreren in een Windows Presentation Foundation (WPF)-bureaublad toepassing. Met Azure AD B2C kunnen uw toepassingen zich met behulp van open-standaardprotocollen verifiëren bij sociale accounts, Enterprise-accounts en Azure Active Directory-accounts.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De systeemeigen clienttoepassing toevoegen
> * Het voorbeeld configureren voor gebruik van de toepassing
> * Aanmelden met behulp van de gebruikersstroom

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

- [Maak gebruikersstromen](tutorial-create-user-flows.md) om gebruikerservaringen in uw toepassing in te schakelen.
- Installeer [Visual Studio 2019](https://www.visualstudio.com/downloads/) met **.net desktop Development** en **ASP.net-en Web Development** -workloads.

## <a name="add-the-native-client-application"></a>De systeemeigen clienttoepassing toevoegen

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

Noteer de **toepassings-id** voor gebruik in een latere stap.

## <a name="configure-the-sample"></a>Voorbeeld configureren

In deze zelfstudie configureert u een voorbeeld dat u kunt downloaden uit GitHub. Het voorbeeld van een WPF-bureaubladtoepassing laat registratie en aanmelding zien, en roept een beveiligde web-API in Azure AD B2C op. [Download een ZIP-bestand ](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/master.zip), [blader door de opslagplaats](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) of kloon de voorbeeldweb-app vanuit GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
```

Als u de app-instellingen wilt wijzigen, vervangt u de `<your-tenant-name>` door uw tenantnaam en vervangt u `<application-ID`> door de toepassings-id die u hebt genoteerd.

1. Open de oplossing `active-directory-b2c-wpf` in Visual Studio.
2. Open het bestand **App.xaml.cs** in het project `active-directory-b2c-wpf`, en voer de volgende updates uit:

    ```csharp
    private static string Tenant = "<your-tenant-name>.onmicrosoft.com";
    private static string ClientId = "<application-ID>";
    ```

3. Werk de variabele **PolicySignUpSignIn** bij met de naam van de gebruikersstroom die u hebt gemaakt.

    ```csharp
    public static string PolicySignUpSignIn = "B2C_1_signupsignin1";
    ```

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

Druk op **F5** om het voorbeeld te bouwen en uit te voeren.

### <a name="sign-up-using-an-email-address"></a>Aanmelden met een e-mailadres

1. Klik op **Aanmelden** om u als een gebruiker aan te melden. Hiervoor wordt de gebruikersstroom **B2C_1_signupsignin1** gebruikt.
2. Azure AD B2C toont een aanmeldingspagina met een koppeling voor registratie. Aangezien u nog geen account hebt, klikt u op de koppeling **Nu registreren**.
3. Tijdens de aanmeldingswerkstroom wordt een pagina weergegeven waarmee de identiteit wordt opgehaald en gecontroleerd van de gebruiker die een e-mailadres heeft gebruikt. Tijdens de aanmeldingswerkstroom worden ook het wachtwoord van de gebruiker en de aangevraagde kenmerken opgehaald die in de gebruikersstroom zijn gedefinieerd.

    Gebruik een geldig e-mailadres en voer de verificatie uit met de verificatiecode. Stel een wachtwoord in. Geef waarden voor de aangevraagde kenmerken op.

    ![De registratie pagina die wordt weer gegeven als onderdeel van de werk stroom voor aanmelden/registreren](media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.PNG)

4. Klik op **Maken** als u een lokaal account wilt maken in de Azure AD B2C-tenant.

Gebruikers kunnen nu hun e-mailadres gebruiken om zich aan te melden en de desktop-app te gebruiken.

> [!NOTE]
> Als u op de knop **API aanroepen** klinkt, ontvangt u de foutmelding 'Onbevoegd'. U ontvangt deze foutmelding omdat u toegang probeert te krijgen tot een resource van de demo-tenant. Omdat uw toegangstoken alleen geldig is voor uw Azure AD-tenant, is de API-aanroep niet geautoriseerd. Ga door met de volgende zelfstudie voor het maken van een beveiligde web-API voor uw tenant.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * De systeemeigen clienttoepassing toevoegen
> * Het voorbeeld configureren voor gebruik van de toepassing
> * Aanmelden met behulp van de gebruikersstroom

> [!div class="nextstepaction"]
> [Zelfstudie: Toegang verlenen aan een web-API van Node.js vanuit een desktop-app met behulp van Azure Active Directory B2C](active-directory-b2c-tutorials-spa-webapi.md)
