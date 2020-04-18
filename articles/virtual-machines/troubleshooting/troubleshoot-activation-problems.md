---
title: Problemen met activering van virtuele machines van Windows in Azure oplossen| Microsoft Documenten
description: Biedt de probleemoplossingsstappen voor het oplossen van problemen met de activering van virtuele machines van Windows in Azure
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
ms.openlocfilehash: fd38f646b8dfc58839cd2645f7fadf7332693854
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605992"
---
# <a name="troubleshoot-azure-windows-virtual-machine-activation-problems"></a>Problemen met de activering van virtuele Microsoft Azure-machines oplossen

Als u problemen ondervindt bij het activeren van de virtuele machine (VM) van Azure Windows die is gemaakt op basis van een aangepaste afbeelding, u de in dit document verstrekte informatie gebruiken om het probleem op te lossen. 

## <a name="understanding-azure-kms-endpoints-for-windows-product-activation-of-azure-virtual-machines"></a>Algemene informatie over Azure KMS-eindpunten voor Windows-productactivering van Azure Virtual Machines

Azure gebruikt verschillende eindpunten voor KMS-activering (Key Management Services), afhankelijk van het cloudgebied waar de VM zich bevindt. Gebruik bij het gebruik van deze handleiding voor probleemoplossing het juiste KMS-eindpunt dat van toepassing is op uw regio.

* Azure public cloud-regio's: kms.core.windows.net:1688
* Azure China 21Vianet nationale cloudregio's: kms.core.chinacloudapi.cn:1688
* Nationale cloudregio's van Azure Germany: kms.core.cloudapi.de:1688
* Nationale cloudregio's van Azure US Gov: kms.core.usgovcloudapi.net:1688

## <a name="symptom"></a>Symptoom

Wanneer u een Azure Windows VM probeert te activeren, ontvangt u een foutbericht dat lijkt op het volgende voorbeeld:

**Fout: 0xC004F074 De Software LicensingService meldde dat de computer niet kon worden geactiveerd. Er kan geen contact worden opgenomen met Key ManagementService (KMS). Zie het logboek van het aanvraaggebeurtenislogboek voor meer informatie.**

## <a name="cause"></a>Oorzaak

Over het algemeen treden er activeringsproblemen met Azure-VM's op als de Windows-VM niet is geconfigureerd met de juiste installatiecode voor de KMS-client, of als de Windows-VM een probleem heeft met de connectiviteit met de Azure KMS-service (kms.core.windows.net, port 1688). 

## <a name="solution"></a>Oplossing

