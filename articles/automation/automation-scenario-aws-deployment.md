---
title: Implementatie van een virtuele machine in Amazon Web Services automatiseren
description: In dit artikel wordt beschreven hoe u Azure Automation kunt gebruiken om het maken van een Amazon Web Service-VM te automatiseren
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 52a887d2d934aa2f7e13f0c2fdb4332066aee0e7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81604834"
---
# <a name="azure-automation-scenario---provision-an-aws-virtual-machine"></a>Azure Automation scenario: richt een virtuele AWS-machine in
In dit artikel leert u hoe u Azure Automation kunt gebruiken om een virtuele machine in te richten in uw Amazon Web Service (AWS)-abonnement en om die VM een specifieke naam te geven, waarnaar AWS verwijst als ' tagging ' van de VM.

## <a name="prerequisites"></a>Vereisten
U moet een Azure Automation-account en een Amazon Web Services-abonnement (AWS) hebben. Voor meer informatie over het instellen van een Azure Automation-account en het configureren van deze met uw AWS-abonnements referenties, raadpleegt [u verificatie configureren met Amazon Web Services](automation-config-aws-account.md). Dit account moet worden gemaakt of bijgewerkt met de referenties van uw AWS-abonnement voordat u doorgaat. in de volgende secties wordt verwezen naar dit account.

## <a name="deploy-amazon-web-services-powershell-module"></a>Amazon Web Services Power shell-module implementeren
Uw VM-inrichtings runbook maakt gebruik van de AWS Power shell-module om het werk uit te voeren. Gebruik de volgende stappen om de module toe te voegen aan uw Automation-account dat is geconfigureerd met uw AWS-abonnements referenties.  

