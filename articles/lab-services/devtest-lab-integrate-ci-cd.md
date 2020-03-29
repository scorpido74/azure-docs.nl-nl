---
title: Azure DevTest Labs integreren in uw Azure-pijplijnen
description: Meer informatie over het integreren van Azure DevTest Labs in uw Azure Pipelines-pijplijnen voor continue integratie en leveringspijplijn
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: a26df85e-2a00-462b-aac1-dd3539532569
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 9604da5252254120ac7bd3fca3f0cc97324aef92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76293212"
---
# <a name="integrate-azure-devtest-labs-into-your-azure-pipelines-cicd-pipeline"></a>Azure DevTest Labs integreren in uw CI/CD-pijplijn voor Azure Pipelines

U de azure *DevTest Labs-taken-extensie* gebruiken om uw Azure Pipelines continuous integration en continuous delivery (CI/CD) build- en releasepipelines te integreren met Azure DevTest Labs. De extensie installeert verschillende taken, waaronder: 

- Een virtuele machine (VM) maken
- Een aangepaste installatiekopie maken vanaf een virtuele machine
- Een VM verwijderen 

Met deze taken u bijvoorbeeld eenvoudig snel een *gouden afbeeldings-VM* implementeren voor een specifieke testtaak en vervolgens de VM verwijderen wanneer de test is voltooid.

In dit artikel ziet u hoe u Azure DevTest Labs-taken gebruikt om een vm te maken en te implementeren, een aangepaste afbeelding te maken en vervolgens de VM te verwijderen, allemaal als één releasepijplijn. U zou de taken normaal gesproken afzonderlijk uitvoeren in uw eigen aangepaste pijplijnen, testen en implementeren.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="prerequisites"></a>Vereisten

