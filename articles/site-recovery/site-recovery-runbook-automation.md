---
title: Azure Automation-runbooks toevoegen aan herstelplannen voor siteherstel
description: Meer informatie over het uitbreiden van herstelplannen met Azure Automation voor herstel na noodgevallen met Azure Site Recovery.
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: rajanaki
ms.openlocfilehash: ecfe993a137ca63c84438870ec54ac1e6d6707da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257483"
---
# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>Azure Automation-runbooks aan herstelplannen toevoegen

In dit artikel wordt beschreven hoe u Azure Automation-runbooks integreren om [herstelplannen](site-recovery-overview.md) voor Azure Site Recovery uit te breiden. We laten u zien hoe u basistaken automatiseren die anders handmatigmoeten worden ingegrepen en hoe u een herstel met meerdere stappen omzetten in een actie met één klik.

## <a name="recovery-plans"></a>Herstelplannen 

U herstelplannen gebruiken wanneer u niet over on-premises machines of Azure VM's heen gaat. Herstelplannen helpen u bij het definiëren van een systematisch herstelproces dat bepaalt hoe machines mislukken en hoe ze starten en herstellen na een failover. 

Herstel grote apps kunnen complex zijn. Herstelplannen helpen bij het opleggen van orde, zodat herstel consistent nauwkeurig, herhaalbaar en geautomatiseerd is. U taken binnen een herstelplan automatiseren met scripts en Azure Automation-runbooks. Typische voorbeelden zijn het configureren van instellingen op een Azure VM na failover of het opnieuw configureren van een app die op de VM wordt uitgevoerd.

- [Meer informatie](recovery-plan-overview.md) over herstelplannen.
- [Meer informatie](../automation/automation-runbook-types.md) over Azure Automation-runbooks.



## <a name="runbooks-in-recovery-plans"></a>Boeken uitvoeren in herstelplannen

U voegt een Azure Automation-account en runbooks toe aan een herstelplan. Het runbook wordt aangeroepen wanneer het herstelplan wordt uitgevoerd.

- Het automatiseringsaccount bevindt zich in elke Azure-regio en moet zich in hetzelfde abonnement bevinden als de kluis Siteherstel. 
- Een runbook kan worden uitgevoerd in een herstelplan tijdens failover van een primaire locatie naar secundaire of tijdens failback van de secundaire locatie naar de primaire.
- Runbooks in een herstelplan worden serieel uitgevoerd, de ene na de andere, in de ingestelde volgorde.
- Als runbooks in een herstelplan VM's configureren om in verschillende groepen te starten, wordt het herstelplan alleen voortgezet wanneer Azure alle VM's als uitgevoerd rapporteert.
- Herstelplannen blijven worden uitgevoerd, zelfs als een script mislukt.

### <a name="recovery-plan-context"></a>Context van herstelplan

Wanneer een script wordt uitgevoerd, injecteert het een herstelplancontext in het runbook. De context bevat de variabelen die in de tabel zijn samengevat.

| **Naam van de variabele** | **Beschrijving** |
| --- | --- |
| RecoveryPlanName |Naam van het herstelplan. Wordt gebruikt in acties op basis van de naam. |
| FailoverType |Hiermee geeft u op of het een test- of productiefailover is. 
| FailoverRichting | Hiermee geeft u op of herstel zich op een primaire of secundaire locatie bevindt. |
| Groeps-ID |Hiermee wordt het groepsnummer in het herstelplan aangegeven wanneer het plan wordt uitgevoerd. |
| VmMap |Een array van alle VM's in de groep. |
| VMMap-toets |Een unieke sleutel (GUID) voor elke VM. |
| SubscriptionId |De Azure-abonnements-ID waarin de VM is gemaakt. |
| ResourceGroupName | Naam van de resourcegroep waarin de VM zich bevindt.
| CloudServiceName |De azure-naam van de cloudservice waaronder de VM is gemaakt. |
| RoleName |De naam van de Azure VM. |
| RecoveryPointId|De tijdstempel voor het VM-herstel. |

In het volgende voorbeeld wordt een contextvariabele weergegeven:

```
{"RecoveryPlanName":"hrweb-recovery",
"FailoverType":"Test",
"FailoverDirection":"PrimaryToSecondary",
"GroupId":"1",
"VmMap":{"7a1069c6-c1d6-49c5-8c5d-33bfce8dd183":
    { "SubscriptionId":"7a1111111-c1d6-49c5-8c5d-111ce8dd183",
    "ResourceGroupName":"ContosoRG",
    "CloudServiceName":"pod02hrweb-Chicago-test",
    "RoleName":"Fabrikam-Hrweb-frontend-test",
    "RecoveryPointId":"TimeStamp"}
    }
}
```

Als u alle VM's in VMMap in een lus wilt openen, u de volgende code gebruiken:

