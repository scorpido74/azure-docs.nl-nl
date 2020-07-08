---
title: Pakketten beheren met Azure-SSIS Integration Runtime-pakket archief
description: Meer informatie over het beheren van pakketten met Azure-SSIS Integration Runtime-pakket opslag.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 05/25/2020
ms.openlocfilehash: fa1ab60ae5690bd3782e4c849ca7cb5c29d640c2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84199054"
---
# <a name="manage-packages-with-azure-ssis-integration-runtime-package-store"></a>Pakketten beheren met Azure-SSIS Integration Runtime-pakket archief

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Als u & uw SSIS-workloads (on-premises SQL Server Integration Services) naar de Cloud wilt verplaatsen, kunt u Azure-SSIS Integration Runtime (IR) inrichten in Azure Data Factory (ADF). Zie [een Azure-SSIS IR inrichten](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)voor meer informatie. Een Azure-SSIS IR ondersteunt:

- Pakketten die zijn geïmplementeerd in SSIS Catalog (SSISDB) die worden gehost door een server of beheerd exemplaar van Azure SQL Database (projectimplementatiemodel)
- Pakketten die zijn geïmplementeerd in het bestandssysteem, Azure Files of SQL Server-database (MSDB) die worden gehost door Azure SQL Managed Instance (pakketimplementatiemodel)

