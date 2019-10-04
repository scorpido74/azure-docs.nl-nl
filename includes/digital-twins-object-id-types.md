---
title: bestand opnemen
description: bestand opnemen
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 09/30/2019
ms.author: v-adgera
ms.custom: include file
ms.openlocfilehash: 1f4cdf45fb643499f7d6ab717c6f36395ad292c8
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827700"
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