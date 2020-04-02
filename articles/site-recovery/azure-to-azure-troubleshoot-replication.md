---
title: Problemen met replicatie van Azure VM's oplossen met Azure Site Recovery
description: Problemen met replicatie in Azure VM-noodherstel oplossen met Azure Site Recovery
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 8/2/2019
ms.openlocfilehash: c5389c064e137358670aecabe97f1cea38dfbcbf
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549277"
---
# <a name="troubleshoot-replication-in-azure-vm-disaster-recovery"></a>Problemen met replicatie in Azure VM-noodherstel oplossen

In dit artikel worden veelvoorkomende problemen in Azure Site Recovery beschreven wanneer u virtuele Azure-machines van de ene regio naar een andere regio repliceert en herstelt. Het legt ook uit hoe de gemeenschappelijke problemen op te lossen. Zie de [ondersteuningsmatrix voor het repliceren van Azure VM's voor](site-recovery-support-matrix-azure-to-azure.md)meer informatie over ondersteunde configuraties.

Azure Site Recovery repliceert gegevens uit het brongebied consequent naar het gebied voor noodherstel. Het creëert ook een crash-consistent herstelpunt om de 5 minuten. Als Site Recovery gedurende 60 minuten geen herstelpunten kan maken, wordt u met deze informatie gewaarschuwd:

Foutbericht: "Geen crash consistent herstelpunt beschikbaar voor de VM in de laatste 60 minuten."</br>
Fout-id: 153007

In de volgende secties worden oorzaken en oplossingen beschreven.

## <a name="high-data-change-rate-on-the-source-virtual-machine"></a><a name="high-data-change-rate-on-the-source-virtal-machine"></a>Hoge snelheid voor gegevenswijzigingen op de virtuele bronmachine

Azure Site Recovery maakt een gebeurtenis als de gegevenswijzigingssnelheid op de virtuele bronmachine hoger is dan de ondersteunde limieten. Als u wilt zien of het probleem te wijten is aan een hoge churn, gaat u naar **VM-gebeurtenissen voor gerepliceerde items** > **VM** > **- de afgelopen 72 uur**.
Zie de gebeurtenis 'Snelheid van gegevenswijziging voorbij ondersteunde limieten':

![Azure Site Recovery-pagina met een hoge gegevenswijzigingssnelheid die te hoog is](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png)

Als u de gebeurtenis selecteert, ziet u de exacte schijfgegevens:

![Pagina met de details van de gebeurtenisgegevenswijzigingssnelheid](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png)

### <a name="azure-site-recovery-limits"></a>Azure Site Recovery-limieten

De volgende tabel bevat de Azure Site Recovery-limieten. Deze limieten zijn gebaseerd op onze tests, maar kunnen niet alle mogelijke input-output (I/O) combinaties van toepassingen dekken. De werkelijke resultaten kunnen variëren op basis van uw toepassings-I/O-combinatie.

Er zijn twee limieten om rekening mee te houden: dataverloop per schijf en dataverloop per virtuele machine. Laten we eens kijken naar de Premium P20-schijf in de volgende tabel voor een voorbeeld. Voor één virtuele virtuele machine kan Site Recovery 5 MB/s churn per schijf verwerken met een maximum van vijf van dergelijke schijven. Site recovery heeft een limiet van 25 MB/s van de totale churn per VM.

**Replicatieopslagdoel** | **Gemiddelde I/O-grootte voor bronschijf** |**Gemiddelde gegevensverloop voor bronschijf** | **Totaal gegevensverloop per dag voor brongegevensschijf**
---|---|---|---
Standard Storage | 8 kB    | 2 MB/s | 168 GB per schijf
Premium P10 of P15 schijf | 8 kB    | 2 MB/s | 168 GB per schijf
Premium P10 of P15 schijf | 16 kB | 4 MB/s |    336 GB per schijf
Premium P10 of P15 schijf | 32 kB of meer | 8 MB/s | 672 GB per schijf
Premium P20 of P30 of P40 of P50 schijf | 8 kB    | 5 MB/s | 421 GB per schijf
Premium P20 of P30 of P40 of P50 schijf | 16 kB of meer |20 MB/s | 1684 GB per schijf

### <a name="solution"></a>Oplossing

Azure Site Recovery heeft beperkingen op de gegevenswijzigingssnelheid, afhankelijk van het type schijf. Als u wilt zien of dit probleem zich regelmatig of tijdelijk voordoet, zoekt u de gegevenswijzigingssnelheid van de getroffen virtuele machine. Ga naar de virtuele bronmachine, zoek de statistieken onder **Controle**en voeg de statistieken toe zoals in deze schermafbeelding wordt weergegeven:

![Pagina met het proces in drie stappen voor het vinden van de gegevenswijzigingssnelheid](./media/site-recovery-azure-to-azure-troubleshoot/churn.png)

1. Selecteer **Metrische gegevens toevoegen**en voeg bytes voor schrijven per seconde van de schijf van de **schijf** en **schrijfbytes per seconde voor gegevensschijven toe**.
1. Monitor de piek zoals weergegeven in de screenshot.
1. Bekijk de totale schrijfbewerkingen die plaatsvinden op osschijven en alle gecombineerde gegevensschijven. Deze statistieken geven u mogelijk geen informatie op het niveau per schijf, maar geven wel het totale patroon van gegevensverloop aan.

Een piek in de snelheid van gegevenswijzigingen kan afkomstig zijn van een incidentele gegevensuitbarsting. Als de gegevenswijzigingssnelheid groter is dan 10 MB/s (voor Premium) of 2 MB/s (voor standaard) en naar beneden komt, wordt de replicatie ingehaald. Als de churn consistent ver buiten de ondersteunde limiet ligt, overweeg dan een van de volgende opties:

- Sluit de schijf uit die een hoge gegevenswijzigingssnelheid veroorzaakt: schakel eerst de replicatie uit. Vervolgens u de schijf uitsluiten met [PowerShell](./azure-to-azure-exclude-disks.md).
- De laag van de opslagschijf voor noodherstel wijzigen: deze optie is alleen mogelijk als het verloop van de schijfgegevens minder dan 20 MB/s bedraagt. Stel dat een VM met een P10-schijf een gegevensverloop heeft van meer dan 8 MB/s, maar minder dan 10 MB/s. Als de klant een P30-schijf kan gebruiken voor doelopslag tijdens de beveiliging, kan het probleem worden opgelost. Deze oplossing is alleen mogelijk voor machines die Premium-Managed Disks gebruiken. Volg deze stappen:

    1. Ga naar **schijven** van de getroffen gerepliceerde machine en kopieer de naam van de replicaschijf.
    1. Ga naar deze replica van de beheerde schijf.
    1. Mogelijk ziet u een banner in **Overzicht** waarin staat dat er een SAS-URL is gegenereerd. Selecteer deze banner en annuleer de export. Negeer deze stap als u de banner niet ziet.
    1. Zodra de SAS-URL is ingetrokken, gaat u naar **Configuratie** voor de beheerde schijf. Verhoog de grootte zodat Site Recovery de waargenomen churn rate op de bronschijf ondersteunt.

## <a name="network-connectivity-problems"></a><a name="Network-connectivity-problem"></a>Problemen met de netwerkconnectiviteit

### <a name="network-latency-to-a-cache-storage-account"></a>Netwerklatentie voor een cacheopslagaccount

Siteherstel stuurt gerepliceerde gegevens naar het cacheopslagaccount. U netwerklatentie ervaren als het uploaden van de gegevens van een virtuele machine naar het cacheopslagaccount in 3 seconden langzamer is dan 4 MB.

Als u wilt controleren op een probleem met betrekking tot latentie, gebruikt u [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy). U dit hulpprogramma voor de opdrachtregel gebruiken om gegevens van de virtuele machine te uploaden naar het cacheopslagaccount. Als de latentie hoog is, controleert u of u een netwerkvirtueel toestel (NVA) gebruikt om uitgaand netwerkverkeer vanuit VM's te beheren. Het toestel kan worden beperkt als al het replicatieverkeer door de NVA gaat.

We raden u aan een eindpunt voor netwerkservice te maken in uw virtuele netwerk voor 'Opslag', zodat het replicatieverkeer niet naar de NVA gaat. Zie [Configuratie van virtuele apparaten in netwerk .](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#network-virtual-appliance-configuration)

### <a name="network-connectivity"></a>Netwerkconnectiviteit

Als replicatie van siteherstel werkt, moet de VM uitgaande connectiviteit bieden voor specifieke URL's of IP-bereiken. Mogelijk hebt u uw VM achter een firewall of gebruikt u NSG-regels (Network Security Group) om uitgaande connectiviteit te beheren. Als dat zo is, u problemen ondervinden. Zie [Uitgaande connectiviteit voor URL's voor siteherstel](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-urls)om ervoor te zorgen dat alle URL's zijn verbonden.

## <a name="error-id-153006---no-app-consistent-recovery-point-available-for-the-vm-in-the-past-x-minutes"></a>Fout-id 153006 - Geen app-consistent herstelpunt beschikbaar voor de VM in de afgelopen "X"-minuten

Hieronder volgen enkele van de meest voorkomende problemen.

#### <a name="known-issue-in-sql-server-20082008-r2"></a>Bekend probleem in SQL server 2008/2008 R2

**Hoe op te lossen:** Er is een bekend probleem met SQL server 2008/2008 R2. Raadpleeg het artikel [Azure Site Recovery Agent of andere niet-component VSS-back-up mislukt voor een server die SQL Server 2008 R2 host.](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2)

#### <a name="azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>Azure Site Recovery-taken mislukken op servers die een versie van SQL Server-exemplaren hosten met AUTO_CLOSE-db's

**Hoe op te lossen:** Raadpleeg het artikel [Niet-component VSS-back-ups zoals Azure Site Recovery-taken mislukken op servers die SQL Server-exemplaren hosten met AUTO_CLOSE DB's.](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser)


#### <a name="known-issue-in-sql-server-2016-and-2017"></a>Bekend probleem in SQL Server 2016 en 2017

**Hoe op te lossen:** Raadpleeg het artikel [Fout treedt op wanneer u een back-up maakt van een virtuele machine met een niet-componentgebaseerde back-up in SQL Server 2016 en 2017](https://support.microsoft.com/en-us/help/4508218/cumulative-update-16-for-sql-server-2017).

#### <a name="youre-using-azure-storage-spaces-direct-configuration"></a>U gebruikt directe configuratie van Azure Storage Spaces

**Hoe op te lossen:** Azure Site Recovery kan geen consistent herstelpunt voor toepassingen maken voor directe configuratie van opslagruimten. [Het replicatiebeleid configureren](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication-s2d-vms).

### <a name="more-causes-because-of-vss-related-issues"></a>Meer oorzaken als gevolg van VSS-gerelateerde problemen:

Als u verder problemen wilt oplossen, controleert u de bestanden op de bronmachine om de exacte foutcode voor fout te krijgen:

    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log

Als u de fouten in het bestand wilt vinden, zoekt u naar de tekenreeks "vacpError" door het bestand vacp.log in een editor te openen.

    Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|

In het voorgaande voorbeeld is **2147754994** de foutcode die u vertelt over de fout na deze zin.

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>VSS-schrijver is niet geïnstalleerd - Fout 2147221164

**Hoe op te lossen:** Azure Site Recovery maakt gebruik van De Shadow Copy Service (Volume Shadow Copy Service). Site Recovery installeert een VSS-provider voor de werking ervan om momentopnamen voor app-consistentie te maken. Azure Site Recovery installeert deze VSS-provider als een service. Als VSS-provider niet is geïnstalleerd, mislukt het maken van de consistentie van de toepassing. Het toont de fout-ID 0x80040154 "Klasse niet geregistreerd." Raadpleeg het artikel voor [het oplossen van problemen met de installatie van VSS-writer.](https://docs.microsoft.com/azure/site-recovery/vmware-azure-troubleshoot-push-install#vss-installation-failures)

#### <a name="vss-writer-is-disabled---error-2147943458"></a>VSS-schrijver is uitgeschakeld - Fout 2147943458

**Hoe op te lossen:** Om de consistentietag voor toepassingen te genereren, gebruikt Azure Site Recovery VSS. Site Recovery installeert een VSS-provider voor de werking ervan om momentopnamen voor app-consistentie te maken. Deze VSS-provider is geïnstalleerd als een service. Als u de VSS Provider-service niet hebt ingeschakeld, mislukt het maken van de consistentievan de toepassing. Het toont de fout "De opgegeven service is uitgeschakeld en kan niet worden gestart (0x80070422)."

Als VSS is uitgeschakeld:

- Controleer of het opstarttype van de VSS-providerservice is ingesteld op **Automatisch**.
- Start de volgende services opnieuw:
   - VSS-service
   - Azure-siteherstel VSS-provider
   - VDS-service

#### <a name="vss-provider-not_registered---error-2147754756"></a>VSS PROVIDER NOT_REGISTERED - Fout 2147754756

**Hoe op te lossen:** Om de consistentietag voor toepassingen te genereren, gebruikt Azure Site Recovery VSS. Controleer of de Azure Site Recovery VSS Provider-service is geïnstalleerd.

Gebruik de volgende opdrachten om DE VSS-provider opnieuw te installeren:
1. Bestaande provider verwijderen: C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Uninstall.cmd
1. VSS-provider opnieuw installeren: C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd

Controleer of het opstarttype van de VSS-providerservice is ingesteld op **Automatisch**.

Start de volgende services opnieuw:
- VSS-service
- Azure-siteherstel VSS-provider
- VDS-service
