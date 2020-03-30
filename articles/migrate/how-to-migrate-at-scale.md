---
title: Migratiemachinemigratie in Azure-migreren automatiseren
description: Beschrijft hoe u scripts gebruiken om een groot aantal machines in Azure Migreren te migreren
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: article
ms.date: 04/01/2019
ms.author: snehaa
ms.openlocfilehash: 317b6e8aa799b7982e9897c6a504d6092491c7ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74196367"
---
# <a name="scale-migration-of-vms"></a>Migratie van VM's schalen 

In dit artikel u begrijpen hoe u scripts gebruiken om een groot aantal virtuele machines (VM's) te migreren. Als u migratie wilt schalen, gebruikt u [Azure Site Recovery](../site-recovery/site-recovery-overview.md). 

Site Recovery-scripts zijn beschikbaar voor uw download op [Azure PowerShell Samples](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-with-site-recovery) repo op GitHub. De scripts kunnen worden gebruikt om VMware, AWS, GCP VM's en fysieke servers te migreren naar beheerde schijven in Azure. U deze scripts ook gebruiken om Hyper-V VM's te migreren als u de VM's migreert als fysieke servers. De scripts die gebruikmaken van Azure Site Recovery PowerShell worden [hier](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell)gedocumenteerd.

## <a name="current-limitations"></a>Huidige beperkingen
- Ondersteuning voor het opgeven van het statische IP-adres alleen voor de primaire NIC van de doel-VM
- De scripts nemen geen Azure Hybrid Benefit gerelateerde ingangen, u moet handmatig de eigenschappen van de gerepliceerde VM in de portal bijwerken

## <a name="how-does-it-work"></a>Hoe werkt het?

### <a name="prerequisites"></a>Vereisten
Voordat u aan de slag gaat, moet u de volgende stappen uitvoeren:
- Controleren of de kluis Siteherstel is gemaakt in uw Azure-abonnement
- Zorg ervoor dat de configuratieserver en processerver in de bronomgeving zijn geïnstalleerd en dat de kluis de omgeving kan detecteren
- Controleren of een replicatiebeleid is gemaakt en gekoppeld aan de configuratieserver
- Controleer of u het VM-beheeraccount aan de config-server hebt toegevoegd (die wordt gebruikt om de on-premises VM's te repliceren)
- Controleren of de doelartefacten in Azure zijn gemaakt
    - Doelgroepgroep
    - Target Storage Account (en de Resource Group) - Een premium opslagaccount maken als u van plan bent te migreren naar schijven met een premium beheerde schijf
    - Cache Storage Account (en de Resource Group) - Een standaardopslagaccount maken in dezelfde regio als de kluis
    - Virtueel netwerk targeten voor failover (en de resourcegroep)
    - Target Subnet
    - Virtuele netwerk targeten voor mislukte test (en de resourcegroep)
    - Beschikbaarheidsset (indien nodig)
    - Target Network Security Group en de resourcegroep
- Zorg ervoor dat u de eigenschappen van de doel-VM hebt bepaald
    - Naam van doelvm
    - Vm-grootte target in Azure (kan worden bepaald met azure-migratiebeoordeling)
    - Privé-IP-adres van de primaire NIC in de VIRTUELE-MACHINE
- De scripts downloaden van [Azure PowerShell Samples](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-with-site-recovery) repo op GitHub

### <a name="csv-input-file"></a>CSV-invoerbestand
Zodra u alle vereisten hebt voltooid, moet u een CSV-bestand maken, dat gegevens heeft voor elke bronmachine die u wilt migreren. De invoer-CSV moet een koplijn hebben met de invoerdetails en een rij met details voor elke machine die moet worden gemigreerd. Alle scripts zijn ontworpen om te werken op hetzelfde CSV-bestand. Er is een voorbeeld-CSV-sjabloon beschikbaar in de map scripts voor uw referentie.

### <a name="script-execution"></a>Script-uitvoering
Zodra de CSV klaar is, u de volgende stappen uitvoeren om de migratie van de on-premises VM's uit te voeren:

**Stap #** | **Scriptnaam** | **Beschrijving**
--- | --- | ---
1 | asr_startmigration.ps1 | Replicatie inschakelen voor alle VM's die in de csv worden vermeld, het script maakt een CSV-uitvoer met de taakgegevens voor elke VM
2 | asr_replicationstatus.ps1 | Controleer de status van replicatie, het script maakt een csv met de status voor elke VM
3 | asr_updateproperties.ps1 | Zodra de VM's zijn gerepliceerd/beveiligd, gebruikt u dit script om de doeleigenschappen van de VM (Compute- en Netwerkeigenschappen) bij te werken.
4 | asr_propertiescheck.ps1 | Controleren of de eigenschappen op de juiste manier zijn bijgewerkt
5 | asr_testmigration.ps1 |  Start de testfailover van de VM's die in de csv worden vermeld, het script maakt een CSV-uitvoer met de taakgegevens voor elke VM
6 | asr_cleanuptestmigration.ps1 | Zodra u handmatig de VM's hebt gevalideerd die zijn mislukt, u dit script gebruiken om de failoverVM's van de test op te schonen
7 | asr_migration.ps1 | Voer een ongeplande failover uit voor de VM's die in de csv worden vermeld, het script maakt een CSV-uitvoer met de taakgegevens voor elke vm. Het script sluit de on-premises VM's niet af voordat de failover wordt geactiveerd, voor consistentie van de toepassing wordt aanbevolen dat u de VM's handmatig afsluit voordat u het script uitvoert.
8 | asr_completemigration.ps1 | De commit-bewerking op de VM's uitvoeren en de entiteiten Azure Site Recovery verwijderen
9 | asr_postmigration.ps1 | Als u van plan bent netwerkbeveiligingsgroepen toe te wijzen aan de NIC's na de failover, u dit script gebruiken om dat te doen. Het wijst een NSG aan een NIC in het doel VM.

## <a name="how-to-migrate-to-managed-disks"></a>Hoe migreren naar beheerde schijven?
Het script migreert standaard de VM's naar beheerde schijven in Azure. Als het opgegeven doelopslagaccount een premium opslagaccount is, worden schijven met een premium beheerde schijf na migratie gemaakt. Het cacheopslagaccount kan nog steeds een standaardaccount zijn. Als het doelopslagaccount een standaardopslagaccount is, worden standaardschijven na migratie gemaakt. 

## <a name="next-steps"></a>Volgende stappen

[Meer informatie](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure) over het migreren van servers naar Azure met Azure Site Recovery
