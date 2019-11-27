---
title: Meer informatie over het weigeren van toewijzingen voor Azure-resources | Microsoft Docs
description: Meer informatie over het weigeren van toewijzingen in op rollen gebaseerd toegangs beheer (RBAC) voor Azure-resources.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 2c663b587d2e9ee278fc774c2841899b060ccbcf
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2019
ms.locfileid: "74479360"
---
# <a name="understand-deny-assignments-for-azure-resources"></a>Meer informatie over het weigeren van toewijzingen voor Azure-resources

Net als bij een roltoewijzing koppelt een *weigerings toewijzing* een reeks weigerings acties toe aan een gebruiker, groep of Service-Principal in een bepaald bereik voor het weigeren van de toegang. Toewijzingen weigeren blok keren dat gebruikers specifieke Azure-resource acties kunnen uitvoeren, zelfs als een roltoewijzing deze toegang verleent.

In dit artikel wordt beschreven hoe weigerings toewijzingen worden gedefinieerd.

## <a name="how-deny-assignments-are-created"></a>Hoe weiger toewijzingen worden gemaakt?

Geweigerde toewijzingen worden door Azure gemaakt en beheerd om resources te beveiligen. Azure-blauw drukken en Azure Managed apps gebruiken toewijzingen weigeren voor het beveiligen van door het systeem beheerde bronnen. Azure-blauw drukken en Azure Managed apps zijn de enige manier waarop het weigeren van toewijzingen kan worden gemaakt. U kunt niet rechtstreeks uw eigen weigerings toewijzingen maken.  Zie [nieuwe resources beveiligen met Azure-blauw drukken resource vergrendeling](../governance/blueprints/tutorials/protect-new-resources.md)voor meer informatie.

> [!NOTE]
> U kunt niet rechtstreeks uw eigen weigerings toewijzingen maken.

## <a name="compare-role-assignments-and-deny-assignments"></a>Roltoewijzingen vergelijken en toewijzingen weigeren

Het weigeren van toewijzingen volgt een vergelijkbaar patroon als roltoewijzingen, maar er zijn ook enkele verschillen.

| Mogelijkheid | Nieuwe roltoewijzing | Toewijzing weigeren |
| --- | --- | --- |
| Toegang verlenen | :heavy_check_mark: |  |
| Toegang weigeren |  | :heavy_check_mark: |
| Kan rechtstreeks worden gemaakt | :heavy_check_mark: |  |
| Toep assen op een bereik | :heavy_check_mark: | :heavy_check_mark: |
| Principals uitsluiten |  | :heavy_check_mark: |
| Overname voor komen op onderliggende bereiken |  | :heavy_check_mark: |
| Toep assen op [klassieke abonnements beheerders](rbac-and-directory-admin-roles.md) toewijzingen |  | :heavy_check_mark: |

## <a name="deny-assignment-properties"></a>Eigenschappen van toewijzing weigeren

 Een weiger toewijzing heeft de volgende eigenschappen:

> [!div class="mx-tableFixed"]
> | Eigenschap | Vereist | Type | Beschrijving |
> | --- | --- | --- | --- |
> | `DenyAssignmentName` | Ja | Tekenreeks | De weergave naam van de weigerings toewijzing. De namen moeten uniek zijn voor een bepaald bereik. |
> | `Description` | Nee | Tekenreeks | De beschrijving van de weigerings toewijzing. |
> | `Permissions.Actions` | Ten minste één actie of één DataActions | String[] | Een matrix met teken reeksen waarmee de beheer bewerkingen worden opgegeven waarnaar de weigerings toewijzing toegang blokkeert. |
> | `Permissions.NotActions` | Nee | String[] | Een matrix met teken reeksen waarmee de beheer bewerkingen worden opgegeven die moeten worden uitgesloten van de weigerings toewijzing. |
> | `Permissions.DataActions` | Ten minste één actie of één DataActions | String[] | Een matrix met teken reeksen waarmee de gegevens bewerkingen worden opgegeven waarnaar de weigerings toewijzing toegang blokkeert. |
> | `Permissions.NotDataActions` | Nee | String[] | Een matrix met teken reeksen waarmee de gegevens bewerkingen worden opgegeven die moeten worden uitgesloten van de weigerings toewijzing. |
> | `Scope` | Nee | Tekenreeks | Een teken reeks die het bereik opgeeft waarop de weigerings toewijzing van toepassing is. |
> | `DoNotApplyToChildScopes` | Nee | Booleaans | Hiermee geeft u op of de weigerings toewijzing van toepassing is op onderliggende bereiken. De standaard waarde is False. |
> | `Principals[i].Id` | Ja | String[] | Een matrix van Azure AD Principal-object-Id's (gebruiker, groep, Service-Principal of beheerde identiteit) waarop de weigerings toewijzing van toepassing is. Ingesteld op een lege GUID `00000000-0000-0000-0000-000000000000` om alle principals weer te geven. |
> | `Principals[i].Type` | Nee | String[] | Een matrix van object typen vertegenwoordigd door principals [i]. id. set to `SystemDefined` om alle principals weer te geven. |
> | `ExcludePrincipals[i].Id` | Nee | String[] | Een matrix van Azure AD Principal-object-Id's (gebruiker, groep, Service-Principal of beheerde identiteit) waarop de weigerings toewijzing niet van toepassing is. |
> | `ExcludePrincipals[i].Type` | Nee | String[] | Een matrix met object typen vertegenwoordigd door ExcludePrincipals [i]. id. |
> | `IsSystemProtected` | Nee | Booleaans | Hiermee wordt aangegeven of deze deny-toewijzing is gemaakt door Azure en niet kan worden bewerkt of verwijderd. Op dit moment worden alle deny-toewijzingen met het systeem beveiligd. |

## <a name="the-all-principals-principal"></a>De principal alle principals

Voor het ondersteunen van weigerings toewijzingen is een door het systeem gedefinieerde principal met *de naam alle principals* geïntroduceerd. Deze principal vertegenwoordigt alle gebruikers, groepen, service-principals en beheerde identiteiten in een Azure AD-adres lijst. Als de principal-ID een nul-GUID `00000000-0000-0000-0000-000000000000` is en het Principal-type `SystemDefined`is, vertegenwoordigt de principal alle principals. In Azure PowerShell uitvoer ziet u dat alle principals er als volgt uitzien:

```azurepowershell
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
```

Alle principals kunnen worden gecombineerd met `ExcludePrincipals` om alle principals behalve sommige gebruikers te weigeren. Voor alle principals gelden de volgende beperkingen:

- Kan alleen worden gebruikt in `Principals` en kan niet worden gebruikt in `ExcludePrincipals`.
- `Principals[i].Type` moet zijn ingesteld op `SystemDefined`.

## <a name="next-steps"></a>Volgende stappen

* [Toewijzing van toewijzingen voor Azure-resources met behulp van de Azure Portal weer geven](deny-assignments-portal.md)
* [Functie definities voor Azure-resources begrijpen](role-definitions.md)
