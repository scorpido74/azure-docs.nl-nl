---
title: bestand opnemen
description: bestand opnemen
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/26/2019
ms.author: tamram
ms.custom: include
ms.openlocfilehash: 694501fdaaaa92e898f4973838d86343e29144e3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "74895265"
---
Azure Storage versleutelt alle gegevens in een opslag account in rust. Standaard worden gegevens versleuteld met door micro soft beheerde sleutels. Voor extra controle over versleutelings sleutels kunt u door de klant beheerde sleutels leveren die worden gebruikt voor het versleutelen van BLOB-en bestands gegevens.

Door de klant beheerde sleutels moeten worden opgeslagen in een Azure Key Vault. U kunt uw eigen sleutels maken en deze opslaan in een sleutelkluis of u kunt de Azure Key Vault API's gebruiken om sleutels te genereren. Het opslag account en de sleutel kluis moeten zich in dezelfde regio bevinden, maar ze kunnen zich in verschillende abonnementen bevinden. Zie [Azure Storage versleuteling voor Data-at-rest](../articles/storage/common/storage-service-encryption.md)voor meer informatie over Azure Storage versleuteling en sleutel beheer. Zie [Wat is Azure Key Vault?](../articles/key-vault/key-vault-overview.md) voor meer informatie over Azure Key Vault.
