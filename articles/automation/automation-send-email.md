---
title: Een e-mail verzenden vanuit een Azure Automation runbook
description: Meer informatie over hoe u SendGrid kunt gebruiken om vanuit een runbook een e-mail te verzenden.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 07/15/2019
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: 8550635b581eb944719c39cc8c195859a2c9e868
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849612"
---
# <a name="tutorial-send-an-email-from-an-azure-automation-runbook"></a>Zelf studie: een e-mail verzenden vanuit een Azure Automation runbook

U kunt een e-mail verzenden vanuit een runbook met [SendGrid](https://sendgrid.com/solutions) met behulp van Power shell. In deze zelf studie wordt uitgelegd hoe u een herbruikbaar runbook maakt waarmee een e-mail wordt verzonden met behulp van een API-sleutel die is opgeslagen in azure-hoofd [kluis](/azure/key-vault/).

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Een Azure-sleutel kluis maken
> * Uw SendGrid-API-sleutel opslaan in de hoofd kluis
> * Een runbook maken waarmee uw API-sleutel wordt opgehaald en een e-mail wordt verzonden

## <a name="prerequisites"></a>Vereisten

Hieronder wordt aangegeven wat de vereisten zijn om deze zelfstudie te voltooien:

* Azure-abonnement: als u er nog geen hebt, kunt u [uw voor delen van uw MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Maak een SendGrid-account](/azure/sendgrid-dotnet-how-to-send-email#create-a-sendgrid-account).
* [Automation-account](automation-offering-get-started.md) met **AZ** -modules en [uitvoeren als-verbinding](automation-create-runas-account.md)om het runbook op te slaan en uit te voeren.

## <a name="create-an-azure-keyvault"></a>Een Azure-sleutel kluis maken

U kunt een Azure-sleutel kluis maken met behulp van het volgende Power shell-script. Vervang de variabele waarden door de waarden die specifiek zijn voor uw omgeving. Gebruik de Inge sloten Azure Cloud Shell via de knop <kbd>try it</kbd> in de rechter bovenhoek van het code blok. U kunt de code ook lokaal kopiëren en uitvoeren als u de [Module Azure PowerShell](/powershell/azure/install-az-ps) hebt geïnstalleerd op uw lokale machine.

> [!NOTE]
> Als u uw API-sleutel wilt ophalen, gebruikt u de stappen in [uw SendGrid-API-sleutel zoeken](/azure/sendgrid-dotnet-how-to-send-email#to-find-your-sendgrid-api-key).

```azurepowershell-interactive
$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
# If you omit the SubscriptionId parameter, the default subscription is selected.
Connect-AzAccount -SubscriptionId $SubscriptionId

# Use Get-AzLocation to see your available locations.
$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "<Enter a universally unique vault name>"
$SendGridAPIKey = "<SendGrid API key>"
$AutomationAccountName = "testaa"

# Create new Resource Group, or omit this step if you already have a resource group.
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName -Location $region
$resourceId = $newKeyVault.ResourceId

# Convert the SendGrid API key into a SecureString
$Secret = ConvertTo-SecureString -String $SendGridAPIKey -AsPlainText -Force
Set-AzKeyVaultSecret -VaultName $VaultName -Name 'SendGridAPIKey' -SecretValue $Secret

# Grant access to the KeyVault to the Automation RunAs account.
$connection = Get-AzAutomationConnection -ResourceGroupName $KeyVaultResourceGroupName -AutomationAccountName $AutomationAccountName -Name AzureRunAsConnection
$appID = $connection.FieldDefinitionValues.ApplicationId
Set-AzKeyVaultAccessPolicy -VaultName $VaultName -ServicePrincipalName $appID -PermissionsToSecrets Set, Get
```

Raadpleeg voor andere manieren om een Azure-sleutel kluis te maken en een geheim op te slaan de Snelstartgids voor de sleutel [kluis](/azure/key-vault/).

## <a name="import-required-modules-to-your-automation-account"></a>De vereiste modules importeren in uw Automation-account

Als u Azure-sleutel kluis binnen een runbook wilt gebruiken, hebt u de volgende modules nodig voor uw Automation-account:

* [AZ. profile](https://www.powershellgallery.com/packages/Az.Profile).
* [AZ. sleutel kluis](https://www.powershellgallery.com/packages/Az.KeyVault).

Klik op <kbd>implementeren naar Azure Automation</kbd> op het tabblad Azure Automation onder installatie opties. Met deze actie wordt de Azure Portal geopend. Selecteer uw Automation-account op de pagina importeren en klik op <kbd>OK</kbd>.

Zie [modules importeren](/azure/automation/shared-resources/modules#import-modules)voor aanvullende methoden voor het toevoegen van de vereiste modules.

## <a name="create-the-runbook-to-send-an-email"></a>Het runbook maken om een e-mail te verzenden

Nadat u een SendGrid hebt gemaakt en uw API-sleutel van uw toepassing hebt opgeslagen, is het tijd om het runbook te maken waarmee de API-sleutel wordt opgehaald en een e-mail wordt verzonden.

Dit runbook maakt gebruik van het [uitvoeren als-account](automation-create-runas-account.md) van AzureRunAsConnection om te verifiëren met Azure om het geheim op te halen uit Azure-sleutel kluis.

Gebruik dit voor beeld om een runbook met de naam **Send-GridMailMessage**te maken. U kunt het Power shell-script wijzigen en het opnieuw gebruiken voor verschillende scenario's.

1. Ga naar uw Azure Automation-account.
2. Selecteer **Runbooks**onder **proces automatisering**.
3. Selecteer boven aan de lijst met runbooks **+ een Runbook maken**.
4. Voer op de pagina **Runbook toevoegen** de waarde **Send-GridMailMessage** in voor de runbooknaam. Selecteer voor het type runbook **Power shell**. Ten slotte selecteert u **Create**.
   ![Runbook maken](./media/automation-send-email/automation-send-email-runbook.png)
5. Het runbook is gemaakt en de pagina **PowerShell-runbook bewerken** wordt geopend.
   het Runbook ![bewerken](./media/automation-send-email/automation-send-email-edit.png)
6. Kopieer het volgende Power shell-voor beeld naar de pagina **bewerken** . Zorg ervoor dat de `$VaultName` de naam is die u hebt opgegeven tijdens het maken van de sleutel kluis.

    ```powershell-interactive
    Param(
      [Parameter(Mandatory=$True)]
      [String] $destEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $fromEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $subject,
      [Parameter(Mandatory=$True)]
      [String] $content
    )

    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Out-Null
    $VaultName = "<Enter your vault name>"
    $SENDGRID_API_KEY = (Get-AzKeyVaultSecret -VaultName $VaultName -Name "SendGridAPIKey").SecretValueText
    $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
    $headers.Add("Authorization", "Bearer " + $SENDGRID_API_KEY)
    $headers.Add("Content-Type", "application/json")

    $body = @{
    personalizations = @(
        @{
            to = @(
                    @{
                        email = $destEmailAddress
                    }
            )
        }
    )
    from = @{
        email = $fromEmailAddress
    }
    subject = $subject
    content = @(
        @{
            type = "text/plain"
            value = $content
        }
    )
    }

    $bodyJson = $body | ConvertTo-Json -Depth 4

    $response = Invoke-RestMethod -Uri https://api.sendgrid.com/v3/mail/send -Method Post -Headers $headers -Body $bodyJson
    ```

7. Selecteer **publiceren** om het runbook op te slaan en te publiceren.

Als u wilt controleren of het runbook is uitgevoerd, kunt u de stappen volgen onder [een Runbook testen](manage-runbooks.md#test-a-runbook) of [een runbook starten](start-runbooks.md).
Als u de test-e-mail niet in eerste instantie ziet, controleert u de map **ongewenste e-mail** en **spam** .

## <a name="clean-up"></a>Opruimen

Wanneer u het runbook niet meer nodig hebt, kunt u het verwijderen. Om dit te doen, selecteert u het runbook in de runbooklijst en klikt u op **Verwijderen**.

Verwijder de sleutel kluis met behulp van de cmdlet [Remove-AzureRMKeyVault](/powershell/module/azurerm.keyvault/remove-azurermkeyvault?view=azurermps) .

```azurepowershell-interactive
$VaultName = "<your KeyVault name>"
$ResourceGroupName = "<your ResourceGroup name>"
Remove-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $ResourceGroupName
```

## <a name="next-steps"></a>Volgende stappen

* Zie [fouten met Runbooks oplossen](./troubleshoot/runbooks.md)voor problemen met het maken of starten van uw runbook.
* Zie [Azure PowerShell-modules bijwerken in azure Automation](automation-update-azure-modules.md)] voor informatie over het bijwerken van modules in uw Automation-account.
* Zie [taak status en taak stromen door sturen naar Azure monitor logboeken](automation-manage-send-joblogs-log-analytics.md)om de uitvoering van het runbook te controleren.
* Zie een [waarschuwing gebruiken om een Azure Automation runbook te activeren](automation-create-alert-triggered-runbook.md)als u een runbook wilt activeren met behulp van een waarschuwing.
