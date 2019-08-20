---
title: Registratie instellen en aanmelden met een micro soft-account-Azure Active Directory B2C
description: Bied u de mogelijkheid om u aan te melden en u aan te melden bij klanten met micro soft-accounts in uw toepassingen met Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 14aff313ece93feabc39194ba6859e3bca6be3bc
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69622405"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Registratie instellen en aanmelden met een Microsoft-account met behulp van Azure Active Directory B2C

## <a name="create-a-microsoft-account-application"></a>Een Microsoft-account-toepassing maken

Als u een Microsoft-account als een [ID-provider](active-directory-b2c-reference-oidc.md) wilt gebruiken in azure Active Directory (Azure AD) B2C, moet u een toepassing maken in de Azure AD-Tenant. De Azure AD-Tenant is niet hetzelfde als uw Azure AD B2C-Tenant. Als u nog geen Microsoft-account hebt, kunt u er een op [https://www.live.com/](https://www.live.com/)ontvangen.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Zorg ervoor dat u de map met uw Azure AD-Tenant gebruikt door het filter **Directory + abonnement** te selecteren in het bovenste menu en de map te kiezen die uw Azure AD-Tenant bevat.
1. Kies **alle services** in de linkerbovenhoek van de Azure Portal en zoek en selecteer **app-registraties**.
1. Selecteer **nieuwe registratie**.
1. Voer een **naam** in voor uw toepassing. Bijvoorbeeld *MSAapp1*.
1. Onder **ondersteunde account typen**selecteert u **accounts in een organisatorische map en persoonlijke micro soft-accounts (bijvoorbeeld Skype, Xbox, Outlook.com)** . Deze optie is gericht op de breedste set micro soft-identiteiten.

   Zie [voor meer informatie over de verschillende selecties van het account type Quick Start: Registreer een toepassing met het micro soft Identity](../active-directory/develop/quickstart-register-app.md)-platform.
1. Onder **omleidings-URI (optioneel)** selecteert u `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` **Web** en voert u in het tekstvak in. Vervang `your-tenant-name` door de naam van uw Azure AD B2C-Tenant.
1. Selecteer **registreren**
1. Noteer de **id van de toepassing (client)** die wordt weer gegeven op de overzichts pagina van de toepassing. U hebt deze nodig wanneer u de ID-provider in de volgende sectie configureert.
1. **Certificaten & geheimen** selecteren
1. Klik op **Nieuw client geheim**
1. Voer een **Beschrijving** in voor het geheim, bijvoorbeeld *toepassings wachtwoord 1*, en klik vervolgens op **toevoegen**.
1. Noteer het toepassings wachtwoord dat wordt weer gegeven in de kolom **waarde** . U hebt deze nodig wanneer u de ID-provider in de volgende sectie configureert.

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Een Microsoft-account als een id-provider configureren

1. Meld u als globale beheerder van de Azure AD B2C-tenant aan bij [Azure Portal](https://portal.azure.com/).
1. Zorg ervoor dat u de map met uw Azure AD B2C-Tenant gebruikt door het filter **Directory + abonnement** te selecteren in het bovenste menu en de map te kiezen die uw Tenant bevat.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer **id-providers**en selecteer vervolgens **micro soft-account**.
1. Voer een **naam**in. Bijvoorbeeld *MSA*.
1. Voer voor de **client-id**de toepassings-id (client) in van de Azure AD-toepassing die u eerder hebt gemaakt.
1. Voer voor het **client geheim**het client geheim in dat u hebt vastgelegd.
1. Selecteer **Opslaan**.
