---
title: Migratie hulpprogramma's toevoegen in Azure Migrate
description: Meer informatie over het toevoegen van hulpprogram ma's voor migratie in Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.manager: carmonm
ms.topic: article
ms.date: 11/18/2019
ms.author: raynew
ms.openlocfilehash: 6bcb65912627356215769ccc8380ee1a7404f6f7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74185966"
---
# <a name="add-migration-tools"></a>Migratiehulpprogramma's toevoegen

In dit artikel wordt beschreven hoe u migratie hulpprogramma's toevoegt in [Azure migrate](migrate-overview.md).

Azure Migrate biedt een hub van hulpprogram ma's voor de evaluatie en migratie naar Azure. Het bevat systeem eigen hulpprogram ma's, hulpprogram ma's van andere Azure-Services en ISV-aanbiedingen (Independent Software Vendor) van derden.

Als u een hulp programma voor migratie wilt toevoegen en nog geen Azure Migrate project hebt ingesteld, volgt u dit [artikel](how-to-add-tool-first-time.md).



## <a name="selecting-an-isv-tool"></a>Een ISV-hulp programma selecteren

Als u een [ISV-hulp programma](migrate-services-overview.md#isv-integration) kiest voor migratie, kunt u beginnen met het verkrijgen van een licentie of het aanmelden voor een gratis proef versie, conform het ISV-beleid. In elk hulp programma is er een optie om verbinding te maken met Azure Migrate. Implementeer het hulp programma en volg de instructies en documentatie van het hulp programma om de werk ruimte van het hulp programma te verbinden met Azure Migrate. 

## <a name="select-a-migration-scenario"></a>Een migratie scenario selecteren

1. Klik in het Azure Migrate project op **overzicht**.
2. Selecteer het migratie scenario dat u wilt gebruiken:

    - Als u machines en werk belastingen wilt migreren naar Azure, selecteert u **servers beoordelen en migreren**.
    - Als u on-premises SQL-machines wilt migreren, selecteert u **data bases evalueren en migreren**.
    - Als u on-premises Web-Apps wilt migreren, selecteert u **Web-apps evalueren en migreren**.
    - Als u grote hoeveel heden on-premises gegevens naar Azure wilt migreren in de offline modus, selecteert u **een Data Box order**.

    ![Beoordelings scenario](./media/how-to-migrate/assess-scenario.png)

## <a name="select-a-server-migration-tool"></a>Selecteer een hulp programma voor server migratie

1. Klik op **servers beoordelen en migreren**.
2. Selecteer in **Azure migrate-servers**, als u nog geen migratie hulpprogramma's hebt toegevoegd, onder **migratie Hulpprogramma's**, **hier op klikken om een hulp programma voor migratie toe te voegen**. Als u hulpprogram ma's voor migratie al hebt toegevoegd, selecteert u **wijzigen**in **meer migratie hulpprogramma's toevoegen**.

    > [!NOTE]
    > Als u naar een ander project moet navigeren, klikt u in **Azure migrate-servers**naast **Details voor een ander migratie project**op **Klik hier**.

3. Selecteer in **Azure migrate**het migratie hulpprogramma dat u wilt gebruiken.
    - Als u Azure Migrate server migratie gebruikt, kunt u migraties rechtstreeks in het Azure Migrate-project instellen en uitvoeren.
    - Als u een beoordelings programma van derden gebruikt, navigeert u naar de koppeling die u voor de ISV hebt gekregen en voert u de migratie uit volgens de instructies die ze bieden.

## <a name="select-a-database-migration-tool"></a>Selecteer een hulp programma voor database migratie

1. Klik op **data bases evalueren en migreren**
2. Klik in **data bases**op **Extra toevoegen**.
3. Selecteer in een hulp programma toevoegen > hulp **programma voor migratie selecteren**het hulp programma dat u wilt gebruiken om uw data base te migreren.

## <a name="select-a-web-app-migration-tool"></a>Een hulp programma voor migratie van web-apps selecteren

1. Klik op **Web-apps evalueren en migreren**.
2. Volg de koppeling naar het migratie hulpprogramma voor de Azure App Service. Gebruik het hulp programma voor migratie om het volgende te doen:

    - **Apps online beoordelen**: u kunt apps met een open bare URL online beoordelen en migreren met behulp van de Azure app Service Migration Assistant.
    - **.Net/php**: voor interne .net-en PHP-apps kunt u de migration assistant downloaden en uitvoeren.

## <a name="order-an-azure-data-box"></a>Een Azure Data Box best Ellen

Als u grote hoeveel heden gegevens naar Azure wilt migreren, kunt u een Azure DAta Box best Ellen voor offline gegevens overdracht.

1. Klik op **order a data Box**.
2. Geef uw abonnement op in **uw Azure data box selecteren**. 
3. De overdracht is een import bewerking naar Azure. Geef de gegevens bron en de Azure-regio bestemming voor de gegevens op.

## <a name="next-steps"></a>Volgende stappen

Probeer een migratie uit met Azure Migrate server migratie voor [Hyper-V-](tutorial-migrate-hyper-v.md) of [VMware](tutorial-migrate-vmware.md) -vm's.
