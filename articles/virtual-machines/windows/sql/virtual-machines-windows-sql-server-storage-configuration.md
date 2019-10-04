---
title: Opslag configuratie voor SQL Server Vm's | Microsoft Docs
description: In dit onderwerp wordt beschreven hoe Azure opslag configureert voor SQL Server Vm's tijdens het inrichten (Resource Manager-implementatie model). Ook wordt uitgelegd hoe u opslag kunt configureren voor uw bestaande SQL Server Vm's.
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
ms.date: 12/05/2017
ms.author: mathoma
ms.openlocfilehash: 57a325dd297955296a94db134b6a2a6d58a37f03
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828618"
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

Bij het inrichten van een Azure-VM met behulp van een SQL Server galerie-afbeelding, selecteert u **configuratie wijzigen** op het tabblad **SQL Server instellingen** om de configuratie pagina geoptimaliseerd voor prestaties te openen. U kunt de waarden standaard laten staan of het type schijf configuratie aanpassen dat het beste bij uw behoeften past, op basis van uw werk belasting. 

![Configuratie van VM-opslag SQL Server tijdens het inrichten](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

Selecteer het type werk belasting waarvoor u uw SQL Server wilt implementeren onder **opslag optimalisatie**. Met de optie **Algemeen** optimalisatie hebt u standaard één gegevens schijf met een maximale IOPS van 5000. u gebruikt hetzelfde station voor uw gegevens, het transactie logboek en de tempdb-opslag. Als u **transactionele verwerking** (OLTP) of **gegevens opslag** selecteert, wordt er een afzonderlijke schijf voor gegevens gemaakt, een afzonderlijke schijf voor het transactie logboek en lokale SSD gebruiken voor TempDB. Er zijn geen opslag verschillen tussen **transactionele verwerking** en **Data Warehousing**, maar de configuratie van de [Stripe en tracerings markeringen](#workload-optimization-settings)worden gewijzigd. Als u Premium Storage kiest, wordt de cache ingesteld op *ReadOnly* voor het gegevens station en *geen* voor het logboek station volgens [SQL Server aanbevolen procedures](virtual-machines-windows-sql-performance.md)voor de VM-prestaties. 

![Configuratie van VM-opslag SQL Server tijdens het inrichten](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration.png)

De schijf configuratie kan volledig worden aangepast, zodat u de opslag topologie, het schijf type en de IOPs die u nodig hebt voor uw SQL Server VM-workload kunt configureren. U hebt ook de mogelijkheid om UltraSSD (preview) te gebruiken als een optie voor het **schijf type** als uw SQL Server virtuele machine zich in een van de ondersteunde regio's bevindt (VS-Oost 2, zuidoost-azië en Europa-Noord) en u [Ultra disks hebt ingeschakeld voor uw abonnement](/azure/virtual-machines/windows/disks-enable-ultra-ssd).  

Daarnaast hebt u de mogelijkheid om de cache voor de schijven in te stellen. Azure-Vm's hebben een cache technologie met meerdere lagen, die [BLOB-cache](/azure/virtual-machines/windows/premium-storage-performance#disk-caching) heet als deze wordt gebruikt met [Premium-schijven](/azure/virtual-machines/windows/disks-types#premium-ssd). BLOB-cache maakt gebruik van een combi natie van het RAM-geheugen van de virtuele machine en de lokale SSD voor caching. 

Schijf cache voor Premium-SSD kan *alleen-lezen*, *readwrite* of *geen*zijn. 

- *ReadOnly* -caching is zeer nuttig voor SQL Server gegevens bestanden die zijn opgeslagen op Premium Storage. *Alleen* -lezen cache levert lage lees latentie, grote Lees-IOPS en door Voer als, lees bewerkingen uit de cache, het besturings systeem in het geheugen van de virtuele machine en de lokale SSD. Deze Lees bewerkingen zijn veel sneller dan lees bewerkingen van gegevens schijf, afkomstig uit de Azure Blob-opslag. Premium-opslag telt niet de Lees bewerkingen van de cache naar de schijf-IOPS en door voer. Daarom kan uw toepas bare totale IOPS-ant door voer worden gerealiseerd. 
- *Geen* cache configuratie moet worden gebruikt voor de schijven die worden gehost SQL Server logboek bestand, terwijl het logboek bestand opeenvolgend wordt geschreven en niet in aanmerking komt voor *ReadOnly* -caching. 
- *Readwrite* -caching mag niet worden gebruikt voor het hosten van SQL Server-bestanden omdat SQL Server geen consistentie van gegevens met de *readwrite* -cache ondersteunt. Schrijft de verspilings capaciteit van de *alleen-lezen* BLOB-cache en de latenties lichter toe als schrijf bewerkingen via *alleen-lezen* BLOB-cache lagen passeren. 


   > [!TIP]
   > Zorg ervoor dat uw opslag configuratie overeenkomt met de beperkingen die zijn opgelegd door de geselecteerde VM-grootte. Als u opslag parameters kiest die de prestaties van de VM-grootte overschrijden, resulteert dit in een fout: `The desired performance might not be reached due to the maximum virtual machine disk performance cap.`. Verlaag de IOPs door het schijf type te wijzigen of verhoog de limiet voor de snelheid van de virtuele machine door de VM-grootte te verhogen. 


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

### <a name="quickstart-template"></a>Quickstart-sjabloon

U kunt de volgende Snelstartgids-sjabloon gebruiken om een SQL Server virtuele machine te implementeren met behulp van opslag optimalisatie. 

* [Een virtuele machine maken met opslag optimalisatie](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage/)
* [Een VM maken met behulp van UltraSSD](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage-ultrassd)

## <a name="existing-vms"></a>Bestaande Vm's

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Voor bestaande SQL Server Vm's kunt u enkele opslag instellingen wijzigen in de Azure Portal. Open de [resource van de virtuele SQL-machines](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource)en selecteer **overzicht**. Op de pagina overzicht van SQL Server wordt het huidige opslag gebruik van uw VM weer gegeven. Alle stations die op uw virtuele machine bestaan, worden weer gegeven in deze grafiek. Voor elk station wordt de opslag ruimte weer gegeven in vier secties:

* SQL-gegevens
* SQL-logboek
* Overige (niet-SQL-opslag)
* Beschikbaar

Als u de opslag instellingen wilt wijzigen, selecteert u **configureren** onder **instellingen**. 

![Opslag configureren voor bestaande SQL Server VM](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-existing.png)

U kunt de schijf instellingen wijzigen voor de stations die zijn geconfigureerd tijdens het proces voor het maken van de SQL Server-VM. Als u **station uitbreiden** selecteert, wordt de pagina voor het wijzigen van de schijf geopend, zodat u het schijf type kunt wijzigen en extra schijven kunt toevoegen. 

![Opslag configureren voor bestaande SQL Server VM](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-extend-drive.png)



## <a name="storage-configuration"></a>Opslagconfiguratie

Deze sectie bevat een Naslag informatie over de opslag configuratie wijzigingen die Azure automatisch uitvoert tijdens het inrichten of configureren van een SQL-VM in de Azure Portal.

* Azure configureert een opslag groep van opslag die is geselecteerd op de VM. In de volgende sectie van dit onderwerp vindt u meer informatie over de configuratie van de opslag groep.
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
