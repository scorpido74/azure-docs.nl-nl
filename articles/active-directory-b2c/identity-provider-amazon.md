---
title: Registratie instellen en aanmelden met een Amazon-account
titleSuffix: Azure AD B2C
description: Bied u de mogelijkheid om u aan te melden en u aan te melden bij klanten met Amazon-accounts in uw toepassingen met Azure Active Directory B2C.
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
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78188457"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Registratie instellen en aanmelden met een Amazon-account met Azure Active Directory B2C

## <a name="create-an-amazon-application"></a>Een Amazon-toepassing maken

Als u een Amazon-account wilt gebruiken als een [ID-provider](authorization-code-flow.md) in Azure Active Directory B2C (Azure AD B2C), moet u een toepassing maken in uw Tenant waarmee deze wordt vertegenwoordigd. Als u nog geen Amazon-account hebt, kunt u zich aanmelden bij [https://www.amazon.com/](https://www.amazon.com/).

1. Meld u aan bij het [Amazon Developer Center](https://login.amazon.com/) met uw Amazon-account referenties.
1. Als u dit nog niet hebt gedaan, klikt u op registreren, volgt **u**de registratie stappen voor de ontwikkelaar en accepteert u het beleid.
1. Selecteer **nieuwe toepassing registreren**.
1. Voer een **naam**, **Beschrijving**en URL voor de **privacyverklaring**in en klik vervolgens op **Opslaan**. De privacyverklaring is een pagina die u beheert en die privacy-informatie verstrekt aan gebruikers.
1. Kopieer de waarden van de **client-id**in het gedeelte **Web Settings** . Selecteer **geheim weer geven** om het client geheim te ontvangen en kopieer het vervolgens. U hebt beide nodig om een Amazon-account te configureren als een id-provider in uw Tenant. **Client geheim** is een belang rijke beveiligings referentie.
1. Selecteer in de sectie **Webinstellingen** de optie **bewerken**en geef `https://your-tenant-name.b2clogin.com` op in **toegestane java script-oorsprong** en `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` in **toegestane retour-url's**. Vervang `your-tenant-name` door de naam van uw Tenant. U moet alle kleine letters gebruiken bij het invoeren van de naam van uw Tenant, zelfs als de Tenant is gedefinieerd met hoofd letters in Azure AD B2C.
1. Klik op **Opslaan**.

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>Een Amazon-account configureren als een id-provider

1. Meld u als globale beheerder van de Azure AD B2C-tenant aan bij [Azure Portal](https://portal.azure.com/).
1. Zorg ervoor dat u de map met uw Azure AD B2C-Tenant gebruikt door het filter **Directory + abonnement** te selecteren in het bovenste menu en de map te kiezen die uw Tenant bevat.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer **id-providers**en selecteer vervolgens **Amazon**.
1. Voer een **naam**in. Bijvoorbeeld *Amazon*.
1. Voer voor de **client-id**de client-id in van de Amazon-toepassing die u eerder hebt gemaakt.
1. Voer voor het **client geheim**het client geheim in dat u hebt vastgelegd.
1. Selecteer **Opslaan**.
