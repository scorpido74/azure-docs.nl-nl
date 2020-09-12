---
title: Persoonlijke Azure-koppeling voor Azure Data Factory
description: Meer informatie over persoonlijke Azure-koppelingen werkt in Azure Data Factory.
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
ms.openlocfilehash: 164a1005d9382711874b644e14b23d2154d613a0
ms.sourcegitcommit: 1b320bc7863707a07e98644fbaed9faa0108da97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/09/2020
ms.locfileid: "89596019"
---
# <a name="azure-private-link-for-azure-data-factory"></a>Persoonlijke Azure-koppeling voor Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Met een Private Link kunt u via een privé-eindpunt verbinding maken met verschillende PaaS-services in Azure. Ga naar de [documentatie pagina van de persoonlijke koppeling](https://docs.microsoft.com/azure/private-link/)voor een lijst met PaaS-services die functionaliteit voor persoonlijke koppelingen ondersteunen. Een persoonlijk eind punt is een privé-IP-adres binnen een specifiek virtueel netwerk en subnet.

## <a name="secure-communication-between-customer-network-and-azure-data-factory-service"></a>Beveiligde communicatie tussen het netwerk van de klant en de Azure Data Factory Service
Als u uw Azure-resources wilt beveiligen tegen aanvallen in een openbaar netwerk of als u ze veilig wilt laten communiceren met elkaar, kunt u een Azure-Virtual Network instellen als een logische weer gave van uw netwerk in de Cloud. U kunt ook een on-premises netwerk verbinden met uw virtuele netwerk door IPSec VPN (site-naar-site) of ExpressRoute (privé-peering) in te stellen. Het zelf-hostende Integration Runtime kan worden geïnstalleerd op een on-premises machine of virtuele machine in Virtual Network om Kopieer activiteiten uit te voeren tussen een gegevens archief in de Cloud en een gegevens archief in een particulier netwerk of trans formatie-activiteiten met reken bronnen in een on-premises netwerk of een virtueel Azure-netwerk. 

Er zijn verschillende communicatie kanalen vereist tussen Data Factory en het virtuele netwerk van de klant.


| **Domein** | **Poort** | **Beschrijving** |
| ---------- | -------- | --------------- |
| `adf.azure.com` | 443 | Besturings vlak. Vereist door Data Factory ontwerpen en bewaken. |
| `*.{region}.datafactory.azure.net` | 443 | Vereist door de zelf-hostende Integration Runtime om verbinding te maken met de Data Factory-service. |
| `*.servicebus.windows.net` | 443 | Vereist door de zelf-hostende Integration Runtime voor interactief ontwerpen. |
| `download.microsoft.com` | 443 | Vereist door de zelf-hostende Integration Runtime voor het downloaden van de updates. |


Met de ondersteuning van een persoonlijke Azure-koppeling voor Azure Data Factory kunt u een persoonlijk eind punt (PE) in uw virtuele netwerk maken en de particuliere verbinding met specifieke Azure Data Factory inschakelen. De communicatie met de Azure Data Factory-service gaat via een persoonlijke Azure-koppeling die beveiligde persoonlijke connectiviteit biedt. En u hoeft niet boven domein en poort in het virtuele netwerk of uw bedrijfs firewall te configureren die een veiligere manier bieden om uw resources te beveiligen.  

![Architectuur van de persoonlijke koppeling Azure Data Factory](./media/data-factory-private-link/private-link-architecture.png)

Hier volgen de voor delen voor het inschakelen van de service voor persoonlijke koppelingen voor elk van de hierboven vermelde communicatie kanalen:
- Geboden U kunt het ontwerpen en bewaken van Azure Data Factory in uw virtuele netwerk doen, zelfs als u alle uitgaande communicatie blokkeert.
- Geboden De opdracht communicatie tussen zelf-hostende Integration Runtime en Azure Data Factory-service kan veilig worden uitgevoerd in een particuliere netwerk omgeving. Het verkeer tussen de zelf-hostende Integration Runtime en de Azure Data Factory-service loopt via een persoonlijke koppeling. 
- (Momenteel niet ondersteund) Interactief ontwerpen met behulp van zelf-hostende Integration Runtime via een privé koppeling, zoals een test verbinding, bladeren in mappen lijst en tabel lijst, schema ophalen en voorbeeld gegevens bekijken.
- (Momenteel niet ondersteund) De nieuwe versie van zelf-hostende Integration Runtime kan automatisch worden gedownload uit het Download centrum als u automatisch bijwerken inschakelt.

> [!NOTE]
> Voor functionaliteit die momenteel niet wordt ondersteund, moet u het domein en de poort in het virtuele netwerk of de firewall van uw bedrijf nog steeds configureren. 

> [!WARNING]
> Wanneer u een gekoppelde service maakt, moet u ervoor zorgen dat de referentie is opgeslagen in Azure Key Vault. Anders werkt het niet wanneer u de service voor persoonlijke koppelingen inschakelt in Azure Data Factory.

## <a name="how-to-set-up-private-link-for-azure-data-factory"></a>Privé-koppeling instellen voor Azure Data Factory
Privé-eindpunten kunnen worden gemaakt met behulp van de Azure-portal, PowerShell of de Azure CLI:

[Portal](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal)


U kunt ook naar uw Azure Data Factory navigeren in Azure Portal en een persoonlijk eind punt maken (PE):

![Privé-eindpunt maken](./media/data-factory-private-link/create-private-endpoint.png)


Als u open bare toegang tot deze Azure Data Factory wilt blok keren en alleen toegang via een persoonlijke koppeling wilt toestaan, kunt u de netwerk toegang van Azure Data Factory in Azure Portal uitschakelen:

![Privé-eindpunt maken](./media/data-factory-private-link/disable-network-access.png)

> [!NOTE]
> Het uitschakelen van open bare netwerk toegang is alleen van toepassing op zelf-hostende Integration Runtime, niet op Azure Integration Runtime en SSIS Integration Runtime.

> [!NOTE]
> Gebruikers hebben nog steeds toegang tot Azure Data Factory portal via een openbaar netwerk nadat ze de toegang tot het open bare netwerk hebben uitgeschakeld.

## <a name="next-steps"></a>Volgende stappen

- [Een data factory maken met behulp van de Azure Data Factory gebruikers interface](quickstart-create-data-factory-portal.md)

- [Inleiding tot Azure Data Factory](introduction.md)

- [Visueel ontwerpen in Azure Data Factory](author-visually.md)

