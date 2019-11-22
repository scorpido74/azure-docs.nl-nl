---
title: bestand opnemen
description: bestand opnemen
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 11/20/2019
ms.custom: include file
ms.openlocfilehash: e46041a33c12b3fcb40e5a04de11108471e59855
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74307219"
---
Het `objectIdType` (of **object-ID-type**) verwijst naar het type identiteit dat wordt gegeven aan een rol. Naast de typen `DeviceId` en `UserDefinedFunctionId` komen object-id-typen overeen met de eigenschappen van Azure Active Directory-objecten.

De volgende tabel bevat de ondersteunde object-id-typen in azure Digital Apparaatdubbels:

| Type | Beschrijving |
| --- | --- |
| Naam | Hiermee wijst u een rol toe aan een gebruiker. |
| DeviceId | Hiermee wijst u een rol toe aan een apparaat. |
| Naam | Hiermee wijst u een rol toe aan een domein naam. Elke gebruiker met de opgegeven domein naam heeft de toegangs rechten van de bijbehorende rol. |
| TenantId | Hiermee wijst u een rol toe aan een Tenant. Elke gebruiker die lid is van de opgegeven Azure AD-Tenant-ID heeft de toegangs rechten van de bijbehorende rol. |
| ServicePrincipalId | Hiermee wijst u een rol toe aan een Service-Principal object-ID. |
| UserDefinedFunctionId | Hiermee wijst u een rol toe aan een door de gebruiker gedefinieerde functie (UDF). |