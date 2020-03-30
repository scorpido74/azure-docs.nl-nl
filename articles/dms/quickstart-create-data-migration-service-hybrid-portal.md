---
title: 'Snelstart: een instantie voor hybride modus maken met Azure-portal'
titleSuffix: Azure Database Migration Service
description: Gebruik de Azure-portal om een exemplaar van Azure Database Migration Service in hybride modus te maken.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: quickstart
ms.date: 03/13/2020
ms.openlocfilehash: dd3e77610749eb5d146b0c0b7cf9d307fba0dd83
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79370233"
---
# <a name="quickstart-create-a-hybrid-mode-instance-with-azure-portal--azure-database-migration-service"></a>Snelstart: een instantie voor hybride modus maken met Azure-portal & Azure Database Migration Service

De hybride modus Azure Database Migration Service beheert databasemigraties met behulp van een migratiemedewerker die on-premises wordt gehost, samen met een instantie van Azure Database Migration Service die in de cloud wordt uitgevoerd. De hybride modus is vooral handig voor scenario's waarin er een gebrek is aan site-to-site-connectiviteit tussen het on-premises netwerk en Azure of als er beperkte bandbreedte is voor connectiviteit van site-to-site.

>[!NOTE]
>Momenteel ondersteunt Azure Database Migration Service die in hybride modus wordt uitgevoerd SQL Server-migraties naar:
>
>- Azure SQL Database beheerd instantie met bijna nul downtime (online).
>- Azure SQL Database single database with some downtime (offline).
>- MongoDb naar Azure CosmosDB met bijna nul downtime (online).
>- MongoDb naar Azure CosmosDB met enige downtime (offline).

In deze Quickstart gebruikt u de Azure-portal om een exemplaar van Azure Database Migration Service in hybride modus te maken. Daarna downloadt, installeert en stelt u de hybride werknemer in uw on-premises netwerk in. Tijdens de preview u de hybridemodus Azure Database Migration Service gebruiken om gegevens te migreren van een on-premises instantie van SQL Server naar Azure SQL Database.

> [!NOTE]
> Het hybride installatieprogramma azure database migration service wordt uitgevoerd op Microsoft Windows Server 2012 R2, Window Server 2016, Windows Server 2019 en Windows 10.

