---
title: Integreren met Azure Private Link service
description: Meer informatie over het integreren van Azure Key Vault met Azure Private Link service
author: msmbaldwin
ms.author: mbaldwin
ms.date: 01/28/2020
ms.service: key-vault
ms.topic: quickstart
ms.openlocfilehash: e058e643f4c37336f09b43c41cd09aa361a23d15
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76907073"
---
# <a name="integrate-key-vault-with-azure-private-link-preview"></a>Key Vault integreren met een persoonlijke Azure-koppeling (preview-versie)

Met Azure Private Link service kunt u toegang krijgen tot Azure-Services (bijvoorbeeld Azure Key Vault, Azure Storage en Azure Cosmos DB) en Azure hostende klanten/partner services via een persoonlijk eind punt in uw virtuele netwerk.

Een persoonlijk Azure-eind punt is een netwerk interface waarmee u privé en veilig een service kunt verbinden met een persoonlijke Azure-koppeling. Het persoonlijke eind punt maakt gebruik van een privé-IP-adres uit uw VNet, waardoor de service effectief in uw VNet wordt gezet. Al het verkeer naar de service kan via het privé-eindpunt worden gerouteerd, waardoor geen gateways, NAT-apparaten, ExpressRoute- of VPN-verbindingen of openbare IP-adressen nodig zijn. Verkeer tussen uw virtuele netwerk en de services wordt via het backbonenetwerk van Microsoft geleid, waarmee de risico's van het openbare internet worden vermeden. U kunt verbinding maken met een exemplaar van een Azure-resource, zodat u het hoogste granulatie niveau krijgt in toegangs beheer.

Zie [Wat is Azure private link (preview)?](../private-link/private-link-overview.md) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

Als u een sleutel kluis met persoonlijke Azure-koppeling (preview-versie) wilt integreren, hebt u het volgende nodig:

- Een sleutel kluis.
- Een virtueel Azure-netwerk.
- Een subnet in het virtuele netwerk.
- Eigenaar-of Inzender machtigingen voor zowel de sleutel kluis als het virtuele netwerk.

Uw persoonlijke eind punt en het virtuele netwerk moeten zich in dezelfde regio bevinden. Wanneer u een regio voor het persoonlijke eind punt selecteert met behulp van de portal, worden er automatisch alleen virtuele netwerken gefilterd die zich in die regio bevinden. Uw sleutel kluis kan zich in een andere regio bevinden.

Uw persoonlijke eind punt maakt gebruik van een privé-IP-adres in uw virtuele netwerk.

## <a name="establish-a-private-link-connection-to-key-vault"></a>Een koppeling met een particuliere verbinding met de sleutel kluis tot stand brengen

Maak eerst een virtueel netwerk door de stappen in [een virtueel netwerk maken te volgen met behulp van de Azure Portal](../virtual-network/quick-create-portal.md)

U kunt vervolgens een nieuwe sleutel kluis maken of een koppeling met een particuliere verbinding tot stand brengen met een bestaande sleutel kluis.

### <a name="create-a-new-key-vault-and-establish-a-private-link-connection"></a>Een nieuwe sleutel kluis maken en een koppeling met een particuliere verbinding tot stand brengen

U kunt een nieuwe sleutel kluis maken door de stappen te volgen in [set en een geheim op te halen uit Azure Key Vault met behulp van de Azure Portal](quick-create-portal.md)

Nadat u de basis beginselen van de sleutel kluis hebt geconfigureerd, selecteert u het tabblad netwerken en voert u de volgende stappen uit:

1. Selecteer het keuze rondje persoonlijk eind punt (preview) op het tabblad netwerken.
1. Klik op de knop + toevoegen om een persoonlijk eind punt toe te voegen.

    ![afbeelding](./media/private-link-service-1.png)
 
1. Selecteer in het veld Locatie van de Blade persoonlijk eind punt maken de regio waarin het virtuele netwerk zich bevindt. 
1. Maak in het veld naam een beschrijvende naam waarmee dit persoonlijke eind punt kan worden geïdentificeerd. 
1. Selecteer het virtuele netwerk en het subnet waarvoor u dit persoonlijke eind punt wilt maken in de vervolg keuzelijst. 
1. Laat de optie ' integreren met de zone voor de privécloud ' ongewijzigd.  
1. Selecteer OK.

    ![afbeelding](./media/private-link-service-2.png)
 
U kunt nu het geconfigureerde persoonlijke eind punt zien. U hebt nu de mogelijkheid om dit privé-eind punt te verwijderen en te bewerken. Selecteer de knop bekijken + maken en maak de sleutel kluis. Het duurt 5-10 minuten voordat de implementatie is voltooid. 

### <a name="establish-a-private-link-connection-to-an-existing-key-vault"></a>Verbinding maken met een bestaande sleutel kluis met een persoonlijke koppeling

Als u al een sleutel kluis hebt, kunt u een koppeling voor een particuliere verbinding maken door de volgende stappen uit te voeren:

1. Meld u aan bij Azure Portal. 
1. Typ ' sleutel kluizen ' in de zoek balk.
1. Selecteer de sleutel kluis in de lijst waaraan u een persoonlijk eind punt wilt toevoegen.
1. Selecteer het tabblad netwerken onder instellingen
1. Klik boven aan de pagina op het tabblad private endpoint Connections (preview)
1. Selecteer de knop ' + persoonlijk eind punt ' boven aan de pagina.

    ![installatie kopie](./media/private-link-service-3.png) ![installatie kopie](./media/private-link-service-4.png)