- Registreer of meld u aan bij uw [Azure DevOps-organisatie](https://dev.azure.com) en [maak een project](/vsts/organizations/projects/create-project) in de organisatie. 
  
- Installeer de azure DevTest Labs-takenextensie van Visual Studio Marketplace.
  
  1. Ga naar [Azure DevTest Labs-taken](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).
  1. Selecteer **Gratis krijgen**.
  1. Selecteer uw Azure DevOps-organisatie in de vervolgkeuzelijst en selecteer **Installeren**. 
  
## <a name="create-the-template-to-build-an-azure-vm"></a>De sjabloon maken om een Azure-vm te maken 

In deze sectie wordt beschreven hoe u de Azure Resource Manager-sjabloon maakt die u gebruikt om een Azure VM on demand te maken.

1. Als u een resourcemanagersjabloon in uw abonnement wilt maken, volgt u de procedure in [Een resourcebeheersjabloon gebruiken.](devtest-lab-use-resource-manager-template.md)
   
1. Voordat u de sjabloon Resourcemanager genereert, voegt u het [WinRM-artefact](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-winrm) toe als onderdeel van het maken van de VM. Implementatietaken zoals *Azure File Copy* en *PowerShell op doelmachines* hebben WinRM-toegang nodig. Het WinRM-artefact vereist een hostnaam als parameter, die de volledig gekwalificeerde domeinnaam (FQDN) van de VM moet zijn. 
   
   > [!NOTE]
   > Wanneer u WinRM met een gedeeld IP-adres gebruikt, moet u een NAT-regel toevoegen om een externe poort aan de WinRM-poort toe te voegen. U hebt de NAT-regel niet nodig als u de VM maakt met een openbaar IP-adres.
   
   
1. Sla de sjabloon op uw computer op als een bestand met de naam *CreateVMTemplate.json*.
   
1. Controleer de sjabloon bij uw bronbesturingssysteem.

## <a name="create-a-script-to-get-vm-properties"></a>Een script maken om VM-eigenschappen te krijgen

Wanneer u taakstappen uitvoert zoals *Azure File Copy* of *PowerShell op doelmachines* in de releasepijplijn, verzamelt het volgende script de waarden die u nodig hebt om een app te implementeren op een vm. Normaal gesproken gebruikt u deze taken om uw app te implementeren op een Azure VM. De taken vereisen waarden zoals de naam vm-brongroep, IP-adres en FQDN.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ga als u het scriptbestand maken:

1. Open een teksteditor en plak het volgende script erin.
   
   ```powershell
   Param( [string] $labVmId)

   $labVmComputeId = (Get-AzResource -Id $labVmId).Properties.ComputeId

   # Get lab VM resource group name
   $labVmRgName = (Get-AzResource -Id $labVmComputeId).ResourceGroupName

   # Get the lab VM Name
   $labVmName = (Get-AzResource -Id $labVmId).Name

   # Get lab VM public IP address
   $labVMIpAddress = (Get-AzPublicIpAddress -ResourceGroupName $labVmRgName
                   -Name $labVmName).IpAddress

   # Get lab VM FQDN
   $labVMFqdn = (Get-AzPublicIpAddress -ResourceGroupName $labVmRgName
              -Name $labVmName).DnsSettings.Fqdn

   # Set a variable labVmRgName to store the lab VM resource group name
   Write-Host "##vso[task.setvariable variable=labVmRgName;]$labVmRgName"

   # Set a variable labVMIpAddress to store the lab VM Ip address
   Write-Host "##vso[task.setvariable variable=labVMIpAddress;]$labVMIpAddress"

   # Set a variable labVMFqdn to store the lab VM FQDN name
   Write-Host "##vso[task.setvariable variable=labVMFqdn;]$labVMFqdn"
   ```

1. Sla het bestand op met een naam als *GetLabVMParams.ps1*en check het in bij uw bronbesturingssysteem. 

## <a name="create-a-release-pipeline-in-azure-pipelines"></a>Een release-pijplijn in Azure Pipelines maken

Ga als lid van het nieuws naar een nieuwe releasepijplijn:

1. Selecteer op uw Azure DevOps-projectpagina **pijplijnen** > **releases** van de linkernavigatie.
1. Selecteer **Nieuwe pijplijn**.
1. Schuif **onder Een sjabloon selecteren**naar beneden en selecteer Taak **leeg**maken en selecteer Vervolgens **Toepassen**.

### <a name="add-and-set-variables"></a>Variabelen toevoegen en instellen

De pijplijntaken gebruiken de waarden die u aan de VM hebt toegewezen toen u de sjabloon Resourcebeheer in de Azure-portal hebt gemaakt. 

Ga als u variabelen voor de waarden toevoegt: 

1. Selecteer op de pagina pijplijn het tabblad **Variabelen.**
   
1. Selecteer Voor elke variabele **Toevoegen** en voer de naam en waarde in:
   
   |Name|Waarde|
   |---|---|
   |*vmName*|VM-naam die u hebt toegewezen in de sjabloon Resourcemanager|
   |*Gebruikersnaam*|Gebruikersnaam om toegang te krijgen tot de VM|
   |*wachtwoord*|Wachtwoord voor de gebruikersnaam. Selecteer het vergrendelingspictogram om het wachtwoord te verbergen en te beveiligen.

### <a name="create-a-devtest-labs-vm"></a>Een VM Voor DevTest Labs maken

De volgende stap is het maken van de gouden afbeelding VM te gebruiken voor toekomstige implementaties. U maakt de VM binnen uw Azure DevTest Labs-exemplaar met behulp van de *azure devTest Labs-taak VM maken.*

1. Selecteer op het tabblad **Pijplijn voor** de pijplijn voor vrijgave de hyperlinktekst in **fase 1** om **fasetaken**weer te geven en selecteer vervolgens het plusteken **+** naast de taak **Agent**. 
   
1. Selecteer **onder Taken toevoegen**de optie Azure **DevTest Labs Vm maken**en selecteer **Toevoegen**. 
   
1. Selecteer **Azure DevTest Labs VM maken** in het linkerdeelvenster. 

1. Vul in het rechterdeelvenster het formulier als volgt in:
   
   |Veld|Waarde|
   |---|---|
   |**Azure RM-abonnement**|Selecteer een serviceverbinding of -abonnement in **beschikbare Azure-serviceverbindingen** of **beschikbare Azure-abonnementen** in de vervolgkeuzelijst en selecteer Indien nodig **autoriseren.**<br /><br />**Let op:** Zie Eindpunt azure [Resource Manager-service](/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager)voor informatie over het maken van een verbinding met beperktere machtigingen voor uw Azure-abonnement.|
   |**Labnaam**|Selecteer de naam van een bestaand lab waarin de lab-VM wordt gemaakt.|
   |**Sjabloonnaam**|Voer het volledige pad en de naam in van het sjabloonbestand dat u hebt opgeslagen in uw broncodeopslagplaats. U ingebouwde eigenschappen gebruiken om het pad te vereenvoudigen, bijvoorbeeld:<br /><br />`$(System.DefaultWorkingDirectory)/Templates/CreateVMTemplate.json`|
   |**Sjabloonparameters**|Voer de parameters in voor de variabelen die u eerder hebt gedefinieerd:<br /><br />`-newVMName '$(vmName)' -userName '$(userName)' -password (ConvertTo-SecureString -String '$(password)' -AsPlainText -Force)`|
   |**VM-ID uitvoervariabelen** > **lab**|Voer de variabele in voor de gemaakte labVM-id. Als u de standaard **labVMId gebruikt,** u de variabele in volgende taken als *$(labVMId)* verwijzen.<br /><br />U een andere naam maken dan de standaardnaam, maar vergeet niet de juiste naam te gebruiken in volgende taken. U de Lab VM-id in de volgende vorm schrijven:<br /><br />`/subscriptions/{subscription Id}/resourceGroups/{resource group Name}/providers/Microsoft.DevTestLab/labs/{lab name}/virtualMachines/{vmName}`|

### <a name="collect-the-details-of-the-devtest-labs-vm"></a>Verzamel de details van de VM DevTest Labs

Voer het script uit dat u eerder hebt gemaakt om de details van de VM DevTest Labs te verzamelen. 

1. Selecteer op het tabblad **Pijplijn voor** de pijplijn voor vrijgave de hyperlinktekst in **fase 1** om **fasetaken**weer te geven en selecteer vervolgens het plusteken **+** naast de taak **Agent**. 
   
1. Selecteer **onder Taken toevoegen**Azure **PowerShell**en selecteer **Toevoegen**. 
   
1. Selecteer **Azure PowerShell-script: FilePath** in het linkerdeelvenster. 
   
1. Vul in het rechterdeelvenster het formulier als volgt in:
   
   |Veld|Waarde|
   |---|---|
   |**Azure-verbindingstype**|Selecteer **Azure Resource Manager**.|
   |**Azure-abonnement**|Selecteer uw serviceverbinding of abonnement.| 
   |**Scripttype**|Selecteer **Scriptbestandspad**.|
   |**Scriptpad**|Voer het volledige pad en de naam in van het PowerShell-script dat u hebt opgeslagen in uw broncodeopslagplaats. U ingebouwde eigenschappen gebruiken om het pad te vereenvoudigen, bijvoorbeeld:<br /><br />`$(System.DefaultWorkingDirectory/Scripts/GetLabVMParams.ps1`|
   |**Scriptargumenten**|Voer de naam in van de *variabele labVmId* die is ingevuld door de vorige taak, bijvoorbeeld:<br /><br />`-labVmId '$(labVMId)'`|

Het script verzamelt de vereiste waarden en slaat deze op in omgevingsvariabelen in de releasepijplijn, zodat u ze in volgende stappen eenvoudig doorverwijzen.

### <a name="create-a-vm-image-from-the-devtest-labs-vm"></a>Een VM-afbeelding maken op basis van de VM DevTest Labs

De volgende taak is het maken van een afbeelding van de nieuw geïmplementeerde VM in uw Azure DevTest Labs-exemplaar. U de afbeelding vervolgens gebruiken om kopieën van de VM op aanvraag te maken wanneer u een dev-taak wilt uitvoeren of een aantal tests wilt uitvoeren. 

1. Selecteer op het tabblad **Pijplijn voor** de pijplijn voor vrijgave de hyperlinktekst in **fase 1** om **fasetaken**weer te geven en selecteer vervolgens het plusteken **+** naast de taak **Agent**. 
   
1. Selecteer **onder Taken toevoegen**de optie Azure **DevTest Labs Aangepaste afbeelding maken**en selecteer **Toevoegen**. 
   
1. Configureer de taak als volgt:
   
   |Veld|Waarde|
   |---|---|
   |**Azure RM-abonnement**|Selecteer uw serviceverbinding of abonnement.|
   |**Labnaam**|Selecteer de naam van een bestaand lab waarin de afbeelding wordt gemaakt.|
   |**Aangepaste afbeeldingsnaam**|Voer een naam in voor de aangepaste afbeelding.|
   |**Beschrijving** (optioneel)|Voer een beschrijving in om het eenvoudig te maken om de juiste afbeelding later te selecteren.|
   |**Bron Lab VM** > **Source Lab VM ID**|Als u de standaardnaam van de variabele LabVMId hebt gewijzigd, voert u deze hier in. De standaardwaarde is **$(labVMId)**.|
   |**Aangepaste** > **afbeeldings-id** voor uitvoervariabelen|U indien nodig de standaardnaam van de variabele bewerken.|
   
### <a name="deploy-your-app-to-the-devtest-labs-vm-optional"></a>Uw app implementeren op de VM DevTest Labs (optioneel)

U taken toevoegen om uw app te implementeren aan de nieuwe VM DevTest Labs. De taken die u gewoonlijk gebruikt om de app te implementeren, zijn *Azure File Copy* en *PowerShell op doelmachines.*

De VM-informatie die u nodig hebt voor de parameters van deze taken wordt opgeslagen in drie configuratievariabelen met de naam **labVmRgName,** **labVMIpAddress**en **labVMFqdn** in de releasepijplijn. Als u alleen wilt experimenteren met het maken van een VM voor DevTest Labs en een aangepaste afbeelding, zonder er een app op te implementeren, u deze stap overslaan.

### <a name="delete-the-vm"></a>De VM verwijderen

De laatste taak is het verwijderen van de VM die u hebt geïmplementeerd in uw Azure DevTest Labs-exemplaar. Normaal gesproken verwijdert u de VM nadat u de dev-taken hebt uitgevoerd of de tests uitvoert die u op de geïmplementeerde VM nodig hebt. 

1. Selecteer op het tabblad **Pijplijn voor** de pijplijn voor vrijgave de hyperlinktekst in **fase 1** om **fasetaken**weer te geven en selecteer vervolgens het plusteken **+** naast de taak **Agent**. 
   
1. Selecteer **onder Taken toevoegen**de optie Azure **DevTest Labs Delete VM**en selecteer **Toevoegen**. 
   
1. Configureer de taak als volgt:
   
   - Selecteer onder **Azure RM-abonnement**uw serviceverbinding of -abonnement. 
   - Als **Lab VM ID**u de standaardnaam van de variabele LabVMId hebt gewijzigd, voert u deze hier in als u de standaardnaam van de variabele LabVMId hebt gewijzigd. De standaardwaarde is **$(labVMId)**.
   
### <a name="save-the-release-pipeline"></a>De releasepijplijn opslaan

Ga als volgende over de volgende keer dat u de nieuwe releasepijplijn wilt opslaan:

1. Selecteer de naam **Nieuwe releasepijplijn** op de pagina releasepijplijn en voer een nieuwe naam in voor de pijplijn. 
   
1. Selecteer het pictogram **Opslaan** rechtsboven. 

## <a name="create-and-run-a-release"></a>Een release maken en uitvoeren

Ga als lid van de nieuwe pijplijn naar een release en voer deze uit:

1. Selecteer Release rechtsboven **maken** op de pagina releasepijplijn. 
   
1. Selecteer **onder Artefacten**de nieuwste build en selecteer **Vervolgens Maken**.
   
1. Vernieuw in elke releasefase de weergave van uw devTest Labs-instantie in de Azure-portal om de vm-creatie, het maken van afbeeldingen en het verwijderen van vm's weer te geven.

U de aangepaste afbeelding gebruiken om VM's te maken wanneer u ze nodig hebt.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het [maken van multi-VM-omgevingen met Resource Manager-sjablonen](devtest-lab-create-environment-from-arm.md).
- Ontdek meer quickstart Resource Manager-sjablonen voor DevTest Labs-automatisering vanuit de [openbare DevTest Labs GitHub-repo.](https://github.com/Azure/azure-quickstart-templates)
- Raadpleeg indien nodig de pagina [Azure DevOps voor probleemoplossing.](https://docs.microsoft.com/azure/devops/pipelines/troubleshooting)
 
