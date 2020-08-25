---
title: Een door de gebruiker toegewezen beheerde identiteit op een Linux-VM gebruiken om toegang te krijgen tot Azure Resource Manager
description: In deze zelfstudie doorloopt u het proces voor het krijgen van toegang tot Azure Resource Manager met een door de gebruiker toegewezen beheerde identiteit op een Linux-VM.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: markvi
ROBOTS: NOINDEX,NOFOLLOW
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57b68ebb21c0c10c3fbe3fd77d11785d16a10053
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/25/2020
ms.locfileid: "60443463"
---
# <a name="tutorial-use-a-user-assigned-managed-identity-on-a-linux-vm-to-access-azure-resource-manager"></a>Zelfstudie: een door de gebruiker toegewezen beheerde identiteit gebruiken op een Linux-VM om toegang te krijgen tot Azure Resource Manager

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

In deze zelfstudie wordt uitgelegd hoe u een door de gebruiker toegewezen beheerde identiteit maakt, deze toewijst aan een Linux-VM en die identiteit vervolgens gebruikt om toegang te krijgen tot de Azure Resource Manager-API. Beheerde identiteiten voor Azure-resources worden automatisch beheerd door Azure. Ze maken verificatie mogelijk voor services die Azure AD-verificatie ondersteunen, zonder dat er referenties in uw code hoeven te worden ingesloten. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een door de gebruiker toegewezen beheerde identiteit maken
> * De door de gebruiker toegewezen beheerde identiteit toewijzen aan een Linux-VM 
> * De door de gebruiker toegewezen beheerde identiteit toegang verlenen tot een resourcegroep in Azure Resource Manager 
> * Een toegangstoken ophalen met de door de gebruiker toegewezen beheerde identiteit en daarmee Azure Resource Manager aanroepen 

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

- [Meld u aan bij Azure Portal](https://portal.azure.com)

- [Een virtuele Linux-machine maken](/azure/virtual-machines/linux/quick-create-portal)

- Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor deze Quickstart gebruikmaken van Azure CLI versie 2.0.4 of hoger. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren]( /cli/azure/install-azure-cli).

## <a name="create-a-user-assigned-managed-identity"></a>Een door de gebruiker toegewezen beheerde identiteit maken

1. Als u de CLI-console gebruikt (in plaats van een Azure Cloud Shell-sessie), begint u door u aan te melden bij Azure. Gebruik een account dat is gekoppeld aan het Azure-abonnement waarmee u de nieuwe door de gebruiker toegewezen beheerde identiteit wilt maken:

    ```azurecli
    az login
    ```

2. Maak een door de gebruiker toegewezen beheerde identiteit met [az identity create](/cli/azure/identity#az-identity-create). De parameter `-g` geeft de resourcegroep aan waarin de door de gebruiker toegewezen beheerde identiteit wordt gemaakt en de parameter `-n` geeft de naam ervan aan. Vervang de parameterwaarden `<RESOURCE GROUP>` en `<UAMI NAME>` door uw eigen waarden:
    
[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]


```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <UAMI NAME>
```

Het antwoord bevat details voor de door de gebruiker toegewezen beheerde identiteit die is gemaakt, vergelijkbaar met het volgende voorbeeld. Noteer de waarde `id` voor uw door de gebruiker toegewezen beheerde identiteit, omdat deze bij de volgende stap wordt gebruikt:

```json
{
"clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
"clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<UAMI NAME>/credentials?tid=5678&oid=9012&aid=12344643-8088-4d70-9532-c3a0fdc190fz",
"id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<UAMI NAME>",
"location": "westcentralus",
"name": "<UAMI NAME>",
"principalId": "9012",
"resourceGroup": "<RESOURCE GROUP>",
"tags": {},
"tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
"type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

## <a name="assign-a-user-assigned-managed-identity-to-your-linux-vm"></a>Een door de gebruiker toegewezen beheerde identiteit toewijzen aan uw Linux-VM

Een door de gebruiker toegewezen beheerde identiteit kan worden gebruikt door clients in meerdere Azure-resources. Gebruik de volgende opdrachten om de door de gebruiker toegewezen beheerde identiteit toe te wijzen aan één VM. Gebruik de eigenschap `Id` die in de vorige stap is geretourneerd voor de parameter `-IdentityID`.

Wijs de door de gebruiker toegewezen beheerde identiteit toe aan de Linux-VM met [az vm identity assign](/cli/azure/vm). Vervang de parameterwaarden `<RESOURCE GROUP>` en `<VM NAME>` door uw eigen waarden. Gebruik de eigenschap `id` die in de vorige stap is geretourneerd voor de waarde van de parameter `--identities`.

```azurecli-interactive
az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<UAMI NAME>"
```

## <a name="grant-your-user-assigned-managed-identity-access-to-a-resource-group-in-azure-resource-manager"></a>Uw door de gebruiker toegewezen beheerde identiteit toegang verlenen tot een resourcegroep in Azure Resource Manager 

Beheerde identiteiten voor Azure-resources biedt identiteiten die uw code kan gebruiken om toegangstokens aan te vragen voor verificatie bij resource-API's die Azure AD-verificatie ondersteunen. In deze zelfstudie krijgt uw code toegang tot de Azure Resource Manager-API.  

Voordat uw code toegang tot de API kan krijgen, moet u de identiteit toegang geven tot een resource in Azure Resource Manager. In dit geval is dat de resourcegroep waarin de VM zich bevindt. Werk de waarde voor `<SUBSCRIPTION ID>` en `<RESOURCE GROUP>` bij overeenkomstig uw omgeving. Vervang bovendien `<UAMI PRINCIPALID>` door de eigenschap `principalId` die wordt geretourneerd door de opdracht `az identity create` in [Een door de gebruiker toegewezen beheerde identiteit maken](#create-a-user-assigned-managed-identity):

```azurecli-interactive
az role assignment create --assignee <UAMI PRINCIPALID> --role 'Reader' --scope "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP> "
```

Het antwoord bevat details voor de gemaakte roltoewijzing, vergelijkbaar met het volgende voorbeeld:

```json
{
  "id": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Authorization/roleAssignments/b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "name": "b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "properties": {
    "principalId": "f5fdfdc1-ed84-4d48-8551-999fb9dedfbl",
    "roleDefinitionId": "/subscriptions/<SUBSCRIPTION ID>/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "scope": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>"
  },
  "resourceGroup": "<RESOURCE GROUP>",
  "type": "Microsoft.Authorization/roleAssignments"
}