1. Open uw webbrowser en ga naar de [PowerShell Gallery](https://www.powershellgallery.com/packages/AWSPowerShell/) en klik op de **knop implementeren op Azure Automation**.<br><br> ![AWS PS-module importeren](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png)
2. U gaat naar de Azure-aanmeldings pagina en na de verificatie, wordt u doorgestuurd naar de Azure Portal en weer gegeven op de volgende pagina:<br><br> ![Module pagina importeren](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)
3. Selecteer het Automation-account dat u wilt gebruiken en klik op **OK** om de implementatie te starten.

   > [!NOTE]
   > Wanneer Azure Automation een Power shell-module importeert, worden de cmdlets geëxtraheerd. De activiteiten worden pas weer gegeven als de module is geïmporteerd en de cmdlets zijn geëxtraheerd. Dit proces kan enkele minuten duren.  
   > <br>

1. Open uw Automation-account in Azure Portal.
2. Klik op de tegel **assets** en selecteer **modules**in het deel venster assets.
3. Op de pagina modules ziet u de module **AWSPowerShell** in de lijst.

## <a name="create-aws-deploy-vm-runbook"></a>AWS voor VM-implementatie maken
Zodra de Power shell-module AWS is geïmplementeerd, kunt u nu een runbook ontwerpen om het inrichten van een virtuele machine in AWS te automatiseren met behulp van een Power shell-script. De volgende stappen laten zien hoe u systeem eigen Power shell-script gebruikt in Azure Automation.  

> [!NOTE]
> Ga naar de [PowerShell Gallery](https://www.powershellgallery.com/packages/New-AwsVM/)voor meer opties en informatie over dit script.
> 

1. Down load het Power shell-script New-AwsVM uit de PowerShell Gallery door een Power shell-sessie te openen en de volgende opdracht te typen:<br>
   ```powershell
   Save-Script -Name New-AwsVM -Path <path>
   ```
   <br>
2. Open uw Automation-account vanuit het Azure Portal en selecteer **Runbooks** onder **proces automatisering**.  
3. Op de pagina Runbooks selecteert u **een Runbook toevoegen**.
4. Selecteer in het deel venster een runbook toevoegen de optie **snel maken** om een nieuw runbook te maken.
5. Typ in het deel venster Eigenschappen van Runbook een naam voor het runbook.
6. Selecteer **Power shell**in de vervolg keuzelijst **type Runbook** en klik vervolgens op **maken**.<br><br> ![Runbook-deel venster maken](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png)
6. Wanneer de pagina Power shell-Runbook bewerken wordt weer gegeven, kopieert en plakt u het Power shell-script in het canvas voor het ontwerpen van Runbook.<br><br> ![Script voor Runbook Power shell](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>
   
    > [!NOTE]
    > Let op het volgende wanneer u werkt met het Power shell-voorbeeld script:
    > 
    > * Het runbook bevat een aantal standaard parameter waarden. Evalueer alle standaard waarden en werk waar nodig bij.
    > * Als u uw AWS-referenties hebt opgeslagen als een ander referentie-element `AWScred`dan, moet u het script op regel 57 bijwerken zodat dit overeenkomt met die van.  
    > * Wanneer u werkt met de AWS CLI-opdrachten in Power shell, met name met dit voor beeld-runbook, moet u de AWS-regio opgeven. Anders mislukken de cmdlets. AWS-onderwerp weer geven [Geef de regio AWS](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) op in de AWS-hulpprogram Ma's voor Power shell-document voor meer informatie.  
    >

7. Als u een lijst met namen van installatie kopieën wilt ophalen uit uw AWS-abonnement, start u Power shell ISE en importeert u de AWS Power shell-module. Verifieer bij AWS door te `Get-AutomationPSCredential` vervangen in uw ISE- `AWScred = Get-Credential`omgeving met. Met deze instructie wordt om uw referenties gevraagd en kunt u uw toegangs sleutel-ID opgeven voor de gebruikers naam en uw geheime toegangs sleutel voor het wacht woord. 

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
8. Kopieer en plak de naam van de installatie kopie in een Automation-variabele waarnaar wordt verwezen in het `$InstanceType`runbook als. Omdat u in dit voor beeld het gratis AWS-abonnement gebruikt, gebruikt u **T2. micro** voor uw runbook-voor beeld.  
9. Sla het runbook op en klik vervolgens op **publiceren** om het runbook te publiceren en klik vervolgens op **Ja** wanneer u hierom wordt gevraagd.

### <a name="testing-the-aws-vm-runbook"></a>Het runbook van de AWS-VM testen
Voordat u verdergaat met het testen van het runbook, moet u een aantal dingen verifiëren:

* Een Asset met `AWScred` de naam voor verificatie op basis van AWS is gemaakt, of het script is bijgewerkt om te verwijzen naar de naam van uw referentie-Asset.    
* De AWS Power shell-module is geïmporteerd in Azure Automation.  
* Er is een nieuw runbook gemaakt en parameter waarden zijn gecontroleerd en zo nodig bijgewerkt.  
* **Uitgebreide records registreren** en **voortgangs records eventueel registreren** onder de runbook-bewerkings **Logboeken en tracering** is ingesteld op **aan**.<br><br> ![Runbook-logboek registratie en](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png)tracering.  

1. Klik op **Start** om het runbook te starten en klik vervolgens op **OK** wanneer het deel venster runbook starten wordt geopend.
2. Geef in het deel venster Runbook starten een VM-naam op. Accepteer de standaard waarden voor de andere para meters die u vooraf hebt geconfigureerd in het script. Klik op **OK** om de runbook-taak te starten.<br><br> ![Nieuw runbook starten-AwsVM](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
3. Er wordt een taak venster geopend voor de runbook-taak die u hebt gemaakt. Sluit dit deel venster.
4. U kunt de voortgang van de taak bekijken en uitvoer stromen weer geven door **alle logboeken** te selecteren in het deel venster Runbook-taak.<br><br> ![Uitvoer van stream](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png)
5. Als u wilt controleren of de virtuele machine wordt ingericht, meldt u zich aan bij de AWS-beheer console als u momenteel niet bent aangemeld.<br><br> ![Geïmplementeerde VM in AWS-console](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## <a name="next-steps"></a>Volgende stappen
* Zie [mijn eerste grafische runbook](automation-first-runbook-graphical.md)om aan de slag te gaan met grafische runbooks.
* Zie [mijn eerste Power shell workflow-runbook](automation-first-runbook-textual.md)om aan de slag te gaan met Power shell workflow-runbooks.
* Zie [Azure Automation runbook-typen](automation-runbook-types.md)voor meer informatie over de typen runbook en hun voor delen en beperkingen.
* Zie [systeem eigen ondersteuning voor Power shell-scripts in azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)voor meer informatie over de functie ondersteuning voor Power shell-scripts.