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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74895265"
---
Azure Storage versleutelt alle gegevens in een opslagaccount in rust. Standaard worden gegevens versleuteld met door Microsoft beheerde sleutels. Voor extra controle over versleutelingssleutels u door de klant beheerde sleutels leveren die u gebruiken voor versleuteling van blob- en bestandsgegevens.

Door de klant beheerde sleutels moeten worden opgeslagen in een Azure Key Vault. U uw eigen sleutels maken en opslaan in een sleutelkluis, of u de Azure Key Vault API's gebruiken om sleutels te genereren. Het opslagaccount en de sleutelkluis moeten zich in dezelfde regio bevinden, maar ze kunnen in verschillende abonnementen zijn. Zie [Azure Storage-versleuteling voor gegevens in rust voor](../articles/storage/common/storage-service-encryption.md)meer informatie over Azure Storage-versleuteling en sleutelbeheer. Zie Wat is Azure Key Vault voor meer informatie over Azure Key [Vault?](../articles/key-vault/key-vault-overview.md)
