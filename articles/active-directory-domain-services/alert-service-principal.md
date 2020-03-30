---
title: Hoofdwaarschuwingen voor services oplossen in Azure AD Domain Services | Microsoft Documenten
description: Informatie over het oplossen van serviceprincipal configuration alerts voor Azure Active Directory Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: f168870c-b43a-4dd6-a13f-5cfadc5edf2c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/20/2019
ms.author: iainfou
ms.openlocfilehash: 175bfe63176b78c5aeafc7147c46dd5ab1110325
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71257967"
---
# <a name="known-issues-service-principal-alerts-in-azure-active-directory-domain-services"></a>Bekende problemen: serviceprincipal alerts in Azure Active Directory Domain Services

[Serviceprincipals](../active-directory/develop/app-objects-and-service-principals.md) zijn toepassingen die het Azure-platform gebruikt voor het beheren, bijwerken en onderhouden van een door Azure AD DS beheerd domein. Als een serviceprincipal wordt verwijderd, wordt de functionaliteit in het door Azure AD DS beheerde domein beïnvloed.

Met dit artikel u serviceprincipal-gerelateerde configuratiewaarschuwingen oplossen en oplossen.

## <a name="alert-aadds102-service-principal-not-found"></a>Waarschuwing AADDS102: Serviceprincipal niet gevonden

### <a name="alert-message"></a>Waarschuwingsbericht

*Een Service Principal die vereist is om Azure AD Domain Services goed te laten functioneren, is verwijderd uit uw Azure AD-map. Deze configuratie heeft invloed op de mogelijkheid van Microsoft om uw beheerde domein te controleren, beheren, patchen en synchroniseren.*

Als een vereiste serviceprincipal wordt verwijderd, kan het Azure-platform geen geautomatiseerde beheertaken uitvoeren. Het door Azure AD DS beheerde domein past mogelijk geen updates toe of maakt geen back-ups.

### <a name="check-for-missing-service-principals"></a>Controleren op ontbrekende serviceprincipals

Voer de volgende stappen uit om te controleren welke serviceprincipal ontbreekt en opnieuw moet worden gemaakt:

