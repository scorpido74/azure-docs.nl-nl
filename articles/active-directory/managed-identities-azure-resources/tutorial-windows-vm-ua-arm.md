---
title: Zelfstudie`:` Gebruik een beheerde identiteit om toegang te krijgen tot Azure Resource Manager - Windows - Azure AD
description: Een zelfstudie die u helpt bij het doorlopen van het proces voor het krijgen van toegang tot Azure Resource Manager met een door de gebruiker toegewezen beheerde identiteit op een Windows-VM.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/14/2020
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec9956f0c5d834633646938da19f03e5467a9f6d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75977839"
---
# <a name="tutorial-use-a-user-assigned-managed-identity-on-a-windows-vm-to-access-azure-resource-manager"></a>Zelfstudie: gebruik een door de gebruiker toegewezen beheerde identiteit op een Windows-vm om toegang te krijgen tot Azure Resource Manager

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

In deze zelfstudie wordt uitgelegd hoe u een door de gebruiker toegewezen identiteit kunt maken, deze toewijzen aan een virtuele Windows-machine (VM) en die identiteit vervolgens gebruiken om toegang te krijgen tot de Azure Resource Manager-API. Beheerde service-identiteiten worden automatisch beheerd door Azure. Ze maken verificatie mogelijk voor services die Azure AD-verificatie ondersteunen, zonder dat er referenties in uw code hoeven te worden ingesloten. 

Procedures voor:

> [!div class="checklist"]
> * Een door de gebruiker toegewezen beheerde identiteit maken
> * Uw door de gebruiker toegewezen identiteit toewijzen aan uw Windows-VM
> * De door de gebruiker toegewezen identiteit toegang verlenen tot een resourcegroep in Azure Resource Manager 
> * Een toegangstoken ophalen met behulp van de door de gebruiker toegewezen identiteit en daarmee Azure Resource Manager aanroepen 
> * De eigenschappen van een resourcegroep lezen

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

