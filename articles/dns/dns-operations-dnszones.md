---
title: DNS-zones beheren in Azure DNS - PowerShell | Microsoft Documenten
description: U DNS-zones beheren met Azure Powershell. In dit artikel wordt beschreven hoe u DNS-zones op Azure DNS bijwerken, verwijderen en maken
services: dns
documentationcenter: na
author: rohinkoul
manager: timlt
ms.assetid: a67992ab-8166-4052-9b28-554c5a39e60c
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2018
ms.author: rohink
ms.openlocfilehash: 0120501aab7f0a63721126bfb5b3d04d9deb42fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76936804"
---
# <a name="how-to-manage-dns-zones-using-powershell"></a>DNS-zones beheren met PowerShell

> [!div class="op_single_selector"]
> * [Portal](dns-operations-dnszones-portal.md)
> * [Powershell](dns-operations-dnszones.md)
> * [Klassieke versie van Azure CLI](dns-operations-dnszones-cli-nodejs.md)
> * [Azure-CLI](dns-operations-dnszones-cli.md)

In dit artikel ziet u hoe u uw DNS-zones beheert met Azure PowerShell. U uw DNS-zones ook beheren met behulp van de azure [CLI](dns-operations-dnszones-cli.md) met meerdere platforms of de Azure-portal.

Deze handleiding gaat specifiek over openbare DNS-zones. Zie [Aan de slag met Azure DNS-privézones met Azure PowerShell](private-dns-getstarted-powershell.md)voor informatie over het gebruik van Azure PowerShell voor het beheren van privézones in Azure DNS.

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

[!INCLUDE [dns-powershell-setup](../../includes/dns-powershell-setup-include.md)]


## <a name="create-a-dns-zone"></a>Een DNS-zone maken

Een DNS-zone wordt gemaakt met de cmdlet `New-AzureRmDnsZone`.

In het volgende voorbeeld wordt een DNS-zone *contoso.com* in de resourcegroep genaamd *MyResourceGroup:*

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
```

In het volgende voorbeeld ziet u hoe u een DNS-zone maakt met twee [Azure Resource Manager-tags](dns-zones-records.md#tags), *project = demo* en *env = test:*

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @{ project="demo"; env="test" }
```

Azure DNS ondersteunt ook privé-DNS-zones.  Voor meer informatie over privé-DNS-zones raadpleegt u [Using Azure DNS for private domains](private-dns-overview.md) (Azure DNS gebruiken voor privédomeinen). Zie voor een voorbeeld van het maken van een privé-DNS-zone [Aan de slag met privézones in Azure DNS met behulp van PowerShell](./private-dns-getstarted-powershell.md).

## <a name="get-a-dns-zone"></a>Een DNS-zone oppakken

Als u een DNS-zone wilt ophalen, gebruikt u de `Get-AzureRmDnsZone` cmdlet. Met deze bewerking wordt een DNS-zoneobject geretourneerd dat overeenkomt met een bestaande zone in Azure DNS. Het object bevat gegevens over de zone (zoals het aantal recordsets), maar `Get-AzureRmDnsRecordSet`bevat niet de recordsets zelf (zie ).

```powershell
Get-AzureRmDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup

Name                  : contoso.com
ResourceGroupName     : myresourcegroup
Etag                  : 00000003-0000-0000-8ec2-f4879750d201
Tags                  : {project, env}
NameServers           : {ns1-01.azure-dns.com., ns2-01.azure-dns.net., ns3-01.azure-dns.org.,
                        ns4-01.azure-dns.info.}
NumberOfRecordSets    : 2
MaxNumberOfRecordSets : 5000
```

## <a name="list-dns-zones"></a>DNS-zones vermelden

Door de zonenaam weg te laten uit `Get-AzureRmDnsZone`, kunt u een opsomming maken van alle zones in een resourcegroep. Deze bewerking retourneert een matrix met zoneobjecten.

```powershell
$zoneList = Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup
```

Door zowel de zonenaam als de resourcegroepnaam uit `Get-AzureRmDnsZone` weg te laten, kunt u een opsomming maken van alle zones in het Azure-abonnement.

```powershell
$zoneList = Get-AzureRmDnsZone
```

## <a name="update-a-dns-zone"></a>Een DNS-zone bijwerken

