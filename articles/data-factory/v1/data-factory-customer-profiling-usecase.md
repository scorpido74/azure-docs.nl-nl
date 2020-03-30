---
title: Use Case - Klantprofilering
description: Ontdek hoe Azure Data Factory wordt gebruikt om een datagestuurde werkstroom (pijplijn) te maken voor klanten van het profiel van gaming.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: c570f988dea894b8106405f4e427edb386a3e74a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75969293"
---
# <a name="use-case---customer-profiling"></a>Use Case - Klantprofilering
Azure Data Factory is een van de vele services die worden gebruikt om de Cortana Intelligence Suite van oplossingsversnellers te implementeren.  Ga voor meer informatie over Cortana Intelligence naar [Cortana Intelligence Suite](https://www.microsoft.com/cortanaanalytics). In dit document beschrijven we een eenvoudige use case om u op weg te helpen met het begrijpen hoe Azure Data Factory veelvoorkomende analyseproblemen kan oplossen.

## <a name="scenario"></a>Scenario
Contoso is een gamebedrijf dat games maakt voor meerdere platforms: gameconsoles, handbediende apparaten en pc's . Terwijl spelers deze spellen spelen, wordt een groot volume aan loggegevens geproduceerd die de gebruikspatronen, speelstijl en voorkeuren van de gebruiker bijhouden.  In combinatie met demografische, regionale en productgegevens kan Contoso analyses uitvoeren om hen te begeleiden hoe ze de ervaring van spelers kunnen verbeteren en erop kunnen richten voor upgrades en in-game aankopen. 

Contoso's doel is om up-sell/cross-sell mogelijkheden te identificeren op basis van de gaminggeschiedenis van zijn spelers en aantrekkelijke functies toe te voegen om de bedrijfsgroei te stimuleren en klanten een betere ervaring te bieden. Voor deze use case gebruiken we een gaming bedrijf als voorbeeld van een bedrijf. Het bedrijf wil zijn games optimaliseren op basis van het gedrag van spelers. Deze principes zijn van toepassing op elk bedrijf dat zijn klanten wil betrekken bij zijn goederen en diensten en de ervaring van hun klanten wil verbeteren.

In deze oplossing wil Contoso de effectiviteit evalueren van een marketingcampagne die het onlangs heeft gelanceerd. We beginnen met de raw gaming logs, verwerken en verrijken ze met geolocatiegegevens, voegen deze samen met advertentiereferentiegegevens en kopiëren ze ten slotte naar een Azure SQL-database om de impact van de campagne te analyseren.

## <a name="deploy-solution"></a>Oplossing implementeren
Het enige wat u nodig hebt om toegang te krijgen tot deze eenvoudige use case is een [Azure-abonnement,](https://azure.microsoft.com/pricing/free-trial/)een [Azure Blob-opslagaccount](../../storage/common/storage-account-create.md)en een [Azure SQL-database.](../../sql-database/sql-database-get-started.md) U implementeert de pijplijn voor klantprofilering vanuit de tegel **Voorbeeldpijplijnen** op de startpagina van uw gegevensfabriek.

1. Maak een gegevensfabriek of open een bestaande gegevensfabriek. Zie [Gegevens kopiëren van Blob-opslag naar SQL-database met Gegevensfabriek](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor stappen om een gegevensfabriek te maken.
2. Klik in het **DATA FACTORY-blad** voor de gegevensfabriek op de tegel **Voorbeeldpijplijnen.**

    ![Betegel voorbeeldpijplijnen](./media/data-factory-samples/SamplePipelinesTile.png)
3. Klik in het blade **van voorbeeldpijplijnen** op de **klantprofilering** die u wilt implementeren.

    ![Voorbeeldvan pijpleidingenblad](./media/data-factory-samples/SampleTile.png)
4. Geef configuratie-instellingen op voor het voorbeeld. Bijvoorbeeld de naam en sleutel van uw Azure-opslagaccount, de naam van Azure SQL-server, de database, de gebruikersnaam en het wachtwoord.

    ![Monsterblad](./media/data-factory-samples/SampleBlade.png)
5. Nadat u klaar bent met het opgeven van de configuratie-instellingen, klikt u op **Maken** om de voorbeeldpijplijnen en gekoppelde services/tabellen die door de pijplijnen worden gebruikt, te maken/implementeren.
6. U ziet de status van implementatie op de voorbeeldtegel waarop u eerder hebt geklikt op het blade van de **voorbeeldpijplijnen.**

    ![Implementatiestatus](./media/data-factory-samples/DeploymentStatus.png)
7. Wanneer u het bericht **Implementatie op de** tegel voor het voorbeeld ziet, sluit u het blade van de **voorbeeldpijplijnen.**  
8. Op **DATA FACTORY blade** ziet u dat gekoppelde services, gegevenssets en pijplijnen worden toegevoegd aan uw gegevensfabriek.  

    ![Blade Gegevensfactory](./media/data-factory-samples/DataFactoryBladeAfter.png)

## <a name="solution-overview"></a>Overzicht van de oplossing
Deze eenvoudige use case kan worden gebruikt als een voorbeeld van hoe u Azure Data Factory gebruiken om gegevens in te nemen, voor te bereiden, te transformeren, te analyseren en te publiceren.

![End-to-end werkstroom](./media/data-factory-customer-profiling-usecase/EndToEndWorkflow.png)

In dit cijfer wordt weergegeven hoe de gegevenspijplijnen worden weergegeven in de Azure-portal nadat ze zijn geïmplementeerd.

1. De **PartitionGameLogsPipeline** leest de onbewerkte gamegebeurtenissen uit blob-opslag en maakt partities op basis van jaar, maand en dag.
2. De **EnrichGameLogsPipeline** voegt partitiegamegebeurtenissen samen met geocodereferentiegegevens en verrijkt de gegevens door IP-adressen toe te geven aan de overeenkomstige geolocaties.
3. De **Pipeline AnalyzeMarketingCampaignPipeline** gebruikt de verrijkte gegevens en verwerkt deze met de advertentiegegevens om de uiteindelijke uitvoer te maken die de effectiviteit van marketingcampagnes bevat.

In dit voorbeeld wordt Data Factory gebruikt om activiteiten te orkestreren die invoergegevens kopiëren, transformeren en verwerken en de uiteindelijke gegevens uitvoeren naar een Azure SQL-database.  U ook het netwerk van gegevenspijplijnen visualiseren, deze beheren en hun status controleren vanuit de gebruikersinterface.

## <a name="benefits"></a>Voordelen
Door hun gebruikersprofielanalyses te optimaliseren en deze af te stemmen op zakelijke doelen, is het gamebedrijf in staat om snel gebruikspatronen te verzamelen en de effectiviteit van zijn marketingcampagnes te analyseren.

