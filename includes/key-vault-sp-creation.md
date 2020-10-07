---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: deb2eb877743d533c5daeee8b6636edd62418fe0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "89494330"
---
De eenvoudigste manier om een cloudtoepassing te verifiëren, is met een beheerde identiteit. Zie [Verificatie bij Azure Key Vault](/azure/key-vault/general/authentication) voor meer informatie.

In deze quickstart wordt echter een desktoptoepassing gemaakt, omdat dit eenvoudiger is. Voor deze toepassing is het gebruik van een service-principal en een toegangsbeheerbeleid vereist. Voor uw service-principal is een unieke naam vereist met de notatie http://&lt;mijn-unieke-service-principal-naam&gt;.

Maak een service-principal met behulp van de Azure CLI-opdracht [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac):

```azurecli
az ad sp create-for-rbac --skip-assignment -n "http://<my-unique-service-principal-name>" --sdk-auth
```

Met deze bewerking wordt een reeks sleutel-waardeparen geretourneerd.

```console
{
  "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
  "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
  "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
  "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```
