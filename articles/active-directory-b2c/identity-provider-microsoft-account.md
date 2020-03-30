---
title: Aanmelden en aanmelden met een Microsoft-account instellen
titleSuffix: Azure AD B2C
description: Geef aanmeldingen en aanmelding aan klanten met Microsoft-accounts in uw toepassingen met Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 25784eb161a860398b0741d1d20375cabd1c4eca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188015"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Aanmelden en aanmelden met een Microsoft-account instellen met Azure Active Directory B2C

## <a name="create-a-microsoft-account-application"></a>Een Microsoft-accounttoepassing maken

Als u een Microsoft-account wilt gebruiken als [identiteitsprovider](openid-connect.md) in Azure Active Directory B2C (Azure AD B2C), moet u een toepassing maken in de Azure AD-tenant. De Azure AD-tenant is niet hetzelfde als uw Azure AD B2C-tenant. Als u nog geen Microsoft-account hebt, kunt [https://www.live.com/](https://www.live.com/)u er een krijgen op .

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Zorg ervoor dat u de map met uw Azure AD-tenant gebruikt door het **filter Directory + abonnement** in het bovenste menu te selecteren en de map te kiezen die uw Azure AD-tenant bevat.
1. Kies **Alle services** in de linkerbovenhoek van de Azure-portal en zoek en selecteer **app-registraties**.
1. Selecteer **Nieuwe registratie**.
1. Voer een **naam** in voor uw toepassing. *MsAapp1*bijvoorbeeld .
1. Selecteer **accounts in een organisatiemap en persoonlijke Microsoft-accounts (bijvoorbeeld Skype, Xbox, Outlook.com)** onder Ondersteunde **accounttypen.** Deze optie is gericht op de breedste set microsoft-identiteiten.

   Zie [Snelstart: Een toepassing registreren bij het Microsoft-identiteitsplatform](../active-directory/develop/quickstart-register-app.md)voor meer informatie over de verschillende selecties van het accounttype.
1. Selecteer Onder **URI omleiden (optioneel)** **web** en voer u `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` het tekstvak in. Vervang `your-tenant-name` door de naam van uw Azure AD B2C-tenant.
1. Selecteer **Registreren**
1. Neem de **toepassings-id (client) op** die wordt weergegeven op de pagina Toepassingsoverzicht. U hebt dit nodig wanneer u de identiteitsprovider in de volgende sectie configureert.
1. Certificaten **& geheimen selecteren**
1. Klik **op Nieuw clientgeheim**
1. Voer een **beschrijving** in voor het geheim, bijvoorbeeld *Toepassingswachtwoord 1,* en klik op **Toevoegen**.
1. Neem het toepassingswachtwoord op dat wordt weergegeven in de kolom **Waarde.** U hebt dit nodig wanneer u de identiteitsprovider in de volgende sectie configureert.

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Een Microsoft-account configureren als identiteitsprovider

1. Meld u als globale beheerder van de Azure AD B2C-tenant aan bij [Azure Portal](https://portal.azure.com/).
1. Zorg ervoor dat u de map met uw Azure AD B2C-tenant gebruikt door het **filter Directory + abonnement** in het bovenste menu te selecteren en de map te kiezen die uw tenant bevat.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer **Identiteitsproviders**en selecteer **vervolgens Microsoft-account**.
1. Voer een **naam**in . Bijvoorbeeld, *MSA*.
1. Voer voor de **client-id**de toepassings-id (client) in van de Azure AD-toepassing die u eerder hebt gemaakt.
1. Voer **voor**het geheim van de client het clientgeheim in dat u hebt opgenomen.
1. Selecteer **Opslaan**.
