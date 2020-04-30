---
title: 'Azure-quickstart: een Azure Automation-account maken | Microsoft Docs'
description: Meer informatie over hoe u een Azure Automation-account kunt maken en een runbook kunt uitvoeren.
services: automation
ms.date: 04/04/2019
ms.topic: quickstart
ms.subservice: process-automation
ms.custom: mvc
ms.openlocfilehash: 7704f080b7c1878f2fa2b079a1f242c8c2cc87a9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81536978"
---
# <a name="create-an-azure-automation-account"></a>Een Azure Automation-account maken

U kunt via Azure een Azure Automation-account maken met behulp van de Azure Portal, een op een browser gebaseerde gebruikers interface waarmee u toegang hebt tot een aantal resources. Met één Automation-account kunt u bronnen beheren voor alle regio's en abonnementen voor een bepaalde Tenant. 

Deze Snelstartgids helpt u bij het maken van een Automation-account en het uitvoeren van een runbook in het account. Als u nog geen abonnement op Azure hebt, maak dan een [gratis Azure-account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

[Meld u aan bij Azure](https://portal.azure.com).

## <a name="create-automation-account"></a>Automation-account maken

1. Kies een naam voor uw Azure-account. De namen van Automation-accounts zijn uniek per regio en resource groep. Namen voor Automation-accounts die zijn verwijderd, zijn mogelijk niet onmiddellijk beschikbaar.

    > [!NOTE]
    > U kunt de account naam niet wijzigen nadat deze is ingevoerd in de gebruikers interface. 

2. Klik op de knop **een resource maken** in de linkerbovenhoek van Azure Portal.

3. Selecteer **deze &-beheer hulpprogramma's**en selecteer vervolgens **Automation**.

4. Voer de account gegevens in, met inbegrip van de geselecteerde account naam. Kies bij **Een Uitvoeren als-account voor Azure maken**, de optie **Ja** zodat de artefacten die verificatie bij Azure vereenvoudigen, automatisch worden ingeschakeld. Wanneer de gegevens zijn voltooid, klikt u op **maken** om de implementatie van het Automation-account te starten.

    ![Gegevens over uw Automation-account invoeren op de pagina](./media/automation-quickstart-create-account/create-automation-account-portal-blade.png)  

    > [!NOTE]
    > Zie [producten beschikbaar per regio](https://azure.microsoft.com/global-infrastructure/services/?products=automation&regions=all)voor een bijgewerkte lijst met locaties waarop u een Automation-account kunt implementeren.

5. Wanneer de implementatie is voltooid, klikt u op **alle services**.

6. Selecteer **Automation-accounts** en kies vervolgens het Automation-account dat u hebt gemaakt.

    ![Overzicht van Automation-account](./media/automation-quickstart-create-account/automation-account-overview.png)

## <a name="run-a-runbook"></a>Een runbook uitvoeren

Voer een van de zelfstudierunbooks uit.

1. Klik onder **proces automatisering**op **Runbooks** . De lijst met runbooks wordt weergegeven. Er zijn standaard diverse zelf studie-runbooks ingeschakeld in het account.

    ![Lijst met runbooks in het Automation-account](./media/automation-quickstart-create-account/automation-runbooks-overview.png)

1. Selecteer het runbook **AzureAutomationTutorialScript**. Met deze actie wordt de overzichtspagina van het runbook geopend.

    ![Overzicht van het runbook](./media/automation-quickstart-create-account/automation-tutorial-script-runbook-overview.png)

1. Klik op **Starten** en klik op de pagina Runbook starten op **OK** om het runbook te starten.

    ![Pagina met runbooktaak](./media/automation-quickstart-create-account/automation-tutorial-script-job.png)

1. Nadat de taak status wordt `Running`weer gegeven, klikt u op **uitvoer** of **alle logboeken** om de uitvoer van de runbook-taak te bekijken. Voor dit zelfstudierunbook bestaat de uitvoer uit een lijst van uw Azure-resources.

## <a name="next-steps"></a>Volgende stappen

In het kader van deze quickstart hebt u een Automation-account geïmplementeerd, een runbooktaak gestart en de resultaten van een taak weergegeven. Als u meer wilt weten over Azure Automation, gaat u verder met de quickstart om een eerste runbook te maken.

> [!div class="nextstepaction"]
> [Automation-quickstart: runbook maken](./automation-quickstart-create-runbook.md)

