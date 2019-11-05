---
title: Zelf studie-gebruikers verifiëren in een systeem eigen client toepassing-Azure Active Directory B2C
description: Zelfstudie over het gebruik van Azure Active Directory B2C voor het opgeven van gebruikersaanmeldingsreferenties voor een .NETdesktop-app.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 10/12/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: cd0fc90988048f98be46370d2c7836d9506cc44a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73475253"
---
# <a name="tutorial-authenticate-users-in-a-native-desktop-client-using-azure-active-directory-b2c"></a>Zelf studie: gebruikers verifiëren in een systeem eigen desktop-client met behulp van Azure Active Directory B2C

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

Noteer de **id van de toepassing (client)** voor gebruik in een latere stap.

## <a name="configure-the-sample"></a>Het voorbeeld configureren

In deze zelfstudie configureert u een voorbeeld dat u kunt downloaden uit GitHub. Het voor beeld van een WPF-bureaublad toepassing demonstreert registratie, aanmelden en kan een beveiligde web-API aanroepen in Azure AD B2C. [Download een ZIP-bestand ](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/master.zip), [blader door de opslagplaats](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) of kloon de voorbeeldweb-app vanuit GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
```

Als u de toepassing wilt bijwerken voor gebruik met uw Azure AD B2C-Tenant en de gebruikers stromen wilt aanroepen in plaats van die in de standaard-demo Tenant:

1. Open de **Active Directory-B2C-WPF-** oplossing (`active-directory-b2c-wpf.sln`) in Visual Studio.
2. Open het *app.xaml.cs* -bestand in het **Active Directory-B2C-WPF-** project en zoek de volgende variabele-definities. Vervang `{your-tenant-name}` door de naam van uw Azure AD B2C Tenant en `{application-ID}` met de toepassings-ID die u eerder hebt vastgelegd.

    ```csharp
    private static readonly string Tenant = "{your-tenant-name}.onmicrosoft.com";
    private static readonly string AzureAdB2CHostname = "{your-tenant-name}.b2clogin.com";
    private static readonly string ClientId = "{application-ID}";
    ```

3. Werk de variabelen van de beleids naam bij met de namen van de gebruikers stromen die u hebt gemaakt als onderdeel van de vereisten. Bijvoorbeeld:

    ```csharp
    public static string PolicySignUpSignIn = "B2C_1_signupsignin1";
    public static string PolicyEditProfile = "B2C_1_profileediting1";
    public static string PolicyResetPassword = "B2C_1_passwordreset1";
    ```

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

Druk op **F5** om het voorbeeld te bouwen en uit te voeren.

### <a name="sign-up-using-an-email-address"></a>Aanmelden met een e-mailadres

1. Selecteer **Aanmelden** om u aan te melden als een gebruiker. Hiervoor wordt de gebruikersstroom **B2C_1_signupsignin1** gebruikt.
2. Azure AD B2C geeft een aanmeldings pagina weer met de koppeling **nu registreren** . Omdat u nog geen account hebt, selecteert u de koppeling **nu registreren** .
3. Tijdens de aanmeldingswerkstroom wordt een pagina weergegeven waarmee de identiteit wordt opgehaald en gecontroleerd van de gebruiker die een e-mailadres heeft gebruikt. Tijdens de aanmeldingswerkstroom worden ook het wachtwoord van de gebruiker en de aangevraagde kenmerken opgehaald die in de gebruikersstroom zijn gedefinieerd.

    Gebruik een geldig e-mailadres en voer de verificatie uit met de verificatiecode. Stel een wachtwoord in. Geef waarden voor de aangevraagde kenmerken op.

    ![De registratie pagina die wordt weer gegeven als onderdeel van de werk stroom voor aanmelden/registreren](media/active-directory-b2c-tutorials-desktop-app/azure-ad-b2c-sign-up-workflow.png)

4. Selecteer **maken** om een lokaal account te maken in de Azure AD B2C Tenant.

De gebruiker kan nu hun e-mail adres gebruiken om zich aan te melden en de bureaublad toepassing te gebruiken. Nadat het aanmelden of aanmelden is geslaagd, worden de token details weer gegeven in het onderste deel venster van de WPF-app.

![Token details die worden weer gegeven in het onderste deel venster van de WPF Desktop-toepassing](media/active-directory-b2c-tutorials-desktop-app/desktop-app-01-post-signin.png)

Als u de knop **API aanroepen** selecteert, wordt er een **fout bericht** weer gegeven. U ontvangt de fout omdat in de huidige status de toepassing probeert toegang te krijgen tot een API die wordt beveiligd door de demo Tenant, `fabrikamb2c.onmicrosoft.com`. Omdat uw toegangs token alleen geldig is voor uw Azure AD B2C Tenant, is de API-aanroep daarom niet toegestaan.

Ga door naar de volgende zelf studie om een beveiligde web-API in uw eigen Tenant te registreren en de API-functionaliteit voor **aanroepen** in te scha kelen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * De systeemeigen clienttoepassing toevoegen
> * Het voorbeeld configureren voor gebruik van de toepassing
> * Aanmelden met behulp van de gebruikersstroom

Als u vervolgens de API-knop functie **aanroepen** wilt inschakelen, verleent u de WPF-bureaublad toepassing toegang tot een web-API die is geregistreerd in uw eigen Azure AD B2C-Tenant:

> [!div class="nextstepaction"]
> [Zelf studie: toegang verlenen tot een node. js-Web-API vanuit een bureau blad-app >](active-directory-b2c-tutorials-desktop-app-webapi.md)