>[!NOTE]
>Als u een site-to-site VPN gebruikt en gedwongen tunneling, [raadpleegt u Azure aangepaste routes gebruiken om KMS-activering in te schakelen met gedwongen tunneling.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-forced-tunneling) 
>
>Als u ExpressRoute gebruikt en u een standaardroute hebt gepubliceerd, [raadpleegt u Kan ik de internetverbinding met virtuele netwerken die zijn aangesloten op ExpressRoute-circuits blokkeren?](https://docs.microsoft.com/azure/expressroute/expressroute-faqs).

### <a name="step-1-configure-the-appropriate-kms-client-setup-key"></a>Stap 1 De juiste KMS-clientinstellingssleutel configureren

Voor de VM die is gemaakt op basis van een aangepaste afbeelding, moet u de juiste KMS-clientinstellingssleutel voor de VM configureren.

1. Voer **slmgr.vbs /dlv** uit op een verhoogde opdrachtprompt. Controleer de waarde Beschrijving in de uitvoer en bepaal of deze is gemaakt op uit retailkanaal (RETAIL-kanaal) of volume (VOLUME_KMSCLIENT) licentiemedia:
  

    ```
    cscript c:\windows\system32\slmgr.vbs /dlv
    ```

2. Als in **slmgr.vbs /dlv** het kanaal RETAIL wordt weergegeven, voert u de volgende opdrachten uit om de [installatiecode voor de KMS-client](https://technet.microsoft.com/library/jj612867%28v=ws.11%29.aspx?f=255&MSPPError=-2147217396) in te stellen voor de gebruikte versie van Windows Server, en dwingt u af dat de activering opnieuw wordt uitgevoerd: 

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk <KMS client setup key>

    cscript c:\windows\system32\slmgr.vbs /ato
     ```

    Voor Windows Server 2016 Datacenter voert u bijvoorbeeld de volgende opdracht uit:

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk CB7KF-BWN84-R7R2Y-793K2-8XDDG
    ```

### <a name="step-2-verify-the-connectivity-between-the-vm-and-azure-kms-service"></a>Stap 2 De connectiviteit tussen de VM- en Azure KMS-service verifiëren

1. Download en haal het [gereedschap PSping](https://docs.microsoft.com/sysinternals/downloads/psping) naar een lokale map in de VM die niet wordt geactiveerd. 

2. Ga naar Start, zoek op Windows PowerShell, klik met de rechtermuisknop op Windows PowerShell en selecteer Uitvoeren als beheerder.

3. Zorg ervoor dat de VM is geconfigureerd om de juiste Azure KMS-server te gebruiken. Voer hiervoor de volgende opdracht uit:
  
    ```powershell
    Invoke-Expression "$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /skms kms.core.windows.net:1688"
    ```

    De opdracht moet terugkeren: De naam van de Key Management Service-machine is ingesteld op kms.core.windows.net:1688 met succes.

4. Controleer met behulp van Psping of er connectiviteit is met de KMS-server. Ga naar de map waarin u het gedownloade bestand Pstools.zip hebt uitgepakt en voer vervolgens het volgende uit:
  
    ```
    \psping.exe kms.core.windows.net:1688
    ```
   Zorg er in de een-na-laatste regel van de uitvoer voor dat u ziet: Verzonden = 4, Ontvangen = 4, Verloren = 0 (0% verlies).

   Als verloren is groter dan 0 (nul), heeft de VM geen verbinding met de KMS-server. Als de VM zich in een virtueel netwerk bevindt en een aangepaste DNS-server heeft opgegeven, moet u ervoor zorgen dat de DNS-server kms.core.windows.net kan oplossen. Of wijzig de DNS-server in een server die kms.core.windows.net wel oplost.

   Als u alle DNS-servers uit een virtueel netwerk verwijdert, gebruiken VM's de interne DNS-service van Azure. Deze service kan kms.core.windows.net oplossen.
  
    Zorg er ook voor dat het uitgaande netwerkverkeer naar KMS-eindpunt met 1688-poort niet wordt geblokkeerd door de firewall in de VM.

5. Controleer met [Network Watcher Next Hop](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) of het volgende hoptype van de betrokken vm naar het doel-IP 23.102.135.246 (voor kms.core.windows.net) of het IP van het juiste KMS-eindpunt dat van toepassing is op uw regio **internet**is.  Als het resultaat VirtualAppliance of VirtualNetworkGateway is, is het waarschijnlijk dat er een standaardroute bestaat.  Neem contact op met uw netwerkbeheerder en werk met hen samen om de juiste manier van handelen te bepalen.  Dit kan een [aangepaste route](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/custom-routes-enable-kms-activation) zijn als die oplossing in overeenstemming is met het beleid van uw organisatie.

6. Nadat u hebt geverifieerd dat de connectiviteit met kms.core.windows.net is geslaagd, voert u de volgende opdracht uit wanneer een Windows PowerShell-opdrachtprompt met verhoogde bevoegdheid wordt weergegeven. Met deze opdracht wordt meerdere keren geprobeerd te activeren.

    ```powershell
    1..12 | ForEach-Object { Invoke-Expression "$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /ato" ; start-sleep 5 }
    ```

    Na een geslaagde activering worden gegevens geretourneerd die er ongeveer als volgt uitzien:
    
    **Windows(R), ServerDatacenter-editie activeren (12345678-1234-1234-1234-12345678) ...  Product geactiveerd.**

## <a name="faq"></a>Veelgestelde vragen 

### <a name="i-created-the-windows-server-2016-from-azure-marketplace-do-i-need-to-configure-kms-key-for-activating-the-windows-server-2016"></a>Ik heb de Windows Server 2016 gemaakt vanuit Azure Marketplace. Moet ik de KMS-toets configureren voor het activeren van de Windows Server 2016? 

 
Nee. De afbeelding in Azure Marketplace heeft de juiste KMS-clientinstellingssleutel die al is geconfigureerd. 

### <a name="does-windows-activation-work-the-same-way-regardless-if-the-vm-is-using-azure-hybrid-use-benefit-hub-or-not"></a>Werkt windows-activering op dezelfde manier, ongeacht of de VM Azure Hybrid Use Benefit (HUB) gebruikt of niet? 

 
Ja. 
 

### <a name="what-happens-if-windows-activation-period-expires"></a>Wat gebeurt er als de activeringsperiode van Windows afloopt? 

 
Wanneer de respijtperiode is verstreken en Windows nog steeds niet is geactiveerd, worden in Windows Server 2008 R2 en latere versies van Windows extra meldingen weergegeven over activeren. De bureaubladachtergrond blijft zwart en Windows Update installeert alleen beveiligings- en kritieke updates, maar geen optionele updates. Zie de sectie Meldingen onder aan de pagina [Licentievoorwaarden.](https://technet.microsoft.com/library/ff793403.aspx)   

## <a name="need-help-contact-support"></a>Hebt u hulp nodig? Neem contact op met ondersteuning.

Als u nog steeds hulp nodig hebt, neemt u [contact op met de ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om uw probleem snel op te lossen.
