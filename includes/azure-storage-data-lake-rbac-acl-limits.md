---
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/29/2020
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: 8b793a28c31df185555b9a548df52e4beeccf305
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131629"
---
| Mechanisme | Bereik |Limieten | Ondersteund machtigings niveau |
|---|---|---|---|
| RBAC | Opslag accounts, containers. <br>Kruis resource RBAC-roltoewijzingen op het niveau van een abonnement of resource groep. | 2000 RBAC-roltoewijzingen in een abonnement | RBAC-rollen (ingebouwd of aangepast) |
| MIGREREN| Map, bestand |32 ACL-vermeldingen (in feite 28 ACL-vermeldingen) per bestand en per map. De toegangs-en standaard-Acl's hebben elk een eigen 32 ACL-ingangs limiet. |ACL-machtiging|
