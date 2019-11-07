---
title: Problemen met doorlopende replicatie van Azrue-Vm's met Azure Site Recovery oplossen
description: Problemen en problemen oplossen bij het repliceren van virtuele machines van Azure voor herstel na nood gevallen
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 8/2/2019
ms.author: asgang
ms.openlocfilehash: 7b9da202704b20e5770343f857c044ea19ae696a
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73620886"
---
# <a name="troubleshoot-ongoing-problems-in-azure-to-azure-vm-replication"></a>Lopende problemen in azure-naar-Azure-VM-replicatie oplossen

In dit artikel worden veelvoorkomende problemen in Azure Site Recovery beschreven wanneer u virtuele machines van Azure repliceert en herstelt vanuit de ene regio naar een andere regio. Ook wordt uitgelegd hoe u problemen kunt oplossen. Zie de [ondersteunings matrix voor het repliceren van virtuele Azure-machines](site-recovery-support-matrix-azure-to-azure.md)voor meer informatie over ondersteunde configuraties.

Azure Site Recovery repliceert gegevens van de bron regio consistent naar de herstel regio voor nood gevallen en maakt elke vijf minuten een crash-consistent herstel punt. Als Site Recovery geen herstel punten voor 60 minuten kunt maken, wordt u hiervan op de hoogte door deze informatie:

Fout bericht: er is in de afgelopen 60 minuten geen crash consistent herstel punt beschikbaar voor de VM.</br>
Fout-ID: 153007 </br>

In de volgende secties worden oorzaken en oplossingen beschreven.

## <a name="high-data-change-rate-on-the-source-virtal-machine"></a>Hoge wijzigings frequentie voor gegevens op de virtuele bron machine
Azure Site Recovery een gebeurtenis wordt geactiveerd als de wijzigings frequentie van de gegevens op de virtuele bron machine hoger is dan de ondersteunde limieten. Als u wilt controleren of het probleem wordt veroorzaakt door een hoog verloop, gaat u naar **gerepliceerde items** > **VM** > **Events-afgelopen 72 uur**.
U moet de gebeurtenis ' gegevens wijzigings frequentie boven ondersteunde limieten ' zien:

![data_change_rate_high](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png)

Als u de gebeurtenis selecteert, ziet u de exacte schijf gegevens:

![data_change_rate_event](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png)


### <a name="azure-site-recovery-limits"></a>Azure Site Recovery-limieten
De volgende tabel bevat de Azure Site Recovery-limieten. Deze limieten zijn gebaseerd op onze tests, maar ze kunnen niet alle mogelijke toepassings-I/O-combi Naties bedekken. De werkelijke resultaten kunnen variëren op basis van uw toepassings-I/O-combinatie. 

Er zijn twee limieten die u moet overwegen: gegevens verloop per schijf en gegevens verloop per virtuele machine. Laten we bijvoorbeeld eens kijken naar de Premium P20-schijf in de volgende tabel. Site Recovery kan 5 MB/s aan verloop per schijf verwerken met een maximum van vijf schijven per VM, als gevolg van de limiet van 25 MB/s van het totale verloop per VM.

**Beoogde replicatieopslag** | **Gemiddelde I/O-grootte voor de bron schijf** |**Gemiddeld gegevens verloop van bron schijf** | **Totaal gegevens verloop per dag voor bron gegevens schijf**
---|---|---|---
Standard Storage | 8 kB | 2 MB/s | 168 GB per schijf
Premium P10 of P15 schijf | 8 kB  | 2 MB/s | 168 GB per schijf
Premium P10 of P15 schijf | 16 kB | 4 MB/s |  336 GB per schijf
Premium P10 of P15 schijf | 32 kB of meer | 8 MB/s | 672 GB per schijf
Premium P20 of P30 of P40 of P50 schijf | 8 kB    | 5 MB/s | 421 GB per schijf
Premium P20 of P30 of P40 of P50 schijf | 16 kB of meer |10 MB/s | 842 GB per schijf

### <a name="solution"></a>Oplossing
Azure Site Recovery heeft limieten voor de snelheid van schijfgegegevenswijzigingen, gebaseerd op het type schijf. Als u wilt weten of het probleem zich voordoet, kunt u de gegevens wijzigings frequentie van de betreffende virtuele machine vinden. Ga naar de virtuele bron machine, zoek de metrische gegevens onder **bewaking**en voeg de metrische gegevens toe zoals in deze scherm opname wordt weer gegeven:

![Proces met drie stappen voor het zoeken van de gegevens wijzigings frequentie](./media/site-recovery-azure-to-azure-troubleshoot/churn.png)

