---
title: Azure-opslag gebruiken voor SQL Server-back-up en herstel | Microsoft Documenten
description: Meer informatie over het maken van een back-up van SQL Server naar Azure Storage. Hiermee worden de voordelen van het maken van back-ups van SQL-databases naar Azure Storage uitgelegd.
services: virtual-machines-windows
documentationcenter: ''
author: MikeRayMSFT
manager: craigg
tags: azure-service-management
ms.assetid: 0db7667d-ef63-4e2b-bd4d-574802090f8b
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: mikeray
ms.openlocfilehash: cb19dc7262721e672bd3f54b32db9188dad7fee0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70101895"
---
# <a name="use-azure-storage-for-sql-server-backup-and-restore"></a>Azure Storage gebruiken voor SQL Server Backup and Restore
## <a name="overview"></a>Overzicht
Vanaf SQL Server 2012 SP1 CU2 u nu SQL Server-back-ups rechtstreeks naar de Azure Blob-opslagservice schrijven. U deze functionaliteit gebruiken om een back-up te maken van en te herstellen vanuit de Azure Blob-service met een on-premises SQL Server-database of een SQL Server-database in een Virtuele Azure-machine. Back-up naar de cloud biedt voordelen van beschikbaarheid, onbeperkte geo-gerepliceerde off-site opslag en het gemak van migratie van gegevens van en naar de cloud. U back-up- of herstelverklaringen uitvoeren met Transact-SQL of SMO.

