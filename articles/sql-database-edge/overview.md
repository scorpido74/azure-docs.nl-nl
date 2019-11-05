---
title: Wat is Azure SQL Database Edge? | Microsoft Docs
description: Meer informatie over Azure SQL Database Edge
keywords: Inleiding tot SQL data base Edge, wat is SQL data base Edge, overzicht van SQL data base Edge
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 9cbfc17e7412b4d30f082354996721ee7b5d6d5b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514002"
---
# <a name="what-is-azure-sql-database-edge-preview"></a>Wat is Azure SQL Database Edge-Preview?

Azure SQL Database Edge-Preview is een geoptimaliseerde relationele data base-engine die is toegespitst op IoT en IoT Edge implementaties. Het biedt mogelijkheden voor het maken van een hoogwaardige gegevens opslag en verwerkings laag voor IoT-toepassingen en-oplossingen. Azure SQL Database Edge biedt mogelijkheden voor het streamen, verwerken en analyseren van relationele en niet-relationele gegevens, zoals JSON, Graph en time-series, waardoor het de juiste keuze is voor een groot aantal moderne IoT-toepassingen.

Azure SQL Database Edge is gebaseerd op de nieuwste versies van de [Microsoft SQL server data base-engine](/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database-edge/toc.json), waarmee toonaangevende prestaties, beveiligings-en query verwerkings mogelijkheden worden geboden. Omdat Azure SQL Database Edge op dezelfde engine is gebouwd als SQL Server en Azure SQL Database, levert deze dezelfde surface area van de T-SQL-programmering die het ontwikkelen van toepassingen of oplossingen eenvoudiger en sneller maakt, en tegelijkertijd een toepassing maakt Draag baarheid tussen IoT Edge apparaten, data centers en de Cloud direct vooruit.

## <a name="deployment-models"></a>Implementatie modellen

Azure SQL Database Edge is beschikbaar op de Azure Marketplace en kan worden geïmplementeerd als een module voor [Azure IOT Edge](../iot-edge/about-iot-edge.md). Zie [Azure SQL database Edge implementeren](deploy-portal.md)voor meer informatie.<br>

![Overzichts diagram van SQL Database rand](media/overview/overview.png)

## <a name="editions-of-sql-database-edge"></a>Edities van SQL Database Edge

SQL Database Edge is beschikbaar met drie verschillende edities of software plannen. Deze edities hebben identieke functie sets en verschillen alleen met de gebruiks rechten en de hoeveelheid CPU/geheugen die ze ondersteunen.

   |**Plannen**  |**Beschrijving**  |
   |---------|---------|
   |Developer  |  SKU alleen voor ontwikkel aars, met dezelfde beperkings limieten die zijn ingesteld door de standaard-SKU die hieronder wordt vermeld |
   |Standard   |  Het Standard-abonnement biedt ondersteuning voor Maxi maal 4 CPU'S en Maxi maal 32 GB geheugen voor de SQL Database Edge-container. |
   |Premium    |  Premium SKU ondersteunt Maxi maal 8 kernen en Maxi maal 64 GB aan geheugen voor de SQL Database Edge-container. |

## <a name="pricing-and-availability"></a>Prijzen en beschik baarheid

Azure SQL Database is momenteel beschikbaar als preview-versie. Zie [Azure SQL database Edge](https://azure.microsoft.com/services/sql-database-edge/)voor meer informatie over de prijzen en beschik baarheid.

> [!IMPORTANT]
> Zie [SQL database EDGE Data Base-functies](https://azure.microsoft.com/services/sql-database-edge/)voor meer informatie over de functie verschillen tussen Azure SQL database edge en SQL Server, evenals de verschillen tussen de verschillende opties voor de Azure SQL database rand.

## <a name="streaming-capabilities"></a>Streaming-mogelijkheden  

Azure SQL Database Edge biedt ingebouwde streaming-mogelijkheden voor real-time analyse en complexe gebeurtenis verwerking. De streaming-mogelijkheid is gebouwd op basis van dezelfde constructies als [Azure stream Analytics](../stream-analytics/stream-analytics-introduction.md) en biedt vergelijk bare mogelijkheden als [Azure stream Analytics op IOT Edge](../stream-analytics/stream-analytics-edge.md).

De Streaming-Engine voor Azure SQL Database Edge is ontworpen voor lage latentie, tolerantie, efficiënt gebruik van band breedte en naleving.

## <a name="machine-learning-and-artificial-intelligence-capabilities"></a>Mogelijkheden voor Machine Learning en kunst matige intelligentie

Azure SQL Database Edge biedt ingebouwde machine learning en analyse mogelijkheden door de open format ONNX-runtime (open Neural Network Exchange) te integreren, waarmee diepe leren en Neural-netwerk modellen tussen verschillende Frameworks kunnen worden uitgewisseld. Zie [hier](https://onnx.ai/)voor meer informatie over ONNX. ONNX runtime biedt de flexibiliteit voor het ontwikkelen van modellen in een taal of hulpprogram ma's van uw keuze, die vervolgens kunnen worden geconverteerd naar de ONNX-indeling voor uitvoering binnen SQL Database Edge. Zie [machine learning en kunst matige intelligentie met ONNX in SQL database Edge](onnx-overview.md)(Engelstalig) voor meer informatie.

## <a name="working-with-azure-sql-database-edge"></a>Werken met Azure SQL Database Edge

Azure SQL Database Edge maakt het ontwikkelen en onderhouden van toepassingen eenvoudiger en productiever. Gebruikers kunnen alle vertrouwde hulpprogram ma's en vaardig heden gebruiken om geweldige apps en oplossingen te bouwen voor hun IoT Edge behoeften. Gebruiker kan in SQL Database Edge ontwikkelen met behulp van hulpprogram ma's zoals het volgende:

- [De Azure Portal](https://portal.azure.com/) -een webtoepassing voor het beheren van alle Azure-Services.
- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) : een gratis, Download bare client toepassing voor het beheren van een SQL-infra structuur, van SQL Server tot SQL database.
- [SQL Server Data tools in Visual Studio](/sql/ssdt/download-sql-server-data-tools-ssdt/) : een gratis, Download bare client toepassing voor het ontwikkelen van SQL Server relationele data bases, SQL-data bases, integratie Services-pakketten, Analysis Services gegevens modellen en Reporting Services-rapporten.
- [Azure Data Studio](/sql/azure-data-studio/what-is/) : een gratis, Download bare, data base tool voor meerdere platforms voor data Professional met behulp van de micro soft-familie van on-premises en Cloud gegevens platformen in Windows, MacOS en Linux.
- [Visual Studio code](https://code.visualstudio.com/docs) : een gratis, Download bare, open-source code-editor voor Windows, MacOS en Linux. Het ondersteunt extensies, waaronder de [MSSQL-extensie](https://aka.ms/mssql-marketplace) voor het uitvoeren van query's Microsoft SQL Server, Azure SQL Database en Azure SQL Data Warehouse.


## <a name="next-steps"></a>Volgende stappen

- Zie [Azure SQL database Edge](https://azure.microsoft.com/services/sql-database-edge/)voor prijzen en Details over de beschik baarheid.
- Aanvraag om Azure SQL Database Edge in te scha kelen voor uw abonnement.
- Ga als volgt te werk om aan de slag te gaan:
  - [SQL Database rand implementeren via Azure Portal](deploy-portal.md)
  - [Machine Learning en kunst matige intelligentie met SQL Database Edge](onnx-overview.md)
