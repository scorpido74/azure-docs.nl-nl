---
title: 'Quickstart: Apps registreren bij het Microsoft Identity Platform | Azure'
description: In deze quickstart leert u hoe u een toepassing kunt registreren met het Microsoft Identity Platform.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 03/12/2020
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 65fff06b4a2d28bbc276920ccbaba90d814d03f3
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/11/2020
ms.locfileid: "88115353"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform"></a>Snelstart: Een toepassing registreren bij het Microsoft-identiteitsplatform

In deze quickstart registreert u een toepassing met behulp van de ervaring **App-registraties** in de Azure Portal. 

Uw app wordt geïntegreerd met het Microsoft Identity Platform door deze te registreren bij een Azure Active Directory-tenant. Enterprise-ontwikkelaars en SaaS-providers (software-as-a-service) kunnen commerciële cloudservices of Line-Of-Business-toepassingen ontwikkelen die kunnen worden geïntegreerd met Microsoft Identity Platform. Integratie biedt beveiligde aanmelding en autorisatie voor dergelijke services.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* Een [Microsoft Azure Active Directory-tenant](quickstart-create-new-tenant.md).

## <a name="register-a-new-application-using-the-azure-portal"></a>Een nieuwe toepassing registreren via de Azure Portal

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Als u via uw account toegang hebt tot meer dan één tenant, selecteert u uw account in de rechterbovenhoek. Stel de portalsessie in op de gewenste Azure AD-tenant.
1. Zoek en selecteer de optie **Azure Active Directory**. Selecteer **App-registraties** onder **Beheren**.
1. Selecteer **Nieuwe registratie**.
1. Voer in **Een toepassing registreren** een zinvolle toepassingsnaam in om weer te geven voor gebruikers.
1. Geef als volgt op wie de toepassing kan gebruiken:

    | Ondersteunde accounttypen | Beschrijving |
    |-------------------------|-------------|
    | **Alleen accounts in deze organisatiemap** | Selecteer deze optie als u een LOB-toepassing (Line-Of-Business) bouwt. Deze optie is niet beschikbaar als u de toepassing niet in een map registreert.<br><br>Deze optie wordt alleen toegewezen aan Azure AD met één tenant.<br><br>Deze optie is standaard tenzij u de app registreert buiten een map. In gevallen waarbij de app is geregistreerd buiten een map, is de standaardinstelling Azure AD met meerdere tenants en persoonlijke Microsoft-accounts. |
    | **Accounts in elke organisatiemap** | Selecteer deze optie als u alle zakelijke klanten en onderwijsinstellingen wilt bereiken.<br><br>Deze optie wordt alleen toegewezen aan Azure AD met meerdere tenants.<br><br>Als u de app hebt geregistreerd als een Microsoft Azure AD met één tenant, kunt u deze bijwerken naar Microsoft Azure AD met meerdere tenants en later weer teruggaan naar één tenant op de pagina **Verificatie**. |
    | **Accounts in elke organisatiemap en persoonlijke Microsoft-accounts** | Selecteer deze optie om de breedste groep klanten te bereiken.<br><br>Deze optie wordt toegewezen aan Azure AD met meerdere tenants en persoonlijke Microsoft-accounts.<br><br>Als u de app hebt geregistreerd als Azure AD met meerdere tenants en persoonlijke Microsoft-accounts, kunt u deze instelling niet wijzigen in de gebruikersinterface. In plaats hiervan moet u de editor voor het toepassingsmanifest gebruiken om de ondersteunde accounttypen te wijzigen. |

1. Selecteer onder **Omleidings-URI (optioneel)** het type app dat u wilt maken: **Web** of **Openbare client (mobiel en desktop)** . Voer vervolgens de omleidings-URI of antwoord-URL voor uw toepassing in.

    * Geef voor webtoepassingen de basis-URL van de app op. `https://localhost:31544` kan bijvoorbeeld de URL zijn van een web-app die op uw lokale machine wordt uitgevoerd. Gebruikers moeten deze URL gebruiken om zich bij een webclienttoepassing aan te melden.
    * Geef voor openbare clienttoepassingen de URI op die in Azure Active Directory wordt gebruikt om tokenantwoorden te retourneren. Voer een waarde in die specifiek is voor de toepassing, zoals `myapp://auth`.

    Voor voorbeelden voor webtoepassingen of systeemeigen toepassingen raadpleegt u de quickstarts in [Microsoft Identity Platform](./index.yml).

1. Selecteer **Registreren** wanneer u klaar bent.

    ![Geeft het scherm weer voor het registreren van een nieuwe toepassing in het Azure Portal](./media/quickstart-add-azure-ad-app-preview/new-app-registration.png)

Microsoft Azure AD wijst een unieke toepassing of client-ID toe aan uw app. De portal opent de **Overzicht**pagina van uw toepassing. Als u mogelijkheden wilt toevoegen aan de toepassing, kunt u andere configuratieopties selecteren, waaronder huisstijl, certificaten en geheimen, API-machtigingen, en meer.

![Voorbeeld van de overzichtspagina van een zojuist geregistreerde app](./media/quickstart-add-azure-ad-app-preview/new-app-overview-page-expanded.png)

## <a name="next-steps"></a>Volgende stappen

* Voor toegang tot web-API's raadpleegt u [Quickstart: Een clienttoepassing configureren voor toegang tot web-API's](quickstart-configure-app-access-web-apis.md)

* Voor meer informatie over de machtigingen raadpleegt u [Machtigingen en toestemming in het eindpunt van het Microsoft Identity Platform](v2-permissions-and-consent.md).

* Voor toegang tot web-API's raadpleegt u [Quickstart: Een toepassing configureren om web-API's beschikbaar te maken](quickstart-configure-app-expose-web-apis.md).

* Zie [Quickstart voor informatie over het beheren van ondersteunde accounts: De accounts wijzigen die worden ondersteund door een toepassing](quickstart-modify-supported-accounts.md).

* Als u een app wilt bouwen en functionaliteit wilt toevoegen, raadpleegt u de quickstarts in [Microsoft Identity Platform](./index.yml).

* Zie [Toepassingsobjecten en service-principal-objecten](app-objects-and-service-principals.md) voor meer informatie over de twee Azure Active Directory-objecten die een geregistreerde toepassing vertegenwoordigen en de relatie ertussen.

* Zie [Huisstijlrichtlijnen voor apps](howto-add-branding-in-azure-ad-apps.md) voor meer informatie over de huisstijlrichtlijnen die u moet gebruiken bij het ontwikkelen van apps.