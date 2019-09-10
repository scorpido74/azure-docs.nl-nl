---
title: Azure AD Domain Services beveiligen | Microsoft Docs '
description: Meer informatie over het uitschakelen van zwakke code ringen, oude protocollen en NTLM-wachtwoord synchronisatie voor een Azure Active Directory Domain Services beheerd domein.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 09/09/2019
ms.author: iainfou
ms.openlocfilehash: db086c56c9f16f4691efaade03571bf8a36c6444
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70842620"
---
# <a name="disable-weak-ciphers-and-password-hash-synchronization-to-secure-an-azure-ad-domain-services-managed-domain"></a>Zwakke versleuteling en wachtwoord-hash-synchronisatie uitschakelen om een Azure AD Domain Services beheerd domein te beveiligen

Azure Active Directory Domain Services (Azure AD DS) maakt standaard gebruik van code ringen, zoals NTLM v1 en TLS v1. Deze code ringen zijn mogelijk vereist voor sommige oudere toepassingen, maar worden beschouwd als zwak en kunnen worden uitgeschakeld als u deze niet nodig hebt. Als u een on-premises hybride verbinding hebt met Azure AD Connect, kunt u de synchronisatie van NTLM-wachtwoord hashes ook uitschakelen.

In dit artikel leest u hoe u NTLM v1-en TLS v1-code ringen kunt uitschakelen en NTLM-wachtwoord synchronisatie kunt uitschakelen.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende resources nodig om dit artikel te volt ooien:

* Een actief Azure-abonnement.
    * Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Een Azure Active Directory Tenant die aan uw abonnement is gekoppeld, gesynchroniseerd met een on-premises Directory of een alleen-Cloud Directory.
    * Als dat nodig is, [maakt u een Azure Active Directory-Tenant][create-azure-ad-tenant] of [koppelt u een Azure-abonnement aan uw account][associate-azure-ad-tenant].
* Een Azure Active Directory Domain Services beheerd domein ingeschakeld en geconfigureerd in uw Azure AD-Tenant.
    * Als dat nodig is, kunt [u een Azure Active Directory Domain Services-exemplaar maken en configureren][create-azure-ad-ds-instance].
* Installeer en configureer Azure PowerShell.
    * Als dat nodig is, volgt u de instructies om [de Azure PowerShell-module te installeren en verbinding te maken met uw Azure-abonnement](/powershell/azure/install-az-ps).
    * Zorg ervoor dat u zich aanmeldt bij uw Azure-abonnement met behulp van de cmdlet [Connect-AzAccount][Connect-AzAccount] .
* Azure AD Power Shell installeren en configureren.
    * Als dat nodig is, volgt u de instructies voor [het installeren van de Azure AD Power shell-module en verbinding maken met Azure AD](/powershell/azure/active-directory/install-adv2).
    * Zorg ervoor dat u zich aanmeldt bij uw Azure AD-Tenant met de cmdlet [Connect-AzureAD][Connect-AzureAD] .

## <a name="disable-weak-ciphers-and-ntlm-password-hash-sync"></a>Zwakke code ringen en NTLM-wachtwoord-hash-synchronisatie uitschakelen

Als u zwakke coderings suites en hash-synchronisatie van NTLM-referenties wilt uitschakelen, meldt u zich aan bij uw Azure-account en haalt u de Azure AD DS-resource op met behulp van de cmdlet [Get-AzResource][Get-AzResource] :

> [!TIP]
> Als er een fout bericht wordt weer gegeven met de opdracht [Get-AzResource][Get-AzResource] dat de *micro soft. Aad/DomainServices-* resource niet bestaat, [moet u uw toegang verhogen om alle Azure-abonnementen en-beheer groepen te beheren][global-admin].

```powershell
Login-AzAccount

$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"
```

Definieer vervolgens *DomainSecuritySettings* voor het configureren van de volgende beveiligings opties:

1. Schakel NTLM v1-ondersteuning uit.
2. Schakel de synchronisatie van NTLM-wachtwoord-hashes uit vanuit uw on-premises AD.
3. Schakel TLS v1 uit.

> [!IMPORTANT]
> Gebruikers en service accounts kunnen geen LDAP-eenvoudige bindingen uitvoeren als u NTLM-wachtwoord synchronisatie uitschakelen in het beheerde domein van Azure AD DS. Als u eenvoudige LDAP-bindingen wilt uitvoeren, stelt u de optie *"SyncNtlmPasswords" = "disabled";* in de volgende opdracht niet in.

```powershell
$securitySettings = @{"DomainSecuritySettings"=@{"NtlmV1"="Disabled";"SyncNtlmPasswords"="Disabled";"TlsV1"="Disabled"}}
```

Pas ten slotte de gedefinieerde beveiligings instellingen toe op de Azure AD DS beheerde domein met de cmdlet [set-AzResource][Set-AzResource] . Geef de Azure AD DS-resource op uit de eerste stap en de beveiligings instellingen van de vorige stap.

```powershell
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -Verbose -Force
```

Het duurt enkele ogen blikken voordat de beveiligings instellingen worden toegepast op het beheerde domein van Azure AD DS.

## <a name="next-steps"></a>Volgende stappen

Zie [How to Synchronizing object and credentials in an Azure AD DS Managed Domain][synchronization](Engelstalig) voor meer informatie over het synchronisatie proces.

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