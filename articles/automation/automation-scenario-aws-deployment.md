---
title: Implementatie van een VM automatiseren in Amazon Web Services
description: In dit artikel wordt uitgelegd hoe u Azure Automation gebruiken om het maken van een Amazon Web Service VM te automatiseren
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: b149a21ef60b22c2e549c91007265ce99babdbe4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75420928"
---
# <a name="azure-automation-scenario---provision-an-aws-virtual-machine"></a>Azure Automation-scenario - een virtuele AWS-machine inrichten
In dit artikel leert u hoe u Azure Automation gebruiken om een virtuele machine in uw Amazon Web Service (AWS)-abonnement in te richten en die VM een specifieke naam te geven - die AWS de VM tagt.

## <a name="prerequisites"></a>Vereisten
Voor de toepassing van dit artikel moet u een Azure Automation-account en een AWS-abonnement hebben. Voor meer informatie over het instellen van een Azure Automation-account en het configureren ervan met uw AWS-abonnementsreferenties, controleert [u Verificatie configureren met Amazon Web Services](automation-config-aws-account.md). Dit account moet worden gemaakt of bijgewerkt met uw AWS-abonnementsgegevens voordat u verdergaat, omdat u in de onderstaande stappen naar dit account verwijst.

## <a name="deploy-amazon-web-services-powershell-module"></a>Amazon Web Services PowerShell-module implementeren
Uw VM-inrichtingsrunbook maakt gebruik van de AWS PowerShell-module om zijn werk te doen. Voer de volgende stappen uit om de module toe te voegen aan uw Automatiseringsaccount die is geconfigureerd met uw AWS-abonnementsgegevens.  

