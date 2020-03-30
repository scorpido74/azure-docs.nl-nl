---
title: Azure-gebeurtenishubs integreren met Azure Private Link-service
description: Meer informatie over het integreren van Azure Event Hubs met Azure Private Link Service
services: event-hubs
author: spelluru
ms.author: spelluru
ms.date: 03/12/2020
ms.service: event-hubs
ms.topic: article
ms.openlocfilehash: cff1b3b79b34d3f0bed27a2ea50799185958a8ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477848"
---
# <a name="integrate-azure-event-hubs-with-azure-private-link-preview"></a>Azure-gebeurtenishubs integreren met Azure Private Link (voorbeeld)
Azure Private Link Service stelt u in staat om toegang te krijgen tot Azure Services (bijvoorbeeld Azure Event Hubs, Azure Storage en Azure Cosmos DB) en Azure hosted customer/partner services via een **privé eindpunt** in uw virtuele netwerk.

Een privéeindpunt is een netwerkinterface die u privé en veilig verbindt met een service die wordt aangedreven door Azure Private Link. Het privéeindpunt maakt gebruik van een privé-IP-adres van uw VNet, waardoor de service effectief in uw VNet wordt opgenomen. Al het verkeer naar de service kan worden doorgestuurd via het privéeindpunt, zodat er geen gateways, NAT-apparaten, ExpressRoute- of VPN-verbindingen of openbare IP-adressen nodig zijn. Verkeer tussen uw virtuele netwerk en de services wordt via het backbonenetwerk van Microsoft geleid, waarmee de risico's van het openbare internet worden vermeden. U verbinding maken met een instantie van een Azure-bron, zodat u het hoogste niveau van granulariteit in toegangscontrole hebt.

Zie Wat is Azure Private Link voor meer [informatie?](../private-link/private-link-overview.md)

> [!NOTE]
> Deze functie wordt alleen ondersteund met de **specifieke** laag. Zie [Overzicht van evenementhubs dedicated](event-hubs-dedicated-overview.md)voor meer informatie over de specifieke laag. 
>
> Deze functie is momenteel in **preview**. 


## <a name="add-a-private-endpoint-using-azure-portal"></a>Een privéeindpunt toevoegen met Azure-portal

### <a name="prerequisites"></a>Vereisten

Als u een naamruimte voor gebeurtenishubs wilt integreren met Azure Private Link, hebt u de volgende entiteiten of machtigingen nodig:

- Een naamruimte voor gebeurtenishubs.
- Een virtueel Azure-netwerk.
- Een subnet in het virtuele netwerk.
- Machtigingen voor eigenaren of inzenders voor zowel de naamruimte als het virtuele netwerk.

Uw privéeindpunt en virtueel netwerk moeten zich in dezelfde regio bevinden. Wanneer u een regio voor het privéeindpunt selecteert met behulp van de portal, filtert het automatisch alleen virtuele netwerken die zich in die regio bevinden. Uw naamruimte kan zich in een andere regio bevinden.

Uw privéeindpunt gebruikt een privé-IP-adres in uw virtuele netwerk.

### <a name="steps"></a>Stappen
Als u al een naamruimte voor gebeurtenishubs hebt, u een privékoppelingsverbinding maken door de volgende stappen te volgen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). 
2. Typ in de zoekbalk **gebeurtenishubs**.
3. Selecteer de **naamruimte** in de lijst waaraan u een privéeindpunt wilt toevoegen.
4. Selecteer het tabblad **Netwerken** onder **Instellingen**.
5. Selecteer het tabblad **Privéeindpuntverbindingen (voorbeeld)** boven aan de pagina. Als u geen speciale laag gebeurtenishubs gebruikt, ziet u een bericht: **Privéeindpuntverbindingen op gebeurtenishubs worden alleen ondersteund door naamruimten die zijn gemaakt onder een speciaal cluster.**
6. Selecteer de knop **+ Privéeindpunt** boven aan de pagina.

    ![Installatiekopie](./media/private-link-service/private-link-service-3.png)
