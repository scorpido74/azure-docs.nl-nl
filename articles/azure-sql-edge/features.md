---
title: Ondersteunde functies van de Azure SQL-rand (preview-versie)
description: Meer informatie over de functies die worden ondersteund door Azure SQL Edge (preview)
keywords: Inleiding tot SQL Edge, wat is SQL Edge, overzicht van SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: e3bac1cbb0e57f9be4a489207f2fe8b30b5d457e
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/31/2020
ms.locfileid: "84233214"
---
# <a name="supported-features-of-azure-sql-edge-preview"></a>Ondersteunde functies van de Azure SQL-rand (preview-versie) 

Dit artikel bevat informatie over functies die door Azure SQL Edge worden ondersteund. Azure SQL Edge, is gebaseerd op de nieuwste versie van de Microsoft SQL Server data base-engine in Linux en ondersteunt een subset van de functies die worden ondersteund in SQL Server 2019 voor Linux, naast sommige functies die momenteel niet worden ondersteund of beschikbaar zijn in SQL Server 2019 op Linux of in Windows. Zie voor een volledige lijst van de functies die worden ondersteund in SQL Server on Linux [edities en ondersteunde functies van SQL Server 2019 op Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-editions-and-components-2019). Voor edities en ondersteunde functies van SQL Server in Windows verwijzen wij naar [edities en ondersteunde functies van SQL Server 2019 (15. x)](https://docs.microsoft.com/sql/sql-server/editions-and-components-of-sql-server-version-15).

> [!NOTE]
> Azure SQL Edge is momenteel in (preview) en kan niet worden gebruikt in productie omgevingen. Micro soft raadt u aan om Azure SQL Edge in productie omgevingen uit te voeren na validatie van de implementatie en de use-case scenario's.

## <a name="azure-sql-edge-editions"></a>Azure SQL Edge-edities

Azure SQL Edge is beschikbaar in twee verschillende edities of software plannen. Deze edities hebben identieke functie sets en verschillen alleen met de gebruiks rechten en de hoeveelheid CPU/geheugen die ze op het hostsysteem kunnen benaderen.

   |**Plannen**  |**Beschrijving**  |
   |---------|---------|
   |Ontwikkel aars van Azure SQL Edge  |  De SKU alleen Development, elke Azure SQL Edge-ontwikkelaars container is beperkt tot Maxi maal 4 kernen en 32 GB geheugen  |
   |Azure SQL Edge    |  Productie-SKU, elke Azure SQL Edge-container is beperkt tot 8 kernen en 64 GB geheugen  |

## <a name="operating-system"></a>Besturingssysteem

Azure SQL Edge-containers zijn momenteel gebaseerd op Ubuntu 16,04 en worden alleen ondersteund om te worden uitgevoerd op docker-hosts met Ubuntu 16,04 (aanbevolen) of Ubuntu 18,04. Azure SQL Edge kan ook worden uitgevoerd op andere hosts van het besturings systeem, bijvoorbeeld andere distributies van Linux of op Windows (met behulp van docker CE of docker EE), maar deze configuraties zijn niet uitgebreid getest door micro soft.

Azure SQL Edge wordt momenteel alleen ondersteund voor implementatie via Azure IoT Edge. Raadpleeg [Azure IOT Edge ondersteunde systemen](https://docs.microsoft.com/azure/iot-edge/support)voor meer informatie over de ondersteunde systemen voor Azure IOT Edge.

De aanbevolen configuratie voor het uitvoeren van Azure SQL Edge in Windows is het configureren van een Ubuntu VM op de Windows-host en het uitvoeren van SQL-Edge in de virtuele Linux-machine.

## <a name="hardware-support"></a>Hardwaresupport

Voor Azure SQL Edge is een 64-bits processor vereist, die van Intel, AMD of ARM kan zijn, met mini maal één processor en één GB RAM-geheugen op de host. Terwijl het geheugen voor het opstarten van de Azure SQL-rand zich dicht bij 500 MB bevindt, is het extra geheugen nodig voor andere IoT Edge-modules die op het apparaat Edge worden uitgevoerd.

## <a name="azure-sql-edge-components"></a>Azure SQL Edge-onderdelen

Azure SQL Edge ondersteunt alleen de data base-engine en biedt geen ondersteuning voor andere onderdelen die beschikbaar zijn met SQL Server 2019 in Windows of met SQL Server 2019 in Linux. Met name Azure SQL Edge biedt geen ondersteuning voor SQL Server-onderdelen, zoals Analysis Services, Reporting Services, Integration Services, Master Data Services, Machine Learning Services (in-data base) en Machine Learning Server (zelfstandig).

## <a name="supported-features"></a>Ondersteunde functies

Naast het ondersteunen van een subset van SQL Server on Linux-functies biedt Azure SQL EDGE ondersteuning voor de volgende nieuwe functies. 

- SQL-streaming, dat is gebaseerd op dezelfde engine die Azure Stream Analytics, biedt mogelijkheden voor realtime gegevens stromen in Azure SQL Edge. 
- Nieuwe T-SQL-functie aanroepen Date_Bucket voor gegevens analyse van time series.
- Machine Learning mogelijkheden via de ONNX-runtime, opgenomen in de SQL-engine.

## <a name="sql-server-on-linux-features-not-supported-in-azure-sql-edge"></a>SQL Server on Linux-functies die niet worden ondersteund in Azure SQL Edge

De onderstaande lijst bevat de SQL Server 2019 in Linux-functies die momenteel niet worden ondersteund in de Azure SQL-rand.

| Gebied | Niet-ondersteunde functie of service |
|-----|-----|
| **Database ontwerp** | In-Memory OLTP en gerelateerde DDL-opdrachten en Transact-SQL-functies, catalogus weergaven en dynamische beheer weergaven |
| &nbsp; | Het gegevens type HierarchyID en gerelateerde DDL-opdrachten en Transact-SQL-functies, catalogus weergaven en dynamische beheer weergaven |
| &nbsp; | Ruimtelijk gegevens type en gerelateerde DDL-opdrachten en Transact-SQL-functies, catalogus weergaven en dynamische beheer weergaven |
| &nbsp; | Data Base-en gerelateerde DDL-opdrachten en Transact-SQL-functies, catalogus weergaven en dynamische beheer weergaven uitrekken |
| &nbsp; | Volledige-tekst indexen en zoek-en gerelateerde DDL-opdrachten en Transact-SQL-functies, catalogus weergaven en dynamische beheer weergaven|
| &nbsp; | Bestands tabel-, FILESTREAM-en gerelateerde DDL-opdrachten en Transact-SQL-functies, catalogus weergaven en dynamische beheer weergaven|
| **Database Engine** | Replicat. Azure SQL Edge kan echter worden geconfigureerd als een push-abonnee van een replicatie topologie. |
| &nbsp; | Poly base. Azure SQL Edge kan echter worden geconfigureerd als een doel voor externe tabellen in poly base |
| &nbsp; | Taal uitbreid baarheid via Java en Spark |
| &nbsp; | Integratie in Active Directory |
| &nbsp; | Database momentopnamen |
| &nbsp; | Ondersteuning voor permanent geheugen |
| &nbsp; | Micro soft Distributed Transaction Coordinator |
| &nbsp; | Beheer van Resource Governor en i/o-resources |
| &nbsp; | Extensie van buffer groep |
| &nbsp; | Gedistribueerde query met verbindingen van derden |
| &nbsp; | Gekoppelde servers |
| &nbsp; | Door systeem uitgebreide opgeslagen procedures (XP_CMDSHELL, enzovoort) |
| &nbsp; | CLR-Assembly's en gerelateerde DDL-opdrachten en Transact-SQL-functies, catalogus weergaven en dynamische beheer weergaven |
| &nbsp; | CLR: afhankelijke T-SQL-functies als ASSEMBLYPROPERTY, FORMAT TEREn, PARSERen, TRY_PARSE |
| &nbsp; | CLR: afhankelijke datum-en tijd catalogus weergaven, functies en query componenten |
| &nbsp; | Extensie van buffer groep |
| &nbsp; | Database Mail |
| **SQL Server Agent** |  Subsystemen: CmdExec, Power shell, Queue Reader, SSIS, SSAS, SSRS |
| &nbsp; | Waarschuwingen |
| &nbsp; | Beheerde back-up |
| **Hoge Beschik baarheid** | AlwaysOn-beschikbaarheids groepen  |
| &nbsp; | Basis beschikbaarheids groepen |
| &nbsp; | Altijd op het failovercluster-exemplaar |
| &nbsp; | Databasespiegeling |
| &nbsp; | Dynamisch toevoegen van geheugen en CPU |
| **Beveiliging** | Uitbreidbaar sleutel beheer |
| &nbsp; | Active Directory-integratie|
| &nbsp; | Ondersteuning voor beveiligde enclaves|
| **Services** | SQL Server Browser |
| &nbsp; | Machine Learning tot en met R en python |
| &nbsp; | StreamInsight |
| &nbsp; | Analysis Services |
| &nbsp; | Reporting Services |
| &nbsp; | Services voor gegevens kwaliteit |
| &nbsp; | Master Data Services |
| &nbsp; | Distributed Replay |
| **Beheer baarheid** | SQL Server Utility controle punt |

## <a name="next-steps"></a>Volgende stappen

- [Azure SQL Edge implementeren](deploy-portal.md)
- [Azure SQL Edge configureren](configure.md)
- [Verbinding maken met een exemplaar van Azure SQL Edge met SQL Server-client hulpprogramma's](connect.md)
- [Back-up en herstel van data bases in Azure SQL Edge](backup-restore.md)