```
$VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
$vmMap = $RecoveryPlanContext.VmMap
    foreach($VMID in $VMinfo)
    {
        $VM = $vmMap.$VMID                
            if( !(($VM -eq $Null) -Or ($VM.ResourceGroupName -eq $Null) -Or ($VM.RoleName -eq $Null))) {
            #this check is to ensure that we skip when some data is not available else it will fail
    Write-output "Resource group name ", $VM.ResourceGroupName
    Write-output "Rolename " = $VM.RoleName
            }
        }
```


Aman Sharma's blog over bij [Harvesting Clouds](http://harvestingclouds.com) heeft een nuttig voorbeeld van een [herstelplan context script](http://harvestingclouds.com/post/script-sample-azure-automation-runbook-for-asr-recovery-plan/).



## <a name="before-you-start"></a>Voordat u begint

- Als azure automation nieuw is, u voorbeeldscripts [aanmelden](https://azure.microsoft.com/services/automation/) en [downloaden.](https://azure.microsoft.com/documentation/scripts/)
- Controleer of het automatiseringsaccount de volgende modules heeft:
    - AzureRM.profile
    - AzureRM.Resources
    - AzureRM.Automation
    - AzureRM.Network
    - AzureRM.Compute

    Alle modules moeten compatibel zijn. De eenvoudigste manier is om altijd gebruik te maken van de nieuwste versies van alle modules.



## <a name="customize-the-recovery-plan"></a>Het herstelplan aanpassen

1. Selecteer **herstelplannen (siteherstel)** in de kluis
2. Als u een herstelplan wilt maken, klikt u op **+Herstelplan**. [Meer informatie](site-recovery-create-recovery-plans.md). Als u al een herstelplan hebt, selecteert u deze om het te openen.
3. Klik op de pagina herstelplan op **Aanpassen**.

    ![Klik op de knop Aanpassen](media/site-recovery-runbook-automation-new/custom-rp.png)

2. Klik op de ellips (...) naast **Groep 1: Actie** > **Post toevoegen starten**.
3. Controleer in **Actie Invoegen**of **Script** is geselecteerd en geef een naam op voor het script (**Hello World**).
4. Geef een automatiseringsaccount op en selecteer een runbook. Als u het script wilt opslaan, klikt u op **OK**. Het script wordt toegevoegd aan **groep 1: Post-stappen**.


## <a name="reuse-a-runbook-script"></a>Een runbook-script opnieuw gebruiken

U één runbookscript gebruiken in meerdere herstelplannen met behulp van externe variabelen. 

- U gebruikt [Azure Automation-variabelen](../automation/automation-variables.md) om parameters op te slaan voor het uitvoeren van een herstelplan.
- Door de naam van het herstelplan toe te voegen als voorvoegsel aan de variabele, u afzonderlijke variabelen voor elk herstelplan maken. Gebruik vervolgens de variabelen als parameters.
- U een parameter wijzigen zonder het script te wijzigen, maar toch de manier waarop het script werkt wijzigen.

### <a name="use-a-simple-string-variable-in-a-runbook-script"></a>Een eenvoudige tekenreeksvariabele gebruiken in een runbook-script

In dit voorbeeld neemt een script de invoer van een Network Security Group (NSG) en past het toe op de VM's in een herstelplan. 

1. Gebruik deze context van het herstelplan zodat het script kan detecteren welk herstelplan wordt uitgevoerd:

    ```
    workflow AddPublicIPAndNSG {
        param (
              [parameter(Mandatory=$false)]
              [Object]$RecoveryPlanContext
        )

        $RPName = $RecoveryPlanContext.RecoveryPlanName
    ```

2. Let op de NSG-naam en resourcegroep. U gebruikt deze variabelen als invoer voor scripts voor herstelplannen. 
1. In de activa van de Automatiseringsrekening. een variabele maken om de NSG-naam op te slaan. Voeg een voorvoegsel toe aan de variabele naam met de naam van het herstelplan.

    ![Een NSG-naamvariabele maken](media/site-recovery-runbook-automation-new/var1.png)

2. Maak een variabele om de naam van de resourcegroep op te slaan voor de NSG-bron. Voeg een voorvoegsel toe aan de variabele naam met de naam van het herstelplan.

    ![Een nsg-brongroepnaam maken](media/site-recovery-runbook-automation-new/var2.png)


3.  Gebruik deze referentiecode in het script om de variabele waarden op te halen:

    ```
    $NSGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSG"
    $NSGRGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSGRG"

    $NSGnameVar = Get-AutomationVariable -Name $NSGValue
    $RGnameVar = Get-AutomationVariable -Name $NSGRGValue
    ```

4.  Gebruik de variabelen in het runbook om de NSG toe te passen op de netwerkinterface van de mislukte VM:

    ```
    InlineScript {
    if (($Using:NSGname -ne $Null) -And ($Using:NSGRGname -ne $Null)) {
            $NSG = Get-AzureRmNetworkSecurityGroup -Name $Using:NSGname -ResourceGroupName $Using:NSGRGname
            Write-output $NSG.Id
            #Apply the NSG to a network interface
            #$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
            #Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd `
            #  -AddressPrefix 192.168.1.0/24 -NetworkSecurityGroup $NSG
        }
    }
    ```


Maak voor elk herstelplan onafhankelijke variabelen, zodat u het script opnieuw gebruiken. Voeg een voorvoegsel toe met de naam van het herstelplan. 

Voor een volledig end-to-end script voor dit scenario, bekijk [dit script](https://gallery.technet.microsoft.com/Add-Public-IP-and-NSG-to-a6bb8fee).


### <a name="use-a-complex-variable-to-store-more-information"></a>Een complexe variabele gebruiken om meer informatie op te slaan

In sommige scenario's u mogelijk geen afzonderlijke variabelen maken voor elk herstelplan. Overweeg een scenario waarin u één script wilt toewijzen aan een openbaar IP-adres op specifieke VM's. In een ander scenario wilt u misschien verschillende NSG's toepassen op verschillende VM's (niet op alle VM's). Opmerking:

- U een script maken dat herbruikbaar is voor elk herstelplan.
- Elk herstelplan kan een variabel aantal VM's hebben.
- Een SharePoint-herstel heeft bijvoorbeeld twee front-ends. Een lob-toepassing (basic line-of-business) heeft slechts één front-end.
- In dit scenario u geen afzonderlijke variabelen maken voor elk herstelplan.

In het volgende voorbeeld maken we een [complexe variabele](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azureautomationvariable) in het Azure Automation-account.

Dit doen we door meerdere waarden op te geven met Azure PowerShell.

1. Meld u in PowerShell aan bij uw Azure-abonnement:

    ```
    Connect-AzureRmAccount
    $sub = Get-AzureRmSubscription -Name <SubscriptionName>
    $sub | Select-AzureRmSubscription
    ```

2. Als u de parameters wilt opslaan, maakt u de complexe variabele met de naam van het herstelplan:

    ```
    $VMDetails = @{"VMGUID"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"};"VMGUID2"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"}}
        New-AzureRmAutomationVariable -ResourceGroupName <RG of Automation Account> -AutomationAccountName <AA Name> -Name <RecoveryPlanName> -Value $VMDetails -Encrypted $false
    ```

3. In deze complexe variabele is **VMDetails** de VM-id voor de beveiligde vm. Als u de VM-id wilt krijgen, u in de Azure-portal de VM-eigenschappen weergeven. De volgende schermafbeelding toont een variabele die de details van twee VM's opslaat:

    ![De VM-id als GUID gebruiken](media/site-recovery-runbook-automation-new/vmguid.png)

4. Gebruik deze variabele in je runbook. Als de aangegeven VM GUID wordt gevonden in de context van het herstelplan, past u de NSG toe op de VM:

    ```
    $VMDetailsObj = (Get-AutomationVariable -Name $RecoveryPlanContext.RecoveryPlanName).ToObject([hashtable])
    ```

4. Loop in uw runbook door de VM's van de context van het herstelplan. Controleer of de vm in **$VMDetailsObj**bestaat. Als deze bestaat, krijgt u toegang tot de eigenschappen van de variabele om de NSG toe te passen:

    ```
        $VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
        $vmMap = $RecoveryPlanContext.VmMap

        foreach($VMID in $VMinfo) {
            $VMDetails = $VMDetailsObj[$VMID].ToObject([hashtable]);
            Write-output $VMDetails
            if ($VMDetails -ne $Null) { #If the VM exists in the context, this will not be Null
                $VM = $vmMap.$VMID
                # Access the properties of the variable
                $NSGname = $VMDetails.NSGName
                $NSGRGname = $VMDetails.NSGResourceGroupName

                # Add code to apply the NSG properties to the VM
            }
        }
    ```

U hetzelfde script gebruiken voor verschillende herstelplannen. Voer verschillende parameters in door de waarde die overeenkomt met een herstelplan op te slaan in verschillende variabelen.

## <a name="sample-scripts"></a>Voorbeeldscripts

Als u voorbeeldscripts wilt implementeren voor uw automatiseringsaccount, klikt u op de knop **Implementeren naar Azure.**

[![Implementeren naar Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

Deze video geeft een ander voorbeeld. Het laat zien hoe je een WordPress-toepassing met twee lagen herstellen naar Azure:


> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over een [Azure Automation Run As-account](../automation/automation-create-runas-account.md)
- Voorbeeldscripts [voor Azure Automation controleren](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=User&f%5B0%5D.Value=SC%20Automation%20Product%20Team&f%5B0%5D.Text=SC%20Automation%20Product%20Team).
- [Meer informatie](site-recovery-failover.md) over het uitvoeren van failovers.



