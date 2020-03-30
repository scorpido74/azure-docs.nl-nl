---
title: Azure VM's met Storage Spaces Direct repliceren met Azure Site Recovery
description: Meer informatie over het repliceren van Azure VM's met Storage Spaces Direct met Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 01/29/2019
ms.openlocfilehash: 9f394fa8d618c97d74a47ff6e42a002f177cf7d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75973654"
---
# <a name="replicate-azure-vms-running-storage-spaces-direct-to-another-region"></a>Azure VM's met opslagruimten rechtstreeks naar een andere regio repliceren

In dit artikel wordt beschreven hoe u het herstel van Azure VM's met beheervan opslagruimten direct inschakelen.

>[!NOTE]
>Alleen crashconsistente herstelpunten worden ondersteund voor directe clusters van opslagruimten.
>

[Opslagruimten direct (S2D)](https://docs.microsoft.com/windows-server/storage/storage-spaces/deploy-storage-spaces-direct) is softwaregedefinieerde opslag, die een manier biedt om [gastclusters](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure) op Azure te maken.  Een gastcluster in Microsoft Azure is een failovercluster dat bestaat uit IaaS VM's. Hiermee kunnen gehoste VM-workloads mislukken in gastclusters, waardoor SLA met een hogere beschikbaarheid voor toepassingen wordt bereikt dan een enkele Azure VM kan bieden. Het is handig in scenario's waarin een VM een kritieke toepassing host, zoals SQL of scale-out bestandsserver.

## <a name="disaster-recovery-with-storage-spaces-direct"></a>Disaster recovery met direct opslagruimten

In een typisch scenario u een gastcluster voor virtuele machines op Azure hebben voor een hogere tolerantie van uw toepassing, zoals Uitschalen van bestandsserver. Hoewel dit uw toepassing een hogere beschikbaarheid kan bieden, wilt u deze toepassingen beveiligen met behulp van Site Recovery voor elke fout op regioniveau. Siteherstel repliceert de gegevens van de ene regio naar een andere Azure-regio en brengt het cluster in het gebied van noodherstel naar boven in geval van failover.

Onderstaand diagram toont een Azure VM-failovercluster met twee knooppunten met direct opslagruimten.

![storagespacesdirect](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacedirect.png)


- Twee Azure virtuele machines in een Windows Failover cluster en elke virtuele machine hebben twee of meer gegevensschijven.
- S2D synchroniseert de gegevens op de gegevensschijf en presenteert de gesynchroniseerde opslag als een opslaggroep.
- De opslaggroep wordt gepresenteerd als een clustergedeeld volume (CSV) voor het failovercluster.
- Het cluster Failover gebruikt de CSV voor de gegevensstations.

**Overwegingen voor noodherstel**

1. Wanneer u [cloudgetuige](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness#CloudWitnessSetUp) voor het cluster instelt, houdt u getuige in de regio Rampherstel.
2. Als u gaat mislukken over de virtuele machines naar het subnet op de DR-regio die verschilt van het brongebied dan cluster IP-adres moet worden gewijzigd na failover.  Als u het IP-cluster wilt wijzigen, moet u het script voor het herstelplan siteherstel [gebruiken.](https://docs.microsoft.com/azure/site-recovery/site-recovery-runbook-automation)</br>
[Voorbeeldscript](https://github.com/krnese/azure-quickstart-templates/blob/master/asr-automation-recovery/scripts/ASR-Wordpress-ChangeMysqlConfig.ps1) om opdracht binnen VM uit te voeren met aangepaste scriptextensie 

### <a name="enabling-site-recovery-for-s2d-cluster"></a>Siteherstel inschakelen voor S2D-cluster:

1. Klik in de kluis van herstelservices op '+repliceren'
1. Selecteer alle knooppunten in het cluster en maak ze deel van een [multi-VM-consistentiegroep](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-common-questions#multi-vm-consistency)
1. Replicatiebeleid selecteren met toepassingsconsistentie uit* (alleen ondersteuning voor crashconsistentie is beschikbaar)
1. De replicatie inschakelen

   ![storagespacesdirect bescherming](./media/azure-to-azure-how-to-enable-replication-s2d-vms/multivmgroup.png)

2. Ga naar gerepliceerde items en u zowel de status van de virtuele machine zien.
3. Beide virtuele machines worden beschermd en worden ook weergegeven als onderdeel van multi-VM consistentie groep.

   ![storagespacesdirect bescherming](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacesdirectgroup.PNG)

## <a name="creating-a-recovery-plan"></a>Een herstelplan maken
Een herstelplan ondersteunt de volgorde van verschillende lagen in een multi-tier toepassing tijdens een failover. Sequencing helpt de consistentie van de toepassing te behouden. Wanneer u een herstelplan maakt voor een multi-tier webtoepassing, voert u de stappen uit die zijn beschreven in [Een herstelplan maken met Siteherstel](site-recovery-create-recovery-plans.md).

### <a name="adding-virtual-machines-to-failover-groups"></a>Virtuele machines toevoegen aan failovergroepen

1.  Maak een herstelplan door de virtuele machines toe te voegen.
2.  Klik op 'Aanpassen' om de VM's te groeperen. Standaard maken alle VM's deel uit van 'Groep 1'.


### <a name="add-scripts-to-the-recovery-plan"></a>Scripts toevoegen aan het herstelplan
Als u uw toepassingen correct wilt laten functioneren, moet u mogelijk bepaalde bewerkingen uitvoeren op de virtuele Azure-machines na de failover of tijdens een testfailover. U sommige post-failoverbewerkingen automatiseren. Hier koppelen we bijvoorbeeld load balancer en wisselen we cluster-IP.


### <a name="failover-of-the-virtual-machines"></a>Failover van de virtuele machines 
Beide knooppunten van de VM's moeten worden mislukt met behulp van het [herstelplan](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) siteherstel 

![storagespacesdirect bescherming](./media/azure-to-azure-how-to-enable-replication-s2d-vms/recoveryplan.PNG)

## <a name="run-a-test-failover"></a>Een testfailover uitvoeren
1.  Selecteer in de Azure-portal de kluis Van Herstelservices.
2.  Selecteer het herstelplan dat u hebt gemaakt.
3.  Selecteer **Failover testen**.
4.  Als u het failoverproces wilt starten, selecteert u het herstelpunt en het virtuele Azure-netwerk.
5.  Wanneer de secundaire omgeving is up, voert u validaties uit.
6.  Wanneer validaties zijn voltooid, selecteert u **De failovertest failover opschonen**om de failover-omgeving op te schonen .

Zie [Failover testen naar Azure in Siterecovery voor](site-recovery-test-failover-to-azure.md)meer informatie.

## <a name="run-a-failover"></a>Een failover uitvoeren

1.  Selecteer in de Azure-portal de kluis Van Herstelservices.
2.  Selecteer het herstelplan dat u hebt gemaakt voor SAP-toepassingen.
3.  Selecteer **Failover**.
4.  Als u het failoverproces wilt starten, selecteert u het herstelpunt.

Zie [Failover in Siteherstel](site-recovery-failover.md)voor meer informatie .
## <a name="next-steps"></a>Volgende stappen

[Meer informatie](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback) over het uitvoeren van failback.
