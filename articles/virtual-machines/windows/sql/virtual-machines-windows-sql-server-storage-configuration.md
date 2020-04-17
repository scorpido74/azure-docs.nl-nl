---
title: Opslagconfiguratie voor SQL Server VM's | Microsoft Documenten
description: In dit onderwerp wordt beschreven hoe Azure opslag voor SQL Server VM's configureert tijdens het inrichten (Resource Manager deployment model). Het legt ook uit hoe u opslag configureren voor uw bestaande SQL Server VM's.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.assetid: 169fc765-3269-48fa-83f1-9fe3e4e40947
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/26/2019
ms.author: mathoma
ms.openlocfilehash: 93f01b3c23e08e7f432841d8a77cbe3602bff1c5
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81482142"
---
# <a name="storage-configuration-for-sql-server-vms"></a>Opslagconfiguratie voor SQL Server-VM's

Wanneer u een SQL Server-virtuele machineafbeelding configureert in Azure, helpt de portal uw opslagconfiguratie te automatiseren. Dit omvat het koppelen van opslag aan de VM, het toegankelijk maken van die opslag voor SQL Server en het configureren om te optimaliseren voor uw specifieke prestatievereisten.

In dit onderwerp wordt uitgelegd hoe Azure opslag voor uw SQL Server VM's configureert, zowel tijdens het inrichten als voor bestaande VM's. Deze configuratie is gebaseerd op de [aanbevolen procedures](virtual-machines-windows-sql-performance.md) voor Azure VM's met SQL Server.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Vereisten

Om de instellingen voor geautomatiseerde opslagconfiguratie te gebruiken, heeft uw virtuele machine de volgende kenmerken nodig:

