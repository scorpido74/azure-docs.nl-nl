---
title: Beoordelingshulpprogramma's toevoegen in Azure Migreren
description: Meer informatie over het toevoegen van beoordelingshulpprogramma's in Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.manager: carmonm
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 64af78abd8f82b41d4a03fbb56c96e3038cef5a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185915"
---
# <a name="add-assessment-tools"></a>Evaluatiehulpprogramma's toevoegen

In dit artikel wordt beschreven hoe u beoordelingshulpprogramma's toevoegt in [Azure Migreren.](migrate-overview.md)

Azure Migrate biedt een hub met hulpprogramma's voor beoordeling en migratie naar Azure. Het bevat Azure Migrate-hulpprogramma's, evenals andere hulpprogramma's en onafhankelijke softwareleveranciers (ISV)-aanbiedingen.

Als u een beoordelingstool wilt toevoegen en u nog geen Azure Migrate-project hebt, volgt u dit [artikel](how-to-add-tool-first-time.md).

## <a name="select-a-tool"></a>Een gereedschap selecteren

Als u een niet-Azure Migrate tool kiest voor beoordeling, begin dan met het verkrijgen van een licentie of het aanmelden voor een gratis proefversie, in overeenstemming met het gereedschapsbeleid. Hulpprogramma's hebben een optie om verbinding te maken met Azure Migrate. Volg de instructies en documentatie om het hulpprogramma te verbinden met Azure Migrate. [Meer informatie](migrate-services-overview.md) over hulpmiddelen.


## <a name="select-an-assessment-scenario"></a>Een beoordelingsscenario selecteren

1. Klik in het Azure Migrate-project op **Overzicht**.
2. Selecteer het beoordelingsscenario dat u wilt gebruiken:

    - Als u machines en workloads wilt detecteren en beoordelen voor migratie naar Azure, selecteert u **Servers beoordelen en migreren.**
    - Als u on-premises SQL-machines wilt beoordelen, selecteert u **Databases beoordelen en migreren.**
    - Als u on-premises web-apps wilt beoordelen, selecteert u **Web-apps beoordelen en migreren.**

    ![Beoordelingsscenario](./media/how-to-assess/assess-scenario.png)

## <a name="select-a-server-assessment-tool"></a>Een serverbeoordelingshulpprogramma selecteren 

1. Klik **op Servers beoordelen en migreren**.
2. Als u in **Azure Migrate - Servers**geen beoordelingstool hebt toegevoegd, selecteert u onder **Beoordelingshulpprogramma's** **klik hier om een beoordelingsprogramma toe te voegen**. Als u al beoordelingstools hebt toegevoegd, selecteert u in **Meer beoordelingstools toevoegen**de optie **Wijzigen**.

    > [!NOTE]
    > Als u naar een ander project wilt navigeren, klikt u in **Azure Migreren - Servers**naast Details **weergeven voor een ander migratieproject**op **Klik hier**.

3. Selecteer in **Azure Migrate**het beoordelingsprogramma dat u wilt gebruiken.

    - Als u Azure Migrate Server Assessment gebruikt, u beoordelingen rechtstreeks instellen, uitvoeren en weergeven in het Azure Migrate-project.
    - Als u een ander beoordelingsinstrument gebruikt, navigeert u naar de koppeling voor hun site en voert u de beoordeling uit in overeenstemming met de instructies die ze geven.


## <a name="select-a-database-assessment-tool"></a>Een databasebeoordelingstool selecteren

1. Klik **op Databases beoordelen en migreren**
2. Klik **in Databases**op Gereedschappen **toevoegen**.
3. Selecteer in Een hulpprogramma toevoegen > **Beoordelingshulpmiddel selecteren**het hulpprogramma dat u wilt gebruiken om uw database te beoordelen.

## <a name="select-a-web-app-assessment-tool"></a>Een beoordelingsprogramma voor een web-app selecteren

1. Klik **op Web-apps beoordelen en migreren**.
2. Volg de koppeling naar het hulpprogramma Migratie voor de Azure App-service. Gebruik het migratiehulpprogramma om het als nodig te hebben:

    - **Apps online beoordelen:** U apps met een openbare URL online beoordelen met behulp van de Azure App Service Migration Assistant.
    - **.NET/PHP**: Voor interne .NET- en PHP-apps u de migratieassistent downloaden en uitvoeren.



## <a name="next-steps"></a>Volgende stappen

Probeer een beoordeling uit met Azure Migrate Server Assessment voor [VMware](tutorial-prepare-vmware.md) VM's, [Hyper-V](tutorial-prepare-hyper-v.md)of [fysieke servers](tutorial-prepare-physical.md)
