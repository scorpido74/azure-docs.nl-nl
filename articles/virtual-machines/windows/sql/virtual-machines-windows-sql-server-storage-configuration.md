---
title: Opslag configuratie voor SQL Server Vm's | Microsoft Docs
description: In dit onderwerp wordt beschreven hoe Azure opslag configureert voor SQL Server Vm's tijdens het inrichten (Resource Manager-implementatie model). Ook wordt uitgelegd hoe u opslag kunt configureren voor uw bestaande SQL Server Vm's.
services: virtual-machines-windows
documentationcenter: na
author: ninarn
manager: craigg
tags: azure-resource-manager
ms.assetid: 169fc765-3269-48fa-83f1-9fe3e4e40947
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/05/2017
ms.author: ninarn
ms.openlocfilehash: 5a8b6c9885099ed549f7f29d7f2096de5983d9d0
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100388"
---
# <a name="storage-configuration-for-sql-server-vms"></a>Opslag configuratie voor SQL Server Vm's

Wanneer u een installatie kopie van een SQL Server virtuele machine in azure configureert, helpt de portal u bij het automatiseren van uw opslag configuratie. Dit omvat het koppelen van opslag aan de VM, waardoor die opslag toegankelijk is voor SQL Server en deze kan worden geconfigureerd om te worden geoptimaliseerd voor uw specifieke prestatie vereisten.

In dit onderwerp wordt uitgelegd hoe Azure opslag voor uw SQL Server Vm's configureert tijdens het inrichten en voor bestaande virtuele machines. Deze configuratie is gebaseerd op de [Aanbevolen prestatie procedures](virtual-machines-windows-sql-performance.md) voor Azure-vm's met SQL Server.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Vereisten

Als u de configuratie-instellingen voor automatische opslag wilt gebruiken, zijn voor uw virtuele machine de volgende kenmerken vereist:

