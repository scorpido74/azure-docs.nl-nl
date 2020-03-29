---
title: Automatiseer StorSimple fileshare DR met Azure Site Recovery | Microsoft Documenten
description: Beschrijft de stappen en aanbevolen procedures voor het maken van een oplossing voor noodherstel voor bestandsshares die worden gehost op Microsoft Azure StorSimple-opslag.
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: ''
ms.assetid: 23049a2c-055e-4d0e-b8f5-af2a87ecf53f
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/13/2017
ms.author: alkohli
ms.openlocfilehash: 650798fdb884e6494990efb533335a1dd8b4d89f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67875397"
---
# <a name="automated-disaster-recovery-solution-using-azure-site-recovery-for-file-shares-hosted-on-storsimple"></a>Geautomatiseerde disaster recovery-oplossing met Azure Site Recovery voor bestandsshares die worden gehost op StorSimple

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Overzicht
Microsoft Azure StorSimple is een hybride cloudopslagoplossing die de complexiteit van ongestructureerde gegevens aanpakt die vaak aan bestandsshares zijn gekoppeld. StorSimple gebruikt cloudopslag als een uitbreiding van de on-premises oplossing en controleert automatisch gegevens voor on-premises opslag en cloudopslag. Geïntegreerde gegevensbescherming, met lokale en cloudsnapshots, elimineert de noodzaak van een uitgebreide opslaginfrastructuur.

[Azure Site Recovery](../site-recovery/site-recovery-overview.md) is een op Azure gebaseerde service die DR-mogelijkheden (disaster recovery) biedt door replicatie, failover en herstel van virtuele machines te orkestreren. Azure Site Recovery ondersteunt een aantal replicatietechnologieën om consistent te repliceren, te beschermen en naadloos te falen over virtuele machines en toepassingen naar private/publieke of gehoste clouds.

Met Azure Site Recovery, virtual machine replication en StorSimple cloud snapshot mogelijkheden u de volledige bestandsserveromgeving beveiligen. In het geval van een onderbreking u met één klik uw bestandsshares binnen enkele minuten online brengen in Azure.

In dit document wordt in detail uitgelegd hoe u een oplossing voor noodherstel maken voor uw bestandsshares die worden gehost op StorSimple-opslag en geplande, ongeplande en testfailovers uitvoeren met behulp van een herstelplan met één klik. In wezen laat het zien hoe u het herstelplan in uw Azure Site Recovery-kluis wijzigen om StorSimple-failovers in te schakelen tijdens rampscenario's. Daarnaast worden ondersteunde configuraties en vereisten beschreven. In dit document wordt ervan uitgegaan dat u bekend bent met de basisprincipes van Azure Site Recovery- en StorSimple-architecturen.

