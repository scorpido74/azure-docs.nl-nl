---
title: Problemen met replicatie van virtuele Azure-machines met Azure Site Recovery oplossen
description: Problemen met de replicatie in azure VM-nood herstel oplossen met Azure Site Recovery
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 8/2/2019
ms.openlocfilehash: e5e52c6e8560c7369054cfc9fcf2ba4c405671e0
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190811"
---
# <a name="troubleshoot-replication-in-azure-vm-disaster-recovery"></a>Problemen met replicatie in nood herstel voor Azure VM oplossen

In dit artikel worden veelvoorkomende problemen in Azure Site Recovery beschreven wanneer u virtuele machines van Azure repliceert en herstelt vanuit de ene regio naar een andere regio. Ook wordt uitgelegd hoe u veelvoorkomende problemen oplost. Zie de [ondersteunings matrix voor het repliceren van virtuele Azure-machines](site-recovery-support-matrix-azure-to-azure.md)voor meer informatie over ondersteunde configuraties.

Azure Site Recovery gegevens van de bron regio consistent repliceren naar de herstel regio voor nood gevallen. Er wordt elke vijf minuten een crash-consistent herstel punt gemaakt. Als Site Recovery geen herstel punten voor 60 minuten kunt maken, wordt u hiervan op de hoogte door deze informatie:

Fout bericht: er is in de afgelopen 60 minuten geen crash consistent herstel punt beschikbaar voor de VM.</br>
Fout-ID: 153007

In de volgende secties worden oorzaken en oplossingen beschreven.

## <a name="high-data-change-rate-on-the-source-virtal-machine"></a>Hoge wijzigings frequentie voor gegevens op de virtuele bron machine

Azure Site Recovery maakt een gebeurtenis als de wijzigings frequentie van de gegevens op de virtuele bron machine hoger is dan de ondersteunde limieten. Als u wilt weten of het probleem wordt veroorzaakt door een hoog verloop, gaat u naar **gerepliceerde items** > **VM** > **Events-afgelopen 72 uur**.
U moet de gebeurtenis ' gegevens wijzigings frequentie boven ondersteunde limieten ' zien:

![Azure Site Recovery pagina met een hoge wijzigings frequentie voor gegevens die te hoog is](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png)

Als u de gebeurtenis selecteert, ziet u de exacte schijf gegevens:

![Pagina waarop de details van de gebeurtenis gegevens wijzigings frequentie worden weer gegeven](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png)

### <a name="azure-site-recovery-limits"></a>Azure Site Recovery-limieten

De volgende tabel bevat de Azure Site Recovery-limieten. Deze limieten zijn gebaseerd op onze tests, maar ze kunnen niet alle mogelijke combi Naties van invoer uitvoer (I/O) voor toepassingen bedekken. De werkelijke resultaten kunnen variëren op basis van uw toepassings-I/O-combinatie.

Er zijn twee limieten die u moet overwegen: gegevens verloop per schijf en gegevens verloop per virtuele machine. Laten we eens kijken naar de Premium P20-schijf in de volgende tabel voor een voor beeld. Voor één virtuele machine kan Site Recovery 5 MB/s aan verloop per schijf verwerken met een maximum van vijf dergelijke schijven. Site Recovery heeft een limiet van 25 MB/s van het totale verloop per VM.

**Beoogde replicatieopslag** | **Gemiddelde I/O-grootte voor de bron schijf** |**Gemiddeld gegevens verloop van bron schijf** | **Totaal gegevens verloop per dag voor bron gegevens schijf**
---|---|---|---
Standard Storage | 8 kB | 2 MB/s | 168 GB per schijf
Premium P10 of P15 schijf | 8 kB  | 2 MB/s | 168 GB per schijf
Premium P10 of P15 schijf | 16 kB | 4 MB/s |  336 GB per schijf
Premium P10 of P15 schijf | 32 kB of meer | 8 MB/s | 672 GB per schijf
Premium P20 of P30 of P40 of P50 schijf | 8 kB    | 5 MB/s | 421 GB per schijf
Premium P20 of P30 of P40 of P50 schijf | 16 kB of meer |10 MB/s | 842 GB per schijf

### <a name="solution"></a>Oplossing

Azure Site Recovery heeft limieten voor de wijzigings snelheid van gegevens, afhankelijk van het type schijf. Als u wilt zien of dit probleem terugkerend of tijdelijk is, zoekt u de gegevens wijzigings frequentie van de betreffende virtuele machine. Ga naar de virtuele bron machine, zoek de metrische gegevens onder **bewaking**en voeg de metrische gegevens toe zoals in deze scherm opname wordt weer gegeven:

