---
title: Azure Migrate-projecten maken en beheren
description: U kunt projecten vinden, maken, beheren en verwijderen in Azure Migrate.
ms.topic: how-to
ms.date: 04/19/2020
ms.openlocfilehash: f5079ed979d98f2c6f0c654c860c6f176f366497
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81676393"
---
# <a name="create-and-manage-azure-migrate-projects"></a>Azure Migrate-projecten maken en beheren

In dit artikel wordt beschreven hoe u [Azure migrate](migrate-services-overview.md) projecten maakt, beheert en verwijdert.


## <a name="create-a-project-for-the-first-time"></a>Voor de eerste keer een project maken

De eerste keer dat u Azure Migrate instelt, maakt u een project en voegt u een hulp programma voor beoordeling of migratie toe. [Volg deze instructies](how-to-add-tool-first-time.md) om de eerste keer in te stellen.

## <a name="create-additional-projects"></a>Aanvullende projecten maken

Als u al een Azure Migrate project hebt en u een aanvullend project wilt maken, gaat u als volgt te werk:  

1. Zoek in de [open bare Azure Portal](https://portal.azure.com) of [Azure Government](https://portal.azure.us)naar **Azure migrate**.
2. Selecteer in de rechter bovenhoek op het Azure Migrate dash board > **servers** **wijzigen** .

   ![Azure Migrate project wijzigen](./media/create-manage-projects/switch-project.png)

3. Als u een nieuw project wilt maken, selecteert u **hier klikken**.

   ![Een tweede Azure Migrate-project maken](./media/create-manage-projects/create-new-project.png)


## <a name="find-a-project"></a>Een project zoeken

Zoek een project op de volgende manier:

1. Zoek in het [Azure Portal](https://portal.azure.com)naar **Azure migrate**.
2. Selecteer in het Azure Migrate Dash **Servers**Board > servers **wijzigen** in de rechter bovenhoek.

    ![Overschakelen naar een bestaand Azure Migrate project](./media/create-manage-projects/switch-project.png)

3. Selecteer het juiste abonnement en Azure Migrate project.


Als u het project hebt gemaakt in de [vorige versie](migrate-services-overview.md#azure-migrate-versions) van Azure migrate, kunt u het als volgt vinden:

1. Zoek in het [Azure Portal](https://portal.azure.com)naar **Azure migrate**.
2. Als u in de vorige versie van het dash board van Azure Migrate een project hebt gemaakt, wordt er een banner weer gegeven dat verwijst naar oudere projecten. Selecteer de banner.

    ![Toegang tot bestaande projecten](./media/create-manage-projects/access-existing-projects.png)

3. Bekijk de lijst met oude projecten.


## <a name="delete-a-project"></a>Een project verwijderen

Verwijder als volgt:

1. Open de Azure-resource groep waarin het project is gemaakt.
2. Selecteer op de pagina resource groep de optie **verborgen typen weer geven**.
3. Selecteer het migratie project dat u wilt verwijderen en de bijbehorende resources.
    - Het resource type is **micro soft. migrate/migrateprojects**.
    - Als de resource groep uitsluitend wordt gebruikt door het Azure Migrate project, kunt u de hele resource groep verwijderen.


Opmerking:

- Wanneer u verwijdert, worden zowel het project als de meta gegevens over gedetecteerde machines verwijderd.
- Als u de oudere versie van Azure Migrate gebruikt, opent u de Azure-resource groep waarin het project is gemaakt. Selecteer het migratie project dat u wilt verwijderen (het resource type is een **migratie project**).
- Als u afhankelijkheids analyse gebruikt met een Azure Log Analytics-werk ruimte:
    - Als u een Log Analytics-werk ruimte hebt gekoppeld aan het hulp programma voor Server evaluatie, wordt de werk ruimte niet automatisch verwijderd. Dezelfde Log Analytics-werk ruimte kan voor meerdere scenario's worden gebruikt.
    - Als u de werk ruimte Log Analytics wilt verwijderen, doet u dat hand matig.

### <a name="delete-a-workspace-manually"></a>Een werk ruimte hand matig verwijderen

1. Blader naar de Log Analytics werkruimte die aan het project is gekoppeld.

    - Als u het Azure migrate project nog niet hebt verwijderd, kunt u de koppeling naar de werk ruimte in **Essentials** > **Server-evaluatie**vinden.
       ![De werk](./media/create-manage-projects/loganalytics-workspace.png)ruimte La.
       
    - Als u het Azure Migrate project al hebt verwijderd, selecteert u **resource groepen** in het linkerdeel venster van de Azure Portal en zoekt u de werk ruimte.
       
2. [Volg de instructies](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace) om de werk ruimte te verwijderen.

## <a name="next-steps"></a>Volgende stappen

Voeg hulpprogram ma's voor [evaluatie](how-to-assess.md) of [migratie](how-to-migrate.md) toe om Azure migrate projecten.
