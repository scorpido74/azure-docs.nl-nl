---
title: Problemen met de azure site recovery-processerver oplossen
description: In dit artikel wordt beschreven hoe u problemen met de Azure Site Recovery-processerver oplossen
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: 812cd0293f9627b7438e9870d8985e71dae1d147
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256859"
---
# <a name="troubleshoot-the-process-server"></a>Problemen met de processerver oplossen

De [siteherstelprocesserver](site-recovery-overview.md) wordt gebruikt wanneer u disaster recovery instelt op Azure voor on-premises Vm's vmware en fysieke servers. In dit artikel wordt beschreven hoe u problemen met de processerver oplossen, waaronder replicatie- en verbindingsproblemen.

[Meer informatie](vmware-physical-azure-config-process-server-overview.md) over de processerver.

## <a name="before-you-start"></a>Voordat u begint

Voordat u begint met het oplossen van problemen:

1. Zorg ervoor dat u begrijpt hoe [u processervers moet controleren.](vmware-physical-azure-monitor-process-server.md)
2. Bekijk hieronder de aanbevolen procedures.
3. Zorg ervoor dat u [capaciteitsoverwegingen](site-recovery-plan-capacity-vmware.md#capacity-considerations)volgt en gebruik de grootterichtlijnen voor de [configuratieserver](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server) of [zelfstandige processervers.](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server)

## <a name="best-practices-for-process-server-deployment"></a>Aanbevolen procedures voor het implementeren van processervers

Voor optimale prestaties van processervers hebben we een aantal algemene best practices samengevat.

**Aanbevolen procedures** | **Details**
--- |---
**Gebruik** | Zorg ervoor dat de configuratieserver/standalone processerver alleen voor het beoogde doel wordt gebruikt. Laat niets anders op de machine draaien.
**IP-adres** | Zorg ervoor dat de processerver een statisch IPv4-adres heeft en geen NAT-geconfigureerd heeft.
**Geheugen/CPU-gebruik beheren** |Houd cpu- en geheugengebruik onder de 70%.
**Zorg voor vrije ruimte** | Vrije ruimte verwijst naar de cacheschijfruimte op de processerver. Replicatiegegevens worden opgeslagen in de cache voordat deze naar Azure worden geüpload.<br/><br/> Houd vrije ruimte boven de 25%. Als deze lager is dan 20%, wordt de replicatie beperkt voor gerepliceerde machines die zijn gekoppeld aan de processerver.

## <a name="check-process-server-health"></a>De status van de processerver controleren

De eerste stap in het oplossen van problemen is het controleren van de status en status van de processerver. Controleer hiervoor alle waarschuwingen, controleert of de vereiste services worden uitgevoerd en controleer of er een heartbeat van de processerver is. Deze stappen worden samengevat in de volgende afbeelding, gevolgd door procedures om u te helpen de stappen uit te voeren.

![Problemen met de status van processerver oplossen](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-process-server-health.png)

## <a name="step-1-troubleshoot-process-server-health-alerts"></a>Stap 1: Problemen met waarschuwingen voor de status van de processerver oplossen

De processerver genereert een aantal statuswaarschuwingen. Deze waarschuwingen en aanbevolen acties worden samengevat in de volgende tabel.

**Waarschuwingstype** | **Fout** | **Oplossen**
--- | --- | --- 
![Goed][green] | Geen  | Processerver is verbonden en gezond.
![Waarschuwing][yellow] | Opgegeven services worden niet uitgevoerd. | 1. Controleer of de services worden uitgevoerd.<br/> 2. Als services worden uitgevoerd zoals verwacht, volgt u de onderstaande instructies om [verbindings- en replicatieproblemen op](#check-connectivity-and-replication)te lossen.
![Waarschuwing][yellow]  | CPU-gebruik > 80% voor de laatste 15 minuten. | 1. Voeg geen nieuwe machines toe.<br/>2. Controleer of het aantal VM's dat de processerver gebruikt, wordt uitgelijnd op [gedefinieerde limieten](site-recovery-plan-capacity-vmware.md#capacity-considerations)en overweeg een [extra processerver](vmware-azure-set-up-process-server-scale.md)in te stellen .<br/>3. Volg de onderstaande instructies om [verbindings- en replicatieproblemen op](#check-connectivity-and-replication)te lossen.
![Kritiek][red] |  CPU-gebruik > 95% voor de laatste 15 minuten. | 1. Voeg geen nieuwe machines toe.<br/>2. Controleer of het aantal VM's dat de processerver gebruikt, wordt uitgelijnd op [gedefinieerde limieten](site-recovery-plan-capacity-vmware.md#capacity-considerations)en overweeg een [extra processerver](vmware-azure-set-up-process-server-scale.md)in te stellen .<br/>3. Volg de onderstaande instructies om [verbindings- en replicatieproblemen op](#check-connectivity-and-replication)te lossen.<br/> 4. Als het probleem blijft bestaan, voert u de [implementatieplanner](https://aka.ms/asr-v2a-deployment-planner) voor VMware/fysieke serverreplicatie uit.
![Waarschuwing][yellow] | Geheugengebruik > 80% voor de laatste 15 minuten. |  1. Voeg geen nieuwe machines toe.<br/>2. Controleer of het aantal VM's dat de processerver gebruikt, wordt uitgelijnd op [gedefinieerde limieten](site-recovery-plan-capacity-vmware.md#capacity-considerations)en overweeg een [extra processerver](vmware-azure-set-up-process-server-scale.md)in te stellen .<br/>3. Volg alle instructies in verband met de waarschuwing.<br/> 4. Als het probleem zich blijft voordoen, volgt u de onderstaande instructies om [verbindings- en replicatieproblemen op](#check-connectivity-and-replication)te lossen.
![Kritiek][red] | Geheugengebruik > 95% voor de laatste 15 minuten. | 1. Voeg geen nieuwe machines toe en overweeg het opzetten van een [extra processerver.](vmware-azure-set-up-process-server-scale.md)<br/> 2. Volg alle instructies in verband met de waarschuwing.<br/> 3. 4. Als het probleem zich blijft aanhouden, volgt u de onderstaande instructies om [verbindings- en replicatieproblemen op](#check-connectivity-and-replication)te lossen.<br/> 4. Als het probleem blijft bestaan, voert u de [implementatieplanner](https://aka.ms/asr-v2a-deployment-planner) uit voor vmware-/fysieke serverreplicatieproblemen.
![Waarschuwing][yellow] | Cache map vrije ruimte < 30% voor de laatste 15 minuten. | 1. Voeg geen nieuwe machines toe en overweeg een [extra processerver](vmware-azure-set-up-process-server-scale.md)in te stellen.<br/>2. Controleer of het aantal VM's dat de processerver gebruikt, aansluit bij [richtlijnen.](site-recovery-plan-capacity-vmware.md#capacity-considerations)<br/> 3. Volg de onderstaande instructies om [verbindings- en replicatieproblemen op](#check-connectivity-and-replication)te lossen.
![Kritiek][red] |  Vrije ruimte < 25% voor de laatste 15 minuten | 1. Volg de instructies in verband met de waarschuwing voor dit probleem.<br/> 2. 3. Volg de onderstaande instructies om [verbindings- en replicatieproblemen op](#check-connectivity-and-replication)te lossen.<br/> 3. Als het probleem blijft bestaan, voert u de [implementatieplanner](https://aka.ms/asr-v2a-deployment-planner) voor VMware/fysieke serverreplicatie uit.
![Kritiek][red] | Geen hartslag van de processerver gedurende 15 minuten of langer. De tmansvs-service communiceert niet met de configuratieserver. | 1) Controleer of de processerver actief is.<br/> 2. Controleer of de tmassvc wordt uitgevoerd op de processerver.<br/> 3. Volg de onderstaande instructies om [verbindings- en replicatieproblemen op](#check-connectivity-and-replication)te lossen.


![Tabeltoets](./media/vmware-physical-azure-troubleshoot-process-server/table-key.png)


## <a name="step-2-check-process-server-services"></a>Stap 2: Processerverservices controleren

Services die op de processerver moeten worden uitgevoerd, worden samengevat in de volgende tabel. Er zijn kleine verschillen in services, afhankelijk van hoe de processerver wordt geïmplementeerd. 

Controleer voor alle services, behalve de Microsoft Azure Recovery Services Agent (obengine), of het StartType is ingesteld op **Automatisch** of **Automatisch (vertraagd starten).**
 
**Implementatie** | **Services uitvoeren**
--- | ---
**Processerver op de configuratieserver** | ProcessServer; ProcessServerMonitor; cxprocessserver; InMage PushInstall; Log Upload Service (LogUpload); InMage Scout Application Service; Microsoft Azure Recovery Services Agent (obengine); InMage Scout VX Agent-Sentinel/Buitenpost (svagents); tmansvc; World Wide Web Publishing Service (W3SVC); MySQL; Microsoft Azure Site Recovery Service (dra)
**Processerver die wordt uitgevoerd als een zelfstandige server** | ProcessServer; ProcessServerMonitor; cxprocessserver; InMage PushInstall; Log Upload Service (LogUpload); InMage Scout Application Service; Microsoft Azure Recovery Services Agent (obengine); InMage Scout VX Agent-Sentinel/Buitenpost (svagents); tmansvc.
**Processerver geïmplementeerd in Azure voor failback** | ProcessServer; ProcessServerMonitor; cxprocessserver; InMage PushInstall; Log Upload Service (LogUpload)


## <a name="step-3-check-the-process-server-heartbeat"></a>Stap 3: De heartbeat van de processerver controleren

Als er geen heartbeat van de processerver is (foutcode 806), gaat u als volgt te werk:

1. Controleer of de vm van de processerver actief is.
2. Controleer deze logboeken op fouten.

    C:\ProgramData\ASR\home\svsystems\eventmanager *.log C\ProgramData\ASR\home\svsystems\monitor_protection*.log

## <a name="check-connectivity-and-replication"></a>Connectiviteit en replicatie controleren

 Initiële en doorlopende replicatiefouten worden vaak veroorzaakt door verbindingsproblemen tussen bronmachines en de processerver of tussen de processerver en Azure. Deze stappen worden samengevat in de volgende afbeelding, gevolgd door procedures om u te helpen de stappen uit te voeren.

![Problemen met connectiviteit en replicatie oplossen](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-connectivity-replication.png)


## <a name="step-4-verify-time-sync-on-source-machine"></a>Stap 4: Tijdsynchronisatie op de bronmachine verifiëren

Zorg ervoor dat de systeemdatum/-tijd voor de gerepliceerde machine synchroon is. [Meer weten?](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time)

## <a name="step-5-check-anti-virus-software-on-source-machine"></a>Stap 5: Controleer antivirussoftware op de bronmachine

Controleer of er geen antivirussoftware op de gerepliceerde machine siteherstel blokkeert. Als u Site Recovery moet uitsluiten van antivirusprogramma's, bekijkt u [dit artikel](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program).

## <a name="step-6-check-connectivity-from-source-machine"></a>Stap 6: De connectiviteit van de bronmachine controleren


1. Installeer de [Telnet-client](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx) op de bronmachine als dat nodig is. Gebruik Ping niet.
2. Ping vanuit de bronmachine de processerver op de HTTPS-poort met Telnet. Standaard is 9443 de HTTPS-poort voor replicatieverkeer.

    `telnet <process server IP address> <port>`

3. Controleer of de verbinding is geslaagd.


**Connectiviteit** | **Details** | **Actie**
--- | --- | ---
**Succesvolle** | Telnet toont een leeg scherm en de processerver is bereikbaar. | Geen verdere actie vereist.
**Mislukte** | Je geen verbinding maken | Zorg ervoor dat inkomende poort 9443 is toegestaan op de processerver. Bijvoorbeeld als u een perimeternetwerk of een afgeschermd subnet hebt. Controleer de connectiviteit opnieuw.
**Gedeeltelijk succesvol** | U verbinding maken, maar de bronmachine meldt dat de processerver niet kan worden bereikt. | Ga verder met de volgende probleemoplossingsprocedure.

## <a name="step-7-troubleshoot-an-unreachable-process-server"></a>Stap 7: Een onbereikbare processerver oplossen

Als de processerver niet bereikbaar is vanaf de bronmachine, wordt fout 78186 weergegeven. Als dit probleem niet wordt verholpen, leidt dit probleem ertoe dat zowel app-consistente als crash-consistente herstelpunten niet worden gegenereerd zoals verwacht.

Problemen oplossen door te controleren of de bronmachine het IP-adres van de processerver kan bereiken en het cxpsclient-hulpprogramma op de bronmachine uit te voeren om de end-to-end-verbinding te controleren.


### <a name="check-the-ip-connection-on-the-process-server"></a>De IP-verbinding op de processerver controleren

Als telnet succesvol is, maar de bronmachine meldt dat de processerver niet kan worden bereikt, controleert u of u het IP-adres van de processerver bereiken.

1. Probeer in een webbrowser het IP-adres https://<PS_IP>:<PS_Data_Port>/.
2. Als deze controle een HTTPS-certificaatfout weergeeft, is dat normaal. Als u de fout negeert, ziet u een 400 - Slecht verzoek. Dit betekent dat de server het browserverzoek niet kan serveren en dat de standaard HTTPS-verbinding prima is.
3. Als deze controle niet werkt, let dan op de foutmelding van de browser. Een fout van 407 duidt bijvoorbeeld op een probleem met proxyverificatie.

### <a name="check-the-connection-with-cxpsclient"></a>Controleer de verbinding met cxpsclient

Daarnaast u de cxpsclient-tool uitvoeren om de end-to-end-verbinding te controleren.

1. Voer het gereedschap als volgt uit:

    ```
    <install folder>\cxpsclient.exe -i <PS_IP> -l <PS_Data_Port> -y <timeout_in_secs:recommended 300>
    ```

2. Controleer op de processerver de gegenereerde logboeken in deze mappen:

    C:\ProgramData\ASR\home\svsystems\transport\log\cxps.err C:\ProgramData\ASR\home\svsystems\transport\log\cxps.xfer



### <a name="check-source-vm-logs-for-upload-failures-error-78028"></a>BronVM-logboeken controleren op uploadfouten (fout 78028)

Probleem met gegevensuploads die zijn geblokkeerd van bronmachines naar de processervice, kan ertoe leiden dat zowel crashconsistente als app-consistente herstelpunten niet worden gegenereerd. 

1. Als u fouten in het uploaden van netwerken wilt oplossen, u zoeken naar fouten in dit logboek:

    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\svagents*.log 

2. Gebruik de rest van de procedures in dit artikel kan u helpen om problemen met het uploaden van gegevens op te lossen.



## <a name="step-8-check-whether-the-process-server-is-pushing-data"></a>Stap 8: Controleren of de processerver gegevens pusht

Controleer of de processerver actief gegevens naar Azure pusht.

  1. Open Taakbeheer op de processerver (druk op Ctrl+Shift+Esc).
  2. Selecteer het tabblad **Prestaties** > **Resourcemonitor openen**.
  3. Selecteer op de pagina **Resourcemonitor** het tabblad **Netwerk.** Controleer **onder Processen met netwerkactiviteit**of cbengine.exe actief een groot aantal gegevens verzendt.

       ![Volumes onder processen met netwerkactiviteit](./media/vmware-physical-azure-troubleshoot-process-server/cbengine.png)

  Als cbengine.exe geen grote hoeveelheid gegevens verzendt, voert u de stappen in de volgende secties uit.

## <a name="step-9-check-the-process-server-connection-to-azure-blob-storage"></a>Stap 9: De processerververbinding met Azure blob-opslag controleren

1. Selecteer **cbengine.exe**in Resourcemonitor .
2. Controleer onder **TCP-verbindingen**of er verbinding is tussen de processerver en de Azure-opslag.

  ![Connectiviteit tussen cbengine.exe en de URL van azure blob-opslag](./media/vmware-physical-azure-troubleshoot-process-server/rmonitor.png)

### <a name="check-services"></a>Diensten controleren

Als er geen verbinding is tussen de processerver en de URL van de Azure Blob-opslag, controleert u of services worden uitgevoerd.

1. Selecteer **Services**in het Configuratiescherm .
2. Controleer of de volgende services worden uitgevoerd:

    - cxprocessserver
    - InMage Scout VX Agent - Sentinel / Buitenpost
    - Microsoft Azure Recovery Services-agent
    - Microsoft Azure-service voor herstel van de site
    - tmansvc tmansvc

3. Start of start een service opnieuw die niet wordt uitgevoerd.
4. Controleer of de processerver is verbonden en bereikbaar is. 

## <a name="step-10-check-the-process-server-connection-to-azure-public-ip-address"></a>Stap 10: controleer de processerververbinding met het openbare IP-adres van Azure

1. Open op de processerver in **%programfiles%\Microsoft Azure Recovery Services Agent\Temp**het nieuwste CBEngineCurr.errlog-bestand.
2. Zoek in het bestand naar **443**of naar de **poging tot tekenreeksverbinding mislukt**.

  ![Foutlogboeken in de map Temp](./media/vmware-physical-azure-troubleshoot-process-server/logdetails1.png)

3. Als u problemen ziet, plaatst u uw azure-ip-adres in het BESTAND CBEngineCurr.currLog met poort 443:

  `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

5. Gebruik Telnet bij de opdrachtregel op de processerver om uw openbare IP-adres in Azure te pingen.
6. Als u geen verbinding maken, volgt u de volgende procedure.

## <a name="step-11-check-process-server-firewall-settings"></a>Stap 11: Controleer de firewall-instellingen van de processerver. 

Controleer of de ip-adresgebaseerde firewall op de processerver de toegang blokkeert.

1. Voor firewallregels op basis van IP-adres:

    a) Download de volledige lijst met [IP-bereiken van Microsoft Azure-datacenter.](https://www.microsoft.com/download/details.aspx?id=41653)

    b) Voeg de IP-adresbereiken toe aan uw firewallconfiguratie, om ervoor te zorgen dat de firewall communicatie naar Azure toestaat (en naar de standaard HTTPS-poort, 443).

    c) Sta IP-adresbereiken toe voor de Azure-regio van uw abonnement en voor de regio Azure West US (gebruikt voor toegangscontrole en identiteitsbeheer).

2. Voeg voor firewalls op basis van URL's de URL's in de volgende tabel toe aan de firewallconfiguratie.

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  


## <a name="step-12-verify-process-server-proxy-settings"></a>Stap 12: Proxy-instellingen voor processervers verifiëren 

1. Als u een proxyserver gebruikt, moet u ervoor zorgen dat de naam van de proxyserver wordt opgelost door de DNS-server. Controleer de waarde die u hebt opgegeven bij het instellen van de configuratieserver in de registersleutel **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings**.
2. Controleer of dezelfde instellingen worden gebruikt door de Azure Site Recovery-agent om gegevens te verzenden.

    a) Zoeken naar **Microsoft Azure Backup**.

    b) Open **Microsoft Azure Backup**en selecteer Eigenschappen van **actiewijziging** > **Change Properties**.

    c) Op het tabblad **Proxyconfiguratie** moet het proxyadres hetzelfde zijn als het proxyadres dat wordt weergegeven in de registerinstellingen. Zo niet, wijzig het dan naar hetzelfde adres.

## <a name="step-13-check-bandwidth"></a>Stap 13: Bandbreedte controleren

Verhoog de bandbreedte tussen de processerver en Azure en controleer vervolgens of het probleem zich nog steeds voordoet.


## <a name="next-steps"></a>Volgende stappen

Als u meer hulp nodig hebt, plaatst u uw vraag in het [Azure Site Recovery-forum.](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) 

[green]: ./media/vmware-physical-azure-troubleshoot-process-server/green.png
[yellow]: ./media/vmware-physical-azure-troubleshoot-process-server/yellow.png
[red]: ./media/vmware-physical-azure-troubleshoot-process-server/red.png
