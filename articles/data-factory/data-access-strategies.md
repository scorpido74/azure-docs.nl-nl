---
title: Strategieën voor gegevenstoegang
description: Azure Data Factory ondersteunt nu vaste IP-adresbereiken.
services: data-factory
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/28/2020
ms.openlocfilehash: 0b966b10c5bbc7bb90a4226d94dda8b75e25c3af
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84247475"
---
# <a name="data-access-strategies"></a>Strategieën voor gegevenstoegang

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Een cruciaal beveiligings doel van een organisatie is het beveiligen van hun gegevens archieven via internet, maar dit is mogelijk een on-premises of een Cloud/SaaS-gegevens archief. 

Normaal gesp roken beheert een gegevens archief in de Cloud de toegang met behulp van de onderstaande mechanismen:
* Firewall regels die de connectiviteit beperken op basis van het IP-adres
* Verificatie mechanismen waarvoor gebruikers hun identiteit moeten bewijzen
* Autorisatie mechanismen waarmee gebruikers worden beperkt tot specifieke acties en gegevens

> [!TIP]
> Met de [introductie van een statisch IP-adres bereik](https://docs.microsoft.com/azure/data-factory/azure-integration-runtime-ip-addresses)kunt u nu IP-adresbereiken voor de specifieke Azure Integration runtime-regio toestaan om ervoor te zorgen dat u niet alle Azure IP-adressen in uw gegevens archieven in de cloud kunt toestaan. Op deze manier kunt u de IP-adressen beperken die zijn toegestaan voor toegang tot de gegevens archieven.

> [!NOTE] 
> De IP-adresbereiken worden geblokkeerd voor Azure Integration runtime en worden momenteel alleen gebruikt voor gegevens verplaatsing, pijp lijn en externe activiteiten. Gegevens stromen deze IP-bereiken nu niet gebruiken. 

Dit kan in veel scenario's werken en we begrijpen dat een uniek, statisch IP-adres per Integration runtime gewenst zou zijn, maar dit zou niet mogelijk kunnen zijn met behulp van Azure Integration Runtime op dit moment zonder server. Indien nodig kunt u altijd een zelf-hostende Integration Runtime instellen en uw statische IP-adres gebruiken. 

## <a name="data-access-strategies-through-azure-data-factory"></a>Strategieën voor gegevens toegang via Azure Data Factory

* **[Trusted service](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)** -Azure Storage (Blob, ADLS Gen2) ondersteunt firewall configuratie waarmee betrouw bare Azure-platform Services kunnen worden geselecteerd om veilig toegang te krijgen tot het opslag account. Vertrouwde services afdwingt beheerde identiteits verificatie, zodat er geen andere data factory verbinding kan maken met deze opslag, tenzij white list dat doet met behulp van de beheerde identiteit. U vindt meer informatie in **[deze blog](https://techcommunity.microsoft.com/t5/azure-data-factory/data-factory-is-now-a-trusted-service-in-azure-storage-and-azure/ba-p/964993)**. Dit is daarom zeer veilig en wordt aanbevolen. 
* **Uniek statisch IP** -u moet een zelf-hostende Integration runtime instellen om een statisch IP-adres voor Data Factory-connectors te krijgen. Dit mechanisme zorgt ervoor dat u de toegang tot alle andere IP-adressen kunt blok keren. 
* **[Statisch IP-bereik](https://docs.microsoft.com/azure/data-factory/azure-integration-runtime-ip-addresses)** : u kunt IP-adressen van Azure Integration runtime gebruiken om deze in uw opslag ruimte weer te geven (bijvoorbeeld S3, Sales Force, enz.). Het is zeker dat IP-adressen die verbinding kunnen maken met de gegevens archieven worden beperkt, maar ook afhankelijk zijn van verificatie-en autorisatie regels.
* **[Servicetag: een](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)** service label vertegenwoordigt een groep IP-adres voorvoegsels van een bepaalde Azure-service (zoals Azure Data Factory). Micro soft beheert de adres voorvoegsels die zijn opgenomen in het servicetag en werkt de servicetag automatisch bij met gewijzigde adressen, zodat de complexiteit van regel matige updates voor netwerk beveiligings regels wordt geminimaliseerd. Het is handig wanneer u gegevens toegang White List op IaaS gehoste gegevens archieven in Virtual Network.
* **Azure-Services toestaan** : met sommige services kunt u alle Azure-Services verbinding laten maken voor het geval u deze optie kiest. 

Zie onder twee tabellen voor meer informatie over ondersteunde netwerk beveiligings mechanismen voor gegevens archieven in Azure Integration Runtime en zelf-hostende Integration Runtime.  
* **Azure Integration Runtime**

    | Gegevensarchieven                  | Ondersteund netwerk beveiligings mechanisme in gegevens archieven         | Vertrouwde service     | Statisch IP-bereik | Servicetags | Azure-Services toestaan |
    |------------------------------|-------------------------------------------------------------|---------------------|-----------------|--------------|----------------------|
    | Azure PaaS-gegevens opslag       | Azure Cosmos DB                                             | -                   | Ja             | -            | Yes                  |
    |                              | Azure Data Explorer                                         | -                   | Ja*            | Ja*         | -                    |
    |                              | Azure Data Lake gen1                                        | -                   | Ja             | -            | Yes                  |
    |                              | Azure Database for MariaDB, MySQL, PostgreSQL               | -                   | Ja             | -            | Yes                  |
    |                              | Azure File Storage                                          | -                   | Yes             | -            | .                    |
    |                              | Azure Storage (blog, ADLS Gen2)                             | Ja (alleen MSI-verificatie) | Yes             | -            | .                    |
    |                              | Azure SQL DB, SQL DW (Synapse Analytics), SQL ml          | -                   | Ja             | -            | Yes                  |
    |                              | Azure Key Vault (voor het ophalen van geheimen/connection string) | Ja                 | Yes             | -            | -                    |
    | Andere PaaS/SaaS-gegevens archieven | AWS S3, Sales Force, Google Cloud Storage, enzovoort.            | -                   | Yes             | -            | -                    |
    | Azure-laaS                   | SQL Server, Oracle, etc.                                  | -                   | Ja             | Yes          | -                    |
    | On-premises laaS              | SQL Server, Oracle, etc.                                  | -                   | Yes             | -            | -                    |
    
    **Alleen van toepassing als Azure Data Explorer virtueel netwerk is geïnjecteerd en IP-bereik kan worden toegepast op NSG/firewall.* 

* **Zelf-hostende Integration Runtime (in Vnet/on-premises)**
    
    | Gegevens archieven                  | Ondersteund netwerk beveiligings mechanisme in gegevens archieven         | Statisch IP-adres | Vertrouwde services  |
    |--------------------------------|---------------------------------------------------------------|-----------|---------------------|
    | Azure PaaS-gegevens opslag       | Azure Cosmos DB                                               | Ja       | -                   |
    |                                | Azure Data Explorer                                           | -         | -                   |
    |                                | Azure Data Lake gen1                                          | Yes       | -                   |
    |                                | Azure Database for MariaDB, MySQL, PostgreSQL               | Yes       | -                   |
    |                                | Azure File Storage                                            | Yes       | -                   |
    |                                | Azure Storage (blog, ADLS Gen2)                             | Yes       | Ja (alleen MSI-verificatie) |
    |                                | Azure SQL DB, SQL DW (Synapse Analytics), SQL ml          | Yes       | -                   |
    |                                | Azure Key Vault (voor het ophalen van geheimen/connection string) | Ja       | Yes                 |
    | Andere PaaS/SaaS-gegevens archieven | AWS S3, Sales Force, Google Cloud Storage, enzovoort.              | Yes       | -                   |
    | Azure-laaS                     | SQL Server, Oracle, etc.                                  | Yes       | -                   |
    | On-premises laaS              | SQL Server, Oracle, etc.                                  | Ja       | -                   |    

## <a name="next-steps"></a>Volgende stappen

Zie de volgende verwante artikelen voor meer informatie:
* [Ondersteunde gegevensarchieven](https://docs.microsoft.com/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)
* [Vertrouwde services Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints#trusted-services)
* [Vertrouwde micro soft-Services Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services)
* [Beheerde identiteit voor Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity)