* Ingericht met een [SQL Server-galerieafbeelding](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).
* Gebruikt het [implementatiemodel resourcebeheer](../../../azure-resource-manager/management/deployment-models.md).
* Maakt gebruik van [premium SSD's](../disks-types.md).

## <a name="new-vms"></a>Nieuwe VM's

In de volgende secties wordt beschreven hoe u opslag configureert voor nieuwe virtuele SQL Server-machines.

### <a name="azure-portal"></a>Azure Portal

Wanneer u een Azure VM indient met een SQL Server-galerieafbeelding, selecteert u **Configuratie wijzigen** op het tabblad **SQL Server-instellingen** om de pagina Prestatiegeoptimaliseerde opslagconfiguratie te openen. U de waarden standaard laten staan of het type schijfconfiguratie wijzigen dat het beste bij uw behoeften past op basis van uw werkbelasting. 

![SQL Server VM-opslagconfiguratie tijdens de inrichting](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

Selecteer het type werkbelasting waarvoor u uw SQL Server implementeert onder **Opslagoptimalisatie.** Met de optie **Algemene** optimalisatie heeft u standaard één gegevensschijf met 5000 max IOPS en gebruikt u ditzelfde station voor uw gegevens, transactielogboek en TempDB-opslag. Als u **Transactionele verwerking** (OLTP) of **gegevensopslag selecteert,** wordt een afzonderlijke schijf voor gegevens, een afzonderlijke schijf voor het transactielogboek en wordt lokale SSD voor TempDB gebruikt. Er zijn geen opslagverschillen tussen **transactionele verwerking** en **gegevensopslag,** maar het verandert wel uw [streepconfiguratie en traceervlaggen.](#workload-optimization-settings) Als u premium opslag kiest, wordt de caching ingesteld op *Alleen* lezen voor het gegevensstation en *Geen* voor het logboekstation volgens de best practices voor [sql server VM-prestaties](virtual-machines-windows-sql-performance.md). 

![SQL Server VM-opslagconfiguratie tijdens de inrichting](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration.png)

De schijfconfiguratie is volledig aanpasbaar, zodat u de opslagtopologie, het schijftype en de IPP's die u nodig hebt voor uw SQL Server VM-workload configureren. U ultrassd (preview) ook gebruiken als optie voor het **schijftype** als uw SQL Server VM zich in een van de ondersteunde regio's bevindt (Oost-VS 2, Zuidoost-Azië en Noord-Europa) en u ultraschijven hebt ingeschakeld [voor uw abonnement.](/azure/virtual-machines/windows/disks-enable-ultra-ssd)  

Bovendien hebt u de mogelijkheid om de caching voor de schijven in te stellen. Azure VM's hebben een multi-tier caching-technologie genaamd [Blob Cache](/azure/virtual-machines/windows/premium-storage-performance#disk-caching) bij gebruik met [Premium-schijven.](/azure/virtual-machines/windows/disks-types#premium-ssd) Blob Cache maakt gebruik van een combinatie van de Virtual Machine RAM en lokale SSD voor caching. 

Schijfcache voor Premium SSD kan *ReadOnly,* *ReadWrite* of *Geen*zijn. 

- *ReadOnly* caching is zeer gunstig voor SQL Server-gegevensbestanden die zijn opgeslagen op Premium Storage. *ReadAlleen* caching brengt lage leeslatentie, hoog lees-IOPS en doorvoer, omdat reads worden uitgevoerd vanuit de cache, die zich binnen het VM-geheugen en de lokale SSD bevindt. Deze reads zijn veel sneller dan leest van dataschijf, die afkomstig is van de Azure blob-opslag. Premium opslag telt niet de leest geserveerd vanuit de cache naar de schijf IOPS en doorvoer. Daarom is uw toepasselijke in staat om een hogere totale IOPS en doorvoer te bereiken. 
- *Geen* cacheconfiguratie moet worden gebruikt voor de schijven die sql serverlogbestand hosten, omdat het logboekbestand achtereenvolgens wordt geschreven en niet profiteert van *ReadOnly-caching.* 
- *ReadWrite-caching* mag niet worden gebruikt om SQL Server-bestanden te hosten, omdat SQL Server de gegevensconsistentie met de *ReadWrite-cache* niet ondersteunt. Schrijft afvalcapaciteit van de *ReadOnly* blob cache en latencies iets toenemen als schrijft gaan door *ReadOnly* blob cache lagen. 


   > [!TIP]
   > Zorg ervoor dat uw opslagconfiguratie overeenkomt met de beperkingen die worden opgelegd door de geselecteerde VM-grootte. Als u opslagparameters kiest die de prestatielimiet van `The desired performance might not be reached due to the maximum virtual machine disk performance cap.`de VM-grootte overschrijden, wordt dit fout: . Verlaag de IPP door het schijftype te wijzigen of verhoog de beperking van de prestatielimiet door de VM-grootte te vergroten. 


Op basis van uw keuzes voert Azure de volgende opslagconfiguratietaken uit na het maken van de VM:

* Creëert en hecht premium SSD's aan de virtuele machine.
* Hiermee configureert u de gegevensschijven om toegankelijk te zijn voor SQL Server.
* Hiermee configureert u de gegevensschijven in een opslaggroep op basis van de vereiste vereisten voor de opgegeven grootte en prestaties (IOPS en doorvoer).
* Associeert de opslagpool met een nieuw station op de virtuele machine.
* Optimaliseert dit nieuwe station op basis van uw opgegeven werkbelastingtype (gegevensopslag, transactionele verwerking of algemeen).

Zie de [sectie Opslagconfiguratie](#storage-configuration)voor meer informatie over hoe Azure opslaginstellingen configureert. Zie [de innamezelfingshandleiding](virtual-machines-windows-portal-sql-server-provision.md)voor een volledige walkthrough van het maken van een SQL Server VM in de Azure-portal.

### <a name="resource-manage-templates"></a>Sjablonen voor resourcebeheer

Als u de volgende ResourceManager-sjablonen gebruikt, worden standaard twee premium gegevensschijven gekoppeld, zonder configuratie van de opslaggroep. U deze sjablonen echter aanpassen om het aantal premium gegevensschijven te wijzigen dat aan de virtuele machine is gekoppeld.

* [VM maken met geautomatiseerde back-up](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
* [VM maken met geautomatiseerde patching](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
* [VM maken met AKV-integratie](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

### <a name="quickstart-template"></a>Quickstartsjabloon

U de volgende snelstartsjabloon gebruiken om een SQL Server VM te implementeren met behulp van opslagoptimalisatie. 

* [VM maken met opslagoptimalisatie](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage/)
* [VM maken met UltraSSD](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage-ultrassd)

## <a name="existing-vms"></a>Bestaande VM's

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Voor bestaande SQL Server VM's u enkele opslaginstellingen in de Azure-portal wijzigen. Open de [bron van virtuele SQL-machines](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource)en selecteer **Overzicht**. De pagina SQL Server-overzicht toont het huidige opslaggebruik van uw vm. Alle stations die op uw VM staan, worden weergegeven in deze grafiek. Voor elk station wordt de opslagruimte in vier secties weergegeven:

* SQL-gegevens
* SQL-logboek
* Andere (niet-SQL-opslag)
* Beschikbaar

Als u de opslaginstellingen wilt wijzigen, selecteert **u Configureren** onder **Instellingen**. 

![Opslag configureren voor bestaande SQL Server VM](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-existing.png)

U de schijfinstellingen wijzigen voor de stations die zijn geconfigureerd tijdens het SQL Server VM-creatieproces. Als **u Station uitbreiden selecteert,** wordt de pagina voor het wijzigen van het station geopend, zodat u het schijftype wijzigen en extra schijven toevoegen. 

![Opslag configureren voor bestaande SQL Server VM](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-extend-drive.png)


## <a name="storage-configuration"></a>Opslagconfiguratie

In deze sectie vindt u een referentie voor de wijzigingen in de opslagconfiguratie die Azure automatisch uitvoert tijdens SQL VM-inrichting of -configuratie in de Azure-portal.

* Azure configureert een opslaggroep uit opslag die is geselecteerd vanuit uw vm. In het volgende gedeelte van dit onderwerp vindt u informatie over de configuratie van de opslaggroep.
* Automatische opslagconfiguratie maakt altijd gebruik van [premium SSD's](../disks-types.md) P30-gegevensschijven. Daarom is er een 1:1-toewijzing tussen het geselecteerde aantal Terabytes en het aantal gegevensschijven dat aan uw vm is gekoppeld.

Zie de [prijspagina Opslag op](https://azure.microsoft.com/pricing/details/storage) het tabblad **Schijfopslag** voor prijsinformatie.

### <a name="creation-of-the-storage-pool"></a>Creatie van de opslagpool

Azure gebruikt de volgende instellingen om de opslaggroep op SQL Server VM's te maken.

| Instelling | Waarde |
| --- | --- |
| Streepgrootte |256 KB (Data warehousing); 64 KB (Transactioneel) |
| Schijfformaten |1 TB per stuk |
| Cache |Lezen |
| Toewijzingsgrootte |64 KB NTFS-toewijzingseenheidsgrootte |
| Herstel | Eenvoudig herstel (geen veerkracht) |
| Aantal kolommen |Aantal gegevensschijven tot 8<sup>1</sup> |


<sup>1</sup> Nadat de opslaggroep is gemaakt, u het aantal kolommen in de opslaggroep niet wijzigen.


## <a name="workload-optimization-settings"></a>Instellingen voor workloadoptimalisatie

In de volgende tabel worden de drie beschikbare werkbelastingsopties en de bijbehorende optimalisaties beschreven:

| Werkbelastingtype | Beschrijving | Optimalisaties |
| --- | --- | --- |
| **Algemeen** |Standaardinstelling die de meeste workloads ondersteunt |Geen |
| **Transactionele verwerking** |Optimaliseert de opslag voor traditionele DATABASE OLTP-workloads |Trace Flag 1117<br/>Trace flag 1118 |
| **Data warehousing** |Optimaliseert de opslag voor analytische en rapportageworkloads |Trace Flag 610<br/>Trace Flag 1117 |

> [!NOTE]
> U het werkbelastingtype alleen opgeven wanneer u een VIRTUELE SQL-machine indient door deze te selecteren in de stap opslagconfiguratie.

## <a name="next-steps"></a>Volgende stappen

Zie [SQL Server op Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md)voor andere onderwerpen met betrekking tot het uitvoeren van SQL Server in Azure VM's.
