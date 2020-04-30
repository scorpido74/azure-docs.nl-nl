---
title: Azure Event Hubs integreren met de persoonlijke koppelings service van Azure
description: Meer informatie over het integreren van Azure Event Hubs met de persoonlijke koppelings service van Azure
services: event-hubs
author: spelluru
ms.author: spelluru
ms.date: 03/12/2020
ms.service: event-hubs
ms.topic: article
ms.openlocfilehash: fb8fc93174345d0bdb09e4308a4206a65ed2270a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82148207"
---
# <a name="integrate-azure-event-hubs-with-azure-private-link-preview"></a>Azure Event Hubs integreren met een persoonlijke Azure-koppeling (preview)
Met Azure Private Link service kunt u toegang krijgen tot Azure-Services (bijvoorbeeld Azure Event Hubs, Azure Storage en Azure Cosmos DB) en door Azure gehoste klanten/partner services via een **persoonlijk eind punt** in uw virtuele netwerk.

Een persoonlijk eind punt is een netwerk interface waarmee u privé en veilig kunt verbinden met een service die wordt aangestuurd door een persoonlijke Azure-koppeling. Het persoonlijke eind punt maakt gebruik van een privé-IP-adres uit uw VNet, waardoor de service effectief in uw VNet wordt gezet. Al het verkeer naar de service kan worden gerouteerd via het persoonlijke eind punt, zodat er geen gateways, NAT-apparaten, ExpressRoute of VPN-verbindingen of open bare IP-adressen nodig zijn. Verkeer tussen uw virtuele netwerk en de services wordt via het backbonenetwerk van Microsoft geleid, waarmee de risico's van het openbare internet worden vermeden. U kunt verbinding maken met een exemplaar van een Azure-resource, zodat u het hoogste granulatie niveau krijgt in toegangs beheer.

Zie [Wat is Azure private link?](../private-link/private-link-overview.md) voor meer informatie.

> [!IMPORTANT]
> Deze functie wordt alleen ondersteund met de **toegewezen** laag. Zie [overzicht van Event hubs dedicated](event-hubs-dedicated-overview.md)voor meer informatie over de toegewezen laag. 
>
> Deze functie is momenteel beschikbaar als **Preview-versie**. 

>[!WARNING]
> Het inschakelen van persoonlijke eind punten kan verhinderen dat andere Azure-Services communiceren met Event Hubs.
>
> Vertrouwde micro soft-services worden niet ondersteund bij het gebruik van virtuele netwerken.
>
> Algemene scenario's voor Azure die niet met virtuele netwerken werken (Houd er rekening mee dat de lijst **niet** volledig is)-
> - Azure Monitor (diagnostische instelling)
> - Azure Stream Analytics
> - Integratie met Azure Event Grid
> - Azure-IoT Hub routes
> - Azure IoT-Device Explorer
>
> De volgende micro soft-services moeten zich in een virtueel netwerk bevinden
> - Azure Web Apps
> - Azure Functions

## <a name="add-a-private-endpoint-using-azure-portal"></a>Een persoonlijk eind punt toevoegen met Azure Portal

### <a name="prerequisites"></a>Vereisten

Als u een Event Hubs naam ruimte met een persoonlijke Azure-koppeling wilt integreren, hebt u de volgende entiteiten of machtigingen nodig:

- Een Event Hubs naam ruimte.
- Een virtueel Azure-netwerk.
- Een subnet in het virtuele netwerk.
- Eigenaar-of Inzender machtigingen voor de naam ruimte en het virtuele netwerk.

Uw persoonlijke eind punt en het virtuele netwerk moeten zich in dezelfde regio bevinden. Wanneer u een regio voor het persoonlijke eind punt selecteert met behulp van de portal, worden er automatisch alleen virtuele netwerken gefilterd die zich in die regio bevinden. Uw naam ruimte kan zich in een andere regio bevinden.

Uw persoonlijke eind punt maakt gebruik van een privé-IP-adres in uw virtuele netwerk.

