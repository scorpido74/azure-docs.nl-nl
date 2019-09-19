---
title: Voor basislijn beleid is MFA vereist voor Service beheer (preview)-Azure Active Directory
description: Beleid voor voorwaardelijke toegang om MFA te vereisen voor Azure Resource Manager
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 06b3a3f12b2955ae97c43b0caf6a4ac13c9b770a
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086734"
---
# <a name="baseline-policy-require-mfa-for-service-management-preview"></a>Basislijn beleid: MFA vereisen voor Service beheer (preview-versie)

U gebruikt mogelijk een aantal Azure-Services in uw organisatie. Deze services kunnen worden beheerd via Azure Resource Manager-API:

* Azure Portal
* Azure PowerShell
* Azure-CLI

Het gebruik van Azure Resource Manager voor het beheren van uw services is een zeer geprivilegieerde actie. Azure Resource Manager kunt de configuratie van de Tenant breed wijzigen, zoals service-instellingen en abonnements facturering. Verificatie met één factor is kwetsbaar voor diverse aanvallen zoals phishing en wachtwoord spray. Daarom is het belang rijk om de identiteit te verifiëren van gebruikers die toegang willen hebben tot de configuraties van Azure Resource Manager en bij te werken door multi-factor Authentication te vereisen voordat toegang wordt toegestaan.

**Vereisen dat MFA voor Service beheer** een [basis beleid](concept-baseline-protection.md) is dat MFA vereist voor gebruikers die toegang hebben tot Azure Portal, Azure PowerShell of Azure cli. Dit beleid is van toepassing op alle gebruikers die toegang tot Azure Resource Manager hebben, ongeacht of ze een beheerder zijn.

Zodra dit beleid is ingeschakeld in een Tenant, worden alle gebruikers die zich aanmelden bij Azure-beheer resources, gevraagd met multi-factor Authentication. Als de gebruiker niet is geregistreerd voor MFA, moet de gebruiker zich registreren met behulp van de Microsoft Authenticator-app om door te gaan.

Gebruik de cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) om interactief aanmelden met [Azure Power shell](https://docs.microsoft.com/powershell/azure/authenticate-azureps)uit te voeren.

```PowerShell
Connect-AzAccount
```

Wanneer deze cmdlet wordt uitgevoerd, vertegenwoordigt deze een tokenreeks. Als u zich wilt aanmelden, kopieert u deze teken reeks [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin) en plakt u deze in een browser. Uw PowerShell-sessie wordt geverifieerd om verbinding te maken met Azure.

Voer de opdracht [AZ login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) uit om interactief aanmelden met behulp van [Azure cli](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)uit te voeren.

```azurecli
az login
```

Als de CLI uw standaardbrowser kan openen, gebeurt dat ook en wordt er een aanmeldingspagina gedownload. Anders moet u een browser pagina openen en de instructies op de opdracht regel volgen om een autorisatie code in te voeren nadat u [https://aka.ms/devicelogin](https://aka.ms/devicelogin) in uw browser hebt genavigeerd. Meld u vervolgens aan met uw account referenties in de browser.

## <a name="deployment-considerations"></a>Overwegingen bij de implementatie

De **vereist MFA voor Service Management** -beleid is van toepassing op alle Azure Resource Manager-gebruikers.

## <a name="enable-the-baseline-policy"></a>Het basislijn beleid inschakelen

Beleid voor **beleids basislijn: Vereisen dat MFA voor Service Management (preview** ) vooraf is geconfigureerd en wordt bovenaan weer gegeven wanneer u navigeert naar de Blade voorwaardelijke toegang in azure Portal.

Om dit beleid in te scha kelen en uw beheerders te beschermen:

1. Meld u aan bij de **Azure Portal** als globale beheerder, beveiligings beheerder of beheerder van de voorwaardelijke toegang.
1. Blader naar **Azure Active Directory** > **voorwaardelijke toegang**.
1. Selecteer **basislijn beleid in de lijst met beleids regels: MFA vereisen voor Service beheer (preview)** .
1. Stel **beleid inschakelen** om **beleid direct te gebruiken**in.
1. Klik op **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie:

* [Basis beveiligings beleid voor voorwaardelijke toegang](concept-baseline-protection.md)
* [Vijf stappen voor het beveiligen van uw identiteits infrastructuur](../../security/fundamentals/steps-secure-identity.md)
* [Wat is voorwaardelijke toegang in Azure Active Directory?](overview.md)
