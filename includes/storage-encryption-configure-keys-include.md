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
ms.openlocfilehash: f846d75833b3a796e24fd23c5f841ea24a8d1876
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2019
ms.locfileid: "74665900"
---
Azure Storage versleutelt alle gegevens in een opslag account in rust. Standaard worden gegevens versleuteld met door micro soft beheerde sleutels. Voor extra controle over versleutelings sleutels kunt u door de klant beheerde sleutels leveren op het niveau van het opslag account.

Door de klant beheerde sleutels moeten worden opgeslagen in een Azure Key Vault. U kunt zelf sleutels maken en deze opslaan in een sleutel kluis, of u kunt de Azure Key Vault-Api's gebruiken om sleutels te genereren. Het opslag account en de sleutel kluis moeten zich in dezelfde regio bevinden, maar ze kunnen zich in verschillende abonnementen bevinden. Zie [Azure Storage versleuteling voor Data-at-rest](../articles/storage/common/storage-service-encryption.md)voor meer informatie over Azure Storage versleuteling en sleutel beheer. Zie [Wat is Azure Key Vault?](../articles/key-vault/key-vault-overview.md) voor meer informatie over Azure Key Vault.
