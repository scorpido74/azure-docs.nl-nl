---
title: Registratie instellen en aanmelden met een LinkedIn-account
titleSuffix: Azure AD B2C
description: Bied u de mogelijkheid om u aan te melden en u aan te melden bij klanten met LinkedIn-accounts in uw toepassingen met Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: fc6537b56b26d6becfe0851a09d3bfe0f13050ab
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2019
ms.locfileid: "74947704"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>Registratie instellen en aanmelden met een LinkedIn-account met behulp van Azure Active Directory B2C

## <a name="create-a-linkedin-application"></a>Een LinkedIn-toepassing maken

Als u een LinkedIn-account wilt gebruiken als een [ID-provider](active-directory-b2c-reference-oauth-code.md) in Azure Active Directory B2C (Azure AD B2C), moet u een toepassing maken in uw Tenant waarmee deze wordt vertegenwoordigd. Als u nog geen LinkedIn-account hebt, kunt u zich aanmelden bij [https://www.linkedin.com/](https://www.linkedin.com/).

1. Meld u aan bij de [LinkedIn-ontwikkelaars website](https://www.developer.linkedin.com/) met uw LinkedIn-account referenties.
1. Selecteer **mijn apps**en klik vervolgens op **toepassing maken**.
1. Voer de **Bedrijfs naam**, **toepassings naam**, **toepassings beschrijving**, **toepassings logo**, **toepassings gebruik**, **website-URL**, **zakelijk e-mail adres**en **zakelijke telefoon**in.
1. Ga akkoord met de **gebruiks voorwaarden van de LINKEDIN API** en klik op **verzenden**.
1. Kopieer de waarden van **Client-ID** en **Clientgeheim**. U kunt ze vinden onder **verificatie sleutels**. U hebt beide nodig om LinkedIn te configureren als een id-provider in uw Tenant. **Client geheim** is een belang rijke beveiligings referentie.
1. Geef `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` op in **geautoriseerde omleidings-url's**. Vervang `your-tenant-name` door de naam van uw Tenant. U moet alle kleine letters gebruiken bij het invoeren van de naam van uw Tenant, zelfs als de Tenant is gedefinieerd met hoofd letters in Azure AD B2C. Selecteer **toevoegen**en klik vervolgens op **bijwerken**.

## <a name="configure-a-linkedin-account-as-an-identity-provider"></a>Een LinkedIn-account configureren als een id-provider

1. Meld u als globale beheerder van de Azure AD B2C-tenant aan bij [Azure Portal](https://portal.azure.com/).
1. Zorg ervoor dat u de map met uw Azure AD B2C-Tenant gebruikt door het filter **Directory + abonnement** te selecteren in het bovenste menu en de map te kiezen die uw Tenant bevat.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer **id-providers**en selecteer vervolgens **LinkedIn**.
1. Voer een **naam**in. Bijvoorbeeld *LinkedIn*.
1. Voer voor de **client-id**de client-id in van de LinkedIn-toepassing die u eerder hebt gemaakt.
1. Voer voor het **client geheim**het client geheim in dat u hebt vastgelegd.
1. Selecteer **Opslaan**.

## <a name="migration-from-v10-to-v20"></a>Migratie van v 1.0 naar v 2.0

LinkedIn heeft [de api's recent bijgewerkt van v 1.0 naar v 2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). Als onderdeel van de migratie kan Azure AD B2C alleen de volledige naam van de LinkedIn-gebruiker verkrijgen tijdens de registratie. Als een e-mail adres een van de kenmerken is die worden verzameld tijdens het aanmelden, moet de gebruiker het e-mail adres hand matig invoeren en valideren.