Wanneer u pakket implementatie model gebruikt, kunt u kiezen of u uw Azure-SSIS IR wilt inrichten met pakket archieven die een pakket beheer-laag hebben op bestands systeem/Azure Files/MSDB die wordt gehost door Azure SQL Managed instance. Met Azure-SSIS IR-pakket archief kunt u pakketten importeren/exporteren/verwijderen en uitvoeren en de uitvoering van pakketten via SQL Server Management Studio (SSMS) controleren en stoppen, vergelijkbaar met de [verouderde SSIS-pakket opslag](https://docs.microsoft.com/sql/integration-services/service/package-management-ssis-service?view=sql-server-2017). 

## <a name="connect-to-azure-ssis-ir"></a>Verbinding maken met Azure-SSIS IR

Zodra uw Azure-SSIS IR is ingericht, kunt u er verbinding mee maken om door de pakket winkels op SSMS te bladeren.

![Verbinding maken met Azure-SSIS IR](media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect.png)

Selecteer in het venster **objectverkenner** van SSMS **Azure-SSIS Integration runtime** in de vervolg keuzelijst **verbinding maken** . Meld u vervolgens aan bij Azure en selecteer het relevante abonnement, de ADF en de Azure-SSIS IR, waarnaar uw pakket winkels zijn gekoppeld. Uw Azure-SSIS IR wordt weer gegeven met **actieve pakketten** en de knoop punten **opgeslagen pakketten** onder. Vouw het knoop punt **opgeslagen pakketten** uit om uw pakket winkels onder te bekijken. Breid uw pakket archieven uit om de onderliggende mappen en pakketten weer te geven. Mogelijk wordt u gevraagd om de toegangs referenties voor uw pakket archieven in te voeren, als SSMS niet automatisch verbinding kan maken. Als u bijvoorbeeld een pakket archief boven op MSDB uitbreidt, wordt u mogelijk gevraagd om eerst verbinding te maken met uw Azure SQL-beheerde exemplaar.

![Verbinding maken met Azure SQL Managed instance](media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect2.png)

## <a name="manage-folders-and-packages"></a>Mappen en pakketten beheren

Wanneer u door de Azure-SSIS IR op SSMS bladert, kunt u met de rechter muisknop op een pakket opslaan/mappen/pakketten klikken om een menu te popupen en een **nieuwe map**te selecteren, pakket **importeren**, **pakket exporteren**, **verwijderen**of **vernieuwen**.

   ![Mappen en pakketten beheren](media/azure-ssis-integration-runtime-package-store/ssms-package-store-manage.png)

   *  Selecteer **nieuwe map** om een nieuwe map voor geïmporteerde pakketten te maken.

   *  Selecteer **pakket importeren** om pakketten te importeren uit **bestands systeem**, **SQL Server** (msdb) of het verouderde **SSIS-pakket** in uw pakket opslag.

      ![Pakket importeren](media/azure-ssis-integration-runtime-package-store/ssms-package-store-import.png)

      Afhankelijk van de locatie van het **pakket** waaruit u wilt importeren, selecteert u het relevante type **Server** / **verificatie**, voert u de toegangs referenties in als dat nodig is, selecteert u het **pad naar het pakket**en voert u de nieuwe **pakket naam**in. Bij het importeren van pakketten kan het beveiligings niveau ervan niet worden gewijzigd. Gebruik SQL Server Data Tools (SSDT) of het opdracht regel programma om het te wijzigen `dtutil` .

   *  Selecteer **pakket exporteren** om pakketten uit uw pakket archief te exporteren naar **bestands systeem**, **SQL Server** (msdb) of de verouderde **SSIS-pakket opslag**.

      ![Pakket exporteren](media/azure-ssis-integration-runtime-package-store/ssms-package-store-export.png)

      Afhankelijk van de **pakket locatie** waarnaar u wilt exporteren, selecteert u het relevante type **Server** / **verificatie**, voert u indien nodig de toegangs referenties in en selecteert u het **pad naar het pakket**. Wanneer u pakketten exporteert, voert u de wacht woorden in om ze eerst te ontsleutelen en vervolgens kunt u het beveiligings niveau wijzigen, bijvoorbeeld om gevoelige gegevens niet op te slaan of om alle/gevoelige gegevens te versleutelen met gebruikers sleutel/wacht woord.

   *  Selecteer **verwijderen** om bestaande mappen/pakketten uit uw pakket archief te verwijderen.

   *  Selecteer **vernieuwen** om nieuw toegevoegde mappen/pakketten weer te geven in uw pakket opslag.

## <a name="execute-packages"></a>Pakketten uitvoeren

Wanneer u door de Azure-SSIS IR op SSMS bladert, kunt u met de rechter muisknop op een opgeslagen pakket klikken om een menu te popupen en **pakket uitvoeren**te selecteren.  Hiermee opent u het dialoog venster **Execute package Utility** , waarin u de pakket uitvoeringen op Azure-SSIS IR kunt configureren als uitvoering van SSIS-pakket activiteiten in ADF-pijp lijnen.

![Execute Package Utility pagina's 1 & 2](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute.png)

![Execute Package Utility pagina's 3 & 4](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute2.png)

De pagina's **Algemeen**, **configuratie**, **uitvoerings opties**en **logboek registratie** van **Execute package Utility** komen overeen met het tabblad **instellingen** van de activiteit uitvoeren SSIS-pakket, waar u het versleutelings wachtwoord voor uw pakket kunt invoeren, toegang hebt tot informatie voor uw pakket configuratie bestand, referenties voor het pakket en toegang tot gegevens voor uw logboek map.  De pagina **waarden instellen** van **Execute package Utility** dialoog venster komt overeen met het tabblad **Eigenschappen onderdrukkingen** van de activiteit uitvoeren SSIS-pakket, waar u de bestaande pakket eigenschappen kunt opgeven die u wilt overschrijven. Zie [SSIS-pakketten uitvoeren als uitvoering van SSIS-pakket activiteiten in ADF-pijp lijnen](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)voor meer informatie.

Wanneer u de knop **uitvoeren** van **Execute package Utility** dialoog venster selecteert, wordt er automatisch een nieuwe ADF-pijp lijn met activiteit voor het uitvoeren van SSIS-pakketten gegenereerd en geactiveerd. Als er al een ADF-pijp lijn met dezelfde instellingen voor het uitvoeren van het pakket bestaat, wordt deze opnieuw uitgevoerd en wordt er geen nieuwe pijp lijn gegenereerd. De activiteiten ADF pijp lijn en execute SSIS-pakket krijgen `Pipeline_SSMS_YourPackageName_HashString` respectievelijk de naam en `Activity_SSMS_YourPackageName` .

![Knop Execute Package Utility](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute3.png)

![SSIS-pakket activiteit uitvoeren](media/azure-ssis-integration-runtime-package-store/ssis-activity-package-store.png)

## <a name="monitor-and-stop-running-packages"></a>Het uitvoeren van pakketten controleren en stoppen

Wanneer u door de Azure-SSIS IR op SSMS bladert, kunt u het knoop punt **actieve pakketten** uitvouwen om uw pakketten die momenteel worden uitgevoerd, weer te geven.  Klik met de rechter muisknop op een van de opdrachten om een menu te popupen en selecteer **stoppen** of **vernieuwen**.

   ![Het uitvoeren van pakketten controleren en stoppen](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor.png)

   *  Selecteer **stoppen** om de momenteel UITGEVOERDe ADF-pijp lijn te annuleren die het pakket uitvoert als EXECUTe SSIS-pakket activiteit.

   *  Selecteer **vernieuwen** om nieuwe pakketten uit uw pakket archieven weer te geven.

## <a name="monitor-azure-ssis-ir-and-edit-package-stores"></a>Azure-SSIS IR bewaken en pakket archieven bewerken

Wanneer u door de Azure-SSIS IR op SSMS bladert, kunt u er met de rechter muisknop op klikken om een menu te pop-up en **Ga naar Azure Data Factory Portal** of **vernieuwen**te selecteren.

   ![Naar de ADF-Portal](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor2.png)

   *  Selecteer **Ga naar Azure Data Factory Portal** om de pagina **Integration Runtimes** van ADF monitoring hub te openen, waar u uw Azure-SSIS IR kunt bewaken. Op de tegel **pakket opslag** ziet u het aantal pakket archieven dat aan uw Azure-SSIS IR is gekoppeld.  Als u dat nummer selecteert, wordt er een venster weer gegeven waarin u gekoppelde ADF-Services kunt bewerken waarmee de toegangs gegevens voor uw pakket archieven worden opgeslagen.

      ![Pakket archieven bewerken](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor3.png)

   *  Selecteer **vernieuwen** om onlangs toegevoegde mappen/pakketten in uw pakket op te slaan en uit te voeren in uw pakket archieven.

## <a name="next-steps"></a>Volgende stappen

U kunt de automatisch gegenereerde ADF-pijp lijnen opnieuw uitvoeren/bewerken met het uitvoeren van SSIS-pakket activiteiten of nieuwe maken in de ADF-Portal. Zie [SSIS-pakketten uitvoeren als uitvoering van SSIS-pakket activiteiten in ADF-pijp lijnen](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)voor meer informatie.