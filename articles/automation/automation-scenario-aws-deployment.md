---
title: Implementatie van een virtuele machine in Amazon Web Services automatiseren
description: In dit artikel wordt beschreven hoe u Azure Automation kunt gebruiken om het maken van een Amazon Web Service-VM te automatiseren
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d2a58d3e79301f277143d8c4b6e810a377a211b9
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849629"
---
# <a name="azure-automation-scenario---provision-an-aws-virtual-machine"></a>Azure Automation scenario: richt een virtuele AWS-machine in
In dit artikel leert u hoe u Azure Automation kunt gebruiken om een virtuele machine in te richten in uw Amazon Web Service (AWS)-abonnement en om die VM een specifieke naam te geven, waarnaar AWS verwijst als ' tagging ' van de VM.

## <a name="prerequisites"></a>Vereisten
Voor de doel einden van dit artikel moet u een Azure Automation-account en een AWS-abonnement hebben. Voor meer informatie over het instellen van een Azure Automation-account en het configureren van deze met uw AWS-abonnements referenties, raadpleegt [u verificatie configureren met Amazon Web Services](automation-config-aws-account.md). Dit account moet worden gemaakt of bijgewerkt met de referenties van uw AWS-abonnement voordat u doorgaat. in de volgende stappen wordt verwezen naar dit account.

## <a name="deploy-amazon-web-services-powershell-module"></a>Amazon Web Services Power shell-module implementeren
Uw VM-inrichtings runbook maakt gebruik van de AWS Power shell-module om het werk te doen. Voer de volgende stappen uit om de module toe te voegen aan uw Automation-account dat is geconfigureerd met uw AWS-abonnements referenties.  

