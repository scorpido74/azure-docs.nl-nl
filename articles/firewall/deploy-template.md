---
title: Azure Firewall implementeren met behulp van een sjabloon
description: Azure Firewall implementeren met behulp van een sjabloon
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 7/9/2018
ms.author: victorh
ms.openlocfilehash: c0a6cda54a58e3cc03ba31e221fb57fc725dd779
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839392"
---
# <a name="deploy-azure-firewall-using-a-template"></a>Azure Firewall implementeren met behulp van een sjabloon

Met de [sjabloon maken AzureFirewall sandbox-installatie](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox) maakt u een test netwerk omgeving met een firewall. Het netwerk heeft één virtueel netwerk (VNet) met drie subnetten: *AzureFirewallSubnet*, *ServersSubnet*en *JumpboxSubnet*. Het subnet *ServersSubnet* en *JumpboxSubnet* heeft één virtuele Windows Server-machine met twee kernen.

De firewall bevindt zich in het *AzureFirewallSubnet* -subnet en heeft een verzameling van toepassings regels met één regel waarmee toegang tot `www.microsoft.com`wordt toegestaan.

Een door de gebruiker gedefinieerde route wijst netwerk verkeer van het *ServersSubnet* -subnet via de firewall, waarbij de firewall regels worden toegepast.

Zie [Azure firewall implementeren en configureren met behulp van de Azure Portal](tutorial-firewall-deploy-portal.md)voor meer informatie over Azure firewall.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-the-template-to-deploy-azure-firewall"></a>De sjabloon gebruiken om Azure Firewall te implementeren

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

**Azure Firewall installeren en implementeren met behulp van de sjabloon:**

1. Open de sjabloon op [https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox).
   
1. Lees de inleiding en klik wanneer u klaar bent om te implementeren **op implementeren in azure**.
   
1. Meld u indien nodig aan bij de Azure Portal. 

1. Typ of selecteer de volgende waarden op de pagina **een sandbox-instelling maken van AzureFirewall** in de portal:
   
   - **Resource groep**: Selecteer **nieuwe maken**, typ een naam voor de resource groep en selecteer **OK**. 
   - **Virtual Network naam**: Typ een naam voor het nieuwe VNet. 
   - **Beheerder gebruikers naam**: Typ een gebruikers naam voor het gebruikers account van de beheerder.
   - **Beheerders wachtwoord**: Typ een beheerders wachtwoord. 
   
1. Lees de voor waarden en selecteer **Ik ga akkoord met de bovenstaande voor waarden**.
   
1. Selecteer **Aankoop**.
   
   Het duurt enkele minuten voordat de resources zijn gemaakt. 
   
1. Verken de resources die zijn gemaakt met de firewall. 

Zie [micro soft. Network/azureFirewalls](/azure/templates/microsoft.network/azurefirewalls)voor meer informatie over de JSON-syntaxis en eigenschappen voor een firewall in een sjabloon.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u deze niet meer nodig hebt, kunt u de resource groep, de firewall en alle gerelateerde bronnen verwijderen door de Power shell [-opdracht Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) uit te voeren. Als u een resource groep met de naam *MyResourceGroup*wilt verwijderen, voert u de volgende handelingen uit: 

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```
Verwijder de resource groep en de firewall nog niet, als u van plan bent om door te gaan naar de zelf studie over Firewall bewaking. 

## <a name="next-steps"></a>Volgende stappen

Vervolgens kunt u de Azure Firewall-logboeken bewaken:

> [!div class="nextstepaction"]
> [Zelfstudie: Azure Firewall-logboeken bewaken](./tutorial-diagnostics.md)
