---
title: Beheerde identiteiten met de gebruiker beheren met REST - Azure AD
description: Stapsgewijze instructies voor het maken, aanbieden en verwijderen van een door de gebruiker toegewezen beheerde identiteit om REST API-aanroepen te maken.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/26/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39e108451e4c19e77e01b5bcc5d8dd21e86ad73a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74547418"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-rest-api-calls"></a>Een door de gebruiker toegewezen beheerde identiteit maken, aanbieden of verwijderen met REST API-aanroepen

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Beheerde identiteiten voor Azure-resources bieden Azure-services de mogelijkheid om te verifiëren voor services die Azure AD-verificatie ondersteunen, zonder dat er referenties in uw code nodig zijn. 

In dit artikel leert u hoe u een door de gebruiker toegewezen beheerde identiteit maken, aanbieden en verwijderen met BEHULP van CURL om REST API-aanroepen te maken.

## <a name="prerequisites"></a>Vereisten

- Als u niet bekend bent met beheerde identiteiten voor Azure-bronnen, raadpleegt u het [overzichtsgedeelte](overview.md). **Controleer het [verschil tussen een door het systeem toegewezen en door de gebruiker toegewezen beheerde identiteit](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.
- Als u Windows gebruikt, installeert u het [Windows-subsysteem voor Linux](https://msdn.microsoft.com/commandline/wsl/about) of gebruikt u de [Azure Cloud Shell](../../cloud-shell/overview.md) in de Azure-portal.
- Als u het [Windows-subsysteem voor Linux](https://msdn.microsoft.com/commandline/wsl/about) of een [Linux-distributiebesturingssysteem](/cli/azure/install-azure-cli-apt?view=azure-cli-latest)gebruikt, [installeert u de lokale azure CLI-console](/cli/azure/install-azure-cli).
- Als u de lokale console Azure CLI `az login` gebruikt, meldt u zich aan bij Azure met een account dat is gekoppeld aan het Azure-abonnement dat u wilt implementeren of ophalen met door de gebruiker toegewezen beheerde identiteitsgegevens.
- Een access token `az account get-access-token` voor dragers ophalen met behulp van de volgende door de gebruiker toegewezen beheerde identiteitsbewerkingen.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Een door de gebruiker toegewezen beheerde identiteit maken 

Als u een door de gebruiker toegewezen beheerde identiteit wilt maken, heeft uw account de roltoewijzing [Voor beheerde identiteitsbijdrager](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) nodig.

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X PUT -d '{"loc
ation": "<LOCATION>"}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview HTTP/1.1
```

**Kopteksten aanvragen**

|Aanvraagheader  |Beschrijving  |
|---------|---------|
|*Content-Type*     | Vereist. Ingesteld op `application/json`.        |
|*Vergunning*     | Vereist. Instellen op `Bearer` een geldig toegangstoken.        |

**Aanvraaginstantie**

|Name  |Beschrijving  |
|---------|---------|
|location     | Vereist. Locatie van de resource.        |

## <a name="list-user-assigned-managed-identities"></a>Door de gebruiker toegewezen beheerde identiteiten weergeven

Als u een door de gebruiker toegewezen beheerde identiteit wilt aanbieden/lezen, heeft uw account de taaktoewijzing [Managed Identity Operator](/azure/role-based-access-control/built-in-roles#managed-identity-operator) of Managed Identity [Contributor](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) nodig.

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview' -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview HTTP/1.1
```

|Aanvraagheader  |Beschrijving  |
|---------|---------|
|*Content-Type*     | Vereist. Ingesteld op `application/json`.        |
|*Vergunning*     | Vereist. Instellen op `Bearer` een geldig toegangstoken.        |

## <a name="delete-a-user-assigned-managed-identity"></a>Een door de gebruiker toegewezen beheerde identiteit verwijderen

Als u een door de gebruiker toegewezen beheerde identiteit wilt verwijderen, heeft uw account de roltoewijzing [Voor beheerde identiteitsbijdrager](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) nodig.

> [!NOTE]
> Als u een door de gebruiker toegewezen beheerde identiteit verwijdert, wordt de verwijzing niet verwijderd uit een resource waaraan de bron is toegewezen. Als u een door de gebruiker toegewezen beheerde identiteit uit een vm wilt verwijderen met BEHULP VAN CURL, wordt [een door de gebruiker toegewezen identiteit verwijderd van een Azure VM.](qs-configure-rest-vm.md#remove-a-user-assigned identity-from-an-azure-vm)

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X DELETE -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
DELETE https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview HTTP/1.1
```
|Aanvraagheader  |Beschrijving  |
|---------|---------|
|*Content-Type*     | Vereist. Ingesteld op `application/json`.        |
|*Vergunning*     | Vereist. Instellen op `Bearer` een geldig toegangstoken.        |

## <a name="next-steps"></a>Volgende stappen

Voor informatie over het toewijzen van een door de gebruiker toegewezen beheerde identiteit aan een Azure VM/VMSS met BEHULP VAN CURL zie, [Beheerde identiteiten configureren voor Azure-resources op een Azure VM met REST API-aanroepen](qs-configure-rest-vm.md#user-assigned-managed-identity) en [Beheerde identiteiten configureren voor Azure-resources op een virtuele machineschaalset met REST API-aanroepen.](qs-configure-rest-vmss.md#user-assigned-managed-identity)
