---
title: Een openbaar IP-adresvoorvoegsel van Azure maken, wijzigen of verwijderen
titlesuffix: Azure Virtual Network
description: Meer informatie over het maken, wijzigen of verwijderen van een openbaar IP-adresvoorvoegsel.
services: virtual-network
documentationcenter: na
author: anavinahar
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/13/2019
ms.author: anavin
ms.openlocfilehash: 5f0c2d9757f3652b0f83b8c36d89c855f7a92fdd
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383870"
---
# <a name="create-change-or-delete-a-public-ip-address-prefix"></a>Een voorvoegsel van een openbaar IP-adres maken, wijzigen of verwijderen

Meer informatie over een openbaar IP-adresvoorvoegsel en hoe u een ip-adres maken, wijzigen en verwijderen. Een openbaar IP-adresvoorvoegsel is een aaneengesloten reeks adressen op basis van het aantal openbare IP-adressen dat u opgeeft. De adressen worden toegewezen aan uw abonnement. Wanneer u een openbare IP-adresbron maakt, u een statisch openbaar IP-adres van het voorvoegsel toewijzen en het adres koppelen aan virtuele machines, load balancers of andere bronnen om internetverbinding mogelijk te maken. Zie Overzicht van het voorvoegsel van openbare IP-adressen als u niet bekend bent met voorvoegsels van openbare [IP-adressen](public-ip-address-prefix.md)

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Voer de volgende taken uit voordat u stappen in een gedeelte van dit artikel voltooit:

