---
title: Inzicht in weigeringstoewijzingen voor Azure-resources
description: Meer informatie over weigeringstoewijzingen in rbac(role-based access control) voor Azure-resources.
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
ms.date: 03/26/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: db249ccde1026cd468a1c30942891119482697ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372489"
---
# <a name="understand-deny-assignments-for-azure-resources"></a>Inzicht in weigeringstoewijzingen voor Azure-resources

Net als bij een roltoewijzing koppelt een *weigeringstoewijzing* een reeks weigeringsacties aan een gebruiker, groep of serviceprincipal op een bepaalde scope met het oog op het weigeren van toegang. Toewijzingen weigeren blokkeren gebruikers om specifieke Azure-bronacties uit te voeren, zelfs als een roltoewijzing hen toegang verleent.

In dit artikel wordt beschreven hoe weigeringstoewijzingen worden gedefinieerd.

## <a name="how-deny-assignments-are-created"></a>Hoe weigeringsopdrachten worden gemaakt

Weigeringstoewijzingen worden gemaakt en beheerd door Azure om resources te beschermen. Azure Blueprints en Azure managed apps gebruiken weigeringstoewijzingen om systeembeheerbronnen te beschermen. Azure Blueprints en Azure managed apps zijn de enige manier waarop weigeringstoewijzingen kunnen worden gemaakt. Je niet direct je eigen weigeringsopdrachten maken. Zie [Resourcevergrendeling in Azure Blueprints](../governance/blueprints/concepts/resource-locking.md)begrijpen in Azure Blueprints voor meer informatie over hoe Blauwdrukken weigerten gebruiken om resources te vergrendelen.

> [!NOTE]
> Je niet direct je eigen weigeringsopdrachten maken.

## <a name="compare-role-assignments-and-deny-assignments"></a>Roltoewijzingen vergelijken en opdrachten weigeren

Weigeren opdrachten volgen een vergelijkbaar patroon als roltoewijzingen, maar hebben ook enkele verschillen.

| Mogelijkheid | Nieuwe roltoewijzing | Toewijzing weigeren |
| --- | --- | --- |
| Toegang verlenen | :heavy_check_mark: |  |
| Toegang weigeren |  | :heavy_check_mark: |
| Kan direct worden gemaakt | :heavy_check_mark: |  |
| Solliciteren op een scope | :heavy_check_mark: | :heavy_check_mark: |
| Aangevers uitsluiten |  | :heavy_check_mark: |
| Overerving naar onderliggende scopes voorkomen |  | :heavy_check_mark: |
| Toepassen op [klassieke abonnementsbeheerderstoewijzingen](rbac-and-directory-admin-roles.md) |  | :heavy_check_mark: |

## <a name="deny-assignment-properties"></a>Toewijzingseigenschappen weigeren

 Een weigeringstoewijzing heeft de volgende eigenschappen:

> [!div class="mx-tableFixed"]
> | Eigenschap | Vereist | Type | Beschrijving |
> | --- | --- | --- | --- |
> | `DenyAssignmentName` | Ja | Tekenreeks | De weergavenaam van de weigeringsopdracht. Namen moeten uniek zijn voor een bepaald bereik. |
> | `Description` | Nee | Tekenreeks | De beschrijving van de weigeringsopdracht. |
> | `Permissions.Actions` | Ten minste één acties of één DataActions | Tekenreeks[] | Een array met tekenreeksen die de beheerbewerkingen opgeven waartoe de toewijzing voor weigeren toegang blokkeert. |
> | `Permissions.NotActions` | Nee | Tekenreeks[] | Een array met tekenreeksen die de beheerbewerkingen opgeven die moeten worden uitgesloten van de weigeringstoewijzing. |
> | `Permissions.DataActions` | Ten minste één acties of één DataActions | Tekenreeks[] | Een array met tekenreeksen die de gegevensbewerkingen opgeven waartoe de toewijzing voor weigeren toegang blokkeert. |
> | `Permissions.NotDataActions` | Nee | Tekenreeks[] | Een array met tekenreeksen die de gegevensbewerkingen opgeven die moeten worden uitgesloten van de toewijzing weigeren. |
> | `Scope` | Nee | Tekenreeks | Een tekenreeks die het bereik opgeeft waarop de toewijzing voor weigeren van toepassing is. |
> | `DoNotApplyToChildScopes` | Nee | Booleaans | Hiermee geeft u op of de toewijzing weigeren van toepassing is op onderliggende scopes. De standaardwaarde is onwaar. |
> | `Principals[i].Id` | Ja | Tekenreeks[] | Een array van Azure AD-hoofdobject-id's (gebruiker, groep, serviceprincipal of beheerde identiteit) waarop de toewijzing voor weigeren van toepassing is. Stel in op `00000000-0000-0000-0000-000000000000` een lege GUID om alle principals te vertegenwoordigen. |
> | `Principals[i].Type` | Nee | Tekenreeks[] | Een reeks objecttypen vertegenwoordigd door Principals[i].Id. Ingesteld op `SystemDefined` alle principals te vertegenwoordigen. |
> | `ExcludePrincipals[i].Id` | Nee | Tekenreeks[] | Een array met Azure AD-hoofdobject-id's (gebruiker, groep, serviceprincipal of beheerde identiteit) waarop de toewijzing voor weigeren niet van toepassing is. |
> | `ExcludePrincipals[i].Type` | Nee | Tekenreeks[] | Een array van objecttypen vertegenwoordigd door ExcludePrincipals[i].Id. |
> | `IsSystemProtected` | Nee | Booleaans | Hiermee geeft u op of deze weigeringstoewijzing is gemaakt door Azure en niet kan worden bewerkt of verwijderd. Momenteel zijn alle weigeringsopdrachten systeembeveiligd. |

## <a name="the-all-principals-principal"></a>De principal

Om weigeringsopdrachten te ondersteunen, is een systeemgedefinieerde principal met de naam *All Principals* geïntroduceerd. Deze principal vertegenwoordigt alle gebruikers, groepen, serviceprincipals en beheerde identiteiten in een Azure AD-map. Als de hoofd-ID een `00000000-0000-0000-0000-000000000000` nul-GUID `SystemDefined`is en het hoofdtype is, vertegenwoordigt de principal alle principals. In Azure PowerShell-uitvoer ziet Alle principals er als volgt uit:

```azurepowershell
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
```

Alle principals kunnen `ExcludePrincipals` worden gecombineerd met het weigeren van alle opdrachtgevers, behalve sommige gebruikers. Alle opdrachtgevers hebben de volgende beperkingen:

- Kan alleen worden `Principals` gebruikt in `ExcludePrincipals`en kan niet worden gebruikt in .
- `Principals[i].Type`moeten worden `SystemDefined`ingesteld op .

## <a name="next-steps"></a>Volgende stappen

* [Zelfstudie: Nieuwe resources beveiligen met Azure Blueprints-bronvergrendelingen](../governance/blueprints/tutorials/protect-new-resources.md)
* [Toewijzingen voor Azure-resources voor Azure-resources weergeven met behulp van de Azure-portal](deny-assignments-portal.md)