### <a name="steps"></a>Stappen
Als u al een Event Hubs naam ruimte hebt, kunt u een koppeling voor een particuliere verbinding maken door de volgende stappen uit te voeren:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). 
2. Typ in de zoek balk in **Event hubs**.
3. Selecteer de **naam ruimte** in de lijst waaraan u een persoonlijk eind punt wilt toevoegen.
4. Selecteer het tabblad **netwerken** onder **instellingen**.
5. Selecteer het tabblad **Private endpoint Connections (preview)** boven aan de pagina. Als u geen toegewezen laag van Event Hubs gebruikt, wordt er een bericht weer gegeven: **privé-eindpunt verbindingen op Event hubs worden alleen ondersteund door naam ruimten die zijn gemaakt onder een toegewezen cluster**.
6. Selecteer de knop **+ privé-eind punt** boven aan de pagina.

    ![Installatiekopie](./media/private-link-service/private-link-service-3.png)
7. Voer de volgende stappen uit op de pagina **basis beginselen** : 
    1. Selecteer het **Azure-abonnement** waarin u het persoonlijke eind punt wilt maken. 
    2. Selecteer de **resource groep** voor de persoonlijke eindpunt resource.
    3. Voer een **naam** in voor het persoonlijke eind punt. 
    5. Selecteer een **regio** voor het persoonlijke eind punt. Uw persoonlijke eind punt moet zich in dezelfde regio bevinden als uw virtuele netwerk, maar kan zich in een andere regio bevinden dan de resource van de persoonlijke koppeling waarmee u verbinding maakt. 
    6. Selecteer **volgende: Resource >** knop onder aan de pagina.

        ![Privé-eind punt maken-pagina basis beginselen](./media/private-link-service/create-private-endpoint-basics-page.png)
8. Voer de volgende stappen uit op de pagina **resource** :
    1. Als u **verbinding maken met een Azure-resource in mijn Directory**selecteert, voert u de volgende stappen uit om de verbindings methode te selecteren: 
        1. Selecteer het **Azure-abonnement** waarin uw **Event hubs naam ruimte** bestaat. 
        2. Selecteer voor **resource type**de optie **micro soft. EventHub/naam ruimten** voor het **bron type**.
        3. Selecteer voor **resource**een event hubs naam ruimte in de vervolg keuzelijst. 
        4. Controleer of de **doel-subresource** is ingesteld op **naam ruimte**.
        5. Selecteer **volgende: configuratie >** knop onder aan de pagina. 
        
            ![Privé-eind punt maken-resource pagina](./media/private-link-service/create-private-endpoint-resource-page.png)    
    2. Als u **verbinding maken met een Azure-resource selecteert op resource-id of alias**, voert u de volgende stappen uit:
        1. Voer de **resource-id** of **alias**in. Dit kan de resource-ID of alias zijn die iemand met u heeft gedeeld. De eenvoudigste manier om de resource-ID op te halen, is door te navigeren naar de Event Hubs naam ruimte in de Azure Portal en het `/subscriptions/`gedeelte van de URI te kopiëren vanaf. Zie de volgende afbeelding voor een voor beeld. 
        2. Voer een **naam ruimte**in voor de **subresource**van het doel. Het is het type van de subbron waartoe uw persoonlijke eind punt toegang heeft.
        3. Beschrijving Voer een **aanvraag bericht**in. De resource-eigenaar ziet dit bericht tijdens het beheer van de verbinding met een privé-eind punt.
        4. Selecteer vervolgens **volgende: configuratie >** knop onder aan de pagina.

            ![Persoonlijk eind punt maken-verbinden via Resource-ID](./media/private-link-service/connect-resource-id.png)
