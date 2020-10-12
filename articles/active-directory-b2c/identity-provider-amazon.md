---
title: Registratie instellen en aanmelden met een Amazon-account
titleSuffix: Azure AD B2C
description: Bied u de mogelijkheid om u aan te melden en u aan te melden bij klanten met Amazon-accounts in uw toepassingen met Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d1a771cb13fcfa76449500ad71c67dcf7c446fa4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85388439"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Registratie instellen en aanmelden met een Amazon-account met Azure Active Directory B2C

## <a name="create-an-app-in-the-amazon-developer-console"></a>Een app maken in de Amazon-ontwikkelaars console

Als u een Amazon-account als een federatieve id-provider in Azure Active Directory B2C (Azure AD B2C) wilt gebruiken, moet u een toepassing maken in uw [Amazon-ontwikkelaars Services en-technologieën](https://developer.amazon.com). Als u nog geen Amazon-account hebt, kunt u zich aanmelden bij [https://www.amazon.com/](https://www.amazon.com/) .

> [!NOTE]  
> Gebruik de volgende Url's in **stap 8** hieronder, waarbij u vervangt `your-tenant-name` door de naam van uw Tenant. Gebruik bij het invoeren van de naam van uw Tenant alle kleine letters, zelfs als de Tenant is gedefinieerd met hoofd letters in Azure AD B2C.
> - Voer voor **toegestane oorsprongen**`https://your-tenant-name.b2clogin.com` 
> - Voer voor **toegestane retour-url's**`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`

[!INCLUDE [identity-provider-amazon-idp-register.md](../../includes/identity-provider-amazon-idp-register.md)]

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>Een Amazon-account configureren als een id-provider

1. Meld u als globale beheerder van de Azure AD B2C-tenant aan bij [Azure Portal](https://portal.azure.com/).
1. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-tenant bevat door in het bovenste menu te klikken op het filter **Map en abonnement** en de map te kiezen waarin de tenant zich bevindt.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer **id-providers**en selecteer vervolgens **Amazon**.
1. Voer een **naam** in. Bijvoorbeeld *Amazon*.
1. Voer voor de **client-id**de client-id in van de Amazon-toepassing die u eerder hebt gemaakt.
1. Voer voor het **client geheim**het client geheim in dat u hebt vastgelegd.
1. Selecteer **Opslaan**.