1. Open uw webbrowser en navigeer naar de [PowerShell-galerie](https://www.powershellgallery.com/packages/AWSPowerShell/) en klik op de **knop Implementeren naar Azure Automation.**<br><br> ![AWS PS-module importeren](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png)
2. U wordt naar de azure-aanmeldingspagina geleid en na verificatie wordt u doorgestuurd naar de Azure-portal en krijgt u de volgende pagina te zien:<br><br> ![Pagina Module importeren](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)
3. Selecteer het automatiseringsaccount dat u wilt gebruiken en klik op **OK** om de implementatie te starten.

   > [!NOTE]
   > Tijdens het importeren van een PowerShell-module in Azure Automation worden ook de cmdlets geëxtraheerd en worden deze activiteiten pas weergegeven nadat de module volledig klaar is met het importeren en extraheren van de cmdlets. Dit proces kan enkele minuten duren.  
   > <br>

1. Open in de Azure-portal uw Automatiseringsaccount waarnaar wordt verwezen in stap 3.
2. Klik op de tegel **Activa** en selecteer in het deelvenster **Activa** de tegel **Modules.**
3. Op de pagina **Modules** ziet u de **AWSPowerShell-module** in de lijst.

## <a name="create-aws-deploy-vm-runbook"></a>AWS-implementatieVM-runbook maken
Zodra de AWS PowerShell Module is geïmplementeerd, u nu een runbook maken om de inrichting van een virtuele machine in AWS te automatiseren met behulp van een PowerShell-script. In de onderstaande stappen wordt uitgelegd hoe u gebruik maken van native PowerShell-script in Azure Automation.  

> [!NOTE]
> Ga voor meer opties en informatie over dit script naar de [PowerShell Gallery.](https://www.powershellgallery.com/packages/New-AwsVM/)
> 

1. Download het PowerShell-script New-AwsVM uit de PowerShell Gallery door een PowerShell-sessie te openen en het volgende te typen:<br>
   ```powershell
   Save-Script -Name New-AwsVM -Path <path>
   ```
   <br>
2. Open in de Azure-portal uw Automatiseringsaccount en selecteer **Runbooks** onder de sectie **Procesautomatisering** aan de linkerkant.  
3. Selecteer op de pagina **Runbooks** de optie **Een runbook toevoegen**.
4. Selecteer Snel **maken** (Een nieuw runbook maken) in het deelvenster **Een runbook toevoegen.**
5. Typ in het deelvenster **Eigenschappen van Runbook** een naam in het vak Naam voor uw runbook en selecteer in de vervolgkeuzelijst **Runbook type** **PowerShell**en klik vervolgens op **Maken**.<br><br> ![Runbook-deelvenster maken](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png)
6. Wanneer de pagina PowerShell Runbook bewerken wordt weergegeven, kopieert en plakt u het PowerShell-script in het canvas voor het ontwerpen van runbook.<br><br> ![Runbook PowerShell-script](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>
   
    > [!NOTE]
    > Let op het volgende wanneer u met het voorbeeld PowerShell-script werkt:
    > 
    > * Het runbook bevat een aantal standaardparameterwaarden. Evalueer alle standaardwaarden en werk waar nodig bij.
    > * Als u uw AWS-referenties hebt opgeslagen als een referentie-asset die anders is genoemd dan **AWScred,** moet u het script op regel 57 bijwerken om dienovereenkomstig te voldoen.  
    > * Wanneer u met de AWS CLI-opdrachten in PowerShell werkt, vooral met dit voorbeeldrunbook, moet u de AWS-regio opgeven. Anders falen de cmdlets. AWS-onderwerp bekijken [Geef AWS-regio op](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) in het AWS Tools for PowerShell-document voor meer informatie.  
    >

7. Als u een lijst met afbeeldingsnamen uit uw AWS-abonnement wilt ophalen, start u PowerShell ISE en importeert u de AWS PowerShell Module. Authenticeer tegen AWS door **Get-AutomationPSCredential** in uw ISE-omgeving te vervangen door **AWScred = Get-Credential**. Dit vraagt u om uw referenties en u uw **Access Key ID** opgeven voor de gebruikersnaam en Secret Access **Key** voor het wachtwoord. Zie het voorbeeld hieronder:  

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
   ![AWS-afbeeldingen bekijken](./media/automation-scenario-aws-deployment/powershell-ise-output.png)<br>  
8. Kopieer en plak het een van de afbeeldingsnamen in een variabele Automatisering zoals bedoeld in het runbook als **$InstanceType**. Aangezien u in dit voorbeeld het gratis AWS-gelaagde abonnement gebruikt, gebruikt u **t2.micro** voor uw runbook-voorbeeld.  
9. Sla het runbook op, klik vervolgens op **Publiceren** om het runbook te publiceren en vervolgens **Ja** wanneer daarom wordt gevraagd.

### <a name="testing-the-aws-vm-runbook"></a>Het AWS VM-runbook testen
Voordat u verder gaat met het testen van het runbook, moet u een paar dingen verifiëren. Met name:  

* Er is een asset gemaakt voor het authenticeren tegen AWS genaamd **AWScred** of het script is bijgewerkt om te verwijzen naar de naam van uw referentie-asset.    
* De AWS PowerShell-module is geïmporteerd in Azure Automation  
* Er is een nieuw runbook gemaakt en parameterwaarden zijn geverifieerd en waar nodig bijgewerkt  
* **Logboekverboserecords** en eventueel **Logboekvoortgangsrecords** onder de runbook-instelling **Logboekregistratie en tracering** zijn ingesteld op **Aan**.<br><br> ![Logboeklogboeklogboeklogboekregistratie en -tracering](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png)  

1. U wilt de runbook starten, dus klik op **Start** en klik vervolgens op **OK** wanneer het deelvenster Startrunbook wordt geopend.
2. Geef in het deelvenster Runbook starten een **VM-naam**op . Accepteer de standaardwaarden voor de andere parameters die u eerder in het script hebt geconfigureerd. Klik op **OK** om de runbook-taak te starten.<br><br> ![Nieuw-AwsVM-runbook starten](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
3. Er wordt een taakvenster geopend voor de runbook-taak die u hebt gemaakt. Sluit dit deelvenster.
4. U de voortgang van de taak weergeven en **uitvoerstromen** weergeven door de tegel **Alle logboeken te** selecteren op de taakpagina runbook.<br><br> ![Streamuitvoer](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png)
5. Als u wilt bevestigen dat de vm wordt ingericht, meldt u zich aan bij de AWS Management Console als u momenteel niet bent ingelogd.<br><br> ![AWS-console geïmplementeerdVM](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## <a name="next-steps"></a>Volgende stappen
* Zie [Mijn eerste grafische runbook](automation-first-runbook-graphical.md) om aan de slag te gaan met grafische runbooks
* Zie [Mijn eerste PowerShell-werkstroomrunboek](automation-first-runbook-textual.md) om aan de slag te gaan met PowerShell-werkstroomrunboeken
* Zie [Azure Automation-runbooktypen](automation-runbook-types.md) voor meer informatie over runbooktypen, hun voordelen en beperkingen
* Zie [Systeemeigen PowerShell-scriptondersteuning in Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/) voor meer informatie over de functie voor PowerShelll-scriptondersteuning


