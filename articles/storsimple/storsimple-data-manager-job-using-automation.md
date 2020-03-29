---
title: Azure Automation gebruiken om een taak te starten in StorSimple Data Manager
description: Meer informatie over het gebruik van Azure Automation voor het activeren van StorSimple Data Manager-taken
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 034b4996672f0961cf31d342aa6055482f099b9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76273997"
---
# <a name="use-azure-automation-to-trigger-a-job"></a>Azure Automation gebruiken om een taak te activeren

In dit artikel wordt uitgelegd hoe u de functie gegevenstransformatie binnen de StorSimple Data Manager-service gebruiken om StorSimple-apparaatgegevens te transformeren. U een taak voor gegevenstransformatie op twee manieren starten: 

 - De .NET SDK gebruiken
 - Azure Automation-runbook gebruiken
 
In dit artikel wordt beschreven hoe u een Azure Automation-runbook maakt en deze vervolgens gebruikt om een taak voor gegevenstransformatie te starten. Ga voor meer informatie over het initiëren van gegevenstransformatie via .NET SDK naar [.NET SDK gebruiken om taken voor gegevenstransformatie te activeren.](storsimple-data-manager-dotnet-jobs.md)

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u ervoor zorgen dat u:

*   Azure PowerShell is geïnstalleerd op de clientcomputer. [Azure PowerShell downloaden](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps).
*   Een correct geconfigureerde taakdefinitie in een StorSimple Data Manager-service binnen een resourcegroep.
*   Download [`DataTransformationApp.zip`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/raw/master/Azure%20Automation%20For%20Data%20Manager/DataTransformationApp.zip) het bestand uit de GitHub-repository. 
*   Download [`Trigger-DataTransformation-Job.ps1`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Azure%20Automation%20For%20Data%20Manager/Trigger-DataTransformation-Job.ps1) script uit de GitHub repository.

## <a name="step-by-step-procedure"></a>Stapsgewijze procedure

### <a name="set-up-the-automation-account"></a>Het automatiseringsaccount instellen

1. Maak een Azure Run As-automatiseringsaccount in de Azure-portal. Ga hiervoor naar **Azure marketplace > Alles** en zoek vervolgens naar **Automatisering.** Selecteer **Automatiseringsaccounts**.

    ![Run als automatiseringsaccount maken](./media/storsimple-data-manager-job-using-automation/search-automation-account1.png)

2. Als u een nieuw automatiseringsaccount wilt toevoegen, klikt u op **+ Toevoegen**.

    ![Run als automatiseringsaccount maken](./media/storsimple-data-manager-job-using-automation/add-automation-account1.png)

3. In de **Add Automation:**

   1. Geef de **naam** van uw automatiseringsaccount.
   2. Selecteer het **abonnement dat** is gekoppeld aan uw StorSimple Data Manager-service.
   3. Maak een nieuwe resourcegroep of selecteer uit een bestaande resourcegroep.
   4. Selecteer een **locatie**.
   5. Laat de standaard **optie Run as-account maken** geselecteerd.
   6. Als u een koppeling wilt krijgen voor snelle toegang op het dashboard, schakelt u **Vastmaken aan dashboard**in. Klik **op Maken**.

      ![Run als automatiseringsaccount maken](./media/storsimple-data-manager-job-using-automation/create-automation-run-as-account.png)
    
      Nadat het automatiseringsaccount is gemaakt, wordt u hiervan op de hoogte gesteld.
    
      ![Melding voor implementatie van automatiseringsaccount](./media/storsimple-data-manager-job-using-automation/deployment-automation-account-notification1.png)

      Ga voor meer informatie naar [Een Run As-account maken](../automation/automation-create-runas-account.md).

3. Ga in het nieuw gemaakte account naar **Gedeelde bronnen > Modules** en klik op module toevoegen **.**

    ![Module importeren 1](./media/storsimple-data-manager-job-using-automation/import-module-1.png)

