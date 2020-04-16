---
title: Gegevens migreren van datalake en datawarehouse naar Azure
description: Gebruik Azure Data Factory om gegevens uit uw gegevensmeer en gegevensmagazijn naar Azure te migreren.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 7/30/2019
ms.openlocfilehash: 4408546c892299e5bbbc22b00a4b334c36eda616
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416434"
---
# <a name="use-azure-data-factory-to-migrate-data-from-your-data-lake-or-data-warehouse-to-azure"></a>Azure Data Factory gebruiken om gegevens uit uw gegevensmeer of gegevensmagazijn naar Azure te migreren

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Als u uw datalake of enterprise data warehouse (EDW) wilt migreren naar Microsoft Azure, u Azure Data Factory gebruiken. Azure Data Factory is zeer geschikt voor de volgende scenario's:

- Migratie van Big data-workloads van Amazon Simple Storage Service (Amazon S3) of een on-premises Hadoop Distributed File System (HDFS) naar Azure
- EDW-migratie van Oracle Exadata, Netezza, Teradata of Amazon Redshift naar Azure

Azure Data Factory kan petabytes (PB) aan gegevens verplaatsen voor migratie van gegevensen en tientallen terabytes (TB) aan gegevens voor migratie van gegevensmagazijnen.

## <a name="why-azure-data-factory-can-be-used-for-data-migration"></a>Waarom Azure Data Factory kan worden gebruikt voor gegevensmigratie

- Azure Data Factory kan eenvoudig de hoeveelheid verwerkingskracht opschalen om gegevens op een serverloze manier te verplaatsen met hoge prestaties, veerkracht en schaalbaarheid. En je betaalt alleen voor wat je gebruikt. Let ook op het volgende: 
  - Azure Data Factory heeft geen beperkingen op het gegevensvolume of het aantal bestanden.
  - Azure Data Factory kan uw netwerk- en opslagbandbreedte volledig gebruiken om het hoogste volume van de doorvoer van gegevensverplaatsingen in uw omgeving te bereiken.
  - Azure Data Factory gebruikt een pay-as-you-go-methode, zodat u alleen betaalt voor de tijd die u daadwerkelijk gebruikt om de gegevensmigratie naar Azure uit te voeren.  
- Azure Data Factory kan zowel een eenmalige historische belasting als geplande incrementele belastingen uitvoeren.
- Azure Data Factory gebruikt Azure Integration Runtime (IR) om gegevens te verplaatsen tussen openbaar toegankelijke datalake- en magazijneindpunten. Het kan ook zelf gehoste IR gebruiken voor het verplaatsen van gegevens voor data lake- en warehouse-eindpunten in Azure Virtual Network (VNet) of achter een firewall.
- Azure Data Factory heeft bedrijfsbeveiliging: u Windows Installer (MSI) of Service Identity gebruiken voor beveiligde service-to-service-integratie of Azure Key Vault gebruiken voor referentiebeheer.
- Azure Data Factory biedt een codevrije ontwerpervaring en een uitgebreid, ingebouwd monitoringdashboard.  

## <a name="online-vs-offline-data-migration"></a>Online versus offline datamigratie

Azure Data Factory is een standaard online gegevensmigratietool om gegevens over te dragen via een netwerk (internet, ER of VPN). Terwijl gebruikers met offline gegevensmigratie apparaten fysiek verzenden van hun organisatie naar een Azure Data Center.  

Er zijn drie belangrijke overwegingen wanneer u kiest tussen een online en offline migratiebenadering:  

- Grootte van de te migreren gegevens
- Netwerkbandbreedte
- Migratievenster

Stel dat u van plan bent Azure Data Factory te gebruiken om uw gegevensmigratie binnen twee weken (uw *migratievenster)* te voltooien. Let op de roze/blauwe snijlijn in de volgende tabel. De laagste roze cel voor een bepaalde kolom toont de koppeling gegevensgrootte/netwerkbandbreedte waarvan het migratievenster het dichtst bij maar minder dan twee weken ligt. (Elke koppeling tussen grootte en bandbreedte in een blauwe cel heeft een online migratievenster van meer dan twee weken.) 

![online versus](media/data-migration-guidance-overview/online-offline.png) offline Deze tabel helpt u te bepalen of u aan uw beoogde migratievenster voldoen via online migratie (Azure Data Factory) op basis van de grootte van uw gegevens en uw beschikbare netwerkbandbreedte. Als het online migratievenster meer dan twee weken bedraagt, wilt u offline migratie gebruiken.

> [!NOTE]
> Door online migratie te gebruiken, u zowel historische gegevensladen als incrementele feeds end-to-end bereiken via één tool.  Door deze aanpak kunnen uw gegevens gesynchroniseerd worden gehouden tussen het bestaande archief en het nieuwe archief gedurende het gehele migratievenster. Dit betekent dat u uw ETL-logica in het nieuwe archief herbouwen met vernieuwde gegevens.


## <a name="next-steps"></a>Volgende stappen

- [Gegevens migreren van AWS S3 naar Azure](data-migration-guidance-s3-azure-storage.md)
- [Gegevens migreren van on-premises hadoopcluster naar Azure](data-migration-guidance-hdfs-azure-storage.md)
- [Gegevens migreren van een on-premises Netezza-server naar Azure](data-migration-guidance-netezza-azure-sqldw.md)
