---
title: Python 2-pakketten beheren in Azure Automation
description: In dit artikel wordt beschreven hoe u Python 2-pakketten beheert in Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 02/25/2019
ms.topic: conceptual
ms.openlocfilehash: 9f52dfd92d430abffe5857d231898dd4b0e7745e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679926"
---
# <a name="manage-python-2-packages-in-azure-automation"></a>Python 2-pakketten beheren in Azure Automation

Met Azure Automation u Python 2-runbooks uitvoeren op Azure en op Linux Hybrid Runbook Workers. Om te helpen bij de vereenvoudiging van runbooks, u Python-pakketten gebruiken om de modules te importeren die u nodig hebt. In dit artikel wordt beschreven hoe u Python-pakketten beheert en gebruikt in Azure Automation.

## <a name="import-packages"></a>Pakketten importeren

Selecteer **python 2-pakketten** in uw automatiseringsaccount onder **Gedeelde bronnen**. Klik **+ Voeg een Python 2-pakket toe.**

![Python-pakket toevoegen](media/python-packages/add-python-package.png)

Selecteer op de pagina Python 2-pakket toevoegen een lokaal pakket dat u wilt uploaden. Het pakket kan een **.whl** of **.tar.gz** bestand zijn. Wanneer het pakket is geselecteerd, klikt u op **OK** om het te uploaden.

![Python-pakket toevoegen](media/python-packages/upload-package.png)

Zodra een pakket is geïmporteerd, wordt het vermeld op de pagina Python 2-pakketten in uw Automatiseringsaccount. Als u een pakket wilt verwijderen, selecteert u het pakket en klikt u op **Verwijderen**.

![Pakketlijst](media/python-packages/package-list.png)

## <a name="import-packages-with-dependencies"></a>Pakketten importeren met afhankelijkheden

Azure-automatisering lost geen afhankelijkheden op voor python-pakketten tijdens het importproces. Er zijn twee manieren om een pakket te importeren met al zijn afhankelijkheden. Slechts één van de volgende stappen moet worden gebruikt om de pakketten in uw Automatiseringsaccount te importeren.

### <a name="manually-download"></a>Handmatig downloaden

Voer op een Windows 64-bits machine met [python2.7](https://www.python.org/downloads/release/latest/python2) en [pip](https://pip.pypa.io/en/stable/) geïnstalleerd de volgende opdracht uit om een pakket en al zijn afhankelijkheden te downloaden:

```cmd
C:\Python27\Scripts\pip2.7.exe download -d <output dir> <package name>
```

Zodra de pakketten zijn gedownload, u ze importeren in uw automatiseringsaccount.

### <a name="runbook"></a>Runbook

Importeer het python-runbook [Import Python 2-pakketten uit pypi in Azure Automation-account](https://gallery.technet.microsoft.com/scriptcenter/Import-Python-2-packages-57f7d509) vanuit de galerie in uw Automatiseringsaccount. Controleer of de runinstellingen zijn ingesteld op **Azure** en start de runbook met de parameters. De runbook vereist een Run As-account om het automatiseringsaccount te laten werken. Zorg ervoor dat u voor elke parameter deze start met de schakelaar zoals te zien in de volgende lijst en afbeelding:

* -s \<abonnementId\>
* -g \<resourceGroep\>
* -een \<automationAccount\>
* -m \<modulePakket\>

![Pakketlijst](media/python-packages/import-python-runbook.png)

Met het runbook u opgeven welk pakket u wilt downloaden. Als u bijvoorbeeld `Azure` de parameter gebruikt, worden alle Azure-modules en alle afhankelijkheden (ongeveer 105) gedownload.

Zodra het runbook is voltooid, u de **Python 2-pakketten** onder **Gedeelde resources** in uw automatiseringsaccount controleren om te controleren of het pakket correct is geïmporteerd.

## <a name="use-a-package-in-a-runbook"></a>Een pakket gebruiken in een runbook

Als een pakket wordt geïmporteerd, u het gebruiken in een runbook. In het volgende voorbeeld wordt het [hulpprogrammapakket Azure Automation gebruikt.](https://github.com/azureautomation/azure_automation_utility) Dit pakket maakt het eenvoudiger om Python te gebruiken met Azure Automation. Als u het pakket wilt gebruiken, volgt u de instructies in de GitHub-opslagplaats en voegt u het toe aan het runbook. U bijvoorbeeld `from azure_automation_utility import get_automation_runas_credential` de functie importeren voor het ophalen van het run As-account.

```python
import azure.mgmt.resource
import automationassets
from azure_automation_utility import get_automation_runas_credential

# Authenticate to Azure using the Azure Automation RunAs service principal
runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
azure_credential = get_automation_runas_credential()

# Intialize the resource management client with the RunAs credential and subscription
resource_client = azure.mgmt.resource.ResourceManagementClient(
    azure_credential,
    str(runas_connection["SubscriptionId"]))

# Get list of resource groups and print them out
groups = resource_client.resource_groups.list()
for group in groups:
    print group.name
```

## <a name="develop-and-test-runbooks-offline"></a>Runbooks offline ontwikkelen en testen

Als u uw Python 2-runbooks offline wilt ontwikkelen en testen, u de module [Azure Automation python geëmuleerd op](https://github.com/azureautomation/python_emulated_assets) GitHub gebruiken. Met deze module u verwijzen naar uw gedeelde bronnen, zoals referenties, variabelen, verbindingen en certificaten.

## <a name="next-steps"></a>Volgende stappen

Zie [Mijn eerste Python 2-runbook om](automation-first-runbook-textual-python2.md)aan de slag te gaan met Python 2 runbooks.
