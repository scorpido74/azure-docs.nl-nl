---
title: Gegevens visualiseren met Power BI Microsoft Azure
description: SQL Data Warehouse-gegevens visualiseren met Power BI
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 7e764eeab6f681d90e1a602f02cdb03330d6fd3d
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350947"
---
# <a name="visualize-data-with-power-bi"></a>Gegevens visualiseren met Power BI
In deze zelfstudie leert u hoe u Power BI gebruikt om verbinding te maken met SQL Data Warehouse en hoe u enkele eenvoudige visualisaties maakt.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Data-Warehouse-Sample-Data-and-PowerBI/player]
> 
> 

## <a name="prerequisites"></a>Vereisten
Voor deze zelfstudie hebt u het volgende nodig:

* Een SQL Data Warehouse die vooraf is geladen met de AdventureWorksDW-dataware. Zie [Een SQL Data Warehouse maken](create-data-warehouse-portal.md) en ervoor kiezen om de voorbeeldgegevens te laden als u een gegevensmagazijn wilt inrichten. Als u al een datawarehouse hebt, maar geen voorbeeldgegevens hebt, u [WideWorldImportersDW laden.](load-data-wideworldimportersdw.md)

## <a name="1-connect-to-your-database"></a>1. Verbinding maken met uw database
Ga als volgt te werk om Power BI te openen en verbinding te maken met de database AdventureWorksDW:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Klik op **SQL-databases** en kies de SQL Data Warehouse-database AdventureWorks.
   
    ![De database zoeken](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-find-database.png)
3. Klik op de knop Openen in Power BI.
   
    ![Power BI-knop](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-button.png)
4. Nu ziet u de verbindingspagina voor SQL Data Warehouse met het webadres van uw database. Klik op volgende.
   
    ![Verbinding met Power BI](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-connect-to-azure.png)
5. Voer de gebruikersnaam en het wachtwoord van uw Azure SQL-server in.
   
    ![Power BI-aanmelding](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-sign-in.png)
6. Als u de database wilt openen, klikt u op de gegevensset AdventureWorksDW op het linkerblad.
   
    ![AdventureWorksDW wordt geopend in Power BI](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-open-adventureworks.png)

## <a name="2-create-a-report"></a>2. Een rapport maken
Nu kunt u Power BI gebruiken om de voorbeeldgegevens uit AdventureWorksDW te analyseren. Voor de analyse heeft AdventureWorksDW een weergave die AggregateSales wordt genoemd. Deze weergave bevat enkele van de belangrijkste metrische gegevens om de verkoopcijfers van het bedrijf te analyseren.

1. Als u een kaart wilt maken van de totale verkoop op postcode, klikt u in het rechterdeelvenster met velden op de weergave AggregateSales om deze uit te vouwen. Klik op de kolommen PostalCode en SalesAmount om deze te selecteren.
   
    ![Power BI selecteert AggregateSales](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-aggregatesales.png)
   
    Power BI herkende automatisch geografische gegevens en zet deze voor u in een kaart.
   
    ![Power BI-kaart](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-map.png)

2. In deze stap maakt u een staafdiagram waarin de totale verkoop per klantinkomen wordt weergegeven. Als u de staafgrafiek wilt maken, gaat u naar de uitgebreide weergave AggregateSales. Klik in het veld SalesAmount. Sleep het veld Customer Income naar links en zet het neer in As.
   
    ![Power BI selecteert as](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-chooseaxis.png)
   
    Het staafdiagram aan de linkerkant.
   
    ![Power BI-staafdiagram](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-bar.png)
3. Met deze stap maakt u een lijndiagram waarin de omzet per datum wordt weergegeven. Als u het lijndiagram wilt maken, gaat u naar de uitgebreide weergave AggregateSales. Klik op SalesAmount en OrderDate. Klik in de kolom Visualisaties op het pictogram Lijndiagram, het eerste pictogram in de tweede regel onder visualisaties.
   
    ![Power BI selecteert lijndiagram](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-prepare-line.png)
   
    U hebt nu een rapport met drie verschillende visualisaties van de gegevens.
   
    ![Power BI-lijndiagram](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-line.png)

U kunt de voortgang op elk moment opslaan door op **Bestand** te klikken en **Opslaan** te selecteren.

## <a name="using-direct-connect"></a>Direct Connect gebruiken
Net als bij Azure SQL Database maakt SQL Data Warehouse Direct Connect logische pushdown mogelijk naast de analytische mogelijkheden van Power BI. Met Direct Connect worden query's in realtime teruggestuurd naar uw Azure SQL Data Warehouse terwijl u de gegevens verkent.  Met deze functie u, in combinatie met de schaal van SQL Data Warehouse, binnen enkele minuten dynamische rapporten maken tegen terabytes aan gegevens. Bovendien stelt de introductie van de open in Power BI-knop gebruikers in staat om Power BI rechtstreeks te verbinden met hun SQL Data Warehouse zonder informatie te verzamelen uit andere delen van Azure.

Bij gebruik van Direct Connect:

* Geef de volledig gekwalificeerde servernaam op bij het verbinden.
* Ervoor zorgen dat firewallregels voor de database zijn geconfigureerd om toegang tot Azure-services toe te staan.
* Elke actie, zoals het selecteren van een kolom of het toevoegen van een filter, brengt direct het gegevensmagazijn op.
* Tegels worden automatisch en ongeveer om de 15 minuten vernieuwd.
* Q&A is niet beschikbaar voor Direct Connect-gegevenssets.
* Schemawijzigingen worden automatisch opgenomen.
* Alle Direct Connect-query's hebben een time-out na 2 minuten.

Deze beperkingen en notities kunnen veranderen naarmate de ervaringen verbeteren.

## <a name="next-steps"></a>Volgende stappen
Nu we je wat tijd hebben gegeven om op te warmen met de voorbeeldgegevens, zie je hoe je [je ontwikkelen](sql-data-warehouse-overview-develop.md) of [laden.](design-elt-data-loading.md) Of neem een kijkje op de [Power BI website.](https://www.powerbi.com/)
