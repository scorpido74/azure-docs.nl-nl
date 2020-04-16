---
title: Maak een gedeelde self-hosted integratieruntime met PowerShell
description: Meer informatie over het maken van een runtime voor gedeelde zelfgehoste integratie in Azure Data Factory, zodat meerdere gegevensfabrieken toegang hebben tot de runtime van de integratie.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: abnarain
author: nabhishek
manager: anansub
ms.custom: seo-lt-2019
ms.date: 10/31/2018
ms.openlocfilehash: cabdb45467f71749184c5f9a6a112242a82d618b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416607"
---
# <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory"></a>Een runtime voor gedeelde zelfgehoste integratie maken in Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In deze handleiding ziet u hoe u een gedeelde zelfgehoste runtime voor zelfgehoste integratie maakt in Azure Data Factory. Vervolgens u de runtime voor gedeelde zelfgehoste integratie gebruiken in een andere gegevensfabriek.

## <a name="create-a-shared-self-hosted-ir-using-azure-data-factory-ui"></a>Een gedeelde, zelf gehoste IR maken met azure datafactory-gebruikersinterface

Als u een gedeelde, zelf gehoste IR wilt maken met azure datafactory-gebruikersinterface, u de volgende stappen uitvoeren:

1. Geef in de zelf gehoste IR toestemming aan de gegevensfabriek waarin u de gekoppelde IR wilt maken.
      
    ![Knop voor het verlenen van toestemming op het tabblad Delen](media/create-self-hosted-integration-runtime/grant-permissions-IR-sharing.png)
      
    ![Selecties voor het toewijzen van machtigingen](media/create-self-hosted-integration-runtime/3_rbac_permissions.png)     
    
2. Let op de resource-ID van de zelf gehoste IR die moet worden gedeeld.
      
   ![Locatie van de resource-id](media/create-self-hosted-integration-runtime/4_ResourceID_self-hostedIR.png)
    
3. Maak in de gegevensfabriek waaraan de machtigingen zijn verleend een nieuwe zelfgehoste IR (gekoppeld) en voert u de resource-id in.
      
   ![Knop voor het maken van een gekoppelde self-hosted integratieruntime](media/create-self-hosted-integration-runtime/6_create-linkedIR_2.png)
      
    ![Vakken voor naam en resource-id](media/create-self-hosted-integration-runtime/6_create-linkedIR_3.png)

## <a name="create-a-shared-self-hosted-ir-using-azure-powershell"></a>Een gedeelde, zelf gehoste IR maken met Azure PowerShell

Als u een gedeelde zelfgehoste IR wilt maken met Azure PowerShell, u de volgende stappen uitvoeren: 
1. Een gegevensfactory maken. 
1. Een zelf-hostende Integration Runtime maken.
1. Deel de self-hosted integratieruntime met andere datafabrieken.
1. Maak een runtime voor gekoppelde integratie.
1. Het delen intrekken.

### <a name="prerequisites"></a>Vereisten 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure-abonnement**. Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint. 

