---
title: 'Snelstart: Apps registreren met Microsoft-identiteitsplatform | Azure'
description: In deze quickstart leert u hoe u een toepassing toevoegt en registreert bij het Microsoft-identiteitsplatform.
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
ms.openlocfilehash: 1625b48d86eebaf5d8fcd4c100d89b83716ba459
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79408365"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform"></a>Snelstart: een toepassing registreren bij het Microsoft-identiteitsplatform

In deze quickstart registreert u een toepassing met behulp van de ervaring **app-registraties** in de Azure-portal. 

Uw app is geïntegreerd met het Microsoft-identiteitsplatform door deze te registreren bij een Azure Active Directory-tenant. Enterprise-ontwikkelaars en software-as-a-service (SaaS)-providers kunnen commerciële cloudservices of line-of-business-toepassingen ontwikkelen die kunnen worden geïntegreerd met het Identiteitsplatform van Microsoft. Integratie biedt veilige aanmelding en autorisatie voor dergelijke services.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Maak gratis een account aan.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* Een [Azure AD-tentant](quickstart-create-new-tenant.md).

## <a name="register-a-new-application-using-the-azure-portal"></a>Een nieuwe toepassing registreren via de Azure Portal

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Als uw account u toegang geeft tot meer dan één tenant, selecteert u uw account in de rechterbovenhoek. Stel uw portalsessie in op de gewenste Azure AD-tenant.
1. Zoek naar **Azure Active Directory** en selecteer deze optie. Selecteer **App-registraties** onder **Beheren**.
1. Selecteer **Nieuwe registratie**.
1. Voer **in Een toepassing registreren**een betekenisvolle toepassingsnaam in om aan gebruikers weer te geven.
1. Geef op wie de toepassing als volgt kan gebruiken:

    | Ondersteunde accounttypen | Beschrijving |
    |-------------------------|-------------|
    | **Alleen accounts in deze organisatiemap** | Selecteer deze optie als u een LOB-toepassing (Line-Of-Business) bouwt. Deze optie is niet beschikbaar als u de toepassing niet registreert in een map.<br><br>Deze optie wordt alleen toegewezen aan Azure AD met één tenant.<br><br>Deze optie is de standaardinstelling, tenzij u de app buiten een map registreert. In gevallen waarbij de app is geregistreerd buiten een map, is de standaardinstelling Azure AD met meerdere tenants en persoonlijke Microsoft-accounts. |
    | **Accounts in elke organisatiemap** | Selecteer deze optie als u alle zakelijke klanten en onderwijsinstellingen wilt bereiken.<br><br>Deze optie wordt alleen toegewezen aan Azure AD met meerdere tenants.<br><br>Als u de app slechts als azure AD-tenant hebt geregistreerd, u deze bijwerken naar Azure AD-multitenant en terug naar één tenant via de **pagina Verificatie.** |
    | **Accounts in elke organisatiemap en persoonlijke Microsoft-accounts** | Selecteer deze optie om de breedste groep klanten te bereiken.<br><br>Deze optie wordt toegewezen aan Azure AD met meerdere tenants en persoonlijke Microsoft-accounts.<br><br>Als u de app hebt geregistreerd als Azure AD-multitenant en persoonlijke Microsoft-accounts, u deze instelling niet wijzigen in de gebruikersinterface. In plaats hiervan moet u de editor voor het toepassingsmanifest gebruiken om de ondersteunde accounttypen te wijzigen. |

1. Selecteer **onder URI omleiden (optioneel)** het type app dat u bouwt: **web-** of **openbare client (mobiel & desktop)**. Voer vervolgens de omleidings-URI of de URL van het antwoord in voor uw toepassing.

    * Geef voor webtoepassingen de basis-URL van de app op. `https://localhost:31544` kan bijvoorbeeld de URL zijn van een web-app die op uw lokale machine wordt uitgevoerd. Gebruikers moeten deze URL gebruiken om zich bij een webclienttoepassing aan te melden.
    * Geef voor openbare clienttoepassingen de URI op die in Azure Active Directory wordt gebruikt om tokenantwoorden te retourneren. Voer een waarde in die specifiek is voor de toepassing, zoals `myapp://auth`.

    Zie de quickstarts in [het Microsoft-identiteitsplatform](https://docs.microsoft.com/azure/active-directory/develop)voor voorbeelden voor webtoepassingen of native toepassingen.

1. Selecteer **Registreren** wanneer u klaar bent.

    ![Toont het scherm om een nieuwe toepassing te registreren in de Azure-portal](./media/quickstart-add-azure-ad-app-preview/new-app-registration.png)

Azure AD wijst een unieke toepassing of client-id toe aan uw app. De portal opent de **overzichtspagina van** uw toepassing. Als u mogelijkheden aan uw toepassing wilt toevoegen, u andere configuratieopties selecteren, waaronder branding, certificaten en geheimen, API-machtigingen en meer.

![Voorbeeld van een nieuw geregistreerde overzichtspagina voor apps](./media/quickstart-add-azure-ad-app-preview/new-app-overview-page-expanded.png)

## <a name="next-steps"></a>Volgende stappen

* Zie [Snelstart: Een clienttoepassing configureren om toegang te krijgen tot web-API's](quickstart-configure-app-access-web-apis.md) voor toegang tot web-API's.

* Zie Machtigingen en toestemming in het eindpunt van [het Microsoft-identiteitsplatform voor](v2-permissions-and-consent.md)meer informatie over de machtigingen.

* Zie [Snelstart: Een toepassing configureren om web-API's bloot te leggen](quickstart-configure-app-expose-web-apis.md)als u web-API's wilt blootleggen.

* Zie [Snelstart: De accounts wijzigen die door een toepassing worden ondersteund](quickstart-modify-supported-accounts.md)om ondersteunde accounts te beheren.

* Zie de quickstarts in [het Microsoft-identiteitsplatform](https://docs.microsoft.com/azure/active-directory/develop)om een app te bouwen en functionaliteit toe te voegen.

* Zie [Toepassingsobjecten en service-principal-objecten](app-objects-and-service-principals.md) voor meer informatie over de twee Azure Active Directory-objecten die een geregistreerde toepassing vertegenwoordigen en de relatie ertussen.

* Zie [Merkrichtlijnen voor toepassingen voor](howto-add-branding-in-azure-ad-apps.md)meer informatie over de merkrichtlijnen die u moet gebruiken bij het ontwikkelen van apps.