## <a name="supported-azure-site-recovery-deployment-options"></a>Ondersteunde azure-siteherstelimplementatieopties
Klanten kunnen bestandsservers implementeren als fysieke servers of virtuele machines (VM's) die worden uitgevoerd op Hyper-V of VMware, en vervolgens bestandsshares maken uit volumes die zijn gesneden uit StorSimple-opslag. Azure Site Recovery kan zowel fysieke als virtuele implementaties beschermen op een secundaire site of naar Azure. Dit document bevat details van een DR-oplossing met Azure als herstelsite voor een vm van bestandsserver die wordt gehost op Hyper-V en met bestandsshares op StorSimple-opslag. Andere scenario's waarin de bestandsserver VM zich op een VMware VM of een fysieke machine bevindt, kunnen op dezelfde manier worden geïmplementeerd.

## <a name="prerequisites"></a>Vereisten
Het implementeren van een oplossing voor noodherstel met één klik die Azure Site Recovery gebruikt voor bestandsshares die worden gehost op StorSimple-opslag, heeft de volgende vereisten:

   - On-premises Windows Server 2012 R2 File server VM gehost op Hyper V of VMware of een fysieke machine
   - StorSimple-opslagapparaat on-premises geregistreerd bij Azure StorSimple-manager
   - StorSimple Cloud Appliance gemaakt in de Azure StorSimple manager. Het apparaat kan in een uitschakeltoestand worden gehouden.
   - Bestandsshares gehost op de volumes die zijn geconfigureerd op het StorSimple-opslagapparaat
   - [Kluis Azure Site Recovery Services](../site-recovery/site-recovery-vmm-to-vmm.md) gemaakt in een Microsoft Azure-abonnement

Als Azure uw herstelsite is, voert u bovendien het hulpprogramma voor de [beoordeling van azure virtual machine readiness assessment](https://azure.microsoft.com/downloads/vm-readiness-assessment/) uit op VM's om te controleren of deze compatibel zijn met Azure VM's en Azure Site Recovery-services.

Als u latentieproblemen wilt voorkomen (wat kan leiden tot hogere kosten), moet u ervoor zorgen dat u uw StorSimple Cloud Appliance, automatiseringsaccount en opslagaccount(en) in dezelfde regio maakt.

## <a name="enable-dr-for-storsimple-file-shares"></a>DR inschakelen voor StorSimple-bestandsshares
Elk onderdeel van de on-premises omgeving moet worden beschermd om volledige replicatie en herstel mogelijk te maken. In deze sectie wordt beschreven hoe u:
    
   - Active Directory- en DNS-replicatie instellen (optioneel)
   - Azure Site Recovery gebruiken om de beveiliging van de bestandsserver-vm mogelijk te maken
   - Beveiliging van StorSimple-volumes inschakelen
   - Het netwerk configureren

### <a name="set-up-active-directory-and-dns-replication-optional"></a>Active Directory- en DNS-replicatie instellen (optioneel)
Als u de machines met Active Directory en DNS wilt beveiligen zodat ze beschikbaar zijn op de DR-site, moet u ze expliciet beveiligen (zodat de bestandsservers toegankelijk zijn na failover met verificatie). Er zijn twee aanbevolen opties op basis van de complexiteit van de on-premises omgeving van de klant.

#### <a name="option-1"></a>Mogelijkheid 1
Als de klant een klein aantal toepassingen heeft, één domeincontroller voor de gehele on-premises site, en deze niet over de hele site valt, raden we u aan azure site recovery-replicatie te gebruiken om de domeincontrollermachine te repliceren naar een secundaire site (dit is van toepassing op zowel site-to-site als site-to-Azure).

#### <a name="option-2"></a>Optie 2
Als de klant een groot aantal toepassingen heeft, een Active Directory-forest uitvoert en over een paar toepassingen tegelijk mislukt, raden we u aan een extra domeincontroller in te stellen op de DR-site (een secundaire site of in Azure).

Raadpleeg [de geautomatiseerde DR-oplossing voor Active Directory en DNS met Azure Site Recovery](../site-recovery/site-recovery-active-directory.md) voor instructies wanneer u een domeincontroller beschikbaar maakt op de DR-site. Voor de rest van dit document gaan we ervan uit dat er een domeincontroller beschikbaar is op de DR-site.

### <a name="use-azure-site-recovery-to-enable-protection-of-the-file-server-vm"></a>Azure Site Recovery gebruiken om de beveiliging van de bestandsserver-vm mogelijk te maken
Deze stap vereist dat u de on-premises bestandsserveromgeving voorbereidt, een Azure Site Recovery-kluis maakt en voorbereidt en bestandsbeveiliging van de VM inschakelt.

#### <a name="to-prepare-the-on-premises-file-server-environment"></a>De on-premises bestandsserveromgeving voorbereiden
1. Stel het **gebruikersaccountbeheer in** op **nooit op de hoogte stellen.** Dit is vereist, zodat u Azure-automatiseringsscripts gebruiken om de iSCSI-doelen na failover door Azure Site Recovery met elkaar te verbinden.
   
   1. Druk op de Windows-toets +Q en zoek naar **UAC**.  
   1. Selecteer **Instellingen voor gebruikersaccountbeheer wijzigen**.  
   1. Sleep de balk naar de onderkant naar **Nooit melden**.  
   1. Klik op **OK** en selecteer **Vervolgens Ja** wanneer u daarom wordt gevraagd.  
   
      ![Instellingen voor gebruikersaccountbeheer](./media/storsimple-disaster-recovery-using-azure-site-recovery/image1.png) 

1. Installeer de VM-agent op elk van de VM's van de bestandsserver. Dit is vereist, zodat u Azure-automatiseringsscripts uitvoeren op de mislukte VM's.
   
   1. [Download de](https://aka.ms/vmagentwin) `C:\\Users\\<username>\\Downloads`agent naar .
   1. Open Windows PowerShell in de administratormodus (Als administrator uitvoeren) en voer de volgende opdracht in om naar de downloadlocatie te navigeren:  
         `cd C:\\Users\\<username>\\Downloads\\WindowsAzureVmAgent.2.6.1198.718.rd\_art\_stable.150415-1739.fre.msi`
         
         > [!NOTE]
         > De bestandsnaam kan veranderen afhankelijk van de versie.
      
1. Klik op **Volgende**.
1. Accepteer de **algemene voorwaarden** en klik op **Volgende**.
1. Klik op **Voltooien**.
1. Maak bestandsshares met volumes die zijn gesneden uit StorSimple-opslag. Zie [De service StorSimple Manager gebruiken om volumes te beheren voor](storsimple-manage-volumes.md)meer informatie.
   
   1. Druk op uw on-premises VM's op de Windows-toets +Q en zoek naar **iSCSI**.
   1. Selecteer **iSCSI-initiator**.
   1. Selecteer het tabblad **Configuratie** en kopieer de naam van de initiator.
   1. Log in bij de [Azure-portal](https://portal.azure.com/).
   1. Selecteer het tabblad **StorSimple** en selecteer vervolgens de StorSimple Manager-service die het fysieke apparaat bevat.
   1. Maak volumecontainer(en) en maak vervolgens volume(s). (Deze volumes zijn voor de bestandsshare(s) op de VM's van de bestandsserver). Kopieer de naam van de initiator en geef een passende naam voor de Access Control Records wanneer u de volumes maakt.
   1. Selecteer het tabblad **Configureren** en noteer het IP-adres van het apparaat.
   1. Ga op uw on-premises VM's opnieuw naar de **iSCSI-initiator** en voer het IP in in de sectie Quick Connect. Klik **op Snel verbinding** (het apparaat moet nu zijn aangesloten).
   1. Open de Azure-portal en selecteer het tabblad **Volumes en apparaten.** Klik op **Automatisch configureren**. Het volume dat u hebt gemaakt, moet worden weergegeven.
   1. Selecteer in de portal het tabblad **Apparaten** en selecteer **Vervolgens Een nieuw virtueel apparaat maken.** (Dit virtuele apparaat wordt gebruikt als er een failover optreedt). Dit nieuwe virtuele apparaat kan offline worden bewaard om extra kosten te voorkomen. Als u het virtuele apparaat offline wilt halen, gaat u naar de sectie **Virtuele machines** op de portal en schakelt u het uit.
   1. Ga terug naar de on-premises VM's en open Schijfbeheer (druk op de Windows-toets + X en selecteer **Schijfbeheer).**
   1. U zult merken dat sommige extra schijven (afhankelijk van het aantal volumes die u hebt gemaakt). Klik met de rechtermuisknop op de eerste, selecteer **Initialize Disk**en selecteer **OK**. Klik met de rechtermuisknop op de sectie **Niet toegewezen,** selecteer **Nieuw eenvoudig volume,** wijs deze een stationsletter toe en voltooi de wizard.
   1. Herhaal stap l voor alle schijven. U nu alle schijven op **deze pc** in de Windows Verkenner bekijken.
   1. Gebruik de rol Bestands- en opslagservices om bestandsshares op deze volumes te maken.

#### <a name="to-create-and-prepare-an-azure-site-recovery-vault"></a>Een Azure Site Recovery-kluis maken en voorbereiden
Raadpleeg de [azure site recovery-documentatie](../site-recovery/site-recovery-hyper-v-site-to-azure.md) om aan de slag te gaan met Azure Site Recovery voordat u de vm van de bestandsserver beschermt.

#### <a name="to-enable-protection"></a>Bescherming inschakelen
1. Koppel het iSCSI-doel(en) los van de on-premises VM's die u wilt beveiligen via Azure Site Recovery:
   
   1. Druk op Windows-toets + Q en zoek naar **iSCSI**.
   1. Selecteer **ISCSI-initiator instellen**.
   1. Koppel het StorSimple-apparaat los dat u eerder hebt aangesloten. U de bestandsserver ook enkele minuten uitschakelen wanneer u beveiliging inschakelt.
      
   > [!NOTE]
   > Hierdoor zijn de bestandsshares tijdelijk niet beschikbaar.
   
1. [Schakel virtuele machinebeveiliging](../site-recovery/site-recovery-hyper-v-site-to-azure.md) van de bestandsserver-VM in vanuit de Azure Site Recovery-portal.
1. Wanneer de eerste synchronisatie begint, u het doel opnieuw verbinden. Ga naar de iSCSI-initiator, selecteer het StorSimple-apparaat en klik op **Verbinden**.
1. Wanneer de synchronisatie is voltooid en de status van de VM **is beveiligd,** selecteert u de vm, selecteert u het tabblad **Configureren** en werkt u het netwerk van de VM dienovereenkomstig bij (dit is het netwerk waarvan de fout op VM(s) deel uitmaakt). Als het netwerk niet wordt weergegeven, betekent dit dat de synchronisatie nog steeds aan de gang is.

### <a name="enable-protection-of-storsimple-volumes"></a>Beveiliging van StorSimple-volumes inschakelen
Als u de **standaardback-up inschakelen voor deze volumeoptie voor** de StorSimple-volumes niet hebt geselecteerd, gaat u naar **Back-upbeleid** in de StorSimple Manager-service en maakt u een geschikt back-upbeleid voor alle volumes. We raden u aan de frequentie van back-ups in te stellen op de doelstelling herstelpunt (RPO) die u voor de toepassing wilt zien.

### <a name="configure-the-network"></a>Het netwerk configureren
Configureer voor de bestandsserver-VM netwerkinstellingen in Azure Site Recovery, zodat de VM-netwerken na een failover zijn gekoppeld aan het juiste DR-netwerk.

U de VM selecteren op het tabblad **Gerepliceerde items** om de netwerkinstellingen te configureren, zoals in de volgende afbeelding wordt weergegeven.

![Rekenen en netwerk](./media/storsimple-disaster-recovery-using-azure-site-recovery/image2.png)

## <a name="create-a-recovery-plan"></a>Een herstelplan maken
U een herstelplan maken in ASR om het failoverproces van de bestandsshares te automatiseren. Als er een verstoring optreedt, u de bestandsaandelen in een paar minuten met slechts één klik naar boven halen. Om deze automatisering in te schakelen, hebt u een Azure-automatiseringsaccount nodig.

#### <a name="to-create-an-automation-account"></a>Een Automatiseringsaccount maken
1. Ga naar de &gt; sectie Azure-portalautomatisering. **Automation**
1. Klik **+ Knop Toevoegen,** opent onder het blad.
   
   ![Automation-account toevoegen](./media/storsimple-disaster-recovery-using-azure-site-recovery/image11.png)
   
   - Naam - Een nieuw automatiseringsaccount invoeren
   - Abonnement - Abonnement kiezen
   - Resourcegroep - Nieuwe/bestaande resourcegroep maken/kiezen
   - Locatie - Kies locatie, bewaar deze in dezelfde geo/regio waarin de StorSimple Cloud Appliance en Storage Accounts zijn gemaakt.
   - Azure Run As-account maken - Selecteer De optie **Ja.**
   
1. Ga naar het automatiseringsaccount, klik op **Galerie bladeren** &gt; **uitvoeren** om alle vereiste runbooks te importeren in het automatiseringsaccount.
1. Voeg de volgende runbooks toe door de tag **Disaster Recovery** in de galerie te vinden:
   
   - Opruimen van StorSimple volumes na Test Failover (TFO)
   - Failover StorSimple volume containers
   - Volumes op StorSimple-apparaat monteren na failover
   - Aangepaste scriptextensie verwijderen in Azure VM
   - StorSimple Virtueel toestel starten
   
      ![Bladeren door galerie](./media/storsimple-disaster-recovery-using-azure-site-recovery/image3.png)
   
1. Publiceer alle scripts door het runbook in het automatiseringsaccount te selecteren en klik op **Publiceren** **bewerken** &gt; en vervolgens **Ja** bij het verificatiebericht. Na deze stap wordt het tabblad **Runbooks** als volgt weergegeven:
   
   ![Runbooks](./media/storsimple-disaster-recovery-using-azure-site-recovery/image4.png)
   
1. Klik in het automatiseringsaccount op **Variabelen** &gt; **Voeg een variabele toe** en voeg de volgende variabelen toe. U ervoor kiezen om deze activa te versleutelen. Deze variabelen zijn een specifiek herstelplan. Als uw herstelplan, dat u in de volgende stap maakt, De naam TestPlan is, dan moeten uw variabelen TestPlan-StorSimRegKey, TestPlan-AzureSubscriptionName, enzovoort zijn.

   - **BaseUrl:** de url van Resource Manager voor de Azure-cloud. **Get-AzEnvironment gebruiken | Naam van object selecteren, cmdlet ResourceManagerUrl.**
   - _RecoveryPlanName_**-ResourceGroupName:** de groep Resourcemanager met de StorSimple-bron.
   - _RecoveryPlanName_**-ManagerName:** De StorSimple-bron met het StorSimple-apparaat.
   - _RecoveryPlanName_**-DeviceName:** Het StorSimple-apparaat dat moet worden mislukt.
   - _RecoveryPlanName_**-DeviceIpAddress:** het IP-adres van het apparaat (dit is te vinden &gt; op het tabblad **Apparaten** onder storSimple-apparaatbeheersectie **Instellingen** &gt; **netwerk** &gt; **DNS-instellingengroep).**
   - _RecoveryPlanName_**-VolumeContainers:** een door komma's gescheiden reeks volumecontainers die aanwezig zijn op het apparaat en die moeten worden mislukt; bijvoorbeeld: volcon1, volcon2, volcon3.
   - _RecoveryPlanName_**-TargetDeviceName**: Het StorSimple Cloud Appliance waarop de containers moeten worden ingeschakeld.
   - _RecoveryPlanName_**-TargetDeviceIpAddress:** Het IP-adres van het doelapparaat (dit is te vinden in het tabblad **Netwerknetwerk van** de sectie &gt; Virtuele **machine-instellingen).** &gt; **Virtual Machine**
   - _RecoveryPlanName_**-StorageAccountName:** De naam van het opslagaccount waarin het script (dat moet worden uitgevoerd op de mislukte vm) wordt opgeslagen. Dit kan elk opslagaccount zijn dat enige ruimte heeft om het script tijdelijk op te slaan.
   - _RecoveryPlanName_**-StorageAccountKey:** de toegangssleutel voor het bovenstaande opslagaccount.
   - _RecoveryPlanName_**-VMGUIDS:** Bij het beveiligen van een VM wijst Azure Site Recovery elke VM een unieke ID toe die de details van de mislukte overVM geeft. Als u de VMGUID wilt verkrijgen, selecteert u het tabblad **Herstelservices** en klikt u op &gt; **Eigenschappen**van machines voor **beveiligde** **objectbeveiliging** &gt; **.** &gt; Als u meerdere VM's hebt, voegt u de GUID's toe als een door komma's gescheiden tekenreeks.

     Als de naam van het herstelplan bijvoorbeeld fileServerpredayRP is, moet het tabblad **Variabelen,** **Verbindingen** en **certificaten** als volgt worden weergegeven nadat u alle elementen hebt toegevoegd.

      ![Assets](./media/storsimple-disaster-recovery-using-azure-site-recovery/image5.png)

1. Upload de Runbook-module uit de StorSimple 8000-serie in uw Automatiseringsaccount. Gebruik de onderstaande stappen om een module toe te voegen:
   
   1. Open powershell, maak een nieuwe map & map in de map wijzigen.
      
      ```
            mkdir C:\scripts\StorSimpleSDKTools
            cd C:\scripts\StorSimpleSDKTools
      ```
   1. Download nuget CLI onder dezelfde map in Stap1.
      Verschillende versies van nuget.exe zijn beschikbaar op [nuget downloads](https://www.nuget.org/downloads). Elke downloadkoppeling verwijst rechtstreeks naar een .exe-bestand, dus zorg ervoor dat u met de rechtermuisknop klikt en het bestand op uw computer opslaat in plaats van het vanuit de browser uit te voeren.
      
      ```
            wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
      ```
      
   1. Download de afhankelijke SDK
      
      ```
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
      ```
      
   1. Maak een Azure Automation Runbook Module voor apparaatbeheer uit de StorSimple 8000-serie. Gebruik de onderstaande opdrachten om een zip-bestand van de automatiseringsmodule te maken.
         
      ```powershell
            # set path variables
            $downloadDir = "C:\scripts\StorSimpleSDKTools"
            $moduleDir = "$downloadDir\AutomationModule\Microsoft.Azure.Management.StorSimple8000Series"

            #don't change the folder name "Microsoft.Azure.Management.StorSimple8000Series"
            mkdir "$moduleDir"

            copy "$downloadDir\Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\Microsoft.IdentityModel.Clients.ActiveDirectory*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\Microsoft.Rest.ClientRuntime.Azure*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Rest.ClientRuntime.2.3.8\lib\net452\Microsoft.Rest.ClientRuntime*.dll" $moduleDir
            copy "$downloadDir\Newtonsoft.Json.6.0.8\lib\net45\Newtonsoft.Json*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.Authentication.2.2.9-preview\lib\net45\Microsoft.Rest.ClientRuntime.Azure.Authentication*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Azure.Management.Storsimple8000series.1.0.0\lib\net452\Microsoft.Azure.Management.Storsimple8000series*.dll" $moduleDir

            #Don't change the name of the Archive
            compress-Archive -Path "$moduleDir" -DestinationPath Microsoft.Azure.Management.StorSimple8000Series.zip
      ```
         
   1. Het zip-bestand azure automation-module (Microsoft.Azure.Management.StorSimple8000Series.zip) importeren dat in bovenstaande stap is gemaakt. Dit kan door het automatiseringsaccount te selecteren, op **Modules** onder GEDEELDE RESOURCES te klikken en vervolgens op **Een module toevoegen**te klikken.
   
   Nadat u de module storSimple 8000-serie hebt geïmporteerd, moet het tabblad **Modules** als volgt worden weergegeven:
   
      ![Modules](./media/storsimple-disaster-recovery-using-azure-site-recovery/image12.png)

1. Ga naar de sectie **Herstelservices** en selecteer de azure site recovery-kluis die u eerder hebt gemaakt.
1. Selecteer de optie **Herstelplannen (Siteherstel)** in de groep **Beheren** en maak als volgt een nieuw herstelplan:
   
   - Klik **op + Herstel plan** knop, opent onder het blad.
      
      ![Herstelplan maken](./media/storsimple-disaster-recovery-using-azure-site-recovery/image6.png)
      
   - Voer de naam van een herstelplan in, kies Bron, Doel & Implementatiemodelwaarden.
   
   - Selecteer de VM's in de beveiligingsgroep die u in het herstelplan wilt opnemen en klik op de knop **OK.**
   
   - Selecteer Herstelplan dat u eerder hebt gemaakt, klik **op Knop Aanpassen** om de aanpassingsweergave herstelplan te openen.
   
   - Klik met de rechtermuisknop op **Alle groepen afsluiten** en klik op **Vooractie toevoegen**.
   
   - Hiermee opent u het actieblad invoegen, voert u een naam in, selecteert u De optie **Primaire zijde** in Waar moet u uitvoeren, selecteert u Het account van automatisering (waarin u de runbooks hebt toegevoegd) en selecteert u vervolgens het runbook **Failover-StorSimple-Volume-Containers.**
   
   - Klik met de rechtermuisknop op **Groep 1: Start** en klik op De optie **Beveiligde items toevoegen** en selecteer vervolgens de VM's die moeten worden beveiligd in het herstelplan en klik op **Ok.** Optioneel, als het al is geselecteerd VM's.
   
   - Klik met de rechtermuisknop op **Groep 1: Start** en klik op **Actie plaatsen,** voeg vervolgens alle volgende scripts toe:  
      
      - Runbook start-StorSimple-Virtual-Appliance  
      - Runbook runbook over-StorSimple-volume-containers niet mislukken  
      - Mont-volumes-after-failover runbook  
      - Runbook runbook voor aangepaste script-extensie verwijderen  
        
   - Voeg een handmatige actie toe na de bovenstaande 4 scripts in dezelfde **sectie Groep 1: Post-steps.** Deze actie is het punt waarop u controleren of alles correct werkt. Deze actie hoeft alleen te worden toegevoegd als onderdeel van Failover testen (schakel dus alleen het selectievakje **Failover** testen in).
    
   - Voeg na de handmatige actie het **script opschonen** toe volgens dezelfde procedure die u voor de andere runbooks hebt gebruikt. Sla het herstelplan **op.**
    
   > [!NOTE]
   > Wanneer u een testfailover uitvoert, moet u alles verifiëren bij de handmatige actiestap, omdat de StorSimple-volumes die op het doelapparaat waren gekloond, worden verwijderd als onderdeel van de opruiming nadat de handmatige actie is voltooid.
       
      ![Herstelplan](./media/storsimple-disaster-recovery-using-azure-site-recovery/image7.png)

## <a name="perform-a-test-failover"></a>Een testfailover uitvoeren
Raadpleeg de companionguide [van Active Directory DR-oplossing](../site-recovery/site-recovery-active-directory.md) voor overwegingen die specifiek zijn voor Active Directory tijdens de failover van de test. De on-premises setup wordt helemaal niet verstoord wanneer de testfailover optreedt. De StorSimple-volumes die aan de on-premises VM zijn gekoppeld, worden gekloond op het StorSimple Cloud Appliance op Azure. Een VM voor testdoeleinden wordt weergegeven in Azure en de gekloonde volumes zijn gekoppeld aan de VM.

#### <a name="to-perform-the-test-failover"></a>De testfailover uitvoeren
1. Selecteer in de Azure-portal de kluis Siteherstel.
1. Klik op het herstelplan dat is gemaakt voor de vm van de bestandsserver.
1. Klik **op Failover testen**.
1. Selecteer het virtuele Azure-netwerk waaraan Azure VM's zijn verbonden nadat er een fail-over is gemaakt.
   
   ![Failover starten](./media/storsimple-disaster-recovery-using-azure-site-recovery/image8.png)
   
1. Klik op **OK** om te beginnen met de failover. U de voortgang bijhouden door op de VM te klikken om de eigenschappen ervan te openen of op de **failovertaak testen** in taak taken voor taak van de taak van &gt; **de taak voor herstel** &gt; van de vault-site . **Site Recovery jobs**
1. Nadat de failover is voltooid, moet u ook de replica Azure-machine kunnen zien verschijnen in de &gt; **Azure-portal Virtuele machines.** U uw validaties uitvoeren.
1. Nadat de validaties zijn voltooid, klikt u op **Validaties Voltooien**. Dit verwijdert de StorSimple Volumes en sluit het StorSimple Cloud Appliance af.
1. Zodra u klaar bent, klikt u op **Failover opdeedtest op** het herstelplan. Leg in Notities eventuele opmerkingen over de testfailover vast en sla deze op. Hiermee wordt de virtuele machine verwijderd die is gemaakt tijdens de failover van de test.

## <a name="perform-a-planned-failover"></a>Een geplande failover uitvoeren
   Tijdens een geplande failover wordt de on-premises bestandsserver VM op een elegante manier afgesloten en wordt een cloudback-upmomentopname gemaakt van de volumes op het StorSimple-apparaat. De StorSimple-volumes zijn niet overnaar het virtuele apparaat, een replica-VM wordt weergegeven op Azure en de volumes zijn gekoppeld aan de VM.

#### <a name="to-perform-a-planned-failover"></a>Een geplande failover uitvoeren
1. Selecteer **herstelplannen** &gt; **(Site Recovery)** &gt; recoveryplan_name **die** zijn gemaakt voor de bestandsserver-VM in de Azure-portal.
1. Klik op het blade van het herstelplan op **Meer** &gt; **geplande failover**.  

   ![Herstelplan](./media/storsimple-disaster-recovery-using-azure-site-recovery/image9.png)
1. Kies op het **plan mislukte blad bevestigen** de bron- en doellocaties en selecteer het doelnetwerk en klik op het controlepictogram ✓ om het failoverproces te starten.
1. Nadat virtuele replica-machines zijn gemaakt, bevinden ze zich in een status waarvoor een commit in behandeling is. Klik **op Commit** om de failover te plegen.
1. Nadat de replicatie is voltooid, starten de virtuele machines op de secundaire locatie.

## <a name="perform-a-failover"></a>Een failover uitvoeren
Tijdens een ongeplande failover worden de StorSimple-volumes niet overgeslagen naar het virtuele apparaat, wordt een replica-VM weergegeven op Azure en worden de volumes aan de VM gekoppeld.

#### <a name="to-perform-a-failover"></a>Een failover uitvoeren
1. Selecteer **herstelplannen** &gt; **(Site Recovery)** &gt; recoveryplan_name **die** zijn gemaakt voor de bestandsserver-VM in de Azure-portal.
1. Klik op het blade van het herstelplan op **Meer** &gt; **failover**.  
1. Kies op het **failoverblad bevestigen** de bron- en doellocaties.
1. Selecteer **Virtuele machines afsluiten en de nieuwste gegevens synchroniseren** om op te geven dat Site Recovery moet proberen de beveiligde virtuele machine af te sluiten en de gegevens te synchroniseren, zodat de nieuwste versie van de gegevens niet meer wordt uitgevoerd.
1. Na de failover bevinden de virtuele machines zich in een commit in afwachting van de status. Klik **op Commit** om de failover te plegen.


## <a name="perform-a-failback"></a>Failback uitvoeren
Tijdens een failback worden StorSimple-volumecontainers niet meer naar het fysieke apparaat gegaan nadat een back-up is genomen.

#### <a name="to-perform-a-failback"></a>Een failback uitvoeren
1. Selecteer **herstelplannen** &gt; **(Site Recovery)** &gt; recoveryplan_name **die** zijn gemaakt voor de bestandsserver-VM in de Azure-portal.
1. Klik op het blade van het herstelplan op **Meer** &gt; **geplande failover**.  
1. Kies de bron- en doellocaties en selecteer de juiste opties voor gegevenssynchronisatie en het maken van vm's.
1. Klik op de knop **OK** om het failbackproces te starten.
   
   ![Failback starten](./media/storsimple-disaster-recovery-using-azure-site-recovery/image10.png)

## <a name="best-practices"></a>Beste praktijken
### <a name="capacity-planning-and-readiness-assessment"></a>Capaciteitsplanning en gereedheidsbeoordeling
#### <a name="hyper-v-site"></a>Hyper-V-site
Gebruik het [hulpprogramma voor de planner voor gebruikerscapaciteit](https://www.microsoft.com/download/details.aspx?id=39057) om de server-, opslag- en netwerkinfrastructuur voor uw Hyper-V-replica-omgeving te ontwerpen.

#### <a name="azure"></a>Azure
U het hulpprogramma voor de [beoordeling van azure virtual machine readiness assessment](https://azure.microsoft.com/downloads/vm-readiness-assessment/) uitvoeren op VM's om ervoor te zorgen dat deze compatibel zijn met Azure VM's en Azure Site Recovery Services. Het hulpprogramma voor gereedheidsbeoordeling controleert VM-configuraties en waarschuwt wanneer configuraties niet compatibel zijn met Azure. Het geeft bijvoorbeeld een waarschuwing als een C: schijf groter is dan 127 GB.

Capaciteitsplanning bestaat uit ten minste twee belangrijke processen:

   - On-premises Hyper-V VM's toewijzen aan Azure VM-formaten (zoals A6, A7, A8 en A9).
   - Het bepalen van de vereiste internetbandbreedte.

## <a name="limitations"></a>Beperkingen
- Momenteel kan alleen 1 StorSimple-apparaat worden overgelaten (naar één StorSimple Cloud Appliance). Het scenario van een bestandsserver die meerdere StorSimple-apparaten omspant, wordt nog niet ondersteund.
- Als u een fout krijgt terwijl u beveiliging voor een virtuele machine inschakelt, moet u ervoor zorgen dat de iSCSI-doelen zijn losgekoppeld.
- Alle volumecontainers die zijn gegroepeerd vanwege back-upbeleid dat zich uitstrekt over volumecontainers, worden samen mislukt.
- Alle volumes in de door u gekozen volumecontainers worden niet meer weergegeven.
- Volumes met een capaciteit van meer dan 64 TB kunnen niet worden mislukt omdat de maximale capaciteit van een enkele StorSimple Cloud Appliance 64 TB is.
- Als de geplande/ongeplande failover mislukt en de VM's in Azure worden gemaakt, ruim dan de VM's niet op. In plaats daarvan, doe een failback. Als u de VM's verwijdert, kunnen de on-premises VM's niet opnieuw worden ingeschakeld.
- Als u na een failover de volumes niet zien, gaat u naar de VM's, opent u Schijfbeheer, scant u de schijven opnieuw en brengt u ze vervolgens online.
- In sommige gevallen kunnen de stationsletters op de DR-site anders zijn dan de letters on-premises. Als dit gebeurt, moet u het probleem handmatig verhelpen nadat de failover is voltooid.
- Failovertaaktime-out: Het StorSimple-script geeft een time-out als de failover van volumecontainers meer tijd in beslag neemt dan de Azure Site Recovery-limiet per script (momenteel 120 minuten).
- Time-out van back-ups: het StorSimple-script time-out als de back-up van volumes meer tijd in beslag neemt dan de Azure Site Recovery-limiet per script (momenteel 120 minuten).
   
  > [!IMPORTANT]
  > Voer de back-up handmatig uit vanuit de Azure-portal en voer het herstelplan opnieuw uit.
   
- Time-out van de kloontaak: het StorSimple-script time-out als het klonen van volumes meer tijd kost dan de Azure Site Recovery-limiet per script (momenteel 120 minuten).
- Tijdsynchronisatiefout: De StorSimple scripts fouten uit te zeggen dat de back-ups niet succesvol waren, hoewel de back-up succesvol is in de portal. Een mogelijke oorzaak hiervoor kan zijn dat de tijd van het StorSimple-toestel mogelijk niet synchroon loopt met de huidige tijd in de tijdzone.
   
  > [!IMPORTANT]
  > Synchroniseer de toesteltijd met de huidige tijd in de tijdzone.
   
- Fout van toestelfailover: Het Script Van StorSimple kan mislukken als er een failover van het toestel optreedt wanneer het herstelplan wordt uitgevoerd.
   
  > [!IMPORTANT]
  > Voer het herstelplan opnieuw uit nadat de failover van het apparaat is voltooid.


## <a name="summary"></a>Samenvatting
Met Azure Site Recovery u een volledig geautomatiseerd noodherstelplan maken voor een bestandsserver-vm met bestandsshares die worden gehost op StorSimple-opslag. U de failover binnen enkele seconden starten vanaf elke locatie in het geval van een onderbreking en de toepassing binnen enkele minuten operationeel krijgen.
