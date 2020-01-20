---
title: 'Snelstartgids: een hybride modus-instantie met Azure Portal maken'
titleSuffix: Azure Database Migration Service
description: Gebruik de Azure Portal om een instantie van Azure Database Migration Service in de hybride modus te maken.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: quickstart
ms.date: 01/21/2019
ms.openlocfilehash: 81039aa4aa37f9587db887beadcdb6d263ec6a74
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76274816"
---
# <a name="quickstart-create-a-hybrid-mode-instance-with-azure-portal--azure-database-migration-service"></a>Quick Start: een hybride modus-exemplaar maken met Azure Portal & Azure Database Migration Service

Azure Database Migration Service hybride modus beheert database migraties met behulp van een migratie medewerker die on-premises wordt gehost, samen met een exemplaar van Azure Database Migration Service dat in de Cloud wordt uitgevoerd. De hybride modus is vooral nuttig voor scenario's waarin een gebrek aan site-naar-site-connectiviteit tussen het on-premises netwerk en Azure of een beperkte band breedte voor site-naar-site-verbinding is.

>[!NOTE]
>Op dit moment worden Azure Database Migration Service die worden uitgevoerd in de hybride modus SQL Server-migraties ondersteund:
>
>- Azure SQL Database beheerde instantie met bijna geen downtime (online).
>- Azure SQL Database afzonderlijke Data Base met enkele downtime (offline).
>- MongoDb met Azure CosmosDB met bijna geen downtime (online).
>- MongoDb naar Azure CosmosDB met enige downtime (offline).

In deze Quick Start gebruikt u de Azure Portal om een instantie van Azure Database Migration Service in de hybride modus te maken. Daarna kunt u de Hybrid worker in uw on-premises netwerk downloaden, installeren en instellen. Tijdens de preview kunt u Azure Database Migration Service hybride modus gebruiken om gegevens van een on-premises exemplaar van SQL Server naar Azure SQL Database te migreren.

> [!NOTE]
> Het installatie programma van Azure Database Migration Service hybride wordt uitgevoerd op micro soft Windows Server 2012 R2, Windows Server 2016, Windows Server 2019, en de functie 10.

