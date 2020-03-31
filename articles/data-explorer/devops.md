---
title: Azure DevOps-taak voor Azure Data Explorer
description: In dit onderwerp leert u een releasepijplijn te maken en
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/05/2019
ms.openlocfilehash: 1e44a7e71858f028b798720c5505eacbfe8c2332
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472040"
---
# <a name="azure-devops-task-for-azure-data-explorer"></a>Azure DevOps-taak voor Azure Data Explorer

[Azure DevOps Services](https://azure.microsoft.com/services/devops/) biedt hulpprogramma's voor ontwikkelingssamenwerking, zoals krachtige pijplijnen, gratis private Git-repositories, configureerbare Kanban-borden en uitgebreide geautomatiseerde en continue testmogelijkheden. [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) is een Azure DevOps-mogelijkheid waarmee u CI/CD beheren om uw code te implementeren met krachtige pijplijnen die werken met elke taal, platform en cloud.
[Azure Data Explorer - Beheeropdrachten](https://marketplace.visualstudio.com/items?itemName=Azure-Kusto.PublishToADX) is de Azure Pipelines-taak waarmee u releasepijplijnen maken en uw databasewijzigingen implementeren in uw Azure Data Explorer-databases. Het is gratis beschikbaar in de [Visual Studio Marketplace.](https://marketplace.visualstudio.com/)

In dit document wordt een eenvoudig voorbeeld beschreven van het gebruik van de taak **Azure Data Explorer – Beheeropdrachten** om uw schemawijzigingen in uw database te implementeren. Raadpleeg Azure [DevOps-documentatie](/azure/devops/user-guide/what-is-azure-devops?view=azure-devops#vsts)voor volledige CI/CD-pijplijnen.

## <a name="prerequisites"></a>Vereisten

* Als u geen Azure-abonnement hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/) voordat u begint.
* Azure Data Explorer-clusterinstelling:
    * Een [Azure Data Explorer-cluster en -database](/azure/data-explorer/create-cluster-database-portal)
    * Azure AD-app (Azure AD) maken door [een Azure AD-toepassing in te richten](/azure/kusto/management/access-control/how-to-provision-aad-app).
    * Geef toegang tot uw Azure AD-app in uw Azure Data Explorer-database door [azure Data Explorer-databasemachtigingen te beheren.](/azure/data-explorer/manage-database-permissions)
* Azure DevOps-installatie:
    * [Meld u aan voor een gratis organisatie](/azure/devops/user-guide/sign-up-invite-teammates?view=azure-devops)
    * [Een organisatie maken](/azure/devops/organizations/accounts/create-organization?view=azure-devops)
    * [Een project maken in Azure DevOps](/azure/devops/organizations/projects/create-project?view=azure-devops)
    * [Code met Git](/azure/devops/user-guide/code-with-git?view=azure-devops)

## <a name="create-folders"></a>Mappen maken

Maak de volgende voorbeeldmappen *(Functies*, *Beleid*, *Tabellen)* in uw Git-opslagplaats. Kopieer de bestanden vanaf [hier](https://github.com/Azure/azure-kusto-docs-samples/tree/master/DevOps_release_pipeline) in de respectievelijke mappen zoals hieronder te zien en bega de wijzigingen. De voorbeeldbestanden worden geleverd om de volgende werkstroom uit te voeren.

![Mappen maken](media/devops/create-folders.png)

> [!TIP]
> Bij het maken van uw eigen workflow raden we u aan uw code idempotent te maken. Gebruik bijvoorbeeld [.create-merge table](/azure/kusto/management/create-table-command#create-merge-table) in plaats van [.create table](/azure/kusto/management/create-table-command)en gebruik de functie [.create-or-alter](/azure/kusto/management/create-alter-function) in plaats van [.create.](/azure/kusto/management/create-function)

## <a name="create-a-release-pipeline"></a>Een release-pijplijn maken

1. Meld u aan bij uw [Azure DevOps-organisatie.](https://dev.azure.com/)
1. Selecteer **Pijplijnreleases** > **in** het linkermenu en selecteer Nieuwe **pijplijn**.

    ![Nieuwe pijplijn](media/devops/new-pipeline.png)

1. Het **venster Nieuwe releasepijplijn** wordt geopend. Selecteer op het tabblad **Pijplijnen** in het deelvenster **Een sjabloon selecteren** de optie Taak **leegmaken**.

     ![Een sjabloon selecteren](media/devops/select-template.png)

1. Selecteer **knop Werkgebied.** Voeg in het deelvenster **Werkgebied** de **naam Werkgebied**toe . Selecteer **Opslaan** om uw pijplijn op te slaan.

    ![Het podium een naam geven](media/devops/stage-name.png)

1. Selecteer **Een artefactknop toevoegen.** Selecteer **in** het deelvenster Een artefact toevoegen de opslagplaats waar uw code bestaat, vul relevante informatie in en klik op **Toevoegen**. Selecteer **Opslaan** om uw pijplijn op te slaan.

    ![Een artefact toevoegen](media/devops/add-artifact.png)

1. Selecteer **op** het tabblad Variabelen de optie **+ Toevoegen** om een variabele voor **eindpunt-URL** te maken die in de taak wordt gebruikt. Schrijf de **naam** en de **waarde** van het eindpunt. Selecteer **Opslaan** om uw pijplijn op te slaan. 

    ![Variabele maken](media/devops/create-variable.png)

    Als u uw Endpoint_URL wilt vinden, bevat de overzichtspagina van uw **Azure Data Explorer-cluster** in de Azure-portal het Azure Data Explorer-cluster URI. Bouw de URI in `https://<Azure Data Explorer cluster URI>?DatabaseName=<DBName>`de volgende indeling .  Https: https:\//kustodocs.westus.kusto.windows.net?DatabaseName=SampleDB

    ![URI azure Data Explorer-cluster](media/devops/adx-cluster-uri.png)

## <a name="create-tasks-to-deploy"></a>Taken maken om te implementeren

1. Klik op het tabblad **Pijplijn** op **1 taak, 0 taak** om taken toe te voegen. 

    ![Taken toevoegen](media/devops/add-task.png)

1. Maak drie taken om **tabellen,** **functies**en **beleid**in deze volgorde te implementeren. 

1. Selecteer **Tasks** op het **+** tabblad Taken op **agenttaak**. Zoek naar **Azure Data Explorer**. Installeer in **Marketplace**de extensie **Azure Data Explorer – Beheeropdrachten.** Selecteer vervolgens **Toevoegen** in **opdracht Azure Data Explorer uitvoeren**.

     ![Beheerdersopdrachten toevoegen](media/devops/add-admin-commands.png)

1. Klik op **Kusto Command** aan de linkerkant en werk de taak bij met de volgende informatie:
    * **Weergavenaam:** naam van de taak
    * **Bestandspad:** geef in de taak **Tabellen** */Tabellen/*.csl op omdat de tabelcreatiebestanden zich in de map *Tabel* bevinden.
    * **URL van eindpunt:** voer de `EndPoint URL`variabele in die in vorige stap is gemaakt.
    * Selecteer **Serviceeindpunt gebruiken** en selecteer **+ Nieuw**.

    ![Kusto-opdrachttaak bijwerken](media/devops/kusto-command-task.png)

1. Voer de volgende gegevens in het venster **Azure Data Explorer-serviceverbinding toevoegen:**

    |Instelling  |Voorgestelde waarde  |
    |---------|---------|
    |**Verbindingsnaam**     |    Een naam invoeren om dit eindpunt van deze service te identificeren     |
    |**Cluster-url**    |    De waarde is te vinden in het overzichtsgedeelte van uw Azure Data Explorer-cluster in de Azure-portal | 
    |**Service principal-id**    |    Voer de AAD App ID in (gemaakt als voorwaarde)     |
    |**Service principal-app-sleutel**     |    Voer de AAD-appsleutel in (gemaakt als voorwaarde)    |
    |**AAD-tenant-id**    |      Voer uw AAD-tenant in (zoals microsoft.com, contoso.com...)    |

    Schakel Het selectievakje **Alle pijplijnen toestaan om deze verbinding te gebruiken** in. Selecteer **OK**.

    ![Serviceverbinding toevoegen](media/devops/add-service-connection.png)

1. Herhaal stap 1-5 nog twee keer om bestanden uit de mappen *Functies* en *Beleid te* implementeren. Selecteer **Opslaan**. Zie op het tabblad **Taken** de drie gemaakte taken: **Tabellen implementeren,** **Functies implementeren**en Beleid **implementeren**.

    ![Alle mappen implementeren](media/devops/deploy-all-folders.png)

1. Selecteer **+ Release** > **Maak de release** om een release te maken.

    ![Een release maken](media/devops/create-release.png)

1. Controleer op het tabblad **Logboeken** of de implementatiestatus is geslaagd.

    ![Implementatie is geslaagd](media/devops/deployment-successful.png)

U hebt nu de creatie van een releasepijplijn voltooid voor de implementatie van drie taken naar preproductie.