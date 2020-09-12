---
title: Beheerd virtueel netwerk & beheerde privé-eind punten
description: Meer informatie over beheerde virtuele netwerken en beheerde privé-eind punten in Azure Data Factory.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/15/2020
ms.openlocfilehash: 0553cb47ba0119cf1bc5e744b689b6c510ba8396
ms.sourcegitcommit: 1b320bc7863707a07e98644fbaed9faa0108da97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/09/2020
ms.locfileid: "89594353"
---
# <a name="azure-data-factory-managed-virtual-network-preview"></a>Beheerde Virtual Network Azure Data Factory (preview-versie)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel worden beheerde Virtual Network en beheerde privé-eind punten in Azure Data Factory uitgelegd.


## <a name="managed-virtual-network"></a>Beheerd virtueel netwerk

Wanneer u een Azure Integration Runtime (IR) in Azure Data Factory Managed Virtual Network (VNET) maakt, wordt de Integration runtime ingericht met de beheerde Virtual Network en worden privé-eind punten gebruikt om een veilige verbinding te maken met ondersteunde gegevens archieven. 

Het maken van een Azure IR binnen beheerde Virtual Network zorgt ervoor dat het gegevens integratie proces wordt geïsoleerd en beveiligd. 

Voor delen van het gebruik van beheerde Virtual Network:

- Met een beheerde Virtual Network, kunt u de last van het beheren van de Virtual Network naar Azure Data Factory verenigen. U hoeft geen subnet te maken voor Azure Integration Runtime die uiteindelijk veel privé Ip's van uw Virtual Network kunnen gebruiken en hiervoor eerdere planning van de netwerk infrastructuur nodig had. 
- Er is geen grondige kennis van Azure Networking nodig om gegevens integraties veilig uit te voeren. In plaats daarvan is het uitvoeren van beveiligde ETL veel vereenvoudigd voor gegevens technici. 
- Beheerde Virtual Network worden samen met beheerde persoonlijke eind punten beschermd tegen gegevens exfiltration. 

> [!IMPORTANT]
>Op dit moment wordt het beheerde VNet alleen ondersteund in dezelfde regio als Azure Data Factory regio.
 

![Virtual Network architectuur voor gebeheerde ADF](./media/managed-vnet/managed-vnet-architecture-diagram.png)

## <a name="managed-private-endpoints"></a>Beheerde privé-eindpunten

Beheerde privé-eind punten zijn particuliere eind punten die zijn gemaakt in de Azure Data Factory beheerde Virtual Network een persoonlijke koppeling naar Azure-resources tot stand brengen. Azure Data Factory deze privé-eind punten in uw naam beheert. 

![Nieuw Beheerd privé-eindpunt](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png)

Azure Data Factory ondersteunt persoonlijke koppelingen. Met persoonlijke koppeling kunt u Azure-Services (PaaS) (zoals Azure Storage, Azure Cosmos DB, Azure Synapse Analytics (voorheen Azure SQL Data Warehouse)) openen.

Wanneer u een persoonlijke koppeling gebruikt, loopt het verkeer tussen uw gegevens archieven en beheerde Virtual Network volledig over het micro soft backbone-netwerk. Private Link beschermt tegen exfiltratie van gegevens. U kunt een privé-koppeling naar een resource tot stand brengen door een privé-eindpunt te maken.

Persoonlijk eind punt maakt gebruik van een privé-IP-adres in de beheerde Virtual Network om de service effectief in te zetten. Privé-eindpunten worden toegewezen aan een specifieke resource in Azure, en niet de volledige service. Klanten kunnen de connectiviteit beperken tot een specifieke resource die is goedgekeurd door hun organisatie. Meer informatie over [privé-koppelingen en privé-eindpunten](https://docs.microsoft.com/azure/private-link/).

> [!NOTE]
> Het wordt aanbevolen dat u beheerde persoonlijke eind punten maakt om verbinding te maken met al uw Azure-gegevens bronnen. 
 
> [!WARNING]
> Als er voor een PaaS-gegevens archief (BLOB, ADLS Gen2, Azure Synapse Analytics) al een persoonlijk eind punt is gemaakt, en zelfs als deze toegang toestaat vanuit alle netwerken, zou ADF alleen toegang kunnen krijgen met een beheerd privé-eind punt. Zorg ervoor dat u in dergelijke scenario's een persoonlijk eind punt maakt. 

Een VPN-verbinding wordt gemaakt met de status ' in behandeling ' wanneer u een beheerd privé-eind punt maakt in Azure Data Factory. Er wordt een goedkeuringswerkstroom geïnitieerd. De eigenaar van de privékoppelingsresource is verantwoordelijk voor het goedkeuren of afwijzen van de verbinding.

![Privé-eindpunt beheren](./media/tutorial-copy-data-portal-private/manage-private-endpoint.png)

Als de eigenaar de verbinding goedkeurt, wordt de privé-koppeling tot stand gebracht. Anders wordt de privé-koppeling niet tot stand gebracht. In beide gevallen wordt het beheerde privé-eindpunt bijgewerkt met de status van de verbinding.

![Beheerd privé-eind punt goed keuren](./media/tutorial-copy-data-portal-private/approve-private-endpoint.png)

Alleen een beheerd privé-eindpunt met een goedgekeurde status kan verkeer verzenden naar een gegeven privé-koppelingsresource.

## <a name="limitations-and-known-issues"></a>Beperkingen en bekende problemen
### <a name="supported-data-sources"></a>Ondersteunde gegevensbronnen
De onderstaande gegevens bronnen worden ondersteund om verbinding te maken via een persoonlijke koppeling vanuit de Virtual Network van ADF die wordt beheerd.
- Azure Blob Storage
- Azure Table Storage
- Azure Files
- Azure Data Lake Gen2
- Azure SQL Database (exclusief Azure SQL Managed instance)
- Azure Synapse Analytics (voorheen Azure SQL Data Warehouse)
- Azure CosmosDB SQL
- Azure Key Vault
- Persoonlijke koppelings service van Azure
- Azure Search
- Azure Database for MySQL
- Azure Database for PostgreSQL
- Azure Database for MariaDB

### <a name="supported-regions"></a>Ondersteunde regio's
- VS - oost
- VS - oost 2
- VS - west-centraal
- West US 2
- South Central US
- Central US
- Europa - noord
- Europa -west
- Verenigd Koninkrijk Zuid
- Azië - zuidoost
- Australië - oost

### <a name="outbound-communications-through-public-endpoint-from-adf-managed-virtual-network"></a>Uitgaande communicatie via het open bare eind punt van de Virtual Network voor ADF beheerd
- Alleen poort 443 wordt geopend voor uitgaande communicatie.
- Azure Storage en Azure Data Lake Gen2 worden niet ondersteund om te worden verbonden via een openbaar eind punt van Virtual Network met ADF-beheer.

## <a name="next-steps"></a>Volgende stappen

- Zelf studie: [een Kopieer pijplijn bouwen met beheerde Virtual Network en privé-eind punten](tutorial-copy-data-portal-private.md) 
- Zelf studie: een [pijp lijn voor toewijzings gegevensstroom maken met beheerde Virtual Network en privé-eind punten](tutorial-data-flow-private.md)
