---
title: Informatie over SAP HANA-databaseback-up in Azure VM's
description: In dit artikel vindt u een back-up van SAP HANA-databases die worden uitgevoerd op virtuele Azure-machines.
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 52c235c95cea73a0c51c62fcb55f7f711d2eff21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476454"
---
# <a name="about-sap-hana-database-backup-in-azure-vms"></a>Informatie over SAP HANA-databaseback-up in Azure VM's

SAP HANA-databases zijn bedrijfskritieke workloads die een doelstelling voor een laag herstelpunt (RPO) en een snelle hersteltijddoelstelling (RTO) vereisen. U nu [een back-up maken van SAP HANA-databases die worden uitgevoerd op Azure VM's](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db) met [Azure Backup.](https://docs.microsoft.com/azure/backup/backup-overview)

Azure Backup is [Backint-gecertificeerd](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5) door SAP, om native back-upondersteuning te bieden door gebruik te maken van de native API's van SAP HANA. Deze aanbieding van Azure Backup sluit aan bij azure backup's mantra van **back-ups met nulinfrastructuur,** waardoor back-upinfrastructuur niet meer hoeft te worden geïmplementeerd en beheren. U nu naadloos een back-up maken en SAP HANA-databases herstellen die worden uitgevoerd op Azure[VM's (M-serie VM's](../virtual-machines/m-series.md) die nu ook worden ondersteund!) en gebruikmaken van enterprise management-mogelijkheden die Azure Backup biedt.

## <a name="added-value"></a>Meerwaarde

Azure Backup gebruiken om een back-up te maken en SAP HANA-databases te herstellen, biedt de volgende voordelen:

* **15 minuten Herstelpunt Doelstelling (RPO)**: Herstel van kritieke gegevens van maximaal 15 minuten is nu mogelijk.
* **Met één klik, point-in-time herstelt:** Het herstellen van productiegegevens naar alternatieve HANA-servers wordt eenvoudig gemaakt. Het vastketenen van back-ups en catalogi om herstelte werk te maken, wordt achter de schermen allemaal beheerd door Azure.
* **Lange termijn retentie**: Voor strenge naleving en audit behoeften. Bewaar uw back-ups jarenlang, op basis van de bewaarduur, waarna de herstelpunten automatisch worden gesnoeid door de ingebouwde mogelijkheid voor levenscyclusbeheer.
* **Back-upbeheer vanuit Azure:** gebruik de beheer- en bewakingsmogelijkheden van Azure Backup voor een verbeterde beheerervaring. Azure CLI wordt ook ondersteund.

Als u de back-up- en herstelscenario's wilt bekijken die we vandaag ondersteunen, raadpleegt u de [SAP HANA-scenarioondersteuningsmatrix.](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support)

## <a name="backup-architecture"></a>Back-up maken van architectuur

![Back-uparchitectuurdiagram](./media/sap-hana-db-about/backup-architecture.png)

