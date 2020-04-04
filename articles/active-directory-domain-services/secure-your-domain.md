---
title: Beveiligde Azure AD-domeinservices | Microsoft Documenten
description: Meer informatie over het uitschakelen van zwakke cijfers, oude protocollen en NTLM-wachtwoordhashsynchronisatie voor een beheerd Azure Active Directory Domain Services-domein.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: 581963c94129c36acbd8761d93e369281797fa9f
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654724"
---
# <a name="disable-weak-ciphers-and-password-hash-synchronization-to-secure-an-azure-ad-domain-services-managed-domain"></a>Zwakke cijfers en wachtwoordhashsynchronisatie uitschakelen om een beheerd Azure AD Domain Services-domein te beveiligen

Azure Active Directory Domain Services (Azure AD DS) maakt standaard het gebruik van cijfers zoals NTLM v1 en TLS v1 mogelijk. Deze cijfers kunnen nodig zijn voor sommige oudere toepassingen, maar worden beschouwd als zwak en kunnen worden uitgeschakeld als u ze niet nodig hebt. Als u on-premises hybride connectiviteit hebt met Azure AD Connect, u ook de synchronisatie van NTLM-wachtwoordhashes uitschakelen.

In dit artikel ziet u hoe u NTLM v1- en TLS v1-ciphers uitschakelt en ntlm-wachtwoordhashsynchronisatie uitschakelt.

## <a name="prerequisites"></a>Vereisten

Om dit artikel te voltooien, hebt u de volgende bronnen nodig:

* Een actief Azure-abonnement.
    * Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)aan .
* Een Azure Active Directory-tenant die is gekoppeld aan uw abonnement, gesynchroniseerd met een on-premises directory of een map met alleen wolken.
    * Maak indien nodig [een Azure Active Directory-tenant][create-azure-ad-tenant] of [koppel een Azure-abonnement aan uw account.][associate-azure-ad-tenant]
* Een beheerd azure Directory Domain Services-domein is ingeschakeld en geconfigureerd in uw Azure AD-tenant.
    * Maak en configureer indien nodig [een Azure Active Directory Domain Services-exemplaar][create-azure-ad-ds-instance].
* Installeer en configureer Azure PowerShell.
    * Volg indien nodig de instructies om [de Azure PowerShell-module te installeren en verbinding te maken met uw Azure-abonnement.](/powershell/azure/install-az-ps)
    * Zorg ervoor dat u zich aanmeldt bij uw Azure-abonnement met de [cmdlet Connect-AzAccount.][Connect-AzAccount]
* Azure AD PowerShell installeren en configureren.
    * Volg indien nodig de instructies om [de Azure AD PowerShell-module](/powershell/azure/active-directory/install-adv2)te installeren en verbinding te maken met Azure AD.
    * Zorg ervoor dat u zich aanmeldt bij uw Azure AD-tenant met de cmdlet [Connect-AzureAD.][Connect-AzureAD]

## <a name="disable-weak-ciphers-and-ntlm-password-hash-sync"></a>Zwakke cijfers en NTLM-wachtwoordhashsynchronisatie uitschakelen

Als u zwakke versleutelingssuites en NTLM-hashsynchronisatie wilt uitschakelen, meldt u zich aan bij uw Azure-account en krijgt u de Azure AD DS-bron met behulp van de cmdlet [Get-AzResource:][Get-AzResource]

> [!TIP]
> Als er een fout optreedt met de opdracht [Get-AzResource][Get-AzResource] dat de *Microsoft.AAD/DomainServices-bron* niet bestaat, [verhoogt u uw toegang om alle Azure-abonnementen en beheergroepen te beheren.][global-admin]

```powershell
Login-AzAccount

$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"
```

Definieer vervolgens *DomainSecuritySettings* om de volgende beveiligingsopties te configureren:

1. NTLM v1-ondersteuning uitschakelen.
2. Schakel de synchronisatie van NTLM-wachtwoordhashes uit van uw on-premises AD.
3. Schakel TLS v1 uit.

> [!IMPORTANT]
> Gebruikers en serviceaccounts kunnen geen eenvoudige LDAP-bindingen uitvoeren als u ntlm-wachtwoordhashsynchronisatie uitschakelt in het beheerde Azure AD DS-domein. Als u ldap-eenvoudige bindingen moet uitvoeren, stelt u de optie *'SyncNtlmPasswords'='Uitgeschakeld'* niet in; beveiligingsconfiguratieoptie in de volgende opdracht.

```powershell
$securitySettings = @{"DomainSecuritySettings"=@{"NtlmV1"="Disabled";"SyncNtlmPasswords"="Disabled";"TlsV1"="Disabled"}}
```

Pas ten slotte de gedefinieerde beveiligingsinstellingen toe op het door Azure AD DS beheerde domein met de cmdlet [Set-AzResource.][Set-AzResource] Geef de Azure AD DS-bron op vanaf de eerste stap en de beveiligingsinstellingen van de vorige stap.

```powershell
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -Verbose -Force
```

Het duurt even voordat de beveiligingsinstellingen worden toegepast op het beheerde Azure AD DS-domein.

## <a name="next-steps"></a>Volgende stappen

Zie [Hoe objecten en referenties worden gesynchroniseerd in een door Azure AD DS beheerd domein][synchronization]voor meer informatie over het synchronisatieproces.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[global-admin]: ../role-based-access-control/elevate-access-global-admin.md
[synchronization]: synchronization.md

<!-- EXTERNAL LINKS -->
[Get-AzResource]: /powershell/module/az.resources/Get-AzResource
[Set-AzResource]: /powershell/module/Az.Resources/Set-AzResource
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD