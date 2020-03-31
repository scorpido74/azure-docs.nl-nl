---
title: Windows Virtual Desktop-hostpool PowerShell - Azure maken
description: Een hostpool maken in Windows Virtual Desktop met PowerShell-cmdlets.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: b390c0beb20b7557294c18f889a0f41023513e2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246956"
---
# <a name="create-a-host-pool-with-powershell"></a>Een hostpool maken met PowerShell

Hostpools zijn een verzameling van een of meer identieke virtuele machines binnen Windows Virtual Desktop-tenantomgevingen. Elke hostgroep kan een appgroep bevatten waarmee gebruikers kunnen communiceren zoals op een fysiek bureaublad.

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>Uw PowerShell-client gebruiken om een hostgroep te maken

Download en importeer eerst [de Windows Virtual Desktop PowerShell-module](/powershell/windows-virtual-desktop/overview/) om te gebruiken in uw PowerShell-sessie als u dat nog niet hebt gedaan.

De volgende cmdlet uitvoeren om u aan te melden bij de Windows Virtual Desktop-omgeving

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Voer vervolgens deze cmdlet uit om een nieuwe hostgroep te maken in uw Windows Virtual Desktop-tenant:

```powershell
New-RdsHostPool -TenantName <tenantname> -Name <hostpoolname>
```

Voer de volgende cmdlet uit om een registratietoken te maken om een sessiehost te autoriseren om deel te nemen aan de hostgroep en op te slaan in een nieuw bestand op uw lokale computer. U opgeven hoe lang het registratietoken geldig is met de parameter -Expiratieuren.

```powershell
New-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname> -ExpirationHours <number of hours> | Select-Object -ExpandProperty Token | Out-File -FilePath <PathToRegFile>
```

Voer daarna deze cmdlet uit om Azure Active Directory-gebruikers toe te voegen aan de standaardgroep bureaublad-app voor de hostgroep.

```powershell
Add-RdsAppGroupUser -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName "Desktop Application Group" -UserPrincipalName <userupn>
```

De **cmdlet Add-RdsAppGroupUser** ondersteunt geen beveiligingsgroepen en voegt slechts één gebruiker tegelijk toe aan de appgroep. Als u meerdere gebruikers aan de appgroep wilt toevoegen, voert u de cmdlet opnieuw uit met de juiste gebruikershoofdnamen.

