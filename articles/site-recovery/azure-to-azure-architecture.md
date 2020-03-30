---
title: Azure naar Azure-noodherstelarchitectuur in Azure-siteherstel
description: Overzicht van de architectuur die wordt gebruikt bij het instellen van noodherstel tussen Azure-regio's voor Azure VM's, met behulp van de Azure Site Recovery-service.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 3/13/2020
ms.author: raynew
ms.openlocfilehash: 94da1639b5398a03b36fba3ff88877468a97ec36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294121"
---
# <a name="azure-to-azure-disaster-recovery-architecture"></a>Architectuur voor herstel na noodgevallen van Azure naar Azure


In dit artikel worden de architectuur, componenten en processen beschreven die worden gebruikt wanneer u noodherstel voor Virtuele Azure-machines (VM's) implementeert met behulp van de [Azure Site Recovery-service.](site-recovery-overview.md) Met noodherstel ingesteld, Azure VM's voortdurend repliceren van naar een ander doelgebied. Als er een storing optreedt, u deze niet meer opnemen via VM's naar het secundaire gebied en deze vanaf daar openen. Als alles weer normaal wordt uitgevoerd, u weer falen en op de primaire locatie blijven werken.



## <a name="architectural-components"></a>Architectuuronderdelen

De componenten die betrokken zijn bij noodherstel voor Azure VM's worden samengevat in de volgende tabel.

**Component** | **Vereisten**
--- | ---
**VM's in brongebied** | Een van de meer Azure VM's in een [ondersteund brongebied](azure-to-azure-support-matrix.md#region-support).<br/><br/> VM's kunnen elk [ondersteund besturingssysteem](azure-to-azure-support-matrix.md#replicated-machine-operating-systems)draaien.
**Bron VM-opslag** | Azure VM's kunnen worden beheerd of niet-beheerde schijven hebben die zijn verspreid over opslagaccounts.<br/><br/>[Meer informatie over](azure-to-azure-support-matrix.md#replicated-machines---storage) ondersteunde Azure-opslag.
**Bron VM-netwerken** | VM's kunnen zich in een of meer subnetten in een virtueel netwerk (VNet) in het brongebied bevinden. [Meer informatie](azure-to-azure-support-matrix.md#replicated-machines---networking) over netwerkvereisten.
**Cacheopslagaccount** | U hebt een cacheopslagaccount in het bronnetwerk nodig. Tijdens de replicatie worden VM-wijzigingen opgeslagen in de cache voordat ze naar doelopslag worden verzonden.  Opslagaccounts in cache moeten standaard zijn.<br/><br/> Het gebruik van een cache zorgt voor minimale impact op productietoepassingen die op een VM worden uitgevoerd.<br/><br/> [Meer informatie](azure-to-azure-support-matrix.md#cache-storage) over cacheopslagvereisten. 
**Doelbronnen** | Doelbronnen worden gebruikt tijdens de replicatie en wanneer er een failover optreedt. Siteherstel kan standaard doelbronnen instellen of u ze maken/aanpassen.<br/><br/> Controleer in het doelgebied of u VM's maken en of uw abonnement voldoende resources heeft om VM-formaten te ondersteunen die nodig zijn in de doelregio. 

![Bron- en doelreplicatie](./media/concepts-azure-to-azure-architecture/enable-replication-step-1.png)

## <a name="target-resources"></a>Doelbronnen

Wanneer u replicatie inschakelt voor een virtuele machine, u met Siterecovery automatisch doelbronnen maken. 

**Doelbron** | **Standaardinstelling**
--- | ---
**Doelabonnement** | Hetzelfde als het bronabonnement.
**Doelgroepgroep** | De resourcegroep waartoe VM's behoren na failover.<br/><br/> Het kan zich in een Azure-regio bevinden, behalve in het brongebied.<br/><br/> Siteherstel maakt een nieuwe resourcegroep in de doelregio, met een 'asr'-achtervoegsel.<br/><br/>
**Doel VNet** | Het virtuele netwerk (VNet) waarin gerepliceerde VM's zich bevinden na failover. Er wordt een netwerktoewijzing gemaakt tussen bron- en doelvirtuele netwerken en vice versa.<br/><br/> Site Recovery maakt een nieuw VNet en subnet, met het 'asr' achtervoegsel.
**Doelopslagaccount** |  Als de VM geen beheerde schijf gebruikt, is dit het opslagaccount waaraan gegevens worden gerepliceerd.<br/><br/> Siteherstel maakt een nieuw opslagaccount in het doelgebied om het bronopslagaccount te spiegelen.
**Beheerde schijven repliceren** | Als de VM een beheerde schijf gebruikt, zijn dit de beheerde schijven waaraan gegevens worden gerepliceerd.<br/><br/> Siteherstel maakt replicabeheerde schijven in het opslaggebied om de bron te spiegelen.
**Beschikbaarheidssets voor doel** |  Beschikbaarheidsset waarin replicerende VM's zich bevinden na failover.<br/><br/> Siteherstel maakt een beschikbaarheidsset in het doelgebied met het achtervoegsel 'asr', voor VM's die zich bevinden in een beschikbaarheidsset op de bronlocatie. Als er een beschikbaarheidsset bestaat, wordt deze gebruikt en wordt er geen nieuwe gemaakt.
**Beschikbaarheidszones voor doel** | Als het doelgebied beschikbaarheidszones ondersteunt, wijst Site Recovery hetzelfde zonenummer toe als dat in het brongebied.

### <a name="managing-target-resources"></a>Doelresources beheren

U doelbronnen als volgt beheren:

- U doelinstellingen wijzigen terwijl u replicatie inschakelt.
- U doelinstellingen wijzigen nadat de replicatie al werkt. De uitzondering is het beschikbaarheidstype (enkele instantie, set of zone). Als u deze instelling wilt wijzigen, moet u de replicatie uitschakelen, de instelling wijzigen en vervolgens opnieuw inschakelen.



## <a name="replication-policy"></a>Beleid voor replicatie 

Wanneer u Azure VM-replicatie inschakelt, maakt siteherstel standaard een nieuw replicatiebeleid met de standaardinstellingen die in de tabel zijn samengevat.

**Beleidsinstelling** | **Details** | **Standaard**
--- | --- | ---
**Herstelpunt behoud** | Hiermee geeft u op hoe lang siteherstel herstelpunten houdt | 24 uur
**App-consistente momentopnamefrequentie** | Hoe vaak Site Recovery een momentopname voor app-consistent maakt. | Elke vier uur

### <a name="managing-replication-policies"></a>Replicatiebeleid beheren

U de instellingen voor standaardreplicatiebeleid als volgt beheren en wijzigen:
- U de instellingen wijzigen terwijl u replicatie inschakelt.
- U op elk gewenst moment een replicatiebeleid maken en het vervolgens toepassen wanneer u replicatie inschakelt.

### <a name="multi-vm-consistency"></a>Consistentie met meerdere vm's

Als u wilt dat VM's samen worden gerepliceerd en crashconsistente en app-consistente herstelpunten hebben gedeeld bij failover, u ze samenvoegen tot een replicatiegroep. Multi-VM-consistentie heeft invloed op de prestaties van de werkbelasting en mag alleen worden gebruikt voor VM's met workloads die consistentie nodig hebben voor alle machines. 



## <a name="snapshots-and-recovery-points"></a>Momentopnamen en herstelpunten

Herstelpunten worden gemaakt op basis van momentopnamen van VM-schijven die op een bepaald moment zijn gemaakt. Wanneer u niet over een vm slaagt, gebruikt u een herstelpunt om de VM op de doellocatie te herstellen.

Als het niet lukt, willen we er over het algemeen voor zorgen dat de VM begint zonder corruptie of gegevensverlies, en dat de VM-gegevens consistent zijn voor het besturingssysteem en voor apps die op de VM worden uitgevoerd. Dit is afhankelijk van het type momentopnamen dat is gemaakt.

Site recovery maakt als volgt momentopnamen:

1. Siteherstel maakt standaard momentopnamen van gegevens en momentopnamen voor app-consistent als u een frequentie voor deze momentopnamen opgeeft.
2. Herstelpunten worden gemaakt op basis van de momentopnamen en opgeslagen in overeenstemming met de bewaarinstellingen in het replicatiebeleid.

### <a name="consistency"></a>Consistentie

In de volgende tabel worden verschillende typen consistentie uitgelegd.

### <a name="crash-consistent"></a>Crash-consistent

**Beschrijving** | **Details** | **Aanbeveling**
--- | --- | ---
Een crashconsistente momentopname legt gegevens vast die zich op de schijf bevond toen de momentopname werd gemaakt. Het bevat niets in het geheugen.<br/><br/> Het bevat het equivalent van de on-disk gegevens die aanwezig zouden zijn als de VM crashte of het netsnoer werd getrokken van de server op het moment dat de momentopname werd genomen.<br/><br/> Een crash-consistent garandeert geen consistentie van de gegevens voor het besturingssysteem of voor apps op de VM. | Site Recovery maakt standaard standaard crashconsistente herstelpunten om de vijf minuten. Deze instelling kan niet worden gewijzigd.<br/><br/>  | Tegenwoordig kunnen de meeste apps goed herstellen van crash-consistent punten.<br/><br/> Crashconsistente herstelpunten zijn meestal voldoende voor de replicatie van besturingssystemen en apps zoals DHCP-servers en afdrukservers.

### <a name="app-consistent"></a>App-consistent

**Beschrijving** | **Details** | **Aanbeveling**
--- | --- | ---
App-consistente herstelpunten worden gemaakt op basis van app-consistente momentopnamen.<br/><br/> Een momentopname die consistent is voor een app bevat alle informatie in een momentopname die consistent is voor crashen, plus alle gegevens in het geheugen en de lopende transacties. | App-consistente momentopnamen maken gebruik van de VSS (Volume Shadow Copy Service):<br/><br/>   1) Wanneer een momentopname wordt gestart, voert VSS een copy-on-write (COW) bewerking uit op het volume.<br/><br/>   2) Voordat het de COW uitvoert, vss informeert elke app op de machine dat het nodig heeft om haar geheugen-resident gegevens te spoelen naar de schijf.<br/><br/>   3) VSS staat vervolgens de back-up / disaster recovery app (in dit geval Site Recovery) om de snapshot gegevens te lezen en verder te gaan. | App-consistente momentopnamen worden gemaakt in overeenstemming met de frequentie die u opgeeft. Deze frequentie moet altijd lager zijn dan u hebt ingesteld voor het behouden van herstelpunten. Als u bijvoorbeeld herstelpunten behoudt met de standaardinstelling van 24 uur, moet u de frequentie op minder dan 24 uur instellen.<br/><br/>Ze zijn complexer en duren langer dan crash-consistente snapshots.<br/><br/> Ze hebben invloed op de prestaties van apps die worden uitgevoerd op een VM die is ingeschakeld voor replicatie. 