- [Aanmelden bij Azure-portal](https://portal.azure.com)

- [Een virtuele Windows-machine maken](/azure/virtual-machines/windows/quick-create-portal)

- Om de stappen voor het maken van de vereiste resources en het rolbeheer in deze zelfstudie uit te voeren, moet uw account 'Eigenaar'-machtigingen hebben voor het juiste bereik (uw abonnement of resourcegroep). Voor hulp bij roltoewijzing gaat u naar [Op rollen gebaseerd toegangsbeheer gebruiken voor het beheer van de toegang tot de resources van uw Azure-abonnement](/azure/role-based-access-control/role-assignments-portal).
- [Installeer de meest recente versie van de Azure PowerShell-module](/powershell/azure/install-az-ps). 
- Voer `Connect-AzAccount` uit om een verbinding op te zetten met Azure.
- Installeer de [nieuwste versie van PowerShellGet](/powershell/scripting/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).
- Voer `Install-Module -Name PowerShellGet -AllowPrerelease` uit om de voorlopige versie van de `PowerShellGet`-module op te halen (mogelijk moet u met `Exit` de huidige PowerShell-sessie afsluiten nadat u met deze opdracht de `Az.ManagedServiceIdentity`-module hebt geïnstalleerd).
- Voer `Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease` uit om de voorlopige versie van de `Az.ManagedServiceIdentity`-module te installeren en de aan de gebruiker toegewezen identiteitsbewerkingen in dit artikel uit te voeren.


## <a name="enable"></a>Inschakelen

Voor een scenario dat is gebaseerd op een door de gebruiker toegewezen identiteit, moet u de volgende stappen uitvoeren:

- Een identiteit maken
 
- De nieuw gemaakte identiteit toewijzen

### <a name="create-identity"></a>Identiteit maken

In deze sectie ziet u hoe u een door de gebruiker toegewezen identiteit maakt. Een door de gebruiker toegewezen identiteit wordt als een zelfstandige Azure-resource gemaakt. Met de [New-AzUserAssignedIdentity](/powershell/module/az.managedserviceidentity/get-azuserassignedidentity) wordt een identiteit gemaakt in uw Azure AD-tenant die kan worden toegewezen aan een of meer Azure-service-exemplaren.

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1
```

Het antwoord bevat details voor de gemaakte door de gebruiker toegewezen identiteit, vergelijkbaar met het volgende voorbeeld. Noteer de waarden voor `Id` en `ClientId` voor de door de gebruiker toegewezen identiteit, aangezien deze worden gebruikt in volgende stappen:

```azurepowershell
{
Id: /subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1
ResourceGroupName : myResourceGroupVM
Name: ID1
Location: westus
TenantId: 733a8f0e-ec41-4e69-8ad8-971fc4b533f8
PrincipalId: e591178e-b785-43c8-95d2-1397559b2fb9
ClientId: af825a31-b0e0-471f-baea-96de555632f9
ClientSecretUrl: https://control-westus.identity.azure.net/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1/credentials?tid=733a8f0e-ec41-4e69-8ad8-971fc4b533f8&oid=e591178e-b785-43c8-95d2-1397559b2fb9&aid=af825a31-b0e0-471f-baea-96de555632f9
Type: Microsoft.ManagedIdentity/userAssignedIdentities
}
```

### <a name="assign-identity"></a>Identiteit toewijzen

In deze sectie ziet u hoe u de door de gebruiker toegewezen identiteit toewijst aan een Windows-vm. Een door de gebruiker toegewezen identiteit kan worden gebruikt door clients op meerdere Azure-resources. Gebruik de volgende opdrachten om de door de gebruiker toegewezen identiteit toe te wijzen aan één VM. Gebruik de eigenschap `Id` die in de vorige stap is geretourneerd voor de parameter `-IdentityID`.

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzVM -ResourceGroupName TestRG -VM $vm -IdentityType "UserAssigned" -IdentityID "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
```

## <a name="grant-access"></a>Toegang verlenen 

In deze sectie ziet u hoe u uw door de gebruiker toegewezen identiteit toegang verleent tot een resourcegroep in Azure Resource Manager. Beheerde identiteiten voor Azure-resources biedt identiteiten die uw code kan gebruiken om toegangstokens aan te vragen voor verificatie bij resource-API's die Azure AD-verificatie ondersteunen. In deze zelfstudie krijgt uw code toegang tot de Azure Resource Manager-API. 

Voordat uw code toegang tot de API kan krijgen, moet u de identiteit toegang geven tot een resource in Azure Resource Manager. In dit geval is dat de resourcegroep waarin de VM zich bevindt. Werk de waarde voor `<SUBSCRIPTION ID>` bij overeenkomstig uw omgeving.

```azurepowershell-interactive
$spID = (Get-AzUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1).principalid
New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/"
```

Het antwoord bevat details voor de gemaakte roltoewijzing, vergelijkbaar met het volgende voorbeeld:

```azurepowershell
RoleAssignmentId: /subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/myResourceGroupVM/providers/Microsoft.Authorization/roleAssignments/f9cc753d-265e-4434-ae19-0c3e2ead62ac
Scope: /subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/myResourceGroupVM
DisplayName: ID1
SignInName:
RoleDefinitionName: Reader
RoleDefinitionId: acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId: e591178e-b785-43c8-95d2-1397559b2fb9
ObjectType: ServicePrincipal
CanDelegate: False
```

## <a name="access-data"></a>Toegang tot gegevens

### <a name="get-an-access-token"></a>Een toegangstoken opvragen 

Voor de rest van de zelfstudie werkt u op de virtuele machine die we eerder hebben gemaakt.

1. Aanmelden bij de Azure-portal op[https://portal.azure.com](https://portal.azure.com)

2. Navigeer in Azure Portal naar **Virtuele machines**, ga naar de virtuele Windows-machine en klik op de pagina **Overzicht** op **Verbinden**.

3. Voer de **gebruikersnaam** en het **wachtwoord** in die u hebt gebruikt bij het maken van de Windows-VM.

4. Nu u een **Verbinding met extern bureaublad** met de virtuele machine hebt gemaakt, opent u **PowerShell** in de externe sessie.

5. Dien met behulp van `Invoke-WebRequest` van Powershell een aanvraag in bij de lokale beheerde identiteiten om een toegangstoken voor Azure Resource Manager op te halen voor het Azure-resources-eindpunt.  De `client_id` waarde is de waarde die wordt geretourneerd toen u de door de gebruiker toegewezen beheerde identiteit hebt gemaakt.

    ```azurepowershell
    $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&client_id=af825a31-b0e0-471f-baea-96de555632f9&resource=https://management.azure.com/' -Method GET -Headers @{Metadata="true"}
    $content = $response.Content | ConvertFrom-Json
    $ArmToken = $content.access_token
    ```

### <a name="read-properties"></a>Eigenschappen lezen

Gebruik het in de vorige stap opgehaalde toegangstoken om toegang te krijgen tot Azure Resource Manager en lees de eigenschappen van de resourcegroep waarvoor u uw door de gebruiker toegewezen identiteit toegang hebt verleend. Vervang `<SUBSCRIPTION ID>` door de abonnements-id van uw omgeving.

```azurepowershell
(Invoke-WebRequest -Uri https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/myResourceGroupVM?api-version=2016-06-01 -Method GET -ContentType "application/json" -Headers @{Authorization ="Bearer $ArmToken"}).content
```
Het antwoord bevat de gegevens van de specifieke resourcegroep, vergelijkbaar met het volgende voorbeeld:

```json
{"id":"/subscriptions/<SUBSCRIPTIONID>/resourceGroups/myResourceGroupVM","name":"myResourceGroupVM","location":"eastus","properties":{"provisioningState":"Succeeded"}}
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een door de gebruiker toegewezen identiteit maakt en deze aan een Azure Virtual Machine koppelt om toegang te krijgen tot de Azure Resource Manager API.  Zie voor meer informatie over Azure Resource Manager:

> [!div class="nextstepaction"]
>[Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview)