```

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-resource-manager"></a>Een toegangstoken ophalen met behulp van de identiteit van de virtuele machine en daarmee Resource Manager aanroepen 

Voor de rest van de zelfstudie werken we op de virtuele machine die we eerder hebben gemaakt.

U hebt een SSH-client nodig om deze stappen uit te voeren. Als u Windows gebruikt, kunt u de SSH-client in het [Windows-subsysteem voor Linux](https://msdn.microsoft.com/commandline/wsl/about) gebruiken. 

1. Meld u aan bij de Azure [Portal](https://portal.azure.com).
2. Navigeer in de portal naar **Virtuele machines**, ga naar de virtuele Windows-machine en klik op de pagina **Overzicht** op **Verbinden**. Kopieer de verbindingsreeks voor uw virtuele machine.
3. Maak verbinding met de virtuele machine met de SSH-client van uw keuze. Als u Windows gebruikt, kunt u de SSH-client in het [Windows-subsysteem voor Linux](https://msdn.microsoft.com/commandline/wsl/about) gebruiken. Zie [De sleutels van uw SSH-client gebruiken onder Windows in Azure](~/articles/virtual-machines/linux/ssh-from-windows.md) of [Een sleutelpaar met een openbare SSH-sleutel en een privé-sleutel maken en gebruiken voor virtuele Linux-machines in Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md) als u hulp nodig hebt bij het configureren van de sleutels van uw SSH-client.
4. Dien in het terminalvenster met behulp van CURL een aanvraag in op het Azure IMDS-eindpunt (Instance Metadata Service) om een toegangstoken voor Azure Resource Manager op te halen.  

   De CURL-aanvraag voor het verkrijgen van een toegangstoken wordt in het volgende voorbeeld weergegeven.Vervang `<CLIENT ID>` door de eigenschap `clientId` die wordt geretourneerd door de opdracht `az identity create` in [Een door de gebruiker toegewezen beheerde identiteit maken](#create-a-user-assigned-managed-identity): 
    
   ```bash
   curl -H Metadata:true "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com/&client_id=<UAMI CLIENT ID>"   
   ```
    
    > [!NOTE]
    > De waarde van de parameter `resource` moet exact overeenkomen met wat er in Azure AD wordt verwacht.Wanneer u de resource-id van Resource Manager gebruikt, moet u de URI opgeven met een slash op het einde. 
    
    Het antwoord bevat het toegangstoken dat u nodig hebt voor toegang tot Azure Resource Manager. 
    
    Voorbeeldantwoord:  

    ```bash
    {
    "access_token":"eyJ0eXAiOi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"
    } 
    ```

5. Gebruik het toegangstoken om toegang te krijgen tot Azure Resource Manager en lees de eigenschappen van de resourcegroep waarvoor u eerder uw door de gebruiker toegewezen beheerde identiteit toegang hebt verleend. Vervang de waarden van `<SUBSCRIPTION ID>`, en `<RESOURCE GROUP>` door de waarden die u eerder hebt opgegeven, en `<ACCESS TOKEN>` door het token dat in de vorige stap is geretourneerd.

    > [!NOTE]
    > De URL is hoofdlettergevoelig, dus gebruik precies dezelfde naam die u eerder hebt gebruikt voor de naam van de resourcegroep, en de hoofdletter ‘G’ in `resourceGroups`.  

    ```bash 
    curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-09-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```

    Het antwoord bevat de gegevens van de specifieke resourcegroep, vergelijkbaar met het volgende voorbeeld: 

    ```bash
    {
    "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/DevTest",
    "name":"DevTest",
    "location":"westus",
    "properties":{"provisioningState":"Succeeded"}
    } 
    ```
    
## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd om een door de gebruiker toegewezen beheerde identiteit te maken en deze te koppelen aan een Linux-VM om toegang te krijgen tot de Azure Resource Manager-API.  Zie voor meer informatie over Azure Resource Manager:

> [!div class="nextstepaction"]
>[Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview)