4. Blader vanaf uw `DataTransformationApp.zip` lokale computer naar de locatie van het bestand en selecteer en open de module. Klik op **OK** om de module te importeren.

    ![Module 2 importeren](./media/storsimple-data-manager-job-using-automation/import-module-2.png)

   Wanneer Azure Automation een module importeert naar uw account, worden metagegevens over de module geëxtraheerd. Deze bewerking kan enkele minuten duren.

   ![Module 4 importeren](./media/storsimple-data-manager-job-using-automation/import-module-4.png)

5. U ontvangt een melding dat de module wordt geïmplementeerd en een andere melding wanneer het proces is voltooid.  De status in **modules** verandert in **Beschikbaar**.

    ![Module 5 importeren](./media/storsimple-data-manager-job-using-automation/import-module-5.png)

### <a name="import-publish-and-run-automation-runbook"></a>Runbook voor automatisering importeren, publiceren en uitvoeren

Voer de volgende stappen uit om het runbook te importeren, te publiceren en uit te voeren om taakdefinitie te activeren.

1. Open uw Automation-account in Azure Portal. Ga naar **Procesautomatisering > Runbooks** en klik **op + Een runbook toevoegen**.

    ![Runbook 1 toevoegen](./media/storsimple-data-manager-job-using-automation/add-runbook-1.png)

2. Klik **in Runbook toevoegen**op Een bestaand **runbook importeren**.

3. Wijs het Azure PowerShell-scriptbestand `Trigger-DataTransformation-Job.ps1` aan voor het **Runbook-bestand**. Het type runbook wordt automatisch geselecteerd. Geef een naam en een optionele beschrijving op voor het runbook. Klik **op Maken**.

    ![Runbook 2 toevoegen](./media/storsimple-data-manager-job-using-automation/add-runbook-2.png)

4. Het nieuwe runbook wordt weergegeven in de lijst met runbooks voor het automatiseringsaccount. Selecteer en klik op dit runbook.

    ![Runbook 3 toevoegen](./media/storsimple-data-manager-job-using-automation/add-runbook-3.png)

5. Bewerk het runbook en klik op **Het deelvenster Testen.**

    ![Runbook 4 toevoegen](./media/storsimple-data-manager-job-using-automation/add-runbook-4.png)

6. Geef de parameters op, zoals de naam van uw StorSimple Data Manager-service, de bijbehorende resourcegroep en de naam van de taakdefinitie. **Start** de test. Het rapport wordt gegenereerd wanneer de run is voltooid. Ga voor meer informatie naar hoe u [een runbook testen.](../automation/automation-first-runbook-textual-powershell.md#step-3---test-the-runbook)

    ![Runbook 8 toevoegen](./media/storsimple-data-manager-job-using-automation/add-runbook-8.png)    

7. Controleer de uitvoer uit het runbook in het testvenster. Sluit indien tevreden het deelvenster. Klik **op Publiceren** en wanneer u om bevestiging wordt gevraagd, bevestig en publiceer het runbook.

    ![Runbook 6 toevoegen](./media/storsimple-data-manager-job-using-automation/add-runbook-6.png)

8. Ga terug naar **Runbooks** en selecteer het nieuw gemaakte runbook.

    ![Runbook 7 toevoegen](./media/storsimple-data-manager-job-using-automation/add-runbook-7.png)

9. **Start** het loopboek. Voer in **Runbook starten**alle parameters in. Klik op **OK** om de taak voor gegevenstransformatie in te dienen en te starten.

10. Als u de voortgang van de taak in azure-portal wilt controleren, gaat u naar **Vacatures** in uw StorSimple Data Manager-service. Selecteer en klik op de taak om de taakgegevens weer te geven.

    ![Runbook 10 toevoegen](./media/storsimple-data-manager-job-using-automation/add-runbook-10.png)

## <a name="next-steps"></a>Volgende stappen

[Gebruik de gebruikersinterface van StorSimple Data Manager om uw gegevens te transformeren.](storsimple-data-manager-ui.md)
