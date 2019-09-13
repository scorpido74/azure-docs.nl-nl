---
title: 'Snelstartgids: aanmelden instellen voor een bureau blad-app met behulp van Azure Active Directory B2C'
description: Voer een voor beeld van een WPF-bureaublad toepassing die gebruikmaakt van Azure Active Directory B2C om het aanmelden van het account te bieden.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/12/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 98d312dbafd399cc8b92e2c4fb70499853c214b5
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70914261"
---
# <a name="quickstart-set-up-sign-in-for-a-desktop-app-using-azure-active-directory-b2c"></a>Quickstart: Aanmelden instellen voor een bureaublad-app met Azure Active Directory B2C

Azure Active Directory (Azure AD) B2C bevat functionaliteit voor identiteitsbeheer in de cloud ter bescherming van uw toepassing, bedrijf en klanten. Met Azure AD B2C kunnen uw toepassingen zich met behulp van open-standaardprotocollen verifiëren bij sociale en enterpriseaccounts. In deze snelstart gebruikt u een WPF-desktoptoepassing (Windows Presentation Foundation) om u aan te melden via een id-provider voor sociale netwerken en voor het aanroepen van een met Azure AD B2C beveiligde web-API.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) met de werk belasting **ASP.net en Web Development** .
- Een sociaal account van Facebook, Google of micro soft.
- [Download een ZIP-bestand ](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/master.zip) of kloon de voorbeeld-web-app vanuit GitHub.

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
    ```

## <a name="run-the-application-in-visual-studio"></a>De toepassing uitvoeren in Visual Studio

1. In de projectmap van de voorbeeldtoepassing opent u de oplossing **active-directory-b2c-wpf.sln** in Visual Studio.
2. Druk op **F5** om fouten in de toepassing op te sporen.

## <a name="sign-in-using-your-account"></a>Aanmelden met uw account

1. Klik op **Aanmelden** om de werkstroom **Registreren of aanmelden** te starten.

    ![Scherm afbeelding van de voor beeld-WPF-toepassing](media/active-directory-b2c-quickstarts-desktop-app/wpf-sample-application.png)

    Het voor beeld ondersteunt verschillende registratie opties. Deze opties zijn het gebruik van een sociale ID-provider of het maken van een lokaal account met behulp van een e-mail adres. Gebruik voor deze Quick Start een id-provider account van de sociale identiteit van Facebook, Google of micro soft.


2. Azure AD B2C geeft een aanmeldings pagina voor een fictief bedrijf met de naam Fabrikam voor de voor beeld-webtoepassing. Klik op de knop van de id-provider voor sociale netwerken die u wilt gebruiken om u aan te melden via een id-provider voor sociale netwerken.

    ![De pagina aanmelden of registreren met id-providers](media/active-directory-b2c-quickstarts-desktop-app/sign-in-or-sign-up-wpf.png)

    U kunt zich verifiëren (aanmelden) met behulp van de referenties van uw sociale account en de toepassing autoriseren om informatie uit uw sociale account te lezen. Door toegang te verlenen, kan de toepassing profielgegevens van het sociaalnetwerkaccount ophalen, zoals uw naam en plaats.

2. Voltooi het aanmeldingsproces voor de id-provider.

    De profielgegevens van uw nieuwe account worden ingevuld met informatie uit uw sociaalnetwerkaccount.

## <a name="edit-your-profile"></a>Het profiel bewerken

Azure AD B2C biedt functionaliteit waarmee gebruikers hun profielen kunnen bijwerken. In de voorbeeldweb-app wordt een Azure AD B2C-gebruikersstroom voor profielbewerking gebruikt voor de werkstroom.

1. Klik in de menubalk van de toepassing op **Profiel bewerken** om het door u gemaakte profiel te bewerken.

    ![Knop profiel bewerken gemarkeerd in WPF-voor beeld-app](media/active-directory-b2c-quickstarts-desktop-app/edit-profile-wpf.png)

2. Kies de id-provider die is gekoppeld aan het account dat u hebt gemaakt. Als u bijvoorbeeld Facebook hebt gebruikt als de ID-provider tijdens het maken van uw account, kiest u Facebook om de details van het gekoppelde profiel te wijzigen.

3. Wijzig uw **weergavenaam** of **plaats** en klik op **Doorgaan**.

    In het tekstvak *Tokengegevens* wordt een nieuw toegangstoken weergegeven. Als u de wijzigingen in uw profiel wilt controleren, kopieert u het toegangstoken en plakt u het in de tokendecoder https://jwt.ms.

## <a name="access-a-protected-api-resource"></a>Toegang tot een beveiligde API-resource

Klik op **API aanroepen** om een aanvraag te verzenden naar de beveiligde resource.

    ![Call API](media/active-directory-b2c-quickstarts-desktop-app/call-api-wpf.png)

    The application includes the Azure AD access token in the request to the protected web API resource. The web API sends back the display name contained in the access token.

U hebt uw Azure AD B2C-gebruikersaccount gebruikt voor het geautoriseerd aanroepen van een web-API die met Azure AD B2C is beveiligd.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt uw Azure AD B2C-tenant gebruiken voor andere snelstarts of zelfstudies voor Azure AD B2C. Als u deze niet meer nodig hebt, kunt u [uw Azure AD B2C-tenant verwijderen](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Volgende stappen

In deze Snelstartgids hebt u een voor beeld van een bureaublad toepassing gebruikt voor het volgende:

* Aanmelden met een aangepaste aanmeldings pagina
* Aanmelden met een sociale ID-provider
* Een Azure AD B2C-account maken
* Een web-API aanroepen die wordt beveiligd door Azure AD B2C

Aan de slag met het maken van uw eigen Azure AD B2C-tenant.

> [!div class="nextstepaction"]
> [Een Azure Active Directory B2C-tenant maken in Azure Portal](tutorial-create-tenant.md)