9. Selecteer op de pagina **configuratie** het subnet in een virtueel netwerk waarop u het persoonlijke eind punt wilt implementeren. 
    1. Selecteer een **virtueel netwerk**. In de vervolg keuzelijst worden alleen virtuele netwerken in het geselecteerde abonnement en de huidige locatie weer gegeven. 
    2. Selecteer een **subnet** in het virtuele netwerk dat u hebt geselecteerd. 
    3. Selecteer **volgende: labels >** knop onder aan de pagina. 

        ![Privé-eind punt maken-configuratie pagina](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. Maak op de pagina **Tags** een wille keurige labels (namen en waarden) die u wilt koppelen aan de persoonlijke eindpunt resource. Selecteer vervolgens de knop **controleren + maken** onder aan de pagina. 
11. Controleer alle instellingen in het **overzicht en maken**en selecteer **maken** om het persoonlijke eind punt te maken.
    
    ![Privé-eind punt maken-pagina controleren en maken](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. Controleer of de verbinding van het privé-eind punt dat u hebt gemaakt, wordt weer gegeven in de lijst met eind punten. In dit voor beeld wordt het persoonlijke eind punt automatisch goedgekeurd omdat u verbinding hebt gemaakt met een Azure-resource in uw directory en u voldoende machtigingen hebt. 

    ![Persoonlijk eind punt gemaakt](./media/private-link-service/private-endpoint-created.png)

## <a name="add-a-private-endpoint-using-powershell"></a>Een persoonlijk eind punt toevoegen met Power shell
In het volgende voor beeld ziet u hoe u Azure PowerShell kunt gebruiken om een verbinding met een privé-eind punt te maken. Er wordt geen speciaal cluster voor u gemaakt. Volg de stappen in [dit artikel](event-hubs-dedicated-cluster-create-portal.md) om een toegewezen Event hubs-cluster te maken. 

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

### <a name="configure-the-private-dns-zone"></a>De privé-DNS-zone configureren
Maak een privé-DNS-zone voor Event Hubs domein en maak een koppelings koppeling met het virtuele netwerk:

```azurepowershell-interactive
$zone = New-AzPrivateDnsZone -ResourceGroupName $rgName `
                            -Name "privatelink.servicebus.windows.net" 
 
$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName $rgName `
                                            -ZoneName "privatelink.servicebus.windows.net" `
                                            -Name "mylink" `
                                            -VirtualNetworkId $virtualNetwork.Id  
 
$networkInterface = Get-AzResource -ResourceId $privateEndpoint.NetworkInterfaces[0].Id -ApiVersion "2019-04-01" 
 
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) { 
    foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) { 
        Write-Host "$($ipconfig.properties.privateIPAddress) $($fqdn)"  
        $recordName = $fqdn.split('.',2)[0] 
        $dnsZone = $fqdn.split('.',2)[1] 
        New-AzPrivateDnsRecordSet -Name $recordName -RecordType A -ZoneName "privatelink.servicebus.windows.net"  `
                                -ResourceGroupName $rgName -Ttl 600 `
                                -PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address $ipconfig.properties.privateIPAddress)  
    } 
}
```

## <a name="manage-private-endpoints-using-azure-portal"></a>Privé-eind punten beheren met Azure Portal

Wanneer u een persoonlijk eind punt maakt, moet de verbinding worden goedgekeurd. Als de resource waarvoor u een persoonlijk eind punt maakt zich in uw directory bevindt, kunt u de verbindings aanvraag goed keuren die u voldoende machtigingen hebt. Als u verbinding maakt met een Azure-resource in een andere Directory, moet u wachten tot de eigenaar van die resource uw verbindings aanvraag goed keuren.

Er zijn vier inrichtings provincies:

| Service actie | Status privé-eind punt service gebruiker | Beschrijving |
|--|--|--|
| Geen | In behandeling | De verbinding wordt hand matig gemaakt en in afwachting van goed keuring van de resource-eigenaar van de persoonlijke koppeling. |
| Goedkeuren | Goedgekeurd | De verbinding is automatisch of hand matig goedgekeurd en is klaar om te worden gebruikt. |
| Afwijzen | Geweigerd | De verbinding is geweigerd door de resource-eigenaar van de persoonlijke koppeling. |
| Verwijderen | De verbinding verbroken | De verbinding is verwijderd door de resource-eigenaar van de persoonlijke koppeling, het persoonlijke eind punt wordt informatieve en moet worden verwijderd voor opschoning. |
 
###  <a name="approve-reject-or-remove-a-private-endpoint-connection"></a>Een verbinding met een privé-eind punt goed keuren, afwijzen of verwijderen

1. Meld u aan bij Azure Portal.
2. Typ in de zoek balk in **Event hubs**.
3. Selecteer de **naam ruimte** die u wilt beheren.
4. Selecteer het tabblad **netwerken** .
5. Ga naar de toepasselijke sectie hieronder op basis van de bewerking die u wilt: goed keuren, afwijzen of verwijderen.

### <a name="approve-a-private-endpoint-connection"></a>Een verbinding met een privé-eind punt goed keuren
1. Als er verbindingen zijn die in behandeling zijn, ziet u een verbinding die wordt weer gegeven met in **behandeling** in de inrichtings status. 
2. Selecteer het **privé-eind punt** dat u wilt goed keuren
3. Selecteer de knop **goed keuren** .

    ![Installatiekopie](./media/private-link-service/approve-private-endpoint.png)
4. Voeg op de pagina **verbinding goed keuren** een opmerking toe (optioneel) en selecteer **Ja**. Als u **Nee**selecteert, gebeurt er niets. 
5. Als het goed is, ziet u de status van de verbinding met een privé-eind punt in de lijst gewijzigd in **goedgekeurd**. 

### <a name="reject-a-private-endpoint-connection"></a>Een persoonlijke eindpunt verbinding weigeren

1. Als er particuliere endpoint-verbindingen zijn die u wilt weigeren, ongeacht of het een aanvraag in behandeling of een bestaande verbinding is, selecteert u de verbinding en klikt u op de knop **afwijzen** .

    ![Installatiekopie](./media/private-link-service/private-endpoint-reject-button.png)
2. Voer op de pagina **verbinding afwijzen** een opmerking in (optioneel) en selecteer **Ja**. Als u **Nee**selecteert, gebeurt er niets. 
3. Als het goed is, ziet u de status van de verbinding met een privé-eind punt in de lijst gewijzigd in **afgewezen**. 

### <a name="remove-a-private-endpoint-connection"></a>Een verbinding met een privé-eind punt verwijderen

1. Als u een verbinding met een privé-eind punt wilt verwijderen, selecteert u deze in de lijst en selecteert u **verwijderen** op de werk balk.
2. Selecteer op de pagina **verbinding verwijderen** de optie **Ja** om het verwijderen van het persoonlijke eind punt te bevestigen. Als u **Nee**selecteert, gebeurt er niets.
3. U ziet dat de status is gewijzigd in **verbroken**. Vervolgens ziet u dat het eind punt verdwijnt uit de lijst.

## <a name="validate-that-the-private-link-connection-works"></a>Controleren of de verbinding van de persoonlijke verbinding werkt

U moet controleren of de resources binnen hetzelfde subnet van de persoonlijke eindpunt resource verbinding maken met uw Event Hubs naam ruimte via een privé-IP-adres en dat ze de juiste integratie van de persoonlijke DNS-zone hebben.

Maak eerst een virtuele machine aan de hand van de stappen in [een virtuele Windows-machine maken in de Azure Portal](../virtual-machines/windows/quick-create-portal.md)

Op het tabblad **netwerk** :

1. Geef het **virtuele netwerk** en het **subnet**op. U kunt een nieuw virtueel netwerk maken of een bestaande selecteren. Als u een bestaand item selecteert, moet u ervoor zorgen dat de regio overeenkomt.
1. Geef een **open bare IP-** resource op.
1. Selecteer in de **beveiligings groep NIC-netwerk**de optie **geen**.
1. Selecteer **Nee**in de **taak verdeling**.

Open de opdracht regel en voer de volgende opdracht uit:

```console
nslookup <your-event-hubs-namespace-name>.servicebus.windows.net
```

Als u de opdracht NS lookup uitvoert om het IP-adres van een Event Hubs naam ruimte via een openbaar eind punt op te lossen, ziet u een resultaat dat er als volgt uitziet:

```console
c:\ >nslookup <your-event-hubs-namespae-name>.servicebus.windows.net

Non-authoritative answer:
Name:    
Address:  (public IP address)
Aliases:  <your-event-hubs-namespace-name>.servicebus.windows.net
```

Als u de opdracht NS lookup uitvoert om het IP-adres van een Event Hubs naam ruimte op te lossen via een persoonlijk eind punt, ziet u een resultaat dat er als volgt uitziet:

```console
c:\ >nslookup your_event-hubs-namespace-name.servicebus.windows.net

Non-authoritative answer:
Name:    
Address:  10.1.0.5 (private IP address)
Aliases:  <your-event-hub-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>Beperkingen en ontwerp overwegingen

**Prijzen**: Zie [prijzen voor persoonlijke Azure-koppelingen](https://azure.microsoft.com/pricing/details/private-link/)voor prijs informatie.

**Beperkingen**: privé-eind punt voor Azure Event hubs is in open bare preview. Deze functie is beschikbaar in alle open bare Azure-regio's.

**Maximum aantal privé-eind punten per Event hubs naam ruimte**: 120.

Zie voor meer informatie [Azure Private Link service: beperkingen](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [persoonlijke Azure-koppelingen](../private-link/private-link-service-overview.md)
- Meer informatie over [Azure Event hubs](event-hubs-about.md)