SQL Server 2016 introduceert nieuwe mogelijkheden; u [back-ups met bestandsmomentopnamen](https://msdn.microsoft.com/library/mt169363.aspx) gebruiken om bijna onmiddellijke back-ups en ongelooflijk snelle herstelbewegingen uit te voeren.

In dit onderwerp wordt uitgelegd waarom u Azure-opslag gebruiken voor SQL-back-ups en beschrijft u vervolgens de betrokken onderdelen. U de resources aan het einde van het artikel gebruiken om toegang te krijgen tot walkthroughs en aanvullende informatie om deze service te gebruiken met uw SQL Server-back-ups.

## <a name="benefits-of-using-the-azure-blob-service-for-sql-server-backups"></a>Voordelen van het gebruik van de Azure Blob-service voor SQL Server Backups
Er zijn verschillende uitdagingen waarmee u wordt geconfronteerd bij het maken van een back-up van SQL Server. Deze uitdagingen omvatten opslagbeheer, risico op opslaguitval, toegang tot off-site opslag en hardwareconfiguratie. Veel van deze uitdagingen worden aangepakt door de Azure Blob Store-service voor SQL Server-back-ups te gebruiken. Houd rekening met de volgende voordelen:

* **Gebruiksgemak**: Het opslaan van uw back-ups in Azure-blobs kan een handige, flexibele en gemakkelijk toegankelijke off-site optie zijn. Het maken van off-site opslag voor uw SQL Server-back-ups kan net zo eenvoudig zijn als het wijzigen van uw bestaande scripts/taken om de **syntaxis VAN BACK-up naar URL** te gebruiken. Off-site opslag moet doorgaans ver genoeg van de locatie van de productiedatabase zijn om te voorkomen dat er één ramp plaatsvindt die zowel de locatie van de off-site als de productiedatabase kan beïnvloeden. Als u ervoor kiest om [uw Azure-blobs te georepliceren,](../../../storage/common/storage-redundancy.md)hebt u een extra beschermingslaag in het geval van een ramp die de hele regio kan beïnvloeden.
* **Back-uparchief**: De Azure Blob Storage-service biedt een beter alternatief voor de vaak gebruikte tapeoptie om back-ups te archiveren. Tapeopslag vereist mogelijk fysiek transport naar een off-site faciliteit en maatregelen om de media te beschermen. Het opslaan van uw back-ups in Azure Blob Storage biedt een directe, zeer beschikbare en een duurzame archiveringsoptie.
* **Beheerde hardware**: er is geen overhead van hardwarebeheer met Azure-services. Azure-services beheren de hardware en bieden georeplicatie voor redundantie en bescherming tegen hardwarefouten.
* **Onbeperkte opslag:** door een directe back-up naar Azure-blobs in te schakelen, hebt u toegang tot vrijwel onbeperkte opslag. Als u een back-up maakt van een Azure-schijf voor virtuele machines, is er ook limieten op basis van de grootte van de machine. Er is een limiet aan het aantal schijven dat u koppelen aan een virtuele Azure-machine voor back-ups. Deze limiet is 16 schijven voor een extra grote instantie en minder voor kleinere exemplaren.
* **Beschikbaarheid van back-ups:** Back-ups die zijn opgeslagen in Azure-blobs zijn overal en op elk moment beschikbaar en kunnen eenvoudig worden geopend voor herstel naar een on-premises SQL Server of een andere SQL Server die wordt uitgevoerd in een Azure Virtual Machine, zonder dat de VHD hoeft te worden gekoppeld/losgekoppeld of gedownload en toegevoegd.
* **Kosten**: Betaal alleen voor de service die wordt gebruikt. Kan kosteneffectief zijn als een off-site en back-up archief optie. Zie de [azure-prijscalculator](https://go.microsoft.com/fwlink/?LinkId=277060 "Prijscalculator")en het [artikel Azure Pricing](https://go.microsoft.com/fwlink/?LinkId=277059 "Prijsartikel") voor meer informatie.
* **Opslagmomentopnamen:** Wanneer databasebestanden worden opgeslagen in een Azure-blob en u SQL Server 2016 gebruikt, u [bestandsmomentopnamen](https://msdn.microsoft.com/library/mt169363.aspx) gebruiken om bijna onmiddellijke back-ups en ongelooflijk snelle herstelfouten uit te voeren.

Zie [SQL Server Backup and Restore met Azure Blob Storage Service](https://go.microsoft.com/fwlink/?LinkId=271617)voor meer informatie.

In de volgende twee secties wordt de Azure Blob-opslagservice geïntroduceerd, inclusief de vereiste SQL Server-componenten. Het is belangrijk om inzicht te krijgen in de componenten en hun interactie om met succes back-up en herstel van de Azure Blob-opslagservice te gebruiken.

## <a name="azure-blob-storage-service-components"></a>Azure Blob Storage Service-componenten
De volgende Azure-componenten worden gebruikt bij het maken van een back-up van de Azure Blob-opslagservice.

| Onderdeel | Beschrijving |
| --- | --- |
| **Opslagaccount** |Het opslagaccount is het startpunt voor alle opslagservices. Als u toegang wilt krijgen tot een Azure Blob Storage-service, maakt u eerst een Azure Storage-account. Zie De Azure Blob Storage [Service gebruiken](https://azure.microsoft.com/develop/net/how-to-guides/blob-storage/) voor meer informatie over Azure Blob-opslagservice |
| **Container** |Een container biedt een groepering van een reeks blobs en kan een onbeperkt aantal Blobs opslaan. Als u een SQL Server-back-up wilt schrijven naar een Azure Blob-service, moet u ten minste de hoofdcontainer hebben gemaakt. |
| **Blob** |Een bestand van elk type en elke grootte. Blobs zijn adresseerbaar met behulp van de volgende URL-indeling: **https://[storage account].blob.core.windows.net/[container]/[blob]**. Zie [Blokkeren en Paginablobs begrijpen](https://msdn.microsoft.com/library/azure/ee691964.aspx) voor meer informatie over paginablobs |

## <a name="sql-server-components"></a>SQL Server-componenten
De volgende SQL Server-componenten worden gebruikt bij het maken van een back-up van de Azure Blob-opslagservice.

| Onderdeel | Beschrijving |
| --- | --- |
| **Url** |Een URL geeft een Uri (Uniform Resource Identifier) op voor een uniek back-upbestand. De URL wordt gebruikt om de locatie en naam van het SQL Server-back-upbestand op te geven. De URL moet wijzen op een werkelijke blob, niet alleen een container. Als de blob niet bestaat, wordt deze gemaakt. Als een bestaande blob is opgegeven, mislukt BACK-up, tenzij de optie > MET OPMAAK is opgegeven. Het volgende is een voorbeeld van de URL die u zou opgeven in de opdracht BACK-UP: **http[s]://[storageaccount].blob.core.windows.net/[container]/[FILENAME.bak]**. HTTPS wordt aanbevolen, maar niet vereist. |
| **Referentie** |De informatie die nodig is om verbinding te maken en te verifiëren voor Azure Blob-opslagservice, wordt opgeslagen als referentie.  Als SQL Server back-ups naar een Azure Blob kan schrijven of er van kan herstellen, moet een SQL Server-referentie worden gemaakt. Zie [SQL Server Credential](https://msdn.microsoft.com/library/ms189522.aspx)voor meer informatie. |

> [!NOTE]
> Sql Server 2016 is bijgewerkt om blokblobs te ondersteunen. Zie [Zelfstudie: De Microsoft Azure Blob-opslagservice gebruiken met SQL Server 2016-databases](https://msdn.microsoft.com/library/dn466438.aspx) voor meer informatie.
> 
> 

## <a name="next-steps"></a>Volgende stappen
1. Maak een Azure-account als u er nog geen hebt. Als u Azure evalueert, u de [gratis proefversie overwegen.](https://azure.microsoft.com/free/)
2. Ga dan door een van de volgende tutorials die u door het maken van een opslagaccount en het uitvoeren van een herstel.
   
   * **SQL Server 2014:** [Zelfstudie: SQL Server 2014 Back-up en herstel naar Microsoft Azure Blob Storage Service](https://msdn.microsoft.com/library/jj720558\(v=sql.120\).aspx).
   * **SQL Server 2016**: [Zelfstudie: de Microsoft Azure Blob-opslagservice gebruiken met SQL Server 2016-databases](https://msdn.microsoft.com/library/dn466438.aspx)
3. Controleer aanvullende documentatie vanaf [SQL Server Backup and Restore met Microsoft Azure Blob Storage Service](https://msdn.microsoft.com/library/jj919148.aspx).

Als u problemen hebt, controleert u het onderwerp [SQL Server Backup naar URL Best Practices and Troubleshooting](https://msdn.microsoft.com/library/jj919149.aspx).

Zie [Back-up en herstel voor SQL Server in Azure Virtual Machines voor](virtual-machines-windows-sql-backup-recovery.md)andere SQL Server-back-up- en herstelopties.

