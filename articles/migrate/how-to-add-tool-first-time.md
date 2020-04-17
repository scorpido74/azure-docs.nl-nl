---
title: Een beoordelings-/migratiehulpprogramma toevoegen in Azure Migreren
description: Beschrijft hoe u een Azure Migrate-project maakt en een beoordelings-/migratietool toevoegt.
ms.topic: how-to
ms.date: 04/16/2020
ms.openlocfilehash: 48bdea31d17ea1ddf0b983af962dce30b22d8dcf
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537726"
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
3. Geef in **Projectdetails**de projectnaam en geografie op waarin u het project wilt maken.  Bekijk ondersteunde regio's voor [publieke](migrate-support-matrix.md#supported-geographies-public-cloud) en [overheidswolken.](migrate-support-matrix.md#supported-geographies-azure-government)

    ![Een Azure-migratieproject maken](./media/how-to-add-tool-first-time/migrate-project.png)

    - De opgegeven geografie voor het project wordt alleen gebruikt om de metagegevens op te slaan die zijn verzameld van on-premises virtuele machines. U elk doelgebied selecteren voor de werkelijke migratie.
    - Als u een project binnen een bepaald gebied in een geografie moet implementeren, gebruikt u de volgende API om een project te maken. Geef de abonnements-id, de naam van de resourcegroep en de projectnaam op, samen met de locatie. Bekijk de regio's/regio's voor [publieke](migrate-support-matrix.md#supported-geographies-public-cloud) en [overheidswolken](migrate-support-matrix.md#supported-geographies-azure-government).

        `PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"`   


4. Klik **op Volgende**en voeg een beoordelings- of migratietool toe.

    > [!NOTE]
    > Wanneer u een project maakt, moet u ten minste één beoordelings- of migratietool toevoegen.

5. Voeg in **Het beoordelingsprogramma Selecteren**een beoordelingstool toe. Als u geen beoordelingstool nodig hebt, selecteert u **Het toevoegen van een beoordelingstool overslaan voor nu** > **Volgende**. 
2. Voeg in **Het hulpprogramma Migratie selecteren**naar behoefte een migratiegereedschap toe. Als u nu geen migratietool nodig hebt, selecteert u **Het toevoegen van een migratietool overslaan voor nu** > **Volgende**.
3. Bekijk in **Hulpmiddelen voor controleren + toevoegen**de instellingen en klik op Gereedschappen **toevoegen**.

Nadat u het project hebt gemaakt, u aanvullende hulpprogramma's selecteren voor beoordeling en migratie van servers en workloads, databases en web-apps.

## <a name="create-additional-projects"></a>Extra projecten maken

In sommige omstandigheden moet u mogelijk aanvullende Azure Migrate-projecten maken. Bijvoorbeeld als u datacenters in verschillende regio's hebt of als u metagegevens in een andere geografie moet opslaan. Maak als volgt een extra project:

1. Klik in het huidige Azure Migrate-project op **Servers** of **Databases**.
2. Klik in de rechterbovenhoek op **Wijzigen** naast de huidige projectnaam.
3. Selecteer in **Instellingen** **Klik hier om een nieuw project te maken**.
4. Maak een nieuw project en voeg een nieuw hulpmiddel toe zoals beschreven in de vorige procedure.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het toevoegen van aanvullende [beoordelings-](how-to-assess.md) en [migratietools.](how-to-migrate.md) 
