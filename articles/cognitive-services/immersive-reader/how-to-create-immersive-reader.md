---
title: Een insluitende lezerservaring maken
titleSuffix: Azure Cognitive Services
description: In dit artikel ziet u hoe u een nieuwe Immersive Reader-bron maakt met een aangepast subdomein en vervolgens Azure AD configureert in uw Azure-tenant.
services: cognitive-services
author: rwaller
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: rwaller
ms.openlocfilehash: 41efe4592c65ae3cdd85ce1b212554e50691905a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78330716"
---
# <a name="create-an-immersive-reader-resource-and-configure-azure-active-directory-authentication"></a>Een bron voor een meeslepende lezer maken en Azure Active Directory-verificatie configureren

In dit artikel bieden we een script waarmee een Immersive Reader-bron wordt gemaakt en Azure Active Directory(Azure AD)-verificatie wordt geconfigureerd. Elke keer dat een Immersive Reader-bron wordt gemaakt, of deze nu met dit script of in de portal wordt gebruikt, moet deze ook worden geconfigureerd met Azure AD-machtigingen. Dit script zal je daarbij helpen.

Het script is ontworpen om alle benodigde Immersive Reader- en Azure AD-resources voor u allemaal in één stap te maken en te configureren. U echter ook gewoon Azure AD-verificatie configureren voor een bestaande Immersive Reader-bron, als u er bijvoorbeeld al een hebt gemaakt in de Azure-portal.

Voor sommige klanten kan het nodig zijn om meerdere Immersive Reader-bronnen te maken, voor ontwikkeling versus productie, of misschien voor meerdere verschillende regio's waarin uw service is geïmplementeerd. Voor die gevallen u meerdere keren terugkomen en het script meerdere keren gebruiken om verschillende bronnen voor meeslepende lezers te maken en deze te configureren met de Azure AD-machtigingen.

Het script is ontworpen om flexibel te zijn. Het zal eerst zoeken naar bestaande Immersive Reader en Azure AD-bronnen in uw abonnement, en maak ze alleen als dat nodig is als ze nog niet bestaan. Als het de eerste keer is dat je een Immersive Reader-bron maakt, doet het script alles wat je nodig hebt. Als u het alleen wilt gebruiken om Azure AD te configureren voor een bestaande Immersive Reader-bron die in de portal is gemaakt, doet dit ook. Het kan ook worden gebruikt om meerdere Immersive Reader-bronnen te maken en te configureren.

## <a name="set-up-powershell-environment"></a>PowerShell-omgeving instellen

1. Begin met het openen van de [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview). Controleer of de cloudshell is ingesteld op PowerShell in de `pwsh`vervolgkeuzelijst linksboven of door te typen.

