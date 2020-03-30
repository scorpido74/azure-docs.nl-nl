---
title: Privé-DNS-zones en -records beveiligen - Azure DNS
description: Ga in dit leerpad aan de slag met het beveiligen van privé-DNS-zones en recordsets in Microsoft Azure DNS.
services: dns
author: asudbring
ms.service: dns
ms.topic: article
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: da94c9aa97483ab5792e917d6a8f60f846b0722e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77473062"
---
# <a name="how-to-protect-private-dns-zones-and-records"></a>Hoe privé DNS-zones en -records te beschermen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Private DNS-zones en -records zijn kritieke bronnen. Als u een DNS-zone of één DNS-record verwijderd, kan dit leiden tot een servicestoring. Het is belangrijk dat DNS-zones en -records worden beschermd tegen ongeautoriseerde of toevallige wijzigingen.

In dit artikel wordt uitgelegd hoe u met Azure DNS uw privé-DNS-zones en -records beschermen tegen dergelijke wijzigingen.  We passen twee krachtige effectenfuncties toe die worden geleverd door Azure Resource Manager: [op rollen gebaseerde toegangscontrole](../role-based-access-control/overview.md) en [bronvergrendelingen](../azure-resource-manager/management/lock-resources.md).

## <a name="role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer

Met RBAC (Azure Role-Based Access Control) u fijnkorrelig toegangsbeheer voor Azure-gebruikers, -groepen en -bronnen gebruiken. Met RBAC u het toegangsniveau toekennen dat gebruikers nodig hebben. Zie [Wat is role-based access control](../role-based-access-control/overview.md)voor meer informatie over hoe RBAC u helpt bij het beheren van toegang.

### <a name="the-private-dns-zone-contributor-role"></a>De rol Private DNS-zone-bijdrager

De rol Private DNS Zone Contributor is een ingebouwde rol voor het beheren van privé DNS-bronnen. Met deze rol die wordt toegepast op een gebruiker of groep, kunnen ze privé-DNS-bronnen beheren.

De resourcegroep *myPrivateDNS* bevat vijf zones voor Contoso Corporation. Door de DNS-beheerder Private DNS Zone Contributor-machtigingen toe te kennen aan die brongroep, u volledige controle over deze DNS-zones uitvoeren. Het voorkomt het verlenen van onnodige machtigingen. De DNS-beheerder kan geen virtuele machines maken of stoppen.

De eenvoudigste manier om RBAC-machtigingen toe te wijzen is [via de Azure-portal.](../role-based-access-control/role-assignments-portal.md)  

Open **Access control (IAM)** voor de resourcegroep selecteert **u Toevoegen**en selecteer vervolgens de rol **Private DNS-zoneinzender.** Selecteer de vereiste gebruikers of groepen om machtigingen te verlenen.

![RBAC op resourcegroepniveau via de Azure-portal](./media/dns-protect-private-zones-recordsets/rbac1.png)

Machtigingen kunnen ook worden [verleend met Azure PowerShell:](../role-based-access-control/role-assignments-powershell.md)

```azurepowershell-interactive
# Grant 'Private DNS Zone Contributor' permissions to all zones in a resource group

$rsg = "<resource group name>"
$usr = "<user email address>"
$rol = "Private DNS Zone Contributor"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg
```

De gelijkwaardige opdracht is ook [beschikbaar via de Azure CLI:](../role-based-access-control/role-assignments-cli.md)

```azurecli-interactive
# Grant 'Private DNS Zone Contributor' permissions to all zones in a resource group

az role assignment create \
--assignee "<user email address>" \
--role "Private DNS Zone Contributor" \
--resource-group "<resource group name>"
```

### <a name="private-zone-level-rbac"></a>RBAC op privézoneniveau

Azure RBAC-regels kunnen worden toegepast op een abonnement, een resourcegroep of op een afzonderlijke resource. Die bron kan een afzonderlijke DNS-zone zijn of een afzonderlijke recordset.

De resourcegroep *myPrivateDNS* bevat bijvoorbeeld de zone *private.contoso.com* en een subzone *customers.private.contoso.com*. Voor elk klantaccount worden CNAME-records gemaakt. Aan het beheerdersaccount dat wordt gebruikt voor het beheren van CNAME-records, worden machtigingen toegewezen om records te maken in de *customers.private.contoso.com* zone. Het account kan alleen *customers.private.contoso.com* beheren.