1. Selecteer **metrische gegevens toevoegen**en voeg de **besturingssysteem schijf toe geschreven bytes per seconde** en **geschreven bytes per seconde voor de gegevens schijf**.
2. De piek bewaken, zoals weer gegeven in de scherm opname.
3. Bekijk het totale aantal schrijf bewerkingen dat wordt uitgevoerd op de schijven van het besturings systeem en alle gegevens schijven gecombineerd. Deze metrische gegevens geven mogelijk geen informatie op het niveau per schijf, maar geven het totale patroon van het verloop van gegevens aan.

Als een piek van een incidenteel gegevens burst is en de wijzigings frequentie van gegevens groter is dan 10 MB/s (voor Premium) en 2 MB/s (voor Standard) gedurende een bepaalde periode en niet beschikbaar is, wordt de replicatie weer gegeven. Maar als het verloop duidelijker is dan de Maxi maal ondersteunde limiet, overweeg dan een van de volgende opties, indien mogelijk:

* **Sluit de schijf uit die een hoge mate van gegevens wijziging veroorzaakt**: u kunt de schijf uitsluiten met behulp van [Power shell](./azure-to-azure-exclude-disks.md). Als u de schijf wilt uitsluiten, moet u eerst de replicatie uitschakelen. 
* **De laag van de opslag schijf voor nood herstel wijzigen**: deze optie kan alleen worden uitgevoerd als het verloop van de schijf gegevens lager is dan 20 MB/s. Stel dat een VM met een P10-schijf een gegevens verloop heeft dat groter is dan 8 MB/s, maar kleiner is dan 10 MB/s. Als de klant een P30-schijf kan gebruiken voor doel opslag tijdens de beveiliging, kan het probleem worden opgelost. Houd er rekening mee dat deze oplossing alleen mogelijk is voor machines die Premium-Managed Disks gebruiken. Volg de onderstaande stappen:
    - Navigeer naar de Blade schijven van de betrokken gerepliceerde machine en kopieer de naam van de replica schijf
    - Ga naar deze door de replica beheerde schijf
    - Mogelijk ziet u een banner op de Blade overzicht met de melding dat er een SAS-URL is gegenereerd. Klik op deze banner en Annuleer de export. Negeer deze stap als u de banner niet ziet.
    - Zodra de SAS-URL is ingetrokken, gaat u naar de Blade configuratie van de beheerde schijf en verhoogt u de grootte zodat ASR het waargenomen verloop op de bron schijf ondersteunt

## <a name="Network-connectivity-problem"></a>Problemen met de netwerk verbinding

### <a name="network-latency-to-a-cache-storage-account"></a>Netwerk latentie naar een cache-opslag account
Site Recovery worden gerepliceerde gegevens naar het cache-opslag account verzonden. U ziet mogelijk netwerk latentie als het uploaden van de gegevens van een virtuele machine naar het cache-opslag account langzamer is dan 4 MB in drie seconden. 

Als u wilt controleren of er een probleem is met de latentie, gebruikt u [azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) om gegevens te uploaden van de virtuele machine naar het cache-opslag account. Als de latentie hoog is, controleert u of u een virtueel netwerk apparaat (NVA) gebruikt om het uitgaande netwerk verkeer van Vm's te beheren. Het apparaat kan worden beperkt als alle replicatie verkeer via de NVA wordt door gegeven. 

U kunt het beste een netwerk service-eind punt in uw virtuele netwerk maken voor ' opslag ', zodat het replicatie verkeer niet naar de NVA gaat. Zie [configuratie van virtueel netwerk apparaat](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#network-virtual-appliance-configuration)voor meer informatie.

### <a name="network-connectivity"></a>Netwerk verbinding
Voor een goede werking van Site Recovery replicatie is uitgaande verbinding met specifieke Url's of IP-adresbereiken vereist van de virtuele machine. Als uw virtuele machine zich achter een firewall bevindt of gebruikmaakt van regels voor netwerk beveiligings groepen (NSG) om de uitgaande connectiviteit te beheren, kunt u een van deze problemen tegen komen. Zie [uitgaande connectiviteit voor site Recovery url's](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges)om ervoor te zorgen dat alle url's zijn verbonden. 

## <a name="error-id-153006---no-app-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>Fout-ID 153006-geen app-consistent herstel punt beschikbaar voor de virtuele machine in de afgelopen ' XXX ' minuten

Hieronder vindt u enkele van de meest voorkomende problemen

