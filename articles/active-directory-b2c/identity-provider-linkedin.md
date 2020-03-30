---
title: Aanmelden en aanmelden met een LinkedIn-account instellen
titleSuffix: Azure AD B2C
description: Geef aanmeldingen en aanmelding aan klanten met LinkedIn-accounts in uw toepassingen met Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d32eb80ffe296d86164a6d27a1b7a28181357243
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188097"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>Aanmelden en aanmelden met een LinkedIn-account instellen met Azure Active Directory B2C

## <a name="create-a-linkedin-application"></a>Een LinkedIn-toepassing maken

Als u een LinkedIn-account wilt gebruiken als [identiteitsprovider](authorization-code-flow.md) in Azure Active Directory B2C (Azure AD B2C), moet u een toepassing maken in uw tenant die deze vertegenwoordigt. Als je nog geen LinkedIn-account hebt, kun [https://www.linkedin.com/](https://www.linkedin.com/)je je aanmelden bij .

1. Meld u aan bij de [LinkedIn Developers-website](https://www.developer.linkedin.com/) met uw LinkedIn-accountgegevens.
1. Selecteer **Mijn apps**en klik op Toepassing **maken**.
1. Voer **bedrijfsnaam,** **toepassingsnaam,** **toepassingsbeschrijving,** **toepassingslogo,** **toepassingsgebruik,** **website-URL,** **zakelijke e-mail**en **zakelijke telefoon in**.
1. Ga akkoord met de **gebruiksvoorwaarden van** de LinkedIn API en klik op **Verzenden**.
1. Kopieer de waarden van **client-id** en **clientgeheim**. U ze vinden onder **Verificatiesleutels**. Je hebt ze allebei nodig om LinkedIn te configureren als identiteitsprovider in je tenant. **Client Secret** is een belangrijke beveiligingsreferentie.
1. Voer `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` **in in geautoriseerde omleidings-URL's**. Vervang `your-tenant-name` door de naam van uw huurder. U moet alle kleine letters gebruiken bij het invoeren van uw tenantnaam, zelfs als de tenant is gedefinieerd met hoofdletters in Azure AD B2C. Selecteer **Toevoegen**en klik op **Bijwerken**.

## <a name="configure-a-linkedin-account-as-an-identity-provider"></a>Een LinkedIn-account configureren als identiteitsprovider

1. Meld u als globale beheerder van de Azure AD B2C-tenant aan bij [Azure Portal](https://portal.azure.com/).
1. Zorg ervoor dat u de map met uw Azure AD B2C-tenant gebruikt door het **filter Directory + abonnement** in het bovenste menu te selecteren en de map te kiezen die uw tenant bevat.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer **Identiteitsproviders**en selecteer **Vervolgens LinkedIn**.
1. Voer een **naam**in . *Bijvoorbeeld, LinkedIn*.
1. Voer voor de **client-id**de client-id in van de LinkedIn-toepassing die u eerder hebt gemaakt.
1. Voer **voor**het geheim van de client het clientgeheim in dat u hebt opgenomen.
1. Selecteer **Opslaan**.

## <a name="migration-from-v10-to-v20"></a>Migratie van v1.0 naar v2.0

LinkedIn heeft onlangs [hun API's bijgewerkt van v1.0 naar v2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). Als onderdeel van de migratie kan Azure AD B2C alleen de volledige naam van de LinkedIn-gebruiker verkrijgen tijdens het aanmelden. Als een e-mailadres een van de kenmerken is die tijdens het aanmelden worden verzameld, moet de gebruiker handmatig het e-mailadres invoeren en valideren.
