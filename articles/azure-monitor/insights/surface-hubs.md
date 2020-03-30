---
title: Surface Hubs bewaken met Azure-monitor | Microsoft Documenten
description: Gebruik de Surface Hub-oplossing om de status van je Surface Hubs bij te houden en te begrijpen hoe ze worden gebruikt.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/16/2018
ms.openlocfilehash: 7999735bf2d182b2811d01172adcfc89cba27dc8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77662498"
---
# <a name="monitor-surface-hubs-with-azure-monitor-to-track-their-health"></a>Surface Hubs met Azure Monitor controleren om hun status bij te houden

![Surface Hub-symbool](./media/surface-hubs/surface-hub-symbol.png)

In dit artikel wordt beschreven hoe u de Surface Hub-oplossing in Azure Monitor gebruiken om Microsoft Surface Hub-apparaten te controleren. De oplossing helpt je de gezondheid van je Surface Hubs bij te houden en te begrijpen hoe ze worden gebruikt.

Elke Surface Hub heeft de Microsoft Monitoring Agent geïnstalleerd. Het is via de agent dat u gegevens van uw Surface Hub verzenden naar een Log Analytics-werkruimte in Azure Monitor. Logbestanden worden gelezen vanuit uw Surface Hubs en worden vervolgens naar Azure Monitor verzonden. Problemen zoals servers die offline zijn, de agenda worden niet gesynchroniseerd of als het apparaataccount zich niet kan aanmelden bij Skype, worden weergegeven in het Surface Hub-dashboard in Azure Monitor. Door de gegevens in het dashboard te gebruiken, u apparaten identificeren die niet worden uitgevoerd of die andere problemen hebben, en mogelijk oplossingen toepassen voor de gedetecteerde problemen.

## <a name="install-and-configure-the-solution"></a>De oplossing installeren en configureren
Gebruik de volgende informatie om de oplossing te installeren en configureren. Als u uw Surface Hubs in Azure Monitor wilt beheren, hebt u het volgende nodig:

* Een [log analytics-abonnementsniveau](https://azure.microsoft.com/pricing/details/log-analytics/) dat het aantal apparaten ondersteunt dat u wilt controleren. De prijzen van Log Analytics zijn afhankelijk van het aantal apparaten dat is ingeschreven en hoeveel gegevens het verwerkt. Je wilt hier rekening mee houden bij het plannen van je Surface Hub-uitrol.

Vervolgens voegt u een bestaande Log Analytics-werkruimte toe of maakt u een nieuwe werkruimte. Gedetailleerde instructies voor het gebruik van een van beide methoden is bij [Een werkruimte logboekanalyse maken in de Azure-portal](../learn/quick-create-workspace.md). Zodra de Werkruimte Log Analytics is geconfigureerd, zijn er twee manieren om uw Surface Hub-apparaten in te schrijven:

* Automatisch via Intune
* Handmatig via **Instellingen** op je Surface Hub-apparaat.

## <a name="set-up-monitoring"></a>Controle instellen
U de status en activiteit van uw Surface Hub controleren met Azure Monitor. Je de Surface Hub inschrijven door Intune te gebruiken of lokaal met **Instellingen** op de Surface Hub.

## <a name="connect-surface-hubs-to-azure-monitor-through-intune"></a>Surface Hubs verbinden met Azure Monitor via Intune
Je hebt de werkruimte-id en werkruimtesleutel nodig voor de Log Analytics-werkruimte waarmee je surfacehubs wordt beheren. U deze ophalen via de werkruimte-instellingen in de Azure-portal.

Intune is een Microsoft-product waarmee u de configuratie-instellingen van de Log Analytics-werkruimte die op een of meer van uw apparaten worden toegepast, centraal beheren. Volg de volgende stappen om uw apparaten te configureren via Intune:

1. Log hier in
2. Navigeer naar **Instellingen** > **Verbonden Bronnen**.
3. Een beleid maken of bewerken op basis van de sjabloon Surface Hub.
4. Navigeer naar het gedeelte Azure Operational Insights van het beleid en voeg de *log-analyse-id* en *werkruimtesleutel* toe aan het beleid.
5. U kunt nu het beleid opslaan.
6. Het beleid koppelen aan de juiste groep apparaten.

   ![Intune-beleid](./media/surface-hubs/intune.png)

Intune synchroniseert vervolgens de Log Analytics-instellingen met de apparaten in de doelgroep en schrijft ze in in uw Log Analytics-werkruimte.

## <a name="connect-surface-hubs-to-azure-monitor-using-the-settings-app"></a>Surface Hubs verbinden met Azure Monitor met de app Instellingen
Je hebt de werkruimte-id en werkruimtesleutel nodig voor de Log Analytics-werkruimte waarmee je surfacehubs wordt beheren. U deze ophalen via de instellingen voor de werkruimte Log Analytics in de Azure-portal.

Als je Intune niet gebruikt om je omgeving te beheren, kun je apparaten handmatig inschrijven via **Instellingen** op elke Surface Hub:

1. Open **Instellingen**vanuit je Surface Hub .
2. Voer de referenties voor apparaatbeheerders in wanneer daarom wordt gevraagd.
3. Klik op **Dit apparaat**en klik onder **Bewaken**op Instellingen **voor logboekanalyse configureren**.
4. Selecteer **Bewaking inschakelen**.
5. Typ in het dialoogvenster Instellingen voor Logboekanalyse de **werkruimte-id** log Analytics en typ de **werkruimtesleutel**.  
   ![instellingen](./media/surface-hubs/settings.png)
6. Klik op **OK** om de configuratie te voltooien.

Er verschijnt een bevestiging waarin staat of de configuratie al dan niet is toegepast op het apparaat. Als dit het was, verschijnt er een bericht waarin staat dat de agent is verbonden met Azure Monitor. Het apparaat begint vervolgens gegevens naar Azure Monitor te verzenden, waar u deze bekijken en ernaar handelen.

## <a name="monitor-surface-hubs"></a>Surface-hubs bewaken
Het bewaken van uw Surface Hubs met Azure Monitor is net als het bewaken van andere ingeschreven apparaten.

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Wanneer u op de surface-hubtegel klikt, wordt de status van uw apparaat weergegeven.

   ![Surface Hub-dashboard](./media/surface-hubs/surface-hub-dashboard.png)

U [waarschuwingen](../platform/alerts-overview.md) maken op basis van bestaande of aangepaste logboekzoekopdrachten. Met behulp van de gegevens die Azure Monitor verzamelt van uw Surface Hubs, u zoeken naar problemen en waarschuwen voor de voorwaarden die u voor uw apparaten definieert.

## <a name="next-steps"></a>Volgende stappen
* Gebruik [Logboekquery's in Azure Monitor](../log-query/log-query-overview.md) om gedetailleerde Surface Hub-gegevens weer te geven.
* Maak [meldingen](../platform/alerts-overview.md) om u te waarschuwen wanneer er problemen optreden met uw Surface Hubs.
