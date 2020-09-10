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
ms.openlocfilehash: 34321ac9baf3d3cb43dace33b574cb582c2d72a0
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/02/2020
ms.locfileid: "89378947"
---
# <a name="client-libraries-for-azure-key-vault"></a>Clientbibliotheken voor Azure Key Vault

De clientbibliotheken voor Azure Key Vault bieden programmatische toegang tot Key Vault-functionaliteit vanuit diverse talen, waaronder .NET, Python, Java en JavaScript.

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
- Meer informatie over het [verifiëren bij Key Vault](authentication.md)