7. Voer **op** de pagina Basics de volgende stappen uit: 
    1. Selecteer het **Azure-abonnement** waarin u het privéeindpunt wilt maken. 
    2. Selecteer de **resourcegroep** voor de privéeindpuntbron.
    3. Voer een **naam** in voor het privéeindpunt. 
    5. Selecteer een **gebied** voor het privéeindpunt. Uw privéeindpunt moet zich in dezelfde regio bevinden als uw virtuele netwerk, maar kan zich in een andere regio bevinden, tegenover de bron voor de privékoppeling waarmee u verbinding maakt. 
    6. Selecteer **Volgende: Resource >** knop onder aan de pagina.

        ![Privéeindpunt maken - pagina Basisbeginselen](./media/private-link-service/create-private-endpoint-basics-page.png)
8. Voer op de pagina **Resource** de volgende stappen uit:
    1. Voer voor verbindingsmethode Verbinding **maken met een Azure-bron in mijn map**uit: 
        1. Selecteer het **Azure-abonnement** waarin de **naamruimte van** uw gebeurtenishubs bestaat. 
        2. Selecteer **Microsoft.EventHub/naamruimten** voor het **resourcetype**voor **Resourcetype**voor Resourcetype .
        3. Selecteer **voor Resource**een naamruimte voor gebeurtenishubs in de vervolgkeuzelijst. 
        4. Controleer of de **subresource Doel** is ingesteld op **naamruimte**.
        5. Selecteer **Volgende: Configuratie >** knop onder aan de pagina. 
        
            ![Privéeindpunt maken - pagina Resource](./media/private-link-service/create-private-endpoint-resource-page.png)    
    2. Als u **Verbinding maken met een Azure-bron selecteert op bron-id of alias,** voert u de volgende stappen uit:
        1. Voer de **bron-id** of **alias**in . Het kan de resource-id of alias zijn die sommigen met u hebben gedeeld.
        2. Voer **voor Subresource Target** **naamruimte**in . Het is het type subresource waartoe uw privéeindpunt toegang heeft.
        3. (facultatief) Voer een **aanvraagbericht in**. De eigenaar van de resource ziet dit bericht tijdens het beheren van een privé-eindpuntverbinding.
        4. Selecteer vervolgens **Volgende: Configuratie >** knop onder aan de pagina.

            ![Privéeindpunt maken - Verbinding maken met resource-id](./media/private-link-service/connect-resource-id.png)
