---
title: Problemen met de activering van virtuele Windows-machines in azure oplossen | Microsoft Docs
description: Biedt de probleemoplossings stappen voor het oplossen van problemen met de activering van Windows-virtuele machines in azure
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 11/15/2018
ms.author: genli
ms.openlocfilehash: f3ad58c4094e9f39bcf9782b7b98e351e9d7809b
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058136"
---
# <a name="troubleshoot-azure-windows-virtual-machine-activation-problems"></a>Problemen met activering van Azure Windows-virtuele machines oplossen

Als u problemen ondervindt bij het activeren van Azure Windows Virtual Machine (VM) die is gemaakt op basis van een aangepaste installatie kopie, kunt u de informatie in dit document gebruiken om het probleem op te lossen. 

## <a name="understanding-azure-kms-endpoints-for-windows-product-activation-of-azure-virtual-machines"></a>Informatie over Azure KMS-eind punten voor Windows-product activering van Azure Virtual Machines

Azure gebruikt verschillende eind punten voor KMS-activering, afhankelijk van de regio van de Cloud waar de virtuele machine zich bevindt. Wanneer u deze hand leiding voor probleem oplossing gebruikt, gebruikt u het juiste KMS-eind punt dat van toepassing is op uw regio.

* Open bare Cloud regio's van Azure: kms.core.windows.net:1688
* Azure China 21Vianet nationale Cloud regio's: kms.core.chinacloudapi.cn:1688
* Azure Duitsland National Cloud regio's: kms.core.cloudapi.de:1688
* Azure US Gov nationale Cloud regio's: kms.core.usgovcloudapi.net:1688

## <a name="symptom"></a>Symptoom

Wanneer u probeert een Azure Windows-VM te activeren, wordt een fout bericht weer gegeven dat lijkt op het volgende voor beeld:

**Fout: 0xC004F074 de software-LicensingService heeft gerapporteerd dat de computer niet kan worden geactiveerd. Er kan geen verbinding worden gemaakt met Key ManagementService (KMS). Raadpleeg het gebeurtenis logboek van de toepassing voor meer informatie.**

## <a name="cause"></a>Oorzaak

Over het algemeen worden er problemen met de activering van Azure VM gegenereerd als de Windows-VM niet is geconfigureerd met behulp van de juiste installatie sleutel voor de KMS-client, of omdat de Windows-VM een connectiviteits probleem heeft met de Azure KMS-service (kms.core.windows.net, poort 1688). 

## <a name="solution"></a>Oplossing