* Het back-upproces begint met [het maken van een vault voor herstelservices](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#create-a-recovery-service-vault) in Azure. Deze kluis wordt gebruikt om de back-ups en herstelpunten op te slaan die in de loop van de tijd zijn gemaakt.
* De Azure VM met SAP HANA-server is geregistreerd bij de kluis en de databases die een back-up moeten maken, worden [ontdekt.](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#discover-the-databases) Als u de Azure Backup-service in staat wilt stellen databases te detecteren, moet een [voorregistratiescript](https://aka.ms/scriptforpermsonhana) worden uitgevoerd op de HANA-server als hoofdgebruiker.
* Dit script maakt **AZUREWLBACKUPHANAUSER** DB-gebruiker en een bijbehorende sleutel met dezelfde naam in **hdbuserstore.** Raadpleeg de sectie [Wat het pre-registratiescript doet](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) om meer te begrijpen over wat het script doet.
* Azure Backup Service installeert nu de **Azure Backup Plugin voor HANA** op de geregistreerde SAP HANA-server.
* De **AZUREWLBACKUPHANAUSER DB-gebruiker** die is gemaakt door het voorregistratiescript wordt gebruikt door de **Azure Backup Plugin voor HANA** om alle back-up- en herstelbewerkingen uit te voeren. Als u back-ups probeert te configureren voor SAP HANA-db's zonder dit script uit te voeren, ontvangt u mogelijk de volgende fout: **UserErrorHanaScriptNotRun**.
* Als [u back-ups](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#configure-backup) wilt configureren in de databases die worden gedetecteerd, kiest u het vereiste back-upbeleid en schakelt u back-ups in.

* Zodra de back-up is geconfigureerd, stelt azure backup-service de volgende Backint-parameters in op databaseniveau op de beveiligde SAP HANA-server:
  * [catalog_backup_using_backint:waar]
  * [enable_accumulated_catalog_backup:false]
  * [parallel_data_backup_backint_channels:1]
  * [log_backup_timeout_s:900)]
  * [backint_response_timeout:7200]

>[!NOTE]
>Zorg ervoor dat deze parameters *niet* aanwezig zijn op HOST-niveau. Parameters op hostniveau overschrijven deze parameters en kunnen onverwacht gedrag veroorzaken.
>

* De **Azure Backup Plugin voor HANA** onderhoudt alle back-upschema's en beleidsdetails. Het activeert de geplande back-ups en communiceert met de **HANA Backup Engine** via de Backint API's.
* De **HANA Backup Engine** retourneert een Backint-stream met de gegevens die moeten worden geback-upt.
* Alle geplande back-ups en on-demand back-ups (geactiveerd vanuit de Azure-portal) die volledig of differentieel zijn, worden geïnitieerd door de **Azure Backup Plugin voor HANA.** Log back-ups worden echter beheerd en geactiveerd door **HANA Backup Engine** zelf.
* Azure Backup voor SAP HANA, een BackInt-gecertificeerde oplossing, is niet afhankelijk van onderliggende schijf- of VM-typen. De back-up wordt uitgevoerd door streams gegenereerd door HANA.

## <a name="using-azure-vm-backup-with-azure-sap-hana-backup"></a>Azure VM-back-up gebruiken met Azure SAP HANA-back-up

Naast het gebruik van de SAP HANA-back-up in Azure die back-up en herstel op databaseniveau biedt, u de Azure VM-back-upoplossing gebruiken om een back-up te maken van de besturingssysteem- en niet-databaseschijven.

De [Backint-gecertificeerde Azure SAP HANA-back-upoplossing](#backup-architecture) kan worden gebruikt voor databaseback-up en herstel.

[Azure VM-back-up](backup-azure-vms-introduction.md) kan worden gebruikt om een back-up van het besturingssysteem en andere niet-databaseschijven te maken. De VM back-up wordt genomen een keer per dag en het back-up van alle schijven (behalve **Write Accelerator (WA) schijven** en **UltraDisks).** Aangezien er een back-up van de database wordt gemaakt met de Azure SAP HANA-back-upoplossing, u een bestandsconsistente back-up maken van alleen de schijven van het besturingssysteem en de niet-database met behulp van de schijfmogelijkheid uitsluiten, die momenteel in preview is.

>[!NOTE]
> Als u pre-post-scripts gebruikt met de Azure VM-back-up, kunnen app-consistente back-ups van de gegevensvolumes van de database worden gemaakt. Als het logboekgebied zich echter op WA-schijven bevindt, garandeert het maken van een momentopname van deze schijven mogelijk geen consistentie in het logboekgebied. HANA heeft een expliciete manier van het genereren van log back-ups om deze exacte reden. Schakel hetzelfde in in uw SAP HANA en er kan een back-up van worden gemaakt met Azure SAP HANA-back-up.

Voer de volgende stappen uit om een VM met SAP HANA te herstellen:

* [Herstel een nieuwe VM vanuit Azure VM-back-up](backup-azure-arm-restore-vms.md) vanaf het nieuwste herstelpunt. Of maak een nieuwe lege VM en bevestig de schijven vanaf het laatste herstelpunt.
* Aangezien er geen back-up van WA-schijven is, worden ze niet hersteld. Maak lege WA-schijven en logboekgebied.
* Nadat alle andere configuraties (zoals IP, systeemnaam, enzovoort) zijn ingesteld, is de VM ingesteld op db-gegevens van Azure-back-up.
* Herstel de DB nu in de VM van de [Azure SAP HANA DB-back-up](sap-hana-db-restore.md#restore-to-a-point-in-time-or-to-a-recovery-point) naar de gewenste point-in-time.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [herstellen van een SAP HANA-database die wordt uitgevoerd op een Azure VM](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Meer informatie over het [beheren van SAP HANA-databases waarvan een back-up wordt gemaakt met Azure Backup](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)