#### <a name="cause-1-known-issue-in-sql-server-20082008-r2"></a>Oorzaak 1: bekend probleem in SQL Server 2008/2008 R2 
**Oplossen** : er is een bekend probleem met SQL Server 2008/2008 R2. Raadpleeg dit KB-artikel [Azure site Recovery agent of andere VSS-back-ups van niet-onderdeel zijn mislukt voor een server die als host fungeert voor SQL Server 2008 R2](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2)

#### <a name="cause-2-azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>Oorzaak 2: Azure Site Recovery taken mislukken op servers die fungeren als host voor elke versie van SQL Server instanties met AUTO_SLUITEN Db's 
**Oplossen** : verwijzen naar KB- [artikel](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser) 


#### <a name="cause-3-known-issue-in-sql-server-2016-and-2017"></a>Oorzaak 3: bekend probleem in SQL Server 2016 en 2017
**Oplossen** : verwijzen naar KB- [artikel](https://support.microsoft.com/help/4493364/fix-error-occurs-when-you-back-up-a-virtual-machine-with-non-component) 

#### <a name="cause-4-you-are-using-storage-spaces-direct-configuration"></a>Oorzaak 4: u maakt gebruik van opslag ruimten direct-configuratie
**Oplossen** : Azure site Recovery kan geen toepassings consistent herstel punt maken voor de configuratie van opslag ruimten direct. Raadpleeg het artikel om [het replicatie beleid](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication-s2d-vms) correct te configureren

### <a name="more-causes-due-to-vss-related-issues"></a>Meer oorzaken vanwege VSS-problemen:

Als u verder wilt oplossen, controleert u de bestanden op de bron computer om de exacte fout code op te halen voor de fout:
    
    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log

Hoe kunt u de fouten in het bestand vinden?
Zoek naar de teken reeks ' vacpError ' door het bestand vacp. log te openen in een editor
        
    Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|

In bovenstaand voor beeld **2147754994** is de fout code die u vertelt over de fout, zoals hieronder wordt weer gegeven

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>VSS Writer is niet geïnstalleerd-fout 2147221164 

*Oplossen*: voor het genereren van een toepassings consistentie code Azure site Recovery maakt gebruik van micro soft Volume Shadow Copy service (VSS). Er wordt een VSS-provider geïnstalleerd voor de werking ervan om app-consistentie momentopnamen te maken. Deze VSS-provider is geïnstalleerd als een service. Als de VSS-Provider service niet is geïnstalleerd, mislukt het maken van de toepassings consistentie van de moment opname met de fout-id 0x80040154 ' klasse is niet geregistreerd '. </br>
Raadpleeg het [artikel over het oplossen van problemen met de VSS Writer-installatie](https://docs.microsoft.com/azure/site-recovery/vmware-azure-troubleshoot-push-install#vss-installation-failures) 

#### <a name="vss-writer-is-disabled---error-2147943458"></a>VSS Writer is uitgeschakeld-fout 2147943458

**Oplossen**: voor het genereren van een toepassings consistentie code Azure site Recovery maakt gebruik van micro soft Volume Shadow Copy service (VSS). Er wordt een VSS-provider geïnstalleerd voor de werking ervan om app-consistentie momentopnamen te maken. Deze VSS-provider is geïnstalleerd als een service. Als de VSS-Provider service is uitgeschakeld, mislukt het maken van de moment opname van de toepassings consistentie met de fout-id. de opgegeven service is uitgeschakeld en kan niet worden gestart (0x80070422). </br>

- Als VSS is uitgeschakeld,
    - Controleer of het opstart type van de VSS-Provider service is ingesteld op **automatisch**.
    - Start de volgende services opnieuw:
        - VSS-service
        - Azure Site Recovery VSS-provider
        - VDS-service

####  <a name="vss-provider-not_registered---error-2147754756"></a>VSS-PROVIDER NOT_REGISTERED-fout 2147754756

**Oplossen**: voor het genereren van een toepassings consistentie code Azure site Recovery maakt gebruik van micro soft Volume Shadow Copy service (VSS). Controleer of de Azure Site Recovery VSS-Provider service is geïnstalleerd of niet. </br>

- Voer de installatie van de provider opnieuw uit met de volgende opdrachten:
- Bestaande provider verwijderen: C:\Program Files (x86) \Microsoft Azure site Recovery\agent\InMageVSSProvider_Uninstall.cmd
- Opnieuw installeren: C:\Program Files (x86) \Microsoft Azure site Recovery\agent\InMageVSSProvider_Install.cmd
 
Controleer of het opstart type van de VSS-Provider service is ingesteld op **automatisch**.
    - Start de volgende services opnieuw:
        - VSS-service
        - Azure Site Recovery VSS-provider
        - VDS-service
