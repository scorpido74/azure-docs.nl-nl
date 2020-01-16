---
title: DNS-zones en records beveiligen-Azure DNS
description: In dit leer traject kunt u aan de slag met het beveiligen van DNS-zones en-record sets in Microsoft Azure DNS.
services: dns
author: asudbring
ms.service: dns
ms.topic: article
ms.date: 12/4/2018
ms.author: allensu
ms.openlocfilehash: c87f9d51c69c4f4d330862e83e5cc8e8e849a988
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75969022"
---
# <a name="how-to-protect-dns-zones-and-records"></a>DNS-zones en-records beveiligen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

DNS-zones en-records zijn essentiële bronnen. Het verwijderen van een DNS-zone of zelfs één DNS-record kan leiden tot een totale service storing.  Daarom is het belang rijk dat kritieke DNS-zones en-records worden beschermd tegen onbevoegde of onopzettelijke wijzigingen.

In dit artikel wordt uitgelegd hoe u met Azure DNS uw DNS-zones en-records kunt beveiligen tegen dergelijke wijzigingen.  We hebben twee krachtige beveiligings functies van Azure Resource Manager toegepast: [toegangs beheer op basis van rollen](../role-based-access-control/overview.md) en [resource vergrendelingen](../azure-resource-manager/management/lock-resources.md).

## <a name="role-based-access-control"></a>Toegangsbeheer op basis van rollen

Met Access Control op basis van rollen (RBAC) van Azure kunt u verfijnd toegangs beheer voor Azure-gebruikers,-groepen en-resources. Met RBAC kunt u nauw keurig de hoeveelheid toegang verlenen die gebruikers nodig hebben om hun taken uit te voeren. Zie [Wat is op rollen gebaseerd Access Control](../role-based-access-control/overview.md)voor meer informatie over hoe RBAC u kunt gebruiken om de toegang te beheren.

### <a name="the-dns-zone-contributor-role"></a>De rol van de DNS-zone bijdrager

De rol van de DNS-zone Inzender is een ingebouwde rol die door Azure wordt verschaft voor het beheren van DNS-resources.  Als u machtigingen voor de DNS-zone Inzender toewijst aan een gebruiker of groep, kan die groep DNS-bronnen beheren, maar niet de resources van een ander type.

Stel bijvoorbeeld dat de resource groep *myzones* vijf zones voor Contoso Corporation bevat. Door de DNS-beheerder DNS-zone Inzender machtigingen te verlenen voor die resource groep, kunt u volledige controle over deze DNS-zones. Er wordt ook voor komen dat overbodige machtigingen worden verleend, bijvoorbeeld de DNS-beheerder kan Virtual Machines niet maken of stoppen.

De eenvoudigste manier om RBAC-machtigingen toe te wijzen, is [via de Azure Portal](../role-based-access-control/role-assignments-portal.md).  Open **toegangs beheer (IAM)** voor de resource groep, selecteer **toevoegen**en selecteer vervolgens de rol **Inzender voor DNS-zone** en selecteer de vereiste gebruikers of groepen om machtigingen te verlenen.

![RBAC op resource groeps niveau via de Azure Portal](./media/dns-protect-zones-recordsets/rbac1.png)

