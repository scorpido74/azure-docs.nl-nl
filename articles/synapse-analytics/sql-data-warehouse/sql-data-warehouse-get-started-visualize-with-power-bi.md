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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80350947"
---
# <a name="visualize-data-with-power-bi"></a>Gegevens visualiseren met Power BI
In deze zelfstudie leert u hoe u Power BI gebruikt om verbinding te maken met SQL Data Warehouse en hoe u enkele eenvoudige visualisaties maakt.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Data-Warehouse-Sample-Data-and-PowerBI/player]
> 
> 

## <a name="prerequisites"></a>Vereisten
Voor deze zelfstudie hebt u het volgende nodig:

* Een SQL Data Warehouse die vooraf is geladen met de AdventureWorksDW-dataware. Zie [Create a SQL Data Warehouse](create-data-warehouse-portal.md) en kies ervoor om de voorbeeld gegevens te laden om een Data Warehouse in te richten. Als u al een Data Warehouse hebt, maar geen voorbeeld gegevens hebt, kunt u [WideWorldImportersDW laden](load-data-wideworldimportersdw.md).

## <a name="1-connect-to-your-database"></a>1. Maak verbinding met uw data base
Ga als volgt te werk om Power BI te openen en verbinding te maken met de database AdventureWorksDW:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/).
2. Klik op **SQL-databases** en kies de SQL Data Warehouse-database AdventureWorks.
   
    ![De database zoeken](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-find-database.png)
3. Klik op de knop Openen in Power BI.
   
    ![Power BI-knop](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-button.png)
4. Nu ziet u de verbindingspagina voor SQL Data Warehouse met het webadres van uw database. Klik op volgende.
   
    ![Verbinding met Power BI](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-connect-to-azure.png)
5. Voer de gebruikers naam en het wacht woord voor uw Azure SQL-Server in.
   
    ![Power BI aanmelden](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-sign-in.png)
6. Als u de Data Base wilt openen, klikt u op de AdventureWorksDW-gegevensset op de Blade links.
   
    ![AdventureWorksDW wordt geopend in Power BI](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-open-adventureworks.png)

## <a name="2-create-a-report"></a>2. een rapport maken
Nu kunt u Power BI gebruiken om de voorbeeldgegevens uit AdventureWorksDW te analyseren. Voor de analyse heeft AdventureWorksDW een weergave die AggregateSales wordt genoemd. Deze weergave bevat enkele van de belangrijkste metrische gegevens om de verkoopcijfers van het bedrijf te analyseren.

1. Als u een kaart wilt maken van de totale verkoop op postcode, klikt u in het rechterdeelvenster met velden op de weergave AggregateSales om deze uit te vouwen. Klik op de kolommen PostalCode en SalesAmount om deze te selecteren.
   
    ![Power BI selecteert AggregateSales](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-aggregatesales.png)
   
    Power BI automatisch geografische gegevens herkend en in een kaart voor u geplaatst.
   
    ![Power BI-kaart](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-map.png)

2. In deze stap maakt u een staafdiagram waarin de totale verkoop per klantinkomen wordt weergegeven. Als u de staaf grafiek wilt maken, gaat u naar de uitgevouwen weer gave AggregateSales. Klik in het veld SalesAmount. Sleep het veld Customer Income naar links en zet het neer in As.
   
    ![Power BI selecteert as](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-chooseaxis.png)
   
    Het staaf diagram wordt aan de linkerkant.
   
    ![Power BI-staafdiagram](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-bar.png)
3. Met deze stap maakt u een lijndiagram waarin de omzet per datum wordt weergegeven. Als u het lijn diagram wilt maken, gaat u naar de uitgevouwen weer gave AggregateSales. Klik op SalesAmount en OrderDate. Klik in de kolom visualisaties op het pictogram lijn diagram, het eerste pictogram in de tweede regel onder visualisaties.
   
    ![Power BI lijn diagram selecteren](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-prepare-line.png)
   
    U hebt nu een rapport met drie verschillende visualisaties van de gegevens.
   
    ![Power BI-lijndiagram](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-line.png)

U kunt de voortgang op elk moment opslaan door op **Bestand** te klikken en **Opslaan** te selecteren.

## <a name="using-direct-connect"></a>Direct Connect gebruiken
Net als bij Azure SQL Database maakt SQL Data Warehouse directe verbinding logische pushdown naast de analytische mogelijkheden van Power BI. Met Direct Connect worden query's in realtime teruggestuurd naar uw Azure SQL Data Warehouse tijdens het verkennen van de gegevens.  Met deze functie, gecombineerd met de schaal van SQL Data Warehouse, kunt u binnen enkele minuten dynamische rapporten maken op basis van terabytes aan gegevens. Daarnaast kunnen gebruikers rechtstreeks verbinding maken met Power BI met hun SQL Data Warehouse Power BI, zonder dat er gegevens uit andere onderdelen van Azure worden verzameld.

Wanneer u direct Connect gebruikt:

* Geef de volledig gekwalificeerde server naam op wanneer u verbinding maakt.
* Zorg ervoor dat de firewall regels voor de data base zo zijn geconfigureerd dat toegang tot Azure-Services is toegestaan.
* Voor elke actie, zoals het selecteren van een kolom of het toevoegen van een filter, wordt het Data Warehouse rechtstreeks opgevraagd.
* Tegels worden automatisch en ongeveer elke 15 minuten vernieuwd.
* Q&A is niet beschikbaar voor Direct Connect-gegevens sets.
* Schema wijzigingen worden automatisch opgenomen.
* Alle direct connect-query's verloopt na twee minuten een time-out.

Deze beperkingen en opmerkingen kunnen veranderen wanneer de ervaring wordt verbeterd.

## <a name="next-steps"></a>Volgende stappen
Nu u hebt opgegeven dat u de voorbeeld gegevens wilt opwarmen, raadpleegt u [ontwikkelen](sql-data-warehouse-overview-develop.md) of [laden](design-elt-data-loading.md). Of kijk eens naar de [Power bi-website](https://www.powerbi.com/).