> [!IMPORTANT]
> Het hybride installatieprogramma Azure Database Migration Service vereist .NET 4.7.2 of hoger. Zie de pagina [.NET Framework downloaden](https://dotnet.microsoft.com/download/dotnet-framework) om de nieuwste versies van .NET te vinden.

Als u geen Azure-abonnement hebt, maakt u een [gratis](https://azure.microsoft.com/free/) account voordat u begint.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Open de webbrowser, navigeer naar [Microsoft Azure Portal](https://portal.azure.com/), en voer vervolgens uw referenties in om u aan te melden bij de portal.

De standaardweergave is uw service-dashboard.

## <a name="register-the-resource-provider"></a>De resourceprovider registreren

Registreer de Microsoft.DataMigration-bronprovider voordat u uw eerste instantie van Azure Database Migration Service maakt.

1. Selecteer in de Azure-portal **Abonnementen,** selecteer het abonnement waarin u de instantie van Azure Database Migration Service wilt maken en selecteer vervolgens **Resourceproviders**.

    ![Zoekmachineprovider zoeken](media/quickstart-create-data-migration-service-hybrid-portal/dms-portal-search-resource-provider.png)

2. Zoek naar migratie en selecteer Vervolgens rechts van **Microsoft.DataMigration**de optie **Register**.

    ![Resourceprovider registreren](media/quickstart-create-data-migration-service-hybrid-portal/dms-portal-register-resource-provider.png)

## <a name="create-an-instance-of-the-service"></a>Een exemplaar van de service maken

1. Selecteer +**Een resource maken** om een instantie van Azure Database Migration Service te maken.

2. Zoek op de Marketplace naar 'migratie', selecteer **Azure Database Migration Service**en selecteer vervolgens in het scherm Azure Database Migration **Service** de optie **Maken**.

3. Ga in het scherm **Migratieservice maken** als volgt te werk:

    - Kies een **servicenaam** die gedenkwaardig en uniek is om uw exemplaar van Azure Database Migration Service te identificeren.
    - Selecteer het Azure-**abonnement** waarin u het exemplaar wilt maken.
    - Selecteer een bestaande **Resourcegroep** of maak een nieuwe.
    - Kies de **locatie** die zich het dichtst bij uw bron- of doelserver bevindt.
    - Selecteer **Hybride (voorbeeld) voor** **servicemodus**.

         ![Migratieservice maken - basisbeginselen](media/quickstart-create-data-migration-service-hybrid-portal/dms-create-service-basics.png)

4. Selecteer **Controleren + maken**.

5. Bekijk op het tabblad **Controleren + maken** de voorwaarden, controleer de andere verstrekte informatie en selecteer Vervolgens **Maken**.

    ![Migratieservice maken - Controleren + maken](media/quickstart-create-data-migration-service-hybrid-portal/dms-create-service-review-and-create.png)

    Na enkele ogenblikken wordt uw exemplaar van Azure Database Migration Service in hybride modus gemaakt en klaar om in te stellen. De instantie Azure Database Migration Service wordt weergegeven zoals weergegeven in de volgende afbeelding:

    ![Instantie van de hybride modus azure database migration service](media/quickstart-create-data-migration-service-hybrid-portal/dms-instance-hybrid-mode.png)

6. Nadat de service is gemaakt, selecteert u **Eigenschappen**en kopieert u de waarde die wordt weergegeven in het vak **Resource-id,** waarmee u de hybride werknemer Azure Database Migration Service installeert.

    ![Eigenschappen van de hybride modus Azure Database Migration Service](media/quickstart-create-data-migration-service-hybrid-portal/dms-copy-resource-id.png)

## <a name="create-azure-app-registration-id"></a>Azure App-registratie-id maken

U moet een Azure App-registratie-id maken die de on-premises hybride werknemer kan gebruiken om te communiceren met Azure Database Migration Service in de cloud.

1. Selecteer in de Azure-portal **Azure Active Directory,** selecteer **App-registraties**en selecteer **Vervolgens Nieuwe registratie**.
2. Geef een naam voor de toepassing op en selecteer vervolgens onder **Ondersteunde accounttypen**het type accounts dat moet worden ondersteund om op te geven wie de toepassing kan gebruiken.

    ![Registertoepassing voor de hybride modus van Azure Database Migration Service](media/quickstart-create-data-migration-service-hybrid-portal/dms-register-application.png)

3. Gebruik de standaardwaarden voor de velden **URI (optioneel) omleiden** en selecteer **Vervolgens Registreren**.

4. Nadat de app-id-registratie is voltooid, noteert u de **id van toepassing (client**), die u zult gebruiken tijdens het installeren van de hybride werknemer.

5. Navigeer in de Azure-portal naar Azure Database Migration Service, selecteer **Toegangsbeheer (IAM)** en selecteer **Roltoewijzing toevoegen** om inzendertoegang toe te wijzen aan de App-id.

    ![Hybride modus Azure Database Migration Service toewijzen inzenderrol](media/quickstart-create-data-migration-service-hybrid-portal/dms-app-assign-contributor.png)

6. Selecteer **Inzender** als rol, wijs toegang toe tot **azure AD-gebruiker of serviceprincipal**en selecteer vervolgens de naam app-id.

    ![De hybride modus Azure Database Migration Service wijst details van de inzenderrol toe](media/quickstart-create-data-migration-service-hybrid-portal/dms-add-role-assignment.png)

7. Selecteer **Opslaan** om de roltoewijzing voor de App-id op te slaan in de Bron Azure Database Migration Service.

## <a name="download-and-install-the-hybrid-worker"></a>De hybride werknemer downloaden en installeren

1. Navigeer in de Azure-portal naar uw instantie van Azure Database Migration Service.

2. Selecteer **Onder Instellingen**de optie **Hybride**en selecteer vervolgens **Installer downloaden** om de hybride werknemer te downloaden.

    ![Downloaden van hybride werknemers van Azure Database Migration Service](media/quickstart-create-data-migration-service-hybrid-portal/dms-installer-download.png)

3. Haal het ZIP-bestand uit op de server die de hybride werknemer azure database migration service host.

    > [!IMPORTANT]
    > Het hybride installatieprogramma Azure Database Migration Service vereist .NET 4.7.2 of hoger. Zie de pagina [.NET Framework downloaden](https://dotnet.microsoft.com/download/dotnet-framework) om de nieuwste versies van .NET te vinden.

4. Zoek en open in de installatiemap het bestand **dmsSettings.json,** geef de **ApplicationId** en **de resourceId**op en sla het bestand op.

    ![Instellingen voor hybride werknemers van Azure Database Migration Service](media/quickstart-create-data-migration-service-hybrid-portal/dms-settings.png)

5. Genereer een certificaat dat Azure Database Migration Service kan gebruiken om de communicatie van de hybride werknemer te verifiëren met behulp van de volgende opdracht.

    ```
    <drive>:\<folder>\Install>DMSWorkerBootstrap.exe -a GenerateCert
    ```

    Er wordt een certificaat gegenereerd in de map Installeren.

    ![Azure Database Migration Service-certificaat voor hybride werknemers](media/quickstart-create-data-migration-service-hybrid-portal/dms-certificate.png)

6. Navigeer in de Azure-portal naar de App-id onder **Beheren,** selecteer **Gecertificateerd & geheimen**en selecteer **vervolgens Certificaat uploaden** om het gegenereerde openbare certificaat te selecteren.

    ![Upload van azure Database Migration Service-certificaatvoor hybride werknemers](media/quickstart-create-data-migration-service-hybrid-portal/dms-app-upload-certificate.png)

7. Installeer de hybride medewerker azure database migration service op uw on-premises server door de volgende opdracht uit te voeren:

    ```
    <drive>:\<folder>\Install>DMSWorkerBootstrap.exe -a Install -IAcceptDMSLicenseTerms -d
    ```

    > [!NOTE]
    > Wanneer u de installatieopdracht uitvoert, u ook de volgende parameters gebruiken:
    >
    > - **-TelemetryOptOut** - Stopt de werknemer van het verzenden van telemetrie, maar blijft lokaal minimaal inloggen.  De installateur stuurt nog steeds telemetrie.
    > - **-p {InstallLocation}**. Hiermee u het installatiepad wijzigen, dat standaard C:\Program Files\DatabaseMigrationServiceHybrid is.

8. Als het installatieprogramma foutloos wordt uitgevoerd, wordt in de service een onlinestatus weergegeven in Azure Database Migration Service en u uw databases migreren.

    ![Azure Database Migration Service online](media/quickstart-create-data-migration-service-hybrid-portal/dms-instance-hybrid-mode-online.png)

## <a name="uninstall-azure-database-migration-service-hybrid-mode"></a>Hybridemodus Azure Database Migration Service verwijderen

Momenteel wordt de hybride modus Azure Database Migration Service alleen ondersteund via het azure database migration service-installatieprogramma voor hybride werknemers op uw on-premises server, met behulp van de volgende opdracht:

```
<drive>:\<folder>\Install>DMSWorkerBootstrap.exe -a uninstall
```

> [!NOTE]
> Wanneer u de opdracht verwijderen uitvoert, u ook de parameter -ReuseCert gebruiken, waardoor het AdApp-cert wordt gegenereerd door de generateCert-workflow.  Dit maakt het gebruik van hetzelfde cert dat eerder is gegenereerd en geüpload.

## <a name="set-up-the-azure-database-migration-service-hybrid-worker-using-powershell"></a>De hybride werknemer azure database migratieservice instellen met PowerShell

Naast het installeren van de hybride medewerker azure database migration service via de Azure-portal, bieden we een [PowerShell-script](https://techcommunity.microsoft.com/gxcuf89792/attachments/gxcuf89792/MicrosoftDataMigration/119/1/DMS_Hybrid_Script.zip) dat u gebruiken om de installatiestappen van werknemers te automatiseren nadat u een nieuw exemplaar van Azure Database Migration Service in hybride modus hebt gemaakt. Het script:

1. Hiermee maakt u een nieuwe AdApp.
2. Downloadt de installer.
3. Voert de generateCert-workflow uit.
4. Uploadt het certificaat.
5. Hiermee voegt u de AdApp toe als bijdrager aan uw instantie Azure Database Migration Service.
6. Voert de installatiewerkstroom uit.

Dit script is bedoeld voor snelle prototypen wanneer de gebruiker al over alle benodigde machtigingen in de omgeving beschikt. Houd er rekening mee dat in uw productieomgeving de AdApp en het Cert verschillende vereisten kunnen hebben, zodat het script kan mislukken.

> [!IMPORTANT]
> In dit script wordt ervan uitgegaan dat er een bestaande instantie van Azure Database Migration Service in hybride modus is en dat het gebruikte Azure-account machtigingen heeft om AdApps in de tenant te maken en RBAC op het abonnement te wijzigen.

Vul de parameters boven aan het script in en voer het script uit vanuit een PowerShell-instantie van administrator.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [SQL Server migreren naar een Azure SQL Database beheerd exemplaar online](tutorial-sql-server-managed-instance-online.md)
> [Migreren SQL Server naar één database of gepoolde database in Azure SQL Database offline](tutorial-sql-server-to-azure-sql.md)