1. Kopieer en plak het volgende codefragment in de shell.

    ```azurepowershell-interactive
    function Create-ImmersiveReaderResource(
        [Parameter(Mandatory=$true, Position=0)] [String] $SubscriptionName,
        [Parameter(Mandatory=$true)] [String] $ResourceName,
        [Parameter(Mandatory=$true)] [String] $ResourceSubdomain,
        [Parameter(Mandatory=$true)] [String] $ResourceSKU,
        [Parameter(Mandatory=$true)] [String] $ResourceLocation,
        [Parameter(Mandatory=$true)] [String] $ResourceGroupName,
        [Parameter(Mandatory=$true)] [String] $ResourceGroupLocation,
        [Parameter(Mandatory=$true)] [String] $AADAppDisplayName="ImmersiveReaderAAD",
        [Parameter(Mandatory=$true)] [String] $AADAppIdentifierUri,
        [Parameter(Mandatory=$true)] [String] $AADAppClientSecret
    )
    {
        $unused = ''
        if (-not [System.Uri]::TryCreate($AADAppIdentifierUri, [System.UriKind]::Absolute, [ref] $unused)) {
            throw "Error: AADAppIdentifierUri must be a valid URI"
        }

        Write-Host "Setting the active subscription to '$SubscriptionName'"
        $subscriptionExists = Get-AzSubscription -SubscriptionName $SubscriptionName
        if (-not $subscriptionExists) {
            throw "Error: Subscription does not exist"
        }
        az account set --subscription $SubscriptionName

        $resourceGroupExists = az group exists --name $ResourceGroupName
        if ($resourceGroupExists -eq "false") {
            Write-Host "Resource group does not exist. Creating resource group"
            $groupResult = az group create --name $ResourceGroupName --location $ResourceGroupLocation
            if (-not $groupResult) {
                throw "Error: Failed to create resource group"
            }
            Write-Host "Resource group created successfully"
        }

        # Create an Immersive Reader resource if it doesn't already exist
        $resourceId = az cognitiveservices account show --resource-group $ResourceGroupName --name $ResourceName --query "id" -o tsv
        if (-not $resourceId) {
            Write-Host "Creating the new Immersive Reader resource '$ResourceName' (SKU '$ResourceSKU') in '$ResourceLocation' with subdomain '$ResourceSubdomain'"
            $resourceId = az cognitiveservices account create `
                            --name $ResourceName `
                            --resource-group $ResourceGroupName `
                            --kind ImmersiveReader `
                            --sku $ResourceSKU `
                            --location $ResourceLocation `
                            --custom-domain $ResourceSubdomain `
                            --query "id" `
                            -o tsv

            if (-not $resourceId) {
                throw "Error: Failed to create Immersive Reader resource"
            }
            Write-Host "Immersive Reader resource created successfully"
        }

        # Create an Azure Active Directory app if it doesn't already exist
        $clientId = az ad app show --id $AADAppIdentifierUri --query "appId" -o tsv
        if (-not $clientId) {
            Write-Host "Creating new Azure Active Directory app"
            $clientId = az ad app create --password $AADAppClientSecret --display-name $AADAppDisplayName --identifier-uris $AADAppIdentifierUri --query "appId" -o tsv

            if (-not $clientId) {
                throw "Error: Failed to create Azure Active Directory app"
            }
            Write-Host "Azure Active Directory app created successfully"
        }

        # Create a service principal if it doesn't already exist
        $principalId = az ad sp show --id $AADAppIdentifierUri --query "objectId" -o tsv
        if (-not $principalId) {
            Write-Host "Creating new service principal"
            az ad sp create --id $clientId | Out-Null
            $principalId = az ad sp show --id $AADAppIdentifierUri --query "objectId" -o tsv

            if (-not $principalId) {
                throw "Error: Failed to create new service principal"
            }
            Write-Host "New service principal created successfully"
        }

        # Sleep for 5 seconds to allow the new service principal to propagate
        Write-Host "Sleeping for 5 seconds"
        Start-Sleep -Seconds 5

        Write-Host "Granting service principal access to the newly created Immersive Reader resource"
        $accessResult = az role assignment create --assignee $principalId --scope $resourceId --role "Cognitive Services User"
        if (-not $accessResult) {
            throw "Error: Failed to grant service principal access"
        }
        Write-Host "Service principal access granted successfully"

        # Grab the tenant ID, which is needed when obtaining an Azure AD token
        $tenantId = az account show --query "tenantId" -o tsv

        # Collect the information needed to obtain an Azure AD token into one object
        $result = @{}
        $result.TenantId = $tenantId
        $result.ClientId = $clientId
        $result.ClientSecret = $AADAppClientSecret
        $result.Subdomain = $ResourceSubdomain

        Write-Host "Success! " -ForegroundColor Green -NoNewline
        Write-Host "Save the following JSON object to a text file for future reference:"
        Write-Output (ConvertTo-Json $result)
    }
    ```

