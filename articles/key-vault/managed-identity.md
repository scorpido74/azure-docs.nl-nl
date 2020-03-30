---
title: Een door het systeem toegewezen beheerde identiteit gebruiken om toegang te krijgen tot Azure Key Vault
description: Meer informatie over het maken van een beheerde identiteit voor App Service-toepassingen en hoe u deze gebruiken om toegang te krijgen tot Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 36a4871339401629300eedd77b6441aed10aabf3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270951"
---
# <a name="provide-key-vault-authentication-with-a-managed-identity"></a>Key Vault-verificatie bieden met een beheerde identiteit

Met een beheerde identiteit uit Azure Active Directory heeft uw app eenvoudig toegang tot andere door Azure AD beveiligde bronnen. De identiteit wordt beheerd door het Azure-platform en vereist niet dat u geheimen indient of roteert. Zie [Beheerde identiteiten voor Azure-bronnen voor](../active-directory/managed-identities-azure-resources/overview.md)meer informatie. 

In dit artikel ziet u hoe u een beheerde identiteit voor een App Service-toepassing maakt en deze gebruikt om toegang te krijgen tot Azure Key Vault. Zie Een beheerde identiteit met [windows VM-systeem gebruiken voor](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md)toepassingen die worden gehost in Azure VM's.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Vereisten 

Als u deze handleiding wilt voltooien, moet u over de volgende bronnen beschikken. 

- Een sleutelkluis. U een bestaand sleutelkluis gebruiken of een nieuwe maken door de stappen in een van deze snelstarts te volgen:
   - [Een sleutelkluis maken met de Azure CLI](quick-create-cli.md)
   - [Een sleutelkluis maken met Azure PowerShell](quick-create-powershell.md)
   - [Maak een sleutelkluis met de Azure-portal.](quick-create-portal.md)
- Een bestaande App Service-toepassing waarmee u toegang tot sleutelkluizen verlenen. U er snel een maken door de stappen in de [documentatie van appservice te](../app-service/overview.md)volgen.
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) of [Azure PowerShell](/powershell/azure/overview). U ook de [Azure-portal](https://portal.azure.com)gebruiken.


## <a name="adding-a-system-assigned-identity"></a>Een door het systeem toegewezen identiteit toevoegen 

Eerst moet u een door het systeem toegewezen identiteit aan een toepassing toevoegen. 
 
### <a name="azure-portal"></a>Azure Portal 

Als u een beheerde identiteit in de portal instelt, moet u eerst een toepassing als normaal aanmaken en vervolgens de functie inschakelen. 

1. Als u een functie-app gebruikt, navigeert u naar **platformfuncties.** Voor andere app-typen bladert u omlaag naar de groep **Instellingen** in de linkernavigatie. 

1. Selecteer **Beheerde identiteit**. 

1. Schakel op het tabblad **Systeem toegewezen** **over** naar **Aan**. Klik op **Opslaan**. 

    ![](./media/managed-identity-system-assigned.png)

### <a name="azure-cli"></a>Azure-CLI

Voor deze quickstart is de Azure CLI-versie 2.0.4 of hoger vereist. Voer `az --version` uit om uw huidige versie te vinden. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli?view=azure-cli-latest). 

Als u zich wilt aanmelden bij Azure CLI, gebruikt u de opdracht [AZ-aanmelding:](/cli/azure/reference-index?view=azure-cli-latest#az-login)

```azurecli-interactive
az login
```

Zie [Aanmelden bij Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)voor meer informatie over aanmeldingsopties met de Azure CLI. 

Als u de identiteit voor deze toepassing wilt maken, gebruikt u de opdracht Aai [AZ-webapp-identiteit toewijzen](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) of [az-functieapp-toewijzingsopdracht:](/cli/azure/functionapp/identity?view=azure-cli-latest#az-functionapp-identity-assign)


```azurecli-interactive
az webapp identity assign --name myApp --resource-group myResourceGroup
```

```azurecli-interactive
az functionapp identity assign --name myApp --resource-group myResourceGroup
```

Maak een notitie `PrincipalId`van de , die nodig zal zijn in de volgende sectie.

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```
## <a name="grant-your-app-access-to-key-vault"></a>Geef uw app toegang tot Key Vault 

### <a name="azure-portal"></a>Azure Portal

1.  Navigeer naar Key Vault-bron. 

1.  Selecteer **Toegangsbeleid** en klik op **Toegangsbeleid toevoegen**. 

1.  Selecteer in **geheime machtigingen**De optie **Oppakken, Lijst**. 

1.  Kies **Principal selecteren**en voer in het zoekveld de naam van de app in.  Selecteer de app in de resultatenlijst en klik op **Selecteren**. 

1.  Klik **op Toevoegen** om het nieuwe toegangsbeleid toe te voegen.

    ![](./media/managed-identity-access-policy.png)

### <a name="azure-cli"></a>Azure-CLI

Als u uw toepassing toegang wilt verlenen tot uw sleutelkluis, gebruikt u de opdracht Azure CLI [az keyvault-setbeleid,](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) waarbij de **parameter ObjectId** wordt voorzien van de hierboven vermelde **principalId.**

```azurecli-interactive
az keyvault set-policy --name myKeyVault --object-id <PrincipalId> --secret-permissions get list 
```

## <a name="next-steps"></a>Volgende stappen

- [Azure Key Vault-beveiliging: identiteits- en toegangsbeheer](overview-security.md#identity-and-access-management)
- [Key Vault-verificatie bieden met een toegangscontrolebeleid](key-vault-group-permissions-for-apps.md)
- [Informatie over sleutels, geheimen en certificaten](about-keys-secrets-and-certificates.md)
- [Beveilig je sleutelkluis.](key-vault-secure-your-key-vault.md)
- [Handleiding azure Key Vault-ontwikkelaar](key-vault-developers-guide.md)
- Aanbevolen [procedures voor Azure Key Vault bekijken](key-vault-best-practices.md)