- Als u nog geen Azure-account hebt, meldt u zich aan voor een [gratis proefaccount.](https://azure.microsoft.com/free)
- Als u de https://portal.azure.comportal gebruikt, opent u en logt u in met uw Azure-account.
- Als u PowerShell-opdrachten gebruikt om taken in dit artikel uit te voeren, voert u de opdrachten uit in de [Azure Cloud Shell](https://shell.azure.com/powershell)of door PowerShell vanaf uw computer uit te voeren. Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Voor deze zelfstudie is de Azure PowerShell-module versie 1.0.0 of hoger vereist. Voer `Get-Module -ListAvailable Az` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.
- Als u CLI-opdrachten (Azure Command-line interface) gebruikt om taken in dit artikel uit te voeren, voert u de opdrachten uit in de [Azure Cloud Shell](https://shell.azure.com/bash)of door de CLI vanaf uw computer uit te voeren. Deze zelfstudie vereist de Azure CLI-versie 2.0.41 of hoger. Voer `az --version` uit om te kijken welke versie is geïnstalleerd. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](/cli/azure/install-azure-cli). Als u de Azure CLI lokaal uitvoert, `az login` moet u ook worden uitgevoerd om een verbinding met Azure te maken.

Het account waarmee u zich aanmeldt of verbinding maakt met Azure, moet worden toegewezen aan de [rol van netwerkbijdrager](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) of aan een [aangepaste rol](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) waaraan de juiste acties zijn toegewezen die worden vermeld in [Machtigingen](#permissions).

Openbare IP-adresvoorvoegsels worden in rekening gebracht. Zie prijzen [voor](https://azure.microsoft.com/pricing/details/ip-addresses)meer informatie.

## <a name="create-a-public-ip-address-prefix"></a>Een openbaar IP-adresvoorvoegsel maken

1. Selecteer boven, linkerhoek van de portal de optie **+ Een resource maken.**
2. Voer *een openbaar ip-voorvoegsel in* het vak Marketplace *doorzoeken* in. Wanneer **het voorvoegsel van openbaar IP-adres** wordt weergegeven in de zoekresultaten, selecteert u het.
3. Selecteer **Onder Openbaar IP-adresvoorvoeging**de optie **Maken**.
4. Voer waarden in of selecteer waarden voor de volgende instellingen onder **Openbaar IP-adresvoorvoegsel maken**en selecteer **Vervolgens Maken:**

   |Instelling|Vereist?|Details|
   |---|---|---|
   |Abonnement|Ja|Moet bestaan in hetzelfde [abonnement](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) als de bron waaraan u het openbare IP-adres wilt koppelen.|
   |Resourcegroep|Ja|Kan in dezelfde of andere [resourcegroep](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) bestaan als de resource waaraan u het openbare IP-adres wilt koppelen.|
   |Name|Ja|De naam moet uniek zijn binnen de resourcegroep die u selecteert.|
   |Regio|Ja|Moet bestaan in dezelfde [regio](https://azure.microsoft.com/regions)als de openbare IP-adressen die u adressen uit het bereik toewijst.|
   |Voorvoegselgrootte|Ja| De grootte van het voorvoegsel dat u nodig hebt. Een IP-adres van /28 of 16 is de standaardinstelling.

**Opdrachten**

|Hulpprogramma|Opdracht|
|---|---|
|CLI|[az-netwerk public-ip-voorvoegsel maken](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-create)|
|PowerShell|[Nieuw-AzPublicIpPrefix](/powershell/module/az.network/new-azpublicipprefix)|

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>Een statisch openbaar IP-adres maken op basis van een voorvoegsel
Zodra u een voorvoegsel hebt gemaakt, moet u statische IP-adressen maken van het voorvoegsel. Volg hiervoor onderstaande stappen.

1. Typ in het vak met de tekst *Zoekbronnen* boven aan de *Azure-portal het voorvoegsel van openbare IP-adres*. Wanneer **voorvoegsels voor openbaar IP-adres** in de zoekresultaten worden weergegeven, selecteert u deze.
2. Selecteer het voorvoegsel waaruit u openbare IP-teksten wilt maken.
3. Wanneer deze in de zoekresultaten wordt weergegeven, selecteert u deze en klikt u op **+IP-adres toevoegen** in de sectie Overzicht.
4. Waarden voor de volgende instellingen invoeren of selecteren onder **Openbaar IP-adres maken**. Aangezien een voorvoegsel voor standaard SKU, IPv4 en statisch is, hoeft u alleen de volgende informatie te verstrekken:

   |Instelling|Vereist?|Details|
   |---|---|---|
   |Name|Ja|De naam van het openbare IP-adres moet uniek zijn binnen de resourcegroep die u selecteert.|
   |Niet-actieve time-out (minuten)|Nee|Hoeveel minuten om een TCP- of HTTP-verbinding open te houden zonder erop te vertrouwen dat clients keep-alive berichten verzenden. |
   |DNS-naamlabel|Nee|Moet uniek zijn binnen het Azure-gebied waarin u de naam maakt (voor alle abonnementen en alle klanten). Azure registreert automatisch de naam en het IP-adres in zijn DNS, zodat u verbinding maken met een bron met de naam. Azure voegt een standaardsubnet toe, zoals *location.cloudapp.azure.com* (waar locatie de locatie is die u selecteert) aan de naam die u opgeeft, om de volledig gekwalificeerde DNS-naam te maken. Zie [Azure DNS gebruiken met een openbaar IP-adres](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address)van Azure voor meer informatie.|

U ook de onderstaande CLI- en PS-opdrachten gebruiken met de parameters --public-ip-prefix (CLI) en -PublicIpPrefix (PS) om een ip-adresbron voor openbaar ip-adres te maken. 

|Hulpprogramma|Opdracht|
|---|---|
|CLI|[az network public-ip create](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create)|
|PowerShell|[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress?view=azps-2.0.0)|

## <a name="view-or-delete-a-prefix"></a>Een voorvoegsel weergeven of verwijderen

1. Typ in het vak met de tekst *Zoekbronnen* boven aan de *Azure-portal het voorvoegsel van openbare IP-adres*. Wanneer **voorvoegsels voor openbaar IP-adres** in de zoekresultaten worden weergegeven, selecteert u deze.
2. Selecteer de naam van het openbare IP-adresvoorvoegsel waarvoor u wilt weergeven, instellingen wijzigen of verwijderen uit de lijst.
3. Voltooi een van de volgende opties, afhankelijk van of u het openbare IP-adresvoorvoegsel wilt bekijken, verwijderen of wijzigen.
   - **Weergave**: In de sectie **Overzicht** worden de belangrijkste instellingen voor het openbare IP-adresvoorvoegsel weergegeven, zoals voorvoegsel.
   - **Verwijderen:** als u het openbare IP-adresvoorvoegsel wilt verwijderen, selecteert u **Verwijderen** in de sectie **Overzicht.** Als adressen in het voorvoegsel zijn gekoppeld aan openbare IP-adresbronnen, moet u eerst de openbare IP-adresbronnen verwijderen. Zie [een openbaar IP-adres verwijderen](virtual-network-public-ip-address.md#view-change-settings-for-or-delete-a-public-ip-address).

**Opdrachten**

|Hulpprogramma|Opdracht|
|---|---|
|CLI|[az-netwerk public-ip prefix lijst](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-list) om openbare IP-adressen te vermelden, [az-netwerk public-ip prefix show](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-show) to show settings; [openbaar-ip-voorvoegsel van het AZ-netwerk](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-update) om bij te werken; [az-netwerk public-ip-voorvoegsel verwijderen](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-delete) om te verwijderen|
|PowerShell|[Get-AzPublicIpPrefix](/powershell/module/az.network/get-azpublicipprefix) om een openbaar IP-adresobject op te halen en de instellingen ervan te bekijken, [Set-AzPublicIpPrefix](/powershell/module/az.network/set-azpublicipprefix) om instellingen bij te werken; [Verwijder-AzPublicIpPrefix](/powershell/module/az.network/remove-azpublicipprefix) om te verwijderen|

## <a name="permissions"></a>Machtigingen

Als u taken wilt uitvoeren op openbare IP-adresvoorvoegsels, moet uw account worden toegewezen aan de [rol van netwerkbijdrager](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) of aan een [aangepaste](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) rol waaraan de juiste acties in de volgende tabel zijn toegewezen:

| Actie                                                            | Name                                                           |
| ---------                                                         | -------------                                                  |
| Microsoft.Network/publicIPPrefixes/read                           | Een openbaar IP-adresvoorvoegsel lezen                                |
| Microsoft.Network/publicIPPrefixes/write                          | Een openbaar IP-adresvoorvoegsel maken of bijwerken                    |
| Microsoft.Network/publicIPPrefixes/delete                         | Een openbaar IP-adresvoorvoegsel verwijderen                              |
|Microsoft.Network/publicIPPrefixes/join/action                     | Een openbaar IP-adres maken op basis van een voorvoegsel |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over scenario's en voordelen van het gebruik van een [openbaar IP-voorvoegsel](public-ip-address-prefix.md)
