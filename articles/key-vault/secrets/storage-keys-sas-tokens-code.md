---
title: Shared Acces Signature-tokens in code ophalen | Azure Key Vault
description: De functie voor beheerde opslagaccounts biedt een naadloze integratie tussen Azure Key Vault en een Azure-opslagaccount. In dit voorbeeld wordt de Azure SDK voor .NET gebruikt om SAS-tokens te beheren.
ms.topic: tutorial
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: afe7d5ce3dd1756ddb9e33fe402fb2eb699ce8f7
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2020
ms.locfileid: "90007414"
---
# <a name="fetch-shared-access-signature-tokens-in-code"></a>Shared Acces Signature-tokens in code ophalen

U kunt uw opslagaccount met SAS-tokens (Shared Access Signature) beheren die zijn opgeslagen in uw sleutelkluis. Zie [Beperkte toegang verlenen tot Azure Storage-resources via SAS](../../storage/common/storage-sas-overview.md) voor meer informatie.

> [!NOTE]
> U kunt het beste [Op rollen gebaseerd toegangsbeheer (RBAC)](../../storage/common/storage-auth-aad.md) gebruiken om uw opslagaccount te beveiligen voor betere beveiliging en het gebruiksgemak van gedeelde-sleutelverificatie.

In dit artikel vindt u voorbeelden van .NET-code waarmee een SAS-definitie wordt gemaakt en SAS-tokens worden opgehaald. Bekijk ons [ShareLink](https://docs.microsoft.com/samples/azure/azure-sdk-for-net/share-link/)-voorbeeld voor volledige informatie, inclusief de gegenereerde client voor met Key Vault beheerde opslagaccounts. Voor informatie over het maken en opslaan van SAS-tokens, raadpleegt u [Sleutels voor opslagaccounts beheren met Key Vault en de Azure CLI](overview-storage-keys.md) of [Sleutels voor opslagaccounts beheren met Key Vault en Azure PowerShell](overview-storage-keys-powershell.md).

## <a name="code-samples"></a>Codevoorbeelden

In het volgende voorbeeld maken we een SAS-sjabloon:

:::code language="csharp" source="~/azure-sdk-for-net/sdk/keyvault/samples/sharelink/Program.cs" range="91-97":::

Met deze sjabloon kunnen we een SAS-definitie maken met behulp van 

:::code language="csharp" source="~/azure-sdk-for-net/sdk/keyvault/samples/sharelink/Program.cs" range="137-156":::

Nadat de SAS-definitie is gemaakt, kunt u SAS-tokens zoals geheimen ophalen met behulp van een `SecretClient`. De geheime naam moet vooraf worden gegaan door de naam van het opslagaccount, gevolgd door een streepje:

:::code language="csharp" source="~/azure-sdk-for-net/sdk/keyvault/samples/sharelink/Program.cs" range="52-58":::

Als uw handtekeningtoken voor gedeelde toegang bijna is verlopen, kunt u hetzelfde geheim nogmaals ophalen om een nieuw token te genereren.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [Beperkte toegang verlenen tot Azure Storage-resources via SAS](../../storage/common/storage-sas-overview.md).
- Meer informatie over [Sleutels voor opslagaccounts beheren met Key Vault en de Azure CLI](overview-storage-keys.md) of [Azure PowerShell](overview-storage-keys-powershell.md).
- Zie het volledige [ShareLink](https://docs.microsoft.com/samples/azure/azure-sdk-for-net/share-link/)-voorbeeld.
- Meer [Key Vault-voorbeelden](https://docs.microsoft.com/samples/browse/?expanded=azure&products=azure-key-vault)
- Zie [Voorbeelden van sleutels voor beheerde opslagaccounts](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
