---
title: Azure Firewall implementeren met behulp van een sjabloon
description: Azure Firewall implementeren met behulp van een sjabloon. Het gecreëerde netwerk heeft één VNet met drie subnetten. Er worden twee virtuele Windows Server-machines met twee cores geïmplementeerd.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: b9e82998283bf44eede88148a12d88aae521b1ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74169199"
---
# <a name="deploy-azure-firewall-using-a-template"></a>Azure Firewall implementeren met behulp van een sjabloon

Met [de sjabloon AzureFirewall sandbox setup maken](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox) wordt een testnetwerkomgeving gemaakt met een firewall. Het netwerk heeft één virtueel netwerk (VNet) met drie subnetten: *AzureFirewallSubnet,* *ServersSubnet*en *JumpboxSubnet*. Het *subnet ServersSubnet* en *JumpboxSubnet* hebben elk één virtuele windows server met twee cores.

De firewall bevindt zich in het *subnet AzureFirewallSubnet* en heeft een `www.microsoft.com`verzameling van toepassingsregels met één regel die toegang geeft tot .

Een door de gebruiker gedefinieerde route wijst het netwerkverkeer van het *Subnet ServersSubnet* door de firewall, waar de firewallregels worden toegepast.

Zie Azure Firewall implementeren [en configureren met behulp van de Azure-portal](tutorial-firewall-deploy-portal.md)voor meer informatie over Azure Firewall.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-the-template-to-deploy-azure-firewall"></a>De sjabloon gebruiken om Azure Firewall te implementeren

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

**Ga als het gaat om het installeren en implementeren van Azure Firewall met behulp van de sjabloon:**

1. Toegang tot [https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox)de sjabloon op .
   
1. Lees de inleiding en selecteer Implementeren naar **Azure**wanneer u klaar bent voor implementatie.
   
1. Meld u indien nodig aan bij Azure Portal. 

1. Typ of selecteer in de portal op de pagina **Een sandbox-instelling van AzureFirewall** maken of selecteer de volgende waarden:
   
   - **Resourcegroep**: Selecteer **Nieuw maken,** typ een naam voor de resourcegroep en selecteer **OK**. 
   - **Virtuele netwerknaam:** typ een naam voor het nieuwe VNet. 
   - **Gebruikersnaam beheerder:** typ een gebruikersnaam voor het beheerdersgebruikersaccount.
   - **Beheerderswachtwoord:** typ een beheerderswachtwoord. 
   
1. Lees de algemene voorwaarden, en selecteer **dan ik ga akkoord met de algemene voorwaarden hierboven**vermeld .
   
1. Selecteer **Aankoop**.
   
   Het duurt een paar minuten om de bronnen te maken. 
   
1. Ontdek de bronnen die met de firewall zijn gemaakt. 

Zie [Microsoft.Network/azureFirewalls](/azure/templates/microsoft.network/azurefirewalls)voor meer informatie over de syntaxis en eigenschappen van JSON voor een firewall in een sjabloon.

## <a name="clean-up-resources"></a>Resources opschonen

Als u ze niet meer nodig hebt, u de brongroep, firewall en alle gerelateerde resources verwijderen door de opdracht [Verwijderen-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) PowerShell uit te voeren. Voer het als nodig op om een resourcegroep met de naam *MyResourceGroup*te verwijderen: 

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```
Verwijder de brongroep en firewall nog niet als u van plan bent door te gaan naar de zelfstudie voor firewallbewaking. 

## <a name="next-steps"></a>Volgende stappen

Vervolgens u de Azure Firewall-logboeken controleren:

> [!div class="nextstepaction"]
> [Zelfstudie: Azure Firewall-logboeken bewaken](./tutorial-diagnostics.md)
