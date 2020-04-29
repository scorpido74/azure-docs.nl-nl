---
title: Stel registratie in en meld u aan met een QQ-account met behulp van Azure Active Directory B2C
description: Gebruik Azure Active Directory B2C om u aan te melden en u aan te melden bij klanten met QQ-accounts in uw toepassingen.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b0f15124c64e5cca54112987d486ddadaca79452
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78187984"
---
# <a name="set-up-sign-up-and-sign-in-with-a-qq-account-using-azure-active-directory-b2c"></a>Stel registratie in en meld u aan met een QQ-account met behulp van Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-qq-application"></a>Een QQ-toepassing maken

Als u een QQ-account wilt gebruiken als een id-provider in Azure Active Directory B2C (Azure AD B2C), moet u een toepassing maken in uw Tenant waarmee deze wordt vertegenwoordigd. Als u nog geen QQ-account hebt, kunt u zich aanmelden bij [https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033).

### <a name="register-for-the-qq-developer-program"></a>Registreren voor het QQ-ontwikkelaars programma

1. Meld u aan bij de [QQ-ontwikkelaars Portal](http://open.qq.com) met uw QQ-account referenties.
1. Nadat u zich hebt aangemeld, [https://open.qq.com/reg](https://open.qq.com/reg) gaat u naar om uzelf te registreren als ontwikkelaar.
1. Selecteer**个人**(afzonderlijke ontwikkelaar).
1. Voer de vereiste gegevens in en selecteer**下一步**(volgende stap).
1. Voltooi het e-mail verificatie proces. Nadat u zich hebt geregistreerd als ontwikkelaar, moet u een paar dagen wachten.

### <a name="register-a-qq-application"></a>Een QQ-toepassing registreren

1. Ga naar [https://connect.qq.com/index.html](https://connect.qq.com/index.html).
1. Selecteer**应用管理**(app Management).
1. Selecteer**创建应用**(app maken) en voer de vereiste gegevens in.
1. Voer `https://your-tenant-name.b2clogin.com/your-tenant-name}.onmicrosoft.com/oauth2/authresp` in**授权回调域**(call back-URL) in. Als uw `tenant_name` is contoso bijvoorbeeld, stelt u de URL in op `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.
1. Selecteer**创建应用**(app maken).
1. Selecteer op de pagina Bevestiging**应用管理**(app Management) om terug te keren naar de pagina voor het beheren van apps.
1. Selecteer**查看**(weer geven) naast de app die u hebt gemaakt.
1. Selecteer**修改**(bewerken).
1. Kopieer de **App-ID** en **app-sleutel**. U hebt beide waarden nodig om de ID-provider toe te voegen aan uw Tenant.

## <a name="configure-qq-as-an-identity-provider"></a>QQ configureren als een id-provider

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
1. Selecteer het pictogram voor het adres van de map en het **abonnement** op de werk balk van de portal en selecteer vervolgens de map die uw Azure AD B2C Tenant bevat.
1. Zoek in het Azure Portal naar en selecteer **Azure AD B2C**.
1. Selecteer **id-providers**en selecteer vervolgens **QQ (preview)**.
1. Voer een **naam**in. Bijvoorbeeld *QQ*.
1. Voer voor de **client-id**de app-id in van de QQ-toepassing die u eerder hebt gemaakt.
1. Voor het **client geheim**voert u de app-sleutel in die u hebt vastgelegd.
1. Selecteer **Opslaan**.
