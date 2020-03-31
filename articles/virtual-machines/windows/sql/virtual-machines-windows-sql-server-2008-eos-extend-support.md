---
title: Ondersteuning voor SQL Server 2008 & 2008 R2 uitbreiden
description: Breid de ondersteuning voor SQL Server 2008 en SQL Server 2008 R2 uit door uw SQL Server-exemplaar naar Azure te migreren of uitgebreide ondersteuning aan te schaffen om instanties on-premises te houden.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/08/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 6c27fa8f80140e6db92ea0f998852507260cc278
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159537"
---
# <a name="extend-support-for-sql-server-2008-and-sql-server-2008-r2-with-azure"></a>Ondersteuning voor SQL Server 2008 en SQL Server 2008 R2 uitbreiden met Azure

SQL Server 2008 en SQL Server 2008 R2 hebben beide het einde van de [levenscyclus van hun ondersteuning (EOS)](https://www.microsoft.com/sql-server/sql-server-2008)bereikt. Omdat veel klanten nog steeds beide versies gebruiken, bieden we verschillende opties om ondersteuning te blijven krijgen. U uw on-premises SQL Server-exemplaren migreren naar virtuele Azure-machines (VM's), migreren naar Azure SQL Database of on-premises blijven en uitgebreide beveiligingsupdates aanschaffen.

In tegenstelling tot een beheerde instantie hoeft u niet opnieuw te worden gehercertificeren naar een Azure VM. En in tegenstelling tot wanneer u on-premises blijft, ontvangt u gratis uitgebreide beveiligingspatches door te migreren naar een Azure VM.

De rest van dit artikel bevat overwegingen voor het migreren van uw SQL Server-exemplaar naar een Azure VM.

Zie Einde van de ondersteuning [voor](/sql/sql-server/end-of-support/sql-server-end-of-life-overview)meer informatie over het einde van de ondersteuningsopties.

## <a name="provisioning"></a>Inrichten

Er is een pay-as-you-go **SQL Server 2008 R2 op Windows Server 2008 R2-afbeelding** beschikbaar op Azure Marketplace.

Klanten die sql server 2008 op SQL Server 2008 staan, moeten zichzelf installeren of upgraden naar SQL Server 2008 R2. Op dezelfde manier moeten klanten op Windows Server 2008 hun VM implementeren vanaf een aangepaste VHD of upgraden naar Windows Server 2008 R2.

Afbeeldingen die via Azure Marketplace worden geïmplementeerd, worden geleverd met de SQL IaaS-extensie vooraf geïnstalleerd. De SQL IaaS-extensie is een vereiste voor flexibele licenties en geautomatiseerdpatchen. Klanten die zelf geïnstalleerde VM's implementeren, moeten de SQL IaaS-extensie handmatig installeren. De SQL IaaS-extensie wordt niet ondersteund op Windows Server 2008.

> [!NOTE]
> Hoewel de SQL Server **Create and** **Manage** blades werken met de SQL Server 2008 R2-afbeelding in de Azure-portal, worden de volgende functies _niet ondersteund:_ automatische back-ups, Azure Key Vault-integratie, R-services en opslagconfiguratie.

## <a name="licensing"></a>Licentieverlening
Met Pay-as-you-go SQL Server 2008 R2-implementaties kunnen worden geconverteerd naar [Azure Hybrid Benefit.](https://azure.microsoft.com/pricing/hybrid-benefit/)

Als u een Software Assurance-licentie (SA)-licentie wilt converteren naar pay-as-you-go, moeten klanten zich registreren bij de SQL [VM-resourceprovider.](virtual-machines-windows-sql-register-with-resource-provider.md) Na die registratie is het SQL-licentietype uitwisselbaar tussen Azure Hybrid Benefit en pay-as-you-go.

Zelf geïnstalleerde SQL Server 2008- of SQL Server 2008 R2-exemplaren op een Azure VM kunnen zich registreren bij de SQL VM-resourceprovider en hun licentietype converteren naar betalen per gebruik.

## <a name="migration"></a>Migratie
U EOS SQL Server-exemplaren migreren naar een Azure VM met handmatige back-up-/herstelmethoden. Dit is de meest voorkomende migratiemethode van on-premises naar een Azure VM.

### <a name="azure-site-recovery"></a>Azure Site Recovery

Voor bulkmigraties raden we de [Azure Site Recovery-service](/azure/site-recovery/site-recovery-overview) aan. Met Azure Site Recovery kunnen klanten de hele VM repliceren, inclusief SQL Server van on-premises naar Azure VM.

SQL Server vereist app-consistente Azure Site Recovery-momentopnamen om herstel te garanderen. Azure Site Recovery ondersteunt app-consistente momentopnamen met een interval van minimaal 1 uur. De minimale doeldoelstelling (RPO) die mogelijk is voor SQL Server met Azure Site Recovery-migraties is 1 uur. De hersteltijddoelstelling (RTO) is 2 uur plus SQL Server-hersteltijd.

### <a name="database-migration-service"></a>Databasemigratieservice

De [Database Migration Service](/azure/dms/dms-overview) is een optie voor klanten als ze migreren van on-premises naar een Azure VM door SQL Server te upgraden naar de 2012-versie of hoger.

## <a name="disaster-recovery"></a>Herstel na noodgeval

Oplossingen voor noodherstel voor EOS SQL Server op een Azure VM zijn als volgt:

- **SQL Server-back-ups**: Gebruik Azure Backup om uw EOS SQL Server 2008 en 2008 R2 te beschermen tegen ransomware, onbedoelde verwijdering en beschadiging met RPO van 15 min en point-in-time recovery. Zie [dit artikel](https://docs.microsoft.com/azure/backup/backup-azure-sql-database#scenario-support)voor meer informatie.
- **Logboekverzending:** u een logboekverzendreplica maken in een andere zone of Azure-regio met continue herstelbewegingen om de RTO te verminderen. U moet logboekverzending handmatig configureren.
- **Azure-siteherstel:** u uw VM repliceren tussen zones en regio's via Azure Site Recovery-replicatie. SQL Server vereist app-consistente momentopnamen om herstel te garanderen in geval van een ramp. Azure Site Recovery biedt minimaal 1 uur RPO en een RTO van 2 uur (plus SQL Server-hersteltijd) Voor EOS SQL Server disaster recovery.

## <a name="security-patching"></a>Beveiligingspatching
Uitgebreide beveiligingsupdates voor SQL Server VM's worden geleverd via de Microsoft Update-kanalen nadat de SQL Server VM is geregistreerd bij de SQL [VM-resourceprovider.](virtual-machines-windows-sql-register-with-resource-provider.md) Patches kunnen handmatig of automatisch worden gedownload.

*Automatisch patchen* is standaard ingeschakeld. Met automatisch patchen kan Azure automatisch een patch uitvoeren voor SQL Server en het besturingssysteem. U een dag van de week, tijd en duur opgeven voor een onderhoudsvenster als de SQL Server IaaS-extensie is geïnstalleerd. Azure voert de patch uit tijdens deze onderhoudssessie. Het onderhoudsschema maakt voor de tijd gebruik van de landinstellingen van de virtuele machine.  Zie [Automatisch patchen voor SQL Server op Azure Virtual Machines voor](virtual-machines-windows-sql-automated-patching.md)meer informatie.


## <a name="next-steps"></a>Volgende stappen

Migreer uw SQL Server VM naar Azure:

* [Een SQL Server-database naar een SQL Server in een virtuele machine van Azure migreren](virtual-machines-windows-migrate-sql.md)

Aan de slag met SQL Server op Azure Virtual Machines:

* [Create a SQL Server VM in the Azure portal](quickstart-sql-vm-create-portal.md) (Een SQL Server-VM maken in Azure Portal)

Antwoorden op veelgestelde vragen over SQL Server VM's:

* [Veelgestelde vragen voor SQL Server op Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-faq.md)

Meer informatie over de opties voor het einde van de ondersteuning en uitgebreide beveiligingsupdates:

* [Einde van de ondersteuning](/sql/sql-server/end-of-support/sql-server-end-of-life-overview) & [uitgebreide beveiligingsupdates](/sql/sql-server/end-of-support/sql-server-extended-security-updates)