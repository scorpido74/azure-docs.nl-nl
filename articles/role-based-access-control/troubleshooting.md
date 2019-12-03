---
title: Problemen met RBAC voor Azure-resources oplossen | Microsoft Docs
description: Problemen oplossen met op rollen gebaseerd toegangs beheer (RBAC) voor Azure-resources.
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
ms.date: 11/22/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: 5429ebb611f852f7672d89de190ddd68dbcb01cf
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707783"
---
# <a name="troubleshoot-rbac-for-azure-resources"></a>Problemen met RBAC voor Azure-resources oplossen

In dit artikel vindt u antwoorden op algemene vragen over op rollen gebaseerd toegangs beheer (RBAC) voor Azure-resources, zodat u weet wat u kunt verwachten bij het gebruik van de functies in de Azure Portal en kunt u toegangs problemen oplossen.

## <a name="problems-with-rbac-role-assignments"></a>Problemen met RBAC-roltoewijzingen

- Als u geen roltoewijzing kunt toevoegen in de Azure Portal op **toegangs beheer (IAM),** **omdat de optie** toewijzing van rol toevoegen > **toevoegen** is uitgeschakeld of omdat u de machtigings fout ' de client met object-id heeft geen autorisatie voor het uitvoeren van een actie ' krijgt ', Controleer of u momenteel bent aangemeld met een gebruiker aan wie een rol is toegewezen die de machtiging `Microsoft.Authorization/roleAssignments/write` heeft, zoals [eigenaar](built-in-roles.md#owner) of [gebruikers toegangs beheerder](built-in-roles.md#user-access-administrator) in het bereik dat u probeert toe te wijzen aan de rol.
- Als u het fout bericht ' er kunnen geen roltoewijzingen meer worden gemaakt (code: RoleAssignmentLimitExceeded) ' wordt weer gegeven wanneer u probeert een rol toe te wijzen, kunt u het aantal roltoewijzingen verminderen door rollen toe te wijzen aan groepen. Azure ondersteunt maximaal **2000** roltoewijzingen per abonnement. De limiet voor het aantal roltoewijzingen is vast en kan niet worden verhoogd.

## <a name="problems-with-custom-roles"></a>Problemen met aangepaste rollen

