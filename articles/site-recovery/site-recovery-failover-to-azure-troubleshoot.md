---
title: Failover oplossen van Azure-fouten | Microsoft Documenten
description: In dit artikel worden manieren beschreven om veelvoorkomende fouten op te lossen bij het niet overmaken naar Azure
author: ponatara
manager: abhemraj
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 01/08/2020
ms.author: mayg
ms.openlocfilehash: 54e44a12f593d2074eefe5b2ff890863db3199f7
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478952"
---
# <a name="troubleshoot-errors-when-failing-over-vmware-vm-or-physical-machine-to-azure"></a>Fouten oplossen bij het mislukken van VMware VM of fysieke machine naar Azure

U een van de volgende fouten ontvangen tijdens het uitvoeren van failover van een virtuele machine naar Azure. Als u problemen wilt oplossen, gebruikt u de beschreven stappen voor elke foutvoorwaarde.

## <a name="failover-failed-with-error-id-28031"></a>Failover is mislukt met fout-id 28031

Site herstel is niet in staat om een mislukte over virtuele machine in Azure te maken. Het kan gebeuren als gevolg van een van de volgende redenen:

* Er is onvoldoende quotum beschikbaar om de virtuele machine te maken: u het beschikbare quotum controleren door naar Abonnement -> Gebruik + quota te gaan. U een [nieuw ondersteuningsverzoek openen](https://aka.ms/getazuresupport) om het quotum te verhogen.

* U probeert virtuele machines van verschillende groottefamilies in dezelfde beschikbaarheidsset te mislukken. Zorg ervoor dat u een familie van dezelfde grootte kiest voor alle virtuele machines in dezelfde beschikbaarheidsset. Wijzig de grootte door naar de compute- en netwerkinstellingen van de virtuele machine te gaan en vervolgens failover opnieuw te proberen.

* Er is een beleid voor het abonnement dat het maken van een virtuele machine voorkomt. Wijzig het beleid om het maken van een virtuele machine mogelijk te maken en probeer vervolgens failover opnieuw.

## <a name="failover-failed-with-error-id-28092"></a>Failover is mislukt met fout-id 28092

Site Recovery is niet in staat om een netwerkinterface te maken voor de mislukte virtuele machine. Zorg ervoor dat u over voldoende quota beschikt om netwerkinterfaces in het abonnement te maken. U het beschikbare quotum controleren door naar Abonnement -> Gebruik + quota te gaan. U een [nieuw ondersteuningsverzoek openen](https://aka.ms/getazuresupport) om het quotum te verhogen. Als u voldoende quotum hebt, kan dit een probleem zijn met tussenpozen en probeert u de bewerking opnieuw. Als het probleem ook na pogingen blijft bestaan, laat u een opmerking achter aan het einde van dit document.  

## <a name="failover-failed-with-error-id-70038"></a>Failover is mislukt met fout-id 70038

Site herstel is niet in staat om een mislukte over Classic virtuele machine in Azure te maken. Het kan gebeuren omdat:

* Een van de bronnen, zoals een virtueel netwerk dat nodig is voor het maken van de virtuele machine bestaat niet. Maak het virtuele netwerk zoals voorzien onder Compute- en Netwerkinstellingen van de virtuele machine of wijzig de instelling in een virtueel netwerk dat al bestaat en probeer vervolgens failover opnieuw.

## <a name="failover-failed-with-error-id-170010"></a>Failover is mislukt met fout-id 170010

Site herstel is niet in staat om een mislukte over virtuele machine in Azure te maken. Het kan gebeuren omdat een interne activiteit van hydratatie is mislukt voor de on-premises virtuele machine.

Als u een machine in Azure wilt weergeven, vereist de Azure-omgeving dat sommige stuurprogramma's in de startstatus van het opstarten zijn en services zoals DHCP in de status automatisch starten. Zo zet hydratatieactiviteit, op het moment van failover, het opstarttype **atapi, intelide, storflt, vmbus en storvsc-stuurprogramma's** om op te starten. Het converteert ook het opstarttype van een paar services zoals DHCP naar automatisch opstarten. Deze activiteit kan mislukken als gevolg van omgevingsspecifieke problemen. 

Als u het opstarttype stuurprogramma's voor **Windows Guest OS**handmatig wilt wijzigen, voert u de volgende stappen uit:

1. [Download](https://download.microsoft.com/download/5/D/6/5D60E67C-2B4F-4C51-B291-A97732F92369/Script-no-hydration.ps1) het no-hydratatiescript en voer het als volgt uit. Dit script controleert of VM hydratatie vereist.

    `.\Script-no-hydration.ps1`

    Het geeft het volgende resultaat als hydratatie nodig is:

        REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc           start =  3 expected value =  0

        This system doesn't meet no-hydration requirement.

    In het geval dat de VM voldoet aan geen-hydratatie eis, zal het script geven het resultaat "Dit systeem voldoet aan no-hydratatie eis". In dit geval zijn alle stuurprogramma's en services in de status zoals vereist door Azure en is hydratatie op de VM niet vereist.

2. Voer het script zonder hydratatie als volgt uit als de VM niet voldoet aan de vereiste voor hydratatie.

    `.\Script-no-hydration.ps1 -set`
    
    Dit zal het opstarttype stuurprogramma's converteren en geeft het resultaat zoals hieronder:
    
        REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc           start =  3 expected value =  0 

        Updating registry:  REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc   start =  0 

        This system is now no-hydration compatible. 

## <a name="unable-to-connectrdpssh-to-the-failed-over-virtual-machine-due-to-grayed-out-connect-button-on-the-virtual-machine"></a>Kan geen verbinding maken/RDP/SSH met de defecte virtuele machine vanwege de grijs weergegeven connect-knop op de virtuele machine

Als de **connect-knop** op de mislukte via VM in Azure grijs wordt weergegeven en u niet verbonden bent met Azure via een Express-route of site-to-site VPN-verbinding,

1. Ga naar **Virtual machine** > **Networking,** klik op de naam van de vereiste netwerkinterface.  ![netwerkinterface](media/site-recovery-failover-to-azure-troubleshoot/network-interface.PNG)
2. Navigeer naar **Ip-configuraties**en klik vervolgens op het naamveld van de vereiste IP-configuratie. ![IP-configuraties](media/site-recovery-failover-to-azure-troubleshoot/IpConfigurations.png)
3. Als u openbaar IP-adres wilt inschakelen, klikt u op **Inschakelen**. ![IP inschakelen](media/site-recovery-failover-to-azure-troubleshoot/Enable-Public-IP.png)
4. Klik op **Vereiste instellingen configureren** > **Maak nieuwe .** ![Nieuwe maken](media/site-recovery-failover-to-azure-troubleshoot/Create-New-Public-IP.png)
5. Voer de naam van het openbare adres in, kies de standaardopties voor **SKU** en **toewijzing**en klik op **OK**.
6. Klik nu op Opslaan om de aangebrachte wijzigingen **op te slaan**.
7. Sluit de panelen en navigeer naar **overzichtssectie** van virtuele machine om verbinding te maken/RDP.

## <a name="unable-to-connectrdpssh---vm-connect-button-available"></a>Kan geen verbinding maken/RDP/SSH - VM Connect-knop beschikbaar

Als de knop **Verbinding** maken op de fout met VM in Azure beschikbaar is (niet grijs weergegeven), controleert u Diagnostische gegevens op uw virtuele machine **opstart** en controleert u op fouten zoals vermeld in [dit artikel](../virtual-machines/windows/boot-diagnostics.md).

1. Als de virtuele machine niet is gestart, probeert u te falen naar een ouder herstelpunt.
2. Als de toepassing in de virtuele machine niet up is, probeert u niet over te gaan naar een app-consistent herstelpunt.
3. Als de virtuele machine is verbonden met het domein, moet u ervoor zorgen dat de domeincontroller nauwkeurig functioneert. Dit kan worden gedaan door het volgen van de onderstaande stappen:

    a. Maak een nieuwe virtuele machine in hetzelfde netwerk.

    b.  Zorg ervoor dat het in staat is om toe te treden tot hetzelfde domein waarop de mislukte virtuele machine naar verwachting naar boven komt.

    c. Als de domeincontroller **niet** goed functioneert, probeert u vervolgens in te loggen op de mislukte virtuele machine met behulp van een lokaal beheerdersaccount.
4. Als u een aangepaste DNS-server gebruikt, moet u ervoor zorgen dat deze bereikbaar is. Dit kan worden gedaan door het volgen van de onderstaande stappen:

    a. Maak een nieuwe virtuele machine in hetzelfde netwerk en

    b. Controleren of de virtuele machine naamomzetting kan doen met behulp van de aangepaste DNS-server

>[!Note]
>Voor het inschakelen van een andere instelling dan Boot Diagnostics moet Azure VM Agent in de virtuele machine worden geïnstalleerd voordat de failover wordt geïnstalleerd

## <a name="unable-to-open-serial-console-after-failover-of-a-uefi-based-machine-into-azure"></a>Kan seriële console niet openen na een failover van een UEFI-gebaseerde machine in Azure

Als u verbinding maken met de machine via RDP, maar geen seriële console openen, voert u de volgende stappen uit:

* Als het besturingssysteem van de machine Red Hat of Oracle Linux 7.*/8.0 is, voert u de volgende opdracht uit op de failover Azure VM met rootmachtigingen. Start de VM opnieuw op na de opdracht.

        grub2-mkconfig -o /boot/efi/EFI/redhat/grub.cfg

* Als het machinebesturingssysteem CentOS 7.* is, voert u de volgende opdracht uit op de failover Azure VM met rootmachtigingen. Start de VM opnieuw op na de opdracht.

        grub2-mkconfig -o /boot/efi/EFI/centos/grub.cfg

## <a name="unexpected-shutdown-message-event-id-6008"></a>Bericht voor onverwachte afsluiting (gebeurtenis-id 6008)

Bij het opstarten van een failover van windows VM-berichten geeft u een onverwacht uitschakelbericht op de herstelde VM aan, dat een vm-afsluitstatus niet is vastgelegd in het herstelpunt dat wordt gebruikt voor failover. Dit gebeurt wanneer u herstelt tot een punt waarop de VM niet volledig is afgesloten.

Dit is normaal gesproken geen reden tot bezorgdheid en kan meestal worden genegeerd voor ongeplande failovers. Als de failover is gepland, moet u ervoor zorgen dat de VM correct is afgesloten voordat de fout is mislukt en voldoende tijd biedt om in behandeling zijnde replicatiegegevens on-premises naar Azure te sturen. Gebruik vervolgens de **optie Laatste** op het [scherm Failover,](site-recovery-failover.md#run-a-failover) zodat alle in behandeling zijnde gegevens op Azure worden verwerkt tot een herstelpunt, dat vervolgens wordt gebruikt voor VM-failover.

## <a name="unable-to-select-the-datastore"></a>Kan het Gegevensarchief niet selecteren

Dit probleem wordt aangegeven wanneer u de datastore in Azure de portal niet zien wanneer u de virtuele machine probeert te beveiligen die een failover heeft ervaren. Dit komt omdat het masterdoel niet wordt herkend als een virtuele machine onder vCenters die zijn toegevoegd aan Azure Site Recovery.

Zie [Machines opnieuw beveiligen en weergeven naar een on-premises site na een fail-over naar Azure voor](vmware-azure-reprotect.md)meer informatie over het opnieuw beveiligen van een virtuele machine.

Ga als ander op zoek naar het probleem:

Maak handmatig het masterdoel in het vCenter dat uw bronmachine beheert. De datastore is beschikbaar na de volgende vCenter-detectie- en vernieuwingsbewerkingen.

> [!Note]
> 
> Het kan tot 30 minuten duren voordat de detectie- en vernieuwingsbewerkingen zijn voltooid. 

## <a name="linux-master-target-registration-with-cs-fails-with-a-tls-error-35"></a>Linux Master Target registratie met CS mislukt met een TLS-fout 35 

De Azure Site Recovery Master Target-registratie met de configuratieserver mislukt omdat de geverifieerde proxy is ingeschakeld op het hoofddoel. 
 
Deze fout wordt aangegeven door de volgende tekenreeksen in het installatielogboek: 

```
RegisterHostStaticInfo encountered exception config/talwrapper.cpp(107)[post] CurlWrapper Post failed : server : 10.38.229.221, port : 443, phpUrl : request_handler.php, secure : true, ignoreCurlPartialError : false with error: [at curlwrapperlib/curlwrapper.cpp:processCurlResponse:231]   failed to post request: (35) - SSL connect error. 
```

Ga als ander op zoek naar het probleem:
 
1. Open op de vm van de configuratieserver een opdrachtprompt en controleer de proxy-instellingen met de volgende opdrachten:

    cat /etc/environment echo $http_proxy echo $https_proxy 

2. Als uit de uitvoer van de vorige opdrachten blijkt dat de http_proxy- of https_proxy-instellingen zijn gedefinieerd, gebruikt u een van de volgende methoden om de communicatie met de hoofddoelstelling met de configuratieserver te deblokkeren:
   
   - Download de [PsExec tool](https://aka.ms/PsExec).
   - Gebruik het hulpprogramma om toegang te krijgen tot de gebruikerscontext van het systeem en te bepalen of het proxyadres is geconfigureerd. 
   - Als de proxy is geconfigureerd, opent u IE in een context van de systeemgebruiker met het gereedschap PsExec.
  
     **psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"**

   - Ga als u erop toe als u erop aangaat dat de hoofddoelserver met de configuratieserver kan communiceren:
  
     - Wijzig de proxy-instellingen in Internet Explorer om het IP-adres van de Hoofddoelserver via de proxy te omzeilen.   
     of
     - Schakel de proxy uit op de Master Target-server. 


## <a name="next-steps"></a>Volgende stappen
- Problemen met [de RDP-verbinding met Windows VM oplossen](../virtual-machines/windows/troubleshoot-rdp-connection.md)
- Problemen met [de SSH-verbinding met Linux VM oplossen](../virtual-machines/linux/detailed-troubleshoot-ssh-connection.md)

Als je meer hulp nodig hebt, plaats je je zoekopdracht op [het siteherstelforum](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) of laat je een reactie achter aan het einde van dit document. We hebben een actieve community die je moet kunnen helpen.
