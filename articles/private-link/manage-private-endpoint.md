---
title: Een privé-eindpuntverbinding beheren in Azure
description: Meer informatie over het beheren van privé-eindpuntverbindingen in Azure
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 62b24b3e2f5c1b89fa7db581ac34cf58381db2a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75452968"
---
# <a name="manage-a-private-endpoint-connection"></a>Manage a Private Endpoint connection (Een privé-eindpuntverbinding beheren)
Azure Private Link werkt aan een goedkeuringsoproepstroommodel waarin de gebruiker van de Private Link-service een verbinding met de serviceprovider kan aanvragen voor het gebruik van de service. De dienstverlener kan dan beslissen of de consument al dan niet verbinding wil maken. Azure Private Link stelt de serviceproviders in staat om de privé-eindpuntverbinding op hun resources te beheren. In dit artikel vindt u instructies over het beheren van de privé-eindpuntverbindingen.

![Privéeindpunten beheren](media/manage-private-endpoint/manage-private-endpoint.png)

Er zijn twee verbindingsgoedkeuringsmethoden waaruit een private link-serviceconsument kan kiezen:
- **Automatisch**: Als de serviceconsument RBAC-machtigingen heeft op de bron van de serviceprovider, kan de consument de automatische goedkeuringsmethode kiezen. In dit geval, wanneer de aanvraag de bron van de serviceprovider bereikt, is er geen actie vereist van de serviceprovider en wordt de verbinding automatisch goedgekeurd. 
- **Handleiding**: Integendeel, als de gebruiker van de dienst geen RBAC-machtigingen heeft op de bron van de serviceprovider, kan de consument de handmatige goedkeuringsmethode kiezen. In dit geval wordt de verbindingsaanvraag weergegeven op de serviceresources als **In behandeling**. De serviceprovider moet de aanvraag handmatig goedkeuren voordat verbindingen kunnen worden gemaakt. In handmatige gevallen kan de serviceconsument ook een bericht opgeven met het verzoek om de serviceprovider meer context te bieden. De serviceprovider heeft de volgende opties om uit te kiezen voor alle privé-eindpuntverbindingen: **goedgekeurd**, **weigeren**, **verwijderen**.

In de onderstaande tabel worden de verschillende acties van de serviceprovider en de resulterende verbindingsstatussen voor privéeindpunten weergegeven.  De serviceprovider kan ook de verbindingsstatus van de privé-eindpuntverbinding op een later tijdstip wijzigen zonder tussenkomst van de consument. De actie zal de toestand van het eindpunt aan de consumentenzijde bijwerken. 


|ServiceProvider-actie   |Service Consumer Private Endpoint State   |Beschrijving   |
|---------|---------|---------|
|Geen    |    In behandeling     |    De verbinding wordt handmatig gemaakt en is in afwachting van goedkeuring door de eigenaar van de Private Link-bron.       |
|Goedkeuren    |  Goedgekeurd       |  De verbinding is automatisch of handmatig goedgekeurd en is klaar om te worden gebruikt.     |
|Afwijzen     | Geweigerd        | Verbinding is geweigerd door de eigenaar van de private link resource.        |
|Verwijderen    |  De verbinding verbroken       | Verbinding is verwijderd door de eigenaar van de privékoppelingsbron, het privéeindpunt wordt informatief en moet worden verwijderd voor het opruimen.        |
|   |         |         |
   
## <a name="manage-private-endpoint-connections-on-azure-paas-resources"></a>Manage Private Endpoint Connections on Azure PaaS resources (Privé-eindpuntverbindingen beheren in Azure PaaS-resources)
Portal is de voorkeursmethode voor het beheren van privéeindpuntverbindingen op Azure PaaS-resources. Momenteel hebben we geen PowerShell/CLI-ondersteuning voor het beheren van verbindingen op Azure PaaS-resources.
1. Meld u aan bij Azure Portal op https://portal.azure.com.
2. Navigeer naar Private Link Center.
3. Selecteer **onder Resources**het resourcetype dat u de privéeindpuntverbindingen wilt beheren.
4. Voor elk van uw resourcetypen u het aantal privé-eindpuntverbindingen weergeven dat eraan is gekoppeld. U de resources filteren als dat nodig is.
5. Selecteer de privéeindpuntverbindingen.  Selecteer onder de vermelde verbindingen de verbinding die u wilt beheren. 
6. U de status van de verbinding wijzigen door te selecteren uit de opties bovenaan.

## <a name="manage-private-endpoint-connections-on-a-customerpartner-owned-private-link-service"></a>Private Endpoint-verbindingen beheren op een private link-service die eigendom is van een klant/partner

Azure PowerShell en Azure CLI zijn de voorkeursmethoden voor het beheren van Private Endpoint-verbindingen op Microsoft Partner Services of services die eigendom zijn van de klant. Momenteel hebben we geen portalondersteuning voor het beheren van verbindingen op een Private Link-service.  
 
### <a name="powershell"></a>PowerShell 
  
Gebruik de volgende PowerShell-opdrachten om privéeindpuntverbindingen te beheren.  
#### <a name="get-private-link-connection-states"></a>Verbindingsstatussen voor privékoppelingen openen 
Gebruik `Get-AzPrivateLinkService` de cmdlet om de privé-eindpuntverbindingen en hun statussen te krijgen.  
```azurepowershell
Get-AzPrivateLinkService -Name myPrivateLinkService -ResourceGroupName myResourceGroup 
 ```
 
#### <a name="approve-a-private-endpoint-connection"></a>Een privé-eindpuntverbinding goedkeuren 
 
Gebruik `Approve-AzPrivateEndpointConnection` de cmdlet om een privé-eindpuntverbinding goed te keuren. 
 
```azurepowershell
Approve-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService
```
 
#### <a name="deny-private-endpoint-connection"></a>Privé-eindpuntverbinding weigeren 
 
Gebruik `Deny-AzPrivateEndpointConnection` de cmdlet om een privé-eindpuntverbinding af te wijzen. 
```azurepowershell
Deny-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService 
```
#### <a name="remove-private-endpoint-connection"></a>Privé-eindpuntverbinding verwijderen 
 
Gebruik `Remove-AzPrivateEndpointConnection` de cmdlet om een privé-eindpuntverbinding te verwijderen. 
```azurepowershell
Remove-AzPrivateEndpointConnection -Name myPrivateEndpointConnection1 -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkServiceName 
```
 
### <a name="azure-cli"></a>Azure-CLI 
 
Gebruiken `az network private-link-service update` voor het beheren van uw privé-eindpuntverbindingen. De verbindingsstatus is ```azurecli connection-status``` opgegeven in de parameter. 
```azurecli
az network private-link-service connection update -g myResourceGroup -n myPrivateEndpointConnection1 --service-name myPLS --connection-status Approved 
```

   

## <a name="next-steps"></a>Volgende stappen
- [Meer informatie over privéeindpunten](private-endpoint-overview.md)
 
