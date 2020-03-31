---
title: Aanmelden en aanmelden met een Amazon-account
titleSuffix: Azure AD B2C
description: Geef aanmeldingen en aanmelding aan klanten met Amazon-accounts in uw toepassingen met Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d4538c1d15aeae624f5d73e9985448bda2fd8f1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188457"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Aanmelden en aanmelden met een Amazon-account instellen met Azure Active Directory B2C

## <a name="create-an-amazon-application"></a>Een Amazon-toepassing maken

Als u een Amazon-account wilt gebruiken als [identiteitsprovider](authorization-code-flow.md) in Azure Active Directory B2C (Azure AD B2C), moet u een toepassing maken in uw tenant die deze vertegenwoordigt. Als je nog geen Amazon-account hebt, [https://www.amazon.com/](https://www.amazon.com/)kun je je aanmelden bij .

1. Meld u aan bij het [Amazon Developer Center](https://login.amazon.com/) met uw Amazon-accountreferenties.
1. Als u dit nog niet hebt gedaan, klikt u op **Aanmelden,** volgt u de registratiestappen voor ontwikkelaars en accepteert u het beleid.
1. Selecteer **Nieuwe toepassing registreren**.
1. Voer een URL **voor naam,** **beschrijving**en **privacybericht in**en klik op **Opslaan**. De privacyverklaring is een pagina die u beheert en die privacygegevens aan gebruikers verstrekt.
1. Kopieer in de sectie **Webinstellingen** de waarden van **client-id**. Selecteer **Geheim weergeven** om het geheim van de client te krijgen en kopieer het vervolgens. Je hebt ze allebei nodig om een Amazon-account te configureren als identiteitsprovider in je tenant. **Client Secret** is een belangrijke beveiligingsreferentie.
1. Selecteer in de sectie **Webinstellingen** **de** `https://your-tenant-name.b2clogin.com` optie Bewerken en voer `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` vervolgens in Toegestane **JavaScript-oorsprong** en in Toegestane **URL's retourneren in.** Vervang `your-tenant-name` door de naam van uw huurder. U moet alle kleine letters gebruiken bij het invoeren van uw tenantnaam, zelfs als de tenant is gedefinieerd met hoofdletters in Azure AD B2C.
1. Klik op **Opslaan**.

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>Een Amazon-account configureren als identiteitsprovider

1. Meld u als globale beheerder van de Azure AD B2C-tenant aan bij [Azure Portal](https://portal.azure.com/).
1. Zorg ervoor dat u de map met uw Azure AD B2C-tenant gebruikt door het **filter Directory + abonnement** in het bovenste menu te selecteren en de map te kiezen die uw tenant bevat.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer **Identiteitsproviders**en selecteer **Vervolgens Amazon**.
1. Voer een **naam**in . Bijvoorbeeld, *Amazon*.
1. Voer voor de **client-id**de client-id in van de Amazon-toepassing die u eerder hebt gemaakt.
1. Voer **voor**het geheim van de client het clientgeheim in dat u hebt opgenomen.
1. Selecteer **Opslaan**.
