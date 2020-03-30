---
title: Runbook- en modulegalerieën voor Azure Automation
description: Runbooks en modules van Microsoft en de community zijn beschikbaar voor u om te installeren en te gebruiken in uw Azure Automation-omgeving.  In dit artikel wordt beschreven hoe u toegang hebt tot deze bronnen en uw runbooks bijdragen aan de galerie.
services: automation
ms.subservice: process-automation
ms.date: 03/20/2019
ms.topic: conceptual
ms.openlocfilehash: 52a0ab0a31600c1548283c7d899b17e497811b5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75421486"
---
# <a name="runbook-and-module-galleries-for-azure-automation"></a>Runbook- en modulegalerieën voor Azure Automation

In plaats van uw eigen runbooks en modules te maken in Azure Automation, hebt u toegang tot scenario's die al zijn gebouwd door Microsoft en de community.

U PowerShell-runbooks en [-modules](#modules-in-powershell-gallery) downloaden uit de PowerShell-galerie en [Python-runbooks](#python-runbooks) in de Script Center Gallery. U ook bijdragen aan de community door scenario's te delen die u ontwikkelt, zie Een runbook toevoegen aan de galerie

## <a name="runbooks-in-powershell-gallery"></a>Boeken uitvoeren in PowerShell-galerie

De [PowerShell Gallery](https://www.powershellgallery.com/packages) biedt een verscheidenheid aan runbooks van Microsoft en de community die u importeren in Azure Automation. Als u er een wilt gebruiken, downloadt u een runbook uit de galerie of u runbooks rechtstreeks importeren uit de galerie of vanuit uw automatiseringsaccount in de Azure-portal.

U alleen rechtstreeks importeren vanuit de PowerShell-galerie via de Azure-portal. U deze functie niet uitvoeren met PowerShell.

> [!NOTE]
> U moet de inhoud valideren van alle runbooks die u uit de PowerShell-galerie haalt en uiterst voorzichtig zijn bij het installeren en uitvoeren ervan in een productieomgeving.

### <a name="to-import-a-powershell-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>Een PowerShell-runbook importeren uit de Runbook Gallery met de Azure-portal

1. Open uw Automation-account in Azure Portal.
2. Klik **onder Procesautomatisering**op **galerie Runbooks**
3. **Selecteer Bron: PowerShell Gallery**.
4. Zoek het gewenste galerie-item en selecteer het om de details te bekijken. Aan de linkerkant u extra zoekparameters voor de uitgever invoeren en typen.

   ![Bladergalerie](media/automation-runbook-gallery/browse-gallery.png)

5. Klik op **Bronproject weergeven** om het item in het [TechNet Script Center](https://gallery.technet.microsoft.com/)weer te geven.
6. Als u een item wilt importeren, klikt u erop om de details weer te geven en klikt u op **importeren.**

   ![Knop Importeren](media/automation-runbook-gallery/gallery-item-detail.png)

7. Wijzig destijds de naam van het runbook en klik op **OK** om het runbook te importeren.
8. De runbook wordt weergegeven op het tabblad **Runbooks** voor het automatiseringsaccount.

### <a name="adding-a-powershell-runbook-to-the-gallery"></a>Een PowerShell-runbook toevoegen aan de galerie

Microsoft raadt u aan om runbooks toe te voegen aan de PowerShell Gallery waarvan u denkt dat deze nuttig zou zijn voor andere klanten. De PowerShell Gallery accepteert PowerShell-modules en PowerShell-scripts. U een runbook toevoegen door [deze te uploaden naar de PowerShell Gallery.](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package)

> [!NOTE]
> Grafische runbooks worden niet ondersteund in PowerShell Gallery.

## <a name="modules-in-powershell-gallery"></a>Modules in PowerShell Gallery

PowerShell-modules bevatten cmdlets die u gebruiken in uw runbooks en bestaande modules die u installeren in Azure Automation zijn beschikbaar in de [PowerShell Gallery.](https://www.powershellgallery.com) U deze galerie starten vanuit de Azure-portal en deze rechtstreeks installeren in Azure Automation. U ze ook downloaden en handmatig installeren.

### <a name="to-import-a-module-from-the-automation-module-gallery-with-the-azure-portal"></a>Een module importeren uit de module automatisering met de Azure-portal

1. Open uw Automation-account in Azure Portal.
2. Selecteer **Modules** onder **Gedeelde bronnen** om de lijst met modules te openen.
3. Klik boven aan de pagina **op Galerie bladeren.**

   ![Modulegalerie](media/automation-runbook-gallery/modules-blade.png)

4. Op de pagina **Bladeren in de galerie** u zoeken op de volgende velden:

   * Modulenaam
   * Tags
   * Auteur
   * Naam cmdlet/DSC-bron

5. Zoek een module waarin u geïnteresseerd bent en selecteer deze om de details te bekijken.

   Wanneer u inzoomt op een specifieke module, u meer informatie bekijken. Deze informatie bevat een koppeling terug naar de PowerShell Gallery, eventuele vereiste afhankelijkheden en alle cmdlets- of DSC-bronnen die de module bevat.

   ![PowerShell-moduledetails](media/automation-runbook-gallery/gallery-item-details-blade.png)

6. Als u de module rechtstreeks in Azure Automation wilt installeren, klikt u op de knop **Importeren.**
7. Wanneer u op de knop Importeren klikt, ziet u in het deelvenster **Importeren** de modulenaam die u gaat importeren. Als alle afhankelijkheden zijn geïnstalleerd, wordt de **knop OK** geactiveerd. Als u afhankelijkheden mist, moet u deze afhankelijkheden importeren voordat u deze module importeren.
8. Klik **op** de pagina Importeren op **OK** om de module te importeren. Terwijl Azure Automation een module importeert naar uw account, worden metagegevens over de module en de cmdlets geëxtraheerd. Deze actie kan een paar minuten duren, omdat elke activiteit moet worden geëxtraheerd.
9. U ontvangt een eerste melding dat de module wordt geïmplementeerd en een andere melding wanneer deze is voltooid.
10. Nadat de module is geïmporteerd, u de beschikbare activiteiten bekijken. U de resources gebruiken in uw runbooks en Gewenste statusconfiguratie.

> [!NOTE]
> Modules die alleen PowerShell-kern ondersteunen, worden niet ondersteund in Azure Automation en kunnen niet worden geïmporteerd in de Azure-portal of rechtstreeks vanuit de PowerShell-galerie worden geïmplementeerd.

## <a name="python-runbooks"></a>Python Runbooks

Python Runbooks zijn beschikbaar in de [galerie Scriptcenter](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B1%5D.Type=ProgrammingLanguage&f%5B1%5D.Value=Python&f%5B1%5D.Text=Python&sortBy=Date&username=). U Python-runbooks bijdragen aan de galerie Scriptcenter door op **Een bijdrage uploaden**te klikken. Wanneer u dit doet, moet u ervoor zorgen dat u de tag **Python** toevoegt bij het uploaden van uw bijdrage.

> [!NOTE]
> Om content te uploaden naar [het Script Center](https://gallery.technet.microsoft.com/scriptcenter) is minimaal 100 punten vereist.

## <a name="requesting-a-runbook-or-module"></a>Een runbook of module aanvragen

U verzoeken verzenden naar [User Voice.](https://feedback.azure.com/forums/246290-azure-automation/)  Als je hulp nodig hebt bij het schrijven van een runbook of een vraag hebt over PowerShell, plaats dan een vraag op ons [forum.](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)

## <a name="common-solutions-available-in-the-runbook-gallery"></a>Algemene oplossingen beschikbaar in de runbook-galerie

De onderstaande lijst bevat een paar runbooks die oplossingen bieden voor veelvoorkomende scenario's. Zie AzureAutomationTeam-profiel voor een volledige lijst met runbooks die zijn gemaakt door het Azure [AutomationTeam-team.](https://www.powershellgallery.com/profiles/AzureAutomationTeam)

* [Update-ModulesInAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/) - Importeert de nieuwste versie op PowerShell Gallery van alle modules in een Automation-account.
* [Enable-AzureDiagnostics](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/) - Dit script configureert Azure Diagnostics en Log Analytics om Azure Automation-logboeken te ontvangen die taakstatus en taakstromen bevatten.
* [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/) - Deze runbook kopieert een extern bestand van een virtuele Windows Azure-machine.
* [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) - Deze runbook kopieert een lokaal bestand naar een virtuele Azure-machine.

## <a name="next-steps"></a>Volgende stappen

* Zie [Runbook beheren in Azure Automation](manage-runbooks.md) voor aan de slag met runbooks.
* Zie [PowerShell-werkstroom](automation-powershell-workflow.md) leren om de verschillen tussen PowerShell- en PowerShell-workflow met runbooks te begrijpen
* Zie voor meer informatie over PowerShell, inclusief taalverwijzingen en leermodules, de [PowerShell-documenten](https://docs.microsoft.com/powershell/scripting/overview).
