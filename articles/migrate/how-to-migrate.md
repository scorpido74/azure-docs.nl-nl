---
title: Migratiehulpprogramma's toevoegen in Azure Migreren
description: Meer informatie over het toevoegen van migratiehulpprogramma's in Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.manager: carmonm
ms.topic: article
ms.date: 11/18/2019
ms.author: raynew
ms.openlocfilehash: 6bcb65912627356215769ccc8380ee1a7404f6f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185966"
---
# <a name="add-migration-tools"></a>Migratiehulpprogramma's toevoegen

In dit artikel wordt beschreven hoe u migratiehulpprogramma's toevoegt in [Azure Migreren.](migrate-overview.md)

Azure Migrate biedt een hub met hulpprogramma's voor beoordeling en migratie naar Azure. Het bevat native tools, hulpprogramma's die worden geleverd door andere Azure-services en isv-aanbiedingen (onafhankelijke softwareleveranciers) van derden.

Als u een migratietool wilt toevoegen en nog geen Azure Migrate-project hebt ingesteld, volgt u dit [artikel](how-to-add-tool-first-time.md).



## <a name="selecting-an-isv-tool"></a>Een ISV-gereedschap selecteren

Als u kiest voor een [ISV-tool](migrate-services-overview.md#isv-integration) voor migratie, u beginnen met het verkrijgen van een licentie, of het aanmelden voor een gratis proefperiode, in overeenstemming met het ISV-beleid. In elk hulpprogramma is er een optie om verbinding te maken met Azure Migrate. Implementeer het hulpprogramma en volg de hulpprogramma-instructies en -documentatie om de gereedschapswerkruimte te verbinden met Azure Migrate. 

## <a name="select-a-migration-scenario"></a>Een migratiescenario selecteren

1. Klik in het Azure Migrate-project op **Overzicht**.
2. Selecteer het migratiescenario dat u wilt gebruiken:

    - Als u machines en workloads wilt migreren naar Azure, selecteert u **Servers beoordelen en migreren.**
    - Als u on-premises SQL-machines wilt migreren, selecteert u **Databases beoordelen en migreren.**
    - Als u on-premises web-apps wilt migreren, selecteert u **Web-apps beoordelen en migreren.**
    - Als u grote hoeveelheden on-premises gegevens wilt migreren naar Azure in de offlinemodus, selecteert u **Een gegevensvak ordenen**.

    ![Beoordelingsscenario](./media/how-to-migrate/assess-scenario.png)

## <a name="select-a-server-migration-tool"></a>Een servermigratiehulpprogramma selecteren

1. Klik **op Servers beoordelen en migreren**.
2. Als u in **Azure Migreren - Servers,** als u nog geen migratiehulpprogramma's hebt toegevoegd, selecteert u In Azure Migrate - Servers , als u nog geen migratiehulpprogramma hebt **toegevoegd,** selecteert u **Klik hier om een migratiehulpprogramma toe te voegen**. Als u al migratiehulpprogramma's hebt toegevoegd, selecteert u in **Meer migratiehulpprogramma's toevoegen**de optie **Wijzigen**.

    > [!NOTE]
    > Als u naar een ander project wilt navigeren, klikt u in **Azure Migreren - Servers**naast Details **weergeven voor een ander migratieproject**op **Klik hier**.

3. Selecteer in **Azure Migreren**het migratiehulpprogramma dat u wilt gebruiken.
    - Als u Azure Migrate Server Migration gebruikt, u migraties rechtstreeks instellen en uitvoeren in het Azure Migrate-project.
    - Als u een beoordelingstool van derden gebruikt, navigeert u naar de koppeling voor de ISV en voert u de migratie uit in overeenstemming met de instructies die ze geven.

## <a name="select-a-database-migration-tool"></a>Een databasemigratiehulpprogramma selecteren

1. Klik **op Databases beoordelen en migreren**
2. Klik **in Databases**op Gereedschappen **toevoegen**.
3. Selecteer in Een hulpprogramma toevoegen > **Migratieselecteren**het gereedschap dat u wilt gebruiken om uw database te migreren.

## <a name="select-a-web-app-migration-tool"></a>Een hulpprogramma voor het migratieen van een web-app selecteren

1. Klik **op Web-apps beoordelen en migreren**.
2. Volg de koppeling naar het hulpprogramma Migratie voor de Azure App-service. Gebruik het migratiehulpprogramma om het als nodig te hebben:

    - **Apps online beoordelen:** U apps met een openbare URL online beoordelen en migreren met behulp van de Azure App Service Migration Assistant.
    - **.NET/PHP**: Voor interne .NET- en PHP-apps u de migratieassistent downloaden en uitvoeren.

## <a name="order-an-azure-data-box"></a>Een Azure-gegevensvak bestellen

Als u grote hoeveelheden gegevens wilt migreren naar Azure, u een Azure DAta Box bestellen voor offline gegevensoverdracht.

1. Klik **op Een gegevensvak bestellen**.
2. Geef uw abonnement op in **Uw Azure-gegevensvak**selecteren. 
3. De overdracht is een import naar Azure. Geef de gegevensbron en de azure-regiobestemming voor de gegevens op.

## <a name="next-steps"></a>Volgende stappen

Probeer een migratie uit met Azure Migrate Server Migration voor [Hyper-V-](tutorial-migrate-hyper-v.md) of [VMware](tutorial-migrate-vmware.md) VM's.