## <a name="replication-process"></a>Replicatieproces

Wanneer u replicatie inschakelt voor een Azure VM, gebeurt het volgende:

1. De serviceextensie Site Recovery Mobility wordt automatisch op de VM geïnstalleerd.
2. De extensie registreert de VM met Site recovery.
3. Continue replicatie begint voor de VM.  Schijfschrijft worden onmiddellijk overgebracht naar het cacheopslagaccount op de bronlocatie.
4. Siteherstel verwerkt de gegevens in de cache en stuurt deze naar het doelopslagaccount of naar de door replica beheerde schijven.
5. Nadat de gegevens zijn verwerkt, worden crashconsistente herstelpunten elke vijf minuten gegenereerd. App-consistente herstelpunten worden gegenereerd volgens de instelling die is opgegeven in het replicatiebeleid.

![Replicatieproces inschakelen, stap 2](./media/concepts-azure-to-azure-architecture/enable-replication-step-2.png)

**Replicatieproces**

## <a name="connectivity-requirements"></a>Connectiviteitsvereisten

 De Azure VM's die u repliceert, hebben uitgaande connectiviteit nodig. Siteherstel heeft nooit inkomende connectiviteit met de VM nodig. 

### <a name="outbound-connectivity-urls"></a>Uitgaande connectiviteit (URL's)

