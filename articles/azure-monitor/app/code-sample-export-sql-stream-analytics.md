---
title: Exporteren naar SQL vanuit Azure Application Insights | Microsoft Documenten
description: Exporteer voortdurend Application Insights-gegevens naar SQL met Behulp van Stream Analytics.
ms.topic: conceptual
ms.date: 09/11/2017
ms.openlocfilehash: 3ef0420cdab64f11b699fd4031ed2b0134f18609
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663672"
---
# <a name="walkthrough-export-to-sql-from-application-insights-using-stream-analytics"></a>Walkthrough: exporteren naar SQL vanuit Application Insights met Stream Analytics
In dit artikel ziet u hoe u uw telemetriegegevens van [Azure Application Insights][start] naar een Azure SQL-database verplaatst met behulp van Continuous [Export][export] en Azure [Stream Analytics.](https://azure.microsoft.com/services/stream-analytics/) 

Continue export verplaatst uw telemetriegegevens naar Azure Storage in JSON-indeling. We ontleden de JSON-objecten met Azure Stream Analytics en maken rijen in een databasetabel.

(Meer in het algemeen is Continuous Export de manier om uw eigen analyse te maken van de telemetrie die uw apps naar Application Insights sturen. U dit codevoorbeeld aanpassen om andere dingen te doen met de geëxporteerde telemetrie, zoals het samenvoegen van gegevens.)

We beginnen met de veronderstelling dat je de app die je wilt controleren al hebt.

In dit voorbeeld gebruiken we de gegevens voor de paginaweergave, maar hetzelfde patroon kan eenvoudig worden uitgebreid naar andere gegevenstypen, zoals aangepaste gebeurtenissen en uitzonderingen. 

## <a name="add-application-insights-to-your-application"></a>Toepassingsinzichten toevoegen aan uw toepassing
Aan de slag:

1. [Toepassingsinzichten instellen voor uw webpagina's.](../../azure-monitor/app/javascript.md) 
   
    (In dit voorbeeld richten we ons op het verwerken van paginaweergavegegevens van de clientbrowsers, maar u ook Application Insights instellen voor de serverkant van uw [Java-](../../azure-monitor/app/java-get-started.md) of [ASP.NET-app-](../../azure-monitor/app/asp-net.md) en procesaanvraag, afhankelijkheid en andere servertelemetrie.)
2. Publiceer uw app en bekijk telemetriegegevens die worden weergegeven in uw Application Insights-bron.

## <a name="create-storage-in-azure"></a>Opslag maken in Azure
Continue export levert altijd gegevens op naar een Azure Storage-account, dus u moet eerst de opslag maken.

1. Maak een opslagaccount in uw abonnement in de [Azure-portal.][portal]
   
    ![Kies in Azure-portal Nieuw, Gegevens, Opslag. Selecteer Klassiek en kies Maken. Geef een naam opslag op.](./media/code-sample-export-sql-stream-analytics/040-store.png)
2. Een container maken
   
    ![Selecteer in de nieuwe opslag Containers, klik op de tegel Containers en voeg vervolgens Toe](./media/code-sample-export-sql-stream-analytics/050-container.png)
3. De opslagtoegangssleutel kopiëren
   
    Je hebt het snel nodig om de invoer voor de stream analytics-service in te stellen.
   
    ![Open in de opslag Instellingen, Toetsen en neem een kopie van de primaire toegangssleutel](./media/code-sample-export-sql-stream-analytics/21-storage-key.png)

## <a name="start-continuous-export-to-azure-storage"></a>Continue export starten naar Azure-opslag
1. Blader in de Azure-portal naar de Application Insights-bron die u voor uw toepassing hebt gemaakt.
   
    ![Kies Bladeren, toepassingsinzichten, uw toepassing](./media/code-sample-export-sql-stream-analytics/060-browse.png)
2. Maak een continue export.
   
    ![Kies Instellingen, Continu exporteren, Toevoegen](./media/code-sample-export-sql-stream-analytics/070-export.png)

    Selecteer het opslagaccount dat u eerder hebt gemaakt:

    ![De exportbestemming instellen](./media/code-sample-export-sql-stream-analytics/080-add.png)

    Stel de gebeurtenistypen in die u wilt zien:

    ![Gebeurtenistypen kiezen](./media/code-sample-export-sql-stream-analytics/085-types.png)


1. Laat sommige gegevens zich ophopen. Achterover leunen en laat mensen gebruik maken van uw toepassing voor een tijdje. Telemetrie komt binnen en u ziet statistische grafieken in [metrische verkenner](../../azure-monitor/app/metrics-explorer.md) en afzonderlijke gebeurtenissen in [diagnostisch zoeken.](../../azure-monitor/app/diagnostic-search.md) 
   
    En ook zullen de gegevens exporteren naar uw opslag. 
2. Controleer de geëxporteerde gegevens in de portal - kies **Bladeren,** selecteer uw opslagaccount en vervolgens **Containers** - of in Visual Studio. Kies in Visual Studio **Weergave / Cloud Explorer**en open Azure / Storage. (Als u deze menuoptie niet hebt, moet u de Azure SDK installeren: Open het dialoogvenster Nieuw project en open Visual C# / Cloud / Get Microsoft Azure SDK voor .NET.)
   
    ![Open serverbrowser, Azure, Storage in Visual Studio](./media/code-sample-export-sql-stream-analytics/087-explorer.png)
   
    Noteer het algemene deel van de padnaam, dat is afgeleid van de toepassingsnaam en de instrumentatietoets. 

De gebeurtenissen worden geschreven naar blobbestanden in JSON-indeling. Elk bestand kan een of meer gebeurtenissen bevatten. Daarom willen we de gebeurtenisgegevens lezen en de velden filteren die we willen. Er zijn allerlei dingen die we kunnen doen met de gegevens, maar ons plan vandaag is om Stream Analytics te gebruiken om de gegevens te verplaatsen naar een SQL-database. Dat maakt het gemakkelijk om veel interessante query's uit te voeren.

## <a name="create-an-azure-sql-database"></a>Een Azure SQL-database maken
Opnieuw vanaf uw abonnement in [Azure portal,][portal]maak de database (en een nieuwe server, tenzij je er al een hebt) waarop u de gegevens schrijft.

![Nieuw, Gegevens, SQL](./media/code-sample-export-sql-stream-analytics/090-sql.png)

Controleer of de databaseserver toegang geeft tot Azure-services:

![Bladeren, servers, uw server, instellingen, firewall, toegang tot Azure toestaan](./media/code-sample-export-sql-stream-analytics/100-sqlaccess.png)

## <a name="create-a-table-in-azure-sql-db"></a>Een tabel maken in Azure SQL DB
Maak verbinding met de database die in de vorige sectie is gemaakt met het beheerprogramma voor uw voorkeur. In deze walkthrough zullen we [SQL Server Management Tools](https://msdn.microsoft.com/ms174173.aspx) (SSMS) gebruiken.

![](./media/code-sample-export-sql-stream-analytics/31-sql-table.png)

Maak een nieuwe query en voer de volgende T-SQL uit:

```SQL

CREATE TABLE [dbo].[PageViewsTable](
    [pageName] [nvarchar](max) NOT NULL,
    [viewCount] [int] NOT NULL,
    [url] [nvarchar](max) NULL,
    [urlDataPort] [int] NULL,
    [urlDataprotocol] [nvarchar](50) NULL,
    [urlDataHost] [nvarchar](50) NULL,
    [urlDataBase] [nvarchar](50) NULL,
    [urlDataHashTag] [nvarchar](max) NULL,
    [eventTime] [datetime] NOT NULL,
    [isSynthetic] [nvarchar](50) NULL,
    [deviceId] [nvarchar](50) NULL,
    [deviceType] [nvarchar](50) NULL,
    [os] [nvarchar](50) NULL,
    [osVersion] [nvarchar](50) NULL,
    [locale] [nvarchar](50) NULL,
    [userAgent] [nvarchar](max) NULL,
    [browser] [nvarchar](50) NULL,
    [browserVersion] [nvarchar](50) NULL,
    [screenResolution] [nvarchar](50) NULL,
    [sessionId] [nvarchar](max) NULL,
    [sessionIsFirst] [nvarchar](50) NULL,
    [clientIp] [nvarchar](50) NULL,
    [continent] [nvarchar](50) NULL,
    [country] [nvarchar](50) NULL,
    [province] [nvarchar](50) NULL,
    [city] [nvarchar](50) NULL
)

CREATE CLUSTERED INDEX [pvTblIdx] ON [dbo].[PageViewsTable]
(
    [eventTime] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, SORT_IN_TEMPDB = OFF, DROP_EXISTING = OFF, ONLINE = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON)

```

![](./media/code-sample-export-sql-stream-analytics/34-create-table.png)

In dit voorbeeld gebruiken we gegevens van paginaweergaven. Als u de andere beschikbare gegevens wilt bekijken, controleert u uw JSON-uitvoer en ziet u het [exportgegevensmodel](../../azure-monitor/app/export-data-model.md).

## <a name="create-an-azure-stream-analytics-instance"></a>Een Azure Stream Analytics-exemplaar maken
Selecteer in de [Azure-portal](https://portal.azure.com/)de Azure Stream Analytics-service en maak een nieuwe Stream Analytics-taak:

![Instellingen voor streamanalyses](./media/code-sample-export-sql-stream-analytics/SA001.png)

![](./media/code-sample-export-sql-stream-analytics/SA002.png)

Wanneer de nieuwe taak is gemaakt, selecteert u **Ga naar resource**.

![Instellingen voor streamanalyses](./media/code-sample-export-sql-stream-analytics/SA003.png)

#### <a name="add-a-new-input"></a>Een nieuwe invoer toevoegen

![Instellingen voor streamanalyses](./media/code-sample-export-sql-stream-analytics/SA004.png)

Stel deze in om invoer uit de blob Continue export te nemen:

![Instellingen voor streamanalyses](./media/code-sample-export-sql-stream-analytics/SA0005.png)

Nu heb je de primaire toegangssleutel nodig van je opslagaccount, die je eerder hebt opgemerkt. Stel dit in als de opslagaccountsleutel.

#### <a name="set-path-prefix-pattern"></a>Padvoorvoegingspatroon instellen

**Zorg ervoor dat u de datumnotatie instelt op YYYY-MM-DD (met streepjes).**

Het padvoorvoegingspatroon geeft aan hoe Stream Analytics de invoerbestanden in de opslag vindt. U moet deze instellen om overeen te komen met de manier waarop Continuous Export de gegevens opslaat. Stel het als volgt in:

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

In dit voorbeeld:

* `webapplication27`is de naam van de application insights-bron, **allemaal in kleine letters.** 
* `1234...`is de instrumentatiesleutel van de application insights-bron **waarbij streepjes zijn verwijderd.** 
* `PageViews`is het type gegevens dat we willen analyseren. De beschikbare typen zijn afhankelijk van het filter dat u instelt in Continue export. Controleer de geëxporteerde gegevens om de andere beschikbare typen te bekijken en zie het [exportgegevensmodel](../../azure-monitor/app/export-data-model.md).
* `/{date}/{time}`is een patroon letterlijk geschreven.

Open Essentials op de overzichtspagina of open Instellingen om de naam en iKey van uw Application Insights-bron te krijgen.

> [!TIP]
> Gebruik de voorbeeldfunctie om te controleren of u het invoerpad correct hebt ingesteld. Als dit niet lukt: controleer of er gegevens in de opslag zijn voor het voorbeeldtijdbereik dat u hebt gekozen. Bewerk de invoerdefinitie en controleer of u het opslagaccount, het voorvoegsel en de datumnotatie correct instelt.

 
## <a name="set-query"></a>Query instellen
Open de querysectie:

Vervang de standaardquery door:

```SQL

    SELECT flat.ArrayValue.name as pageName
    , flat.ArrayValue.count as viewCount
    , flat.ArrayValue.url as url
    , flat.ArrayValue.urlData.port as urlDataPort
    , flat.ArrayValue.urlData.protocol as urlDataprotocol
    , flat.ArrayValue.urlData.host as urlDataHost
    , flat.ArrayValue.urlData.base as urlDataBase
    , flat.ArrayValue.urlData.hashTag as urlDataHashTag
      ,A.context.data.eventTime as eventTime
      ,A.context.data.isSynthetic as isSynthetic
      ,A.context.device.id as deviceId
      ,A.context.device.type as deviceType
      ,A.context.device.os as os
      ,A.context.device.osVersion as osVersion
      ,A.context.device.locale as locale
      ,A.context.device.userAgent as userAgent
      ,A.context.device.browser as browser
      ,A.context.device.browserVersion as browserVersion
      ,A.context.device.screenResolution.value as screenResolution
      ,A.context.session.id as sessionId
      ,A.context.session.isFirst as sessionIsFirst
      ,A.context.location.clientip as clientIp
      ,A.context.location.continent as continent
      ,A.context.location.country as country
      ,A.context.location.province as province
      ,A.context.location.city as city
    INTO
      AIOutput
    FROM AIinput A
    CROSS APPLY GetElements(A.[view]) as flat


```

De eerste paar eigenschappen zijn specifiek voor paginaweergavegegevens. Export van andere telemetrietypen heeft verschillende eigenschappen. Zie de [gedetailleerde gegevensmodelreferentie voor de eigenschapstypen en -waarden.](../../azure-monitor/app/export-data-model.md)

## <a name="set-up-output-to-database"></a>Uitvoer instellen in database
Selecteer SQL als uitvoer.

![Selecteer uitvoer uitvoer in streamanalytics](./media/code-sample-export-sql-stream-analytics/SA006.png)

Geef de SQL-database op.

![Vul de gegevens van uw database in](./media/code-sample-export-sql-stream-analytics/SA007.png)

Sluit de wizard en wacht op een melding dat de uitvoer is ingesteld.

## <a name="start-processing"></a>Verwerking starten
Start de taak vanaf de actiebalk:

![Klik in streamanalytics op Start](./media/code-sample-export-sql-stream-analytics/SA008.png)

U kiezen of u vanaf nu wilt beginnen met het verwerken van de gegevens of om te beginnen met eerdere gegevens. Dit laatste is handig als u Continu Exporteren al een tijdje hebt uitgevoerd.

Ga na een paar minuten terug naar SQL Server Management Tools en kijk hoe de gegevens binnenkomen. Gebruik bijvoorbeeld een query als deze:

    SELECT TOP 100 *
    FROM [dbo].[PageViewsTable]


## <a name="related-articles"></a>Verwante artikelen:
* [Exporteren naar PowerBI met Behulp van Stream Analytics](../../azure-monitor/app/export-power-bi.md )
* [Gedetailleerde gegevensmodelreferentie voor de eigenschapstypen en -waarden.](../../azure-monitor/app/export-data-model.md)
* [Continue export in applicatie-inzichten](../../azure-monitor/app/export-telemetry.md)
* [Application Insights](https://azure.microsoft.com/services/application-insights/)

<!--Link references-->

[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[export]: ../../azure-monitor/app/export-telemetry.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[portal]: https://portal.azure.com/
[start]: ../../azure-monitor/app/app-insights-overview.md

