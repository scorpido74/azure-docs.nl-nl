---
title: Runbook-en module galerieën voor Azure Automation
description: Runbooks en modules van micro soft en de community kunnen worden geïnstalleerd en gebruikt in uw Azure Automation omgeving.  In dit artikel wordt beschreven hoe u toegang krijgt tot deze bronnen en hoe u uw runbooks bijdraagt aan de galerie.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 94910d0f42ad6b208cac54dd2826cbd2d917504b
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850717"
---
# <a name="runbook-and-module-galleries-for-azure-automation"></a>Runbook-en module galerieën voor Azure Automation

In plaats van uw eigen runbooks en modules te maken in Azure Automation, hebt u toegang tot scenario's die al zijn gemaakt door micro soft en de community.

U kunt Power shell-runbooks en- [modules](#modules-in-powershell-gallery) ophalen uit de PowerShell Gallery-en [python-Runbooks](#python-runbooks) in de Script Center-galerie. U kunt ook bijdragen aan de community door scenario's te delen die u ontwikkelt, een runbook toevoegen aan de galerie

## <a name="runbooks-in-powershell-gallery"></a>Runbooks in PowerShell Gallery

De [PowerShell Gallery](https://www.powershellgallery.com/packages) biedt verschillende Runbooks van micro soft en de community die u in azure Automation kunt importeren. Als u één wilt gebruiken, moet u een runbook downloaden uit de galerie of u kunt vanuit de galerie of vanuit uw Automation-account rechtstreeks runbooks importeren in de Azure Portal.

U kunt alleen rechtstreeks importeren vanuit de PowerShell Gallery met behulp van de Azure Portal. U kunt deze functie niet uitvoeren met Power shell.

> [!NOTE]
> U moet de inhoud van alle runbooks valideren die u van de PowerShell Gallery ontvangt en de meeste voorzichtigheid gebruiken om ze te installeren en uit te voeren in een productie omgeving.

### <a name="to-import-a-powershell-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>Een Power shell-runbook importeren vanuit de Runbook Gallery met de Azure Portal

1. Open uw Automation-account in Azure Portal.
2. Klik onder **proces automatisering**op **Runbooks galerie**
3. **Bron selecteren: PowerShell Gallery**.
4. Zoek het gewenste galerij-item en selecteer dit om de details ervan weer te geven. Aan de linkerkant kunt u aanvullende zoek parameters opgeven voor de uitgever en het type.

   ![Bladergalerie](media/automation-runbook-gallery/browse-gallery.png)

5. Klik op **bron project weer geven** om het item in het [TechNet-Script centrum](https://gallery.technet.microsoft.com/)weer te geven.
6. Als u een item wilt importeren, klikt u erop om de details ervan weer te geven en klikt u vervolgens op de knop **importeren** .

   ![Knop Importeren](media/automation-runbook-gallery/gallery-item-detail.png)

7. Wijzig desgewenst de naam van het runbook en klik vervolgens op **OK** om het runbook te importeren.
8. Het runbook wordt weer gegeven op het tabblad **Runbooks** voor het Automation-account.

### <a name="adding-a-powershell-runbook-to-the-gallery"></a>Een Power shell-runbook toevoegen aan de galerie

Micro soft raadt u aan runbooks toe te voegen aan de PowerShell Gallery die u nuttig vindt voor andere klanten. De PowerShell Gallery accepteert Power shell-modules en Power shell-scripts. U kunt een runbook toevoegen door [het te uploaden naar de PowerShell Gallery](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package).

> [!NOTE]
> Grafische runbooks worden niet ondersteund in PowerShell Gallery.

## <a name="modules-in-powershell-gallery"></a>Modules in PowerShell Gallery

Power shell-modules bevatten cmdlets die u in uw runbooks kunt gebruiken en bestaande modules die u in Azure Automation kunt installeren, zijn beschikbaar in de [PowerShell Gallery](https://www.powershellgallery.com). U kunt deze galerie starten vanuit de Azure Portal en deze rechtstreeks installeren in Azure Automation. U kunt ze ook downloaden en hand matig installeren.

### <a name="to-import-a-module-from-the-automation-module-gallery-with-the-azure-portal"></a>Een module importeren vanuit de galerie met automatiserings modules met de Azure Portal

1. Open uw Automation-account in Azure Portal.
2. Selecteer **modules** onder **gedeelde bronnen** om de lijst met modules te openen.
3. Klik op **Bladeren in Galerie** boven aan de pagina.

   ![Module galerie](media/automation-runbook-gallery/modules-blade.png)

4. Op de pagina **Blade galerie** kunt u zoeken op de volgende velden:

   * Modulenaam
   * Tags
   * Auteur
   * Cmdlet/DSC-resource naam

5. Zoek een module waarin u bent geïnteresseerd en selecteer deze om de details ervan weer te geven.

   Als u een specifieke module inzoomt, kunt u meer informatie weer geven. Deze informatie omvat een koppeling terug naar de PowerShell Gallery, eventueel vereiste afhankelijkheden en alle cmdlets of DSC-resources die de module bevat.

   ![Details van Power shell-module](media/automation-runbook-gallery/gallery-item-details-blade.png)

6. Als u de module rechtstreeks in Azure Automation wilt installeren, klikt u op de knop **importeren** .
7. Wanneer u op de knop Importeren klikt, ziet u in het deel venster **importeren** de naam van de module die u wilt importeren. Als alle afhankelijkheden zijn geïnstalleerd, wordt de knop **OK** geactiveerd. Als er afhankelijkheden ontbreken, moet u deze afhankelijkheden importeren voordat u deze module kunt importeren.
8. Klik op de pagina **importeren** op **OK** om de module te importeren. Terwijl Azure Automation een module in uw account importeert, worden de meta gegevens van de module en de cmdlets opgehaald. Deze actie kan enkele minuten duren omdat elke activiteit moet worden geëxtraheerd.
9. U ontvangt een eerste melding dat de module wordt geïmplementeerd en een andere melding wanneer deze is voltooid.
10. Nadat de module is geïmporteerd, kunt u de beschik bare activiteiten bekijken. U kunt de bijbehorende resources gebruiken in de runbooks en desired state Configuration.

> [!NOTE]
> Modules die alleen Power shell core ondersteunen, worden niet ondersteund in Azure Automation en kunnen niet worden geïmporteerd in de Azure Portal, of rechtstreeks vanuit de PowerShell Gallery worden geïmplementeerd.

## <a name="python-runbooks"></a>Python-Runbooks

Python-Runbooks zijn beschikbaar in de [Script Center-galerie](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B1%5D.Type=ProgrammingLanguage&f%5B1%5D.Value=Python&f%5B1%5D.Text=Python&sortBy=Date&username=). U kunt python-runbooks bijdragen aan de Script Center-galerie door te klikken op **een bijdrage uploaden**. Wanneer u dit doet, moet u ervoor zorgen dat u de tag **python** toevoegt wanneer u uw bijdrage uploadt.

> [!NOTE]
> Voor het uploaden van inhoud naar [Script Center](https://gallery.technet.microsoft.com/scriptcenter) zijn mini maal 100 punten vereist.

## <a name="requesting-a-runbook-or-module"></a>Een runbook of module aanvragen

U kunt aanvragen verzenden naar de stem van de [gebruiker](https://feedback.azure.com/forums/246290-azure-automation/).  Als u hulp nodig hebt bij het schrijven van een runbook of als u een vraag hebt over Power shell, plaatst u een vraag naar ons [forum](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc).

## <a name="common-solutions-available-in-the-runbook-gallery"></a>Algemene oplossingen die beschikbaar zijn in de runbook Gallery

De onderstaande lijst bevat enkele runbooks die oplossingen bieden voor veelvoorkomende scenario's. Zie [AzureAutomationTeam-profiel](https://www.powershellgallery.com/profiles/AzureAutomationTeam)voor een volledige lijst met runbooks die zijn gemaakt door het Azure Automation team.

* [Update-ModulesInAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/) : Hiermee wordt de meest recente versie van PowerShell Gallery van alle modules in een Automation-account geïmporteerd.
* [Enable-AzureDiagnostics](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/) : met dit script worden Azure Diagnostics en log Analytics geconfigureerd om Azure Automation logboeken te ontvangen met taak status en taak stromen.
* [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/) : met dit runbook wordt een extern bestand van een virtuele Windows Azure-machine gekopieerd.
* [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) : met dit runbook wordt een lokaal bestand naar een virtuele machine van Azure gekopieerd.

## <a name="next-steps"></a>Volgende stappen

* Zie [Runbook beheren in azure Automation](manage-runbooks.md) om aan de slag te gaan met runbooks.
* Zie de [Power shell-werk stroom leren](automation-powershell-workflow.md) voor informatie over de verschillen tussen de Power shell-en Power shell-werk stroom met runbooks.
* Raadpleeg de [Power shell-documenten](https://docs.microsoft.com/powershell/scripting/overview)voor meer informatie over Power shell, inclusief taal referentie-en leer modules.
