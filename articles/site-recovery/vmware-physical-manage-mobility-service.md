---
title: De mobiliteitsagent voor VMware/fysieke servers beheren met Azure Site Recovery
description: De medewerker Mobility Service beheren voor noodherstel van Vm's en fysieke servers naar Azure met behulp van de Azure Site Recovery-service.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: 9be758c286e072b0fbefc5f8b20b7accc4e6741b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256963"
---
# <a name="manage-the-mobility-agent"></a>De Mobility-agent beheren 

U stelt een mobiliteitsagent in op uw server wanneer u Azure Site Recovery gebruikt voor noodherstel van VMware VM's en fysieke servers naar Azure. De mobiliteitsagent coördineert de communicatie tussen uw beveiligde machine, configuratieserver/scale-outprocesserver en beheert gegevensreplicatie. In dit artikel worden veelvoorkomende taken voor het beheren van een mobiliteitsagent samengevat nadat deze is geïmplementeerd.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="update-mobility-service-from-azure-portal"></a>Mobiliteitsservice bijwerken vanuit Azure-portal

1. Voordat u begint, moet u ervoor zorgen dat de configuratieserver, scale-outprocesservers en alle hoofddoelservers die deel uitmaken van uw implementatie, worden bijgewerkt voordat u de Mobiliteitsservice op beveiligde machines bijwerkt.
2. Open in de portal de kluis > **gerepliceerde items**.
3. Als de configuratieserver de nieuwste versie is, ziet u een melding met de tekst 'Update van de replicatiesagent van de nieuwe site-replicatie is beschikbaar. Klik om te installeren."

     ![Venster Gerepliceerde items](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)

4. Klik op de melding en selecteer in **agentupdate**de machines waarop u de Mobiliteitsservice wilt upgraden. Klik vervolgens op **OK**.

     ![VM-lijst met gerepliceerde items](./media/vmware-azure-install-mobility-service/update-okpng.png)

5. De taak Update Mobility Service wordt gestart voor elk van de geselecteerde machines.

## <a name="update-mobility-service-through-powershell-script-on-windows-server"></a>Mobiliteitsservice bijwerken via powershell-script op Windows-server

Voordat u begint, moet u ervoor zorgen dat de configuratieserver, scale-outprocesservers en alle hoofddoelservers die deel uitmaken van uw implementatie, worden bijgewerkt voordat u de Mobiliteitsservice op beveiligde machines bijwerkt.

Het volgende script gebruiken om de mobiliteitsservice op een server te upgraden via de cmdlet van de powershell

```azurepowershell
Update-AzRecoveryServicesAsrMobilityService -ReplicationProtectedItem $rpi -Account $fabric.fabricSpecificDetails.RunAsAccounts[0]
```

## <a name="update-mobility-service-manually-on-each-protected-server"></a>Mobiliteitsservice handmatig bijwerken op elke beveiligde server

1. Voordat u begint, moet u ervoor zorgen dat de configuratieserver, scale-outprocesservers en alle hoofddoelservers die deel uitmaken van uw implementatie, worden bijgewerkt voordat u de Mobiliteitsservice op beveiligde machines bijwerkt.

2. [Zoek het installatieprogramma](vmware-physical-mobility-service-overview.md#locate-installer-files) van de agent op basis van het besturingssysteem van de server.

>[!IMPORTANT]
> Als u Azure IaaS VM van de ene Azure-regio naar de andere repliceert, gebruikt u deze methode niet. Raadpleeg [onze richtlijnen](azure-to-azure-autoupdate.md) voor informatie over alle beschikbare opties.

3. Kopieer het installatiebestand naar de beveiligde machine en voer het uit om de mobiliteitsagent bij te werken.

## <a name="update-account-used-for-push-installation-of-mobility-service"></a>Update-account dat wordt gebruikt voor push-installatie van Mobility-service

Wanneer u Site recovery hebt geïmplementeerd om de push-installatie van de Mobiliteitsservice in te schakelen, hebt u een account opgegeven dat de siteherstelprocesserver gebruikt om toegang te krijgen tot de machines en de service te installeren wanneer replicatie is ingeschakeld voor de machine. Als u de referenties voor dit account wilt bijwerken, volgt u [deze instructies](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="uninstall-mobility-service"></a>Mobility-service verwijderen

### <a name="on-a-windows-machine"></a>Op een Windows-machine

Verwijder van de gebruikersinterface of van een opdrachtprompt.

- **In de gebruikersinterface**: Selecteer in het Configuratiescherm van de machine **Programma's**. Selecteer **Microsoft Azure Site Recovery Mobility Service/Master Target-server** > **Verwijderen**.
- **Vanuit een opdrachtprompt**: Open een opdrachtpromptvenster als beheerder op de machine. Voer de volgende opdracht uit: 
    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

### <a name="on-a-linux-machine"></a>Op een Linux machine
1. Meld u op de Linux-machine aan als **rootgebruiker.**
2. Ga in een terminal naar /usr/local/ASR.
3. Voer de volgende opdracht uit:
    ```
    uninstall.sh -Y
   ```
   
## <a name="install-site-recovery-vss-provider-on-source-machine"></a>Site Recovery VSS-provider installeren op de bronmachine

Azure Site Recovery VSS-provider is vereist op de bronmachine om consistentiepunten voor toepassingen te genereren. Als de installatie van de provider niet is geslaagd door middel van push-installatie, volg dan de onderstaande richtlijnen om het handmatig te installeren.

1. Open het cmd-venster voor beheerders.
2. Navigeer naar de installatielocatie van de mobiliteitsservice. (Bijvoorbeeld - C:\Program Files (x86)\Microsoft Azure Site Recovery\agent)
3. Voer het script InMageVSSProvider_Uninstall.cmd uit. Hierdoor wordt de service verwijderen als deze al bestaat.
4. Voer het script InMageVSSProvider_Install.cmd uit om de VSS-provider handmatig te installeren.

## <a name="next-steps"></a>Volgende stappen

- [Noodherstel instellen voor VMware VM's](vmware-azure-tutorial.md)
- [Noodherstel instellen voor fysieke servers](physical-azure-disaster-recovery.md)