* Ingericht met een [SQL Server galerie-afbeelding](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).
* Maakt gebruik van het [Resource Manager-implementatie model](../../../azure-resource-manager/resource-manager-deployment-model.md).
* Maakt gebruik van [Premium-ssd's](../disks-types.md).

## <a name="new-vms"></a>Nieuwe Vm's

In de volgende secties wordt beschreven hoe u opslag configureert voor nieuwe SQL Server virtuele machines.

### <a name="azure-portal"></a>Azure Portal

Bij het inrichten van een Azure-VM met behulp van een SQL Server galerie-afbeelding, kunt u ervoor kiezen om de opslag automatisch te configureren voor uw nieuwe virtuele machine. U geeft de opslag grootte, prestatie limieten en het type werk belasting op. Op de volgende scherm afbeelding ziet u de Blade opslag configuratie die wordt gebruikt tijdens het inrichten van SQL VM.

![Configuratie van VM-opslag SQL Server tijdens het inrichten](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

Op basis van uw keuzes voert Azure de volgende opslag configuratie taken uit na het maken van de VM:

* Hiermee worden Premium-Ssd's gemaakt en gekoppeld aan de virtuele machine.
* Hiermee configureert u de gegevens schijven die toegankelijk moeten zijn voor SQL Server.
* Hiermee configureert u de gegevens schijven in een opslag groep op basis van de opgegeven grootte en prestaties (IOPS en door Voer) vereisten.
* Koppelt de opslag groep aan een nieuw station op de virtuele machine.
* Optimaliseert dit nieuwe station op basis van het opgegeven type werk belasting (gegevens opslag, transactionele verwerking of algemeen).

Zie de [sectie opslag configuratie](#storage-configuration)voor meer informatie over hoe Azure opslag instellingen configureert. Zie [de zelf studie over het inrichten](virtual-machines-windows-portal-sql-server-provision.md)voor een volledig overzicht van het maken van een SQL Server-VM in de Azure Portal.

### <a name="resource-manage-templates"></a>Resource beheer sjablonen

Als u de volgende Resource Manager-sjablonen gebruikt, worden standaard twee Premium-gegevens schijven gekoppeld zonder configuratie van de opslag groep. U kunt deze sjablonen echter aanpassen om het aantal Premium-gegevens schijven te wijzigen dat aan de virtuele machine is gekoppeld.

* [Een VM maken met geautomatiseerde back-up](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
* [Een VM maken met geautomatiseerde patching](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
* [Een virtuele machine maken met Azure-integratie](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

## <a name="existing-vms"></a>Bestaande Vm's

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Voor bestaande SQL Server Vm's kunt u enkele opslag instellingen wijzigen in de Azure Portal. Open de [resource van de virtuele SQL-machines](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource)en selecteer **overzicht**. Op de pagina overzicht van SQL Server wordt het huidige opslag gebruik van uw VM weer gegeven. Alle stations die op uw virtuele machine bestaan, worden weer gegeven in deze grafiek. Voor elk station wordt de opslag ruimte weer gegeven in vier secties:

* SQL-gegevens
* SQL-logboek
* Overige (niet-SQL-opslag)
* Beschikbaar

Als u de opslag instellingen wilt wijzigen, selecteert u **configureren** onder **instellingen**. 

![Opslag configureren voor bestaande SQL Server VM](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-existing.png)

De configuratie opties die u ziet, zijn afhankelijk van of u deze functie al eerder hebt gebruikt. Wanneer u voor de eerste keer gebruikt, kunt u uw opslag vereisten voor een nieuw station opgeven. Als u deze functie eerder hebt gebruikt om een station te maken, kunt u ervoor kiezen om de opslag van dat station uit te breiden.

### <a name="use-for-the-first-time"></a>Voor de eerste keer gebruiken

Als dit de eerste keer is dat u deze functie gebruikt, kunt u de opslag grootte en prestatie limieten voor een nieuw station opgeven. Deze ervaring is vergelijkbaar met wat u ziet tijdens de inrichtings tijd. Het belangrijkste verschil is dat u het type werk belasting niet mag opgeven. Deze beperking voor komt dat bestaande SQL Server configuraties op de virtuele machine worden onderbroken.

Azure maakt een nieuw station op basis van uw specificaties. In dit scenario voert Azure de volgende opslag configuratie taken uit:

* Hiermee maakt en koppelt u Premium Storage-gegevens schijven aan de virtuele machine.
* Hiermee configureert u de gegevens schijven die toegankelijk moeten zijn voor SQL Server.
* Hiermee configureert u de gegevens schijven in een opslag groep op basis van de opgegeven grootte en prestaties (IOPS en door Voer) vereisten.
* Koppelt de opslag groep aan een nieuw station op de virtuele machine.

Zie de [sectie opslag configuratie](#storage-configuration)voor meer informatie over hoe Azure opslag instellingen configureert.

### <a name="add-a-new-drive"></a>Een nieuw station toevoegen

Als u de opslag al hebt geconfigureerd op uw SQL Server VM, worden er twee nieuwe opties in de groeiende opslag opgesteld. De eerste optie is het toevoegen van een nieuw station, waardoor het prestatie niveau van uw virtuele machine kan toenemen.

Nadat u het station hebt toegevoegd, moet u echter een extra hand matige configuratie uitvoeren om de prestaties te verhogen.

### <a name="extend-the-drive"></a>Het station uitbreiden

De andere optie voor het uitbreiden van opslag is het uitbreiden van het bestaande station. Met deze optie wordt de beschik bare opslag ruimte voor uw station verhoogd, maar wordt de prestaties niet verbeterd. Met opslag groepen kunt u het aantal kolommen niet wijzigen nadat de opslag groep is gemaakt. Het aantal kolommen bepaalt het aantal parallelle schrijf bewerkingen, dat over de gegevens schijven kan worden verdeeld. Daarom kunnen toegevoegde gegevens schijven de prestaties niet verhogen. Ze kunnen alleen meer opslag ruimte bieden voor de gegevens die worden geschreven. Deze beperking betekent ook dat bij het uitbreiden van het station het minimum aantal gegevens schijven dat u kunt toevoegen, wordt bepaald door het aantal kolommen. Dus als u een opslag groep met vier gegevens schijven maakt, is het aantal kolommen ook vier. Telkens wanneer u de opslag ruimte uitbreidt, moet u ten minste vier gegevens schijven toevoegen.

![Een station uitbreiden voor een SQL-VM](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-extend-a-drive.png)

## <a name="storage-configuration"></a>Opslagconfiguratie

Deze sectie bevat een Naslag informatie over de opslag configuratie wijzigingen die Azure automatisch uitvoert tijdens het inrichten of configureren van een SQL-VM in de Azure Portal.

* Als u minder dan twee TBs van opslag voor uw virtuele machine hebt geselecteerd, maakt Azure geen opslag groep.
* Als u ten minste twee TBs van opslag voor uw virtuele machine hebt geselecteerd, configureert Azure een opslag groep. In de volgende sectie van dit onderwerp vindt u de details van de configuratie van de opslag groep.
* Automatische opslag configuratie maakt altijd gebruik van [Premium ssd's](../disks-types.md) P30-gegevens schijven. Daarom is er een 1:1-toewijzing tussen het geselecteerde aantal terabytes en het aantal gegevens schijven dat aan uw virtuele machine is gekoppeld.

Zie de pagina [prijzen voor opslag](https://azure.microsoft.com/pricing/details/storage) op het tabblad **Disk Storage** voor prijs informatie.

### <a name="creation-of-the-storage-pool"></a>De opslag groep maken

Azure gebruikt de volgende instellingen voor het maken van de opslag groep op SQL Server Vm's.

| Instelling | Value |
| --- | --- |
| Stripe-grootte |256 KB (data warehousing); 64 KB (transactioneel) |
| Schijfformaten |1 TB elk |
| Cache |Lezen |
| Toewijzings grootte |64 KB NTFS Allocation Unit Size |
| Directe bestands initialisatie |Enabled |
| Pagina's in het geheugen vergren delen |Enabled |
| Herstel |Eenvoudig herstel (geen tolerantie) |
| Aantal kolommen |Aantal gegevens schijven<sup>1</sup> |
| TempDB-locatie |Opgeslagen op gegevens schijven<sup>2</sup> |

<sup>1</sup> nadat de opslag groep is gemaakt, kunt u het aantal kolommen in de opslag groep niet wijzigen.

<sup>2</sup> deze instelling is alleen van toepassing op het eerste station dat u maakt met behulp van de functie voor opslag configuratie.

## <a name="workload-optimization-settings"></a>Instellingen voor werk belasting optimalisatie

In de volgende tabel worden de drie beschik bare opties voor werkbelasting typen en de bijbehorende optimalisaties beschreven:

| Type werkbelasting | Description | Optimalisaties |
| --- | --- | --- |
| **Algemeen** |Standaard instelling die de meeste werk belastingen ondersteunt |Geen |
| **Transactionele verwerking** |Optimaliseert de opslag voor traditionele OLTP-workloads van data bases |Tracerings vlag 1117<br/>Tracerings vlag 1118 |
| **Gegevensopslag** |Optimaliseert de opslag voor analyse-en rapportage werk belastingen |Tracerings vlag 610<br/>Tracerings vlag 1117 |

> [!NOTE]
> U kunt het type werk belasting alleen opgeven wanneer u een virtuele SQL-machine inricht door deze te selecteren in de stap opslag configuratie.

## <a name="next-steps"></a>Volgende stappen

Zie [SQL Server op azure virtual machines](virtual-machines-windows-sql-server-iaas-overview.md)voor andere onderwerpen met betrekking tot het uitvoeren van SQL Server in azure vm's.
