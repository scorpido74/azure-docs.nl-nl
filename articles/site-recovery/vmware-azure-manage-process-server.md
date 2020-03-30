---
title: Een processerver voor VMware VM's/fysieke servernoodherstel beheren in Azure Site Recovery
description: In dit artikel wordt beschreven dat u een processerver beheert voor noodherstel van VMware VM's/fysieke servers met Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: ef16e3b75ca8e051b1b7abb1a92843279884c697
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257249"
---
# <a name="manage-process-servers"></a>Processervers beheren

In dit artikel worden veelvoorkomende taken beschreven voor het beheren van de server voor het siteherstelproces.

De processerver wordt gebruikt om replicatiegegevens te ontvangen, optimaliseren en verzenden naar Azure. Het voert ook een push-installatie van de Mobility-service uit op VMware VM's en fysieke servers die u wilt repliceren, en voert automatische detectie uit van on-premises machines. Voor het repliceren van on-premises Vm's vMware of fysieke servers naar Azure, wordt de processerver standaard geïnstalleerd op de configuratieservermachine. 

- Voor grote implementaties hebt u mogelijk extra on-premises processervers nodig om de capaciteit te schalen.
- Voor failback van Azure naar on-premises moet u een tijdelijke processerver instellen in Azure. U deze vm verwijderen wanneer failback is gedaan. 

Meer informatie over de processerver.


## <a name="upgrade-a-process-server"></a>Een processerver upgraden

Wanneer u een on-premises processerver implementeert of als Een Azure VM voor failback, wordt de nieuwste versie van de processerver geïnstalleerd. De siteherstelteams geven regelmatig fixes en verbeteringen vrij en we raden u aan processervers up-to-date te houden. U een processerver als volgt upgraden:

[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]


## <a name="move-vms-to-balance-the-process-server-load"></a>VM's verplaatsen om de belasting van de processerver in evenwicht te brengen

Breng de belasting in evenwicht door VM's tussen twee processervers te verplaatsen, als volgt:

1. Klik in de kluis onder **Beheren** op **Siteherstelinfrastructuur**. Klik **onder Voor VMware & Fysieke machines**op **Configuratieservers**.
2. Klik op de configuratieserver waarmee de processervers zijn geregistreerd.
3. Klik op de processerver waarvoor u balansverkeer wilt laden.

    ![LoadBalance](media/vmware-azure-manage-process-server/LoadBalance.png)

4. Klik **op Laadbalans**en selecteer de doelprocesserver waarnaar u machines wilt verplaatsen. Klik vervolgens op **OK**

    ![LoadPS (LoadPS)](media/vmware-azure-manage-process-server/LoadPS.PNG)

2. Klik **op Machines selecteren**en kies de machines die u van de huidige naar de doelprocesserver wilt verplaatsen. Details van de gemiddelde gegevenswijziging worden weergegeven ten opzichte van elke virtuele machine. Klik vervolgens op **OK**. 
3. Houd in de kluis de voortgang van de taak in de gaten onder **Taken voor siteherstel** > **controleren**.

Het duurt ongeveer 15 minuten voordat wijzigingen in het portaal tot uiting komen. [Vernieuw de configuratieserver voor](vmware-azure-manage-configuration-server.md#refresh-configuration-server)een sneller effect.

## <a name="switch-an-entire-workload-to-another-process-server"></a>Een volledige werkbelasting overschakelen naar een andere processerver

Verplaats de volledige werkbelasting die door een processerver wordt afgehandeld, als volgt naar een andere processerver:

1. Klik in de kluis onder **Beheren** op **Siteherstelinfrastructuur**. Klik **onder Voor VMware & Fysieke machines**op **Configuratieservers**.
2. Klik op de configuratieserver waarmee de processervers zijn geregistreerd.
3. Klik op de processerver van waaruit u de werkbelasting wilt schakelen.
4. Klik op **Switch,** selecteer de doelprocesserver waarnaar u de werkbelasting wilt verplaatsen. Klik vervolgens op **OK**

    ![Switch](media/vmware-azure-manage-process-server/Switch.PNG)

5. Houd in de kluis de voortgang van de taak in de gaten onder **Taken voor siteherstel** > **controleren**.

Het duurt ongeveer 15 minuten voordat wijzigingen in het portaal tot uiting komen. [Vernieuw de configuratieserver voor](vmware-azure-manage-configuration-server.md#refresh-configuration-server)een sneller effect.

## <a name="register-a-master-target-server"></a>Een hoofddoelserver registreren

Hoofddoelserver bevindt zich op configuratieserver en scale-out processervers. Het moet worden geregistreerd bij de configuratieserver. In het geval dat er een fout in deze registratie, kan het van invloed zijn op de gezondheid van beschermde items. Als u hoofddoelserver wilt registreren met de configuratieserver, logt u in bij de specifieke configuratieserver/scale-outprocesserver waarop de registratie vereist is. Navigeer naar map **%PROGRAMDATA%\ASR\Agent**en voer het volgende uit op opdrachtprompt van de beheerder.

   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="reregister-a-process-server"></a>Een processerver opnieuw registreren

Registreer een processerver die on-premises of op een Azure VM wordt uitgevoerd, opnieuw als volgt:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

Ga als volgt te werk nadat u de instellingen hebt opgeslagen:

1. Open op de processerver een opdrachtprompt voor beheerders.
2. Blader naar map **%PROGRAMDATA%\ASR\Agent**en voer de opdracht uit:

    ```
    cdpcli.exe --registermt
    net stop obengine
    net start obengine
    ```

## <a name="modify-proxy-settings-for-an-on-premises-process-server"></a>Proxy-instellingen voor een on-premises processerver wijzigen

Als een on-premises processerver een proxy gebruikt om verbinding te maken met Azure, u de proxy-instellingen als volgt wijzigen:

1. Meld u aan bij de processervermachine. 
2. Open een opdrachtvenster voor beheer PowerShell en voer de volgende opdracht uit:
   ```powershell
   $pwd = ConvertTo-SecureString -String MyProxyUserPassword
   Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
   net stop obengine
   net start obengine
   ```
2. Blader naar map **%PROGRAMDATA%\ASR\Agent**en voer deze opdracht uit:
   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="remove-a-process-server"></a>Een processerver verwijderen

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

## <a name="exclude-folders-from-anti-virus-software"></a>Mappen uitsluiten van antivirussoftware

Als antivirussoftware wordt uitgevoerd op een scale-outprocesserver (of hoofddoelserver), sluit u de volgende mappen uit van antivirusbewerkingen:


- C:\Program Files\Microsoft Azure Recovery Services Agent
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- De installatiemap van de server verwerken. Bijvoorbeeld: C:\Program Files (x86)\Microsoft Azure Site Recovery