RBAC-machtigingen op zoneniveau kunnen worden verleend via de Azure-portal.  Open **Access control (IAM)** voor de zone, selecteer **Toevoegen**en selecteer vervolgens de rol Private DNS **Zone Contributor.** Selecteer de vereiste gebruikers of groepen om machtigingen te verlenen.

![DNS-zoneniveau RBAC via de Azure-portal](./media/dns-protect-private-zones-recordsets/rbac2.png)

Machtigingen kunnen ook worden [verleend met Azure PowerShell:](../role-based-access-control/role-assignments-powershell.md)

```azurepowershell-interactive
# Grant 'Private DNS Zone Contributor' permissions to a specific zone

$rsg = "<resource group name>"
$usr = "<user email address>"
$zon = "<zone name>"
$rol = "Private DNS Zone Contributor"
$rsc = "Microsoft.Network/privateDnsZones"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg -ResourceName $zon -ResourceType $rsc
```

De gelijkwaardige opdracht is ook [beschikbaar via de Azure CLI:](../role-based-access-control/role-assignments-cli.md)

```azurecli-interactive
# Grant 'Private DNS Zone Contributor' permissions to a specific zone

az role assignment create \
--assignee <user email address> \
--role "Private DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/privateDnsZones/<zone name>/"
```

### <a name="record-set-level-rbac"></a>Record set level RBAC

Machtigingen worden toegepast op recordniveau.  De gebruiker krijgt de controle over items die hij nodig heeft en kan geen andere wijzigingen aanbrengen.

RBAC-machtigingen op recordniveau kunnen worden geconfigureerd via de Azure-portal, met behulp van de knop **Toegangsbeheer (IAM)** op de recordsetpagina:

![Record set level RBAC via de Azure portal](./media/dns-protect-private-zones-recordsets/rbac3.png)

![Record set level RBAC via de Azure portal](./media/dns-protect-private-zones-recordsets/rbac4.png)

RBAC-machtigingen op recordniveau kunnen ook worden [verleend met Azure PowerShell:](../role-based-access-control/role-assignments-powershell.md)

```azurepowershell-interactive
# Grant permissions to a specific record set

$usr = "<user email address>"
$rol = "Private DNS Zone Contributor"
$sco = 
"/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/privateDnsZones/<zone name>/<record type>/<record name>"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -Scope $sco
```

De gelijkwaardige opdracht is ook [beschikbaar via de Azure CLI:](../role-based-access-control/role-assignments-cli.md)

```azurecli-interactive
# Grant permissions to a specific record set

az role assignment create \
--assignee "<user email address>" \
--role "Private DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/privateDnsZones/<zone name>/<record type>/<record name>"
```

### <a name="custom-roles"></a>Aangepaste rollen

De ingebouwde private DNS-zonebijdrager maakt volledige controle over een DNS-bron mogelijk. Het is mogelijk om uw eigen aangepaste Azure-rollen te bouwen om fijnmazige controle te bieden.

Het account dat wordt gebruikt voor het beheren van CNAMEs, krijgt alleen toestemming om CNAME-records te beheren. Het account kan geen records van andere typen wijzigen. Het account kan geen bewerkingen op zoneniveau uitvoeren, zoals zoneverwijderen.

In het volgende voorbeeld wordt alleen een aangepaste roldefinitie weergegeven voor het beheren van CNAME-records:

```json
{
    "Name": "Private DNS CNAME Contributor",
    "Id": "",
    "IsCustom": true,
    "Description": "Can manage DNS CNAME records only.",
    "Actions": [
        "Microsoft.Network/privateDnsZones/CNAME/*",
        "Microsoft.Network/privateDNSZones/read",
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
        "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
}
```

De eigenschap Acties definieert de volgende DNS-specifieke machtigingen:

* `Microsoft.Network/privateDnsZones/CNAME/*`verleent volledige controle over CNAME-records
* `Microsoft.Network/privateDNSZones/read`geeft toestemming om DNS-privézones te lezen, maar niet om ze te wijzigen, zodat u de zone zien waarin de CNAME wordt gemaakt.

