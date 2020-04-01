---
title: 'Zelfstudie: Gebruikers verifiëren in een native clienttoepassing'
titleSuffix: Azure AD B2C
description: Zelfstudie over het gebruik van Azure Active Directory B2C voor het opgeven van gebruikersaanmeldingsreferenties voor een .NETdesktop-app.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/12/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 06d27c3a3daa4702653a2063d0ac70fd094e2d74
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78186196"
---
# <a name="tutorial-authenticate-users-in-a-native-desktop-client-using-azure-active-directory-b2c"></a>Zelfstudie: Gebruikers in een native bureaubladclient verifiëren met Azure Active Directory B2C

In deze zelfstudie ziet u hoe u Azure Active Directory B2C (Azure AD B2C) gebruiken om gebruikers aan te melden en aan te melden in een WPF-bureaubladtoepassing (Windows Presentation Foundation). Met Azure AD B2C kunnen uw toepassingen zich met behulp van open-standaardprotocollen verifiëren bij sociale accounts, Enterprise-accounts en Azure Active Directory-accounts.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De systeemeigen clienttoepassing toevoegen
> * Het voorbeeld configureren voor gebruik van de toepassing
> * Aanmelden met behulp van de gebruikersstroom

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

- [Maak gebruikersstromen](tutorial-create-user-flows.md) om gebruikerservaringen in uw toepassing in te schakelen.
- Installeer [Visual Studio 2019](https://www.visualstudio.com/downloads/) met **.NET-desktopontwikkeling** en **ASP.NET en webdevelopment** workloads.

## <a name="add-the-native-client-application"></a>De systeemeigen clienttoepassing toevoegen

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

Neem de **toepassings-id (client) op** voor gebruik in een latere stap.

## <a name="configure-the-sample"></a>Configureren van het voorbeeld

In deze zelfstudie configureert u een voorbeeld dat u kunt downloaden uit GitHub. De voorbeeld-WPF-bureaubladtoepassing toont aanmelding, aanmelding en kan een beveiligde web-API aanroepen in Azure AD B2C. [Download een ZIP-bestand ](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/msalv3.zip), [blader door de opslagplaats](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) of kloon de voorbeeldweb-app vanuit GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
```

Ga als u de toepassing wilt bijwerken om te werken met uw Azure AD B2C-tenant en de gebruikersstromen aan te roepen in plaats van de gebruikersstromen in de standaarddemotenant:

1. Open de **active-directory-b2c-wpf-oplossing** (`active-directory-b2c-wpf.sln`) in Visual Studio.
2. Open in het **active-directory-b2c-wpf-project** het *App.xaml.cs-bestand* en vind de volgende variabele definities. Vervang `{your-tenant-name}` de naam van uw Azure `{application-ID}` AD B2C-tenant en door de toepassings-id die u eerder hebt geregistreerd.

    ```csharp
    private static readonly string Tenant = "{your-tenant-name}.onmicrosoft.com";
    private static readonly string AzureAdB2CHostname = "{your-tenant-name}.b2clogin.com";
    private static readonly string ClientId = "{application-ID}";
    ```

3. Werk de variabelen van de beleidsnaam bij met de namen van de gebruikersstromen die u hebt gemaakt als onderdeel van de vereisten. Bijvoorbeeld:

    ```csharp
    public static string PolicySignUpSignIn = "B2C_1_signupsignin1";
    public static string PolicyEditProfile = "B2C_1_profileediting1";
    public static string PolicyResetPassword = "B2C_1_passwordreset1";
    ```

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

Druk op **F5** om het voorbeeld te bouwen en uit te voeren.

### <a name="sign-up-using-an-email-address"></a>Aanmelden met een e-mailadres

1. Selecteer **Aanmelden** om u als gebruiker aan te melden. Hiervoor wordt de gebruikersstroom **B2C_1_signupsignin1** gebruikt.
2. Azure AD B2C presenteert een aanmeldingspagina met een koppeling **Nu aanmelden.** Omdat je nog geen account hebt, selecteer je de koppeling **Nu aanmelden.**
3. Tijdens de aanmeldingswerkstroom wordt een pagina weergegeven waarmee de identiteit wordt opgehaald en gecontroleerd van de gebruiker die een e-mailadres heeft gebruikt. Tijdens de aanmeldingswerkstroom worden ook het wachtwoord van de gebruiker en de aangevraagde kenmerken opgehaald die in de gebruikersstroom zijn gedefinieerd.

    Gebruik een geldig e-mailadres en voer de verificatie uit met de verificatiecode. Stel een wachtwoord in. Geef waarden voor de aangevraagde kenmerken op.

    ![Aanmeldingspagina weergegeven als onderdeel van de aanmeldings-/aanmeldingswerkstroom](./media/tutorial-desktop-app/azure-ad-b2c-sign-up-workflow.png)

4. Selecteer **Maken** om een lokaal account te maken in de Azure AD B2C-tenant.

De gebruiker kan nu zijn e-mailadres gebruiken om in te loggen en de bureaubladtoepassing te gebruiken. Na een succesvolle aanmelding of aanmelding worden de tokengegevens weergegeven in het onderste deelvenster van de WPF-app.

![Tokendetails die worden weergegeven in het onderste deelvenster van wpf-bureaubladtoepassing](./media/tutorial-desktop-app/desktop-app-01-post-signin.png)

Als u de **knop API aanroepen** selecteert, wordt een **foutbericht** weergegeven. U komt de fout tegen omdat de toepassing in de huidige staat probeert `fabrikamb2c.onmicrosoft.com`toegang te krijgen tot een API die is beschermd door de demotenant. Aangezien uw toegangstoken alleen geldig is voor uw Azure AD B2C-tenant, is de API-aanroep daarom ongeautoriseerd.

Ga verder naar de volgende zelfstudie om een beveiligde web-API in uw eigen tenant te registreren en de **Call API-functionaliteit** in te schakelen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * De systeemeigen clienttoepassing toevoegen
> * Het voorbeeld configureren voor gebruik van de toepassing
> * Aanmelden met behulp van de gebruikersstroom

Als u vervolgens de functie van de **Call API-knop** wilt inschakelen, verleent u de WPF-bureaubladtoepassing toegang tot een web-API die is geregistreerd in uw eigen Azure AD B2C-tenant:

> [!div class="nextstepaction"]
> [Zelfstudie: Geef toegang tot een Node.js-web-API vanuit een bureaublad-app >](tutorial-desktop-app-webapi.md)