![Pagina met het proces met drie stappen voor het zoeken van de gegevens wijzigings frequentie](./media/site-recovery-azure-to-azure-troubleshoot/churn.png)

1. Selecteer **metrische gegevens toevoegen**en voeg de **besturingssysteem schijf toe geschreven bytes per seconde** en **geschreven bytes per seconde voor de gegevens schijf**.
1. De piek bewaken, zoals weer gegeven in de scherm opname.
1. Bekijk het totale aantal schrijf bewerkingen dat wordt uitgevoerd op de schijven van het besturings systeem en alle gegevens schijven gecombineerd. Deze metrische gegevens geven mogelijk geen informatie op het niveau per schijf, maar geven het totale patroon van het verloop van gegevens aan.

Een piek in de gegevens wijzigings verhouding kan afkomstig zijn van een incidentele gegevens burst. Als de waarde voor het wijzigen van de gegevens groter is dan 10 MB/s (voor Premium) of 2 MB/s (voor Standard) en is uitgeschakeld, wordt de replicatie weer gegeven. Als het verloop consistent is naast de ondersteunde limiet, kunt u een van de volgende opties overwegen:

- Sluit de schijf uit die een hoge mate van gegevens wijziging veroorzaakt: Schakel eerst de replicatie uit. Vervolgens kunt u de schijf uitsluiten met behulp van [Power shell](./azure-to-azure-exclude-disks.md).
- De laag van de opslag schijf voor nood herstel wijzigen: deze optie kan alleen worden uitgevoerd als het verloop van de schijf gegevens lager is dan 20 MB/s. Stel dat een VM met een P10-schijf een gegevens verloop heeft dat groter is dan 8 MB/s, maar kleiner is dan 10 MB/s. Als de klant een P30-schijf kan gebruiken voor doel opslag tijdens de beveiliging, kan het probleem worden opgelost. Deze oplossing is alleen mogelijk voor machines die gebruikmaken van Premium-Managed Disks. Volg deze stappen:

    1. Ga naar **schijven** van de betreffende gerepliceerde machine en kopieer de naam van de replica schijf.
    1. Ga naar deze replica van de beheerde schijf.
    1. U ziet mogelijk een banner in **overzicht** met de melding dat er een SAS-URL is gegenereerd. Selecteer deze banner en Annuleer de export. Sla deze stap over als u de banner niet ziet.
    1. Zodra de SAS-URL is ingetrokken, gaat u naar **configuratie** voor de beheerde schijf. Verg root de grootte zodat Site Recovery het waargenomen verloop op de bron schijf ondersteunt.

## <a name="Network-connectivity-problem"></a>Problemen met de netwerk verbinding

### <a name="network-latency-to-a-cache-storage-account"></a>Netwerk latentie naar een cache-opslag account

Site Recovery worden gerepliceerde gegevens naar het cache-opslag account verzonden. U kunt de netwerk latentie ondervinden als het uploaden van de gegevens van een virtuele machine naar het cache-opslag account langzamer is dan 4 MB in drie seconden.

Gebruik [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)om te controleren of er een probleem is met de latentie. U kunt dit opdracht regel hulpprogramma gebruiken voor het uploaden van gegevens van de virtuele machine naar het cache-opslag account. Als de latentie hoog is, controleert u of u een virtueel netwerk apparaat (NVA) gebruikt om het uitgaande netwerk verkeer van Vm's te beheren. Het apparaat kan worden beperkt als alle replicatie verkeer via de NVA wordt door gegeven.

U kunt het beste een netwerk service-eind punt in uw virtuele netwerk maken voor ' opslag ', zodat het replicatie verkeer niet naar de NVA gaat. Zie [configuratie van virtueel netwerk apparaat](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#network-virtual-appliance-configuration)voor meer informatie.

### <a name="network-connectivity"></a>Netwerk verbinding

Site Recovery replicatie werkt alleen als de virtuele machine een uitgaande verbinding met specifieke Url's of IP-adresbereiken heeft. Mogelijk hebt u uw virtuele machine achter een firewall of gebruikt u NSG-regels (netwerk beveiligings groep) om de uitgaande verbinding te beheren. Als dat het geval is, kunnen er problemen optreden. Zie [uitgaande connectiviteit voor site Recovery url's](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges)om ervoor te zorgen dat alle url's zijn verbonden.

## <a name="error-id-153006---no-app-consistent-recovery-point-available-for-the-vm-in-the-past-x-minutes"></a>Fout-ID 153006-geen app-consistent herstel punt beschikbaar voor de virtuele machine in de afgelopen "X" minuten

Hier volgen enkele van de meest voorkomende problemen.

