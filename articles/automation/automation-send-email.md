---
title: Een e-mail verzenden vanuit een Azure Automation runbook
description: Meer informatie over hoe u SendGrid kunt gebruiken om vanuit een runbook een e-mail te verzenden.
services: automation
ms.subservice: process-automation
ms.date: 07/15/2019
ms.topic: tutorial
ms.openlocfilehash: 4d825dee469497cbb56a91c913ff3ac51963058b
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82855685"
---
# <a name="tutorial-send-an-email-from-an-azure-automation-runbook"></a>Zelf studie: een e-mail verzenden vanuit een Azure Automation runbook

U kunt een e-mail verzenden vanuit een runbook met [SendGrid](https://sendgrid.com/solutions) met behulp van Power shell. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Maak een Azure Key Vault.
> * Sla uw `SendGrid` API-sleutel op in de sleutel kluis.
> * Maak een herbruikbaar runbook waarmee uw API-sleutel wordt opgehaald en een e-mail wordt verzonden met behulp van een API-sleutel die is opgeslagen in [Azure Key Vault](/azure/key-vault/).

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [de module Azure PowerShell installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor de installatie-instructies voor AZ module op uw Hybrid Runbook Worker. Voor uw Automation-account kunt u uw modules bijwerken naar de nieuwste versie met behulp van [het bijwerken van Azure PowerShell-modules in azure Automation](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Vereisten

Hieronder wordt aangegeven wat de vereisten zijn om deze zelfstudie te voltooien:

* Azure-abonnement: als u er nog geen hebt, kunt u [uw voor delen van uw MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Maak een SendGrid-account](/azure/sendgrid-dotnet-how-to-send-email#create-a-sendgrid-account).
* [Automation-account](automation-offering-get-started.md) met **AZ** -modules en [uitvoeren als-verbinding](automation-create-runas-account.md)om het runbook op te slaan en uit te voeren.

## <a name="create-an-azure-key-vault"></a>Een Azure Key Vault maken

U kunt een Azure Key Vault maken met behulp van het volgende Power shell-script. Vervang de variabele waarden door de waarden die specifiek zijn voor uw omgeving. Gebruik de Inge sloten Azure Cloud Shell via de knop **try it** in de rechter bovenhoek van het code blok. U kunt de code ook lokaal kopiëren en uitvoeren als u de [Module Azure PowerShell](/powershell/azure/install-az-ps) hebt geïnstalleerd op uw lokale machine.

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

# Grant access to the Key Vault to the Automation Run As account.
$connection = Get-AzAutomationConnection -ResourceGroupName $KeyVaultResourceGroupName -AutomationAccountName $AutomationAccountName -Name AzureRunAsConnection
$appID = $connection.FieldDefinitionValues.ApplicationId
Set-AzKeyVaultAccessPolicy -VaultName $VaultName -ServicePrincipalName $appID -PermissionsToSecrets Set, Get
```

Zie [Key Vault Quick](/azure/key-vault/)starts voor andere manieren om een Azure Key Vault te maken en een geheim op te slaan.

## <a name="import-required-modules-to-your-automation-account"></a>De vereiste modules importeren in uw Automation-account

Als u Azure Key Vault binnen een runbook wilt gebruiken, moet uw Automation-account de volgende modules hebben:

* [Az.Profile](https://www.powershellgallery.com/packages/Az.Profile)
* [Az.KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault)

Klik op **implementeren naar Azure Automation** op het tabblad Azure Automation onder **installatie opties**. Met deze actie wordt de Azure Portal geopend. Selecteer uw Automation-account op de pagina importeren en klik op **OK**.

Zie [modules importeren](/azure/automation/shared-resources/modules#importing-modules)voor aanvullende methoden voor het toevoegen van de vereiste modules.

## <a name="create-the-runbook-to-send-an-email"></a>Het runbook maken om een e-mail te verzenden

Nadat u een sleutel kluis hebt gemaakt en uw `SendGrid` API-sleutel hebt opgeslagen, is het tijd om het runbook te maken waarmee de API-sleutel wordt opgehaald en een e-mail wordt verzonden.

Dit runbook wordt `AzureRunAsConnection` gebruikt als een [uitvoeren als-account](automation-create-runas-account.md) om te verifiëren bij Azure om het geheim op te halen uit Azure Key Vault.

Gebruik dit voor beeld om een runbook met de naam **Send-GridMailMessage**te maken. U kunt het Power shell-script wijzigen en het opnieuw gebruiken voor verschillende scenario's.

1. Ga naar uw Azure Automation-account.
2. Selecteer **Runbooks**onder **proces automatisering**.
3. Selecteer boven aan de lijst met runbooks **+ een Runbook maken**.
4. Voer op de pagina **Runbook toevoegen** de waarde **Send-GridMailMessage** in voor de runbooknaam. Selecteer voor het type runbook **Power shell**. Ten slotte selecteert u **Create**.
   ![Runbook maken](./media/automation-send-email/automation-send-email-runbook.png)
5. Het runbook is gemaakt en de pagina **PowerShell-runbook bewerken** wordt geopend.
   ![Het Runbook bewerken](./media/automation-send-email/automation-send-email-edit.png)
6. Kopieer het volgende Power shell-voor beeld naar de pagina **bewerken** . Zorg ervoor dat `$VaultName` de naam is die u hebt opgegeven tijdens het maken van de sleutel kluis.

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

## <a name="clean-up"></a>Opschonen

Wanneer u het runbook niet meer nodig hebt, kunt u het verwijderen. Om dit te doen, selecteert u het runbook in de runbooklijst en klikt u op **Verwijderen**.

Verwijder de sleutel kluis met behulp van de cmdlet [Remove-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/remove-azkeyvault?view=azps-3.7.0) .

```azurepowershell-interactive
$VaultName = "<your KeyVault name>"
$ResourceGroupName = "<your ResourceGroup name>"
Remove-AzKeyVault -VaultName $VaultName -ResourceGroupName $ResourceGroupName
```

## <a name="next-steps"></a>Volgende stappen

* Zie [fouten met Runbooks oplossen](./troubleshoot/runbooks.md)voor problemen met het maken of starten van uw runbook.
* Zie [Azure PowerShell-modules bijwerken in azure Automation](automation-update-azure-modules.md)] voor informatie over het bijwerken van modules in uw Automation-account.
* Zie [taak status en taak stromen door sturen naar Azure monitor logboeken](automation-manage-send-joblogs-log-analytics.md)om de uitvoering van het runbook te controleren.
* Zie een [waarschuwing gebruiken om een Azure Automation runbook te activeren](automation-create-alert-triggered-runbook.md)als u een runbook wilt activeren met behulp van een waarschuwing.