- **Azure PowerShell**. Volg de instructies in [Azure PowerShell installeren op Windows met PowerShellGet](https://docs.microsoft.com/powershell/azure/install-az-ps). U gebruikt PowerShell om een script uit te voeren om een zelfgehoste runtime voor integratie te maken die kan worden gedeeld met andere gegevensfabrieken. 

> [!NOTE]  
> Selecteer de regio's die u interesseren voor producten die beschikbaar zijn [per regio](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory)voor een lijst met Azure-regio's waarin gegevensfabriek momenteel beschikbaar is.

### <a name="create-a-data-factory"></a>Een gegevensfactory maken

1. Start Windows PowerShell ISE (Integrated Scripting Environment).

1. Variabelen maken. Kopieer en plak het volgende script. Vervang de variabelen, zoals **SubscriptionName** en **ResourceGroupName,** door werkelijke waarden: 

    ```powershell
    # If input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$". 
    $SubscriptionName = "[Azure subscription name]" 
    $ResourceGroupName = "[Azure resource group name]" 
    $DataFactoryLocation = "EastUS" 

    # Shared Self-hosted integration runtime information. This is a Data Factory compute resource for running any activities 
    # Data factory name. Must be globally unique 
    $SharedDataFactoryName = "[Shared Data factory name]" 
    $SharedIntegrationRuntimeName = "[Shared Integration Runtime Name]" 
    $SharedIntegrationRuntimeDescription = "[Description for Shared Integration Runtime]"

    # Linked integration runtime information. This is a Data Factory compute resource for running any activities
    # Data factory name. Must be globally unique
    $LinkedDataFactoryName = "[Linked Data factory name]"
    $LinkedIntegrationRuntimeName = "[Linked Integration Runtime Name]"
    $LinkedIntegrationRuntimeDescription = "[Description for Linked Integration Runtime]"
    ```

1. Meld u aan en selecteer een abonnement. Voeg de volgende code toe aan het script om u aan te melden en selecteer uw Azure-abonnement:

    ```powershell
    Connect-AzAccount
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

1. Maak een resourcegroep en een gegevensfabriek.

    > [!NOTE]  
    > Deze stap is optioneel. Als u al een gegevensfabriek hebt, slaat u deze stap over. 

    Maak een [Azure-brongroep](../azure-resource-manager/management/overview.md) met de opdracht [Nieuw-AzResourceGroep.](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en groepsgewijs worden beheerd. In het volgende voorbeeld `myResourceGroup` wordt een resourcegroep genamed op de locatie West-Europa: 

    ```powershell
    New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
    ```

    Voer de volgende opdracht uit om een data factory te maken: 

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                             -Location $DataFactoryLocation `
                             -Name $SharedDataFactoryName
    ```

### <a name="create-a-self-hosted-integration-runtime"></a>Een zelf-hostende Integration Runtime maken

> [!NOTE]  
> Deze stap is optioneel. Als u al de zelf gehoste runtime voor integratie hebt die u wilt delen met andere gegevensfabrieken, slaat u deze stap over.

Voer de volgende opdracht uit om een zelf gehoste nawerking van integratie te maken:

```powershell
$SharedIR = Set-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Type SelfHosted `
    -Description $SharedIntegrationRuntimeDescription
```

#### <a name="get-the-integration-runtime-authentication-key-and-register-a-node"></a>De verificatiesleutel voor de integratieruntijd oppakken en een knooppunt registreren

Voer de volgende opdracht uit om de verificatiesleutel voor de zelfgehoste runtime voor integratie op te halen:

```powershell
Get-AzDataFactoryV2IntegrationRuntimeKey `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName
```

Het antwoord bevat de verificatiesleutel voor deze zelf gehoste runtime voor integratie. U gebruikt deze sleutel wanneer u het runtime-knooppunt voor integratie registreert.

#### <a name="install-and-register-the-self-hosted-integration-runtime"></a>De runtime voor zelf gehoste integratie installeren en registreren

1. Download het self-hosted integration runtime installer van [Azure Data Factory Integration Runtime](https://aka.ms/dmg).

2. Voer het installatieprogramma uit om de zelfgehoste integratie op een lokale computer te installeren.

3. Registreer de nieuwe zelfgehoste integratie met de verificatiesleutel die u in een vorige stap hebt opgehaald.

### <a name="share-the-self-hosted-integration-runtime-with-another-data-factory"></a>Deel de self-hosted integratieruntime met een andere gegevensfabriek

#### <a name="create-another-data-factory"></a>Een andere gegevensfabriek maken

> [!NOTE]  
> Deze stap is optioneel. Als u de gegevensfabriek waarmee u wilt delen al hebt, slaat u deze stap over.

```powershell
$factory = Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $LinkedDataFactoryName
```
#### <a name="grant-permission"></a>Toestemming

Geef toestemming aan de gegevensfabriek die toegang nodig heeft om toegang te krijgen tot de zelf gehoste runtime voor integratie die u hebt gemaakt en geregistreerd.

> [!IMPORTANT]  
> Sla deze stap niet over!

```powershell
New-AzRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId ` #MSI of the Data Factory with which it needs to be shared
    -RoleDefinitionId 'b24988ac-6180-42a0-ab88-20f7382dd24c' ` #This is the Contributor role
    -Scope $SharedIR.Id
```

### <a name="create-a-linked-self-hosted-integration-runtime"></a>Runtime voor gekoppelde self-hosted integratie maken

Voer de volgende opdracht uit om een gekoppelde self-hosted integration runtime te maken:

```powershell
Set-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $LinkedDataFactoryName `
    -Name $LinkedIntegrationRuntimeName `
    -Type SelfHosted `
    -SharedIntegrationRuntimeResourceId $SharedIR.Id `
    -Description $LinkedIntegrationRuntimeDescription
```

Nu u deze gekoppelde integratieruntime gebruiken in elke gekoppelde service. De runtime voor gekoppelde integratie gebruikt de runtime voor gedeelde integratie om activiteiten uit te voeren.

### <a name="revoke-integration-runtime-sharing-from-a-data-factory"></a>Integratieruntimedelen intrekken vanuit een gegevensfabriek

Voer de volgende opdracht uit om de toegang van een gegevensfabriek in te trekken uit de runtime voor gedeelde integratie:

```powershell
Remove-AzRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId `
    -RoleDefinitionId 'b24988ac-6180-42a0-ab88-20f7382dd24c' `
    -Scope $SharedIR.Id
```

Voer de volgende opdracht uit tegen de runtime van gedeelde integratie als u de huidige runtime voor gekoppelde integratie wilt verwijderen:

```powershell
Remove-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Links `
    -LinkedDataFactoryName $LinkedDataFactoryName
```

### <a name="next-steps"></a>Volgende stappen

- Bekijk [runtime-concepten voor integratie in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/concepts-integration-runtime).

- Meer informatie over het [maken van een zelf gehoste runtime voor integratie in de Azure-portal.](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
