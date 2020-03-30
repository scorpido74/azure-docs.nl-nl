---
title: 'Snelstart: een instantie maken met de Azure-portal'
titleSuffix: Azure Database Migration Service
description: Gebruik de Azure-portal om een instantie van Azure Database Migration Service te maken.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: quickstart
ms.date: 01/08/2020
ms.openlocfilehash: efbf7d9db91b8c23151ca4337bd4d58f814a7096
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78254982"
---
# <a name="quickstart-create-an-instance-of-the-azure-database-migration-service-by-using-the-azure-portal"></a>Snelstart: Een Azure Database Migration Service-exemplaar maken met behulp van de Azure-portal

In deze Quickstart gebruikt u de Azure-portal om een exemplaar van Azure Database Migration Service te maken.  Nadat u de service hebt gemaakt, u deze gebruiken om gegevens van SQL Server on-premises naar Azure SQL Database te migreren.

Als u geen Azure-abonnement hebt, maakt u een [gratis](https://azure.microsoft.com/free/) account voordat u begint.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Open de webbrowser, navigeer naar [Microsoft Azure Portal](https://portal.azure.com/), en voer vervolgens uw referenties in om u aan te melden bij de portal.

De standaardweergave is uw service-dashboard.

## <a name="register-the-resource-provider"></a>De resourceprovider registreren

Registreer de Microsoft.DataMigration-resourceprovider voordat u uw eerste Database Migration Service-exemplaar maakt.

1. Selecteer in Azure Portal de optie **Alle services** en vervolgens **Abonnementen**.

2. Selecteer het abonnement waarin u de instantie van Azure Database Migration Service wilt maken en selecteer vervolgens **Resourceproviders**.

3. Zoek naar migratie en selecteer Vervolgens rechts van **Microsoft.DataMigration**de optie **Register**.

    ![Resourceprovider registreren](media/quickstart-create-data-migration-service-portal/dms-register-provider.png)

## <a name="create-an-instance-of-the-service"></a>Een exemplaar van de service maken

1. Selecteer +**Een resource maken** om een instantie van Azure Database Migration Service te maken.

2. Zoek in de marketplace naar 'migration', selecteer **Azure Database Migration Service** en selecteer vervolgens in het scherm **Azure Database Migration Service ** de optie **Maken**.

3. Ga in het scherm **Migratieservice maken** als volgt te werk:

    - Kies een **servicenaam** die gedenkwaardig en uniek is om uw exemplaar van Azure Database Migration Service te identificeren.
    - Selecteer het Azure-**abonnement** waarin u het exemplaar wilt maken.
    - Selecteer een bestaande **Resourcegroep** of maak een nieuwe.
    - Kies de **locatie** die zich het dichtst bij uw bron- of doelserver bevindt.
    - Selecteer een bestaand **virtueel netwerk** of maak er een.

        Het virtuele netwerk biedt Azure Database Migration Service toegang tot de brondatabase en doelomgeving.

        Zie het artikel Een virtueel netwerk maken met de [Azure-portal](https://aka.ms/vnet)voor meer informatie over het maken van een virtueel netwerk in de Azure-portal.

    - Selecteer Basis: 1 vCore als **prijscategorie**.

        ![Migratieservice maken](media/quickstart-create-data-migration-service-portal/dms-create-service1.png)

4. Selecteer **Maken**.

    Na enkele ogenblikken wordt uw exemplaar van de Azure Database Migration-service gemaakt en klaar voor gebruik. Azure Database Migration Service wordt weergegeven zoals weergegeven in de volgende afbeelding:

    ![Migratieservice gemaakt](media/quickstart-create-data-migration-service-portal/dms-service-created.png)

## <a name="clean-up-resources"></a>Resources opschonen

Alle resources die u in deze QuickStart hebt gemaakt, kunt u verwijderen door de [Azure-resourcegroep](../azure-resource-manager/management/overview.md) te verwijderen. U verwijdert de resourcegroep door te navigeren naar het Azure Database Migration Service-exemplaar dat u hebt gemaakt. Selecteer bij **Resourcegroep** de naam van de resourcegroep en selecteer vervolgens **Resourcegroep verwijderen**. Door deze actie worden alle items in de resourcegroep verwijderd, evenals de groep zelf.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een on-premises SQL-server migreren naar Azure SQL Database](tutorial-sql-server-to-azure-sql.md)