---
title: bestand opnemen
description: bestand opnemen
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 01/15/2020
ms.custom: include file
ms.openlocfilehash: cb43c8b8c952d8db6cf450a7015c22c85e7fe4b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76268281"
---
Het `objectIdType` type (of **object-id)** verwijst naar het type identiteit dat aan een rol wordt gegeven. Naast de `DeviceId` `UserDefinedFunctionId` typen en typen komen object-id-typen overeen met eigenschappen van Azure Active Directory-objecten.

De volgende tabel bevat de ondersteunde object-id-typen in Azure Digital Twins:

| Type | Beschrijving |
| --- | --- |
| UserId | Wijst een rol toe aan een gebruiker. |
| DeviceId | Wijst een rol toe aan een apparaat. |
| DomainName | Wijst een rol toe aan een domeinnaam. Elke gebruiker met de opgegeven domeinnaam heeft de toegangsrechten van de bijbehorende rol. |
| TenantId | Wijst een rol toe aan een huurder. Elke gebruiker die deel uitmaakt van de opgegeven Azure AD-tenant-id heeft de toegangsrechten van de bijbehorende rol. |
| ServicePrincipalId | Wijst een rol toe aan een serviceprincipalobject-id. |
| UserDefinedFunctionId | Wijst een rol toe aan een door de gebruiker gedefinieerde functie (UDF). |