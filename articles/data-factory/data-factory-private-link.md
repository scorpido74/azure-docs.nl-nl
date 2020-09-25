---
title: Persoonlijke Azure-koppeling voor Azure Data Factory
description: Meer informatie over hoe Azure private link werkt in Azure Data Factory.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/01/2020
ms.openlocfilehash: 2cd9f01404a4e33303356dd3f452cd7dbc47a747
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91328561"
---
# <a name="azure-private-link-for-azure-data-factory"></a>Persoonlijke Azure-koppeling voor Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Met behulp van een persoonlijke Azure-koppeling kunt u via een persoonlijk eind punt verbinding maken met verschillende PaaS-implementaties (platform as a Service) in Azure. Een persoonlijk eind punt is een privé-IP-adres binnen een specifiek virtueel netwerk en subnet. Zie de documentatie van een [persoonlijke koppeling](https://docs.microsoft.com/azure/private-link/)voor een lijst met PaaS-implementaties die functionaliteit voor persoonlijke koppelingen ondersteunen. 

## <a name="secure-communication-between-customer-networks-and-azure-data-factory"></a>Beveiligde communicatie tussen klanten netwerken en Azure Data Factory 
U kunt een virtueel Azure-netwerk instellen als een logische weer gave van uw netwerk in de Cloud. Dit biedt de volgende voor delen:
* U helpt uw Azure-resources te beschermen tegen aanvallen in open bare netwerken.
* U kunt de netwerken en Data Factory veilig met elkaar communiceren. 

U kunt ook een on-premises netwerk verbinden met uw virtuele netwerk door Internet Protocol een VPN-verbinding (site-naar-site) of een Azure ExpressRoute-verbinding (privé-peering) in te stellen. 

U kunt ook een zelf-hostende Integration runtime installeren op een on-premises computer of een virtuele machine in het virtuele netwerk. Zo kunt u:
* Kopieer activiteiten uit te voeren tussen een gegevens archief in de Cloud en een gegevens archief in een particulier netwerk.
* Verzend transformatie activiteiten op basis van reken bronnen in een on-premises netwerk of een virtueel Azure-netwerk. 

Er zijn verschillende communicatie kanalen vereist tussen Azure Data Factory en het virtuele netwerk van de klant, zoals wordt weer gegeven in de volgende tabel:

| Domain | Poort | Beschrijving |
| ---------- | -------- | --------------- |
| `adf.azure.com` | 443 | Een besturings vlak dat vereist is voor Data Factory ontwerpen en bewaken. |
| `*.{region}.datafactory.azure.net` | 443 | Vereist door de zelf-hostende Integration Runtime om verbinding te maken met de Data Factory-service. |
| `*.servicebus.windows.net` | 443 | Vereist door de zelf-hostende Integration runtime voor interactief ontwerpen. |
| `download.microsoft.com` | 443 | Vereist door de zelf-hostende Integration Runtime voor het downloaden van de updates. |

Met de ondersteuning van een persoonlijke koppeling voor Azure Data Factory kunt u het volgende doen:
* Maak een persoonlijk eind punt in uw virtuele netwerk.
* De particuliere verbinding met een specifiek data factory exemplaar inschakelen. 

De communicatie met de Azure Data Factory-service gaat via een privé koppeling en biedt beveiligde persoonlijke connectiviteit. U hoeft het voor gaande domein en de poort in een virtueel netwerk of uw bedrijfs firewall niet te configureren om een veiligere manier te bieden om uw resources te beveiligen.  

![Diagram van een persoonlijke koppeling voor Azure Data Factory architectuur.](./media/data-factory-private-link/private-link-architecture.png)

Het inschakelen van de service voor persoonlijke koppelingen voor elk van de voor gaande communicatie kanalen biedt de volgende functionaliteit:
- **Ondersteund**:
   - U kunt de data factory in uw virtuele netwerk ontwerpen en bewaken, zelfs als u alle uitgaande communicatie blokkeert.
   - De opdracht communicatie tussen de zelf-hostende Integration runtime en de Azure Data Factory-service kan veilig worden uitgevoerd in een particuliere netwerk omgeving. Het verkeer tussen de zelf-hostende Integration runtime en de Azure Data Factory-service verloopt via een persoonlijke koppeling. 
- **Momenteel niet ondersteund**:
   - Interactief ontwerpen waarbij gebruik wordt gemaakt van een zelf-hostende Integration runtime, zoals de verbinding testen, bladeren in mappen lijst en tabel lijst, schema ophalen en gegevens bekijken, via een persoonlijke koppeling.
   - De nieuwe versie van de zelf-hostende Integration runtime kan automatisch worden gedownload van het micro soft Download centrum als u auto update inschakelt.

   > [!NOTE]
   > Voor functionaliteit die momenteel niet wordt ondersteund, moet u nog steeds het eerder genoemde domein en de poort in het virtuele netwerk of de firewall van uw bedrijf configureren. 

> [!WARNING]
> Wanneer u een gekoppelde service maakt, moet u ervoor zorgen dat uw referenties zijn opgeslagen in een Azure-sleutel kluis. Anders werken de referenties niet wanneer u een persoonlijke koppeling inschakelt in Azure Data Factory.

## <a name="set-up-private-link-for-azure-data-factory"></a>Persoonlijke koppeling instellen voor Azure Data Factory
U kunt persoonlijke eind punten maken met behulp van [de Azure Portal](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal), Power shell of de Azure cli.

U kunt ook naar uw Azure data factory gaan in de Azure Portal en een persoonlijk eind punt maken, zoals hier wordt weer gegeven:

![Scherm opname van het deel venster "persoonlijke eindpunt verbindingen" voor het maken van een persoonlijk eind punt.](./media/data-factory-private-link/create-private-endpoint.png)


Als u open bare toegang tot de Azure data factory wilt blok keren en alleen toegang via een persoonlijke koppeling wilt toestaan, schakelt u netwerk toegang tot Azure Data Factory in de Azure Portal uit, zoals hier wordt weer gegeven:

![Scherm opname van het deel venster ' netwerk toegang ' voor het maken van een persoonlijk eind punt.](./media/data-factory-private-link/disable-network-access.png)

> [!NOTE]
> Het uitschakelen van open bare netwerk toegang is alleen van toepassing op de zelf-hostende Integration runtime, niet op Azure Integration Runtime en SQL Server Integration Services (SSIS) Integration Runtime.

> [!NOTE]
> U hebt nog steeds toegang tot de Azure Data Factory portal via een openbaar netwerk nadat u open bare netwerk toegang hebt uitgeschakeld.

## <a name="next-steps"></a>Volgende stappen

- [Een data factory maken met behulp van de Azure Data Factory gebruikers interface](quickstart-create-data-factory-portal.md)
- [Inleiding tot Azure Data Factory](introduction.md)
- [Visueel ontwerpen in Azure Data Factory](author-visually.md)

