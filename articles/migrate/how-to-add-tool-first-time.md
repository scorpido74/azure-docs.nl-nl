---
title: Een hulp programma voor beoordeling/migratie toevoegen in Azure Migrate
description: Hierin wordt beschreven hoe u een Azure Migrate project maakt en een hulp programma voor evaluatie/migratie toevoegt.
ms.topic: how-to
ms.date: 04/16/2020
ms.openlocfilehash: 1294831162df973e1f87052c1520d5d95990cf8f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82901193"
---
# <a name="add-an-assessmentmigration-tool-for-the-first-time"></a>Voor de eerste keer een evaluatie-/migratiehulpprogramma toevoegen

In dit artikel wordt beschreven hoe u voor de eerste keer een hulp programma voor beoordeling of migratie toevoegt aan een [Azure migrate](migrate-overview.md) project.  
Azure Migrate biedt een centrale hub voor het bijhouden van detectie, evaluatie en migratie van uw on-premises apps en werk belastingen, en persoonlijke/open bare Cloud-Vm's naar Azure. De hub biedt Azure Migrate tools voor evaluatie en migratie, evenals andere hulpprogram ma's en ISV- [aanbiedingen](migrate-services-overview.md#isv-integration) (Independent Software Vendor). 

## <a name="check-permissions-to-create-project"></a>Controleer de machtigingen voor het maken van het project

Als u nog geen Azure Migrate project hebt gemaakt, controleert u of u de juiste machtigingen hebt.

1. Open in de Azure-portal het abonnement en selecteer **Toegangsbeheer (IAM)** .
2. In Toegang controleren, zoekt u het relevante account en klikt u hierop om machtigingen weer te geven. U moet de machtigingen Inzender of Eigenaar hebben.
    - Als u net pas een gratis Azure-account hebt gemaakt, bent u de eigenaar van uw abonnement.
    - Als u niet de eigenaar van het abonnement bent, kunt u met de eigenaar samenwerken om de rol toe te wijzen.

## <a name="create-a-project-and-add-a-tool"></a>Een project maken en een hulp programma toevoegen

Stel een nieuw Azure Migrate project in een Azure-abonnement in en voeg een hulp programma toe.

- Een Azure Migrate project wordt gebruikt voor het opslaan van de meta gegevens voor detectie, evaluatie en migratie die zijn verzameld uit de omgeving die u wilt beoordelen of migreren. 
- In een project kunt u gedetecteerde assets bijhouden en de evaluatie en migratie indelen.

1. Zoek in de Azure-portal in **Alle services** naar **Azure Migrate**.
2. Onder **Services** selecteert u **Azure Migrate**.

    ![Azure Migrate instellen](./media/how-to-add-tool-first-time/azure-migrate-search.png)

3. Klik in **Overzicht** op **Servers evalueren en migreren**.
4. Klik onder **Servers detecteren, evalueren en migreren** op **Servers evalueren en migreren**.

    ![Servers detecteren en evalueren](./media/how-to-add-tool-first-time/assess-migrate.png)

1. Klik in **Servers detecteren, evalueren en migreren** op **Hulpprogramma's toevoegen**.
2. Selecteer in **Project migreren** uw Azure-abonnement en maak een resourcegroep als u er nog geen hebt.
3. Geef in **Project Details**de project naam en geografie op waarin u het project wilt maken.  Bekijk ondersteunde geografische regio's voor [openbare](migrate-support-matrix.md#supported-geographies-public-cloud) clouds en [overheidsclouds](migrate-support-matrix.md#supported-geographies-azure-government).

    ![Een Azure Migrate-project maken](./media/how-to-add-tool-first-time/migrate-project.png)

    - De opgegeven geografie voor het project wordt alleen gebruikt om de metagegevens op te slaan die zijn verzameld van on-premises virtuele machines. U kunt een wille keurige doel regio voor de daad werkelijke migratie selecteren.
    - Als u een project binnen een bepaalde regio in een geografie wilt implementeren, gebruikt u de volgende API om een project te maken. Geef de abonnements-ID, de naam van de resource groep en de project naam op, samen met de locatie. Bekijk de geografische grafieken/regio's voor [open bare](migrate-support-matrix.md#supported-geographies-public-cloud) en [overheids Clouds](migrate-support-matrix.md#supported-geographies-azure-government).

        `PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"`   


4. Klik op **volgende**en voeg een hulp programma voor beoordeling of migratie toe.

    > [!NOTE]
    > Wanneer u een project maakt, moet u ten minste één hulp programma voor evaluatie of migratie toevoegen.

5. Voeg in **hulp programma voor beoordeling selecteren**een evaluatie hulpprogramma toe. Als u geen beoordelings programma nodig hebt, selecteert u **overs Laan een evaluatie programma toevoegen voor nu**  >  **volgende**. 
2. Voeg in **hulp programma voor migratie het selectie vakje**een migratie hulpprogramma toe zoals vereist. Als u momenteel geen hulp programma voor migratie nodig hebt, selecteert u **overs Laan nu een migratie programma toevoegen**  >  **Next**.
3. Controleer in **Evalueren en hulpprogramma's toevoegen** de instellingen en klik op **Hulpmiddelen toevoegen**.

Nadat u het project hebt gemaakt, kunt u extra hulpprogram ma's selecteren voor de evaluatie en migratie van servers en werk belastingen, data bases en web-apps.

## <a name="create-additional-projects"></a>Aanvullende projecten maken

In sommige gevallen moet u mogelijk aanvullende Azure Migrate-projecten maken. Als u bijvoorbeeld data centers in verschillende geografische grafieken hebt, of als u meta gegevens in een andere geografie wilt opslaan. Maak als volgt een extra project:

1. Klik in het huidige Azure Migrate project op **servers** of **data bases**.
2. Klik in de rechter bovenhoek op **wijzigen** naast de huidige project naam.
3. Selecteer in **instellingen** **de optie Klik hier om een nieuw project te maken**.
4. Maak een nieuw project en voeg een nieuw hulp programma toe zoals beschreven in de vorige procedure.

## <a name="next-steps"></a>Volgende stappen

- Aan de slag met [Azure migrate: Server evaluatie](migrate-services-overview.md#azure-migrate-server-assessment-tool)of [Azure migrate: Server migratie](migrate-services-overview.md#azure-migrate-server-migration-tool).
- Als u een ISV-hulp programma hebt toegevoegd, of als u de bewerking wilt inpakken, controleert u de [stappen](prepare-isv-movere.md) voor het koppelen van het hulp programma aan Azure Migrate.
- Meer informatie over het toevoegen van extra hulpprogram ma's voor [evaluatie](how-to-assess.md) en [migratie](how-to-migrate.md) . 
