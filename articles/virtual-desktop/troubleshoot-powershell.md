---
title: Windows Virtual Desktop PowerShell - Azure
description: Problemen met PowerShell oplossen wanneer u een Windows Virtual Desktop-tenantomgeving instelt.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3fb5436c2b5c30c5336385792d0597bdcea2b538
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127468"
---
# <a name="windows-virtual-desktop-powershell"></a>Windows Virtual Desktop PowerShell

Gebruik dit artikel om fouten en problemen op te lossen bij het gebruik van PowerShell met Windows Virtual Desktop. Zie [Windows Virtual Desktop Powershell](/powershell/module/windowsvirtualdesktop/)voor meer informatie over PowerShell voor Extern bureaublad-services.

## <a name="provide-feedback"></a>Feedback geven

Ga naar de [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) om de Windows Virtual Desktop-service te bespreken met het productteam en actieve communityleden.

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>PowerShell-opdrachten die worden gebruikt tijdens de installatie van Windows Virtual Desktop

In deze sectie worden PowerShell-opdrachten weergegeven die doorgaans worden gebruikt tijdens het instellen van Windows Virtual Desktop en worden manieren geboden om problemen op te lossen die zich tijdens het gebruik ervan kunnen voordoen.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-is-already-assigned-to-a-remoteapp-app-group-in-the-specified-host-pool"></a>Fout: Opdracht Add-RdsAppGroupUser - De opgegeven UserPrincipalName is al toegewezen aan een RemoteApp-appgroep in de opgegeven hostgroep

```Powershell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName 'Desktop Application Group' -UserPrincipalName <UserName>
```

**Oorzaak:** De gebruikte gebruikersnaam is al toegewezen aan een app-groep van een ander type. Gebruikers kunnen niet worden toegewezen aan zowel een extern bureaublad als een externe app-groep onder dezelfde sessiehostgroep.

**Oplossing:** Als de gebruiker zowel externe apps als extern bureaublad nodig heeft, maakt u verschillende hostpools of verleent u gebruikers toegang tot het externe bureaublad, waardoor elke toepassing op de vm van sessiehost kan worden gebruikt.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-doesnt-exist-in-the-azure-active-directory-associated-with-the-remote-desktop-tenant"></a>Fout: Opdracht Add-RdsAppGroupUser : de opgegeven UserPrincipalName bestaat niet in de Azure Active Directory die is gekoppeld aan de extern bureaublad-tenant

```PowerShell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName "Desktop Application Group" -UserPrincipalName <UserPrincipalName>
```

**Oorzaak:** De gebruiker die is opgegeven door de -UserPrincipalName kan niet worden gevonden in de Azure Active Directory die is gekoppeld aan de Windows Virtual Desktop-tenant.

**Oplossing:** Bevestig de items in de volgende lijst.

- De gebruiker wordt gesynchroniseerd met Azure Active Directory.
- De gebruiker is niet gebonden aan business to consumer (B2C) of business-to-business (B2B) commerce.
- De Windows Virtual Desktop-tenant is gekoppeld aan het corrigeren van Azure Active Directory.

### <a name="error-get-rdsdiagnosticactivities----user-isnt-authorized-to-query-the-management-service"></a>Fout: Get-RdsDiagnosticActivities - De gebruiker is niet bevoegd om de beheerservice op te vragen

```PowerShell
Get-RdsDiagnosticActivities -ActivityId <ActivityId>
```

**Oorzaak:** parameter -TenantName

**Oplossing:** Probleem Get-RdsDiagnosticActivities met \<-TenantName TenantName>.

### <a name="error-get-rdsdiagnosticactivities----the-user-isnt-authorized-to-query-the-management-service"></a>Fout: Get-RdsDiagnosticActivities - de gebruiker is niet bevoegd om de beheerservice op te vragen

```PowerShell
Get-RdsDiagnosticActivities -Deployment -username <username>
```

**Oorzaak:** Met behulp van -Deployment-schakelaar.

**Oplossing:** -Implementatieswitch kan alleen worden gebruikt door implementatiebeheerders. Deze beheerders zijn meestal lid van het Virtual Desktop-team Extern bureaublad/Windows. Vervang de -Deployment-switch \<door -TenantName TenantName>.

### <a name="error-new-rdsroleassignment----the-user-isnt-authorized-to-query-the-management-service"></a>Fout: Nieuw-RdsRoleAssignment - de gebruiker is niet bevoegd om de beheerservice op te vragen

**Oorzaak 1:** Het account dat wordt gebruikt, heeft geen machtigingen voor eigenaren van extern bureaublad-services op de tenant.

**Fix 1:** Een gebruiker met machtigingen voor eigenaren van Extern bureaublad-services moet de roltoewijzing uitvoeren.

**Oorzaak 2:** Het account dat wordt gebruikt, heeft machtigingen voor de eigenaar van Extern bureaublad-services, maar maakt geen deel uit van de Azure Active Directory van de tenant of heeft geen machtigingen voor query's in de Azure Active Directory waar de gebruiker zich bevindt.

**Fix 2:** Een gebruiker met Active Directory-machtigingen moet de roltoewijzing uitvoeren.

>[!Note]
>Nieuw-RdsRoleAssignment kan geen machtigingen geven aan een gebruiker die niet bestaat in de Azure Active Directory (AD).

## <a name="next-steps"></a>Volgende stappen

- Zie [Overzicht, feedback en ondersteuning](troubleshoot-set-up-overview.md)voor een overzicht van probleemoplossing voor het oplossen van problemen met Windows Virtual Desktop en de escalatietracks.
- Zie [Tenant en hostpool maken](troubleshoot-set-up-issues.md)als u problemen wilt oplossen tijdens het maken van een tenant en hostpool in een Windows Virtual Desktop-omgeving.
- Zie Configuratie van de [virtuele machine sessiehost](troubleshoot-vm-configuration.md)voor het oplossen van problemen tijdens het configureren van een virtuele machine (VM) in Windows Virtual Desktop.
- Zie [Windows Virtual Desktop-serviceverbindingen](troubleshoot-service-connection.md)voor het oplossen van problemen met Windows Virtual Desktop-clientverbindingen.
- Zie Problemen met [de Extern bureaublad-client oplossen](troubleshoot-client.md) als u problemen met Extern bureaublad-clients wilt oplossen
- Zie De Windows Virtual [Desktop-omgeving](environment-setup.md)voor meer informatie over de service.
- Zie [Zelfstudie: Problemen met resourcebeheersjabloonimplementaties](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)oplossen.
- Zie [Controlebewerkingen met Resource Manager](../azure-resource-manager/management/view-activity-logs.md)voor meer informatie over controleacties.
- Zie [Implementatiebewerkingen weergeven](../azure-resource-manager/templates/deployment-history.md)voor meer informatie over acties om de fouten tijdens de implementatie te bepalen.
