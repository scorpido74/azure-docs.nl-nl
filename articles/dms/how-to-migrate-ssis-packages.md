---
title: SSIS-pakketten opnieuw implementeren in SQL-database
titleSuffix: Azure Database Migration Service
description: Meer informatie over het migreren of opnieuw implementeren van SQL Server Integration Services-pakketten en -projecten naar azure SQL Database single database met behulp van de Azure Database Migration Service en Data Migration Assistant.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 90a39b8fe3604a05f1d35a875ae4e34491b47d72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77648526"
---
# <a name="redeploy-ssis-packages-to-azure-sql-database-with-azure-database-migration-service"></a>SSIS-pakketten opnieuw implementeren in Azure SQL-database met Azure Database Migration Service

Als u SQL Server Integration Services (SSIS) gebruikt en uw SSIS-projecten/-pakketten wilt migreren van de bron SSISDB die door SQL Server wordt gehost naar de bestemming SSISDB die wordt gehost door Azure SQL Database, u deze opnieuw implementeren met de wizard Implementatie van Integration Services. U de wizard starten vanuit SQL Server Management Studio (SSMS).

Als de versie van SSIS die u gebruikt eerder is dan 2012, moet u deze eerst converteren met de wizard Projectconversie van Integration Services, die ook vanaf SSMS kan worden gestart. Zie voor meer informatie het artikel [Projecten converteren naar het projectimplementatiemodel](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert).

> [!NOTE]
> De Azure Database Migration Service (DMS) ondersteunt momenteel niet de migratie van een bron SSISDB naar een Azure SQL Database-server, maar u uw SSIS-projecten/-pakketten opnieuw implementeren met behulp van het volgende proces.

In dit artikel leert u het volgende:
> [!div class="checklist"]
>
> * Beoordeel bron SSIS-projecten/-pakketten.
> * SSIS-projecten/-pakketten migreren naar Azure.

## <a name="prerequisites"></a>Vereisten

Als u deze stappen wilt uitvoeren, moet u het volgende doen:

* SSMS versie 17.2 of hoger.
* Een instantie van uw doeldatabaseserver om SSISDB te hosten. Als u er nog geen hebt, maakt u een Azure SQL Database-server (zonder database) met behulp van de Azure-portal door te navigeren naar het [SQL Server-formulier](https://ms.portal.azure.com/#create/Microsoft.SQLServer)(alleen logische server).
* SSIS moet worden ingericht in Azure Data Factory (ADF) met Azure-SSIS Integration Runtime (IR) met de bestemming SSISDB die wordt gehost door de instantie van Azure SQL Database-server (zoals beschreven in het artikel [Provision the Azure-SSIS Integration Runtime in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)).

## <a name="assess-source-ssis-projectspackages"></a>Beoordeel bron SSIS-projecten/-pakketten

Hoewel de beoordeling van bron SSISDB nog niet is geïntegreerd in de Database Migration Assistant (DMA) of de Azure Database Migration Service (DMS), worden uw SSIS-projecten/-pakketten beoordeeld/gevalideerd wanneer ze opnieuw worden geïmplementeerd op de bestemming SSISDB die wordt gehost op een Azure SQL-databaseserver.

## <a name="migrate-ssis-projectspackages"></a>SSIS-projecten/-pakketten migreren

Voer de volgende stappen uit om SSIS-projecten/pakketten te migreren naar Azure SQL Database-server.

1. Open SSMS en selecteer **Opties om** het dialoogvenster Verbinding maken met server weer **te** geven.

2. Geef op het tabblad **Aanmelding** de informatie op die nodig is om verbinding te maken met de Azure SQL Database-server die de doelsisdb host.

    ![Tabblad SSIS-aanmelding](media/how-to-migrate-ssis-packages/dms-ssis-login-tab.png)

3. Selecteer of voer op het tabblad **Verbindingseigenschappen** in het tekstvak **Verbinding maken met database** **ssisdb**in of voer deze in en selecteer **Vervolgens Verbinding maken**.

    ![Tabblad Eigenschappen van SSIS-verbinding](media/how-to-migrate-ssis-packages/dms-ssis-conncetion-properties-tab.png)

4. Vouw in de SSMS Object Explorer het knooppunt **Integratieservicescatalogi** uit, vouw **SSISDB**uit en als er geen bestaande mappen zijn, klik je met de rechtermuisknop op **SSISDB** en maak je een nieuwe map.

5. Vouw onder **SSISDB**een map uit, klik met de rechtermuisknop op **Projecten**en selecteer **Project implementeren**.

    ![SSIS SSISDB-knooppunt uitgebreid](media/how-to-migrate-ssis-packages/dms-ssis-ssisdb-node-expanded.png)

6. Bekijk de informatie in de wizard Implementatie integratieservices op de pagina **Inleiding** en selecteer **Volgende**.

    ![Introductiepagina van de wizard Deployment](media/how-to-migrate-ssis-packages/dms-deployment-wizard-introduction-page.png)

7. Geef op de pagina **Bron selecteren** het bestaande SSIS-project op dat u wilt implementeren.

    Als SSMS ook is verbonden met de SQL Server die de bron SSISDB host, selecteert u **de integratieservicescatalogus**en voert u de servernaam en het projectpad in uw catalogus in om uw project rechtstreeks te implementeren.

    Selecteer afwisselend **projectimplementatiebestand**en geef vervolgens het pad op naar een bestaand projectimplementatiebestand (.ispac) om uw project te implementeren.

    ![Pagina Bron selecteren van de wizard Deployment](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-source-page.png)
 
8. Selecteer **Volgende**.
9. Geef op de pagina **Doel selecteren** de bestemming voor uw project op.

    a. Voer in het tekstvak Servernaam de volledig gekwalificeerde Azure SQL Database-servernaam (<server_name>.database.windows.net) in.

    b. Geef de verificatiegegevens op en selecteer **Verbinding maken**.

    ![Wizard Deployment Selecteer doelpagina](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-destination-page.png)

    c. Selecteer **Bladeren** om de doelmap in SSISDB op te geven en selecteer **Volgende**.

    > [!NOTE]
    > De knop **Volgende** is alleen ingeschakeld nadat u **Verbinding**hebt geselecteerd.

10. Bekijk op de pagina **Valideren** eventuele fouten/waarschuwingen en wijzig indien nodig uw pakketten dienovereenkomstig.

    ![Pagina Validatie van de wizard Implementatie](media/how-to-migrate-ssis-packages/dms-deployment-wizard-validate-page.png)

11. Selecteer **Volgende**.

12. Controleer **op** de pagina Controleren uw implementatie-instellingen.

    > [!NOTE]
    > U uw instellingen wijzigen door **Vorige te** selecteren of door een van de stapkoppelingen in het linkerdeelvenster te selecteren.

13. Selecteer **Implementeren** om het implementatieproces te starten.

14. Nadat het implementatieproces is voltooid, u de pagina Resultaten bekijken, waarin het succes of de fout van elke implementatieactie wordt weergegeven.
    a. Als een actie is mislukt, selecteert u in de kolom **Resultaat** de optie **Kan geen** uitleg van de fout worden weergegeven.
    b. Selecteer eventueel **Rapport opslaan** om de resultaten op te slaan in een XML-bestand.

15. Selecteer **Sluiten** om de wizard Implementatie van Integratieservices af te sluiten.

Als de implementatie van uw project zonder fouten slaagt, u alle pakketten selecteren die het bevat om op uw Azure-SSIS IR uit te voeren.

## <a name="next-steps"></a>Volgende stappen

* Bekijk de migratierichtlijnen in de [Migratiehandleiding voor Microsoft-database](https://datamigration.microsoft.com/).
