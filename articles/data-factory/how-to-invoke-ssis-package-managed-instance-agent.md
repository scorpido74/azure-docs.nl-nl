---
title: SSIS-pakketten uitvoeren door Azure SQL Managed Instance Agent
description: Meer informatie over het uitvoeren van SSIS-pakketten door Azure SQL Managed Instance Agent.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: lle
author: lle
ms.date: 04/14/2020
ms.openlocfilehash: b3b7a25149a9d075c81b30307ade2beb71907637
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394720"
---
# <a name="execute-ssis-packages-by-azure-sql-managed-instance-agent"></a>SSIS-pakketten uitvoeren door Azure SQL Managed Instance Agent
In dit artikel wordt beschreven hoe u een SQL Server Integration Services-pakket (SSIS) uitvoert met Azure SQL Managed Instance Agent. Deze functie biedt vergelijkbare gedragingen, net als wanneer u SSIS-pakketten plant door SQL Server Agent in uw on-prem-omgeving.

Met deze functie u SSIS-pakketten uitvoeren die zijn opgeslagen in SSISDB van Azure SQL Managed Instance of File System zoals Azure Files.

## <a name="prerequisites"></a>Vereisten
Download en installeer de nieuwste versie van SSMS, versie 18.5 of hoger, om deze functie te gebruiken. Download het van [deze website.](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)

En u moet een Azure-SSIS-integratieruntime inrichten in Azure Data Factory, waarin Azure SQL Managed Instance als eindpuntserver wordt gebruikt. Als u het nog niet hebt ingericht, indient u deze door instructies in de zelfstudie te [volgen.](tutorial-create-azure-ssis-runtime-portal.md) 

## <a name="run-ssis-packages-in-ssisdb-by-azure-sql-managed-instance-agent"></a>SSIS-pakketten uitvoeren in SSISDB door Azure SQL Managed Instance Agent
In deze stap gebruikt u Azure SQL Managed Instance Agent om SSIS-pakketten aan te roepen die zijn opgeslagen in SSISDB in Azure SQL Managed Instance.
1. Maak in de nieuwste versie van SSMS verbinding met Azure SQL Managed Instance.
2. Maak een nieuwe agentjob en een nieuwe jobstap.

![Nieuwe agent Job](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

3. Kies **sql server-integratieservicespakkettype** op de pagina **Nieuwe taakstap.**

![Nieuwe SSIS-taakstap](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

4. Kies op het tabblad **Pakket** de optie **SSIS-catalogus** als pakketbrontype.
5. Omdat de SSISDB zich in dezelfde Azure SQL Managed Instance bevindt, hoeft u geen verificatie op te geven.
6. Geef een SSIS-pakket op van uw SSISDB.

![Type packagebron - SSIS-catalogus](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb.png)

7. Op het tabblad **Configuraties** u **parameterwaarden** opgeven, waarden overschrijven in **Verbindingsbeheer,** **Eigenschap** overschrijven en **Logboekregistratieniveau kiezen.**

![Pakketbrontype - Configuratie van ssis-catalogus](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb-configuration.png)

8. Nadat u alle bovenstaande configuratie hebt voltooid, klikt u op **OK** om de configuratie van de agenttaak op te slaan.
9. Start de agenttaak om het SSIS-pakket uit te voeren.


## <a name="run-ssis-packages-in-file-system-by-azure-sql-managed-instance-agent"></a>SSIS-pakketten uitvoeren in bestandssysteem door Azure SQL managed instance agent
In deze stap gebruikt u Azure SQL Managed Instance Agent om SSIS-pakketten aan te roepen die zijn opgeslagen in bestandssysteem om uit te voeren.
1. Maak in de nieuwste versie van SSMS verbinding met Azure SQL Managed Instance.
2. Maak een nieuwe agentjob en een nieuwe jobstap.

   ![Nieuwe agent Job](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

3. Kies **sql server-integratieservicespakkettype** op de pagina **Nieuwe taakstap.**

   ![Nieuwe SSIS-taakstap](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

4. Kies op het tabblad **Pakket** **het bestandssysteem** als pakketbrontype.

   ![Type packagebron - bestandssysteem](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-file-system.png)

   1. Als uw pakket is geüpload naar Azure File, kiest u **Azure-bestandsshare** als bestandsbrontype.
      - Het pakketpad is ** \\ <storage account name>.file.core.windows.net\< \<bestandssharenaam>pakketnaam>.dtsx**
      - Typ de naam en accountsleutel van Azure-bestandsaccount in **de toegangsreferenties voor pakketbestanden** om toegang te krijgen tot het Azure-bestand. Het domein is ingesteld als **Azure**.
   2. Als uw pakket wordt geüpload naar een netwerkshare, kiest u **Netwerkdelen** als bestandsbrontype.
      - Het pakketpad is het **UNC-pad** van uw pakketbestand met de dtsx-extensie.
      - Typ het bijbehorende **domein,** **gebruikersnaam**en **wachtwoord** om toegang te krijgen tot het netwerksharepakketbestand.
   3. Als uw pakketbestand is versleuteld met een wachtwoord, selecteert u **Versleutelingswachtwoord** en typt u het wachtwoord in.

 5. Typ **op** het tabblad Configuraties het **pad met configuratiebestanden** als u een configuratiebestand nodig hebt om het SSIS-pakket uit te voeren.
 6. Op het tabblad **Uitvoeringsopties** u kiezen of u **windows-verificatie** of **32-bits runtime** wilt gebruiken om het SSIS-pakket uit te voeren.
 7. Op het tabblad **Logboekregistratie** u het **logboekregistratiepad** en de bijbehorende registratietoegangsreferenties kiezen om de logboekbestanden op te slaan. Standaard is het logboekpad hetzelfde als het pad met de pakketmap en is de toegangsreferentie voor logboekregistratie hetzelfde als de referentie voor pakkettoegangs.
 8. Op het tabblad **Waarden instellen** u het **eigenschappenpad** en de **waarde** intypen om de eigenschappen van het pakket te overschrijven.
 Als u bijvoorbeeld de waarde van uw gebruikersvariabele wilt overschrijven, voert u het pad in de volgende indeling in: **<variable name>\Package.Variables[User:: ]. Waarde**.
 9. Nadat u alle bovenstaande configuratie hebt voltooid, klikt u op **OK** om de configuratie van de agenttaak op te slaan.
 10. Start de agenttaak om het SSIS-pakket uit te voeren.


 ## <a name="cancel-ssis-package-execution"></a>Uitvoering ssis-pakket annuleren
 Als u pakketuitvoering wilt annuleren van een Azure SQL Managed Agent-taak, moet u onderstaande stappen volgen in plaats van de agenttaak rechtstreeks te stoppen.
 1. Vind uw SQL agent **jobId** van **msdb.dbo.sysjobs**.
 2. Zoek de bijbehorende **SSIS-executionId** op basis van de taak-id op onderstaande query:
    ```sql
    select * from ssisdb.internal.execution_parameter_values_noncatalog where  parameter_value = 'SQL_Agent_Job_{jobId}' order by execution_id desc
    ```
 3. Selecteer **Actieve bewerkingen** onder De SSIS-catalogus.

    ![Type packagebron - bestandssysteem](./media/how-to-invoke-ssis-package-managed-instance-agent/catalog-active-operations.png)

 4. De overeenkomstige bewerking stoppen op basis **van executionId**.

## <a name="next-steps"></a>Volgende stappen
 U ook SSIS-pakketten plannen met Azure Data Factory. Zie [Azure Data Factory Event Trigger](how-to-create-event-trigger.md)voor stapsgewijze instructies. 