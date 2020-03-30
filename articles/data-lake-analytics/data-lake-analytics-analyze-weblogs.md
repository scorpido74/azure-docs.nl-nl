---
title: Websitelogboeken analyseren met Azure Data Lake Analytics
description: Meer informatie over het analyseren van websitelogboeken met Azure Data Lake Analytics om U-SQL-functies en query's uit te voeren.
services: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 3a196735-d0d9-4deb-ba68-c4b3f3be8403
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 04c6d4c74a82ccfbcbb0faecb0dca5ec495f6663
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71672883"
---
# <a name="analyze-website-logs-using-azure-data-lake-analytics"></a>Websitelogboeken analyseren met Azure Data Lake Analytics
Meer informatie over het analyseren van websitelogboeken met Behulp van Data Lake Analytics, vooral bij het achterhalen van welke verwijzers fouten hebben gemaakt toen ze de website probeerden te bezoeken.

## <a name="prerequisites"></a>Vereisten
* **Visual Studio 2015 of Visual Studio 2013**.
* **[Data Lake Tools voor Visual Studio](https://aka.ms/adltoolsvs)**.

    Zodra Data Lake Tools voor Visual Studio is geïnstalleerd, ziet u een **Data Lake-item** in het menu **Extra** in Visual Studio:

    ![Menu U-SQL Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-menu.png)
* **Basiskennis van Data Lake Analytics en de Data Lake Tools voor Visual Studio.** Zie om aan de slag te gaan:

  * [Ontwikkel U-SQL-script met behulp van Data Lake-tools voor Visual Studio.](data-lake-analytics-data-lake-tools-get-started.md)
* **Een Data Lake Analytics-account.**  Zie [Een Azure Data Lake Analytics-account maken](data-lake-analytics-get-started-portal.md).
* **Installeer de voorbeeldgegevens.** Open in de Azure Portal u Data Lake Analytics-account en klik op **Voorbeeldscripts** in het linkermenu en klik vervolgens op **Voorbeeldgegevens kopiëren**. 

## <a name="connect-to-azure"></a>Verbinding maken met Azure
Voordat u U-SQL-scripts bouwen en testen, moet u eerst verbinding maken met Azure.

**Verbinding maken met Data Lake Analytics**

1. Open Visual Studio.
2. Klik **op Opties en instellingen voor Gegevensmeer >**.
3. Klik **op Aanmelden**of Gebruiker **wijzigen** als iemand zich heeft aangemeld en volg de instructies.
4. Klik op **OK** om het dialoogvenster Opties en instellingen te sluiten.

**Bladeren door uw Data Lake Analytics-accounts**

1. Open **serververkenner** in Visual Studio door op **Ctrl+ALT+S te**drukken .
2. Vouw in **Server Explorer** achtereenvolgens **Azure** en **Data Lake Analytics** uit. U ziet een lijst met uw Data Lake Analytics-accounts, als u die hebt. U geen Data Lake Analytics-accounts maken vanuit de studio. Zie Aan de [slag met Azure Data Lake Analytics met Azure Portal](data-lake-analytics-get-started-portal.md) of Aan de slag met Azure Data Lake Analytics met Azure [PowerShell](data-lake-analytics-get-started-powershell.md)als u een account wilt maken.

## <a name="develop-u-sql-application"></a>U-SQL-toepassing ontwikkelen
Een U-SQL-toepassing is meestal een U-SQL-script. Zie [Aan de slag met U-SQL](data-lake-analytics-u-sql-get-started.md)voor meer informatie over U-SQL.

U gebruikersgedefinieerde operatoren toevoegen aan de toepassing.  Zie [U-SQL-gebruikersgedefinieerde operators ontwikkelen voor Data Lake Analytics-taken voor](data-lake-analytics-u-sql-develop-user-defined-operators.md)meer informatie.

**Een Data Lake Analytics-taak maken en verzenden**

1. Klik **op het project Bestand > Nieuw >**.
2. Selecteer het type U-SQL Project.

    ![nieuw U-SQL Visual Studio-project](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)
3. Klik op **OK**. Visual maakt een oplossing met een Script.usql-bestand.
4. Voer het volgende script in het Script.usql-bestand in:

        // Create a database for easy reuse, so you don't need to read from a file every time.
        CREATE DATABASE IF NOT EXISTS SampleDBTutorials;

        // Create a Table valued function. TVF ensures that your jobs fetch data from the weblog file with the correct schema.
        DROP FUNCTION IF EXISTS SampleDBTutorials.dbo.WeblogsView;
        CREATE FUNCTION SampleDBTutorials.dbo.WeblogsView()
        RETURNS @result TABLE
        (
            s_date DateTime,
            s_time string,
            s_sitename string,
            cs_method string,
            cs_uristem string,
            cs_uriquery string,
            s_port int,
            cs_username string,
            c_ip string,
            cs_useragent string,
            cs_cookie string,
            cs_referer string,
            cs_host string,
            sc_status int,
            sc_substatus int,
            sc_win32status int,
            sc_bytes int,
            cs_bytes int,
            s_timetaken int
        )
        AS
        BEGIN

            @result = EXTRACT
                s_date DateTime,
                s_time string,
                s_sitename string,
                cs_method string,
                cs_uristem string,
                cs_uriquery string,
                s_port int,
                cs_username string,
                c_ip string,
                cs_useragent string,
                cs_cookie string,
                cs_referer string,
                cs_host string,
                sc_status int,
                sc_substatus int,
                sc_win32status int,
                sc_bytes int,
                cs_bytes int,
                s_timetaken int
            FROM @"/Samples/Data/WebLog.log"
            USING Extractors.Text(delimiter:' ');
            RETURN;
        END;

        // Create a table for storing referrers and status
        DROP TABLE IF EXISTS SampleDBTutorials.dbo.ReferrersPerDay;
        @weblog = SampleDBTutorials.dbo.WeblogsView();
        CREATE TABLE SampleDBTutorials.dbo.ReferrersPerDay
        (
            INDEX idx1
            CLUSTERED(Year ASC)
            DISTRIBUTED BY HASH(Year)
        ) AS

        SELECT s_date.Year AS Year,
            s_date.Month AS Month,
            s_date.Day AS Day,
            cs_referer,
            sc_status,
            COUNT(DISTINCT c_ip) AS cnt
        FROM @weblog
        GROUP BY s_date,
                cs_referer,
                sc_status;

    Zie Aan de slag [met Data Lake Analytics U-SQL-taal](data-lake-analytics-u-sql-get-started.md)om inzicht te krijgen in de U-SQL-taal.    
5. Voeg een nieuw U-SQL-script toe aan uw project en voer het volgende in:

        // Query the referrers that ran into errors
        @content =
            SELECT *
            FROM SampleDBTutorials.dbo.ReferrersPerDay
            WHERE sc_status >=400 AND sc_status < 500;

        OUTPUT @content
        TO @"/Samples/Outputs/UnsuccessfulResponses.log"
        USING Outputters.Tsv();
6. Schakel terug naar het eerste U-SQL-script en geef naast de knop **Verzenden** uw Analytics-account op.
7. Ga naar **Solution Explorer**, klik met de rechtermuisknop op **Script.usql** en klik vervolgens op **Build Script**. Controleer de resultaten in het deelvenster Uitvoer.
8. Ga naar **Solution Explorer**, klik met de rechtermuisknop op **Script.usql** en klik vervolgens op **Submit Script**.
9. Controleer of het **Analytics-account** de taak wilt uitvoeren en klik op **Verzenden.** Het resultaat van het verzenden en de koppeling naar de taak zijn beschikbaar in het resultaatvenster van Data Lake Tools voor Visual Studio wanneer het verzenden is voltooid.
10. Wacht tot de taak is voltooid.  Als de taak is mislukt, ontbreekt waarschijnlijk het bronbestand.  Zie het gedeelte Voorwaarde van deze zelfstudie. Zie Azure Data [Lake Analytics-taken controleren en oplossen](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)voor meer informatie over probleemoplossing.

    Wanneer de taak is voltooid, ziet u het volgende scherm:

    ![data lake analytics analyseren weblogs website logs](./media/data-lake-analytics-analyze-weblogs/data-lake-analytics-analyze-weblogs-job-completed.png)
11. Herhaal nu stap 7- 10 voor **Script1.usql**.

**De taakuitvoer weergeven**

1. Ga naar **Server Explorer**, vouw **Azure** uit, vouw **Data Lake Analytics** uit, vouw uw Data Lake Analytics-account uit, vouw **Storage Accounts** uit, klik met de rechtermuisknop op het Data Lake Storage-standaardaccount en klik vervolgens op **Explorer**.
2. Dubbelklik op **Voorbeelden** om de map te openen en dubbelklik op **Uitvoer**.
3. Dubbelklik op **UnsuccessfulResponses.log**.
4. U ook dubbelklikken op het uitvoerbestand in de grafiekweergave van de taak om rechtstreeks naar de uitvoer te navigeren.

## <a name="see-also"></a>Zie ook
Om aan de slag te gaan met Data Lake Analytics met verschillende hulpprogramma's, zie:

* [Aan de slag met Data Lake Analytics met Azure Portal](data-lake-analytics-get-started-portal.md)
* [Aan de slag met Data Lake Analytics met Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Aan de slag met Data Lake Analytics met .NET SDK](data-lake-analytics-get-started-net-sdk.md)