- Als u stappen nodig hebt voor het maken van een aangepaste rol, raadpleegt u de zelf studies voor aangepaste rollen met behulp van [Azure PowerShell](tutorial-custom-role-powershell.md) of [Azure cli](tutorial-custom-role-cli.md).
- Als u een bestaande aangepaste rol niet kunt bijwerken, controleert u of u momenteel bent aangemeld met een gebruiker aan wie een rol is toegewezen die de machtiging `Microsoft.Authorization/roleDefinition/write` heeft, zoals [eigenaar](built-in-roles.md#owner) of [beheerder van gebruikers toegang](built-in-roles.md#user-access-administrator).
- Als u een aangepaste rol niet kunt verwijderen en het volgende fout bericht wordt weer gegeven: er zijn bestaande roltoewijzingen die verwijzen naar de rol (code: RoleDefinitionHasAssignments) ', dan zijn er roltoewijzingen die nog steeds gebruikmaken van de aangepaste rol. Verwijder deze roltoewijzingen en probeer de aangepaste rol opnieuw te verwijderen.
- Als tijdens het maken van een nieuwe aangepaste rol het foutbericht 'Role definition limit exceeded. No more role definitions can be created (RoleDefinitionLimitExceeded)' Er kunnen geen roldefinities meer worden gemaakt (code: RoleDefinitionLimitExceeded) "wanneer u probeert een nieuwe aangepaste rol te maken, verwijdert u alle aangepaste rollen die niet worden gebruikt. Azure biedt ondersteuning voor Maxi maal **5000** aangepaste rollen in een Tenant. (Voor speciale clouds, zoals Azure Government, Azure Duitsland en Azure China 21Vianet, is de limiet 2000 aangepaste rollen.)
- Als er een fout bericht wordt weer gegeven dat vergelijkbaar is met de machtiging ' de client is gemachtigd voor het uitvoeren van de actie ' micro soft. Authorization/roleDefinitions/write ' op het bereik '/Subscriptions/{subscriptionid} ', is het gekoppelde abonnement niet gevonden ' wanneer u een aangepaste rol probeert bij te werken, controleert u of een of meer [toewijs bare bereiken](role-definitions.md#assignablescopes) zijn verwijderd in de Tenant. Als het bereik is verwijderd, maakt u een ondersteuningsticket, aangezien hiervoor op dit moment geen selfserviceoplossing beschikbaar is.

## <a name="recover-rbac-when-subscriptions-are-moved-across-tenants"></a>RBAC herstellen als abonnementen tussen tenants zijn verplaatst

- Zie het [eigendom van een Azure-abonnement overdragen aan een ander account](../billing/billing-subscription-transfer.md)als u stappen nodig hebt voor het overdragen van een abonnement naar een andere Azure AD-Tenant.
- Als u een abonnement verplaatst naar een ander Azure AD-tenant, worden alle roltoewijzingen permanent verwijderd uit de Azure AD-brontenant en worden deze niet gemigreerd naar de Azure AD-doeltenant. U moet uw roltoewijzingen opnieuw maken in de doel-tenant. U moet ook hand matig beheerde identiteiten voor Azure-resources maken. Zie [Veelgestelde vragen en bekende problemen met beheerde identiteiten](../active-directory/managed-identities-azure-resources/known-issues.md)voor meer informatie.
- Als u een globale Azure AD-beheerder bent en u geen toegang hebt tot een abonnement nadat het is verplaatst tussen tenants, gebruikt u de wissel knop **voor het toegangs beheer voor Azure** om de toegang tot het abonnement tijdelijk te [verhogen](elevate-access-global-admin.md) .

## <a name="issues-with-service-admins-or-co-admins"></a>Problemen met servicebeheerders of co-beheerders

- Als u problemen ondervindt met Service beheerder of mede beheerders, raadpleegt u Azure-abonnements beheerders en [klassieke beheerders rollen voor abonnementen, Azure RBAC-rollen en Azure AD-beheerders rollen](rbac-and-directory-admin-roles.md) [toevoegen of wijzigen](../billing/billing-add-change-azure-subscription-administrator.md) .

## <a name="access-denied-or-permission-errors"></a>Toegang geweigerd of machtigings fouten

- Als u de machtiging fout ' de client met object-id heeft geen toestemming om actie te ondernemen over het bereik (code: AuthorizationFailed) ' wordt weer geven wanneer u een resource probeert te maken, controleert u of u momenteel bent aangemeld met een gebruiker aan wie een rol is toegewezen die is geschreven machtiging voor de resource in het geselecteerde bereik. Als u bijvoorbeeld virtuele machines in een resourcegroep wilt beheren, moet u de rol [Inzender voor virtuele machines](built-in-roles.md#virtual-machine-contributor) toewijzen aan de resourcegroep (of bovenliggend bereik). Voor een lijst met machtigingen voor elke ingebouwde rol, raadpleegt u [Ingebouwde rollen voor Azure-resources](built-in-roles.md).
- Als u de machtiging fout ' u bent niet gemachtigd om een ondersteunings aanvraag te maken ' krijgt wanneer u een ondersteunings ticket probeert te maken of bij te werken, controleert u of u momenteel bent aangemeld met een gebruiker aan wie een rol is toegewezen die de machtiging `Microsoft.Support/supportTickets/write` heeft, zoals [mede werker van de ondersteunings aanvraag](built-in-roles.md#support-request-contributor).

## <a name="role-assignments-with-unknown-security-principal"></a>Roltoewijzingen met onbekende beveiligingsprincipal

Als u een rol toewijst aan een beveiligingsprincipal (gebruiker, groep, Service-Principal of beheerde identiteit) en u de beveiligings-principal later verwijdert zonder de roltoewijzing te verwijderen, wordt het beveiligings-principal-type voor de roltoewijzing vermeld als **onbekend**. In de volgende scherm afbeelding ziet u een voor beeld in de Azure Portal. De naam van de beveiligingsprincipal wordt weer gegeven als **identiteit verwijderd** en de **identiteit bestaat niet meer**. 

![Resource groep voor web-app](./media/troubleshooting/unknown-security-principal.png)

Als u deze roltoewijzing met Azure PowerShell wilt weer geven, ziet u een lege `DisplayName` en een `ObjectType` ingesteld op onbekend. [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) retourneert bijvoorbeeld een roltoewijzing die er ongeveer als volgt uitziet:

```azurepowershell
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

Ook als u deze roltoewijzing met behulp van Azure CLI vermeldt, ziet u een leeg `principalName`. Bijvoorbeeld, de [lijst AZ Role Assignment](/cli/azure/role/assignment#az-role-assignment-list) retourneert een roltoewijzing die er ongeveer als volgt uitziet:

```azurecli
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

Het is geen probleem om deze roltoewijzingen te verlaten, maar u kunt ze wel verwijderen met behulp van stappen die vergelijkbaar zijn met andere roltoewijzingen. Zie [Azure Portal](role-assignments-portal.md#remove-a-role-assignment), [Azure POWERSHELL](role-assignments-powershell.md#remove-a-role-assignment)of [Azure cli](role-assignments-cli.md#remove-a-role-assignment) (Engelstalig) voor meer informatie over het verwijderen van roltoewijzingen

Als u in Power shell probeert de roltoewijzingen te verwijderen met de naam van de object-ID en roldefinitie en er meer dan één roltoewijzing overeenkomt met de para meters, wordt het volgende fout bericht weer gegeven: ' de opgegeven informatie is niet toegewezen aan een roltoewijzing '. Hieronder ziet u een voor beeld van het fout bericht:

```Example
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor"

Remove-AzRoleAssignment : The provided information does not map to a role assignment.
At line:1 char:1
+ Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : CloseError: (:) [Remove-AzRoleAssignment], KeyNotFoundException
+ FullyQualifiedErrorId : Microsoft.Azure.Commands.Resources.RemoveAzureRoleAssignmentCommand
```

Als u dit fout bericht ontvangt, moet u ook de `-Scope`-of `-ResourceGroupName`-para meters opgeven.

```Example
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor" - Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="rbac-changes-are-not-being-detected"></a>RBAC-wijzigingen worden niet gedetecteerd

Azure Resource Manager worden soms configuraties en gegevens in de cache opgeslagen om de prestaties te verbeteren. Wanneer u roltoewijzingen maakt of verwijdert, kan het tot 30 minuten duren voordat de wijzigingen van kracht worden. Als u de Azure Portal, Azure PowerShell of Azure CLI gebruikt, kunt u het vernieuwen van uw roltoewijzingen afdwingen door u af te melden en u aan te melden. Als u wijzigingen aanbrengt in de roltoewijzing met REST API-aanroepen, kunt u een vernieuwing afdwingen door uw toegangs token te vernieuwen.

## <a name="web-app-features-that-require-write-access"></a>Functies van web-apps waarvoor schrijf toegang is vereist

Als u een gebruiker alleen-lezen toegang verleent tot één web-app, worden sommige functies uitgeschakeld die u mogelijk niet verwacht. De volgende beheer functies vereisen **Schrijf** toegang tot een web-app (Inzender of eigenaar) en zijn niet beschikbaar in een alleen-lezen scenario.

* Opdrachten (zoals starten, stoppen, enz.)
* Instellingen wijzigen, zoals algemene configuratie, schaal instellingen, back-upinstellingen en controle-instellingen
* Toegang tot publicatie referenties en andere geheimen, zoals app-instellingen en verbindings reeksen
* Streaming-logboeken
* Configuratie van Diagnostische logboeken
* Console (opdracht prompt)
* Actieve en recente implementaties (voor lokale Git-continue implementatie)
* Geschatte uitgaven
* Webtests
* Virtueel netwerk (alleen zichtbaar voor een lezer als een virtueel netwerk eerder is geconfigureerd door een gebruiker met schrijf toegang).

Als u geen toegang hebt tot deze tegels, moet u de beheerder vragen om de toegang tot de web-app te verlenen.

## <a name="web-app-resources-that-require-write-access"></a>Web-app-resources waarvoor schrijf toegang is vereist

Web-apps zijn gecompliceerd door de aanwezigheid van een aantal verschillende resources die Interplay. Hier volgt een typische resource groep met een aantal websites:

![Resource groep voor web-app](./media/troubleshooting/website-resource-model.png)

Als u iemand toegang verleent tot alleen de web-app, is veel van de functionaliteit op de Blade website in de Azure Portal uitgeschakeld.

Voor deze items is **Schrijf** toegang vereist voor het **app service abonnement** dat overeenkomt met uw website:  

* De prijs categorie van de web-app weer geven (gratis of standaard)  
* Configuratie schalen (aantal exemplaren, grootte van de virtuele machine, instellingen voor automatisch schalen)  
* Quota (opslag, band breedte, CPU)  

Deze items hebben **Schrijf** toegang tot de hele **resource groep** die uw website bevat:  

* SSL-certificaten en-bindingen (SSL-certificaten kunnen worden gedeeld tussen sites in dezelfde resource groep en geografische locatie)  
* Waarschuwingsregels  
* Instellingen voor automatisch schalen  
* Application Insights-onderdelen  
* Webtests  

## <a name="virtual-machine-features-that-require-write-access"></a>Functies van virtuele machines waarvoor schrijf toegang is vereist

Net als web apps vereist sommige functies op de Blade van de virtuele machine schrijf toegang tot de virtuele machine of andere resources in de resource groep.

Virtuele machines zijn gerelateerd aan domein namen, virtuele netwerken, opslag accounts en waarschuwings regels.

Voor deze items is **Schrijf** toegang tot de **virtuele machine**vereist:

* Eindpunten  
* IP-adressen  
* Schijven  
* Extensies  

Hiervoor is **Schrijf** toegang vereist voor zowel de **virtuele machine**als de **resource groep** (samen met de domein naam) waarin deze zich bevindt:  

* Beschikbaarheidsset  
* Set met gelijke taak verdeling  
* Waarschuwingsregels  

Als u geen toegang hebt tot deze tegels, vraagt u uw beheerder om toegang tot de resource groep te krijgen.

## <a name="azure-functions-and-write-access"></a>Azure Functions en schrijf toegang

Voor sommige functies van [Azure functions](../azure-functions/functions-overview.md) is schrijf toegang vereist. Als een gebruiker bijvoorbeeld de rol [lezer](built-in-roles.md#reader) krijgt toegewezen, kunnen ze de functies in een functie-app niet weer geven. De portal wordt weer gegeven **(geen toegang)** .

![Functie-apps zonder toegang](./media/troubleshooting/functionapps-noaccess.png)

Een lezer kan op het tabblad **platform functies** klikken en vervolgens op **alle instellingen** klikken om enkele instellingen te bekijken die betrekking hebben op een functie-app (vergelijkbaar met een web-app), maar ze kunnen geen van deze instellingen wijzigen. U hebt de rol [Inzender](built-in-roles.md#contributor) nodig om toegang te krijgen tot deze functies.

## <a name="next-steps"></a>Volgende stappen

- [Problemen met gast gebruikers oplossen](role-assignments-external-users.md#troubleshoot)
- [Toegang tot Azure-resources beheren met op rollen gebaseerd toegangsbeheer en de Azure-portal](role-assignments-portal.md)
- [Activiteiten logboeken voor RBAC-wijzigingen in azure-resources weer geven](change-history-report.md)

