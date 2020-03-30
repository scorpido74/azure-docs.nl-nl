---
title: Azure-functies gebruiken om een taak voor het opschonen van een database uit te voeren
description: Gebruik Azure-functies om een taak te plannen die verbinding maakt met Azure SQL Database om regelmatig rijen op te schonen.
ms.assetid: 076f5f95-f8d2-42c7-b7fd-6798856ba0bb
ms.topic: conceptual
ms.date: 10/02/2019
ms.openlocfilehash: 2e3f53943d45e90b8aff8e386ce8d0e28670673f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366803"
---
# <a name="use-azure-functions-to-connect-to-an-azure-sql-database"></a>Azure-functies gebruiken om verbinding te maken met een Azure SQL-database

In dit artikel ziet u hoe u Azure-functies gebruikt om een geplande taak te maken die verbinding maakt met een Azure SQL Database of Azure SQL Managed Instance. De functiecode ruimt rijen in een tabel in de database op. De nieuwe C#-functie is gemaakt op basis van een vooraf gedefinieerde timertriggersjabloon in Visual Studio 2019. Als u dit scenario wilt ondersteunen, moet u ook een tekenreeks voor databaseverbindingen instellen als app-instelling in de functie-app. Voor Azure SQL Managed Instance moet u [openbaar eindpunt inschakelen](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure) om verbinding te kunnen maken vanuit Azure-functies. In dit scenario wordt een bulkbewerking ten opzichte van de database gebruikt. 

Als dit uw eerste ervaring is met het werken met C#-functies, moet u de [referentie voor Azure Functions C# developer](functions-dotnet-class-library.md)lezen.

## <a name="prerequisites"></a>Vereisten

+ Voer de stappen uit in het artikel [Maak uw eerste functie met Visual Studio](functions-create-your-first-function-visual-studio.md) om een lokale functie-app te maken die zich richt op versie 2.x of een latere versie van de runtime. U moet uw project ook hebben gepubliceerd naar een functie-app in Azure.

+ In dit artikel wordt een transact-SQL-opdracht weergegeven waarmee een bulkopruimingsbewerking wordt uitgevoerd in de tabel **SalesOrderHeader** in de AdventureWorksLT-voorbeelddatabase. Als u de AdventureWorksLT-voorbeelddatabase wilt maken, voert u de stappen uit in het artikel [Een Azure SQL-database maken in de Azure-portal.](../sql-database/sql-database-get-started-portal.md)

+ U moet een [firewallregel op serverniveau](../sql-database/sql-database-get-started-portal-firewall.md) toevoegen voor het openbare IP-adres van de computer die u voor deze quickstart gebruikt. Deze regel is vereist om toegang te hebben tot de SQL-database-instantie vanaf uw lokale computer.  

## <a name="get-connection-information"></a>Verbindingsgegevens ophalen

U moet de verbindingstekenreeks voor de database die u hebt gemaakt wanneer u [een Azure SQL-database in de Azure-portal hebt gemaakt,](../sql-database/sql-database-get-started-portal.md)opvragen.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. Selecteer **SQL-databases** in het linkermenu en selecteer uw database op de pagina **SQL-databases.**

1. Selecteer **Verbindingstekenreeksen** onder **Instellingen** en kopieer de volledige **ADO.NET** verbindingstekenreeks. Voor Azure SQL Managed Instance kopieer verbindingstekenreeks voor openbaar eindpunt.

    ![Kopieer de verbindingsreeks voor ADO.NET.](./media/functions-scenario-database-table-cleanup/adonet-connection-string.png)

## <a name="set-the-connection-string"></a>De verbindingsreeks instellen

Een functie-app fungeert als host voor de uitvoering van uw functies in Azure. Als beste beveiligingspraktijk slaat u verbindingstekenreeksen en andere geheimen op in de instellingen van uw functie-app. Het gebruik van toepassingsinstellingen voorkomt dat de verbindingstekenreeks per ongeluk wordt openbaar gemaakt met uw code. U hebt vanuit Visual Studio toegang tot app-instellingen voor uw functie-app.

