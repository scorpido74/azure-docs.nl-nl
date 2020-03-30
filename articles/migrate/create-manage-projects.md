---
title: Azure Migrate-projecten maken en beheren
description: Projecten zoeken, maken, beheren en verwijderen in Azure Migreren.
ms.topic: how-to
ms.date: 02/17/2020
ms.openlocfilehash: a49595f0580e71048239d5c5d8f4d1a66e24fe6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269638"
---
# <a name="create-and-manage-azure-migrate-projects"></a>Azure Migrate-projecten maken en beheren

In dit artikel wordt beschreven hoe [azure-migratieprojecten](migrate-services-overview.md) kunnen worden gemaakt, beheren en verwijderd.


## <a name="create-a-project-for-the-first-time"></a>Voor het eerst een project maken

De eerste keer dat u Azure Migrate instelt, maakt u een project en voegt u een beoordelings- of migratietool toe. [Volg deze instructies](how-to-add-tool-first-time.md) om voor de eerste keer in te stellen.

## <a name="create-additional-projects"></a>Extra projecten maken

Als u al een Azure Migrate-project hebt en u een extra project wilt maken, gaat u als volgt te werk:  

1. Zoek in de [Azure-portal](https://portal.azure.com)naar **Azure Migrate**.
2. Selecteer **wijzigen** in de rechterbovenhoek in het Azure Migrate-dashboard **> servers.**

   ![Azure-project migreren wijzigen](./media/create-manage-projects/switch-project.png)

3. Als u een nieuw project wilt maken, selecteert **u hier**.

   ![Een tweede Azure Migrate-project maken](./media/create-manage-projects/create-new-project.png)


## <a name="find-a-project"></a>Een project zoeken

Zoek een project als volgt:

1. Zoek in de [Azure-portal](https://portal.azure.com)naar **Azure Migrate**.
2. Selecteer wijzigen in het Azure Migrate-dashboard > **Servers** **in** de rechterbovenhoek.

    ![Overschakelen naar een bestaand Azure Migrate-project](./media/create-manage-projects/switch-project.png)

3. Selecteer het juiste abonnement en Azure Migrate-project.


Als u het project hebt gemaakt in de [vorige versie](migrate-services-overview.md#azure-migrate-versions) van Azure Migrate, gaat u als volgt te werk:

1. Zoek in de [Azure-portal](https://portal.azure.com)naar **Azure Migrate**.
2. Als u in het azure migrate-dashboard een project in de vorige versie hebt gemaakt, wordt een banner weergegeven die verwijst naar oudere projecten. Selecteer de banner.

    ![Toegang tot bestaande projecten](./media/create-manage-projects/access-existing-projects.png)

3. Bekijk de lijst met oude projecten.


## <a name="delete-a-project"></a>Een project verwijderen

Als volgt verwijderen:

1. Open de Azure-brongroep waarin het project is gemaakt.
2. Selecteer **verborgen typen weergeven**op de pagina resourcegroep .
3. Selecteer het migratieproject dat u wilt verwijderen en de bijbehorende resources.
    - Het resourcetype is **Microsoft.Migrate/migrateprojects**.
    - Als de brongroep uitsluitend wordt gebruikt door het Azure Migrate-project, u de volledige brongroep verwijderen.


Opmerking:

- Wanneer u verwijdert, worden zowel het project als de metagegevens over gedetecteerde machines verwijderd.
- Als u de oudere versie van Azure Migrate gebruikt, opent u de Azure-brongroep waarin het project is gemaakt. Selecteer het migratieproject dat u wilt verwijderen (het resourcetype is **migratieproject).**
- Als u afhankelijkheidsanalyse gebruikt met een Azure Log Analytics-werkruimte:
    - Als u een loganalytics-werkruimte hebt gekoppeld aan het hulpprogramma Serverbeoordeling, wordt de werkruimte niet automatisch verwijderd. Dezelfde Log Analytics-werkruimte kan voor meerdere scenario's worden gebruikt.
    - Als u de werkruimte Log Analytics wilt verwijderen, moet u dat handmatig uitvoeren.

### <a name="delete-a-workspace-manually"></a>Een werkruimte handmatig verwijderen

1. Blader naar de werkruimte Log Analytics die aan het project is gekoppeld.

    - Als u het Azure Migrate-project niet hebt verwijderd, u de koppeling naar de werkruimte vinden in **Essentials** > **Server Assessment**.
       ![LA](./media/create-manage-projects/loganalytics-workspace.png)Workspace .
       
    - Als u het Azure Migrate-project al hebt verwijderd, selecteert u **Resourcegroepen** in het linkerdeelvenster van de Azure-portal en zoekt u de werkruimte.
       
2. [Volg de instructies](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace) om de werkruimte te verwijderen.

## <a name="next-steps"></a>Volgende stappen

[Beoordelings-](how-to-assess.md) of [migratiehulpprogramma's](how-to-migrate.md) toevoegen aan Azure Migrate-projecten.