> [!IMPORTANT]
> Voor het hybride installatie programma van Azure Database Migration Service is .NET 4.7.2 of hoger vereist. Zie de pagina [down load .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework) voor de nieuwste versies van .net.

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Open de webbrowser, navigeer naar [Microsoft Azure Portal](https://portal.azure.com/), en voer vervolgens uw referenties in om u aan te melden bij de portal.

De standaardweergave is uw service-dashboard.

## <a name="register-the-resource-provider"></a>De resourceprovider registreren

Registreer de resource provider micro soft. DataMigration voordat u uw eerste exemplaar van Azure Database Migration Service maakt.

1. Selecteer in de Azure Portal **abonnementen**, selecteer het abonnement waarin u het exemplaar van Azure database Migration service wilt maken en selecteer vervolgens **resource providers**.

    ![Resource provider zoeken](media/quickstart-create-data-migration-service-hybrid-portal/dms-portal-search-resource-provider.png)

2. Zoek naar migratie en selecteer rechts van **Microsoft.DataMigration** de optie **Registreren**.

    ![Resourceprovider registreren](media/quickstart-create-data-migration-service-hybrid-portal/dms-portal-register-resource-provider.png)

## <a name="create-an-instance-of-the-service"></a>Een exemplaar van de service maken

1. Selecteer +**een resource maken** om een exemplaar van Azure database Migration service te maken.

2. Zoek op de Marketplace naar ' migratie ', selecteer **Azure database Migration service**en selecteer vervolgens **maken**op het scherm **Azure database Migration service** .

3. Ga in het scherm **Migratieservice maken** als volgt te werk:

    - Kies een **service naam** die u wilt onthouden en die uniek is om uw exemplaar van Azure database Migration service te identificeren.
    - Selecteer het Azure-**abonnement** waarin u het exemplaar wilt maken.
    - Selecteer een bestaande **Resourcegroep** of maak een nieuwe.
    - Kies de **locatie** die zich het dichtst bij uw bron- of doelserver bevindt.
    - Voor **Service modus**selecteert u **hybride (preview-versie)** .

         ![Migratie service maken-basis beginselen](media/quickstart-create-data-migration-service-hybrid-portal/dms-create-service-basics.png)

4. Selecteer **Controleren + maken**.

5. Bekijk op het tabblad **controleren en maken** de voor waarden, Controleer de gegevens die u hebt ontvangen en selecteer vervolgens **maken**.

    ![Migratie service maken-controleren en maken](media/quickstart-create-data-migration-service-hybrid-portal/dms-create-service-review-and-create.png)

    Na enkele ogen blikken wordt uw exemplaar van Azure Database Migration Service in hybride modus gemaakt en klaar om te worden ingesteld. Het Azure Database Migration Service-exemplaar wordt weer gegeven, zoals wordt weer gegeven in de volgende afbeelding:

    ![Azure Database Migration Service hybride modus-exemplaar](media/quickstart-create-data-migration-service-hybrid-portal/dms-instance-hybrid-mode.png)

6. Nadat de service is gemaakt, selecteert u **Eigenschappen**en kopieert u de waarde die wordt weer gegeven in het vak **resource-id** , die u gebruikt om de Azure database Migration service Hybrid worker te installeren.

    ![Eigenschappen van hybride modus Azure Database Migration Service](media/quickstart-create-data-migration-service-hybrid-portal/dms-copy-resource-id.png)

## <a name="create-azure-app-registration-id"></a>Azure-app registratie-ID maken

U moet een Azure-app registratie-ID maken die de on-premises Hybrid Worker kan gebruiken om te communiceren met Azure Database Migration Service in de Cloud.

1. Selecteer in de Azure Portal **Azure Active Directory**, selecteer **app-registraties**en selecteer vervolgens **nieuwe registratie**.
2. Geef een naam op voor de toepassing en selecteer onder **ondersteunde account typen**het type accounts dat moet worden ondersteund om op te geven wie de toepassing kan gebruiken.

    ![Toepassings register toepassing Azure Database Migration Service hybride modus](media/quickstart-create-data-migration-service-hybrid-portal/dms-register-application.png)

3. Gebruik de standaard waarden voor de **omleidings-URI (optioneel)** velden en selecteer vervolgens **registreren**.

4. Nadat de registratie van de App-ID is voltooid, noteert u de **toepassings-id (client)** die u gebruikt tijdens de installatie van de Hybrid Worker.

5. Ga in het Azure Portal naar Azure Database Migration Service, selecteer **toegangs beheer (IAM)** en selecteer vervolgens **roltoewijzing toevoegen** om de Inzender toegang aan de app-id toe te wijzen.

    ![Rol Inzender toewijzen Azure Database Migration Service hybride modus](media/quickstart-create-data-migration-service-hybrid-portal/dms-app-assign-contributor.png)

6. Selecteer **Inzender** als rol, wijs toegang toe aan **Azure AD-gebruiker of Service-Principal**en selecteer vervolgens de naam van de App-ID.

    ![Details van rol Inzender toewijzen Azure Database Migration Service hybride modus](media/quickstart-create-data-migration-service-hybrid-portal/dms-add-role-assignment.png)

7. Selecteer **Opslaan** om de roltoewijzing voor de App-ID op de Azure database Migration service resource op te slaan.

## <a name="download-and-install-the-hybrid-worker"></a>De Hybrid worker downloaden en installeren

1. Ga in het Azure Portal naar uw exemplaar van Azure Database Migration Service.

2. Selecteer **hybride**onder **instellingen**en selecteer vervolgens het **installatie programma** voor het downloaden van de Hybrid Worker.

    ![Azure Database Migration Service Hybrid worker downloaden](media/quickstart-create-data-migration-service-hybrid-portal/dms-installer-download.png)

3. Pak het ZIP-bestand uit op de server die als host fungeert voor de Azure Database Migration Service Hybrid Worker.

    > [!IMPORTANT]
    > Voor het hybride installatie programma van Azure Database Migration Service is .NET 4.7.2 of hoger vereist. Zie de pagina [down load .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework) voor de nieuwste versies van .net.

4. Zoek en open het bestand **dmsSettings. json** in de installatiemap, geef de **ApplicationId** en **resourceId**op en sla het bestand op.

    ![Instellingen voor Hybrid worker Azure Database Migration Service](media/quickstart-create-data-migration-service-hybrid-portal/dms-settings.png)

5. Genereer een certificaat dat Azure Database Migration Service kan gebruiken om de communicatie van de Hybrid worker te verifiëren met behulp van de volgende opdracht.

    ```
    <drive>:\<folder>\Install>DMSWorkerBootstrap.exe -a GenerateCert
    ```

    Er wordt een certificaat gegenereerd in de installatiemap.

    ![Azure Database Migration Service Hybrid worker-certificaat](media/quickstart-create-data-migration-service-hybrid-portal/dms-certificate.png)

6. Navigeer in het Azure Portal naar de App-ID onder **beheren**, selecteer **Certificaten & geheimen**en selecteer vervolgens **certificaat uploaden** om het open bare certificaat te selecteren dat u hebt gegenereerd.

    ![Azure Database Migration Service Hybrid worker-certificaat uploaden](media/quickstart-create-data-migration-service-hybrid-portal/dms-app-upload-certificate.png)

7. Installeer de Azure Database Migration Service Hybrid worker op uw on-premises server door de volgende opdracht uit te voeren:

    ```
    <drive>:\<folder>\Install>DMSWorkerBootstrap.exe -a Install -IAcceptDMSLicenseTerms
    ```

    > [!NOTE]
    > Wanneer u de installatie opdracht uitvoert, kunt u ook de volgende para meters gebruiken:
    >
    > - **-TelemetryOptOut** : de werk nemer stopt met het verzenden van telemetrie, maar blijft lokaal in het logboek registreren.  Het installatie programma verzendt nog steeds telemetrie.
    > - **-p {InstallLocation}** . Hiermee schakelt u het wijzigen van het installatiepad in, standaard is C:\Program Files\DatabaseMigrationServiceHybrid.

8. Als het installatie programma zonder fouten wordt uitgevoerd, wordt in de service een online status weer gegeven in Azure Database Migration Service en bent u klaar om uw data bases te migreren.

    ![Online Azure Database Migration Service](media/quickstart-create-data-migration-service-hybrid-portal/dms-instance-hybrid-mode-online.png)

## <a name="uninstall-azure-database-migration-service-hybrid-mode"></a>Azure Database Migration Service hybride modus verwijderen

Het verwijderen van Azure Database Migration Service hybride modus wordt momenteel alleen ondersteund via het installatie programma voor de Azure Database Migration Service Hybrid worker op de on-premises server, met behulp van de volgende opdracht:

```
<drive>:\<folder>\Install>DMSWorkerBootstrap.exe -a uninstall
```

> [!NOTE]
> Bij het uitvoeren van de uninstall-opdracht kunt u ook de para meter-ReuseCert gebruiken, waarmee het AdApp-certificaat wordt behouden dat door de generateCert-werk stroom is gegenereerd.  Hiermee maakt u gebruik van hetzelfde certificaat dat eerder is gegenereerd en geüpload.

## <a name="set-up-the-azure-database-migration-service-hybrid-worker-using-powershell"></a>De Azure Database Migration Service Hybrid worker instellen met behulp van Power shell

Naast de installatie van de Azure Database Migration Service Hybrid worker via de Azure Portal bieden we een [Power shell-script](https://techcommunity.microsoft.com/gxcuf89792/attachments/gxcuf89792/MicrosoftDataMigration/119/1/DMS_Hybrid_Script.zip) dat u kunt gebruiken om de installatie stappen voor de werk nemers te automatiseren nadat u een nieuw exemplaar van Azure database Migration service in de hybride modus hebt gemaakt. Het script:

1. Hiermee maakt u een nieuwe AdApp.
2. Down load het installatie programma.
3. De generateCert-werk stroom wordt uitgevoerd.
4. Hiermee uploadt u het certificaat.
5. Hiermee wordt de AdApp als inzender toegevoegd aan uw Azure Database Migration Service-exemplaar.
6. Voert de installatie werk stroom uit.

Dit script is bedoeld voor snelle prototypen wanneer de gebruiker al over de benodigde machtigingen beschikt in de omgeving. Houd er rekening mee dat de AdApp en het certificaat in uw productie omgeving verschillende vereisten kunnen hebben, zodat het script kan mislukken.

> [!IMPORTANT]
> In dit script wordt ervan uitgegaan dat er een bestaand exemplaar van Azure Database Migration Service in de hybride modus en dat het gebruikte Azure-account machtigingen heeft voor het maken van AdApps in de Tenant en voor het wijzigen van RBAC op het abonnement.

Vul de para meters boven aan het script in en voer het script uit vanuit een Power shell-instantie van Administrators.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [SQL Server naar een Azure SQL database Managed instance
> online migreren](tutorial-sql-server-managed-instance-online.md) om [SQL Server te migreren naar een enkele data base of gepoolde data base in Azure SQL database offline](tutorial-sql-server-to-azure-sql.md)
