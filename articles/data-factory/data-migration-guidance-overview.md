---
title: Azure Data Factory gebruiken om gegevens van uw data Lake en Data Warehouse te migreren naar Azure | Microsoft Docs
description: Gebruik Azure Data Factory om gegevens van uw data Lake en Data Warehouse te migreren naar Azure.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 7/30/2019
ms.openlocfilehash: 937a076b3e0e3c5170779d3449776f0aa1cf5b49
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259004"
---
# <a name="use-azure-data-factory-to-migrate-data-from-your-data-lake-or-data-warehouse-to-azure"></a>Azure Data Factory gebruiken om gegevens van uw data Lake of Data Warehouse te migreren naar Azure 

Azure Data Factory kan het hulp programma zijn om gegevens migratie uit te voeren wanneer u uw data Lake of ENTER prise Data Warehouse (EDW) wilt migreren naar Azure. De migratie van data Lake en data warehouses zijn gerelateerd aan de volgende scenario's: 

- Migratie van de werk belasting van Big data van AWS S3, on-premises Hadoop-bestands systeem naar Azure. 
- EDW migratie van Oracle Exadata, Netezza, Teradata, AWS Redshift naar Azure. 

Azure Data Factory kunt PBs van gegevens verplaatsen voor data Lake Migration en tien tallen TB gegevens voor Data Warehouse migratie. 

## <a name="why-azure-data-factory-can-be-used-for-data-migration"></a>Waarom Azure Data Factory kunnen worden gebruikt voor gegevens migratie 

- Met Azure Data Factory kunt u eenvoudig de hoeveelheid paarden van uw gegevens op serverloze wijze schalen met hoge prestaties, flexibiliteit en schaal baarheid en betaalt u alleen voor wat u gebruikt.  
  - Azure Data Factory heeft geen beperking voor het gegevens volume en het aantal bestanden.
  - Azure Data Factory kan 100% gebruikmaken van uw netwerk-en opslag bandbreedte om de maximale door Voer van gegevens verplaatsing in uw omgeving te krijgen.   
  - Azure Data Factory volgt de strategie voor betalen naar gebruik, zodat u alleen voor de tijd hoeft te betalen wanneer u Azure Data Factory gebruikt om de gegevens migratie naar Azure uit te voeren.  
- Azure Data Factory beschikt over de mogelijkheid om eenmalig historische belasting en de geplande incrementele belasting uit te voeren. 
- Azure Data Factory gebruikt Azure IR voor het verplaatsen van gegevens tussen openbaar toegankelijke data Lake/Warehouse-eind punten, of het gebruik van zelf-hostende IR voor het verplaatsen van gegevens voor data Lake/Warehouse-eind punten in VNet of achter de firewall. 
- Azure Data Factory heeft beveiliging op bedrijfs niveau: gebruik MSI of service-identiteit voor beveiligde service-to-Service-integratie, of gebruik de Azure Key Vault voor referentie beheer. 
- Azure Data Factory biedt een code-gratis ontwerp ervaring en een uitgebreid ingebouwd controle dashboard.  

## <a name="online-vs-offline-data-migration"></a>Online versus offline gegevens migratie

Azure Data Factory is een standaard hulp programma voor het migreren van gegevens via het netwerk (Internet, er of VPN), waarbij gebruikers fysiek gegevens overdracht van uw organisatie naar Azure Data Center verzenden via een offline gegevens migratie.  

Er zijn drie belang rijke overwegingen bij het selecteren van online versus offline migratie methode:  

- Grootte van de gegevens die moeten worden gemigreerd. 
- Netwerk bandbreedte. 
- Migratie venster.   

Als u de gegevens migratie binnen twee weken (migratie venster) wilt volt ooien, ziet u in de onderstaande afbeelding een knip lijn om weer te geven wanneer het handig is om een hulp programma voor online migratie (Azure Data Factory) met verschillende gegevens grootte en netwerk bandbreedte te gebruiken.   

![Online versus offline](media/data-migration-guidance-overview/online-offline.png)

> [!NOTE]
> Het voor deel van het online migratie proces is dat u zowel historische gegevens belasting als incrementele feeds met één hulp programma kunt bereiken.  Op die manier kunnen de gegevens worden gesynchroniseerd tussen bestaande en nieuwe opslag tijdens het hele migratie venster, zodat u de ETL-logica in de nieuwe Store opnieuw kunt samen stellen met vernieuwde gegevens. 


## <a name="next-steps"></a>Volgende stappen

- [Gegevens migreren van AWS S3 naar Azure](data-migration-guidance-s3-azure-storage.md)
- [Gegevens migreren van een on-premises Hadoop-cluster naar Azure](data-migration-guidance-hdfs-azure-storage.md)
- [Gegevens migreren van een on-premises Netezza-server naar Azure](data-migration-guidance-netezza-azure-sqldw.md)