1. Open uw webbrowser en ga naar de [PowerShell Gallery](https://www.powershellgallery.com/packages/AWSPowerShell/) en klik op de **knop implementeren op Azure Automation**.<br><br> ![AWS PS-module importeren](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png)
2. U gaat naar de Azure-aanmeldings pagina en na de verificatie, wordt u doorgestuurd naar de Azure Portal en weer gegeven op de volgende pagina:<br><br> ![Module pagina importeren](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)
3. Selecteer het Automation-account dat u wilt gebruiken en klik op **OK** om de implementatie te starten.

   > [!NOTE]
   > Bij het importeren van een Power shell-module in Azure Automation, worden ook de cmdlets geëxtraheerd en deze activiteiten worden pas weer gegeven als de module de cmdlets volledig heeft geïmporteerd en geëxtraheerd. Dit proces kan enkele minuten duren.  
   > <br>

1. Open in de Azure Portal uw Automation-account waarnaar wordt verwezen in stap 3.
2. Klik op de tegel **assets** en selecteer in het deel venster **assets** de tegel **modules** .
3. Op de pagina **modules** ziet u de module **AWSPowerShell** in de lijst.

## <a name="create-aws-deploy-vm-runbook"></a>AWS voor VM-implementatie maken
Zodra de Power shell-module AWS is geïmplementeerd, kunt u nu een runbook ontwerpen om het inrichten van een virtuele machine in AWS te automatiseren met behulp van een Power shell-script. De volgende stappen laten zien hoe u systeem eigen Power shell-script kunt gebruiken in Azure Automation.  

> [!NOTE]
> Ga naar de [PowerShell Gallery](https://www.powershellgallery.com/packages/New-AwsVM/)voor meer opties en informatie over dit script.
> 

1. Down load het Power shell-script New-AwsVM uit de PowerShell Gallery door een Power shell-sessie te openen en het volgende te typen:<br>
   ```powershell
   Save-Script -Name New-AwsVM -Path <path>
   ```
   <br>
2. Open uw Automation-account vanuit het Azure Portal en selecteer **Runbooks** onder de sectie **proces automatisering** aan de linkerkant.  
3. Op de pagina **Runbooks** selecteert u **een runbook toevoegen**.
4. Selecteer in het deel venster **een Runbook toevoegen** de optie **snel maken** (een nieuw runbook maken).
5. Typ in het deel venster Eigenschappen van **runbook** een naam in het vak naam voor uw Runbook en selecteer in de vervolg keuzelijst **type Runbook** de optie **Power shell**en klik vervolgens op **maken**.<br><br> ![runbook-venster maken](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png)
6. Wanneer de pagina Power shell-Runbook bewerken wordt weer gegeven, kopieert en plakt u het Power shell-script in het canvas voor het ontwerpen van Runbook.<br><br> ![Script voor Runbook Power shell](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>
   
    > [!NOTE]
    > Let op het volgende wanneer u werkt met het Power shell-voorbeeld script:
    > 
    > * Het runbook bevat een aantal standaard parameter waarden. Evalueer alle standaard waarden en werk waar nodig bij.
    > * Als u uw AWS-referenties hebt opgeslagen als een referentie-Asset dat een andere naam heeft dan **AWScred**, moet u het script bijwerken op regel 57 zodat dit overeenkomt met die van de overeenkomst.  
    > * Wanneer u werkt met de AWS CLI-opdrachten in Power shell, met name met dit voor beeld-runbook, moet u de AWS-regio opgeven. Anders mislukken de cmdlets. AWS-onderwerp weer geven [Geef de regio AWS](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) op in de AWS-hulpprogram Ma's voor Power shell-document voor meer informatie.  
    >

7. Als u een lijst met namen van installatie kopieën wilt ophalen uit uw AWS-abonnement, start u Power shell ISE en importeert u de AWS Power shell-module. Verificatie op basis van AWS door **Get-AutomationPSCredential** in uw ISE-omgeving te vervangen door **AWScred = Get-Credential**. Hiermee wordt u gevraagd uw referenties in te voeren en kunt u uw **toegangs sleutel-id** opgeven voor de gebruikers naam en **geheime toegangs sleutel** voor het wacht woord. Zie het voorbeeld hieronder:  

        ```powershell
        #Sample to get the AWS VM available images
        #Please provide the path where you have downloaded the AWS PowerShell module
        Import-Module AWSPowerShell
        $AwsRegion = "us-west-2"
        $AwsCred = Get-Credential
        $AwsAccessKeyId = $AwsCred.UserName
        $AwsSecretKey = $AwsCred.GetNetworkCredential().Password
   
        # Set up the environment to access AWS
        Set-AwsCredentials -AccessKey $AwsAccessKeyId -SecretKey $AwsSecretKey -StoreAs AWSProfile
        Set-DefaultAWSRegion -Region $AwsRegion
   
        Get-EC2ImageByName -ProfileName AWSProfile
        ```
        
    De volgende uitvoer wordt geretourneerd:<br><br>
   ![AWS-installatie kopieën ophalen](./media/automation-scenario-aws-deployment/powershell-ise-output.png)<br>  
8. Kopieer en plak de naam van de installatie kopie in een Automation-variabele waarnaar wordt verwezen in het runbook als **$InstanceType**. Omdat in dit voor beeld het gratis AWS-abonnement wordt gebruikt, gebruikt u **T2. micro** voor uw runbook-voor beeld.  
9. Sla het runbook op en klik vervolgens op **publiceren** om het runbook te publiceren en klik vervolgens op **Ja** wanneer u hierom wordt gevraagd.

### <a name="testing-the-aws-vm-runbook"></a>Het runbook van de AWS-VM testen
Voordat u verdergaat met het testen van het runbook, moet u enkele dingen verifiëren. Specifiek:  

* Een Asset voor verificatie op basis van AWS is gemaakt met de naam **AWScred** of het script is bijgewerkt om te verwijzen naar de naam van uw referentie-Asset.    
* De AWS Power shell-module is geïmporteerd in Azure Automation  
* Er is een nieuw runbook gemaakt en parameter waarden zijn gecontroleerd en zo nodig bijgewerkt  
* **Uitgebreide records registreren** en **voortgangs records eventueel registreren** onder de registratie van runbook-instellingen **en tracering** is ingesteld op **aan**.<br><br> ![Runbook-logboek registratie en tracering](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png)  

1. U wilt het runbook starten, dus klik op **Start** en klik vervolgens op **OK** wanneer het deel venster runbook starten wordt geopend.
2. Geef een **VMname**op in het deel venster Runbook starten. Accepteer de standaard waarden voor de andere para meters die u vooraf hebt geconfigureerd in het script. Klik op **OK** om de runbook-taak te starten.<br><br> ![start New-AwsVM runbook](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
3. Er wordt een taak venster geopend voor de runbook-taak die u hebt gemaakt. Sluit dit deel venster.
4. U kunt de voortgang van de taak weer geven en uitvoer **stromen** weer geven door de tegel **alle logboeken** op de pagina runbook-taak te selecteren.<br><br> ![stream-uitvoer](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png)
5. Als u wilt bevestigen dat de virtuele machine wordt ingericht, meldt u zich aan bij de AWS-beheer console als u momenteel niet bent aangemeld.<br><br> ![Geïmplementeerde VM in AWS-console](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## <a name="next-steps"></a>Volgende stappen
* Zie [Mijn eerste grafische runbook](automation-first-runbook-graphical.md) om aan de slag te gaan met grafische runbooks
* Zie [Mijn eerste PowerShell Workflow-runbook](automation-first-runbook-textual.md) om aan de slag te gaan met PowerShell Workflow-runbooks
* Zie [Azure Automation-runbooktypen](automation-runbook-types.md) voor meer informatie over runbooktypen, hun voordelen en beperkingen
* Zie [Systeemeigen PowerShell-scriptondersteuning in Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/) voor meer informatie over de functie voor PowerShelll-scriptondersteuning