Machtigingen kunnen ook worden [verleend met behulp van Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group
New-AzRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -ResourceGroupName "<resource group name>"
```

De overeenkomstige opdracht is ook [beschikbaar via de Azure cli](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group
azure role assignment create --signInName "<user email address>" --roleName "DNS Zone Contributor" --resourceGroup "<resource group name>"
```

### <a name="zone-level-rbac"></a>RBAC zone niveau

Azure RBAC-regels kunnen worden toegepast op een abonnement, een resource groep of een afzonderlijke resource. In het geval van Azure DNS kan die bron een afzonderlijke DNS-zone of zelfs een afzonderlijke recordset zijn.

Stel bijvoorbeeld dat de resource groep *myzones* de zone *contoso.com* bevat en een *Customers.contoso.com* subzone waarin CNAME-records voor elk klant account worden gemaakt.  Het account dat wordt gebruikt voor het beheren van deze CNAME-records moet machtigingen krijgen om alleen records in de zone *Customers.contoso.com* te maken, maar mag geen toegang hebben tot de andere zones.

RBAC-machtigingen op zone niveau kunnen worden verleend via de Azure Portal.  Open **toegangs beheer (IAM)** voor de zone, selecteer **toevoegen**, selecteer de rol **Inzender voor DNS-zone** en selecteer de vereiste gebruikers of groepen om machtigingen te verlenen.

![DNS-zone niveau RBAC via de Azure Portal](./media/dns-protect-zones-recordsets/rbac2.png)

Machtigingen kunnen ook worden [verleend met behulp van Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell
# Grant 'DNS Zone Contributor' permissions to a specific zone
New-AzRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -ResourceGroupName "<resource group name>" -ResourceName "<zone name>" -ResourceType Microsoft.Network/DNSZones
```

De overeenkomstige opdracht is ook [beschikbaar via de Azure cli](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to a specific zone
azure role assignment create --signInName <user email address> --roleName "DNS Zone Contributor" --resource-name <zone name> --resource-type Microsoft.Network/DNSZones --resource-group <resource group name>
```

### <a name="record-set-level-rbac"></a>Record sets niveau RBAC

We kunnen nog een stap verder gaan. Overweeg de e-mail beheerder voor Contoso Corporation, die toegang moet hebben tot de MX-en TXT-records aan de Apex van de zone contoso.com.  Ze heeft geen toegang tot andere MX-of TXT-records of andere records van andere typen.  Met Azure DNS kunt u machtigingen op het niveau van de recordset toewijzen om precies te bepalen met welke records de e-mail beheerder toegang moet hebben.  De e-mail beheerder krijgt precies het besturings element dat ze nodig heeft en kan geen andere wijzigingen aanbrengen.

RBAC-machtigingen op basis van het record niveau kunnen worden geconfigureerd via de Azure Portal, met behulp van de knop **gebruikers** op de pagina Recordset:

![Het niveau van de record sets RBAC via de Azure Portal](./media/dns-protect-zones-recordsets/rbac3.png)

Er kunnen ook RBAC-machtigingen op het niveau van record sets worden [verleend met behulp van Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell
# Grant permissions to a specific record set
New-AzRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -Scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

De overeenkomstige opdracht is ook [beschikbaar via de Azure cli](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant permissions to a specific record set
azure role assignment create --signInName "<user email address>" --roleName "DNS Zone Contributor" --scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

### <a name="custom-roles"></a>Aangepaste rollen

Met de ingebouwde rol Inzender voor DNS-zones kunt u volledige controle over een DNS-bron. Het is ook mogelijk om uw eigen Azure-rollen voor klanten te bouwen, om nog nauw keurigere controle te leveren.

Bekijk het voor beeld waarin een CNAME-record in de zone *Customers.contoso.com* wordt gemaakt voor elk Contoso Corporation-klant account.  Het account dat wordt gebruikt om deze CNAMEs te beheren, moet machtigingen krijgen om alleen CNAME-records te beheren.  Er kunnen vervolgens geen records worden gewijzigd van andere typen (zoals het wijzigen van MX-records) of het uitvoeren van bewerkingen op zone niveau, zoals zone verwijderen.

In het volgende voor beeld ziet u een aangepaste roldefinitie voor het beheer van CNAME-records:

```json
{
    "Name": "DNS CNAME Contributor",
    "Id": "",
    "IsCustom": true,
    "Description": "Can manage DNS CNAME records only.",
    "Actions": [
        "Microsoft.Network/dnsZones/CNAME/*",
        "Microsoft.Network/dnsZones/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
    ],
    "NotActions": [
    ],
    "AssignableScopes": [
        "/subscriptions/ c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
}
```

De eigenschap actions definieert de volgende DNS-specifieke machtigingen:

* `Microsoft.Network/dnsZones/CNAME/*` volledige controle over CNAME-records
* `Microsoft.Network/dnsZones/read` verleent toestemming om DNS-zones te lezen, maar niet om ze te wijzigen, zodat u de zone kunt zien waarin de CNAME wordt gemaakt.

De overige acties worden gekopieerd uit de [ingebouwde rol Inzender voor DNS-zone](../role-based-access-control/built-in-roles.md#dns-zone-contributor).

> [!NOTE]
> Het gebruik van een aangepaste RBAC-rol om te voor komen dat record sets worden verwijderd terwijl ze nog steeds kunnen worden bijgewerkt, is geen effectief besturings element. Zo voor komt u dat record sets worden verwijderd, maar niet om te voor komen dat ze worden gewijzigd.  Toegestane wijzigingen zijn het toevoegen en verwijderen van records uit de recordset, inclusief het verwijderen van alle records om een lege recordset te verlaten. Dit heeft hetzelfde effect als het verwijderen van de recordset uit het oogpunt van een DNS-oplossing.

Aangepaste roldefinities kunnen momenteel niet worden gedefinieerd via de Azure Portal. Een aangepaste rol op basis van deze roldefinitie kan worden gemaakt met behulp van Azure PowerShell:

```azurepowershell
# Create new role definition based on input file
New-AzRoleDefinition -InputFile <file path>
```

Het kan ook worden gemaakt via de Azure CLI:

```azurecli
# Create new role definition based on input file
azure role create -inputfile <file path>
```

De rol kan vervolgens op dezelfde manier worden toegewezen als ingebouwde rollen, zoals eerder in dit artikel is beschreven.

Zie [aangepaste rollen in azure RBAC](../role-based-access-control/custom-roles.md)voor meer informatie over het maken, beheren en toewijzen van aangepaste rollen.

## <a name="resource-locks"></a>Resourcevergrendelingen

Naast RBAC ondersteunt Azure Resource Manager ook een ander type beveiligings beheer, namelijk de mogelijkheid om resources te vergren delen. Met de RBAC-regels kunt u de acties van specifieke gebruikers en groepen beheren, worden resource vergrendelingen toegepast op de resource en zijn ze effectief voor alle gebruikers en rollen. Zie voor meer informatie [Resources vergrendelen met Azure Resource Manager](../azure-resource-manager/management/lock-resources.md).

Er zijn twee typen bron vergrendeling: **CanNotDelete** en **alleen-lezen**. Deze kunnen worden toegepast op een DNS-zone of op een afzonderlijke Recordset.  In de volgende secties worden verschillende algemene scenario's beschreven en wordt uitgelegd hoe u deze kunt ondersteunen met resource vergrendelingen.

### <a name="protecting-against-all-changes"></a>Beveiligen tegen alle wijzigingen

Als u wilt voor komen dat er wijzigingen worden aangebracht, past u een alleen-lezen vergrendeling toe op de zone.  Zo voor komt u dat nieuwe record sets worden gemaakt en dat bestaande record sets worden gewijzigd of verwijderd.

Resource vergrendelingen op zone niveau kunnen worden gemaakt via de Azure Portal.  Selecteer op de pagina DNS-zone de optie **vergren**delen en selecteer **+ toevoegen**:

![Resource vergrendeling op zone niveau via de Azure Portal](./media/dns-protect-zones-recordsets/locks1.png)

Resource vergrendelingen op zone niveau kunnen ook worden gemaakt via Azure PowerShell:

```azurepowershell
# Lock a DNS zone
New-AzResourceLock -LockLevel <lock level> -LockName <lock name> -ResourceName <zone name> -ResourceType Microsoft.Network/DNSZones -ResourceGroupName <resource group name>
```

Het configureren van Azure resource Locks wordt momenteel niet ondersteund via de Azure CLI.

### <a name="protecting-individual-records"></a>Afzonderlijke records beveiligen

Als u wilt voor komen dat een bestaande DNS-record wordt ingesteld tegen wijziging, moet u een alleen-lezen vergrendeling Toep assen op de recordset.

> [!NOTE]
> Het Toep assen van een CanNotDelete-vergren deling op een recordset is geen effectief besturings element. Hiermee wordt voor komen dat de recordset wordt verwijderd, maar wordt niet voor komen dat deze wordt gewijzigd.  Toegestane wijzigingen zijn het toevoegen en verwijderen van records uit de recordset, inclusief het verwijderen van alle records om een lege recordset te verlaten. Dit heeft hetzelfde effect als het verwijderen van de recordset uit het oogpunt van een DNS-oplossing.

Resource vergrendelingen op record sets kunnen momenteel alleen worden geconfigureerd met behulp van Azure PowerShell.  Ze worden niet ondersteund in de Azure Portal of Azure CLI.

```azurepowershell
# Lock a DNS record set
New-AzResourceLock -LockLevel <lock level> -LockName "<lock name>" -ResourceName "<zone name>/<record set name>" -ResourceType "Microsoft.Network/DNSZones/<record type>" -ResourceGroupName "<resource group name>"
```

### <a name="protecting-against-zone-deletion"></a>Beveiligen tegen zone verwijdering

Wanneer een zone in Azure DNS wordt verwijderd, worden ook alle record sets in de zone verwijderd.  Deze bewerking kan niet ongedaan worden gemaakt.  Het per ongeluk verwijderen van een kritieke zone heeft mogelijk een aanzienlijke invloed op het bedrijf.  Het is daarom belang rijk om te beschermen tegen het verwijderen van onbedoelde zones.

Wanneer u een CanNotDelete-vergren deling toepast op een zone, voor komt u dat de zone wordt verwijderd.  Omdat de vergren delingen echter worden overgenomen door onderliggende resources, worden er ook geen record sets in de zone verwijderd. Dit is mogelijk niet gewenst.  Bovendien is het niet effectief, zoals beschreven in de bovenstaande opmerking, omdat er nog steeds records uit de bestaande record sets kunnen worden verwijderd.

U kunt ook een CanNotDelete-vergren deling Toep assen op een recordset in de zone, zoals de SOA-Recordset.  Omdat de zone niet kan worden verwijderd zonder ook de record sets te verwijderen, wordt hiermee beschermd tegen zone verwijdering, terwijl wel toestaat dat record sets in de zone vrij kunnen worden gewijzigd. Als er wordt geprobeerd om de zone te verwijderen, wordt door Azure Resource Manager gedetecteerd dat de SOA-recordset ook wordt verwijderd en wordt de oproep geblokkeerd, omdat de SOA is vergrendeld.  Geen record sets worden verwijderd.

Met de volgende Power shell-opdracht wordt een CanNotDelete-vergren deling voor de SOA-record van de opgegeven zone gemaakt:

```azurepowershell
# Protect against zone delete with CanNotDelete lock on the record set
New-AzResourceLock -LockLevel CanNotDelete -LockName "<lock name>" -ResourceName "<zone name>/@" -ResourceType" Microsoft.Network/DNSZones/SOA" -ResourceGroupName "<resource group name>"
```

Een andere manier om te voor komen dat onbedoeld zone wordt verwijderd, is door een aangepaste rol te gebruiken om ervoor te zorgen dat de operator en service accounts die worden gebruikt voor het beheren van uw zones geen zone-verwijderings machtigingen hebben. Wanneer u een zone wilt verwijderen, kunt u een verwijderen uit twee stappen afdwingen, eerst machtigingen voor het verwijderen van een zone verlenen (in het bereik van de zone, om te voor komen dat de verkeerde zone wordt verwijderd) en de tweede om de zone te verwijderen.

Deze tweede benadering heeft als voor deel dat het werkt voor alle zones die door deze accounts worden gebruikt, zonder dat u een vergren deling hoeft te onthouden. Het nadeel is dat alle accounts met machtigingen voor zone verwijdering, zoals de eigenaar van het abonnement, nog steeds per ongeluk een kritieke zone kunnen verwijderen.

Het is mogelijk om beide benaderingen-resource vergrendelingen en aangepaste rollen te gebruiken, als een ingrijpende benadering voor DNS-zone beveiliging.

## <a name="next-steps"></a>Volgende stappen

* Zie [aan de slag met toegangs beheer in de Azure Portal](../role-based-access-control/overview.md)voor meer informatie over het werken met RBAC.
* Zie [resources vergren delen met Azure Resource Manager](../azure-resource-manager/management/lock-resources.md)voor meer informatie over het werken met resource vergrendelingen.
