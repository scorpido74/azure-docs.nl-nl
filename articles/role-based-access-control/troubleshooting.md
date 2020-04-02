---
title: Problemen met Azure RBAC oplossen
description: Problemen met Azure-rolebased access control (Azure RBAC) oplossen.
services: azure-portal
documentationcenter: na
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: 09d5b7a126a1b8832bfe40e2e25dd4000d5d9155
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548287"
---
# <a name="troubleshoot-azure-rbac"></a>Problemen met Azure RBAC oplossen

In dit artikel worden enkele veelgestelde vragen beantwoord over Azure-rolebased access control (Azure RBAC), zodat u weet wat u verwachten bij het gebruik van de rollen en toegangsproblemen oplossen.

## <a name="azure-role-assignments-limit"></a>Azure-taaktoewijzingenlimiet

Azure ondersteunt maximaal **2000** roltoewijzingen per abonnement. Als u het foutbericht 'Er kunnen geen roltoewijzingen meer worden gemaakt (code: RoleAssignmentLimitExceeded)" wanneer u een rol probeert toe te wijzen, probeert u het aantal roltoewijzingen in het abonnement te verminderen.

> [!NOTE]
> De limiet voor toewijzingen voor **2000** per abonnement is vast en kan niet worden verhoogd.

Als u dicht bij deze limiet komt, u het aantal roltoewijzingen hier verminderen:

- Voeg gebruikers toe aan groepen en wijs in plaats daarvan rollen toe aan de groepen. 
- Combineer meerdere ingebouwde rollen met een aangepaste rol. 
- Maak algemene roltoewijzingen op een hoger bereik, zoals een abonnement of beheergroep.
- Als u Azure AD Premium P2 hebt, maakt u roltoewijzingen in aanmerking voor [Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md) in plaats van permanent toegewezen. 
- Voeg een aanvullend abonnement toe. 

