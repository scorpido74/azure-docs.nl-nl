---
title: Een scale-outprocesserver instellen tijdens het herstel van VMware-vm's en fysieke servers met Azure Site Recovery | Microsoft Docs'
description: In dit artikel wordt beschreven hoe u scale-out processerver instelt tijdens noodherstel van VMware VM's en fysieke servers.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/23/2019
ms.author: ramamill
ms.openlocfilehash: 1b6084b4e93f3dc17f633f1b8496f9c26e7f576f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257145"
---
# <a name="scale-with-additional-process-servers"></a>Schalen met extra processervers

Wanneer u vmware-vm's of fysieke servers naar Azure repliceert met [siteherstel,](site-recovery-overview.md)wordt er standaard een processerver geïnstalleerd op de configuratieservermachine en wordt deze gebruikt om de gegevensoverdracht tussen Siterecovery en uw on-premises infrastructuur te coördineren. Als u de capaciteit wilt vergroten en uw replicatie-implementatie wilt uitschalen, u extra zelfstandige processervers toevoegen. In dit artikel wordt beschreven hoe u een scale-outprocesserver in- en uitzetten.

## <a name="before-you-start"></a>Voordat u begint

### <a name="capacity-planning"></a>Capaciteitsplanning

Zorg ervoor dat u [capaciteitsplanning](site-recovery-plan-capacity-vmware.md) voor VMware-replicatie hebt uitgevoerd. Dit helpt u om te bepalen hoe en wanneer u extra processervers moet implementeren.

Vanaf 9.24 versie, wordt de begeleiding toegevoegd tijdens selectie van processerver voor nieuwe replicaties. Processerver wordt gemarkeerd als Gezond, Waarschuwing en Kritiek op basis van bepaalde criteria. Als u inzicht wilt krijgen in verschillende scenario's die van invloed kunnen zijn op de status van de processerver, controleert u de waarschuwingen van de [processerver.](vmware-physical-azure-monitor-process-server.md#process-server-alerts)

> [!NOTE]
> Het gebruik van een gekloonde processservercomponent wordt niet ondersteund. Volg de stappen in dit artikel voor elke PS-scale-out.

### <a name="sizing-requirements"></a>Maatseisen 

Controleer de vereisten voor grootte die in de tabel zijn samengevat. Als u uw implementatie over het algemeen moet schalen naar meer dan 200 bronmachines of als u een totale dagelijkse churn rate van meer dan 2 TB hebt, hebt u extra processervers nodig om het verkeersvolume te verwerken.

| **Extra processerver** | **Schijfgrootte cache** | **Gegevenswijzigingssnelheid** | **Beveiligde machines** |
| --- | --- | --- | --- |
|4 vCPU's (2 sockets * 2 cores \@ 2,5 GHz), 8 GB geheugen |300 GB |250 GB of minder |Repliceren 85 of minder machines. |
|8 vCPU's (2 sockets * 4 cores \@ 2,5 GHz), 12 GB geheugen |600 GB |250 GB tot 1 TB |Repliceren tussen 85-150 machines. |
|12 vCPU's (2 sockets * 6 cores \@ 2,5 GHz) 24 GB geheugen |1 TB |1 TB tot 2 TB |Repliceren tussen 150-225 machines. |

Wanneer elke beveiligde bronmachine is geconfigureerd met 3 schijven van elk 100 GB.

### <a name="prerequisites"></a>Vereisten

De vereisten voor de extra processerver worden samengevat in de volgende tabel.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="download-installation-file"></a>Installatiebestand downloaden

Download het installatiebestand voor de processerver als volgt:

1. Meld u aan bij de Azure-portal en blader naar uw Vault voor Herstelservices.
2. Open **Site Recovery Infrastructure** > **VMWare en Physical Machines** > **Configuration Servers** (onder Voor VMware & Fysieke Machines).
3. Selecteer de configuratieserver die u wilt inzoomen op de servergegevens. Klik vervolgens op **+ Processerver**.
4. Selecteer in **Processerver** >  toevoegen**Kiezen waar u uw processerver wilt implementeren,** selecteer **On-premises een scale-out processerver implementeren.**

   ![Pagina Servers toevoegen](./media/vmware-azure-set-up-process-server-scale/add-process-server.png)
1. Klik **op De Microsoft Azure Site Recovery Unified Setup downloaden**. Hiermee wordt de nieuwste versie van het installatiebestand gedownload.

   > [!WARNING]
   > De installatieversie van de processerver moet hetzelfde zijn als of eerder dan de configuratieserverversie die u hebt uitgevoerd. Een eenvoudige manier om ervoor te zorgen dat versiecompatibiliteit is het gebruik van hetzelfde installatieprogramma, dat u onlangs hebt gebruikt om uw configuratieserver te installeren of bij te werken.

## <a name="install-from-the-ui"></a>Installeren vanaf de gebruikersinterface

Installeer als volgt. Nadat u de server hebt ingesteld, migreert u bronmachines om deze te gebruiken.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-add-process-server.md)]


## <a name="install-from-the-command-line"></a>Installeren vanaf de opdrachtregel

Installeren door de volgende opdracht uit te voeren:

```
UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
```

Waar de parameters van de opdrachtregel als volgt zijn:

[!INCLUDE [site-recovery-unified-setup-parameters](../../includes/site-recovery-unified-installer-command-parameters.md)]

Bijvoorbeeld:

```
MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted
cd C:\Temp\Extracted
UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "PS" /InstallLocation "D:\" /EnvType "VMWare" /CSIP "10.150.24.119" /PassphraseFilePath "C:\Users\Administrator\Desktop\Passphrase.txt" /DataTransferSecurePort 443
```
### <a name="create-a-proxy-settings-file"></a>Een bestand met proxy-instellingen maken

Als u een proxy moet instellen, wordt een bestand als invoer gebruikt door de parameter ProxySettingsFilePath. U het bestand als volgt maken en als parameter ProxySettingsFilePath van invoer doorgeven.

```
* [ProxySettings]
* ProxyAuthentication = "Yes/No"
* Proxy IP = "IP Address"
* ProxyPort = "Port"
* ProxyUserName="UserName"
* ProxyPassword="Password"
```

## <a name="next-steps"></a>Volgende stappen
Meer informatie over [het beheren van processerverinstellingen](vmware-azure-manage-process-server.md)