> [!NOTE]
> Het gebruik van een aangepaste RBAC-rol om te voorkomen dat recordsets worden verwijderd terwijl ze toch kunnen worden bijgewerkt, is geen effectieve controle. Het voorkomt dat recordsets worden verwijderd, maar het voorkomt niet dat ze worden gewijzigd.  Toegestane wijzigingen omvatten het toevoegen en verwijderen van records uit de recordset, inclusief het verwijderen van alle records om een lege recordset achter te laten. Dit heeft hetzelfde effect als het verwijderen van de recordset vanuit het oogpunt van DNS-resolutie.

Aangepaste roldefinities kunnen momenteel niet worden gedefinieerd via de Azure-portal. Er kan een aangepaste rol worden gemaakt op basis van deze roldefinitie met Azure PowerShell:

```azurepowershell-interactive
# Create new role definition based on input file

New-AzRoleDefinition -InputFile <file path>
```

Het kan ook worden gemaakt via de Azure CLI:

```azurecli-interactive
# Create new role definition based on input file

az role create -inputfile <file path>
```

De rol kan vervolgens op dezelfde manier worden toegewezen als ingebouwde rollen, zoals eerder in dit artikel wordt beschreven.

Zie [Aangepaste rollen in Azure RBAC](../role-based-access-control/custom-roles.md)voor meer informatie over het maken, beheren en toewijzen van aangepaste rollen.

## <a name="resource-locks"></a>Resourcevergrendelingen

Azure Resource Manager ondersteunt een ander type beveiligingsbeheer, de mogelijkheid om resources te vergrendelen. Resourcevergrendelingen worden toegepast op de resource en zijn effectief voor alle gebruikers en rollen. Zie voor meer informatie [Resources vergrendelen met Azure Resource Manager](../azure-resource-manager/management/lock-resources.md).

Er zijn twee typen resourcevergrendeling: **CanNotDelete** en **ReadOnly**. Deze vergrendelingstypen kunnen worden toegepast op een privé-DNS-zone of op een afzonderlijke recordset.  In de volgende secties worden verschillende veelvoorkomende scenario's beschreven en hoe u deze ondersteunen met behulp van resourcevergrendelingen.

### <a name="protecting-against-all-changes"></a>Beschermen tegen alle veranderingen

Als u wilt voorkomen dat er wijzigingen worden aangebracht, past u een ReadOnly-vergrendeling toe op de zone. Met deze vergrendeling wordt voorkomen dat nieuwe recordsets worden gemaakt en dat bestaande recordsets worden gewijzigd of verwijderd.

Bronnenop zoneniveau kunnen worden gemaakt via de Azure-portal.  Selecteer op de pagina DNS-zone de optie **Sloten**en selecteer **+Toevoegen:**

![Resourcevergrendelingen op zoneniveau via de Azure-portal](./media/dns-protect-private-zones-recordsets/locks1.png)

Resourcevergrendelingen op zoneniveau kunnen ook worden gemaakt via [Azure PowerShell:](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcelock?view=latest)

```azurepowershell-interactive
# Lock a DNS zone

$lvl = "<lock level>"
$lnm = "<lock name>"
$rsc = "<zone name>"
$rty = "Microsoft.Network/privateDnsZones"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

De gelijkwaardige opdracht is ook [beschikbaar via de Azure CLI:](https://docs.microsoft.com/cli/azure/lock?view=azure-cli-latest#az-lock-create)

```azurecli-interactive
# Lock a DNS zone

az lock create \
--lock-type "<lock level>" \
--name "<lock name>" \
--resource-name "<zone name>" \
--namespace "Microsoft.Network" \
--resource-type "privateDnsZones" \
--resource-group "<resource group name>"
```
### <a name="protecting-individual-records"></a>Individuele records beveiligen

Als u wilt voorkomen dat een bestaande DNS-record is ingesteld tegen wijziging, past u een ReadOnly-vergrendeling toe op de recordset.

> [!NOTE]
> Het toepassen van een CanNotDelete-vergrendeling op een recordset is geen effectief besturingselement. Het voorkomt dat de recordset wordt verwijderd, maar het voorkomt niet dat deze wordt gewijzigd.  Toegestane wijzigingen omvatten het toevoegen en verwijderen van records uit de recordset, inclusief het verwijderen van alle records om een lege recordset achter te laten. Dit heeft hetzelfde effect als het verwijderen van de recordset vanuit het oogpunt van DNS-resolutie.

Bronvergrendelingen op recordniveau kunnen momenteel alleen worden geconfigureerd met Azure PowerShell.  Ze worden niet ondersteund in de Azure-portal of Azure CLI.

Azure PowerShell

```azurepowershell-interactive
# Lock a DNS record set

