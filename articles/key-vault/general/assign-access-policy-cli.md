---
title: Een Azure Key Vault toegangs beleid (CLI) toewijzen
description: De Azure CLI gebruiken om een Key Vault toegangs beleid toe te wijzen aan een service-principal of toepassings-id.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: 32a323e8cc56a8c45b18737bb55ebe19d4be27c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89381052"
---
# <a name="assign-a-key-vault-access-policy"></a>Een Key Vault toegangs beleid toewijzen

Een Key Vault toegangs beleid bepaalt of een bepaalde service-principal, namelijk een toepassing of gebruikers groep, verschillende bewerkingen kan uitvoeren op Key Vault [geheimen](../secrets/index.yml), [sleutels](../keys/index.yml)en [certificaten](../certificates/index.yml). U kunt toegangs beleid toewijzen met behulp van de [Azure Portal](assign-access-policy-portal.md), de Azure cli (dit artikel) of [Azure PowerShell](assign-access-policy-powershell.md).

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

Voor meer informatie over het maken van groepen in Azure Active Directory met behulp van de Azure CLI raadpleegt u [AZ Ad Group Create](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-create) en [AZ Ad Group lid add](/cli/azure/ad/group/member?view=azure-cli-latest#az-ad-group-member-add).

## <a name="configure-the-azure-cli-and-sign-in"></a>De Azure CLI configureren en aanmelden

1. Als u Azure CLI-opdrachten lokaal wilt uitvoeren, installeert u de [Azure cli](/cli/azure/install-azure-cli?view=azure-cli-latest).
 
    Als u opdrachten rechtstreeks in de Cloud wilt uitvoeren, gebruikt u de [Azure Cloud shell](/azure/cloud-shell/overview).

1. Alleen lokale CLI: Meld u aan bij Azure met `az login` :

    ```bash
    az login
    ```

    `az login`Met de opdracht opent u een browser venster om indien nodig referenties te verzamelen.

## <a name="acquire-the-object-id"></a>De object-ID ophalen

Bepaal de object-ID van de toepassing, groep of gebruiker aan wie u het toegangs beleid wilt toewijzen:

- Toepassingen en andere service-principals: gebruik de opdracht [AZ AD SP List](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-list) om de service-principals op te halen. Bekijk de uitvoer van de opdracht om de object-ID te bepalen van de beveiligingsprincipal waaraan u het toegangs beleid wilt toewijzen.

    ```azurecli-interactive
    az ad sp list --show-mine
    ```

- Groepen: gebruik de opdracht [AZ Ad Group List](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-list) en filtert de resultaten met de `--display-name` para meter:

     ```azurecli-interactive
    az ad group list --display-name <search-string>
    ```

- Gebruikers: gebruik de opdracht [AZ AD User show](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-show) , waarbij het e-mail adres van de gebruiker wordt door gegeven in de `--id` para meter:

    ```azurecli-interactive
    az ad user show --id <email-address-of-user>
    ```

## <a name="assign-the-access-policy"></a>Het toegangs beleid toewijzen
    
Gebruik de opdracht [AZ-set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) om de gewenste machtigingen toe te wijzen:

```azurecli-interactive
az keyvault set-policy --name myKeyVault --object-id <object-id> --secret-permissions <secret-permissions> --key-permissions <key-permissions> --certificate-permissions <certificate-permissions>
```

Vervang door `<object-id>` de object-id van de Service-Principal.

U hoeft alleen `--secret-permissions` , en toe te voegen, `--key-permissions` en `--certificate-permissions` bij het toewijzen van machtigingen aan deze specifieke typen. De toegestane waarden voor `<secret-permissions>` , `<key-permissions>` en zijn te `<certificate-permissions>` vinden in de documentatie [AZ-set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) .

## <a name="next-steps"></a>Volgende stappen

- [Azure Key Vault-beveiliging: Identiteits- en toegangsbeheer](overview-security.md#identity-and-access-management)
- [Beveilig uw sleutel kluis](secure-your-key-vault.md).
- [Gids voor Azure Key Vault-ontwikkelaars](developers-guide.md)
- [Best practices voor Azure Key Vault](best-practices.md)
