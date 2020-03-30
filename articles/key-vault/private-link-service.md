---
title: Integreren met Azure Private Link Service
description: Meer informatie over het integreren van Azure Key Vault met Azure Private Link Service
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 03/08/2020
ms.service: key-vault
ms.topic: quickstart
ms.openlocfilehash: c24be648e4ca1433c7c2af3d659bf4520a7a188c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79457284"
---
# <a name="integrate-key-vault-with-azure-private-link"></a>Key Vault integreren met Azure Private Link

Azure Private Link Service stelt u in staat om toegang te krijgen tot Azure Services (bijvoorbeeld Azure Key Vault, Azure Storage en Azure Cosmos DB) en Azure gehoste klant/partnerservices via een privéeindpunt in uw virtuele netwerk.

Een Azure Private Endpoint is een netwerkinterface die u privé en veilig verbindt met een service die wordt aangedreven door Azure Private Link. Het privéeindpunt maakt gebruik van een privé-IP-adres van uw VNet, waardoor de service effectief in uw VNet wordt opgenomen. Al het verkeer naar de service kan worden doorgestuurd via het privéeindpunt, zodat er geen gateways, NAT-apparaten, ExpressRoute- of VPN-verbindingen of openbare IP-adressen nodig zijn. Verkeer tussen uw virtuele netwerk en de services wordt via het backbonenetwerk van Microsoft geleid, waarmee de risico's van het openbare internet worden vermeden. U verbinding maken met een instantie van een Azure-bron, zodat u het hoogste niveau van granulariteit in toegangscontrole hebt.

Zie Wat is Azure Private Link voor meer [informatie?](../private-link/private-link-overview.md)

## <a name="prerequisites"></a>Vereisten

Als u een sleutelkluis wilt integreren met Azure Private Link, hebt u het volgende nodig:

- Een sleutelkluis.
- Een virtueel Azure-netwerk.
- Een subnet in het virtuele netwerk.
- Machtigingen voor eigenaren of bijdragen voor zowel de sleutelkluis als het virtuele netwerk.

Uw privéeindpunt en virtueel netwerk moeten zich in dezelfde regio bevinden. Wanneer u een regio voor het privéeindpunt selecteert met behulp van de portal, filtert het automatisch alleen virtuele netwerken die zich in die regio bevinden. Uw sleutelkluis kan zich in een andere regio bevinden.

Uw privéeindpunt gebruikt een privé-IP-adres in uw virtuele netwerk.

## <a name="establish-a-private-link-connection-to-key-vault-using-the-azure-portal"></a>Een privékoppelingsverbinding met Key Vault tot stand brengen met de Azure-portal 

Maak eerst een virtueel netwerk door de stappen te volgen in [Een virtueel netwerk maken met de Azure-portal](../virtual-network/quick-create-portal.md)

U vervolgens een nieuwe sleutelkluis maken of een privékoppelingsverbinding tot stand brengen met een bestaande sleutelkluis.

### <a name="create-a-new-key-vault-and-establish-a-private-link-connection"></a>Een nieuwe sleutelkluis maken en een privékoppelingsverbinding tot stand brengen

U een nieuwe sleutelkluis maken door de stappen in Set te volgen [en een geheim op te halen uit Azure Key Vault met behulp van de Azure-portal](quick-create-portal.md)

Nadat u de basisbeginselen van de toetskluis hebt geconfigureerd, selecteert u het tabblad Netwerken en volgt u de volgende stappen:

1. Selecteer de knop Privé-eindpuntopkeuzeop het tabblad Netwerken.
1. Klik op de knop '+ toevoegen' om een privéeindpunt toe te voegen.

    ![Installatiekopie](./media/private-link-service-1.png)
 
1. Selecteer in het veld 'Locatie' van het privéeindpuntblad maken de regio waarin uw virtuele netwerk zich bevindt. 
1. Maak in het veld 'Naam' een beschrijvende naam waarmee u dit privéeindpunt identificeren. 
1. Selecteer het virtuele netwerk en subnet waarin u dit privéeindpunt wilt maken in het vervolgkeuzemenu. 
1. Laat de optie "integreren met de private zone DNS" ongewijzigd.  
1. Selecteer 'Ok'.

    ![Installatiekopie](./media/private-link-service-2.png)
 
U nu het geconfigureerde privéeindpunt zien. U hebt nu de optie om dit privéeindpunt te verwijderen en te bewerken. Selecteer de knop 'Controleren + maken' en maak de sleutelkluis. Het duurt 5-10 minuten voordat de implementatie is voltooid. 

### <a name="establish-a-private-link-connection-to-an-existing-key-vault"></a>Een privékoppelingsverbinding met een bestaande sleutelkluis tot stand brengen

Als u al een sleutelkluis hebt, u een verbinding met een privékoppeling maken door de volgende stappen te volgen:

1. Meld u aan bij Azure Portal. 
1. Typ in de zoekbalk 'sleutelkluizen' in
1. Selecteer de sleutelkluis in de lijst waaraan u een privéeindpunt wilt toevoegen.
1. Selecteer het tabblad 'Netwerken' onder Instellingen
1. Het tabblad Privéeindpuntverbindingen boven aan de pagina selecteren
1. Selecteer de knop '+ Privéeindpunt' boven aan de pagina.

    ![Afbeelding](./media/private-link-service-3.png) ![](./media/private-link-service-4.png)

U ervoor kiezen om een privéeindpunt te maken voor elke Azure-bron die dit blad gebruikt. U de vervolgkeuzemenu's gebruiken om een resourcetype te selecteren en een resource in uw map selecteren, of u verbinding maken met een Azure-bron met behulp van een bron-id. Laat de optie "integreren met de private zone DNS" ongewijzigd.  

![Afbeelding](./media/private-link-service-3.png)
![](./media/private-link-service-4.png)

## <a name="establish-a-private-link-connection-to-key-vault-using-cli"></a>Een privékoppelingsverbinding met Key Vault tot stand brengen met CLI

### <a name="login-to-azure-cli"></a>Inloggen bij Azure CLI
```console
az login 
```
### <a name="select-your-azure-subscription"></a>Selecteer uw Azure-abonnement 
```console
az account set --subscription {AZURE SUBSCRIPTION ID}
```
### <a name="create-a-new-resource-group"></a>Een nieuwe resourcegroep maken 
```console
az group create -n {RG} -l {AZURE REGION}
```
### <a name="register-microsoftkeyvault-as-a-provider"></a>Microsoft.KeyVault registreren als provider 
```console
az provider register -n Microsoft.KeyVault
```
### <a name="create-a-new-key-vault"></a>Een nieuwe Key Vault maken
```console
az keyvault create --name {KEY VAULT NAME} --resource-group {RG} --location {AZURE REGION}
```
### <a name="turn-on-key-vault-firewall"></a>Key Vault Firewall inschakelen
```console
az keyvault update --name {KEY VAULT NAME} --resource-group {RG} --location {AZURE REGION} --default-action deny
```
### <a name="create-a-virtual-network"></a>Een virtueel netwerk maken
```console
az network vnet create --resource-group {RG} --name {vNet NAME} --location {AZURE REGION}
```
### <a name="add-a-subnet"></a>Een subnet toevoegen
```console
az network vnet subnet create --resource-group {RG} --vnet-name {vNet NAME} --name {subnet NAME} --address-prefixes {addressPrefix}
```
### <a name="disable-virtual-network-policies"></a>Beleid voor virtuele netwerken uitschakelen 
```console
az network vnet subnet update --name {subnet NAME} --resource-group {RG} --vnet-name {vNet NAME} --disable-private-endpoint-network-policies true
```
### <a name="add-a-private-dns-zone"></a>Een privé-DNS-zone toevoegen 
```console
az network private-dns zone create --resource-group {RG} --name privatelink.vaultcore.azure.net
```
### <a name="link-private-dns-zone-to-virtual-network"></a>Private DNS-zone koppelen aan virtueel netwerk 
```console
az network private-dns link vnet create --resoruce-group {RG} --virtual-network {vNet NAME} --zone-name privatelink.vaultcore.azure.net --name {dnsZoneLinkName} --registration-enabled true
```
### <a name="create-a-private-endpoint-automatically-approve"></a>Een privéeindpunt maken (automatisch goedkeuren) 
```console
az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.KeyVault/vaults/ {KEY VAULT NAME}" --group-ids vault --connection-name {Private Link Connection Name} --location {AZURE REGION}
```
### <a name="create-a-private-endpoint-manually-request-approval"></a>Een privéeindpunt maken (handmatig goedkeuring aanvragen) 
```console
az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.KeyVault/vaults/ {KEY VAULT NAME}" --group-ids vault --connection-name {Private Link Connection Name} --location {AZURE REGION} --manual-request
```
### <a name="show-connection-status"></a>Verbindingsstatus weergeven 
```console
az network private-endpoint show --resource-group {RG} --name {Private Endpoint Name}
```
## <a name="manage-private-link-connection"></a>Verbinding met privékoppelingen beheren

Wanneer u een privéeindpunt maakt, moet de verbinding worden goedgekeurd. Als de bron waarvoor u een privéeindpunt maakt, zich in uw map bevindt, u de verbindingsaanvraag goedkeuren, mits u over voldoende machtigingen beschikt. Als u verbinding maakt met een Azure-bron in een andere map, moet u wachten tot de eigenaar van die bron uw verbindingsaanvraag goedkeurt.

Er zijn vier provisioning staten:

| Service bieden actie | Particuliere eindpuntstaat voor serviceconsumenten | Beschrijving |
|--|--|--|
| Geen | In behandeling | De verbinding wordt handmatig gemaakt en wordt in afwachting van goedkeuring van de eigenaar van de Private Link-bron. |
| Goedkeuren | Goedgekeurd | De verbinding is automatisch of handmatig goedgekeurd en is klaar om te worden gebruikt. |
| Afwijzen | Geweigerd | Verbinding is geweigerd door de eigenaar van de private link resource. |
| Verwijderen | De verbinding verbroken | Verbinding is verwijderd door de eigenaar van de privékoppelingsbron, het privéeindpunt wordt informatief en moet worden verwijderd voor het opschonen. |
 