Als u het aantal roltoewijzingen wilt opdoen, u de grafiek bekijken [op de pagina IAM (Access Control)](role-assignments-list-portal.md#list-number-of-role-assignments) in de Azure-portal. U ook de volgende Azure PowerShell-opdrachten gebruiken:

```azurepowershell
$scope = "/subscriptions/<subscriptionId>"
$ras = Get-AzRoleAssignment -Scope $scope | Where-Object {$_.scope.StartsWith($scope)}
$ras.Count
```

## <a name="problems-with-azure-role-assignments"></a>Problemen met Azure-roltoewijzingen

- Als u een roltoewijzing niet toevoegen aan de Azure-portal voor **toegangsbeheer (IAM),** omdat de optie**Roltoewijzing** **toevoegen** > is uitgeschakeld of omdat u de machtigingsfout 'De client met object-id `Microsoft.Authorization/roleAssignments/write` heeft geen autorisatie heeft om actie uit te voeren' krijgt, controleert u of u momenteel bent aangemeld bij een gebruiker die een rol heeft toegewezen die de machtiging heeft, zoals [Eigenaar](built-in-roles.md#owner) of [Gebruikerstoegangsbeheerder,](built-in-roles.md#user-access-administrator) op het bereik dat u probeert toe te wijzen.

## <a name="problems-with-custom-roles"></a>Problemen met aangepaste rollen

- Als u stappen nodig hebt voor het maken van een aangepaste rol, raadpleegt u de aangepaste rolzelfstudies met de [Azure-portal](custom-roles-portal.md) (momenteel in preview), [Azure PowerShell](tutorial-custom-role-powershell.md)of [Azure CLI](tutorial-custom-role-cli.md).
- Als u een bestaande aangepaste rol niet bijwerken, controleert u of u momenteel `Microsoft.Authorization/roleDefinition/write` bent aangemeld bij een gebruiker waaraan een rol is toegewezen die de machtiging heeft, zoals [Eigenaar](built-in-roles.md#owner) of [Beheerder van gebruikerstoegang.](built-in-roles.md#user-access-administrator)
- Als u een aangepaste rol niet verwijderen en het foutbericht 'Er zijn bestaande roltoewijzingen die verwijzen naar rol (code: RoleDefinitionHasAssignments)' krijgt, zijn er roltoewijzingen die nog steeds de aangepaste rol gebruiken. Verwijder deze roltoewijzingen en probeer de aangepaste rol opnieuw te verwijderen.
- Als tijdens het maken van een nieuwe aangepaste rol het foutbericht 'Role definition limit exceeded. No more role definitions can be created (RoleDefinitionLimitExceeded)' Er kunnen geen roldefinities meer worden gemaakt (code: RoleDefinitionLimitExceeded)" wanneer u een nieuwe aangepaste rol probeert te maken, verwijdert u aangepaste rollen die niet worden gebruikt. Azure ondersteunt maximaal **5000** aangepaste rollen in een map. (Voor Azure Germany en Azure China 21Vianet is de limiet 2000 aangepaste rollen.)
- Als u een fout krijgt die vergelijkbaar is met 'De client heeft toestemming om actie uit te voeren 'Microsoft.Authorization/roleDefinitions/write' op bereik '/abonnementen/{subscriptionid}', maar het gekoppelde abonnement niet is gevonden' wanneer u een aangepaste rol probeert bij te werken, controleert u of een of meer [toewijsbare scopes](role-definitions.md#assignablescopes) in de map zijn verwijderd. Als het bereik is verwijderd, maakt u een ondersteuningsticket, aangezien hiervoor op dit moment geen selfserviceoplossing beschikbaar is.

## <a name="custom-roles-and-management-groups"></a>Aangepaste rollen en beheergroepen

- U slechts één `AssignableScopes` beheergroep definiëren in een aangepaste rol. Het toevoegen van `AssignableScopes` een beheergroep aan is momenteel in preview.
- Aangepaste rollen `DataActions` met kan niet worden toegewezen aan het bereik van de beheergroep.
- Azure Resource Manager valideert het bestaan van de beheergroep niet in het toewijsbare bereik van de roldefinitie.
- Zie [Uw resources ordenen met Azure-beheergroepen](../governance/management-groups/overview.md#custom-rbac-role-definition-and-assignment)voor meer informatie over aangepaste rollen en beheergroepen.

## <a name="transferring-a-subscription-to-a-different-directory"></a>Een abonnement overzetten naar een andere directory

- Zie [Het eigendom van een Azure-abonnement overzetten naar](../cost-management-billing/manage/billing-subscription-transfer.md)een ander account als u stappen nodig hebt voor het overzetten van een abonnement naar een andere Azure-map.
- Als u een abonnement overzet naar een andere Azure AD-map, worden alle roltoewijzingen **permanent** verwijderd uit de bron-Azure AD-map en worden ze niet gemigreerd naar de doelmap van Azure AD. U moet uw roltoewijzingen opnieuw maken in de doelmap. U moet ook handmatig beheerde identiteiten opnieuw maken voor Azure-bronnen. Zie [veelgestelde vragen en bekende problemen met beheerde identiteiten](../active-directory/managed-identities-azure-resources/known-issues.md)voor meer informatie.
- Als u een Azure AD Global Administrator bent en u geen toegang hebt tot een abonnement nadat het is overgedragen tussen mappen, gebruikt u het **schakelsysteem Access-beheer voor Azure-resources** om [uw toegang](elevate-access-global-admin.md) tijdelijk te verhogen om toegang tot het abonnement te krijgen.

## <a name="issues-with-service-admins-or-co-admins"></a>Problemen met servicebeheerders of co-beheerders

- Zie [Azure-abonnementsbeheerders toevoegen of wijzigen](../cost-management-billing/manage/add-change-subscription-administrator.md) en rollen voor klassieke [abonnementsbeheerders, Azure-rollen en Azure AD-beheerders](rbac-and-directory-admin-roles.md)als u problemen ondervindt met Servicebeheerder of Co-beheerders.

## <a name="access-denied-or-permission-errors"></a>Toegangsfouten of machtigingsfouten

- Als u de machtigingsfout 'De client met object-id heeft geen toestemming om actie uit te voeren over bereik (code: Autorisatiemislukt)' krijgt wanneer u een resource probeert te maken, controleert u of u momenteel bent aangemeld bij een gebruiker die een rol heeft toegewezen die schrijftoestemming heeft voor de resource in het geselecteerde bereik. Als u bijvoorbeeld virtuele machines in een resourcegroep wilt beheren, moet u de rol [Inzender voor virtuele machines](built-in-roles.md#virtual-machine-contributor) toewijzen aan de resourcegroep (of bovenliggend bereik). Voor een lijst met machtigingen voor elke ingebouwde rol, raadpleegt u [Ingebouwde rollen voor Azure-resources](built-in-roles.md).
- Als u de machtigingsfout 'U hebt geen toestemming hebt om een ondersteuningsverzoek te maken' krijgt wanneer u een ondersteuningsticket probeert te maken `Microsoft.Support/supportTickets/write` of bij te werken, controleert u of u momenteel bent aangemeld bij een gebruiker die een rol heeft toegewezen die de toestemming heeft, zoals [ondersteuningsaanvraaginzender.](built-in-roles.md#support-request-contributor)

## <a name="role-assignments-with-unknown-security-principal"></a>Rolopdrachten met onbekende beveiligingsprincipal

Als u een rol toewijst aan een beveiligingsprincipal (gebruiker, groep, serviceprincipal of beheerde identiteit) en vervolgens die beveiligingsprincipal verwijdert zonder de roltoewijzing te verwijderen, wordt het beveiligingsprincipaltype voor de roltoewijzing vermeld als **Onbekend**. In de volgende schermopname ziet u een voorbeeld in Azure Portal. De hoofdnaam van de beveiliging wordt vermeld als **Identiteit verwijderd** en Identiteit bestaat **niet meer**. 

![Web-app-brongroep](./media/troubleshooting/unknown-security-principal.png)

Als u deze roltoewijzing aangeeft met Azure `DisplayName` PowerShell, ziet u een lege en een `ObjectType` ingesteld op Onbekend. [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) retourneert bijvoorbeeld een roltoewijzing die vergelijkbaar is met de volgende:

```
RoleAssignmentId   : /subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
Scope              : /subscriptions/11111111-1111-1111-1111-111111111111
DisplayName        :
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 33333333-3333-3333-3333-333333333333
ObjectType         : Unknown
CanDelegate        : False
```

Als u deze roltoewijzing aangeeft met Azure CLI, `principalName`ziet u ook een lege . De [lijst met az-rollentoewijzing](/cli/azure/role/assignment#az-role-assignment-list) retourneert bijvoorbeeld een roltoewijzing die vergelijkbaar is met de volgende:

```
{
    "canDelegate": null,
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222",
    "name": "22222222-2222-2222-2222-222222222222",
    "principalId": "33333333-3333-3333-3333-333333333333",
    "principalName": "",
    "roleDefinitionId": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/ba92f5b4-2d11-453d-a403-e96b0029c9fe",
    "roleDefinitionName": "Storage Blob Data Contributor",
    "scope": "/subscriptions/11111111-1111-1111-1111-111111111111",
    "type": "Microsoft.Authorization/roleAssignments"
}
```

Het is geen probleem om deze roltoewijzingen te verlaten, maar u ze verwijderen met stappen die vergelijkbaar zijn met andere roltoewijzingen. Zie [Azure Portal,](role-assignments-portal.md#remove-a-role-assignment) [Azure PowerShell](role-assignments-powershell.md#remove-a-role-assignment)of [Azure CLI](role-assignments-cli.md#remove-a-role-assignment) voor informatie over het verwijderen van roltoewijzingen

Als u in PowerShell de roltoewijzingen probeert te verwijderen met de naam object-id en roldefinitie en meer dan één roltoewijzing overeenkomt met uw parameters, krijgt u het foutbericht: "De verstrekte informatie wordt niet toegewezen aan een roltoewijzing". Het volgende toont een voorbeeld van het foutbericht:

```
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor"

Remove-AzRoleAssignment : The provided information does not map to a role assignment.
At line:1 char:1
+ Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : CloseError: (:) [Remove-AzRoleAssignment], KeyNotFoundException
+ FullyQualifiedErrorId : Microsoft.Azure.Commands.Resources.RemoveAzureRoleAssignmentCommand
```

Als u dit foutbericht ontvangt, moet `-Scope` `-ResourceGroupName` u ook de of parameters opgeven.

```
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor" - Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="role-assignment-changes-are-not-being-detected"></a>Wijzigingen in roltoewijzing worden niet gedetecteerd

Azure Resource Manager caches soms configuraties en gegevens om de prestaties te verbeteren. Wanneer u roltoewijzingen toevoegt of verwijdert, kan het tot 30 minuten duren voordat wijzigingen van kracht worden. Als u de Azure-portal, Azure PowerShell of Azure CLI gebruikt, u een vernieuwing van uw functietoewijzingswijzigingen forceren door u af te melden en u aan te melden. Als u wijzigingen in roltoewijzing doorvoert met REST API-aanroepen, u een vernieuwing forceren door uw toegangstoken te vernieuwen.

Als u een roltoewijzing toevoegt of verwijdert bij `DataActions`het bereik van de beheergroep en de rol heeft, wordt de toegang op het gegevensvlak mogelijk enkele uren niet bijgewerkt. Dit geldt alleen voor het bereik van de beheergroep en het gegevensvlak.

## <a name="web-app-features-that-require-write-access"></a>Web-app-functies waarvoor schrijftoegang vereist is

Als u een gebruiker alleen-lezen toegang verleent tot één web-app, worden sommige functies uitgeschakeld die u mogelijk niet verwacht. De volgende beheermogelijkheden vereisen **schrijftoegang** tot een web-app (inzender of eigenaar) en zijn niet beschikbaar in een alleen-lezen scenario.

* Opdrachten (zoals starten, stoppen, enz.)
* Instellingen wijzigen, zoals algemene configuratie, schaalinstellingen, back-upinstellingen en controle-instellingen
* Toegang tot publicatiereferenties en andere geheimen, zoals app-instellingen en verbindingstekenreeksen
* Logboeken streamen
* Configuratie van diagnostische logboeken
* Console (opdrachtprompt)
* Actieve en recente implementaties (voor lokale git continue implementatie)
* Geschatte uitgaven
* Webtests
* Virtueel netwerk (alleen zichtbaar voor een lezer als een virtueel netwerk eerder is geconfigureerd door een gebruiker met schrijftoegang).

Als u geen toegang hebt tot een van deze tegels, moet u uw beheerder om inzendertoegang tot de web-app vragen.

## <a name="web-app-resources-that-require-write-access"></a>Web-app-bronnen waarvoor schrijftoegang vereist is

Web-apps worden gecompliceerd door de aanwezigheid van een paar verschillende bronnen die samenspelen. Hier is een typische resource groep met een paar websites:

![Web-app-brongroep](./media/troubleshooting/website-resource-model.png)

Als u iemand toegang verleent tot alleen de web-app, is een groot deel van de functionaliteit op het websiteblad in de Azure-portal uitgeschakeld.

Deze items vereisen **schrijftoegang** tot het **App Service-abonnement** dat overeenkomt met uw website:  

* De prijscategorie van de web-app weergeven (gratis of standaard)  
* Schaalconfiguratie (aantal exemplaren, grootte van de virtuele machine, instellingen voor automatisch schalen)  
* Quota (opslag, bandbreedte, CPU)  

Deze items vereisen **schrijftoegang** tot de hele **resourcegroep** die uw website bevat:  

* TLS/SSL-certificaten en bindingen (TLS/SSL-certificaten kunnen worden gedeeld tussen sites in dezelfde brongroep en geolocatie)  
* Waarschuwingsregels  
* Instellingen voor automatisch schalen  
* Componenten voor toepassingsinzichten  
* Webtests  

## <a name="virtual-machine-features-that-require-write-access"></a>Functies voor virtuele machines waarvoor schrijftoegang vereist is

Net als bij web-apps vereisen sommige functies op het virtuele machineblad schrijftoegang tot de virtuele machine of tot andere bronnen in de resourcegroep.

Virtuele machines zijn gerelateerd aan domeinnamen, virtuele netwerken, opslagaccounts en waarschuwingsregels.

Deze items vereisen **schrijftoegang** tot de **virtuele machine:**

* Eindpunten  
* IP-adressen  
* Disks  
* Extensies  

Hiervoor moet **u schrijftoegang** hebben tot zowel de **virtuele machine**als de **resourcegroep** (samen met de domeinnaam) waarin deze zich bevindt:  

* Beschikbaarheidsset  
* Laadbalansset  
* Waarschuwingsregels  

Als u geen toegang hebt tot een van deze tegels, vraagt u uw beheerder om inzendertoegang tot de resourcegroep.

## <a name="azure-functions-and-write-access"></a>Azure-functies en schrijftoegang

Voor sommige functies van [Azure-functies](../azure-functions/functions-overview.md) is schrijftoegang vereist. Als een gebruiker bijvoorbeeld [de](built-in-roles.md#reader) leesrol krijgt toegewezen, kan hij de functies in een functie-app niet bekijken. Het portaal wordt weergegeven **(geen toegang)**.

![Functie-apps geen toegang](./media/troubleshooting/functionapps-noaccess.png)

Een lezer kan op het tabblad **Platformfuncties** klikken en vervolgens op **Alle instellingen** klikken om een aantal instellingen met betrekking tot een functie-app weer te geven (vergelijkbaar met een web-app), maar ze kunnen geen van deze instellingen wijzigen. Om toegang te krijgen tot deze functies heb je de rol [inzender](built-in-roles.md#contributor) nodig.

## <a name="next-steps"></a>Volgende stappen

- [Problemen oplossen voor gastgebruikers](role-assignments-external-users.md#troubleshoot)
- [Toegang tot Azure-resources beheren met op rollen gebaseerd toegangsbeheer en de Azure-portal](role-assignments-portal.md)
- [Activiteitslogboeken weergeven voor RBAC-wijzigingen in Azure-resources](change-history-report.md)
