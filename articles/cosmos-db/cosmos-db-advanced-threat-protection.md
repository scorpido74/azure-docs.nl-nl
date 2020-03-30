---
title: Geavanceerde bedreigingsbeveiliging voor Azure Cosmos DB
description: Ontdek hoe Azure Cosmos DB versleuteling biedt van gegevens in rust en hoe deze wordt geïmplementeerd.
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: seodec18
ms.author: memildin
author: memildin
manager: rkarlin
ms.openlocfilehash: bcc1c6ffe7cdec4aed325a67969235ae993a5109
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77614837"
---
# <a name="advanced-threat-protection-for-azure-cosmos-db-preview"></a>Geavanceerde bedreigingsbeveiliging voor Azure Cosmos DB (preview)

Advanced Threat Protection for Azure Cosmos DB biedt een extra laag beveiligingsinformatie die ongebruikelijke en mogelijk schadelijke pogingen detecteert om toegang te krijgen tot Azure Cosmos DB-accounts of deze te exploiteren. Met deze beveiligingslaag u bedreigingen aanpakken, zelfs zonder beveiligingsexpert te zijn, en deze te integreren met centrale beveiligingsbewakingssystemen.

Beveiligingswaarschuwingen worden geactiveerd wanneer er afwijkingen in de activiteit optreden. Deze beveiligingswaarschuwingen zijn geïntegreerd met [Azure Security Center](https://azure.microsoft.com/services/security-center/)en worden ook via e-mail verzonden naar abonnementsbeheerders, met details over de verdachte activiteit en aanbevelingen over het onderzoeken en herstellen van de bedreigingen.

> [!NOTE]
>
> * Advanced Threat Protection voor Azure Cosmos DB is momenteel alleen beschikbaar voor de SQL API.
> * Advanced Threat Protection for Azure Cosmos DB is momenteel niet beschikbaar in Azure-overheids- en soevereine cloudregio's.

Voor een volledige onderzoekservaring van de beveiligingswaarschuwingen raden we aan [diagnostische logboekregistratie in Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/logging)in te schakelen, die bewerkingen op de database zelf registreert, inclusief CRUD-bewerkingen op alle documenten, containers en databases.

## <a name="threat-types"></a>Bedreigingstypen

Advanced Threat Protection for Azure Cosmos DB detecteert afwijkende activiteiten die ongebruikelijke en mogelijk schadelijke pogingen aangeven om databases te openen of te exploiteren. Het kan momenteel de volgende waarschuwingen activeren:

- **Toegang vanaf ongebruikelijke locaties:** deze waarschuwing wordt geactiveerd wanneer er een wijziging is in het toegangspatroon naar een Azure Cosmos-account, waarbij iemand verbinding heeft gemaakt met het Azure Cosmos DB-eindpunt vanaf een ongebruikelijke geografische locatie. In sommige gevallen detecteert de waarschuwing een legitieme actie, wat betekent dat een nieuwe toepassing of de onderhoudsbewerking van een ontwikkelaar wordt uitgevoerd. In andere gevallen detecteert de waarschuwing een kwaadaardige actie van een voormalige werknemer, externe aanvaller, enz.

- **Ongebruikelijke gegevensextractie:** deze waarschuwing wordt geactiveerd wanneer een client een ongebruikelijke hoeveelheid gegevens uit een Azure Cosmos DB-account haalt. Dit kan het symptoom zijn van sommige gegevensexfiltratie die wordt uitgevoerd om alle gegevens die in het account zijn opgeslagen over te dragen naar een extern gegevensarchief.

## <a name="set-up-advanced-threat-protection"></a>Geavanceerde bescherming voor bedreigingen instellen

### <a name="set-up-atp-using-the-portal"></a>ATP instellen via de portal

1. Start de Azure-portal op [https://portal.azure.com](https://portal.azure.com/).

2. Selecteer in het Azure Cosmos DB-account in het menu **Instellingen** de optie **Geavanceerde beveiliging**.

    ![ATP instellen](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp.png)

3. In het **geavanceerde beveiligingsconfiguratieblad:**

    * Klik op de optie **Geavanceerde bedreigingsbeveiliging** om deze in te stellen **op AAN**.
    * Klik op **Opslaan** om het nieuwe of bijgewerkte Advanced Threat Protection-beleid op te slaan.   

### <a name="set-up-atp-using-rest-api"></a>ATP instellen met REST API

Gebruik Rest API-opdrachten om de instelling Geavanceerde bedreigingsbeveiliging voor een specifiek Azure Cosmos DB-account te maken, bij te werken of te ontvangen.

* [Geavanceerde bescherming tegen bedreigingen - Maken](https://go.microsoft.com/fwlink/?linkid=2099745)
* [Geavanceerde bescherming tegen bedreigingen - Get](https://go.microsoft.com/fwlink/?linkid=2099643)

### <a name="set-up-atp-using-azure-powershell"></a>ATP instellen met Azure PowerShell

Gebruik de volgende PowerShell-cmdlets:

* [Advanced Threat Protection inschakelen](https://go.microsoft.com/fwlink/?linkid=2099607&clcid=0x409)
* [Profiteer van geavanceerde bescherming tegen bedreigingen](https://go.microsoft.com/fwlink/?linkid=2099608&clcid=0x409)
* [Geavanceerde bedreigingsbeveiliging uitschakelen](https://go.microsoft.com/fwlink/?linkid=2099709&clcid=0x409)

### <a name="using-azure-resource-manager-templates"></a>Azure Resource Manager-sjablonen gebruiken

Gebruik een Azure Resource Manager-sjabloon om Cosmos DB in te stellen met Advanced Threat Protection ingeschakeld.
Zie [Een CosmosDB-account maken met Advanced Threat Protection](https://azure.microsoft.com/resources/templates/201-cosmosdb-advanced-threat-protection-create-account/)voor meer informatie.

### <a name="using-azure-policy"></a>Azure-beleid gebruiken

Gebruik een Azure-beleid om Geavanceerde bedreigingsbeveiliging voor Cosmos DB in te schakelen.

1. Start de pagina Azure **Policy - Definitions** en zoek naar het beleid Geavanceerde **bedreigingsbeveiliging implementeren voor Cosmos DB.**

    ![Zoekbeleid](./media/cosmos-db-advanced-threat-protection/cosmos-db.png) 

1. Klik op het beleid **Geavanceerde bedreigingsbeveiliging implementeren voor CosmosDB** en klik vervolgens op **Toewijzen**.

    ![Abonnement of groep selecteren](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-policy.png)


1. Klik **in** het veld Bereik op de drie puntjes, selecteer een Azure-abonnement of brongroep en klik vervolgens op **Selecteren**.

    ![Pagina Beleidsdefinities](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-details.png)


1. Voer de andere parameters in en klik op **Toewijzen**.

## <a name="manage-atp-security-alerts"></a>ATP-beveiligingswaarschuwingen beheren

Wanneer er afwijkingen van Azure Cosmos DB-activiteit optreden, wordt een beveiligingswaarschuwing geactiveerd met informatie over de verdachte beveiligingsgebeurtenis. 

 Vanuit Azure Security Center u uw huidige [beveiligingswaarschuwingen](../security-center/security-center-alerts-overview.md)controleren en beheren.  Klik op een specifieke waarschuwing in [Security Center](https://ms.portal.azure.com/#blade/Microsoft_Azure_Security/SecurityMenuBlade/0) om mogelijke oorzaken en aanbevolen acties te bekijken om de potentiële bedreiging te onderzoeken en te beperken. In de volgende afbeelding ziet u een voorbeeld van waarschuwingsgegevens in Security Center.

 ![Details van bedreigingen](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert-details.png)

Er wordt ook een e-mailmelding verzonden met de waarschuwingsgegevens en aanbevolen acties. In de volgende afbeelding ziet u een voorbeeld van een waarschuwingse-mail.

 ![Meldingsdetails](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert.png)

## <a name="cosmos-db-atp-alerts"></a>Cosmos DB ATP-waarschuwingen

 Zie de sectie [Cosmos DB-waarschuwingen](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurecosmos) in de documentatie van het Azure Security Center om een lijst te zien met de waarschuwingen die zijn gegenereerd bij het bewaken van Azure Cosmos DB-accounts.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [diagnostische logboekregistratie in Azure Cosmos DB](cosmosdb-monitor-resource-logs.md)
* Meer informatie over [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