$lvl = "<lock level>"
$lnm = "<lock name>"
$rnm = "<zone name>/<record set name>"
$rty = "Microsoft.Network/privateDnsZones"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rnm -ResourceType $rty -ResourceGroupName $rsg
```
### <a name="protecting-against-zone-deletion"></a>Beschermen tegen zoneverwijdering

Wanneer een zone wordt verwijderd in Azure DNS, worden alle recordsets in de zone verwijderd.  Deze bewerking kan niet ongedaan worden gemaakt. Het per ongeluk verwijderen van een kritieke zone heeft het potentieel om een aanzienlijke zakelijke impact te hebben.  Het is belangrijk om te beschermen tegen onbedoelde zone verwijdering.

Als u een CanNotDelete-vergrendeling toepast op een zone, wordt voorkomen dat de zone wordt verwijderd. Vergrendelingen worden overgenomen door onderliggende bronnen. Een vergrendeling voorkomt dat recordsets in de zone worden verwijderd. Zoals beschreven in de bovenstaande notitie, is het niet effectief omdat records nog steeds kunnen worden verwijderd uit de bestaande recordsets.

Als alternatief u een CanNotDelete-vergrendeling toepassen op een record dat in de zone is ingesteld, zoals de SOA-recordset. De zone wordt niet verwijderd zonder ook de recordsets te verwijderen. Dit slot beschermt tegen het verwijderen van de zone, terwijl nog steeds het mogelijk is recordsets binnen de zone vrij te wijzigen. Als er een poging wordt gedaan om de zone te verwijderen, detecteert Azure Resource Manager deze verwijdering. De verwijdering zou ook de SOA-recordset verwijderen, Azure Resource Manager blokkeert de aanroep omdat de SOA is vergrendeld.  Er worden geen recordsets verwijderd.

Met de volgende PowerShell-opdracht wordt een CanNotDelete-vergrendeling gemaakt ten opzichte van de SOA-record van de opgegeven zone:

```azurepowershell-interactive
# Protect against zone delete with CanNotDelete lock on the record set

$lvl = "CanNotDelete"
$lnm = "<lock name>"
$rnm = "<zone name>/@"
$rty = "Microsoft.Network/privateDnsZones/SOA"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rnm -ResourceType $rty -ResourceGroupName $rsg
```
Een andere optie om onbedoelde zoneverwijdering te voorkomen, is door een aangepaste rol te gebruiken. Deze rol zorgt ervoor dat de accounts die worden gebruikt om uw zones te beheren, geen machtigingen voor het verwijderen van de zone hebben. 

Wanneer u een zone moet verwijderen, u een verwijdering in twee stappen afdwingen:

 - Ten eerste, subsidiezone verwijderen machtigingen
 - Ten tweede, verlenen machtigingen om de zone te verwijderen.

De aangepaste rol werkt voor alle zones die door deze accounts worden geopend. Accounts met machtigingen voor het verwijderen van de zone, zoals de eigenaar van het abonnement, kunnen nog steeds per ongeluk een zone verwijderen.

Het is mogelijk om beide benaderingen - resourcesloten en aangepaste rollen - tegelijkertijd te gebruiken als een diepgaande benadering van DNS-zonebeveiliging.

## <a name="next-steps"></a>Volgende stappen

* Zie Aan de slag met [toegangsbeheer in de Azure-portal](../role-based-access-control/overview.md)voor meer informatie over het werken met RBAC.
* Zie Resources vergrendelen met Azure [Resource Manager](../azure-resource-manager/management/lock-resources.md)voor meer informatie over het werken met resourcevergrendelingen.
