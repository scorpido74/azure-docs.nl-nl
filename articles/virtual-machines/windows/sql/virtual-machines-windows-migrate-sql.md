---
title: Een SQL Server-Data Base migreren naar SQL Server op een VM | Microsoft Docs
description: Meer informatie over het migreren van een on-premises gebruikers database naar SQL Server op een virtuele machine van Azure.
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
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646860"
---
# <a name="migrate-a-sql-server-database-to-sql-server-in-an-azure-vm"></a>Een SQL Server-database migreren naar SQL Server in een Azure VM

Er zijn een aantal methoden voor het migreren van een on-premises SQL Server-gebruikers database naar SQL Server in een Azure-VM. In dit artikel worden verschillende methoden beschreven en wordt de beste methode aanbevolen voor verschillende scenario's.


[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

  > [!NOTE]
  > SQL Server 2008 en SQL Server 2008 R2 nadert het [einde van hun ondersteunings cyclus](https://www.microsoft.com/sql-server/sql-server-2008) voor on-premises exemplaren. Als u ondersteuning wilt uitbreiden, kunt u uw SQL Server-exemplaar naar een Azure-VM migreren of uitgebreide beveiligings updates kopen om deze on-premises te blijven gebruiken. Zie [ondersteuning voor SQL Server 2008 en 2008 R2 uitbreiden met Azure](virtual-machines-windows-sql-server-2008-eos-extend-support.md) voor meer informatie.

## <a name="what-are-the-primary-migration-methods"></a>Wat zijn de primaire migratie methoden?
De primaire migratie methoden zijn:

* On-premises back-ups uitvoeren met compressie en het back-upbestand hand matig kopiëren naar de virtuele machine van Azure
* Maak een back-up naar de URL en herstel de virtuele Azure-machine vanuit de URL
* Ontkoppel en kopieer vervolgens de gegevens-en logboek bestanden naar Azure Blob-opslag en koppel deze vervolgens aan SQL Server in azure VM vanaf een URL
* On-premises fysieke machine converteren naar Hyper-V VHD, uploaden naar Azure Blob Storage en vervolgens implementeren als nieuwe virtuele machine met behulp van geüploade VHD
* Harde schijf verzenden met Windows import/export-service
* Als u on-premises een implementatie van AlwaysOn-beschikbaarheids groepen hebt, gebruikt u de [wizard Azure replica toevoegen](../sqlclassic/virtual-machines-windows-classic-sql-onprem-availability.md) om een replica in azure te maken en vervolgens failover uit te wijzen naar de Azure data base-instantie
* SQL Server [transactionele replicatie](https://msdn.microsoft.com/library/ms151176.aspx) gebruiken om het Azure SQL Server-exemplaar te configureren als een abonnee en vervolgens de replicatie uit te scha kelen, gebruikers naar het Azure data base-exemplaar te wijzen

> [!TIP]
> U kunt ook dezelfde technieken gebruiken om data bases te verplaatsen tussen SQL Server Vm's in Azure. Er is bijvoorbeeld geen ondersteunde manier om een upgrade uit te kunnen zetten van een SQL Server galerie-image-VM van de ene versie naar een andere. In dit geval moet u een nieuwe SQL Server VM maken met de nieuwe versie/editie en vervolgens een van de migratie technieken in dit artikel gebruiken om uw data bases te verplaatsen. 

## <a name="choosing-your-migration-method"></a>De migratie methode kiezen
Voor optimale prestaties van de gegevens overdracht migreert u de database bestanden naar de Azure-VM met behulp van een gecomprimeerd back-upbestand.

Gebruik de optie AlwaysOn of de optie transactionele replicatie om de downtime te minimaliseren tijdens het database migratie proces.

Als het niet mogelijk is om de bovenstaande methoden te gebruiken, migreert u de data base hand matig. Met deze methode begint u doorgaans met een back-up van de data base, gevolgd door een kopie van de back-up van de data base in Azure en vervolgens een Data Base herstellen. U kunt de database bestanden ook kopiëren naar Azure en deze vervolgens koppelen. Er zijn verschillende manieren waarop u dit hand matige proces van het migreren van een Data Base naar een virtuele machine van Azure kunt uitvoeren.

> [!NOTE]
> Wanneer u een upgrade uitvoert naar SQL Server 2014 of SQL Server 2016 van oudere versies van SQL Server, moet u overwegen of er wijzigingen nodig zijn. U wordt aangeraden alle afhankelijkheden op te lossen voor functies die niet worden ondersteund door de nieuwe versie van SQL Server als onderdeel van uw migratie project. Zie [upgrade naar SQL Server](https://msdn.microsoft.com/library/bb677622.aspx)voor meer informatie over de ondersteunde edities en scenario's.

De volgende tabel bevat een overzicht van de primaire migratie methoden en komt aan de orde wanneer het gebruik van elke methode het meest geschikt is.

| Methode | Versie van de bron database | Versie van de doel database | Beperking van de back-upgrootte van de bron database | Opmerkingen |
| --- | --- | --- | --- | --- |
| [On-premises back-ups uitvoeren met compressie en het back-upbestand hand matig kopiëren naar de virtuele machine van Azure](#backup-and-restore) |SQL Server 2005 of hoger |SQL Server 2005 of hoger |[Opslag limiet voor Azure VM](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) | Dit is een zeer eenvoudige en goed geteste techniek voor het verplaatsen van data bases op verschillende computers. |
| [Maak een back-up naar de URL en herstel de virtuele Azure-machine vanuit de URL](#backup-to-url-and-restore) |SQL Server 2012 SP1 CU2 of hoger |SQL Server 2012 SP1 CU2 of hoger |< 12,8 TB voor SQL Server 2016, anders < 1 TB | Deze methode is een andere manier om het back-upbestand naar de virtuele machine te verplaatsen met behulp van Azure Storage. |
| [Ontkoppel en kopieer vervolgens de gegevens-en logboek bestanden naar Azure Blob-opslag en koppel deze vervolgens aan SQL Server in azure virtual machine van URL](#detach-and-attach-from-url) |SQL Server 2005 of hoger |SQL Server 2014 of hoger |[Opslag limiet voor Azure VM](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |Gebruik deze methode wanneer u van plan bent om [deze bestanden op te slaan met behulp van de Azure Blob Storage-service](https://msdn.microsoft.com/library/dn385720.aspx) en deze te koppelen aan SQL Server die worden uitgevoerd in een Azure-VM, met name met zeer grote data bases |
| [De on-premises machine converteren naar Hyper-V-Vhd's, uploaden naar Azure Blob Storage en vervolgens een nieuwe virtuele machine implementeren met geüploade VHD](#convert-to-vm-and-upload-to-url-and-deploy-as-new-vm) |SQL Server 2005 of hoger |SQL Server 2005 of hoger |[Opslag limiet voor Azure VM](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |Gebruiken wanneer u [uw eigen SQL Server-licentie](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)meebrengt bij het migreren van een Data Base die u uitvoert op een oudere versie van SQL Server, of bij het migreren van systeem-en gebruikers databases samen als onderdeel van de migratie van data base afhankelijk van andere gebruikers databases en/of systeem databases. |
| [Harde schijf verzenden met Windows import/export-service](#ship-hard-drive) |SQL Server 2005 of hoger |SQL Server 2005 of hoger |[Opslag limiet voor Azure VM](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |Gebruik de [Windows import/export-service](../../../storage/common/storage-import-export-service.md) als de methode voor hand matig kopiëren te langzaam is, zoals bij zeer grote data bases |
| [De wizard Azure replica toevoegen gebruiken](../sqlclassic/virtual-machines-windows-classic-sql-onprem-availability.md) |SQL Server 2012 of hoger |SQL Server 2012 of hoger |[Opslag limiet voor Azure VM](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |Minimaliseert downtime, gebruik wanneer u een on-premises implementatie hebt |
| [SQL Server transactionele replicatie gebruiken](https://msdn.microsoft.com/library/ms151176.aspx) |SQL Server 2005 of hoger |SQL Server 2005 of hoger |[Opslag limiet voor Azure VM](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |Gebruiken wanneer u de downtime wilt minimaliseren en niet over een on-premises implementatie beschikt |

## <a name="backup-and-restore"></a>Back-up maken en terugzetten
Maak een back-up van uw data base met compressie, kopieer de back-up naar de virtuele machine en herstel de data base. Als uw back-upbestand groter is dan 1 TB, moet u dit verwijderen omdat de maximale grootte van een VM-schijf 1 TB is. Gebruik de volgende algemene stappen voor het migreren van een gebruikers database met deze hand matige methode:

1. Voer een volledige back-up van de data base uit naar een on-premises locatie.
2. Maak of upload een virtuele machine met de gewenste versie van SQL Server.
3. Connectiviteit instellen op basis van uw vereisten. Zie [verbinding maken met een SQL Server virtuele machine op Azure (Resource Manager)](virtual-machines-windows-sql-connect.md).
4. Kopieer uw back-upbestand (en) naar uw VM met behulp van extern bureau blad, Windows Verkenner of de Kopieer opdracht vanaf een opdracht prompt.

## <a name="backup-to-url-and-restore"></a>Back-up naar URL en herstel
In plaats van een back-up te maken naar een lokaal bestand, kunt u de [back-up naar URL](https://msdn.microsoft.com/library/dn435916.aspx) gebruiken en vervolgens van URL naar de virtuele machine herstellen. Met SQL Server 2016 worden gestripde back-upsets ondersteund, worden ze aanbevolen voor de prestaties en is de limiet voor de maximale grootte per BLOB vereist. Voor zeer grote data bases wordt het gebruik van de [Windows import/export-service](../../../storage/common/storage-import-export-service.md) aanbevolen.

## <a name="detach-and-attach-from-url"></a>Loskoppelen en koppelen van URL
Ontkoppel uw data base en logboek bestanden en breng ze over naar [Azure Blob-opslag](https://msdn.microsoft.com/library/dn385720.aspx). Koppel de data base vervolgens van de URL op uw virtuele Azure-machine. Gebruik deze als u wilt dat de fysieke database bestanden zich in Blob Storage bevinden. Dit kan handig zijn voor zeer grote data bases. Gebruik de volgende algemene stappen voor het migreren van een gebruikers database met deze hand matige methode:

1. Ontkoppel de database bestanden van het on-premises data base-exemplaar.
2. Kopieer de losgekoppelde database bestanden naar Azure Blob-opslag met behulp van het [opdracht regel programma AZCopy](../../../storage/common/storage-use-azcopy.md).
3. Koppel de database bestanden van de Azure-URL aan het SQL Server-exemplaar in de Azure VM.

## <a name="convert-to-vm-and-upload-to-url-and-deploy-as-new-vm"></a>Converteer de SQL Server naar een virtuele machine en upload deze naar de URL. Implementeer de SQL Server vervolgens als nieuwe VM
Gebruik deze methode om alle systeem-en gebruikers databases in een on-premises SQL Server-exemplaar te migreren naar een virtuele machine van Azure. Gebruik de volgende algemene stappen voor het migreren van een hele SQL Server-exemplaar met behulp van deze hand matige methode:

1. Converteer fysieke of virtuele machines naar Hyper-V-Vhd's.
2. Upload VHD-bestanden naar Azure Storage met behulp van de [cmdlet Add-AzureVHD](https://msdn.microsoft.com/library/windowsazure/dn495173.aspx).
3. Implementeer een nieuwe virtuele machine met behulp van de geüploade VHD.

> [!NOTE]
> Als u een volledige toepassing wilt migreren, kunt u overwegen [Azure site Recovery](../../../site-recovery/site-recovery-overview.md)] te gebruiken.

## <a name="ship-hard-drive"></a>Harde schijf verzenden
Gebruik de [service methode van Windows import/export](../../../storage/common/storage-import-export-service.md) om grote hoeveel heden bestands gegevens over te brengen naar Azure Blob-opslag in situaties waarin het uploaden via het netwerk prohibitively kostbaar of niet haalbaar is. Met deze service verzendt u een of meer harde schijven met deze gegevens naar een Azure-Data Center, waar uw gegevens worden geüpload naar uw opslag account.

## <a name="next-steps"></a>Volgende stappen
Zie [SQL Server in azure virtual machines Overview](virtual-machines-windows-sql-server-iaas-overview.md)voor meer informatie over het uitvoeren van SQL Server op Azure virtual machines.

> [!TIP]
> Als u vragen hebt over virtuele machines met SQL Server, raadpleegt u [Veelgestelde vragen](virtual-machines-windows-sql-server-iaas-faq.md).

Voor instructies over het maken van een virtuele machine van Azure SQL Server van een vastgelegde installatie kopie raadpleegt u [Tips & slagen op het klonen van Azure SQL-virtuele machines van vastgelegde installatie kopieën](https://blogs.msdn.microsoft.com/psssql/2016/07/06/tips-tricks-on-cloning-azure-sql-virtual-machines-from-captured-images/) op de CSS SQL Server engineers-blog.

