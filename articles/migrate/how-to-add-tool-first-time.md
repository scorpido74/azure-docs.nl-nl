---
title: Een beoordelings-/migratiehulpprogramma toevoegen in Azure Migreren
description: Beschrijft hoe u een Azure Migrate-project maakt en een beoordelings-/migratietool toevoegt.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 319d97d96bd054aed90079777e2ff83d0e308e5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185942"
---
# <a name="add-an-assessmentmigration-tool-for-the-first-time"></a>Voor de eerste keer een evaluatie-/migratiehulpprogramma toevoegen

In dit artikel wordt beschreven hoe u voor het eerst een beoordelings- of migratietool toevoegt aan een [Azure Migrate-project.](migrate-overview.md)  
Azure Migrate biedt een centrale hub om detectie, beoordeling en migratie van uw on-premises apps en workloads en private/public cloud VM's naar Azure bij te houden. De hub biedt Azure Migrate-tools voor beoordeling en migratie, evenals andere tools en onafhankelijke softwareleveranciers [(ISV)-aanbiedingen.](migrate-services-overview.md#isv-integration) 

## <a name="create-a-project-and-add-a-tool"></a>Een project maken en een hulpprogramma toevoegen

Stel een nieuw Azure Migrate-project in een Azure-abonnement in en voeg een hulpprogramma toe.

- Een Azure Migrate-project wordt gebruikt voor het opslaan van detectie-, beoordelings- en migratiemetagegevens die zijn verzameld vanuit de omgeving die u beoordeelt of migreert. 
- In een project u gedetecteerde assets bijhouden en beoordeling en migratie orkestreren.

1. Zoek in de Azure-portal in **Alle services** naar **Azure Migrate**.
2. Onder **Services** selecteert u **Azure Migrate**.

    ![Azure-migreren instellen](./media/how-to-add-tool-first-time/azure-migrate-search.png)

3. Klik in **Overzicht** op **Servers evalueren en migreren**.
4. Klik **onder Servers ontdekken, beoordelen en migreren**op Servers beoordelen en **migreren.**

    ![Servers ontdekken en beoordelen](./media/how-to-add-tool-first-time/assess-migrate.png)

1. Klik in **Servers detecteren, evalueren en migreren** op **Hulpprogramma's toevoegen**.
2. Selecteer in **Project migreren** uw Azure-abonnement en maak een resourcegroep als u er nog geen hebt.
3. Geef in **Projectdetails**de projectnaam en geografie op waarin u het project wilt maken. 

    ![Een Azure-migratieproject maken](./media/how-to-add-tool-first-time/migrate-project.png)

    U een Azure Migrate-project maken in een van deze regio's.

   **Geografie** | **Opslaglocatieregio**
    --- | ---
    Azië   | Zuidoost-Azië of Oost-Azië
    Europa | Europa - noord of Europa - west
    Japan  | Japan Oost of Japan West
    Verenigd Koninkrijk | Verenigd Koninkrijk Zuid of UK West
    Verenigde Staten | Centraal VS of West US 2
    Canada | Canada - midden
    India  | India Centraal of India Zuid
    Australië | Australië Zuidoost

    De opgegeven geografie voor het project wordt alleen gebruikt om de metagegevens op te slaan die zijn verzameld van on-premises virtuele machines. U elk doelgebied selecteren voor de werkelijke migratie.

    Als u een specifiek gebied binnen een geografie wilt opgeven voor het implementeren van het migratieproject en de bijbehorende resources (beleidsbeperkingen in uw abonnement kunnen het implementeren van Azure-resources alleen in een specifieke Azure-regio toestaan), u de onderstaande API gebruiken om een migratieproject maken. Geef de abonnements-id, de naam van de resourcegroep, Projectnaam migreren samen met locatie op (een van de Azure-regio's die worden vermeld in de tabel waar Azure Migreren is geïmplementeerd.)

    `PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"`   


4. Klik **op Volgende**en voeg een beoordelings- of migratietool toe.

    > [!NOTE]
    > Wanneer u een project maakt, moet u ten minste één beoordelings- of migratietool toevoegen.

5. Voeg in **Het beoordelingsprogramma Selecteren**een beoordelingstool toe. Als u geen beoordelingstool nodig hebt, selecteert u **Het toevoegen van een beoordelingstool overslaan voor nu** > **Volgende**. 
2. Voeg in **Het hulpprogramma Migratie selecteren**naar behoefte een migratiegereedschap toe. Als u nu geen migratietool nodig hebt, selecteert u **Het toevoegen van een migratietool overslaan voor nu** > **Volgende**.
3. Bekijk in **Controle + hulpprogramma's de**instellingen en klik op Hulpmiddelen **toevoegen**.

Na het maken van het project u aanvullende hulpprogramma's selecteren voor beoordeling en migratie van servers en workloads, databases en web-apps.

## <a name="create-additional-projects"></a>Extra projecten maken

In sommige omstandigheden moet u mogelijk aanvullende Azure Migrate-projecten maken. Bijvoorbeeld als u datacenters in verschillende regio's hebt of als u metagegevens in een andere geografie moet opslaan. Maak als volgt een extra project:

1. Klik in het huidige Azure Migrate-project op **Servers** of **Databases**.
2. Klik in de rechterbovenhoek op **Wijzigen** naast de huidige projectnaam.
3. Selecteer in **Instellingen** **Klik hier om een nieuw project te maken**.
4. Maak een nieuw project en voeg een nieuw hulpmiddel toe zoals beschreven in de vorige procedure.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het toevoegen van aanvullende [beoordelings-](how-to-assess.md) en [migratietools.](how-to-migrate.md) 
