---
title: 'Azure-quickstart: een Azure Automation-runbook maken | Microsoft Docs'
description: Meer informatie over hoe u een Azure Automation-runbook kunt maken
services: automation
ms.date: 02/05/2019
ms.topic: quickstart
ms.subservice: process-automation
ms.custom: mvc
ms.openlocfilehash: 250f51c9f028dd55d8327259e35b82b0c392c1f6
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536995"
---
# <a name="create-an-azure-automation-runbook"></a>Een Azure Automation-runbook maken

Azure Automation-runbooks kunnen via Azure worden gemaakt. Deze methode biedt een gebruikersinterface op basis van een browser voor het maken van Automation-runbooks. Deze quickstart bevat stapsgewijze instructies voor het maken, bewerken, testen en publiceren van een Automation PowerShell-runbook.

Als u geen Azure-abonnement hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan https://portal.azure.combij Azure op .

## <a name="create-the-runbook"></a>Het runbook maken

Maak eerst een runbook. Het voorbeeldrunbook dat in deze quickstart is gemaakt, produceert standaard `Hello World` als uitvoer.

1. Open uw Automation-account.

1. Klik **op Runbooks** onder **Procesautomatisering**. De lijst met runbooks wordt weergegeven.

1. Klik boven aan de lijst op **Een runbook maken.**

1. Voer `Hello-World` voor de naam van het runboek in het veld **Naam** in en selecteer **PowerShell** voor het **veld Runbook-type.** 

   ![Gegevens over uw Automation-runbook invoeren op de pagina](./media/automation-quickstart-create-runbook/automation-create-runbook-configure.png)

1. Klik **op Maken**. Het runbook is gemaakt en de pagina PowerShell-runbook bewerken wordt geopend.

    ![PowerShell-script in de runbookeditor ontwerpen](./media/automation-quickstart-create-runbook/automation-edit-runbook-empty.png)

1. Typ of kopieer en plak de volgende code in het bewerkingsvenster. Hiermee wordt een optionele `Name` invoerparameter gemaakt `World`die wordt aangeroepen met een standaardwaarde van , en wordt een tekenreeks uitgevoerd die deze invoerwaarde gebruikt:

   ```powershell-interactive
   param
   (
       [Parameter(Mandatory=$false)]
       [String] $Name = "World"
   )

   "Hello $Name!"
   ```

1. Klik **op Opslaan** om een conceptkopie van het runbook op te slaan.

    ![PowerShell-script in de runbookeditor ontwerpen](./media/automation-quickstart-create-runbook/automation-edit-runbook.png)

## <a name="test-the-runbook"></a>Het runbook testen

Zodra het runbook is gemaakt, moet u het runbook testen om te valideren dat het werkt.

1. Klik op **Testvenster** om het testvenster te openen.

1. Voer een waarde in bij **Naam** en klik op **Starten**. De testtaak wordt gestart en de taakstatus en uitvoer worden weergegeven.

    ![Taak voor het testen van een runbook](./media/automation-quickstart-create-runbook/automation-test-runbook.png)

1. Sluit het deelvenster Testen door in de rechterbovenhoek op de **X** te klikken. Selecteer **OK** in de pop-up die wordt weergegeven.

1. Op de pagina PowerShell-runbook bewerken klikt u op **Publiceren** om het runbook als de officiële versie van het runbook in het account te publiceren.

   ![Taak voor het testen van een runbook](./media/automation-quickstart-create-runbook/automation-hello-world-runbook-job.png)

## <a name="run-the-runbook"></a>Het runbook uitvoeren

Zodra het runbook is gepubliceerd, wordt de overzichtspagina weergegeven.

1. Klik op de overzichtspagina van runbook op **Starten** om de configuratiepagina Runbook starten voor dit runbook te openen.

   ![Taak voor het testen van een runbook](./media/automation-quickstart-create-runbook/automation-hello-world-runbook-start.png)

1. Laat **Naam** leeg, zodat de standaardwaarde wordt gebruikt en klik op **OK**. De runbook-taak wordt ingediend en de pagina Taak wordt weergegeven.

   ![Taak voor het testen van een runbook](./media/automation-quickstart-create-runbook/automation-job-page.png)

1. Wanneer de taakstatus is `Running` of `Completed`klikt u op **Uitvoer** om het deelvenster Uitvoer te openen en de uitvoer van de runbook weer te geven.

   ![Taak voor het testen van een runbook](./media/automation-quickstart-create-runbook/automation-hello-world-runbook-job-output.png)

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u het runbook niet meer nodig hebt, kunt u het verwijderen. Om dit te doen, selecteert u het runbook in de runbooklijst en klikt u op **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd om een runbook te maken, bewerken, testen en publiceren, en hebt u een runbooktaak gestart. Voor meer informatie over Automation-runbooks, gaat u verder naar het artikel over de verschillende runbooktypen die u in Automation kunt maken en gebruiken.

> [!div class="nextstepaction"]
> [Procedures voor Automation: runbooktypen](./automation-runbook-types.md)
