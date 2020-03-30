---
title: Een SQL Server-database migreren naar SQL Server op een vm | Microsoft Documenten
description: Meer informatie over het migreren van een on-premises gebruikersdatabase naar SQL Server in een virtuele Azure-machine.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 00fd08c6-98fa-4d62-a3b8-ca20aa5246b1
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.tgt_pltfrm: vm-windows-sql-server
ms.topic: article
ms.date: 08/18/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: c8314b04c05e2ecba2715b807171b5c1a2fa988a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646860"
---
# <a name="migrate-a-sql-server-database-to-sql-server-in-an-azure-vm"></a>Migreer een SQL Server-database naar een SQL Server in een virtuele machine van Azure

Er zijn een aantal methoden om een on-premises SQL Server-gebruikersdatabase te migreren naar SQL Server in een Azure VM. Dit artikel zal kort bespreken verschillende methoden en raden de beste methode voor verschillende scenario's.


[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

  > [!NOTE]
  > SQL Server 2008 en SQL Server 2008 R2 naderen het [einde van hun ondersteuningslevenscyclus](https://www.microsoft.com/sql-server/sql-server-2008) voor on-premises exemplaren. Als u de ondersteuning wilt uitbreiden, u uw SQL Server-exemplaar migreren naar een Azure VM of uitgebreide beveiligingsupdates kopen om deze on-premises te houden. Zie [Ondersteuning voor SQL Server 2008 en 2008 R2 uitbreiden met Azure](virtual-machines-windows-sql-server-2008-eos-extend-support.md) voor meer informatie.

## <a name="what-are-the-primary-migration-methods"></a>Wat zijn de primaire migratiemethoden?
De primaire migratiemethoden zijn:

* On-premises back-up uitvoeren met compressie en het back-upbestand handmatig kopiëren naar de virtuele Azure-machine
* Een back-up naar URL uitvoeren en vanuit de URL herstellen naar de virtuele Azure-machine
* De gegevens en logboekbestanden loskoppelen en vervolgens kopiëren naar Azure blob-opslag en vervolgens koppelen aan SQL Server in Azure VM vanaf URL
* On-premises fysieke machine converteren naar Hyper-V VHD, uploaden naar Azure Blob-opslag en implementeer vervolgens als nieuwe VM met geüploade VHD
* Harde schijf verzenden met Windows Import/Export Service
* Als u een alwaysonbeschikbaarheidsgroep-implementatie on-premises hebt, gebruikt u de [wizard Azure Replica toevoegen](../sqlclassic/virtual-machines-windows-classic-sql-onprem-availability.md) om een replica in Azure te maken en vervolgens failover, gebruikers aan te wijzen op de Azure-database-instantie
* Sql Server [transactionele replicatie gebruiken](https://msdn.microsoft.com/library/ms151176.aspx) om de Azure SQL Server-instantie als abonnee te configureren en vervolgens replicatie uit te schakelen, gebruikers naar de Azure-databaseinstantie te wijzen

> [!TIP]
> U dezelfde technieken ook gebruiken om databases tussen SQL Server VM's in Azure te verplaatsen. Er is bijvoorbeeld geen ondersteunde manier om een SQL Server-galerijafbeeldings-VM van de ene versie/editie naar de andere te upgraden. In dit geval moet u een nieuwe SQL Server VM maken met de nieuwe versie/editie en vervolgens een van de migratietechnieken in dit artikel gebruiken om uw databases te verplaatsen. 

## <a name="choosing-your-migration-method"></a>Uw migratiemethode kiezen
Migreer de databasebestanden naar de Azure VM met een gecomprimeerd back-upbestand voor optimale prestaties voor gegevensoverdracht.

Als u downtime tijdens het databasemigratieproces wilt minimaliseren, gebruikt u de optie AlwaysOn of de optie transactionele replicatie.

Als het niet mogelijk is om de bovenstaande methoden te gebruiken, migreert u uw database handmatig. Met deze methode begint u over het algemeen met een databaseback-up, gevolgd door een kopie van de databaseback-up in Azure en vervolgens een databaseherstel uitvoert. U de databasebestanden ook zelf kopiëren naar Azure en ze vervolgens koppelen. Er zijn verschillende methoden waarmee u dit handmatige proces uitvoeren om een database te migreren naar een Azure VM.

> [!NOTE]
> Wanneer u een upgrade uitvoert naar SQL Server 2014 of SQL Server 2016 van oudere versies van SQL Server, moet u overwegen of er wijzigingen nodig zijn. We raden u aan alle afhankelijkheden op functies aan te pakken die niet worden ondersteund door de nieuwe versie van SQL Server als onderdeel van uw migratieproject. Zie [Upgraden naar SQL Server voor](https://msdn.microsoft.com/library/bb677622.aspx)meer informatie over de ondersteunde edities en scenario's.

In de volgende tabel worden elk van de primaire migratiemethoden weergegeven en wordt besproken wanneer het gebruik van elke methode het meest geschikt is.

| Methode | Brondatabaseversie | Versie van de doeldatabase | Beperking van de grootte van de brondatabaseback-up | Opmerkingen |
| --- | --- | --- | --- | --- |
| [On-premises back-up uitvoeren met compressie en het back-upbestand handmatig kopiëren naar de virtuele Azure-machine](#backup-and-restore) |SQL Server 2005 of hoger |SQL Server 2005 of hoger |[Azure VM-opslaglimiet](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) | Dit is een zeer eenvoudige en goed geteste techniek voor het verplaatsen van databases over machines. |
| [Een back-up naar URL uitvoeren en vanuit de URL herstellen naar de virtuele Azure-machine](#backup-to-url-and-restore) |SQL Server 2012 SP1 CU2 of hoger |SQL Server 2012 SP1 CU2 of hoger |< 12,8 TB voor SQL Server 2016, anders < 1 TB | Deze methode is gewoon een andere manier om het back-upbestand naar de VM te verplaatsen met Azure-opslag. |
| [De gegevens en logboekbestanden loskoppelen en vervolgens kopiëren naar Azure blob-opslag en vervolgens koppelen aan SQL Server in Azure-virtuele machine vanaf URL](#detach-and-attach-from-url) |SQL Server 2005 of hoger |SQL Server 2014 of hoger |[Azure VM-opslaglimiet](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |Gebruik deze methode wanneer u van plan bent deze bestanden op te [slaan met behulp van de Azure Blob-opslagservice](https://msdn.microsoft.com/library/dn385720.aspx) en ze toe te voegen aan SQL Server die wordt uitgevoerd in een Azure-vm, met name met zeer grote databases |
| [On-premises machine converteren naar Hyper-V VHD's, uploaden naar Azure Blob-opslag en implementeer vervolgens een nieuwe virtuele machine met geüploade VHD](#convert-to-vm-and-upload-to-url-and-deploy-as-new-vm) |SQL Server 2005 of hoger |SQL Server 2005 of hoger |[Azure VM-opslaglimiet](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |Gebruik bij het brengen van [uw eigen SQL Server-licentie](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md), bij het migreren van een database die u uitvoert op een oudere versie van SQL Server, of wanneer u systeem- en gebruikersdatabases samenmigreert als onderdeel van de migratie van database die afhankelijk is van andere gebruikersdatabases en/of systeemdatabases. |
| [Harde schijf verzenden met Windows Import/Export Service](#ship-hard-drive) |SQL Server 2005 of hoger |SQL Server 2005 of hoger |[Azure VM-opslaglimiet](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |Gebruik de [Windows Import/Export Service](../../../storage/common/storage-import-export-service.md) wanneer handmatige kopieermethode te traag is, zoals bij zeer grote databases |
| [De wizard Azure Replica toevoegen gebruiken](../sqlclassic/virtual-machines-windows-classic-sql-onprem-availability.md) |SQL Server 2012 of hoger |SQL Server 2012 of hoger |[Azure VM-opslaglimiet](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |Minimaliseert downtime, gebruik wanneer u een always on-premises implementatie hebt |
| [SQL Server transactionele replicatie gebruiken](https://msdn.microsoft.com/library/ms151176.aspx) |SQL Server 2005 of hoger |SQL Server 2005 of hoger |[Azure VM-opslaglimiet](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |Gebruik wanneer u downtime moet minimaliseren en geen always on-premises implementatie hebt |

## <a name="backup-and-restore"></a>Back-ups en herstellen
Maak een back-up van uw database met compressie, kopieer de back-up naar de vm en herstel de database. Als uw back-upbestand groter is dan 1 TB, moet u het streep maken omdat de maximale grootte van een VM-schijf 1 TB is. Gebruik de volgende algemene stappen om een gebruikersdatabase te migreren met behulp van deze handmatige methode:

1. Voer een volledige databaseback-up uit naar een on-premises locatie.
2. Maak of upload een virtuele machine met de gewenste versie van SQL Server.
3. Configuratie van connectiviteit op basis van uw vereisten. Zie [Verbinding maken met een virtuele sql server-machine op Azure (Resource Beheer)](virtual-machines-windows-sql-connect.md).
4. Kopieer uw back-upbestand(en) naar uw vm met extern bureaublad, Windows Verkenner of de opdracht kopiëren vanuit een opdrachtprompt.

## <a name="backup-to-url-and-restore"></a>Back-up maken van URL en herstellen
In plaats van een back-up te maken van een lokaal bestand, u de [back-up gebruiken om te URL'en](https://msdn.microsoft.com/library/dn435916.aspx) vervolgens herstellen van URL naar de VM. Met SQL Server 2016 worden gestreepte back-upsets ondersteund, aanbevolen voor prestaties en moeten ze de groottelimieten per blob overschrijden. Voor zeer grote databases wordt het gebruik van de [Windows Import/Export Service](../../../storage/common/storage-import-export-service.md) aanbevolen.

## <a name="detach-and-attach-from-url"></a>Loskoppelen en loskoppelen van URL
Maak uw database en logboekbestanden los en breng deze over naar [Azure Blob-opslag.](https://msdn.microsoft.com/library/dn385720.aspx) Voeg vervolgens de database toe vanaf de URL op uw Azure VM. Gebruik dit als u wilt dat de fysieke databasebestanden zich in blobopslag bevinden. Dit kan handig zijn voor zeer grote databases. Gebruik de volgende algemene stappen om een gebruikersdatabase te migreren met behulp van deze handmatige methode:

1. Maak de databasebestanden los van de on-premises database-instantie.
2. Kopieer de losgekoppelde databasebestanden naar Azure blob-opslag met behulp van het [AZCopy-opdrachtregelhulpprogramma](../../../storage/common/storage-use-azcopy.md).
3. Voeg de databasebestanden van de Azure-URL toe aan de SQL Server-instantie in de Azure VM.

## <a name="convert-to-vm-and-upload-to-url-and-deploy-as-new-vm"></a>Converteer de SQL Server naar een virtuele machine en upload deze naar de URL. Implementeer de SQL Server vervolgens als nieuwe VM
Gebruik deze methode om alle systeem- en gebruikersdatabases in een on-premises SQL Server-instantie te migreren naar azure virtuele machine. Gebruik de volgende algemene stappen om een volledige SQL Server-instantie te migreren met deze handmatige methode:

1. Fysieke of virtuele machines converteren naar Hyper-V VHD's.
2. Upload VHD-bestanden naar Azure Storage met de [cmdlet Add-AzureVHD.](https://msdn.microsoft.com/library/windowsazure/dn495173.aspx)
3. Implementeer een nieuwe virtuele machine met behulp van de geüploade VHD.

> [!NOTE]
> Als u een hele toepassing wilt migreren, u [Azure Site Recovery gebruiken.](../../../site-recovery/site-recovery-overview.md)

## <a name="ship-hard-drive"></a>Harde schijf van het schip
Gebruik de [methode Windows Import/Export Service](../../../storage/common/storage-import-export-service.md) om grote hoeveelheden bestandsgegevens over te zetten naar Azure Blob-opslag in situaties waarin uploaden via het netwerk onbetaalbaar of niet haalbaar is. Met deze service verzendt u een of meer harde schijven met die gegevens naar een Azure-datacenter, waar uw gegevens worden geüpload naar uw opslagaccount.

## <a name="next-steps"></a>Volgende stappen
Zie [SQL Server op Azure Virtual Machines overzicht](virtual-machines-windows-sql-server-iaas-overview.md)voor meer informatie over het uitvoeren van SQL Server op Azure Virtual Machines.

> [!TIP]
> Als u vragen hebt over virtuele machines met SQL Server, raadpleegt u [Veelgestelde vragen](virtual-machines-windows-sql-server-iaas-faq.md).

Zie [Tips & Trucs voor het 'klonen' van Azure SQL-virtuele machines van vastgelegde afbeeldingen](https://blogs.msdn.microsoft.com/psssql/2016/07/06/tips-tricks-on-cloning-azure-sql-virtual-machines-from-captured-images/) op de CSS SQL Server Engineers-blog voor instructies over het maken van een Azure SQL Server Virtual Machine op basis van een vastgelegde afbeelding.

