---
title: 'Azure-quickstart: een Azure Automation-account maken | Microsoft Docs'
description: Meer informatie over hoe u een Azure Automation-account kunt maken en een runbook kunt uitvoeren.
services: automation
ms.date: 04/04/2019
ms.topic: quickstart
ms.subservice: process-automation
ms.custom: mvc
ms.openlocfilehash: 7704f080b7c1878f2fa2b079a1f242c8c2cc87a9
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536978"
---
# <a name="create-an-azure-automation-account"></a>Een Azure Automation-account maken

U een Azure Automation-account maken via Azure, met behulp van de Azure-portal, een browsergebaseerde gebruikersinterface die toegang biedt tot een aantal bronnen. Eén Automation-account kan resources beheren in alle regio's en abonnementen voor een bepaalde tenant. 

Met deze quickstart u een Automatiseringsaccount maken en een runbook in het account uitvoeren. Als u geen Azure-abonnement hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

[Meld u aan bij Azure](https://portal.azure.com).

## <a name="create-automation-account"></a>Automation-account maken

1. Kies een naam voor uw Azure-account. Naam van automatiseringsaccount is uniek per regio en resourcegroep. Namen voor automatiseringsaccounts die zijn verwijderd, zijn mogelijk niet onmiddellijk beschikbaar.

    > [!NOTE]
    > U de accountnaam niet meer wijzigen nadat deze in de gebruikersinterface is ingevoerd. 

2. Klik **op de knop Een resource maken** in de linkerbovenhoek van de Azure-portal.

3. Selecteer **IT-& Management Tools**en selecteer Vervolgens **Automatisering**.

4. Voer de accountgegevens in, inclusief de geselecteerde accountnaam. Kies bij **Een Uitvoeren als-account voor Azure maken**, de optie **Ja** zodat de artefacten die verificatie bij Azure vereenvoudigen, automatisch worden ingeschakeld. Wanneer de informatie is voltooid, klikt u op **Maken** om de implementatie van het automatiseringsaccount te starten.

    ![Gegevens over uw Automation-account invoeren op de pagina](./media/automation-quickstart-create-account/create-automation-account-portal-blade.png)  

    > [!NOTE]
    > Zie [Producten beschikbaar per regio](https://azure.microsoft.com/global-infrastructure/services/?products=automation&regions=all)voor een bijgewerkte lijst met locaties waarop u een Automatiseringsaccount implementeren.

5. Klik op **Alle services**wanneer de implementatie is voltooid.

6. Selecteer **Automatiseringsaccounts** en kies vervolgens het automatiseringsaccount dat u hebt gemaakt.

    ![Overzicht van Automation-account](./media/automation-quickstart-create-account/automation-account-overview.png)

## <a name="run-a-runbook"></a>Een runbook uitvoeren

Voer een van de zelfstudierunbooks uit.

1. Klik **op Runbooks** onder **Procesautomatisering**. De lijst met runbooks wordt weergegeven. Standaard zijn er verschillende zelfstudierunboeken ingeschakeld in het account.

    ![Lijst met runbooks in het Automation-account](./media/automation-quickstart-create-account/automation-runbooks-overview.png)

1. Selecteer het runbook **AzureAutomationTutorialScript**. Met deze actie wordt de overzichtspagina van het runbook geopend.

    ![Overzicht van het runbook](./media/automation-quickstart-create-account/automation-tutorial-script-runbook-overview.png)

1. Klik op **Starten** en klik op de pagina Runbook starten op **OK** om het runbook te starten.

    ![Pagina met runbooktaak](./media/automation-quickstart-create-account/automation-tutorial-script-job.png)

1. Nadat de taakstatus is weergegeven, `Running`klikt u op **Uitvoer** of Alle **logboeken** om de uitvoer van de runbook-taak weer te geven. Voor dit zelfstudierunbook bestaat de uitvoer uit een lijst van uw Azure-resources.

## <a name="next-steps"></a>Volgende stappen

In het kader van deze quickstart hebt u een Automation-account geïmplementeerd, een runbooktaak gestart en de resultaten van een taak weergegeven. Als u meer wilt weten over Azure Automation, gaat u verder met de quickstart om een eerste runbook te maken.

> [!div class="nextstepaction"]
> [Automation-quickstart: runbook maken](./automation-quickstart-create-runbook.md)

