---
title: Een e-mail verzenden vanuit een Azure Automation-runbook
description: Meer informatie over het gebruik van SendGrid om een e-mail te verzenden vanuit een runbook.
services: automation
ms.subservice: process-automation
ms.date: 07/15/2019
ms.topic: tutorial
ms.openlocfilehash: d4b35458c76da82b33dfcb530cfdc71ee3da3bb6
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604787"
---
# <a name="tutorial-send-an-email-from-an-azure-automation-runbook"></a>Zelfstudie: Een e-mail verzenden vanuit een Azure Automation-runbook

U een e-mail verzenden vanuit een runbook met [SendGrid](https://sendgrid.com/solutions) met PowerShell. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Maak een Azure Key Vault.
> * Bewaar `SendGrid` uw API-sleutel in de sleutelkluis.
> * Maak een herbruikbare runbook waarmee uw API-sleutel wordt opgehaald en een e-mail wordt verzendt met een API-sleutel die is opgeslagen in [Azure Key Vault.](/azure/key-vault/)

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor installatie-instructies voor az-modules op uw hybride runbookworker. Voor uw Automatiseringsaccount u uw modules bijwerken naar de nieuwste versie met [Azure PowerShell-modules bijwerken in Azure Automation.](automation-update-azure-modules.md)

## <a name="prerequisites"></a>Vereisten

Hieronder wordt aangegeven wat de vereisten zijn om deze zelfstudie te voltooien:

* Azure-abonnement: Als u er nog geen hebt, u uw VOORDELEN voor [MSDN-abonnees activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of u aanmelden voor een [gratis account.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Een SendGrid-account maken](/azure/sendgrid-dotnet-how-to-send-email#create-a-sendgrid-account).
* [Automatiseringsaccount](automation-offering-get-started.md) met **Az-modules** en [Run As-verbinding](automation-create-runas-account.md)om het runbook op te slaan en uit te voeren.

## <a name="create-an-azure-key-vault"></a>Een Azure Key Vault maken

U een Azure Key Vault maken met het volgende PowerShell-script. Vervang de variabele waarden door waarden die specifiek zijn voor uw omgeving. Gebruik de ingesloten Azure Cloud Shell via de knop **Try It,** in de rechterbovenhoek van het codeblok. U de code ook lokaal kopiëren en uitvoeren als u de [Azure PowerShell-module](/powershell/azure/install-az-ps) op uw lokale machine hebt geïnstalleerd.

> [!NOTE]
> Als u uw API-sleutel wilt ophalen, gebruikt u de stappen in [Zoek uw SendGrid API-sleutel](/azure/sendgrid-dotnet-how-to-send-email#to-find-your-sendgrid-api-key).

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

Zie [Key Vault Quickstarts](/azure/key-vault/)voor andere manieren om een Azure Key Vault te maken en een geheim op te slaan.

## <a name="import-required-modules-to-your-automation-account"></a>Vereiste modules importeren in uw Automatiseringsaccount

Als u Azure Key Vault in een runbook wilt gebruiken, heeft uw Automatiseringsaccount de volgende modules nodig:

* [Az.Profile](https://www.powershellgallery.com/packages/Az.Profile)
* [Az.KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault)

Klik **op Implementeren voor Azure Automation** op het tabblad Azure Automation onder **Installatieopties**. Met deze actie wordt de Azure-portal geopend. Selecteer op de pagina Importeren uw automatiseringsaccount en klik op **OK**.

Zie [Modules importeren](/azure/automation/shared-resources/modules#importing-modules)voor aanvullende methoden voor het toevoegen van de vereiste modules.

## <a name="create-the-runbook-to-send-an-email"></a>Het runbook maken om een e-mail te verzenden

Nadat u een sleutelkluis hebt `SendGrid` gemaakt en uw API-sleutel hebt opgeslagen, is het tijd om het runbook te maken waarmee de API-sleutel wordt opgehaald en een e-mail wordt verzendt.

Deze runbook `AzureRunAsConnection` wordt gebruikt als [een Run As-account](automation-create-runas-account.md) om te verifiëren met Azure om het geheim uit Azure Key Vault op te halen.

Gebruik dit voorbeeld om een runbook te maken met de naam **Send-GridMailMessage**. U het PowerShell-script wijzigen en opnieuw gebruiken voor verschillende scenario's.

1. Ga naar uw Azure Automation-account.
2. Selecteer **Runbooks**onder **Procesautomatisering**.
3. Selecteer boven aan de lijst met runbooks de optie **+ Maak een runbook**.
4. Voer op de pagina **Runbook toevoegen** in **Send-GridMailMessage** in voor de naam van het runbook. Selecteer **PowerShell**voor het type runbook . Ten slotte selecteert u **Create**.
   ![Runbook maken](./media/automation-send-email/automation-send-email-runbook.png)
5. Het runbook is gemaakt en de pagina **PowerShell-runbook bewerken** wordt geopend.
   ![Het runbook bewerken](./media/automation-send-email/automation-send-email-edit.png)
6. Kopieer het volgende PowerShell-voorbeeld naar de pagina **Bewerken.** Zorg ervoor `$VaultName` dat dit de naam is die u hebt opgegeven toen u uw sleutelkluis maakte.

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

7. Selecteer **Publiceren** om het runbook op te slaan en te publiceren.

Als u wilt controleren of de runbook succesvol wordt uitgevoerd, u de stappen volgen onder [Een runbook testen](manage-runbooks.md#testing-a-runbook) of [Een runbook starten.](start-runbooks.md)
Als je je test-e-mail in eerste instantie niet ziet, controleer je de mappen **Ongewenste e-mail** en **spam.**

## <a name="clean-up"></a>Opschonen

Wanneer u het runbook niet meer nodig hebt, kunt u het verwijderen. Om dit te doen, selecteert u het runbook in de runbooklijst en klikt u op **Verwijderen**.

Verwijder de sleutelkluis met de cmdlet [Remove-AzKeyVault.](https://docs.microsoft.com/powershell/module/az.keyvault/remove-azkeyvault?view=azps-3.7.0)

```azurepowershell-interactive
$VaultName = "<your KeyVault name>"
$ResourceGroupName = "<your ResourceGroup name>"
Remove-AzKeyVault -VaultName $VaultName -ResourceGroupName $ResourceGroupName
```

## <a name="next-steps"></a>Volgende stappen

* Zie Fouten oplossen met runbooks voor problemen met het maken of starten van uw [runbook.](./troubleshoot/runbooks.md)
* Zie [Azure PowerShell-modules bijwerken in Azure Automation](automation-update-azure-modules.md)als u modules in uw Automatiseringsaccount wilt bijwerken.
* Zie [Taakstatus en taakstromen doorsturen van Automatisering naar Azure Monitor-logboeken als](automation-manage-send-joblogs-log-analytics.md)u de uitvoering van runbook wilt controleren.
* Zie Een waarschuwing gebruiken om [een Azure Automation-runbook te activeren](automation-create-alert-triggered-runbook.md)als u een runbook wilt activeren met een waarschuwing.
