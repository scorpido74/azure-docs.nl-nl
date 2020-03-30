---
title: Veelgestelde vragen over SQL Server op Linux Azure Virtual Machines | Microsoft Documenten
description: In dit artikel vindt u antwoorden op veelgestelde vragen over het uitvoeren van SQL Server op Linux Azure VM's.
services: virtual-machines-linux
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: troubleshooting
ms.workload: iaas-sql-server
ms.date: 12/13/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 1e729c608a2cad28c810f8d5236360c909a496b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70082035"
---
# <a name="frequently-asked-questions-for-sql-server-on-linux-azure-virtual-machines"></a>Veelgestelde vragen voor SQL Server op Linux Azure Virtual Machines

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](sql-server-linux-faq.md)

In dit artikel vindt u antwoorden op enkele van de meest voorkomende vragen over het uitvoeren van [SQL Server op Linux Azure Virtual Machines.](sql-server-linux-virtual-machines-overview.md)

> [!NOTE]
> Dit artikel richt zich op specifieke problemen voor SQL Server op Linux VM's. Zie de [veelgestelde vragen over Windows.](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="images"></a><a id="images"></a>Afbeeldingen

1. **Welke SQL Server virtuele machine galerij afbeeldingen zijn beschikbaar?**

   Azure onderhoudt virtuele machineafbeeldingen voor alle ondersteunde grote releases van SQL Server op alle edities voor zowel Linux als Windows. Zie voor meer informatie de volledige lijst met [Linux VM-afbeeldingen](sql-server-linux-virtual-machines-overview.md#create) en [Windows VM-afbeeldingen](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).

1. **Worden bestaande SQL Server-virtuele machinegalerieafbeeldingen bijgewerkt?**

   Elke twee maanden worden SQL Server-afbeeldingen in de virtuele machinegalerij bijgewerkt met de nieuwste Linux- en Windows-updates. Voor Linux-afbeeldingen bevat dit de nieuwste systeemupdates. Voor Windows-afbeeldingen omvat dit alle updates die belangrijk zijn gemarkeerd in Windows Update, inclusief belangrijke SQL Server-beveiligingsupdates en servicepacks. SQL Server cumulatieve updates worden anders behandeld voor Linux en Windows. Voor Linux zijn ook cumulatieve SQL Server-updates opgenomen in de vernieuwing. Maar op dit moment worden Windows VM's niet bijgewerkt met SQL Server of Windows Server cumulatieve updates.

1. **Welke gerelateerde SQL Server-pakketten zijn ook geïnstalleerd?**

   Zie [Geïnstalleerde pakketten](sql-server-linux-virtual-machines-overview.md#packages)voor de standaard geïnstalleerde SQL Server-pakketten op SQL Server Linux VM's.

1. **Kunnen sql server-afbeeldingen met virtuele machines uit de galerie worden verwijderd?**

   Ja. Azure behoudt slechts één afbeelding per grote versie en editie. Wanneer bijvoorbeeld een nieuw SQL Server-servicepack wordt uitgebracht, voegt Azure een nieuwe afbeelding toe aan de galerie voor dat servicepack. De SQL Server-afbeelding voor het vorige servicepack wordt onmiddellijk verwijderd uit de Azure-portal. Het is echter nog steeds beschikbaar voor de levering van PowerShell voor de komende drie maanden. Na drie maanden is de vorige servicepackafbeelding niet meer beschikbaar. Dit verwijderingsbeleid is ook van toepassing als een SQL Server-versie niet wordt ondersteund wanneer deze het einde van de levenscyclus bereikt.

## <a name="creation"></a>Maken

1. **Hoe maak ik een Linux Azure virtuele machine met SQL Server?**

   De eenvoudigste oplossing is het maken van een Linux Virtual Machine die SQL Server bevat. Zie Een virtuele machine van [Linux SQL Server inde Azure inrichten](provision-sql-server-linux-virtual-machine.md)voor een zelfstudie over het aanmelden voor Azure en het maken van een SQL VM van de portal. U hebt ook de mogelijkheid om SQL Server handmatig te installeren op een VM met een gratis gelicentieerde editie (Developer of Express) of door een on-premises licentie opnieuw te gebruiken. Als u uw eigen licentie meeneemt, moet u [license mobility via Software Assurance op Azure](https://azure.microsoft.com/pricing/license-mobility)hebben.

1. **Waarom kan ik geen RHEL- of SLES SQL Server VM inrichten met een Azure-abonnement met een bestedingslimiet?**

   Virtuele RHEL- en SLES-machines vereisen een abonnement zonder bestedingslimiet en een geverifieerde betalingsmethode (meestal een creditcard) die aan het abonnement is gekoppeld. Als u een RHEL- of SLES VM indient zonder de bestedingslimiet te opheffen, wordt uw abonnement uitgeschakeld en worden alle VM's/services gestopt. Als u deze status tegenkomt, u het abonnement opnieuw inschakelen om [de bestedingslimiet te verwijderen.](https://account.windowsazure.com/subscriptions) Uw resterende credits worden hersteld voor de huidige factureringscyclus, maar een RHEL- of SLES VM-afbeeldingstoeslag gaat in tegen uw creditcard als u ervoor kiest om deze opnieuw te starten en door te voeren.

## <a name="licensing"></a>Licentieverlening

1. **Hoe kan ik mijn gelicentieerd exemplaar van SQL Server installeren op een Azure-VM?**

   Maak eerst een Linux OS-only virtuele machine. Voer vervolgens de [SQL Server-installatiestappen](https://docs.microsoft.com/sql/linux/sql-server-linux-setup#platforms) uit voor uw Linux-distributie. Tenzij u een van de vrij gelicentieerde edities van SQL Server installeert, moet u ook beschikken over een SQL Server-licentie en [licentiemobiliteit via Software Assurance op Azure.](https://azure.microsoft.com/pricing/license-mobility/)

1. **Zijn er Bring-Your-Own-License (BYOL) Linux virtuele machine afbeeldingen voor SQL Server?**

   Op dit moment zijn er geen BYOL Linux virtuele machine afbeeldingen voor SQL Server. U SQL Server echter handmatig installeren op een VM met alleen Linux,zoals besproken in de vorige vragen.

1. **Kan ik een VM wijzigen zodat mijn eigen SQL Server-licentie wordt gebruikt, wanneer de VM is gemaakt vanuit een van de Betalen per gebruik-installatiekopieën uit de galerie?**

   Nee. U niet overschakelen van pay-per-second licenties naar het gebruik van uw eigen licentie. U moet een nieuwe Linux-vm maken, SQL Server installeren en uw gegevens migreren. Zie de vorige vraag voor meer informatie over het meenemen van uw eigen licentie.

## <a name="administration"></a>Beheer

1. **Kan ik een Virtuele Linux Server-machine beheren met SQL Server Management Studio (SSMS)?**

   Ja, maar SSMS is momenteel een windows-only tool. U moet op afstand verbinding maken vanaf een Windows-machine om SSMS te gebruiken met Linux SQL Server VM's. Lokaal op Linux kan de nieuwe [mssql-conf tool](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf) veel administratieve taken uitvoeren. Zie [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is)voor een hulpprogramma voor databasebeheer met meerdere platforms.

1. **Kan ik SQL Server volledig verwijderen uit een SQL-VM?**

   Ja, maar er worden nog steeds kosten in rekening gebracht voor uw SQL VM, zoals beschreven in [prijsrichtlijnen voor SQL Server Azure VM's.](../../windows/sql/virtual-machines-windows-sql-server-pricing-guidance.md?toc=%2fazure%2fvirtual-machines%2flinux%2fsql%2ftoc.json) Als u de SQL Server niet langer nodig hebt, kunt u een nieuwe virtuele machine implementeren en de gegevens en toepassingen migreren naar de nieuwe virtuele machine. Vervolgens kunt u de virtuele SQL Server-machine verwijderen.

## <a name="updating-and-patching"></a>Bijwerken en patchen

1. **Hoe upgrade ik naar een nieuwe versie/editie van de SQL Server in een Azure VM?**

   er wordt momenteel geen upgrade uitgevoerd voor SQL Server in een virtuele machine van Azure. Maak een nieuwe Azure-virtuele machine met de gewenste SQL Server-versie/-editie en migreer uw databases naar de nieuwe server met behulp van [standaard gegevensmigratietechnieken.](https://docs.microsoft.com/sql/linux/sql-server-linux-migrate-overview)

## <a name="general"></a>Algemeen

1. **Worden SQL Server High Availability-oplossingen ondersteund op Azure VM's?**

   Momenteel niet. Always On Availability Groups en Failover Clustering vereisen beide een clusteroplossing in Linux, zoals Pacemaker. De ondersteunde Linux-distributies voor SQL Server ondersteunen hun add-ons met hoge beschikbaarheid in de cloud niet.

## <a name="resources"></a>Resources

**Linux VM's:**

* [Overzicht van SQL Server op een Linux VM](sql-server-linux-virtual-machines-overview.md)
* [Een SQL Server Linux VM inrichten](provision-sql-server-linux-virtual-machine.md)
* [Documentatie voor SQL Server op Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)

**Windows VM's:**

* [Overzicht van SQL Server op een Windows VM](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
* [Een SQL Server Windows VM inrichten](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)
* [Veelgestelde vragen (Windows)](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)