Wijzigingen aan een DNS-zoneresource kunnen worden aangebracht met behulp van `Set-AzureRmDnsZone`. Met deze cmdlet worden geen DNS-recordsets in de zone bijgewerkt (zie [DNS-records beheren](dns-operations-recordsets.md)). De cmdlet wordt alleen gebruikt voor het bijwerken van eigenschappen van de zoneresource zelf. De beschrijfbare zone-eigenschappen zijn momenteel beperkt tot de ['tags' van Azure Resource Manager voor de zonebron.](dns-zones-records.md#tags)

Gebruik een van de volgende twee manieren om een DNS-zone bij te werken:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group"></a>De zone opgeven met de zonenaam en resourcegroep

Deze benadering vervangt de bestaande zonetags door de opgegeven waarden.

```powershell
Set-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @{ project="demo"; env="test" }
```

### <a name="specify-the-zone-using-a-zone-object"></a>Geef de zone op met behulp van een $zone-object

Deze benadering haalt het bestaande zoneobject op, wijzigt de tags en verbindt de wijzigingen. Op deze manier kunnen bestaande tags behouden blijven.

```powershell
# Get the zone object
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup

# Remove an existing tag
$zone.Tags.Remove("project")

# Add a new tag
$zone.Tags.Add("status","approved")

# Commit changes
Set-AzureRmDnsZone -Zone $zone
```

Bij `Set-AzureRmDnsZone` het gebruik met een $zone object worden [Etag-controles](dns-zones-records.md#etags) gebruikt om ervoor te zorgen dat gelijktijdige wijzigingen niet worden overschreven. U de `-Overwrite` optionele schakelaar gebruiken om deze controles te onderdrukken.

## <a name="delete-a-dns-zone"></a>Een DNS-zone verwijderen

DNS-zones kunnen worden `Remove-AzureRmDnsZone` verwijderd met behulp van de cmdlet.

> [!NOTE]
> Als u een DNS-zone verwijdert, worden ook alle DNS-records binnen de zone verwijderd. Deze bewerking kan niet ongedaan worden gemaakt. Als de DNS-zone in gebruik is en de zone wordt verwijderd, werken services die van de zone gebruik maken niet langer.
>
>Zie [DNS-zones en records beschermen](dns-protect-zones-recordsets.md) om beveiliging in te stellen tegen het per ongeluk verwijderen van zones.


Gebruik een van de volgende twee manieren om een DNS-zone te verwijderen:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group-name"></a>Geef de zone op met behulp van de zonenaam en de resourcegroepnaam

```powershell
Remove-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
```

### <a name="specify-the-zone-using-a-zone-object"></a>Geef de zone op met behulp van een $zone-object

U kunt de te verwijderen zone opgeven met behulp van een `$zone`-object dat wordt geretourneerd door `Get-AzureRmDnsZone`.

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsZone -Zone $zone
```

Het zone-object kan ook worden doorgesluisd in plaats van te worden doorgegeven als een parameter:

```powershell
Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsZone

```

Als `Set-AzureRmDnsZone`bij het opgeven van `$zone` de zone met behulp van een object kan Etag-controles worden uitgevoerd om ervoor te zorgen dat gelijktijdige wijzigingen niet worden verwijderd. Gebruik `-Overwrite` de schakelaar om deze controles te onderdrukken.

## <a name="confirmation-prompts"></a>Bevestigingsprompts

De cmdlets `New-AzureRmDnsZone`, `Set-AzureRmDnsZone` en `Remove-AzureRmDnsZone` ondersteunen bevestigingspromts.

Zowel in `New-AzureRmDnsZone` als `Set-AzureRmDnsZone` wordt om bevestiging gevraagd als de PowerShell-voorkeursvariabele `$ConfirmPreference` de waarde `Medium` of lager heeft. Vanwege het potentieel grote effect van het verwijderen van een DNS-zone vraagt de cmdlet `Remove-AzureRmDnsZone` om bevestiging als de PowerShell-variabele `$ConfirmPreference` een andere waarde heeft dan `None`.

Omdat de standaardwaarde voor `$ConfirmPreference``High` is, wordt standaard alleen in `Remove-AzureRmDnsZone` om bevestiging gevraagd.

U kunt de huidige instelling van `$ConfirmPreference` overschrijven met behulp van de parameter `-Confirm`. Als u `-Confirm` of `-Confirm:$True` opgeeft, vraagt de cmdlet u om bevestiging voordat deze wordt uitgevoerd. Als u `-Confirm:$False` opgeeft, wordt u niet om bevestiging gevraagd.

Zie [over voorkeursvariabelen](/powershell/module/microsoft.powershell.core/about/about_preference_variables) voor meer informatie over `-Confirm` en `$ConfirmPreference`.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [beheren van recordsets en records](dns-operations-recordsets.md) in uw DNS-zone.
<br>
Meer informatie over het [delegeren van uw domein aan Azure DNS](dns-domain-delegation.md).
<br>
Bekijk de [azure DNS PowerShell-referentiedocumentatie](/powershell/module/azurerm.dns).

