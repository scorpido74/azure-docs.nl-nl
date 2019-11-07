---
title: 'Snelstartgids: een Azure Database Migration Service hybride modus-exemplaar maken met behulp van de Azure Portal | Microsoft Docs'
description: De Azure Portal gebruiken om een instantie van Azure Database Migration Service in de hybride modus te maken
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: quickstart
ms.date: 11/06/2019
ms.openlocfilehash: 47c0ecb5674f821d3034fb5f165df08f176c2e93
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73646948"
---
# <a name="quickstart-create-an-instance-of-azure-database-migration-service-in-hybrid-mode-using-the-azure-portal-preview"></a>Snelstartgids: een exemplaar van Azure Database Migration Service in de hybride modus maken met behulp van de Azure Portal (preview-versie)

Azure Database Migration Service hybride modus beheert database migraties met behulp van een migratie medewerker die on-premises wordt gehost, samen met een exemplaar van Azure Database Migration Service dat in de Cloud wordt uitgevoerd. De hybride modus is vooral nuttig voor scenario's waarin een gebrek aan site-naar-site-connectiviteit tussen het on-premises netwerk en Azure of een beperkte band breedte voor site-naar-site-verbinding is.

In deze Quick Start gebruikt u de Azure Portal om een instantie van Azure Database Migration Service in de hybride modus te maken. Daarna kunt u de Hybrid worker in uw on-premises netwerk downloaden, installeren en instellen. Tijdens de preview kunt u Azure Database Migration Service hybride modus gebruiken om gegevens van een on-premises exemplaar van SQL Server naar Azure SQL Database te migreren.

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

2. Zoek in de marketplace naar 'migration', selecteer **Azure Database Migration Service** en selecteer vervolgens in het scherm **Azure Database Migration Service**  de optie **Maken**.

3. Ga in het scherm **Migratieservice maken** als volgt te werk:

    - Kies een **service naam** die u wilt onthouden en die uniek is om uw exemplaar van Azure database Migration service te identificeren.
    - Selecteer het Azure-**abonnement** waarin u het exemplaar wilt maken.
    - Selecteer een bestaande **Resourcegroep** of maak een nieuwe.
    - Kies de **locatie** die zich het dichtst bij uw bron- of doelserver bevindt.

    > [!IMPORTANT]
    > Tijdens de preview-fase wordt de hybride modus alleen ondersteund in de regio VS-Oost. Omdat de Hybrid Worker is geïnstalleerd in uw on-premises netwerk, is er weinig of geen invloed op de prestaties, zelfs als u migreert naar een doel in een andere regio.

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

4. Zoek en open het bestand **dmsSettings. json** in de installatiemap, geef de **ApplicationId** en **resourceId**op en sla het bestand op.

    ![Instellingen voor Hybrid worker Azure Database Migration Service](media/quickstart-create-data-migration-service-hybrid-portal/dms-settings.png)
 
5. Genereer een certificaat dat Azure Database Migration Service kan gebruiken om de communicatie van de Hybrid worker te verifiëren met behulp van de volgende opdracht.

    ```
    <drive>:\<folder>\Install>DMSWorkerBootstrap.exe -a GenerateCert
    ```

    Er wordt een certificaat gegenereerd in de installatiemap.

    ![Azure Database Migration Service Hybrid worker-certificaat](media/quickstart-create-data-migration-service-hybrid-portal/dms-certificate.png)

6. Navigeer in het Azure Portal naar de App-ID onder **beheren**, selecteer **Certificaten & geheimen**en selecteer vervolgens **certificaat uploaden** om het open bare certificaat te selecteren dat u zojuist hebt gegenereerd.

    ![Azure Database Migration Service Hybrid worker-certificaat uploaden](media/quickstart-create-data-migration-service-hybrid-portal/dms-app-upload-certificate.png)

7. Installeer de Azure Database Migration Service Hybrid worker op uw on-premises server door de volgende opdracht uit te voeren:

    ```
    <drive>:\<folder>\Install>DMSWorkerBootstrap.exe -a Install -IAcceptDMSLicenseTerms
    ```

8. Als het installatie programma zonder fouten wordt uitgevoerd, wordt in de service een online status weer gegeven in Azure Database Migration Service en bent u klaar om uw data bases te migreren.

    ![Online Azure Database Migration Service](media/quickstart-create-data-migration-service-hybrid-portal/dms-instance-hybrid-mode-online.png)

## <a name="uninstall-azure-database-migration-service-hybrid-mode"></a>Azure Database Migration Service hybride modus verwijderen

Het verwijderen van Azure Database Migration Service hybride modus wordt momenteel alleen ondersteund via het installatie programma voor de Azure Database Migration Service Hybrid worker op de on-premises server, met behulp van de volgende opdracht:

```
<drive>:\<folder>\Install>DMSWorkerBootstrap.exe -a uninstall
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [SQL Server naar een Azure SQL database Managed instance
> online migreren](tutorial-sql-server-managed-instance-online.md) om [SQL Server te migreren naar een enkele data base of gepoolde data base in Azure SQL database offline](tutorial-sql-server-to-azure-sql.md)