###  <a name="how-to-manage-a-private-endpoint-connection-to-key-vault-using-the-azure-portal"></a>Een privé-eindpuntverbinding met Key Vault beheren met de Azure-portal 

1. Meld u aan bij Azure Portal.
1. Typ in de zoekbalk 'sleutelkluizen' in
1. Selecteer de sleutelkluis die u wilt beheren.
1. Selecteer het tabblad Netwerken.
1. Als er verbindingen in behandeling zijn, wordt er een verbinding weergegeven met 'In behandeling' in de inrichtingsstatus. 
1. Selecteer het privéeindpunt dat u wilt goedkeuren
1. Selecteer de knop Goedkeuren.
1. Als er privéeindpuntverbindingen zijn die u wilt weigeren, selecteert u, of het nu gaat om een aanvraag in behandeling of een bestaande verbinding, de verbinding en klikt u op de knop Weigeren.

    ![Installatiekopie](./media/private-link-service-7.png)

##  <a name="how-to-manage-a-private-endpoint-connection-to-key-vault-using-azure-cli"></a>Een privé-eindpuntverbinding met Key Vault beheren met Azure CLI

### <a name="approve-a-private-link-connection-request"></a>Een verbindingsaanvraag voor privékoppelingen goedkeuren
```console
az keyvault private-endpoint-connection approve --approval-description {"OPTIONAL DESCRIPTION"} --resource-group {RG} --vault-name {KEY VAULT NAME} –name {PRIVATE LINK CONNECTION NAME}
```

### <a name="deny-a-private-link-connection-request"></a>Een verbindingsaanvraag voor privékoppelingen weigeren
```console
az keyvault private-endpoint-connection reject --rejection-description {"OPTIONAL DESCRIPTION"} --resource-group {RG} --vault-name {KEY VAULT NAME} –name {PRIVATE LINK CONNECTION NAME}
```

### <a name="delete-a-private-link-connection-request"></a>Een verbindingsaanvraag voor privékoppelingen verwijderen
```console
az keyvault private-endpoint-connection delete --resource-group {RG} --vault-name {KEY VAULT NAME} --name {PRIVATE LINK CONNECTION NAME}
```

## <a name="validate-that-the-private-link-connection-works"></a>Valideren of de privékoppelingsverbinding werkt

U moet valideren dat de bronnen binnen hetzelfde subnet van de privé-eindpuntbron verbinding maken met uw sleutelkluis via een privé-IP-adres en dat ze de juiste integratie van de privé-DNS-zone hebben.

Maak eerst een virtuele machine door de stappen te volgen in [Een virtuele Windows-machine maken in de Azure-portal](../virtual-machines/windows/quick-create-portal.md)

Ga als op het tabblad 'Netwerken':

1. Geef virtueel netwerk en subnet op. U een nieuw virtueel netwerk maken of een bestaand netwerk selecteren. Als u een bestaande regio selecteert, moet u ervoor zorgen dat de regio overeenkomt.
1. Geef een openbare IP-bron op.
1. Selecteer 'Geen' in de groep 'NIC-netwerkbeveiliging'.
1. Selecteer in de taakverdeling 'Nee' de optie 'Nee'.

Open de opdrachtregel en voer de volgende opdracht uit:

```console
nslookup <your-key-vault-name>.vault.azure.net
```

Als u de opdracht ns-opzoeking uitvoert om het IP-adres van een sleutelkluis op een openbaar eindpunt op te lossen, ziet u een resultaat dat er als volgt uitziet:

```console
c:\ >nslookup <your-key-vault-name>.vault.azure.net

Non-authoritative answer:
Name:    
Address:  (public IP address)
Aliases:  <your-key-vault-name>.vault.azure.net
```

Als u de opdracht ns-opzoeking uitvoert om het IP-adres van een sleutelkluis via een privéeindpunt op te lossen, ziet u een resultaat dat er als volgt uitziet:

```console
c:\ >nslookup your_vault_name.vault.azure.net

Non-authoritative answer:
Name:    
Address:  10.1.0.5 (private IP address)
Aliases:  <your-key-vault-name>.vault.azure.net
          <your-key-vault-name>.privatelink.vaultcore.azure.net
```

## <a name="limitations-and-design-considerations"></a>Beperkingen en ontwerpoverwegingen

**Prijzen:** Zie Azure [Private Link-prijzen](https://azure.microsoft.com/pricing/details/private-link/)voor prijsinformatie.

**Beperkingen**: Privéeindpunt voor Azure Key Vault is alleen beschikbaar in openbare Azure-regio's.

**Maximum aantal privéeindpunten per key vault**: 64.

**Maximaal aantal key vaults met privéeindpunten per abonnement:** 64.

Zie Azure [Private Link-service: Beperkingen voor](../private-link/private-link-service-overview.md#limitations) meer informatie

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure Private Link](../private-link/private-link-service-overview.md)
- Meer informatie over [Azure Key Vault](key-vault-overview.md)
