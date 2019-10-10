---
title: Advanced Threat Protection voor Azure Cosmos DB
description: Meer informatie over het Azure Cosmos DB versleutelen van gegevens in rust en hoe deze worden geïmplementeerd.
author: rkarlin
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 2b12d639e734502113b6afdd7250fca6a520c687
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176028"
---
# <a name="advanced-threat-protection-for-azure-cosmos-db"></a>Advanced Threat Protection voor Azure Cosmos DB

Advanced Threat Protection voor Azure Cosmos DB biedt een extra beveiligingslaag die ongebruikelijke en mogelijk schadelijke pogingen detecteert om Azure Cosmos DB accounts te openen of misbruik te maken. Met deze beveiligingslaag kunt u bedreigingen aanpakken, zelfs zonder een beveiligings expert, en ze integreren met centrale beveiligings bewakings systemen.

Beveiligings waarschuwingen worden geactiveerd wanneer afwijkingen in de activiteit optreden. Deze beveiligings waarschuwingen zijn geïntegreerd met [Azure Security Center](https://azure.microsoft.com/services/security-center/)en worden ook via e-mail verzonden naar abonnements beheerders, met details van de verdachte activiteit en aanbevelingen voor het onderzoeken en oplossen van de bedreigingen.

> [!NOTE]
>
> * Advanced Threat Protection voor Azure Cosmos DB is momenteel alleen beschikbaar voor de SQL-API.
> * Advanced Threat Protection voor Azure Cosmos DB is momenteel niet beschikbaar in azure Government en soevereine Cloud regio's.

Voor een volledige onderzoek van de beveiligings waarschuwingen, is het aanbevolen om [Diagnostische logboek registratie in azure Cosmos db in](https://docs.microsoft.com/azure/cosmos-db/logging)te scha kelen, waarmee bewerkingen op de data base zelf worden geregistreerd, inclusief ruwe bewerkingen op alle documenten, containers en data bases.

## <a name="set-up-advanced-threat-protection"></a>Geavanceerde bedreigings beveiliging instellen

### <a name="set-up-atp-using-the-portal"></a>ATP instellen met behulp van de portal

1. Start de Azure Portal op [https://portal.azure.com](https://portal.azure.com/).

2. Selecteer vanuit het Azure Cosmos DB-account in het menu **instellingen** de optie **geavanceerde beveiliging**.

    ![ATP instellen](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp.png)

3. Op de Blade **Geavanceerde beveiligings** configuratie:

    * Klik op de optie **geavanceerde beveiliging tegen bedreigingen** om deze **in**te stellen op aan.
    * Klik op **Opslaan** om het nieuwe of bijgewerkte geavanceerde beveiligings beleid voor bedreigingen op te slaan.   

### <a name="set-up-atp-using-rest-api"></a>ATP instellen met behulp van REST API

Gebruik rest API-opdrachten om de instelling geavanceerde beveiliging tegen bedreigingen te maken, bij te werken of op te halen voor een specifiek Azure Cosmos DB-account.

* [Advanced Threat Protection-maken](https://go.microsoft.com/fwlink/?linkid=2099745)
* [Advanced Threat Protection-ophalen](https://go.microsoft.com/fwlink/?linkid=2099643)

### <a name="set-up-atp-using-azure-powershell"></a>ATP instellen met behulp van Azure PowerShell

Gebruik de volgende Power shell-cmdlets:

* [Advanced Threat Protection inschakelen](https://go.microsoft.com/fwlink/?linkid=2099607&clcid=0x409)
* [Geavanceerde beveiliging tegen bedreigingen verkrijgen](https://go.microsoft.com/fwlink/?linkid=2099608&clcid=0x409)
* [Geavanceerde bedreigings beveiliging uitschakelen](https://go.microsoft.com/fwlink/?linkid=2099709&clcid=0x409)

### <a name="using-azure-resource-manager-templates"></a>Azure Resource Manager sjablonen gebruiken

Gebruik een Azure Resource Manager sjabloon om Cosmos DB in te stellen als Advanced Threat Protection is ingeschakeld.
Zie [een CosmosDB-account maken met geavanceerde beveiliging tegen bedreigingen](https://azure.microsoft.com/en-us/resources/templates/201-cosmosdb-advanced-threat-protection-create-account/)voor meer informatie.

### <a name="using-azure-policy"></a>Azure Policy gebruiken

Gebruik een Azure Policy om geavanceerde bedreigingen beveiliging in te scha kelen voor Cosmos DB.

1. Start de pagina Azure **Policy-Definitions** en zoek naar het **Cosmos DBS beleid Deploying Advanced Threat Protection** .

    ![Zoek beleid](./media/cosmos-db-advanced-threat-protection/cosmos-db.png) 

1. Klik op het beleid **geavanceerde beveiliging tegen bedreigingen implementeren voor CosmosDB** en klik vervolgens op **toewijzen**.

    ![Abonnement of groep selecteren](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-policy.png)


1. Klik in het veld **bereik** op de drie puntjes, selecteer een Azure-abonnement of resource groep en klik vervolgens op **selecteren**.

    ![Pagina beleids definities](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-details.png)


1. Voer de andere para meters in en klik op **toewijzen**.

## <a name="manage-atp-security-alerts"></a>ATP-beveiligings waarschuwingen beheren

Wanneer Azure Cosmos DB afwijkende activiteiten optreden, wordt een beveiligings waarschuwing geactiveerd met informatie over de verdachte beveiligings gebeurtenis. 

 Vanuit Azure Security Center kunt u uw huidige [beveiligings waarschuwingen](../security-center/security-center-alerts-overview.md)controleren en beheren.  Klik op een specifieke waarschuwing in [Security Center](https://ms.portal.azure.com/#blade/Microsoft_Azure_Security/SecurityMenuBlade/0) om mogelijke oorzaken en aanbevolen acties te bekijken om de mogelijke dreiging te onderzoeken en te verhelpen. In de volgende afbeelding ziet u een voor beeld van waarschuwings details die zijn opgenomen in Security Center.

 ![Details van bedreiging](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert-details.png)

Er wordt ook een e-mail melding met de waarschuwings Details en aanbevolen acties verzonden. De volgende afbeelding toont een voor beeld van een e-mail waarschuwing.

 ![Meldingsdetails](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert.png)

## <a name="cosmos-db-atp-alerts"></a>Cosmos DB ATP-waarschuwingen

 Zie de sectie [Cosmos DB-waarschuwingen](../security-center/security-center-alerts-data-services.md#cosmos-db) in de Security Center documentatie voor een overzicht van de waarschuwingen die worden gegenereerd bij het controleren van Azure Cosmos DB accounts.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Diagnostische logboek registratie in azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/logging#turn-on-logging-in-the-azure-portal)
* Meer informatie over [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
