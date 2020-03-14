---
title: 'Snelstartgids: apps registreren bij micro soft Identity platform | Azure'
description: In deze Quick Start leert u hoe u een toepassing kunt toevoegen en registreren met het micro soft-identiteits platform.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 03/09/2020
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: dc719064166be917d868c7b7fee6b126b4099840
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79240882"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform"></a>Snelstartgids: een toepassing registreren bij het micro soft Identity-platform

In deze Quick Start registreert u een toepassing met behulp van de **app-registraties** -ervaring in de Azure Portal. Uw app is geïntegreerd met het micro soft-identiteits platform. Ontwikkel aars van ondernemingen en software-as-a-Service (SaaS)-providers kunnen commerciële Cloud Services of line-of-business-toepassingen ontwikkelen die kunnen worden geïntegreerd met het micro soft Identity-platform. Integratie biedt beveiligde aanmelding en autorisatie voor dergelijke services.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Maak gratis een account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="register-a-new-application-using-the-azure-portal"></a>Een nieuwe toepassing registreren via de Azure Portal

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Als uw account u toegang geeft tot meer dan één Tenant, selecteert u uw account in de rechter bovenhoek. Stel uw portal-sessie in op de gewenste Azure AD-Tenant.
1. Zoek en selecteer **Azure Active Directory**. Selecteer onder **beheren**de optie **app-registraties**.
1. Selecteer **nieuwe registratie**.
1. In **een toepassing registreren**voert u een zinvolle toepassings naam in om weer te geven voor gebruikers.
1. Geef als volgt op wie de toepassing kan gebruiken:

    | Ondersteunde accounttypen | Beschrijving |
    |-------------------------|-------------|
    | **Alleen accounts in deze organisatiemap** | Selecteer deze optie als u een LOB-toepassing (Line-Of-Business) bouwt. Deze optie is niet beschikbaar als u de toepassing niet registreert in een directory.<br><br>Deze optie wordt alleen toegewezen aan Azure AD met één tenant.<br><br>Deze optie is standaard ingeschakeld, tenzij u de app buiten een directory registreert. In gevallen waarbij de app is geregistreerd buiten een map, is de standaardinstelling Azure AD met meerdere tenants en persoonlijke Microsoft-accounts. |
    | **Accounts in elke organisatiemap** | Selecteer deze optie als u alle zakelijke klanten en onderwijsinstellingen wilt bereiken.<br><br>Deze optie wordt alleen toegewezen aan Azure AD met meerdere tenants.<br><br>Als u de app als alleen Azure AD hebt geregistreerd, kunt u deze bijwerken naar Azure AD multi tenant en teruggaan naar een enkele Tenant via de **verificatie** pagina. |
    | **Accounts in elke organisatiemap en persoonlijke Microsoft-accounts** | Selecteer deze optie om de breedste groep klanten te bereiken.<br><br>Deze optie wordt toegewezen aan Azure AD met meerdere tenants en persoonlijke Microsoft-accounts.<br><br>Als u de app als Azure AD-multi tenant en persoonlijke micro soft-accounts hebt geregistreerd, kunt u deze instelling niet wijzigen in de gebruikers interface. In plaats hiervan moet u de editor voor het toepassingsmanifest gebruiken om de ondersteunde accounttypen te wijzigen. |

1. Onder **omleidings-URI (optioneel)** selecteert u het type app dat u wilt maken: **Web** of **open bare client (mobiele & bureau blad)** . Voer vervolgens de omleidings-URI of antwoord-URL in voor uw toepassing.

    * Geef voor webtoepassingen de basis-URL van de app op. `https://localhost:31544` kan bijvoorbeeld de URL zijn van een web-app die op uw lokale machine wordt uitgevoerd. Gebruikers moeten deze URL gebruiken om zich bij een webclienttoepassing aan te melden.
    * Geef voor openbare clienttoepassingen de URI op die in Azure Active Directory wordt gebruikt om tokenantwoorden te retourneren. Voer een waarde in die specifiek is voor de toepassing, zoals `myapp://auth`.

    Voor voor beelden voor webtoepassingen of systeem eigen toepassingen raadpleegt u de Quick starts in [micro soft Identity platform](https://docs.microsoft.com/azure/active-directory/develop).

1. Selecteer **Registreren** wanneer u klaar bent.

    ![Toont het scherm om een nieuwe toepassing te registreren in de Azure Portal](./media/quickstart-add-azure-ad-app-preview/new-app-registration.png)

Azure AD wijst een unieke toepassing of client ID toe aan uw app. De portal opent de **overzichts** pagina van uw toepassing. Als u mogelijkheden wilt toevoegen aan uw toepassing, kunt u andere configuratie opties selecteren, zoals een huis stijl, certificaten en geheimen, API-machtigingen en meer.

![Voor beeld van een nieuw geregistreerde app-overzichts pagina](./media/quickstart-add-azure-ad-app-preview/new-app-overview-page-expanded.png)

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel voor meer informatie over het verkrijgen van toegang tot Web-Api's.
> [!div class="nextstepaction"]
> [Snelstartgids: een client toepassing configureren voor toegang tot Web-Api's](quickstart-configure-app-access-web-apis.md)

* Zie [machtigingen en toestemming in het micro soft Identity platform-eind punt](v2-permissions-and-consent.md)voor meer informatie over de machtigingen.

* Zie [Quick Start: een toepassing configureren om Web-api's](quickstart-configure-app-expose-web-apis.md)weer te geven om Web-api's beschikbaar te maken.

* Voor het beheren van ondersteunde accounts raadpleegt u [Quick Start: de accounts wijzigen die door een toepassing worden ondersteund](quickstart-modify-supported-accounts.md).

* Als u een app wilt bouwen en functionaliteit wilt toevoegen, raadpleegt u de Quick starts in [micro soft Identity platform](https://docs.microsoft.com/azure/active-directory/develop).

* Zie [Toepassingsobjecten en service-principal-objecten](app-objects-and-service-principals.md) voor meer informatie over de twee Azure Active Directory-objecten die een geregistreerde toepassing vertegenwoordigen en de relatie ertussen.

* Zie [huisstijl richtlijnen voor toepassingen voor](howto-add-branding-in-azure-ad-apps.md)meer informatie over de huisstijl richtlijnen die u moet gebruiken bij het ontwikkelen van apps.