Als uitgaande toegang voor VM's wordt beheerd met URL's, u deze URL's toestaan.

| **Url** | **Details** |
| ------- | ----------- |
| *.blob.core.windows.net | Hiermee kunnen gegevens van de VM naar het cache-opslagaccount in de bronregio worden geschreven. |
| login.microsoftonline.com | Verzorgt autorisatie en authenticatie voor de URL’s van Site Recovery. |
| *.hypervrecoverymanager.windowsazure.com | Maakt het de VM mogelijk te communiceren met de Site Recovery-service. |
| *.servicebus.windows.net | Maakt het de VM mogelijk bewakings- en diagnosegegevens van Site Recovery te schrijven. |
| *.vault.azure.net | Biedt toegang om replicatie voor virtuele machines met ADE-poort via portal mogelijk te maken
| *.automation.ext.azure.com | Maakt het mogelijk om een automatische upgrade van mobiliteitsagent mogelijk te maken voor een gerepliceerd item via portal

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Uitgaande connectiviteit voor IP-adresbereiken

Als u uitgaande connectiviteit voor VM's wilt beheren met BEHULP van IP-adressen, u deze adressen toestaan.
Houd er rekening mee dat details van de vereisten voor netwerkconnectiviteit kunnen worden gevonden in [whitepaper netwerken](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags) 

#### <a name="source-region-rules"></a>Bronregioregels

