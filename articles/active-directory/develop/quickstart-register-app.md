---
title: Een app registreren met het micro soft Identity platform-micro soft Identity platform
description: Leer hoe u een toepassing toevoegt en registreert bij het Microsoft Identity Platform.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2019
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: aragra, lenalepa, sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b0adc75d2da1a8ef053edf0078746df8f36ed5e
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72430321"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform"></a>Snelstartgids: een toepassing registreren bij het micro soft Identity-platform

Enterprise-ontwikkelaars en SaaS-providers (software-as-a-service) kunnen commerciële cloudservices of bedrijfstoepassingen ontwikkelen die kunnen worden geïntegreerd met Microsoft Identity Platform om beveiligde aanmelding en autorisatie te bieden voor hun services.

In deze Quick start ziet u hoe u een toepassing kunt toevoegen en registreren met behulp van de **app-registraties** -ervaring in de Azure Portal, zodat uw app kan worden geïntegreerd met het micro soft Identity-platform. Lees [deze blogpost](https://developer.microsoft.com/graph/blogs/new-app-registration/) voor meer informatie over de nieuwe functies en verbeteringen in de nieuwe versie voor app-registraties.

## <a name="register-a-new-application-using-the-azure-portal"></a>Een nieuwe toepassing registreren via de Azure Portal

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Als u via uw account toegang hebt tot meer dan één tenant, selecteert u uw account in de rechterbovenhoek en stelt u de portalsessie in op de door u gewenste Azure Active Directory-tenant.
1. Selecteer in het navigatie deel venster aan de linkerkant de **Azure Active Directory** -service en selecteer vervolgens **app-registraties > nieuwe registratie**.
1. Wanneer de pagina **Een toepassing registreren** verschijnt, voert u de registratiegegevens van de toepassing in:

   - **Naam**: voer een beschrijvende toepassingsnaam in. Deze wordt zichtbaar voor gebruikers van de app.
   - **Ondersteunde accounttypen**: selecteer voor welke accounts de toepassing ondersteuning moet bieden.

       | Ondersteunde accounttypen | Beschrijving |
       |-------------------------|-------------|
       | **Alleen accounts in deze organisatiemap** | Selecteer deze optie als u een LOB-toepassing (Line-Of-Business) bouwt. Deze optie is niet beschikbaar als u de toepassing niet in een map registreert.<br><br>Deze optie wordt alleen toegewezen aan Azure AD met één tenant.<br><br>Dit is de standaardoptie tenzij u de app registreert buiten een map. In gevallen waarbij de app is geregistreerd buiten een map, is de standaardinstelling Azure AD met meerdere tenants en persoonlijke Microsoft-accounts. |
       | **Accounts in elke organisatiemap** | Selecteer deze optie als u alle zakelijke klanten en onderwijsinstellingen wilt bereiken.<br><br>Deze optie wordt alleen toegewezen aan Azure AD met meerdere tenants.<br><br>Als u de app hebt geregistreerd als een Azure AD met één tenant, kunt u deze bijwerken naar Azure AD met meerdere tenants en later weer teruggaan naar één tenant op de blade **Verificatie**. |
       | **Accounts in elke organisatiemap en persoonlijke Microsoft-accounts** | Selecteer deze optie om de breedste groep klanten te bereiken.<br><br>Deze optie wordt toegewezen aan Azure AD met meerdere tenants en persoonlijke Microsoft-accounts.<br><br>Als u de app hebt geregistreerd als Azure AD met meerdere tenants en persoonlijke Microsoft-accounts, kunt u dit niet wijzigen in de gebruikersinterface. In plaats hiervan moet u de editor voor het toepassingsmanifest gebruiken om de ondersteunde accounttypen te wijzigen. |

   - **Omleidings-URI**: selecteer het type app dat u bouwt - **Web** of **Openbare client (mobiel en desktop)** - en voer vervolgens de omleidings-URI (of antwoord-URL) voor de toepassing in.
       - Geef voor webtoepassingen de basis-URL van de app op. `https://localhost:31544` kan bijvoorbeeld de URL zijn van een web-app die op uw lokale machine wordt uitgevoerd. Gebruikers moeten deze URL gebruiken om zich bij een webclienttoepassing aan te melden.
       - Geef voor openbare clienttoepassingen de URI op die in Azure Active Directory wordt gebruikt om tokenantwoorden te retourneren. Voer een waarde in die specifiek is voor de toepassing, zoals `myapp://auth`.

     Raadpleeg onze [snelstarts](https://docs.microsoft.com/azure/active-directory/develop) om specifieke voorbeelden te zien van webtoepassingen of systeemeigen toepassingen.

1. Selecteer **Registreren** wanneer u klaar bent.

    [![Shows het scherm om een nieuwe toepassing in de Azure Portal te registreren](./media/quickstart-add-azure-ad-app-preview/new-app-registration-expanded.png)](./media/quickstart-add-azure-ad-app-preview/new-app-registration-expanded.png#lightbox)

In Azure AD wordt een unieke toepassings-id (client) toegewezen aan de app, waarna u naar de pagina **Overzicht** van de toepassing wordt geleid. Als u aanvullende mogelijkheden wilt toevoegen aan de toepassing, kunt u andere configuratieopties selecteren, waaronder huisstijl, certificaten en geheimen, API-machtigingen, en meer.

[![Example van een nieuw geregistreerde app-overzichts pagina](./media/quickstart-add-azure-ad-app-preview/new-app-overview-page-expanded.png)](./media/quickstart-add-azure-ad-app-preview/new-app-overview-page-expanded.png#lightbox)

## <a name="next-steps"></a>Volgende stappen

- Krijg meer informatie over de [machtigingen en toestemming](v2-permissions-and-consent.md).
- Raadpleeg deze snelstarts om extra configuratiefuncties in te schakelen in de registratie van de toepassing, zoals referenties en machtigingen, en aanmelden in te schakelen voor gebruikers van andere tenants:
    - [Een clienttoepassing configureren voor toegang tot web-API's](quickstart-configure-app-access-web-apis.md)
    - [Een toepassing configureren voor het beschikbaar maken van web-API's](quickstart-configure-app-expose-web-apis.md)
    - [De accounts wijzigen die worden ondersteund door een toepassing](quickstart-modify-supported-accounts.md)
- Kies een [snelstart](https://docs.microsoft.com/azure/active-directory/develop) om snel een app te bouwen en functionaliteit toe te voegen, zoals tokens ophalen, tokens vernieuwen, gebruikers aanmelden, bepaalde gebruikersgegevens weergeven, enzovoort.
- Raadpleeg [Toepassingsobjecten en service-principal-objecten](app-objects-and-service-principals.md) voor meer informatie over de twee Azure Active Directory-objecten die een geregistreerde toepassing vertegenwoordigen en de relatie ertussen.
- Zie [Huisstijlrichtlijnen voor apps](howto-add-branding-in-azure-ad-apps.md) voor meer informatie over de huisstijlrichtlijnen die u moet gebruiken bij het ontwikkelen van apps.