>[!NOTE]
>Als u een site-naar-site-VPN en geforceerde tunneling gebruikt, raadpleegt u [aangepaste Azure-routes gebruiken om de KMS-activering met geforceerde Tunneling in te scha kelen](https://blogs.msdn.com/b/mast/archive/2015/05/20/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling.aspx). 
>
>Als u ExpressRoute gebruikt en u een standaard route hebt gepubliceerd, gaat u naar [Azure VM kan niet worden geactiveerd via ExpressRoute](https://blogs.msdn.com/b/mast/archive/2015/12/01/azure-vm-may-fail-to-activate-over-expressroute.aspx).

### <a name="step-1-configure-the-appropriate-kms-client-setup-key"></a>Stap 1 de juiste installatie sleutel voor de KMS-client configureren

Voor de virtuele machine die is gemaakt op basis van een aangepaste installatie kopie, moet u de juiste installatie sleutel voor de KMS-client configureren voor de virtuele machine.

1. Voer **slmgr. vbs/dlv** uit vanaf een opdracht prompt met verhoogde bevoegdheden. Controleer de beschrijvings waarde in de uitvoer en bepaal vervolgens of deze is gemaakt op basis van Retail Channel (DETAILHANDELKANAAL) of volume (VOLUME_KMSCLIENT)-licentie media:
  

    ```
    cscript c:\windows\system32\slmgr.vbs /dlv
    ```

2. Als in **slmgr.vbs /dlv** het kanaal RETAIL wordt weergegeven, voert u de volgende opdrachten uit om de [installatiecode voor de KMS-client](https://technet.microsoft.com/library/jj612867%28v=ws.11%29.aspx?f=255&MSPPError=-2147217396) in te stellen voor de gebruikte versie van Windows Server, en dwingt u af dat de activering opnieuw wordt uitgevoerd: 

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk <KMS client setup key>

    cscript c:\windows\system32\slmgr.vbs /ato
     ```

    Bijvoorbeeld, voor Windows Server 2016 Data Center, voert u de volgende opdracht uit:

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk CB7KF-BWN84-R7R2Y-793K2-8XDDG
    ```

### <a name="step-2-verify-the-connectivity-between-the-vm-and-azure-kms-service"></a>Stap 2 de connectiviteit tussen de virtuele machine en de Azure KMS-service controleren

1. Down load en pak het [PSping](http:/technet.microsoft.com/sysinternals/jj729731.aspx) -hulp programma uit naar een lokale map in de virtuele machine die niet wordt geactiveerd. 

2. Ga naar Start, zoek op Windows Power shell, klik met de rechter muisknop op Windows Power shell en selecteer vervolgens als administrator uitvoeren.

3. Zorg ervoor dat de VM is geconfigureerd om de juiste Azure KMS-server te gebruiken. U doet dit door de volgende opdracht uit te voeren:
  
    ```powershell
    Invoke-Expression "$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /skms kms.core.windows.net:1688"
    ```

    Met deze opdracht wordt dit geretourneerd: De naam van de Key Management service-computer is ingesteld op kms.core.windows.net:1688.

4. Controleer met behulp van Psping dat u verbinding hebt met de KMS-server. Ga naar de map waarin u het gedownloade bestand Pstools.zip hebt uitgepakt en voer vervolgens het volgende uit:
  
    ```
    \psping.exe kms.core.windows.net:1688
    ```
   Controleer of u in de een-na-laatste regel van de uitvoer het volgende ziet: Verzonden = 4, ontvangen = 4, verloren = 0 (0% verlies).

   Als verloren is groter dan 0 (nul), is de virtuele machine niet verbonden met de KMS-server. Als de virtuele machine zich in een virtueel netwerk bevindt en een aangepaste DNS-server is opgegeven, moet u ervoor zorgen dat de DNS-server kms.core.windows.net kan omzetten. U kunt ook de DNS-server wijzigen in een die kms.core.windows.net omzetten.

   Als u alle DNS-servers uit een virtueel netwerk verwijdert, gebruiken Vm's de interne DNS-service van Azure. Met deze service kunt u kms.core.windows.net omzetten.
  
    Zorg er ook voor dat het uitgaande netwerk verkeer naar het KMS-eind punt met de 1688-poort niet wordt geblokkeerd door de firewall in de virtuele machine.

5. Nadat u de connectiviteit met kms.core.windows.net hebt gecontroleerd, voert u de volgende opdracht uit op die verhoogde Windows Power shell-prompt. Met deze opdracht wordt meerdere keren geprobeerd te activeren.

    ```powershell
    1..12 | ForEach-Object { Invoke-Expression "$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /ato" ; start-sleep 5 }
    ```

    Na een geslaagde activering worden gegevens geretourneerd die er ongeveer als volgt uitzien:
    
    **Windows (R), ServerDatacenter Edition (12345678-1234-1234-1234-12345678) activeren...  Het product is geactiveerd.**

## <a name="faq"></a>Veelgestelde vragen 

### <a name="i-created-the-windows-server-2016-from-azure-marketplace-do-i-need-to-configure-kms-key-for-activating-the-windows-server-2016"></a>Ik heb Windows Server 2016 van Azure Marketplace gemaakt. Moet ik de KMS-sleutel voor het activeren van de Windows Server 2016 configureren? 

 
Nee. Voor de installatie kopie in azure Marketplace is de juiste installatie sleutel voor de KMS-client al geconfigureerd. 

### <a name="does-windows-activation-work-the-same-way-regardless-if-the-vm-is-using-azure-hybrid-use-benefit-hub-or-not"></a>Werkt Windows activeren op dezelfde manier, ongeacht of de virtuele machine gebruikmaakt van Azure Hybrid Use Benefit (HUB) of niet? 

 
Ja. 
 

### <a name="what-happens-if-windows-activation-period-expires"></a>Wat gebeurt er als de Windows-activerings periode verloopt? 

 
Wanneer de respijt periode is verlopen en Windows nog steeds niet wordt geactiveerd, worden in Windows Server 2008 R2 en latere versies van Windows aanvullende meldingen over het activeren weer gegeven. De achtergrond van het bureau blad blijft zwart en Windows Update installeert alleen beveiligings-en essentiële updates, maar geen optionele updates. Zie de sectie meldingen onder aan de pagina [licentie voorwaarden](https://technet.microsoft.com/library/ff793403.aspx) .   

## <a name="need-help-contact-support"></a>Hulp nodig? Neem contact op met ondersteuning.

Als u nog steeds hulp nodig hebt, neemt u [contact op met de ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om uw probleem snel op te lossen.