Voer de volgende cmdlet uit om het registratietoken te exporteren naar een variabele, die u later in Register the virtual machines zult gebruiken [naar de Windows Virtual Desktop hostpool.](#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool)

```powershell
$token = (Export-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname>).Token
```

## <a name="create-virtual-machines-for-the-host-pool"></a>Virtuele machines maken voor de hostpool

U nu een virtuele Azure-machine maken die kan worden samengevoegd met uw Windows Virtual Desktop-hostgroep.

U een virtuele machine op meerdere manieren maken:

- [Een virtuele machine maken op basis van een Azure Gallery-afbeelding](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Een virtuele machine maken op basis van een beheerde afbeelding](../virtual-machines/windows/create-vm-generalized-managed.md)
- [Een virtuele machine maken op basis van een niet-beheerde afbeelding](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-user-image-data-disks)

>[!NOTE]
>Als u een virtuele machine implementeert met Windows 7 als host-besturingssysteem, zal het creatie- en implementatieproces een beetje anders zijn. Zie [Een virtuele windows 7-machine implementeren op Windows Virtual Desktop](deploy-windows-7-virtual-machine.md)voor meer informatie.

Nadat u uw virtuele machines voor sessiehost hebt gemaakt, [past u een Windows-licentie toe op een vm](./apply-windows-license.md#apply-a-windows-license-to-a-session-host-vm) voor sessiehost om uw virtuele Windows- of Windows Server-machines uit te voeren zonder te betalen voor een andere licentie. 

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-agent-installations"></a>De virtuele machines voorbereiden op windows virtual desktop-agentinstallaties

U moet de volgende dingen doen om uw virtuele machines voor te bereiden voordat u de Windows Virtual Desktop-agents installeren en de virtuele machines registreren bij uw Windows Virtual Desktop-hostgroep:

- U moet zich aansluiten bij de machine. Hierdoor kunnen inkomende Windows Virtual Desktop-gebruikers vanuit hun Azure Active Directory-account worden toegewezen aan hun Active Directory-account en met succes toegang krijgen tot de virtuele machine.
- U moet de rdsh-rol (Remote Desktop Session Host) installeren als de virtuele machine een Windows Server OS uitvoert. Met de RDSH-rol kunnen de Windows Virtual Desktop-agents correct worden geïnstalleerd.

Als u met een domein wilt deelnemen, doet u de volgende dingen op elke virtuele machine:

1. [Maak verbinding met de virtuele machine](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) met de referenties die u hebt opgegeven bij het maken van de virtuele machine.
2. Start op de virtuele machine het **Configuratiescherm** en selecteer **Systeem**.
3. Selecteer **Computernaam**, selecteer **Instellingen wijzigen**en selecteer **Wijzigen...**
4. Selecteer **Domein** en voer het Active Directory-domein in op het virtuele netwerk.
5. Verifieer met een domeinaccount met bevoegdheden voor machines die lid zijn van het domein.

    >[!NOTE]
    > Als u uw VM's aansluit bij een Azure Active Directory Domain Services-omgeving (Azure AD DS), controleert u of uw domeinlid ook lid is van de [groep AAD DC Administrators.](../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group)

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool"></a>De virtuele machines registreren in de windows Virtual Desktop-hostgroep

Het registreren van de virtuele machines in een Windows Virtual Desktop-hostgroep is net zo eenvoudig als het installeren van de Windows Virtual Desktop-agents.

Ga als volgt te werk om de Windows Virtual Desktop-agents te registreren:

1. [Maak verbinding met de virtuele machine](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) met de referenties die u hebt opgegeven bij het maken van de virtuele machine.
2. Download en installeer de Windows Virtual Desktop Agent.
   - Download de [Windows Virtual Desktop Agent](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv).
   - Klik met de rechtermuisknop op het gedownloade installatieprogramma, selecteer **Eigenschappen**, selecteer **Deblokkering opheffen**en selecteer **OK**. Hierdoor kan uw systeem de installateur vertrouwen.
   - Voer het installatieprogramma uit. Wanneer de installateur u om het registratietoken vraagt, voert u de waarde in die u hebt gekregen van de cmdlet **Export-RdsRegistrationInfo.**
3. Download en installeer de Windows Virtual Desktop Agent Bootloader.
   - Download de [Bootloader voor virtuele bureaubladagent van Windows.](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH)
   - Klik met de rechtermuisknop op het gedownloade installatieprogramma, selecteer **Eigenschappen**, selecteer **Deblokkering opheffen**en selecteer **OK**. Hierdoor kan uw systeem de installateur vertrouwen.
   - Voer het installatieprogramma uit.

>[!IMPORTANT]
>Om uw Windows Virtual Desktop-omgeving in Azure te beveiligen, raden we u aan de binnenkomende poort 3389 niet op uw VM's te openen. Voor Windows Virtual Desktop is geen open inkomende poort 3389 vereist voor gebruikers om toegang te krijgen tot de VM's van de hostgroep. Als u poort 3389 moet openen voor probleemoplossingsdoeleinden, raden we u aan [just-in-time VM-toegang te](../security-center/security-center-just-in-time.md)gebruiken.

## <a name="next-steps"></a>Volgende stappen

Nu u een hostpool hebt gemaakt, u deze vullen met RemoteApps. Zie de zelfstudie App-groepen beheren voor meer informatie over het beheren van apps in Windows Virtual Desktop.

> [!div class="nextstepaction"]
> [Zelfstudie voor app-groepen beheren](./manage-app-groups.md)
