---
title: Een hulp programma voor evaluatie/migratie voor de eerste keer in Azure Migrate toevoegen | Microsoft Docs
description: Hierin wordt beschreven hoe u een Azure Migrate project maakt en een hulp programma voor evaluatie/migratie toevoegt.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/06/2019
ms.author: raynew
ms.openlocfilehash: a4e66175b20552e632702cb2ba46d0ae6c0956d4
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720240"
---
# <a name="add-an-assessmentmigration-tool-for-the-first-time"></a>Voor de eerste keer een evaluatie-/migratiehulpprogramma toevoegen

In dit artikel wordt beschreven hoe u voor de eerste keer een hulp programma voor beoordeling of migratie toevoegt aan een [Azure migrate](migrate-overview.md) project.  
Azure Migrate biedt een centrale hub voor het bijhouden van detectie, evaluatie en migratie van uw on-premises apps en werk belastingen, en persoonlijke/open bare Cloud-Vm's naar Azure. De hub biedt Azure Migrate tools voor evaluatie en migratie, evenals andere hulpprogram ma's en ISV- [aanbiedingen](migrate-services-overview.md#isv-integration) (Independent Software Vendor). 

## <a name="create-a-project-and-add-a-tool"></a>Een project maken en een hulp programma toevoegen

Stel een nieuw Azure Migrate project in een Azure-abonnement in en voeg een hulp programma toe.

- Een Azure Migrate project wordt gebruikt voor het opslaan van de meta gegevens voor detectie, evaluatie en migratie die zijn verzameld uit de omgeving die u wilt beoordelen of migreren. 
- In een project kunt u gedetecteerde assets bijhouden en de evaluatie en migratie organiseren.

1. Zoek in de Azure-portal in **Alle services** naar **Azure Migrate**.
2. Onder **Services** selecteert u **Azure Migrate**.

    ![Azure Migrate instellen](./media/how-to-add-tool-first-time/azure-migrate-search.png)

3. Klik in **Overzicht** op **Servers evalueren en migreren**.
4. Klik onder **servers detecteren, evalueren en migreren**op **servers beoordelen en migreren**.

    ![Servers detecteren en beoordelen](./media/how-to-add-tool-first-time/assess-migrate.png)

1. Klik in **Servers detecteren, evalueren en migreren** op **Hulpprogramma's toevoegen**.
2. Selecteer in **Project migreren** uw Azure-abonnement en maak een resourcegroep als u er nog geen hebt.
3. Geef in **Project Details**de project naam en geografie op waarin u het project wilt maken. 

    ![Een Azure Migrate-project maken](./media/how-to-add-tool-first-time/migrate-project.png)

    U kunt in elk van deze geografische gebieden een Azure Migrate project maken.

   **Geografie** | **Opslag locatie regio**
    --- | ---
    Azië   | Zuidoost-Azië of Azië-oost
    Europa | Europa - noord of Europa - west
    Japan  | Japan-Oost of Japan-West
    Verenigd Koninkrijk | UK-zuid of UK-west
    Verenigde Staten | VS-midden, VS-West 2
    Canada | Canada - centraal
    India  | India centraal of India-Zuid
    Australië | Australië-Zuidoost

    De opgegeven geografie voor het project wordt alleen gebruikt om de metagegevens op te slaan die zijn verzameld van on-premises virtuele machines. U kunt een wille keurige doel regio voor de daad werkelijke migratie selecteren.

    Als u een specifieke regio binnen een geografie wilt opgeven voor het implementeren van het gemigreerde project en de bijbehorende resources (beleids beperkingen in uw abonnement kunnen de implementatie van Azure-resources alleen toestaan voor een specifieke Azure-regio), kunt u de onderstaande API gebruiken om een migratie project maken. Geef de abonnements-ID, de naam van de resource groep, de naam van het project en de locatie op (een van de Azure-regio's die worden vermeld in de tabel waarin Azure Migrate wordt geïmplementeerd.)

    `PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"`   


4. Klik op **volgende**en voeg een hulp programma voor beoordeling of migratie toe.

    > [!NOTE]
    > Wanneer u een project maakt, moet u ten minste één hulp programma voor evaluatie of migratie toevoegen.

5. Voeg in **hulp programma voor beoordeling selecteren**een evaluatie hulpprogramma toe. Als u geen beoordelings programma nodig hebt, selecteert u **overs Laan om nu een evaluatie tool toe te voegen** > **volgende**. 
2. Voeg in **hulp programma voor migratie het selectie vakje**een migratie hulpprogramma toe zoals vereist. Als u momenteel geen hulp programma voor migratie nodig hebt, selecteert u **overs Laan om nu een migratie hulpmiddel toe te voegen** > **volgende**.
3. Controleer de instellingen in **hulp middelen voor beoordeling en toevoegen**en klik op **hulp middelen toevoegen**.

Nadat u het project hebt gemaakt, kunt u extra hulp middelen selecteren voor de evaluatie en migratie van servers en werk belastingen, data bases en web-apps.

## <a name="create-additional-projects"></a>Aanvullende projecten maken

In sommige gevallen moet u mogelijk aanvullende Azure Migrate-projecten maken. Als u bijvoorbeeld data centers in verschillende geografische grafieken hebt, of als u meta gegevens in een andere geografie wilt opslaan. Maak als volgt een extra project:

1. Klik in het huidige Azure Migrate project op **servers** of **data bases**.
2. Klik in de rechter bovenhoek op **wijzigen** naast de huidige project naam.
3. Selecteer in **instellingen** **de optie Klik hier om een nieuw project te maken**.
4. Maak een nieuw project en voeg een nieuw hulp programma toe zoals beschreven in de vorige procedure.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het toevoegen van extra hulpprogram ma's voor [evaluatie](how-to-assess.md) en [migratie](how-to-migrate.md) . 