1. Voer de `Create-ImmersiveReaderResource`functie uit, met de parameters naar gelang van het geval.

    ```azurepowershell-interactive
    Create-ImmersiveReaderResource
      -SubscriptionName '<SUBSCRIPTION_NAME>' `
      -ResourceName '<RESOURCE_NAME>' `
      -ResourceSubdomain '<RESOURCE_SUBDOMAIN>' `
      -ResourceSKU '<RESOURCE_SKU>' `
      -ResourceLocation '<RESOURCE_LOCATION>' `
      -ResourceGroupName '<RESOURCE_GROUP_NAME>' `
      -ResourceGroupLocation '<RESOURCE_GROUP_LOCATION>' `
      -AADAppDisplayName '<AAD_APP_DISPLAY_NAME>' `
      -AADAppIdentifierUri '<AAD_APP_IDENTIFIER_URI>' `
      -AADAppClientSecret '<AAD_APP_CLIENT_SECRET>'
    ```

    | Parameter | Opmerkingen |
    | --- | --- |
    | SubscriptionName |Naam van het Azure-abonnement dat u wilt gebruiken voor uw Immersive Reader-bron. U moet een abonnement hebben om een resource te maken. |
    | ResourceName |  Moet alfanumeriek zijn en '-' kunnen bevatten, zolang het '-' niet het eerste of laatste teken is. De lengte mag niet groter zijn dan 63 tekens.|
    | ResourceSubdomein |Een aangepast subdomein is nodig voor uw Immersive Reader-bron. Het subdomein wordt door de SDK gebruikt wanneer de immersive reader-service wordt aangeroepen om de Reader te starten. Het subdomein moet wereldwijd uniek zijn. Het subdomein moet alfanumeriek zijn en '-' kunnen bevatten, zolang het '-' niet het eerste of laatste teken is. De lengte mag niet groter zijn dan 63 tekens. Deze parameter is optioneel als de resource al bestaat. |
    | ResourceSKU |Opties: `S0`. Ga naar onze [prijspagina voor Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/immersive-reader/) voor meer informatie over elke beschikbare SKU. Deze parameter is optioneel als de resource al bestaat. |
    | ResourceLocation |Opties: `eastus` `eastus2`, `southcentralus` `westus`, `westus2` `australiaeast`, `southeastasia` `centralindia`, `japaneast` `northeurope`, `uksouth` `westeurope`, , , , , . Deze parameter is optioneel als de resource al bestaat. |
    | ResourceGroupName |Resources worden gemaakt in resourcegroepen binnen abonnementen. Geef de naam van een bestaande resourcegroep. Als de resourcegroep nog niet bestaat, wordt er een nieuwe met deze naam gemaakt. |
    | ResourceGroupLocatie |Als uw resourcegroep niet bestaat, moet u een locatie leveren waar u de groep maken. Voer . `az account list-locations` Gebruik de *eigenschap naam* (zonder spaties) van het geretourneerde resultaat. Deze parameter is optioneel als uw resourcegroep al bestaat. |
    | AADAppDisplayName |De weergavenaam van de Azure Active Directory-toepassing. Als er geen bestaande Azure AD-toepassing wordt gevonden, wordt er een nieuwe met deze naam gemaakt. Deze parameter is optioneel als de Azure AD-toepassing al bestaat. |
    | AADAppIdentifierUri |De URI voor de Azure AD-app. Als er geen bestaande Azure AD-app wordt gevonden, wordt er een nieuwe app met deze URI gemaakt. Bijvoorbeeld `https://immersivereaderaad-mycompany`. |
    | AADAppClientSecret |Een wachtwoord dat u maakt dat later wordt gebruikt om te verifiëren bij het aanschaffen van een token om de immersive reader te starten. Het wachtwoord moet ten minste 16 tekens lang zijn, ten minste 1 speciaal teken bevatten en ten minste 1 numeriek teken bevatten. |

1. Kopieer de JSON-uitvoer naar een tekstbestand voor later gebruik. De uitvoer moet er als volgt uitzien.

    ```json
    {
      "TenantId": "...",
      "ClientId": "...",
      "ClientSecret": "...",
      "Subdomain": "..."
    }
    ```

## <a name="next-steps"></a>Volgende stappen

* Bekijk de [Node.js snel start](./quickstart-nodejs.md) om te zien wat je nog meer doen met de Immersive Reader SDK met behulp van Node.js
* Bekijk de [Python-zelfstudie](./tutorial-python.md) om te zien wat u nog meer doen met de Immersive Reader SDK met Python
* Bekijk de [Swift-zelfstudie](./tutorial-ios-picture-immersive-reader.md) om te zien wat je nog meer doen met de Immersive Reader SDK met Swift
* Ontdek de [Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) en de [Immersive Reader SDK Reference](./reference.md)




