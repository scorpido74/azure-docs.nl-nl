---
title: Registratie instellen en aanmelden met een micro soft-account
titleSuffix: Azure AD B2C
description: Bied u de mogelijkheid om u aan te melden en u aan te melden bij klanten met micro soft-accounts in uw toepassingen met Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ad4b08a12e63b4ae3eed0eb09e295d9730de97bb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85387997"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Registratie instellen en aanmelden met een Microsoft-account met behulp van Azure Active Directory B2C

## <a name="create-a-microsoft-account-application"></a>Een Microsoft-account-toepassing maken

Als u een Microsoft-account als een [ID-provider](openid-connect.md) in Azure Active Directory B2C (Azure AD B2C) wilt gebruiken, moet u een toepassing maken in de Azure AD-Tenant. De Azure AD-Tenant is niet hetzelfde als uw Azure AD B2C-Tenant. Als u nog geen Microsoft-account hebt, kunt u er een op ontvangen [https://www.live.com/](https://www.live.com/) .

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Zorg ervoor dat u de map met uw Azure AD-Tenant gebruikt door het filter **Directory + abonnement** te selecteren in het bovenste menu en de map te kiezen die uw Azure AD-Tenant bevat.
1. Kies **alle services** in de linkerbovenhoek van de Azure Portal en zoek en selecteer **app-registraties**.
1. Selecteer **Nieuwe registratie**.
1. Voer een **naam** in voor uw toepassing. Bijvoorbeeld *MSAapp1*.
1. Onder **ondersteunde account typen**selecteert u **accounts in elke organisatie Directory (een Azure AD-adres lijst en persoonlijke micro soft-accounts (bijvoorbeeld Skype, Xbox)**.

   Zie [Quick Start: een toepassing registreren bij het micro soft-identiteits platform](../active-directory/develop/quickstart-register-app.md)voor meer informatie over de verschillende selecties van het account type.
1. Onder **omleidings-URI (optioneel)** selecteert u **Web** en voert u `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/oauth2/authresp` in het tekstvak in. Vervang door `<tenant-name>` de naam van uw Azure AD B2C-Tenant.
1. Selecteer **registreren**
1. Noteer de **id van de toepassing (client)** die wordt weer gegeven op de overzichts pagina van de toepassing. U hebt deze nodig wanneer u de ID-provider in de volgende sectie configureert.
1. **Certificaten & geheimen** selecteren
1. Klik op **Nieuw clientgeheim**
1. Voer een **Beschrijving** in voor het geheim, bijvoorbeeld *toepassings wachtwoord 1*, en klik vervolgens op **toevoegen**.
1. Noteer het toepassings wachtwoord dat wordt weer gegeven in de kolom **waarde** . U hebt deze nodig wanneer u de ID-provider in de volgende sectie configureert.

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Een Microsoft-account als een id-provider configureren

1. Meld u als globale beheerder van de Azure AD B2C-tenant aan bij [Azure Portal](https://portal.azure.com/).
1. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-tenant bevat door in het bovenste menu te klikken op het filter **Map en abonnement** en de map te kiezen waarin de tenant zich bevindt.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer **id-providers**en selecteer vervolgens **micro soft-account**.
1. Voer een **naam**in. Bijvoorbeeld *MSA*.
1. Voer voor de **client-id**de toepassings-id (client) in van de Azure AD-toepassing die u eerder hebt gemaakt.
1. Voer voor het **client geheim**het client geheim in dat u hebt vastgelegd.
1. Selecteer **Opslaan**.