#### <a name="known-issue-in-sql-server-20082008-r2"></a>Bekend probleem in SQL Server 2008/2008 R2

**Oplossen**: er is een bekend probleem met SQL Server 2008/2008 R2. Raadpleeg het artikel [Azure site Recovery agent of andere VSS-back-ups van niet-onderdeel zijn mislukt voor een server die als host fungeert voor SQL Server 2008 R2](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2).

#### <a name="azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>Azure Site Recovery-taken mislukken op servers die als host fungeren voor elke versie van SQL Server instanties met AUTO_CLOSE Db's

**Oplossen**: Raadpleeg het artikel [VSS-back-ups zonder onderdelen, zoals Azure site Recovery taken mislukken op servers die SQL Server instanties hosten met AUTO_CLOSE db's](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser).


#### <a name="known-issue-in-sql-server-2016-and-2017"></a>Bekend probleem in SQL Server 2016 en 2017

**Oplossen**: Raadpleeg het artikel [fout treedt op wanneer u een back-up maakt van een virtuele machine met een back-up die geen onderdeel is van SQL Server 2016 en 2017](https://support.microsoft.com/help/4493364/fix-error-occurs-when-you-back-up-a-virtual-machine-with-non-component).

#### <a name="youre-using-azure-storage-spaces-direct-configuration"></a>U gebruikt Azure Storage Spaces direct-configuratie

**Oplossen**: Azure site Recovery kan geen toepassings consistent herstel punt maken voor opslagruimten direct configuratie. [Configureer het replicatie beleid](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication-s2d-vms).

### <a name="more-causes-because-of-vss-related-issues"></a>Meer oorzaken vanwege VSS-gerelateerde problemen:

Als u verder wilt oplossen, controleert u de bestanden op de bron computer om de exacte fout code op te halen voor de fout:

    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log

Zoek naar de teken reeks ' vacpError ' door het bestand vacp. log te openen in een editor om de fouten in het bestand te vinden.

    Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|

In het vorige voor beeld is **2147754994** de fout code die aangeeft dat er een fout is opgetreden na deze zin.

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>VSS Writer is niet geïnstalleerd-fout 2147221164

**Oplossen**: als u een toepassings consistentie label wilt genereren, gebruikt Azure site Recovery Volume Shadow Copy service (VSS). Site Recovery installeert een VSS-provider voor de bewerking om moment opnamen van de app-consistentie te maken. Azure Site Recovery installeert deze VSS-provider als een service. Als VSS-provider niet is geïnstalleerd, mislukt het maken van de toepassings consistentie van de moment opname. De fout-ID 0x80040154 ' klasse is niet geregistreerd ' wordt weer gegeven. Raadpleeg het artikel over het [oplossen van problemen met de VSS Writer-installatie](https://docs.microsoft.com/azure/site-recovery/vmware-azure-troubleshoot-push-install#vss-installation-failures).

#### <a name="vss-writer-is-disabled---error-2147943458"></a>VSS Writer is uitgeschakeld-fout 2147943458

**Oplossen**: voor het genereren van de toepassings consistentie code Azure site Recovery gebruikt VSS. Site Recovery installeert een VSS-provider voor de bewerking om moment opnamen van de app-consistentie te maken. Deze VSS-provider is geïnstalleerd als een service. Als u de VSS-Provider service niet hebt ingeschakeld, mislukt het maken van de toepassings consistentie van de moment opname. De fout ' de opgegeven service is uitgeschakeld en kan niet worden gestart (0x80070422) ' wordt weer gegeven.

Als VSS is uitgeschakeld:

- Controleer of het opstart type van de VSS-Provider service is ingesteld op **automatisch**.
- Start de volgende services opnieuw:
   - VSS-service
   - Azure Site Recovery VSS-provider
   - VDS-service

#### <a name="vss-provider-not_registered---error-2147754756"></a>NOT_REGISTERED van VSS-PROVIDER-fout 2147754756

**Oplossen**: voor het genereren van de toepassings consistentie code Azure site Recovery gebruikt VSS. Controleer of de Azure Site Recovery VSS-Provider service is geïnstalleerd.

Gebruik de volgende opdrachten om VSS-provider opnieuw te installeren:
1. Bestaande provider verwijderen: C:\Program Files (x86) \Microsoft Azure site Recovery\agent\ InMageVSSProvider_Uninstall. cmd
1. VSS-provider opnieuw installeren: C:\Program Files (x86) \Microsoft Azure site Recovery\agent\ InMageVSSProvider_Install. cmd

Controleer of het opstart type van de VSS-Provider service is ingesteld op **automatisch**.

Start de volgende services opnieuw:
- VSS-service
- Azure Site Recovery VSS-provider
- VDS-service
