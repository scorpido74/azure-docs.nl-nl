---
title: Beoordelings hulpprogramma's toevoegen in Azure Migrate
description: Meer informatie over het toevoegen van beoordelings hulpprogramma's in Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.manager: carmonm
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 64af78abd8f82b41d4a03fbb56c96e3038cef5a5
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185915"
---
# <a name="add-assessment-tools"></a>Evaluatiehulpprogramma's toevoegen

In dit artikel wordt beschreven hoe u beoordelings hulpprogramma's toevoegt in [Azure migrate](migrate-overview.md).

Azure Migrate biedt een hub van hulpprogram ma's voor de evaluatie en migratie naar Azure. Het bevat Azure Migrate-hulpprogram ma's, evenals andere hulpprogram ma's en ISV-aanbiedingen (Independent Software Vendor).

Als u een evaluatie programma wilt toevoegen en u nog geen Azure Migrate project hebt, volgt u dit [artikel](how-to-add-tool-first-time.md).

## <a name="select-a-tool"></a>Hulpprogramma selecteren

Als u een niet-Azure Migrate hulp programma voor evaluatie kiest, moet u eerst een licentie aanvragen of zich aanmelden voor een gratis proef versie, in overeenstemming met het hulp programma beleid. Hulpprogram ma's hebben een optie om verbinding te maken met Azure Migrate. Volg de instructies en documentatie om het hulp programma te verbinden met Azure Migrate. [Meer informatie](migrate-services-overview.md) over hulpprogram ma's.


## <a name="select-an-assessment-scenario"></a>Een evaluatie scenario selecteren

1. Klik in het Azure Migrate project op **overzicht**.
2. Selecteer het beoordelings scenario dat u wilt gebruiken:

    - Als u machines en werk belastingen wilt detecteren en beoordelen voor migratie naar Azure, selecteert u **servers beoordelen en migreren**.
    - Als u on-premises SQL-machines wilt beoordelen, selecteert u **data bases evalueren en migreren**.
    - Als u on-premises Web-Apps wilt beoordelen, selecteert u **Web-apps evalueren en migreren**.

    ![Beoordelings scenario](./media/how-to-assess/assess-scenario.png)

## <a name="select-a-server-assessment-tool"></a>Selecteer een hulp programma voor Server evaluatie 

1. Klik op **servers beoordelen en migreren**.
2. Als u in **Azure migrate-servers**geen evaluatie programma hebt toegevoegd, selecteert u in het gedeelte **hulpprogram ma's** **voor beoordeling de optie Klik hier om een evaluatie programma toe te voegen**. Als u al beoordelings hulpprogramma's hebt toegevoegd, selecteert u in **meer evaluatie Programma's toevoegen**de optie **wijzigen**.

    > [!NOTE]
    > Als u naar een ander project moet navigeren, klikt u in **Azure migrate-servers**naast **Details voor een ander migratie project**op **Klik hier**.

3. Selecteer in **Azure migrate**het beoordelings programma dat u wilt gebruiken.

    - Als u Azure Migrate server beoordeling gebruikt, kunt u beoordelingen rechtstreeks in het Azure Migrate project instellen, uitvoeren en weer geven.
    - Als u een ander evaluatie programma gebruikt, navigeert u naar de koppeling die is verstrekt voor de site en voert u de evaluatie uit volgens de instructies die ze bieden.


## <a name="select-a-database-assessment-tool"></a>Selecteer een hulp programma voor het evalueren van data bases

1. Klik op **data bases evalueren en migreren**
2. Klik in **data bases**op **Extra toevoegen**.
3. Selecteer in hulp programma toevoegen > een **beoordelings hulpmiddel selecteren**het hulp programma dat u wilt gebruiken om uw data base te beoordelen.

## <a name="select-a-web-app-assessment-tool"></a>Selecteer een hulp programma voor het evalueren van webtoepassingen

1. Klik op **Web-apps evalueren en migreren**.
2. Volg de koppeling naar het migratie hulpprogramma voor de Azure App Service. Gebruik het hulp programma voor migratie om het volgende te doen:

    - **Apps online beoordelen**: u kunt apps met een open bare URL online beoordelen met behulp van de Azure app Service Migration Assistant.
    - **.Net/php**: voor interne .net-en PHP-apps kunt u de migration assistant downloaden en uitvoeren.



## <a name="next-steps"></a>Volgende stappen

Probeer een evaluatie uit met behulp van Azure Migrate server evaluatie voor [VMware](tutorial-prepare-vmware.md) -Vm's, [Hyper-V](tutorial-prepare-hyper-v.md)of [fysieke servers](tutorial-prepare-physical.md)
