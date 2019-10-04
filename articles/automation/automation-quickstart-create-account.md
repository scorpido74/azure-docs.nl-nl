---
title: 'Azure-quickstart: een Azure Automation-account maken | Microsoft Docs'
description: Meer informatie over hoe u een Azure Automation-account kunt maken en een runbook kunt uitvoeren.
services: automation
author: csand-msft
ms.author: csand
ms.date: 04/04/2019
ms.topic: quickstart
ms.service: automation
ms.subservice: process-automation
ms.custom: mvc
ms.openlocfilehash: 80a175aad3c692a5dfd04c7399211c58134c5164
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71937170"
---
# <a name="create-an-azure-automation-account"></a>Een Azure Automation-account maken

Azure Automation-accounts kunnen via Azure worden gemaakt. Deze methode biedt een gebruikersinterface op basis van een browser voor het maken en configureren van Automation-accounts en verwante resources. Deze quickstart laat u stapsgewijs zien hoe u een Automation-account kunt maken en een runbook in het account kunt uitvoeren.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis Azure-account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Aanmelden bij Azure op https://portal.azure.com

## <a name="create-automation-account"></a>Automation-account maken

1. Klik op de knop **Een resource maken** in de linkerbovenhoek van Azure.

1. Selecteer **deze &-beheer hulpprogramma's**en selecteer vervolgens **Automation**.

1. Voer de accountgegevens in. Kies bij **Een Uitvoeren als-account voor Azure maken**, de optie **Ja** zodat de artefacten die verificatie bij Azure vereenvoudigen, automatisch worden ingeschakeld. Het is belangrijk te weten dat bij het maken van een Automation-account de naam kan niet worden gewijzigd nadat deze is geselecteerd. *Automation-accountnamen zijn uniek per regio en resourcegroep. Namen voor Automation-Accounts die zijn verwijderd, zijn mogelijk niet onmiddellijk beschikbaar.* Met één Automation-account kunt u resources in alle regio's en abonnementen voor een bepaalde tenant beheren. Als u daarmee klaar bent, klikt u op **Maken** om met de implementatie van het Automation-account te beginnen.

    ![Gegevens over uw Automation-account invoeren op de pagina](./media/automation-quickstart-create-account/create-automation-account-portal-blade.png)  

    > [!NOTE]
    > Zie [Beschikbare producten per regio](https://azure.microsoft.com/global-infrastructure/services/?products=automation&regions=all) voor een bijgewerkte lijst met locaties waarin u een Automation-account kunt implementeren.

1. Wanneer de implementatie is voltooid, klikt u op **Alle Services**, selecteert u **Automation-accounts** en selecteert u het Automation-account dat u hebt gemaakt.

    ![Overzicht van Automation-account](./media/automation-quickstart-create-account/automation-account-overview.png)

## <a name="run-a-runbook"></a>Een runbook uitvoeren

Voer een van de zelfstudierunbooks uit.

1. Klik op **Runbooks** onder **PROCESAUTOMATISERING**. De lijst met runbooks wordt weergegeven. Verschillende zelfstudierunbooks zijn standaard ingeschakeld in het account.

    ![Lijst met runbooks in het Automation-account](./media/automation-quickstart-create-account/automation-runbooks-overview.png)

1. Selecteer het runbook **AzureAutomationTutorialScript**. Met deze actie wordt de overzichtspagina van het runbook geopend.

    ![Overzicht van het runbook](./media/automation-quickstart-create-account/automation-tutorial-script-runbook-overview.png)

1. Klik op **Starten** en klik op de pagina **Runbook starten** op **OK** om het runbook te starten.

    ![Pagina met runbooktaak](./media/automation-quickstart-create-account/automation-tutorial-script-job.png)

1. Nadat de **Taakstatus** in **In uitvoering** verandert, klikt u op **Uitvoer** of **Alle logboeken** om de uitvoer van de runbooktaak weer te geven. Voor dit zelfstudierunbook bestaat de uitvoer uit een lijst van uw Azure-resources.

## <a name="next-steps"></a>Volgende stappen

In het kader van deze quickstart hebt u een Automation-account geïmplementeerd, een runbooktaak gestart en de resultaten van een taak weergegeven. Als u meer wilt weten over Azure Automation, gaat u verder met de quickstart om een eerste runbook te maken.

> [!div class="nextstepaction"]
> [Automation-quickstart: runbook maken](./automation-quickstart-create-runbook.md)

