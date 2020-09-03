---
title: Clientbibliotheken voor Azure Key Vault | Microsoft Docs
description: Clientbibliotheken voor Azure Key Vault
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/14/2020
ms.author: mbaldwin
ms.openlocfilehash: 9cc8c1e9406f6b92a445cd52a4ad26e71d83ef5a
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/17/2020
ms.locfileid: "88263781"
---
# <a name="client-libraries-for-azure-key-vault"></a>Clientbibliotheken voor Azure Key Vault

De clientbibliotheken voor Azure Key Vault bieden programmatische toegang tot Key Vault-functionaliteit vanuit diverse talen, waaronder .NET, Python, Java en Javascript.

## <a name="client-libraries-per-language-and-object"></a>Clientbibliotheken per taal en object

Elke SDK heeft afzonderlijke clientbibliotheken voor geheimen, sleutels en certificaten, zoals u kunt zien in de onderstaande tabel.

| Taal | Geheimen | Sleutels | Certificaten |
|--|--|--|--|
| .NET | - [API-naslaginformatie](/dotnet/api/azure.security.keyvault.secrets?view=azure-dotnet)<br>- [NuGet-pakket](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)<br>- [Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Secrets)<br>- [Quickstart](../secrets/quick-create-net.md) | - [API-naslaginformatie](/dotnet/api/azure.security.keyvault.keys?view=azure-dotnet)<br>- [NuGet-pakket](https://www.nuget.org/packages/Azure.Security.KeyVault.Keys/)<br>- [Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Keys) | - [API-naslaginformatie](/dotnet/api/azure.security.keyvault.certificates?view=azure-dotnet)<br>- [NuGet-pakket](https://www.nuget.org/packages/Azure.Security.KeyVault.Certificates/)<br>- [Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Certificates) |
| Python| - [API-naslaginformatie](/python/api/overview/azure/keyvault-secrets-readme?view=azure-python)<br>- [Pypi-pakket](https://pypi.org/project/azure-keyvault-secrets/)<br>- [Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets)<br>- [Quickstart](../secrets/quick-create-python.md) |- [API-naslaginformatie](/python/api/overview/azure/keyvault-keys-readme?view=azure-python)<br>- [Pypi-pakket](https://pypi.org/project/azure-keyvault-keys/)<br>- [Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys)<br>- [Quickstart](../keys/quick-create-python.md) | - [API-naslaginformatie](/python/api/overview/azure/keyvault-certificates-readme?view=azure-python)<br>- [Pypi-pakket](https://pypi.org/project/azure-keyvault-certificates/)<br>- [Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates)<br>- [Quickstart](../certificates/quick-create-python.md) |
| Java | - [API-naslaginformatie](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>- [Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets)<br>- [Quickstart](../secrets/quick-create-java.md) |- [API-naslaginformatie](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)<br>- [Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys) | - [API-naslaginformatie](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)<br>- [Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-certificates) |
| Node.js | - [API-naslaginformatie](/javascript/api/@azure/keyvault-secrets/?view=azure-node-latest)<br>- [NPM-pakket](https://www.npmjs.com/package/@azure/keyvault-secrest)<br>- [Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-secrets)<br>- [Quickstart](../secrets/quick-create-node.md) |- [API-naslaginformatie](/javascript/api/@azure/keyvault-keys/?view=azure-node-latest)<br>- [NPM-pakket](https://www.npmjs.com/package/@azure/keyvault-keys)<br>- [Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-keys)| - [API-naslaginformatie](/javascript/api/@azure/keyvault-certificates/?view=azure-node-latest)<br>- [NPM-pakket](https://www.npmjs.com/package/@azure/keyvault-certificates)<br>- [Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-certificates) |

## <a name="next-steps"></a>Volgende stappen

- Zie de [gids voor Azure Key Vault-ontwikkelaars](developers-guide.md) (vooralsnog Engelstalig).
- Lees meer over [beheerde identiteiten voor Azure Key Vault](managed-identity.md).