1. Selecteer Azure Active **Directory** in de Azure-portal in het linkernavigatiemenu.
1. Selecteer **Enterprise-toepassingen**. Kies *Alle toepassingen* in het vervolgkeuzemenu **Toepassingstype** en selecteer **Vervolgens Toepassen**.
1. Zoek naar elk van de toepassings-id's. Als er geen bestaande toepassing wordt gevonden, volgt u de *stappen Voor het oplossen* van de serviceprincipal of registreert u de naamruimte opnieuw.

    | Toepassings-id | Oplossing |
    | :--- | :--- |
    | 2565bd9d-da50-47d4-8b85-4c97f669dc36 | [Een ontbrekende serviceprincipal opnieuw maken](#recreate-a-missing-service-principal) |
    | 443155a6-77f3-45e3-882b-22b3a8d431fb | [De Microsoft.AAD-naamruimte opnieuw registreren](#re-register-the-microsoft-aad-namespace) |
    | abba844e-bc0e-44b0-947a-dc74e5d09022 | [De Microsoft.AAD-naamruimte opnieuw registreren](#re-register-the-microsoft-aad-namespace) |
    | d87dcbc6-a371-462e-88e3-28ad15ec4e64 | [De Microsoft.AAD-naamruimte opnieuw registreren](#re-register-the-microsoft-aad-namespace) |

### <a name="recreate-a-missing-service-principal"></a>Een ontbrekende serviceprincipal opnieuw maken

Als toepassings-id *2565bd9d-da50-47d4-8b85-4c97f669dc36* ontbreekt in uw Azure AD-map, gebruikt u Azure AD PowerShell om de volgende stappen uit te voeren. Zie [Azure AD PowerShell installeren](/powershell/azure/active-directory/install-adv2)voor meer informatie .

1. Installeer de Azure AD PowerShell-module en importeer deze als volgt:

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

1. Maak nu de serviceprincipal opnieuw met de cmdlet [Nieuw-AzureAdServicePrincipal:][New-AzureAdServicePrincipal]

    ```powershell
    New-AzureAdServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
    ```

De status van het Azure AD DS-domein wordt automatisch binnen twee uur bijgewerkt en verwijdert de waarschuwing.

### <a name="re-register-the-microsoft-aad-namespace"></a>De Microsoft AAD-naamruimte opnieuw registreren

Bij aanvraag ID *443155a6-77f3-45e3-882b-22b3a8d431fb*, *abba844e-bc0e-44b0-947 a-dc74e5d09022*, of *d87dcbc6-a371-462e-88e3-28ad15ec4e64* ontbreekt in uw Azure AD-directory, voer de volgende stappen uit om de *Microsoft.AAD-bronprovider* opnieuw te registreren:

1. Zoek en selecteer in de Azure-portal abonnementen en selecteer **deze.**
1. Kies het abonnement dat is gekoppeld aan uw door Azure AD DS beheerde domein.
1. Kies **resourceproviders**in de linkernavigatie.
1. Zoek naar *Microsoft.AAD*en selecteer **Opnieuw registreren**.

De status van het Azure AD DS-domein wordt automatisch binnen twee uur bijgewerkt en verwijdert de waarschuwing.

## <a name="alert-aadds105-password-synchronization-application-is-out-of-date"></a>Waarschuwing AADDS105: Wachtwoordsynchronisatietoepassing is verouderd

### <a name="alert-message"></a>Waarschuwingsbericht

*De service principal met de applicatie-ID "d87dcbc6-a371-462e-88e3-28ad15ec4e64" werd verwijderd en vervolgens opnieuw gemaakt. De recreatie laat inconsistente machtigingen achter op Azure AD Domain Services-bronnen die nodig zijn om uw beheerde domein te onderhouden. Synchronisatie van wachtwoorden op uw beheerde domein kan worden beïnvloed.*

Azure AD DS synchroniseert automatisch gebruikersaccounts en referenties vanuit Azure AD. Als er een probleem is met de Azure AD-toepassing die voor dit proces wordt gebruikt, mislukt de synchronisatie van referenties tussen Azure AD DS en Azure AD.

### <a name="resolution"></a>Oplossing

Als u de Azure AD-toepassing wilt opnieuw maken die wordt gebruikt voor het synchroniseren van referenties, gebruikt u Azure AD PowerShell om de volgende stappen uit te voeren. Zie [Azure AD PowerShell installeren](/powershell/azure/active-directory/install-adv2)voor meer informatie .

1. Installeer de Azure AD PowerShell-module en importeer deze als volgt:

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

2. Verwijder nu de oude toepassing en het object met de volgende PowerShell-cmdlets:

    ```powershell
    $app = Get-AzureADApplication -Filter "IdentifierUris eq 'https://sync.aaddc.activedirectory.windowsazure.com'"
    Remove-AzureADApplication -ObjectId $app.ObjectId
    $spObject = Get-AzureADServicePrincipal -Filter "DisplayName eq 'Azure AD Domain Services Sync'"
    Remove-AzureADServicePrincipal -ObjectId $app.ObjectId
    ```

Nadat u beide toepassingen hebt verwijderd, maakt het Azure-platform deze automatisch opnieuw en probeert het wachtwoordsynchronisatie te hervatten. De status van het Azure AD DS-domein wordt automatisch binnen twee uur bijgewerkt en verwijdert de waarschuwing.

## <a name="next-steps"></a>Volgende stappen

Als u nog steeds problemen hebt, [opent u een Azure-ondersteuningsaanvraag][azure-support] voor extra hulp bij het oplossen van problemen.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md

<!-- EXTERNAL LINKS -->
[New-AzureAdServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
