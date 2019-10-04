---
title: bestand opnemen
description: bestand opnemen
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 09/30/2019
ms.custom: include file
ms.openlocfilehash: 12d4278171d43fdaf8613a1c91bdbffc269adc56
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949048"
---
Het `objectIdType` (of **object-ID-type**) verwijst naar het type identiteit dat wordt gegeven aan een rol. Naast het `DeviceId`-en `UserDefinedFunctionId`-type komen object-id-typen overeen met eigenschappen van Azure Active Directory-objecten.

De volgende tabel bevat de ondersteunde object-id-typen in azure Digital Apparaatdubbels:

| type | Description |
| --- | --- |
| Naam | Hiermee wijst u een rol toe aan een gebruiker. |
| DeviceId | Hiermee wijst u een rol toe aan een apparaat. |
| Naam | Hiermee wijst u een rol toe aan een domein naam. Elke gebruiker met de opgegeven domein naam heeft de toegangs rechten van de bijbehorende rol. |
| TenantId | Hiermee wijst u een rol toe aan een Tenant. Elke gebruiker die lid is van de opgegeven Azure AD-Tenant-ID heeft de toegangs rechten van de bijbehorende rol. |
| ServicePrincipalId | Hiermee wijst u een rol toe aan een Service-Principal object-ID. |
| UserDefinedFunctionId | Hiermee wijst u een rol toe aan een door de gebruiker gedefinieerde functie (UDF). |