9. Op de pagina **Configuratie** selecteert u het subnet in een virtueel netwerk naar de plaats waar u het privéeindpunt wilt implementeren. 
    1. Selecteer een **virtueel netwerk**. Alleen virtuele netwerken in het momenteel geselecteerde abonnement en locatie worden vermeld in de vervolgkeuzelijst. 
    2. Selecteer een **subnet** in het virtuele netwerk dat u hebt geselecteerd. 
    3. Selecteer **Volgende: Tags >** knop onder aan de pagina. 

        ![Privéeindpunt maken - pagina Configuratie](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. Maak op de pagina **Labels** tags tags (namen en waarden) die u wilt koppelen aan de private endpoint-bron. Selecteer vervolgens De knop **Controleren + maken** onder aan de pagina. 
11. Bekijk in de **optieR aan het maken ,** controleer alle instellingen en selecteer **Maken** om het privéeindpunt te maken.
    
    ![Privéeindpunt maken - Pagina controleren en maken](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. Controleer of u de privé-eindpuntverbinding ziet die u hebt gemaakt, wordt weergegeven in de lijst met eindpunten. In dit voorbeeld wordt het privéeindpunt automatisch goedgekeurd omdat u verbinding hebt gemaakt met een Azure-bron in uw map en u over voldoende machtigingen beschikt. 

    ![Privéeindpunt gemaakt](./media/private-link-service/private-endpoint-created.png)

## <a name="add-a-private-endpoint-using-powershell"></a>Een privéeindpunt toevoegen met PowerShell
In het volgende voorbeeld ziet u hoe u Azure PowerShell gebruiken om een privé-eindpuntverbinding te maken. Het maakt geen speciaal cluster voor u. Volg stappen in [dit artikel](event-hubs-dedicated-cluster-create-portal.md) om een speciaal cluster van gebeurtenishubs te maken. 

```azurepowershell-interactive
# create resource group

$rgName = "<RESOURCE GROUP NAME>"
$vnetlocation = "<VIRTUAL NETWORK LOCATION>"
$vnetName = "<VIRTUAL NETWORK NAME>"
$subnetName = "<SUBNET NAME>"
$namespaceLocation = "<NAMESPACE LOCATION>"
$namespaceName = "<NAMESPACE NAME>"
$peConnectionName = "<PRIVATE ENDPOINT CONNECTION NAME>"

# create virtual network
$virtualNetwork = New-AzVirtualNetwork `
                    -ResourceGroupName $rgName `
                    -Location $vnetlocation `
                    -Name $vnetName `
                    -AddressPrefix 10.0.0.0/16

# create subnet with endpoint network policy disabled
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
                    -Name $subnetName `
                    -AddressPrefix 10.0.0.0/24 `
                    -PrivateEndpointNetworkPoliciesFlag "Disabled" `
                    -VirtualNetwork $virtualNetwork

# update virtual network
$virtualNetwork | Set-AzVirtualNetwork

# create an event hubs namespace in a dedicated cluster
$namespaceResource = New-AzResource -Location $namespaceLocation `
                                    -ResourceName $namespaceName `
                                    -ResourceGroupName $rgName `
                                    -Sku @{name = "Standard"; capacity = 1} `
                                    -Properties @{clusterArmId = "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/clusters/<EVENT HUBS CLUSTER NAME>"} `
                                    -ResourceType "Microsoft.EventHub/namespaces" -ApiVersion "2018-01-01-preview"

# create private endpoint connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name $peConnectionName `
                                -PrivateLinkServiceId $namespaceResource.ResourceId `
                                -GroupId "namespace"

# get subnet object that you will use later
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $rgName -Name $vnetName
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                                | Where-Object  {$_.Name -eq $subnetName}  
   
# create a private endpoint   
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName  `
                                -Name $vnetName   `
                                -Location $vnetlocation `
                                -Subnet  $subnet   `
                                -PrivateLinkServiceConnection $privateEndpointConnection

(Get-AzResource -ResourceId $namespaceResource.ResourceId -ExpandProperties).Properties


```

## <a name="manage-private-endpoints-using-azure-portal"></a>Privéeindpunten beheren met Azure-portal

Wanneer u een privéeindpunt maakt, moet de verbinding worden goedgekeurd. Als de bron waarvoor u een privéeindpunt maakt, zich in uw map bevindt, u de verbindingsaanvraag goedkeuren, mits u over voldoende machtigingen beschikt. Als u verbinding maakt met een Azure-bron in een andere map, moet u wachten tot de eigenaar van die bron uw verbindingsaanvraag goedkeurt.

Er zijn vier provisioning staten:

| Serviceactie | Particuliere eindpuntstaat voor serviceconsumenten | Beschrijving |
|--|--|--|
| Geen | In behandeling | De verbinding wordt handmatig gemaakt en wordt in afwachting van goedkeuring van de eigenaar van de Private Link-bron. |
| Goedkeuren | Goedgekeurd | De verbinding is automatisch of handmatig goedgekeurd en is klaar om te worden gebruikt. |
| Afwijzen | Geweigerd | Verbinding is geweigerd door de eigenaar van de private link resource. |
| Verwijderen | De verbinding verbroken | Verbinding is verwijderd door de eigenaar van de privékoppelingsbron, het privéeindpunt wordt informatief en moet worden verwijderd voor het opschonen. |
 
###  <a name="approve-reject-or-remove-a-private-endpoint-connection"></a>Een privé-eindpuntverbinding goedkeuren, afwijzen of verwijderen

1. Meld u aan bij Azure Portal.
2. Typ in de zoekbalk **gebeurtenishubs**.
3. Selecteer de **naamruimte** die u wilt beheren.
4. Selecteer het tabblad **Netwerken.**
5. Ga naar het juiste gedeelte hieronder op basis van de bewerking die u wilt uitvoeren: goedkeuren, weigeren of verwijderen.

### <a name="approve-a-private-endpoint-connection"></a>Een privé-eindpuntverbinding goedkeuren
1. Als er verbindingen in behandeling zijn, wordt er een verbinding weergegeven met **Inbehandeling** in de inrichtingsstatus. 
2. Selecteer het **privéeindpunt** dat u wilt goedkeuren
3. Selecteer de knop **Goedkeuren.**

    ![Installatiekopie](./media/private-link-service/approve-private-endpoint.png)
4. Voeg op de pagina **Verbindingsgoedkeuring** een opmerking toe (optioneel) en selecteer **Ja**. Als u **Nee**selecteert, gebeurt er niets. 
5. U ziet de status van de privé-eindpuntverbinding in de lijst die is gewijzigd in **Goedgekeurd**. 

### <a name="reject-a-private-endpoint-connection"></a>Een privé-eindpuntverbinding weigeren

1. Als er privéeindpuntverbindingen zijn die u wilt weigeren, selecteert u de verbinding of klikt u op de knop **Weigeren** als het gaat om een aanvraag in behandeling of een bestaande verbinding.

    ![Installatiekopie](./media/private-link-service/private-endpoint-reject-button.png)
2. Voer **op** de pagina Verbinding weigeren een opmerking in (optioneel) en selecteer **Ja**. Als u **Nee**selecteert, gebeurt er niets. 
3. U moet de status van de privé-eindpuntverbinding zien in de lijst die is gewijzigd in **Afgewezen**. 

### <a name="remove-a-private-endpoint-connection"></a>Een privé-eindpuntverbinding verwijderen

1. Als u een privé-eindpuntverbinding wilt verwijderen, selecteert u deze in de lijst en selecteert u **Verwijderen** op de werkbalk.
2. Selecteer op de pagina **Verbinding verwijderen** de optie **Ja** om het verwijderen van het privéeindpunt te bevestigen. Als u **Nee**selecteert, gebeurt er niets.
3. U moet de status wijzigen in **Verbroken**. Vervolgens ziet u het eindpunt uit de lijst verdwijnen.

## <a name="validate-that-the-private-link-connection-works"></a>Valideren of de privékoppelingsverbinding werkt

U moet valideren dat de bronnen binnen hetzelfde subnet van de private endpoint-bron verbinding maken met de naamruimte van uw Gebeurtenishubs via een privé-IP-adres en dat ze de juiste integratie van de privé-DNS-zone hebben.

Maak eerst een virtuele machine door de stappen te volgen in [Een virtuele Windows-machine maken in de Azure-portal](../virtual-machines/windows/quick-create-portal.md)

Ga als het tabblad Netwerken als een van **de** documenten:

1. Virtueel **netwerk** en **subnet**opgeven . U een nieuw virtueel netwerk maken of een bestaand netwerk selecteren. Als u een bestaande regio selecteert, moet u ervoor zorgen dat de regio overeenkomt.
1. Geef een **openbare IP-bron** op.
1. Selecteer **Geen in**de **groep NIC-netwerkbeveiliging**.
1. Selecteer **Nee**in **de taakverdeling Laden**.

Open de opdrachtregel en voer de volgende opdracht uit:

```console
nslookup <your-event-hubs-namespace-name>.servicebus.windows.net
```

Als u de opdracht ns-opzoeking uitvoert om het IP-adres van een naamruimte voor gebeurtenishubs op te lossen via een openbaar eindpunt, ziet u een resultaat dat er als volgt uitziet:

```console
c:\ >nslookup <your-event-hubs-namespae-name>.servicebus.windows.net

Non-authoritative answer:
Name:    
Address:  (public IP address)
Aliases:  <your-event-hubs-namespace-name>.servicebus.windows.net
```

Als u de opdracht ns-opzoeking uitvoert om het IP-adres van een naamruimte van een gebeurtenishubs op te lossen via een privéeindpunt, ziet u een resultaat dat er als volgt uitziet:

```console
c:\ >nslookup your_event-hubs-namespace-name.servicebus.windows.net

Non-authoritative answer:
Name:    
Address:  10.1.0.5 (private IP address)
Aliases:  <your-event-hub-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>Beperkingen en ontwerpoverwegingen

**Prijzen:** Zie Azure [Private Link-prijzen](https://azure.microsoft.com/pricing/details/private-link/)voor prijsinformatie.

**Beperkingen**: Privéeindpunt voor Azure-gebeurtenishubs is in openbare preview. Deze functie is beschikbaar in alle openbare Azure-regio's.

**Maximaal aantal privéeindpunten per naamruimte van gebeurtenishubs:** 120.

Zie Azure [Private Link-service: Beperkingen voor](../private-link/private-link-service-overview.md#limitations) meer informatie

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure Private Link](../private-link/private-link-service-overview.md)
- Meer informatie over [Azure Event Hubs](event-hubs-about.md)
