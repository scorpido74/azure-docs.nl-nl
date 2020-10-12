---
title: bestand opnemen
description: bestand opnemen
services: active-directory
author: daveba
ms.service: active-directory
ms.subservice: msi
ms.topic: include
ms.date: 05/31/2018
ms.author: daveba
ms.custom: include file
ms.openlocfilehash: dc7849e52b3e00c87d271ee8aef8788d816dff7a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90971035"
---
- Elke beheerde identiteit telt mee voor de quotumlimiet voor objecten in een Azure AD-tenant, zoals beschreven in [Limieten en beperkingen van de Azure AD-service](../articles/active-directory/users-groups-roles/directory-service-limits-restrictions.md).
-   De snelheid waarmee beheerde identiteiten kunnen worden gemaakt, kent de volgende limieten:

    1. Per Azure AD-tenant per Azure-regio: 200 maakbewerkingen per 20 seconden.
    2. Per Azure-abonnement per Azure-regio: 40 maakbewerkingen per 20 seconden.

- Wanneer u aan gebruikers toegewezen beheerde identiteiten maakt, worden alleen alfanumerieke tekens (0-9, a-z, A-Z) en het afbreekstreepje (-) ondersteund. De toewijzing aan een virtuele machine of virtuele-machineschaalset verloopt goed als de naam wordt beperkt tot 24 tekens.
