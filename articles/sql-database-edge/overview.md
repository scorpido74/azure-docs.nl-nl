---
title: Wat is Azure SQL Database Edge? | Microsoft Docs
description: Meer informatie over Azure SQL Database Edge
keywords: inleiding tot sql-database edge, wat is sql database edge, sql database edge overzicht
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 03/24/2020
ms.openlocfilehash: d5c48b6036065f6182912c21c144cab80fc3cfbf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246701"
---
# <a name="what-is-azure-sql-database-edge-preview"></a>Wat is Azure SQL Database Edge Preview?

Azure SQL Database Edge Preview is een geoptimaliseerde relationele databaseengine die is afgestemd op IoT- en IoT Edge-implementaties. Het biedt mogelijkheden om een krachtige gegevensopslag- en verwerkingslaag te maken voor IoT-toepassingen en -oplossingen. Azure SQL Database Edge biedt mogelijkheden voor het streamen, verwerken en analyseren van relationele en niet-relationele gegevens, zoals JSON-, grafiek- en tijdreeksgegevens, waardoor het de juiste keuze is voor een verscheidenheid aan moderne IoT-toepassingen.

Azure SQL Database Edge is gebouwd op de nieuwste versies van de [Microsoft SQL Server Database Engine](/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database-edge/toc.json), die toonaangevende mogelijkheden voor prestaties, beveiliging en queryverwerking biedt. Aangezien Azure SQL Database Edge is gebouwd op dezelfde engine als SQL Server en Azure SQL Database, biedt het hetzelfde T-SQL-programmeeroppervlak dat de ontwikkeling van toepassingen of oplossingen eenvoudiger en sneller maakt en tegelijkertijd toepassing maakt draagbaarheid tussen IoT Edge-apparaten, datacenters en de cloud.

## <a name="deployment-models"></a>Implementatiemodellen

Azure SQL Database Edge is beschikbaar op de Azure Marketplace en kan worden geïmplementeerd als een module voor [Azure IoT Edge.](../iot-edge/about-iot-edge.md) Zie [Azure SQL Database Edge implementeren](deploy-portal.md)voor meer informatie.<br>

![SQL Database Edge-overzichtsdiagram](media/overview/overview.png)

## <a name="editions-of-sql-database-edge"></a>Edities van SQL Database Edge

SQL Database Edge is beschikbaar met drie verschillende edities of softwareplannen. Deze edities hebben identieke functiesets en verschillen alleen in termen van hun gebruiksrechten en de hoeveelheid cpu/geheugen die ze ondersteunen.

   |**Plannen**  |**Beschrijving**  |
   |---------|---------|
   |Azure SQL Database Edge Developer  |  Ontwikkeling alleen sku, elke SQL Database Edge-container is beperkt tot maximaal 4 cores en 32 GB geheugen  |
   |Azure SQL Database Edge    |  Elke SQL Database Edge-container is beperkt tot maximaal 8 cores en 64 GB geheugen. |

## <a name="pricing-and-availability"></a>Prijzen en beschikbaarheid

Azure SQL Database bevindt zich momenteel in preview. Zie [Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/)voor meer informatie over de prijzen en beschikbaarheid.

> [!IMPORTANT]
> Zie [SQL Database Edge Database-functies](https://azure.microsoft.com/services/sql-database-edge/)voor de functieverschillen tussen Azure SQL Database Edge en SQL Server en de verschillen tussen de verschillende Azure SQL Database Edge-opties.

## <a name="streaming-capabilities"></a>Streamingmogelijkheden  

Azure SQL Database Edge biedt ingebouwde streamingmogelijkheden voor realtime analyses en complexe gebeurtenisverwerking. De streamingmogelijkheid is gebouwd met dezelfde constructies als [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) en biedt vergelijkbare mogelijkheden als Azure Stream Analytics op [IoT Edge.](../stream-analytics/stream-analytics-edge.md)

De streaming-engine voor Azure SQL Database Edge is ontworpen voor low-latency, tolerantie, efficiënt gebruik van bandbreedte en compliance.

## <a name="machine-learning-and-artificial-intelligence-capabilities"></a>Machine Learning en kunstmatige intelligentie mogelijkheden

Azure SQL Database Edge biedt ingebouwde machine learning- en analysemogelijkheden door de runtime van open formaat ONNX (Open Neural Network Exchange) te integreren, waarmee deep learning- en neurale netwerkmodellen tussen verschillende frameworks kunnen worden uitgewisseld. Voor meer informatie over ONNX, zie [hier](https://onnx.ai/). ONNX-runtime biedt de flexibiliteit om modellen te ontwikkelen in een taal of tools naar keuze, die vervolgens kunnen worden geconverteerd naar de ONNX-indeling voor uitvoering binnen SQL Database Edge. Zie [Machine Learning en Kunstmatige intelligentie met ONNX in SQL Database Edge](onnx-overview.md)voor meer informatie.

## <a name="working-with-azure-sql-database-edge"></a>Werken met Azure SQL Database Edge

Azure SQL Database Edge maakt het ontwikkelen en onderhouden van toepassingen eenvoudiger en productiever. Gebruikers kunnen alle vertrouwde tools en vaardigheden gebruiken om geweldige apps en oplossingen te bouwen voor hun IoT Edge-behoeften. De gebruiker kan zich ontwikkelen in SQL Database Edge met behulp van tools zoals:

- [De Azure-portal](https://portal.azure.com/) - Een webgebaseerde toepassing voor het beheren van alle Azure-services.
- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) - Een gratis, downloadbare clienttoepassing voor het beheren van elke SQL-infrastructuur, van SQL Server tot SQL Database.
- [SQL Server Data Tools in Visual Studio](/sql/ssdt/download-sql-server-data-tools-ssdt/) - Een gratis, downloadbare clienttoepassing voor het ontwikkelen van SQL Server relationele databases, SQL-databases, Integration Services-pakketten, Analysis Services-gegevensmodellen en Rapportageservicesrapporten.
- [Azure Data Studio](/sql/azure-data-studio/what-is/) - Een gratis, downloadbare databasetool voor gegevensprofessionals die gebruik maken van de Microsoft-familie van on-premises en clouddataplatforms op Windows, MacOS en Linux.
- [Visual Studio Code](https://code.visualstudio.com/docs) - Een gratis, downloadbare, open-source code editor voor Windows, macOS en Linux. Het ondersteunt extensies, waaronder de [mssql-extensie](https://aka.ms/mssql-marketplace) voor het opvragen van Microsoft SQL Server, Azure SQL Database en Azure SQL Data Warehouse.


## <a name="next-steps"></a>Volgende stappen

- Zie [Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/)voor prijs- en beschikbaarheidsgerelateerde details.
- Vraag om Azure SQL Database Edge in te schakelen voor uw abonnement.
- Zie het volgende om aan de slag te gaan:
  - [SQL Database Edge implementeren via Azure-portal](deploy-portal.md)
  - [Machine Learning en kunstmatige intelligentie met SQL Database Edge](onnx-overview.md)
