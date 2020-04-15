---
title: Noodherstel voor FarmBeats
description: In dit artikel wordt beschreven hoe gegevensherstel beschermt tegen het verlies van uw gegevens.
author: uhabiba04
ms.topic: article
ms.date: 04/13/2020
ms.author: v-umha
ms.openlocfilehash: d64735e683ba1133e7d381a68611d204c4068026
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313066"
---
# <a name="disaster-recovery-for-farmbeats"></a>Noodherstel voor FarmBeats

Gegevensherstel beschermt u tegen het verlies van uw gegevens in een gebeurtenis zoals het invouwen van Azure-regio. In een dergelijk geval u failover starten en de gegevens herstellen die zijn opgeslagen in uw FarmBeats-implementatie.

Gegevensherstel is geen standaardfunctie in Azure FarmBeats. U deze functie handmatig configureren door de vereiste Azure-resources te configureren die door FarmBeats worden gebruikt om gegevens op te slaan in een gekoppelde Azure-regio. Gebruik Active – Passieve aanpak om herstel mogelijk te maken.

In de volgende secties vindt u informatie over hoe u gegevensherstel configureren in Azure FarmBeats:

- [Gegevensredundantie inschakelen](#enable-data-redundancy)
- [Service herstellen vanuit online back-up](#restore-service-from-online-backup)


## <a name="enable-data-redundancy"></a>Gegevensredundantie inschakelen

FarmBeats slaat gegevens op in drie Azure first party-services, die **Azure-opslag,** **Cosmos DB** en Time **Series Insights**zijn. Gebruik de volgende stappen om gegevensredundantie voor deze services in te schakelen naar een gekoppeld Azure-gebied:

1.  **Azure Storage** : volg deze richtlijn om gegevensredundantie in te schakelen voor elk opslagaccount in uw FarmBbeats-implementatie.
2.  **Azure Cosmos DB** - Volg deze richtlijn om gegevensredundantie voor Cosmos DB-account uw FarmBeats-implementatie in te schakelen.
3.  **Azure Time Series Insights (TSI)** - TSI biedt momenteel geen redundantie voor gegevens. Als u Time Series Insights-gegevens wilt herstellen, gaat u naar uw sensor/weerpartner en duwt u de gegevens opnieuw naar farmbeats-implementatie.

## <a name="restore-service-from-online-backup"></a>Service herstellen vanuit online back-up

U failover starten en gegevens herstellen waarvoor elk van de bovengenoemde gegevensopslag voor uw FarmBeats-implementatie wordt opgeslagen. Zodra u de gegevens voor Azure-opslag en Cosmos DB hebt hersteld, maakt u een andere FarmBeats-implementatie in het gekoppelde Azure-gebied en configureert u de nieuwe implementatie om gegevens uit herstelde gegevensopslag (d.w.z. Azure Storage en Cosmos DB) te gebruiken met de onderstaande stappen:

1. [Cosmos DB configureren](#configure-cosmos-db)
2. [Opslagaccount configureren](#configure-storage-account)


### <a name="configure-cosmos-db"></a>Cosmos DB configureren

Kopieer de toegangssleutel van de herstelde Cosmos DB en werk de nieuwe FarmBeats Datahub Key Vault bij.


  ![Herstel na noodgevallen](./media/disaster-recovery-for-farmbeats/key-vault-secrets.png)

> [!NOTE]
> Kopieer de URL van herstelde Cosmos DB en werk deze bij in de nieuwe FarmBeats Datahub App Service Configuration. U nu cosmos DB-account verwijderen in de nieuwe FarmBeats-implementatie.

  ![Herstel na noodgevallen](./media/disaster-recovery-for-farmbeats/configuration.png)

### <a name="configure-storage-account"></a>Opslagaccount configureren

Kopieer de toegangssleutel van het herstelde opslagaccount en werk deze bij in de nieuwe FarmBeats Datahub Key Vault.

![Herstel na noodgevallen](./media/disaster-recovery-for-farmbeats/key-vault-7-secrets.png)

>[!NOTE]
> Zorg ervoor dat de naam van het opslagaccount wordt bijgewerkt in het nieuwe VM-config-bestand farmBeats Batch.

![Herstel na noodgevallen](./media/disaster-recovery-for-farmbeats/batch-prep-files.png)

Als u gegevensherstel voor uw Accelerator-opslagaccount hebt ingeschakeld, volgt u de stap 2 om de toegangssleutel en naam van het accelerator-opslagaccount bij te werken in het nieuwe farmbeats-exemplaar.