U kunt kiezen voor het maken van een persoonlijk eind punt voor elke Azure-resource in het gebruik van deze Blade. U kunt de vervolg keuzemenu's gebruiken om een resource type te selecteren en een resource in uw directory te selecteren, of u kunt verbinding maken met elke Azure-resource met een resource-ID. Laat de optie ' integreren met de zone voor de privécloud ' ongewijzigd.  

![installatie kopie](./media/private-link-service-3.png)
![installatie kopie](./media/private-link-service-4.png)

## <a name="manage-private-link-connection"></a>Verbinding met persoonlijke koppeling beheren

Wanneer u een persoonlijk eind punt maakt, moet de verbinding worden goedgekeurd. Als de resource waarvoor u een persoonlijk eind punt maakt zich in uw directory bevindt, kunt u de verbindings aanvraag goed keuren die u voldoende machtigingen hebt. Als u verbinding maakt met een Azure-resource in een andere Directory, moet u wachten tot de eigenaar van die bron uw verbindings aanvraag heeft goedgekeurd.

Er zijn vier inrichtings provincies:

| Actie voor service leveren | Status privé-eind punt service gebruiker | Beschrijving |
|--|--|--|
| Geen | In behandeling | De verbinding wordt hand matig gemaakt en in afwachting van goed keuring van de resource-eigenaar van de persoonlijke koppeling. |
| Goedkeuren | Goedgekeurd | De verbinding is automatisch of hand matig goedgekeurd en is klaar om te worden gebruikt. |
| Afwijzen | Afgekeurd | De verbinding is geweigerd door de resource-eigenaar van de persoonlijke koppeling. |
| Verwijderen | De verbinding verbroken | De verbinding is verwijderd door de resource-eigenaar van de persoonlijke koppeling, het persoonlijke eind punt wordt informatieve en moet worden verwijderd voor opschoning. |
 
###  <a name="how-to-manage-a-private-endpoint-connection-to-key-vault"></a>Een particuliere endpoint-verbinding met de sleutel kluis beheren

1. Meld u aan bij Azure Portal.
1. Typ ' sleutel kluizen ' in de zoek balk.
1. Selecteer de sleutel kluis die u wilt beheren.
1. Selecteer het tabblad netwerken.
1. Als er verbindingen zijn die in behandeling zijn, ziet u een verbinding die wordt weer gegeven met de status in behandeling in de inrichting. 
1. Selecteer het privé-eind punt dat u wilt goed keuren
1. Selecteer de knop goed keuren.
1. Als er particuliere endpoint-verbindingen zijn die u wilt weigeren, ongeacht of het een aanvraag in behandeling of een bestaande verbinding is, selecteert u de verbinding en klikt u op de knop afwijzen.

    ![afbeelding](./media/private-link-service-7.png)

## <a name="validate-that-the-private-link-connection-works"></a>Controleren of de verbinding van de persoonlijke verbinding werkt

U moet controleren of de resources binnen hetzelfde subnet van de persoonlijke eindpunt resource verbinding maken met uw sleutel kluis via een privé-IP-adres en dat ze de juiste integratie van de persoonlijke DNS-zone hebben.

Maak eerst een virtuele machine aan de hand van de stappen in [een virtuele Windows-machine maken in de Azure Portal](../virtual-machines/windows/quick-create-portal.md)

Op het tabblad netwerk:

1. Geef het virtuele netwerk en het subnet op. U kunt een nieuw virtueel netwerk maken of een bestaande selecteren. Als u een bestaand item selecteert, moet u ervoor zorgen dat de regio overeenkomt.
1. Geef een open bare IP-resource op.
1. Selecteer ' geen ' in de beveiligings groep NIC-netwerk.
1. Selecteer Nee in de taak verdeling.

Open de opdracht regel en voer de volgende opdracht uit:

```console
nslookup <your-key-vault-name>.vault.azure.net
```

Als u de opdracht NS lookup uitvoert om het IP-adres van een sleutel kluis via een openbaar eind punt op te lossen, ziet u een resultaat dat er als volgt uitziet:

```console
c:\ >nslookup <your-key-vault-name>.vault.azure.net

Non-authoritative answer:
Name:    
Address:  (public IP address)
Aliases:  <your-key-vault-name>.vault.azure.net
```

Als u de opdracht NS lookup uitvoert om het IP-adres van een sleutel kluis via een persoonlijk eind punt op te lossen, ziet u een resultaat dat er als volgt uitziet:

```console
c:\ >nslookup your_vault_name.vault.azure.net

Non-authoritative answer:
Name:    
Address:  10.1.0.5 (private IP address)
Aliases:  <your-key-vault-name>.vault.azure.net
          <your-key-vault-name>.privatelink.vaultcore.azure.net
```

## <a name="limitations-and-design-considerations"></a>Beperkingen en ontwerp overwegingen

**Prijzen**: Zie [prijzen voor Azure private link (preview)](https://azure.microsoft.com/pricing/details/private-link/)voor informatie over prijzen.

**Beperkingen**: privé-eind punt voor Azure Key Vault bevindt zich in de open bare preview. Deze functie is beschikbaar in alle open bare Azure-regio's.

**Maximum aantal privé-eind punten per Key Vault**: 64.

**Maximum aantal sleutel kluizen met persoonlijke eind punten per abonnement**: 64.

Zie voor meer informatie [Azure Private Link service: beperkingen](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [persoonlijke Azure-koppelingen (preview-versie)](../private-link/private-link-service-overview.md)
- Meer informatie over [Azure Key Vault](key-vault-overview.md)