**Regel** |  **Details** | **Servicetag**
--- | --- | --- 
HTTPS-uitgaande toestaan: poort 443 | Bereiken toestaan die overeenkomen met opslagaccounts in het brongebied | Opslag. \<regionaam>
HTTPS-uitgaande toestaan: poort 443 | Bereiken toestaan die overeenkomen met Azure Active Directory (Azure AD)  | AzureActiveDirectory
HTTPS-uitgaande toestaan: poort 443 | Reeksen toestaan die overeenkomen met de gebeurtenishub in het doelgebied. | EventsHub. \<regionaam>
HTTPS-uitgaande toestaan: poort 443 | Bereiken toestaan die overeenkomen met Azure Site Recovery  | AzureSiteHerstel
HTTPS-uitgaande toestaan: poort 443 | Bereiken toestaan die overeenkomen met Azure Key Vault (dit is alleen vereist voor het inschakelen van replicatie van virtuele machines met ADE via portal) | AzureKeyVault
HTTPS-uitgaande toestaan: poort 443 | Bereiken toestaan die overeenkomen met Azure Automation Controller (Dit is alleen vereist voor het inschakelen van automatische upgrade van mobiliteitsagent voor een gerepliceerd item via portal) | GuestAndHybridManagement

#### <a name="target-region-rules"></a>Regels voor doelregio's

**Regel** |  **Details** | **Servicetag**
--- | --- | --- 
HTTPS-uitgaande toestaan: poort 443 | Bereiken toestaan die overeenkomen met opslagaccounts in het doelgebied | Opslag. \<regionaam>
HTTPS-uitgaande toestaan: poort 443 | Bereiken toestaan die overeenkomen met Azure AD  | AzureActiveDirectory
HTTPS-uitgaande toestaan: poort 443 | Reeksen toestaan die overeenkomen met de gebeurtenishub in het brongebied. | EventsHub. \<regionaam>
HTTPS-uitgaande toestaan: poort 443 | Bereiken toestaan die overeenkomen met Azure Site Recovery  | AzureSiteHerstel
HTTPS-uitgaande toestaan: poort 443 | Bereiken toestaan die overeenkomen met Azure Key Vault (dit is alleen vereist voor het inschakelen van replicatie van virtuele machines met ADE via portal) | AzureKeyVault
HTTPS-uitgaande toestaan: poort 443 | Bereiken toestaan die overeenkomen met Azure Automation Controller (Dit is alleen vereist voor het inschakelen van automatische upgrade van mobiliteitsagent voor een gerepliceerd item via portal) | GuestAndHybridManagement


#### <a name="control-access-with-nsg-rules"></a>Toegang beheren met NSG-regels

Als u vm-connectiviteit beheert door netwerkverkeer van en naar Azure-netwerken/subnetten te filteren met [NSG-regels,](https://docs.microsoft.com/azure/virtual-network/security-overview)moet u de volgende vereisten noteren:

- NSG-regels voor de bronAzure-regio moeten uitgaande toegang voor replicatieverkeer toestaan.
- We raden u aan regels te maken in een testomgeving voordat u ze in productie neemt.
- Gebruik [servicetags](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) in plaats van afzonderlijke IP-adressen toe te staan.
    - Servicetags vertegenwoordigen een groep IP-adresvoorvoegsels die zijn verzameld om de complexiteit bij het maken van beveiligingsregels te minimaliseren.
    - Microsoft werkt servicetags na verloop van tijd automatisch bij. 
 
Meer informatie over [uitgaande connectiviteit](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags) voor Site Recovery en het beheren van connectiviteit [met NSGs](concepts-network-security-group-with-site-recovery.md).


### <a name="connectivity-for-multi-vm-consistency"></a>Connectiviteit voor multi-VM-consistentie

Als u multi-VM-consistentie inschakelt, communiceren machines in de replicatiegroep met elkaar via poort 20004.
- Zorg ervoor dat de interne communicatie tussen de VM's via poort 20004 niet door een firewall-apparaat wordt geblokkeerd.
- Als u wilt dat Linux VM’s deel uitmaken van een replicatiegroep, zorg er dan voor dat het uitgaande verkeer op poort 20004 handmatig wordt geopend volgens de richtlijnen van de specifieke Linux-versie.




## <a name="failover-process"></a>Failoverproces

Wanneer u een failover initieert, worden de VM's gemaakt in de doelgroep, het virtuele netwerk target, het subnet target en in de doelbeschikbaarheidsset. Tijdens een failover u elk herstelpunt gebruiken.

![Failoverproces](./media/concepts-azure-to-azure-architecture/failover.png)

## <a name="next-steps"></a>Volgende stappen

[Repliceer snel](azure-to-azure-quickstart.md) een Azure VM naar een secundaire regio.
