---
title: Role-based Access Control gebruiken in Azure API Management | Microsoft Documenten
description: Meer informatie over het gebruik van de ingebouwde rollen en het maken van aangepaste rollen in Azure API Management
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 364cd53e-88fb-4301-a093-f132fa1f88f5
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: ed0cd51fc686735f2d9c110ce46d5904107cafc2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430616"
---
# <a name="how-to-use-role-based-access-control-in-azure-api-management"></a>Op rollen gebaseerd toegangsbeheer gebruiken in API Management

Azure API Management is gebaseerd op Azure Role-Based Access Control (RBAC) om fijnkorrelig toegangsbeheer mogelijk te maken voor API Management-services en -entiteiten (bijvoorbeeld API's en -beleid). Dit artikel geeft u een overzicht van de ingebouwde en aangepaste rollen in API Management. Zie [Aan de slag met toegangsbeheer in de Azure-portal](https://azure.microsoft.com/documentation/articles/role-based-access-control-what-is/)voor meer informatie over toegangsbeheer in de Azure-portal.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="built-in-roles"></a>Ingebouwde rollen

API Management biedt momenteel drie ingebouwde rollen en zal in de nabije toekomst nog twee rollen toevoegen. Deze rollen kunnen worden toegewezen op verschillende scopes, waaronder abonnement, resourcegroep en afzonderlijke API-beheerinstantie. Als u bijvoorbeeld de rol 'API Management Service Reader' toewijst aan een gebruiker op het niveau van de brongroep, heeft de gebruiker toegang tot alle API-beheerinstanties binnen de brongroep. 

De volgende tabel bevat korte beschrijvingen van de ingebouwde rollen. U deze rollen toewijzen met behulp van de Azure-portal of andere hulpprogramma's, waaronder Azure [PowerShell,](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell) [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)en [REST API.](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest) Zie [Roltoewijzingen gebruiken om toegang tot uw Azure-abonnementsbronnen te beheren voor](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)meer informatie over het toewijzen van ingebouwde rollen.

| Rol          | Toegang lezen<sup>[1]</sup> | Schrijftoegang<sup>[2]</sup> | Servicecreatie, verwijdering, schalen, VPN en aangepaste domeinconfiguratie | Toegang tot de verouderde uitgeversportal | Beschrijving
| ------------- | ---- | ---- | ---- | ---- | ---- 
| Api-beheerservice bijdrager | ✓ | ✓ | ✓ | ✓ | Super gebruiker. Heeft volledige CRUD-toegang tot API Management-services en -entiteiten (bijvoorbeeld API's en -beleid). Heeft toegang tot de legacy publisher portal. |
| API Management Service Reader | ✓ | | || Heeft alleen-lezen toegang tot API Management-services en -entiteiten. |
| API Management Service Operator | ✓ | | ✓ | | Kan API Management-services beheren, maar geen entiteiten.|
| API-beheerserviceeditor<sup>*</sup> | ✓ | ✓ | |  | Kan API Management-entiteiten beheren, maar geen services.|
| ContentManager API-beheer<sup>*</sup> | ✓ | | | ✓ | Kan de ontwikkelaarsportal beheren. Alleen-lezen toegang tot services en entiteiten.|

<sup>[1] Lees de toegang tot API Management-services en -entiteiten (bijvoorbeeld API's en -beleid).</sup>

<sup>[2] Schrijf toegang tot API Management-services en -entiteiten, behalve de volgende bewerkingen: het maken, verwijderen en schalen van instanties; VPN-configuratie; en aangepaste domeininstelling.</sup>

<sup>\*De functie Serviceeditor is beschikbaar nadat we alle beheerdersgebruikersinterface van de bestaande uitgeversportal hebben gemigreerd naar de Azure-portal. De rol Content Manager is beschikbaar nadat de publisher portal is aangepast om alleen functionaliteit te bevatten die betrekking heeft op het beheren van de ontwikkelaarsportal.</sup>  

## <a name="custom-roles"></a>Aangepaste rollen

Als geen van de ingebouwde rollen aan uw specifieke behoeften voldoet, kunnen aangepaste rollen worden gemaakt om gedetailleerder toegangsbeheer te bieden voor API-beheerentiteiten. U bijvoorbeeld een aangepaste rol maken die alleen-lezen toegang heeft tot een API-beheerservice, maar alleen schrijftoegang heeft tot één specifieke API. Zie Aangepaste rollen in [Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)voor meer informatie over aangepaste rollen. 

> [!NOTE]
> Als u een API-beheerexemplaar in de Azure-portal wilt ```Microsoft.ApiManagement/service/read``` kunnen zien, moet een aangepaste rol de actie bevatten.

Wanneer u een aangepaste rol maakt, u gemakkelijker beginnen met een van de ingebouwde rollen. Bewerk de kenmerken om **Acties,** **NotActions**of **AssignableScopes**toe te voegen en sla de wijzigingen op als een nieuwe rol. Het volgende voorbeeld begint met de rol 'API Management Service Reader' en maakt een aangepaste rol genaamd 'Calculator API Editor'. U de aangepaste rol toewijzen aan een specifieke API. Bijgevolg heeft deze rol alleen toegang tot die API. 

```powershell
$role = Get-AzRoleDefinition "API Management Service Reader Role"
$role.Id = $null
$role.Name = 'Calculator API Contributor'
$role.Description = 'Has read access to Contoso APIM instance and write access to the Calculator API.'
$role.Actions.Add('Microsoft.ApiManagement/service/apis/write')
$role.Actions.Add('Microsoft.ApiManagement/service/apis/*/write')
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add('/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>')
New-AzRoleDefinition -Role $role
New-AzRoleAssignment -ObjectId <object ID of the user account> -RoleDefinitionName 'Calculator API Contributor' -Scope '/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>'
```

Het artikel over [de beheerbewerkingen van azure resource manager-resources](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement) bevat de lijst met machtigingen die kunnen worden verleend op API-beheerniveau.

## <a name="video"></a>Video


> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Role-Based-Access-Control-in-API-Management/player]
>
>

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over toegangsbeheer op basis van rollen in Azure:
  * [Aan de slag met toegangsbeheer in de Azure-portal](../role-based-access-control/overview.md)
  * [Roltoewijzingen gebruiken voor het beheer van de toegang tot de resources van uw Azure-abonnement](../role-based-access-control/role-assignments-portal.md)
  * [Aangepaste rollen in Azure RBAC](../role-based-access-control/custom-roles.md)
  * [Azure Resource Manager-resourceproviderbewerkingen](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement)
