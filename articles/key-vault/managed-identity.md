---
title: Een door het systeem toegewezen beheerde identiteit gebruiken om toegang te krijgen tot Azure Key Vault
description: Meer informatie over het maken van een beheerde identiteit voor App Service toepassingen en hoe u deze kunt gebruiken voor toegang tot Azure Key Vault
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79270951"
---
# <a name="provide-key-vault-authentication-with-a-managed-identity"></a>Key Vault-verificatie bieden met een beheerde identiteit

Met een beheerde identiteit van Azure Active Directory kan uw app eenvoudig toegang krijgen tot andere met Azure AD beveiligde bronnen. De identiteit wordt beheerd door het Azure-platform en u hoeft geen geheimen in te richten of te draaien. Zie [beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md)voor meer informatie. 

In dit artikel wordt beschreven hoe u een beheerde identiteit voor een App Service toepassing maakt en deze gebruikt om toegang te krijgen tot Azure Key Vault. Zie voor toepassingen die worden gehost in virtuele machines van Azure, [een door Windows-VM systeem toegewezen beheerde identiteit gebruiken om toegang te krijgen tot Azure Key Vault](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md).


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Vereisten 

Voor het volt ooien van deze hand leiding hebt u de volgende resources nodig. 

- Een sleutel kluis. U kunt een bestaande sleutel kluis gebruiken of een nieuwe maken door de stappen in een van deze Quick starts te volgen:
   - [Een sleutel kluis maken met Azure CLI](quick-create-cli.md)
   - [Een sleutel kluis maken met Azure PowerShell](quick-create-powershell.md)
   - [Een sleutel kluis maken met de Azure Portal](quick-create-portal.md).
- Een bestaande App Service-toepassing waaraan toegang tot de sleutel kluis moet worden verleend. U kunt een snelle versie maken door de stappen in de [app service-documentatie](../app-service/overview.md)te volgen.
- [Azure cli](/cli/azure/install-azure-cli?view=azure-cli-latest) of [Azure PowerShell](/powershell/azure/overview). U kunt ook de [Azure Portal](https://portal.azure.com)gebruiken.


## <a name="adding-a-system-assigned-identity"></a>Een door het systeem toegewezen identiteit toevoegen 

Eerst moet u een door het systeem toegewezen identiteit toevoegen aan een toepassing. 
 
### <a name="azure-portal"></a>Azure-portal 

Als u een beheerde identiteit in de portal instelt, moet u eerst een toepassing als normaal aanmaken en vervolgens de functie inschakelen. 

1. Als u een functie-app gebruikt, navigeert u naar **platform functies**. Voor andere typen apps schuift u omlaag naar de **instellingen** groep in het linkernavigatievenster. 

1. Selecteer **beheerde identiteit**. 

1. Schakel op het tabblad **systeem toegewezen** de optie **status** in **op aan**. Klik op **Opslaan**. 

    ![](./media/managed-identity-system-assigned.png)

### <a name="azure-cli"></a>Azure CLI

Voor deze Quick start is de Azure CLI-versie 2.0.4 of hoger vereist. Voer `az --version` uit om uw huidige versie te vinden. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli?view=azure-cli-latest). 

Als u zich wilt aanmelden met Azure CLI, gebruikt u de opdracht [AZ login](/cli/azure/reference-index?view=azure-cli-latest#az-login) :

```azurecli-interactive
az login
```

Zie [Aanmelden met Azure cli](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)voor meer informatie over aanmeldings opties met de Azure cli. 

Als u de identiteit voor deze toepassing wilt maken, gebruikt u de opdracht Azure CLI [AZ webapp Identity Assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) of [AZ functionapp id Assign](/cli/azure/functionapp/identity?view=azure-cli-latest#az-functionapp-identity-assign) opdracht:


```azurecli-interactive
az webapp identity assign --name myApp --resource-group myResourceGroup
```

```azurecli-interactive
az functionapp identity assign --name myApp --resource-group myResourceGroup
```

Noteer de `PrincipalId`, die in de volgende sectie nodig is.

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```
## <a name="grant-your-app-access-to-key-vault"></a>Uw app toegang geven tot Key Vault 

### <a name="azure-portal"></a>Azure-portal

1.  Navigeer naar Key Vault resource. 

1.  Selecteer **toegangs beleid** en klik op **toegangs beleid toevoegen**. 

1.  Selecteer in **geheime machtigingen** **ophalen, lijst**. 

1.  Kies **Principal selecteren**en voer in het zoek veld de naam van de app in.  Selecteer de app in de lijst met resultaten en klik op **selecteren**. 

1.  Klik op **toevoegen** om het toevoegen van het nieuwe toegangs beleid te volt ooien.

    ![](./media/managed-identity-access-policy.png)

### <a name="azure-cli"></a>Azure CLI

Als u uw toepassing toegang wilt verlenen tot uw sleutel kluis, gebruikt u de Azure CLI [AZ set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) opdracht, waarbij u de **ObjectId** -para meter opgeeft met de **principalId** die u hierboven hebt genoteerd.

```azurecli-interactive
az keyvault set-policy --name myKeyVault --object-id <PrincipalId> --secret-permissions get list 
```

## <a name="next-steps"></a>Volgende stappen

- [Azure Key Vault beveiliging: identiteits-en toegangs beheer](overview-security.md#identity-and-access-management)
- [Key Vault verificatie met een toegangscontrole beleid bieden](key-vault-group-permissions-for-apps.md)
- [Informatie over sleutels, geheimen en certificaten](about-keys-secrets-and-certificates.md)
- [Beveilig uw sleutel kluis](key-vault-secure-your-key-vault.md).
- [Ontwikkelaars handleiding Azure Key Vault](key-vault-developers-guide.md)
- [Azure Key Vault aanbevolen procedures](key-vault-best-practices.md) controleren