U moet uw app eerder hebben gepubliceerd naar Azure. Als u dit nog niet hebt gedaan, [publiceert u uw functie-app naar Azure.](functions-develop-vs.md#publish-to-azure)

1. Klik in Solution Explorer met de rechtermuisknop op het functie-app-project en kies**Azure App Service-instellingen** **publiceren** > . Selecteer **Instelling toevoegen**in De naam van de instelling Nieuwe **app**, typ `sqldb_connection`en selecteer **OK**.

    ![Toepassingsinstellingen voor de functie-app.](./media/functions-scenario-database-table-cleanup/functions-app-service-add-setting.png)

1. Plak in de nieuwe **sqldb_connection-instelling** de verbindingstekenreeks die u in `{your_username}` de `{your_password}` vorige sectie hebt gekopieerd in het veld **Lokaal** en vervang en tijdelijke aanduidingen met echte waarden. Selecteer **Waarde invoegen van lokaal** om de bijgewerkte waarde naar het veld Extern **te** kopiëren en selecteer **OK**.

    ![Sql-verbindingstekenreeksinstelling toevoegen.](./media/functions-scenario-database-table-cleanup/functions-app-service-settings-connection-string.png)

    De verbindingstekenreeksen worden versleuteld opgeslagen in Azure **(Remote).** Om lekkende geheimen te voorkomen, moet het projectbestand local.settings.json **(Local)** worden uitgesloten van bronbeheer, bijvoorbeeld met behulp van een .gitignore-bestand.

## <a name="add-the-sqlclient-package-to-the-project"></a>Het SqlClient-pakket toevoegen aan het project

U moet het NuGet-pakket toevoegen dat de SqlClient-bibliotheek bevat. Deze gegevenstoegangsbibliotheek is nodig om verbinding te maken met een SQL-database.

1. Open het project voor lokale functie-apps in Visual Studio 2019.

1. Klik in Solution Explorer met de rechtermuisknop op het functie-app-project en kies **NuGet-pakketten beheren.**

1. Zoek op het tabblad **Bladeren** naar ```System.Data.SqlClient``` en selecteer deze.

1. Selecteer op de pagina **System.Data.SqlClient** de versie `4.5.1` en klik op **Installeren**.

1. Wanneer de installatie is voltooid, controleert u de wijzigingen en klikt u vervolgens op **OK** om het venster **Preview** te sluiten.

1. Als een venster voor **akkoord gaan met de licentie** wordt weergegeven, klikt u op **Ik ga akkoord**.

U nu de C#-functiecode toevoegen die verbinding maakt met uw SQL-database.

## <a name="add-a-timer-triggered-function"></a>Een door een timer geactiveerde functie toevoegen

1. Klik in Solution Explorer met de rechtermuisknop op het functie-app-project en kies**Nieuwe Azure-functie** **toevoegen.** > 

1. Als de sjabloon **Azure-functies** is geselecteerd, geeft u het nieuwe item iets als en `DatabaseCleanup.cs` selecteert u **Toevoegen**.

1. Kies in het dialoogvenster **Nieuwe Azure-functie** de optie **Timertrigger** en vervolgens **OK**. In dit dialoogvenster wordt een codebestand voor de functie die wordt geactiveerd door de timer geactiveerd.

1. Open het nieuwe codebestand en voeg het volgende toe met instructies boven aan het bestand:

    ```cs
    using System.Data.SqlClient;
    using System.Threading.Tasks;
    ```

1. Vervang de `Run` bestaande functie door de volgende code:

    ```cs
    [FunctionName("DatabaseCleanup")]
    public static async Task Run([TimerTrigger("*/15 * * * * *")]TimerInfo myTimer, ILogger log)
    {
        // Get the connection string from app settings and use it to create a connection.
        var str = Environment.GetEnvironmentVariable("sqldb_connection");
        using (SqlConnection conn = new SqlConnection(str))
        {
            conn.Open();
            var text = "UPDATE SalesLT.SalesOrderHeader " +
                    "SET [Status] = 5  WHERE ShipDate < GetDate();";

            using (SqlCommand cmd = new SqlCommand(text, conn))
            {
                // Execute the command and log the # rows affected.
                var rows = await cmd.ExecuteNonQueryAsync();
                log.LogInformation($"{rows} rows were updated");
            }
        }
    }
    ```

    Deze functie wordt elke 15 `Status` seconden uitgevoerd om de kolom bij te werken op basis van de verzenddatum. Zie [Timertrigger voor Azure-functies voor](functions-bindings-timer.md)meer informatie over de trigger voor timer .

1. Druk op **F5** om de functie-app te starten. Het uitvoeringsvenster [Azure Functions Core Tools](functions-develop-local.md) wordt geopend achter Visual Studio.

1. Op 15 seconden na het opstarten wordt de functie uitgevoerd. Bekijk de uitvoer en noteer het aantal rijen dat is bijgewerkt in de tabel **SalesOrderHeader.**

    ![Bekijk de functielogboeken.](./media/functions-scenario-database-table-cleanup/function-execution-results-log.png)

    Bij de eerste uitvoering moet u 32 rijen met gegevens bijwerken. Na uitvoeringen worden geen gegevensrijen bijgewerkt, tenzij u wijzigingen aanbrengt in `UPDATE` de tabelgegevens van SalesOrderHeader, zodat meer rijen worden geselecteerd door de instructie.

Als u [van](functions-develop-vs.md#publish-to-azure)plan bent deze `TimerTrigger` functie te publiceren, vergeet dan niet om het kenmerk te wijzigen in een redelijker [cron-schema](functions-bindings-timer.md#ncrontab-expressions) dan elke 15 seconden.

## <a name="next-steps"></a>Volgende stappen

Leer vervolgens hoe u deze gebruiken. Functies met Logic Apps om te integreren met andere services.

> [!div class="nextstepaction"]
> [Een functie maken die integreert met Logic Apps](functions-twitter-email.md)

Zie de volgende artikelen voor meer informatie over functies:

+ [Naslaginformatie over Azure Functions voor ontwikkelaars](functions-reference.md)  
  Naslaginformatie voor programmeurs over het coderen van functies en het definiëren van triggers en bindingen.
+ [Azure Functions testen](functions-test-a-function.md)  
  Beschrijft de verschillende hulpprogramma’s en technieken voor het testen van uw functies.  
