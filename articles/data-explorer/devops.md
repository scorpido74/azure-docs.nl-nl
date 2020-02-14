---
title: Azure DevOps-taak voor Azure Data Explorer
description: In dit onderwerp leert u hoe u een release pijplijn maakt en implementeert
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/05/2019
ms.openlocfilehash: 6394d7149bd4e80f0a17a59a6259eedf4c806fd4
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77188178"
---
# <a name="azure-devops-task-for-azure-data-explorer"></a>Azure DevOps-taak voor Azure Data Explorer

[Azure DevOps Services](https://azure.microsoft.com/services/devops/) biedt ontwikkel samenwerkings hulpmiddelen, zoals pijp lijnen met hoge prestaties, gratis persoonlijke Git-opslag plaatsen, Configureer bare Kanban boards en uitgebreide geautomatiseerde en continue test mogelijkheden. [Azure-pijp lijnen](https://azure.microsoft.com/services/devops/pipelines/) is een Azure DevOps-mogelijkheid waarmee u CI/cd kunt beheren om uw code te implementeren met hoogwaardige pijp lijnen die geschikt zijn voor elke taal, elk platform en elke Cloud.
[Azure Data Explorer-beheer opdrachten](https://marketplace.visualstudio.com/items?itemName=Azure-Kusto.PublishToADX) is de Azure pipelines-taak waarmee u release pijplijnen kunt maken en de wijzigingen in uw data base kunt implementeren in uw Azure Data Explorer-data bases. Het is gratis beschikbaar in [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

In dit document wordt een eenvoudig voor beeld van het gebruik van de taak **Azure Data Explorer-beheer opdrachten** beschreven om uw schema wijzigingen te implementeren in uw data base. Raadpleeg de [documentatie van Azure DevOps](/azure/devops/user-guide/what-is-azure-devops?view=azure-devops#vsts)voor de volledige CI/cd-pijp lijnen.

## <a name="prerequisites"></a>Vereisten

* Als u nog geen abonnement op Azure hebt, maak dan een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.
* Setup van Azure Data Explorer-cluster:
    * Een [Azure Data Explorer-cluster en-data base](/azure/data-explorer/create-cluster-database-portal)
    * Maak Azure Active Directory-app (Azure AD) door [een Azure AD-toepassing](/azure/kusto/management/access-control/how-to-provision-aad-app)in te richten.
    * Verleen toegang tot uw Azure AD-app in uw Azure Data Explorer-data base door [azure Data Explorer-database machtigingen te beheren](/azure/data-explorer/manage-database-permissions).
* Setup van Azure DevOps:
    * [Meld u aan voor een gratis organisatie](/azure/devops/user-guide/sign-up-invite-teammates?view=azure-devops)
    * [Een organisatie maken](/azure/devops/organizations/accounts/create-organization?view=azure-devops)
    * [Een project maken in azure DevOps](/azure/devops/organizations/projects/create-project?view=azure-devops)
    * [Code met git](/azure/devops/user-guide/code-with-git?view=azure-devops)

## <a name="create-folders"></a>Mappen maken

Maak de volgende voorbeeld mappen (*functies*, *beleids regels*, *tabellen*) in uw Git-opslag plaats. Kopieer de bestanden van [hier](https://github.com/Azure/azure-kusto-docs-samples/tree/master/DevOps_release_pipeline) naar de respectieve mappen zoals hieronder wordt weer gegeven en voer de wijzigingen door. De voorbeeld bestanden worden gegeven om de volgende werk stroom uit te voeren.

![Mappen maken](media/devops/create-folders.png)

> [!TIP]
> Wanneer u uw eigen werk stroom maakt, wordt u aangeraden om uw code idempotent te maken. Gebruik bijvoorbeeld [. Create-merge-tabel](/azure/kusto/management/create-table-command#create-merge-table) in plaats van [. Create](/azure/kusto/management/create-table-command)- [of-ALTER](/azure/kusto/management/functions#create-or-alter-function) -functie in plaats van [. Create](/azure/kusto/management/functions#create-function) -functie.

## <a name="create-a-release-pipeline"></a>Een release-pijplijn maken

1. Meld u aan bij uw [Azure DevOps-organisatie](https://dev.azure.com/).
1. Selecteer **pijp lijnen** > **releases** uit het menu links en selecteer **nieuwe pijp lijn**.

    ![Nieuwe pijplijn](media/devops/new-pipeline.png)

1. Het venster **nieuwe release pijplijn** wordt geopend. Selecteer op het tabblad **pijp lijnen** in het deel venster **sjabloon selecteren** de optie **lege taak**.

     ![Een sjabloon selecteren](media/devops/select-template.png)

1. Knop **fase** selecteren. Voeg in het deel venster **fase** de naam van het **stadium**toe. Selecteer **Opslaan** om uw pijp lijn op te slaan.

    ![Het stadium een naam](media/devops/stage-name.png)

1. Selecteer knop **een artefact toevoegen** . In het deel venster **een artefact toevoegen** selecteert u de opslag plaats waar uw code bestaat, vult u de relevante gegevens in en klikt u op **toevoegen**. Selecteer **Opslaan** om uw pijp lijn op te slaan.

    ![Een artefact toevoegen](media/devops/add-artifact.png)

1. Op het tabblad **variabelen** selecteert u **+ toevoegen** om een variabele voor **eind punt-URL** te maken die wordt gebruikt in de taak. Schrijf de **naam** en de **waarde** van het eind punt. Selecteer **Opslaan** om uw pijp lijn op te slaan. 

    ![Variabele maken](media/devops/create-variable.png)

    De overzichts pagina van uw **azure Data Explorer-cluster** in de Azure Portal bevat de cluster-URI van Azure Data Explorer om uw Endpoint_URL te vinden. Maak de URI in de volgende indeling `https://<Azure Data Explorer cluster URI>?DatabaseName=<DBName>`.  Bijvoorbeeld https:\//kustodocs.westus.kusto.Windows.net? DATABASENAME = SampleDB

    ![URI van Azure Data Explorer cluster](media/devops/adx-cluster-uri.png)

## <a name="create-tasks-to-deploy"></a>Te implementeren taken maken

1. Klik op het tabblad **pijp lijn** op **1 taak, 0 taak** om taken toe te voegen. 

    ![Taken toevoegen](media/devops/add-task.png)

1. Maak in deze volg orde drie taken om **tabellen**, **functies**en **beleid**te implementeren. 

1. Selecteer **+** op **Agent taak**op het tabblad **taken** . Zoek naar **Azure Data Explorer**. Installeer de extensie **Azure Data Explorer-beheer opdrachten** in **Marketplace**. Selecteer vervolgens **toevoegen** in **uitvoeren van Azure-Data Explorer opdracht**.

     ![Beheer opdrachten toevoegen](media/devops/add-admin-commands.png)

1. Klik aan de linkerkant op de **opdracht Kusto** en werk de taak bij met de volgende gegevens:
    * **Weergave naam**: naam van de taak
    * **Bestandspad: Geef**in de **taak tabellen** */Tables/* . CSL op, omdat de tabel voor het maken van bestanden zich in de map *Table* bevindt.
    * **Eind punt-URL**: voer de `EndPoint URL`variabele in die u in de vorige stap hebt gemaakt.
    * Selecteer **service-eind punt gebruiken** en selecteer **+ Nieuw**.

    ![Opdracht taak Kusto bijwerken](media/devops/kusto-command-task.png)

1. Voer de volgende informatie uit in het venster **Azure Data Explorer service-verbinding toevoegen** :

    |Instelling  |Voorgestelde waarde  |
    |---------|---------|
    |**Verbindingsnaam**     |    Voer een naam in om dit service-eind punt aan te duiden     |
    |**Cluster-URL**    |    U vindt de waarde in het gedeelte Overzicht van uw Azure Data Explorer-cluster in de Azure Portal | 
    |**Service-Principal-id**    |    Voer de ID van de AAD-app in (gemaakt als vereiste)     |
    |**App-sleutel van Service-Principal**     |    Voer de sleutel voor de AAD-app in (gemaakt als vereiste)    |
    |**AAD-Tenant-id**    |      Voer uw AAD-Tenant in (zoals microsoft.com, contoso.com...)    |

    Schakel **het selectie vakje alle pijp lijnen toestaan om deze verbinding te gebruiken in** . Selecteer **OK**.

    ![Service verbinding toevoegen](media/devops/add-service-connection.png)

1. Herhaal stap 1-5 nog twee keer om bestanden te implementeren vanuit de mappen *functies* en *beleid* . Selecteer **Opslaan**. Ga naar het tabblad **taken** en Bekijk de drie taken die zijn gemaakt: **tabellen implementeren**, **functies implementeren**en **beleid implementeren**.

    ![Alle mappen implementeren](media/devops/deploy-all-folders.png)

1. Selecteer **+ release** > **Create release** om een release te maken.

    ![Een release maken](media/devops/create-release.png)

1. Controleer op het tabblad **Logboeken** of de implementatie status is geslaagd.

    ![De implementatie is voltooid](media/devops/deployment-successful.png)

U bent nu klaar met het maken van een release pijplijn voor de implementatie van drie taken naar de voorafgaande productie.