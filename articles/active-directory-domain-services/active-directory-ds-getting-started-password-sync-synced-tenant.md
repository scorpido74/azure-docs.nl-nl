---
title: 'Azure AD Domain Services: Wachtwoord synchronisatie inschakelen | Microsoft Docs'
description: Aan de slag met Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 8731f2b2-661c-4f3d-adba-2c9e06344537
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: iainfou
ms.openlocfilehash: 0b38bdba0d88da9296106411737c280dcf6d5df1
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69613172"
---
# <a name="enable-password-hash-synchronization-to-azure-active-directory-domain-services"></a>Synchronisatie van wachtwoordhashes inschakelen voor Azure Active Directory Domain Services

Een gesynchroniseerde Azure AD-tenant wordt zodanig ingesteld dat deze wordt gesynchroniseerd met de on-premises directory van uw organisatie via Azure AD Connect. Azure AD Connect synchroniseert niet standaard NTLM- en Kerberos-referentie-hashes met Azure AD. Als u Azure AD Domain Services wilt gebruiken, moet u Azure AD Connect configureren zodat de referentie-hashes die vereist zijn voor NTLM- en Kerberos-verificatie worden gesynchroniseerd. In de volgende stappen schakelt u de synchronisatie van de vereiste referentie-hashes van uw on-premises map met uw Azure AD-tenant in.

> [!NOTE]
> **Als uw organisatie gebruikersaccounts heeft die worden gesynchroniseerd vanuit uw on-premises map, moet u synchronisatie van NTLM- en Kerberos-hashes inschakelen om het beheerde domein te kunnen gebruiken.** Een gesynchroniseerd gebruikersaccount is een account dat is gemaakt in uw on-premises map en dat wordt gesynchroniseerd met uw Azure AD-tenant met gebruik van Azure AD Connect. De wachtwoord-hash-synchronisatie is een ander proces dan de synchronisatie van gebruikers/objecten. U moet wachtwoord-hash-synchronisatie uitschakelen en inschakelen om een volledige wachtwoord synchronisatie te krijgen. vervolgens worden de batch-hash-updates in het logboek voor toepassings gebeurtenissen weer geven.

In dit artikel schakelt u synchronisatie in van referentie-hashes die vereist zijn voor NT LAN Manager (NTLM) en Kerberos-verificatie om te Azure AD Domain Services. Wanneer u de referentiesynchronisatie hebt ingesteld, kunnen gebruikers zich bij het beheerde domein aanmelden met hun zakelijke referenties.

De vereiste stappen voor gebruikersaccounts in de cloud en gebruikersaccounts die worden gesynchroniseerd vanuit uw on-premises map met Azure AD Connect, verschillen.

| **Type gebruikersaccount** | **Stappen die moeten worden uitgevoerd** |
| --- | --- |
| **Gebruikersaccounts die zijn gesynchroniseerd vanuit een on-premises map** |**&#x2713;** [Volg de instructies in dit artikel](active-directory-ds-getting-started-password-sync-synced-tenant.md#install-or-update-azure-ad-connect) |
| **Cloud-gebruikersaccounts die zijn gemaakt in Azure AD** |**&#x2713;** [synchroniseer de wachtwoorden voor cloudgebruikersaccounts met het beheerde domein](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) |

> [!TIP]
> **Mogelijk moet u beide sets met stappen voltooien.**
> Als de Azure AD-tenant een combinatie heeft van cloudgebruikers en gebruikers van on-premises AD, moet u beide sets met stappen uitvoeren.

## <a name="install-or-update-azure-ad-connect"></a>Azure AD Connect installeren of bijwerken

Installeer de meest recente aanbevolen versie van Azure AD Connect op een computer die lid is van het domein. Als u een bestaand exemplaar van de Azure AD Connect-installatie hebt, moet u deze bijwerken om de nieuwste versie van Azure AD Connect te verkrijgen. Gebruik altijd de nieuwste versie van Azure AD Connect beschikt om te voorkomen dat bekende problemen/fouten optreden die mogelijk al zijn opgelost.

**[Azure AD Connect downloaden](https://www.microsoft.com/download/details.aspx?id=47594)**

Aanbevolen versie: **1.1.614.0** : gepubliceerd op 5 september 2017.

> [!WARNING]
> U MOET de meest recente aanbevolen versie van Azure AD Connect installeren om ervoor te zorgen dat oudere wachtwoordreferenties (die zijn vereist voor NTLM- en Kerberos-verificatie) kunnen worden gesynchroniseerd met uw Azure AD-tenant. Deze functionaliteit is niet beschikbaar in eerdere versies van Azure AD Connect of in het oudere DirSync-hulpprogramma.

Installatie-instructies voor Azure AD Connect zijn beschikbaar in het artikel [Getting started with Azure AD Connect](../active-directory/hybrid/whatis-hybrid-identity.md) (Aan de slag met Azure AD Connect).

## <a name="enable-synchronization-of-ntlm-and-kerberos-credential-hashes-to-azure-ad"></a>Synchronisatie van referentie-hashes van NTLM en Kerberos naar Azure AD inschakelen

Voer het volgende PowerShell-script uit voor elk AD-forest. Het script zorgt ervoor dat alle NTLM- en Kerberos-wachtwoordhashes van on-premises gebruikers kunnen worden gesynchroniseerd naar de Azure AD-tenant. Het script initieert ook een volledige synchronisatie in Azure AD Connect.

```powershell
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"
$azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"
Import-Module adsync
$c = Get-ADSyncConnector -Name $adConnector
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1
$c.GlobalParameters.Remove($p.Name)
$c.GlobalParameters.Add($p)
$c = Add-ADSyncConnector -Connector $c
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $false
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $true
```

Afhankelijk van de grootte van de directory (aantal gebruikers, groepen enzovoort), kan de synchronisatie van referentie-hashes naar Azure AD enige tijd duren. De wachtwoorden kunnen in het beheerde domein van Azure AD Domain Services worden gebruikt kort nadat de referentie-hashes zijn gesynchroniseerd naar Azure AD.

## <a name="next-steps"></a>Volgende stappen

> [!NOTE]
> De **synchronisatie van wacht woord** -hashes is een ander proces dan synchronisatie van gebruiker/object. U moet wachtwoord-hash-synchronisatie uitschakelen en inschakelen om een volledige wachtwoord synchronisatie te krijgen. vervolgens worden de batch-hash-updates in het logboek voor toepassings gebeurtenissen weer geven.

* [Wachtwoordsynchronisatie met AAD Domain Services inschakelen voor een Azure AD-directory die zich alleen in de cloud bevindt](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)
* [Een Azure AD Domain Services domein beheren](tutorial-create-management-vm.md)
* [Een virtuele Windows-computer toevoegen aan een beheerd domein van Azure AD Domain Services](active-directory-ds-admin-guide-join-windows-vm.md)
* [Een virtuele Red Hat Enterprise Linux-computer toevoegen aan een beheerd domein van Azure AD Domain Services](active-directory-ds-admin-guide-join-rhel-linux-vm.md)
