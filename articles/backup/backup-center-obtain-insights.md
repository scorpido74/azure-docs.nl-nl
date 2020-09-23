---
title: Inzichten verkrijgen met behulp van Back-upcentrum
description: Meer informatie over het analyseren van historische trends en het verkrijgen van meer inzicht in uw back-ups met Back-upcentrum.
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: 5964f285089feea721a0b452efed884e905b89cc
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90994972"
---
# <a name="obtain-insights-using-backup-center"></a>Inzichten verkrijgen met behulp van Back-upcentrum

Voor het analyseren van historische trends en het verkrijgen van diepere inzichten op uw back-ups biedt Back-upcentrum een interface voor het [maken van back-uprapporten](configure-reports.md), die gebruikmaakt van [Azure monitor-logboeken](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs) en [Azure-werkmappen](https://docs.microsoft.com/azure/azure-monitor/platform/workbooks-overview). Back-uprapporten bieden de volgende mogelijkheden:

- Het toewijzen en voors pellen van verbruikte Cloud opslag.

- Controle van back-ups en herstel bewerkingen.

- De belangrijkste trends op verschillende niveaus nauw keurig te identificeren.

- Krijg inzicht in de mogelijkheden van de kosten optimalisatie voor uw back-ups.

## <a name="supported-scenarios"></a>Ondersteunde scenario's

- Er zijn momenteel geen back-uprapporten beschikbaar voor back-up van Azure Database for PostgreSQL server.

- Raadpleeg de [ondersteunings matrix](backup-center-support-matrix.md) voor een gedetailleerde lijst met ondersteunde en niet-ondersteunde scenario's.

## <a name="get-started"></a>Aan de slag

### <a name="configure-your-vaults-to-send-data-to-a-log-analytics-workspace"></a>Uw kluizen configureren voor het verzenden van gegevens naar een Log Analytics-werk ruimte

[Meer informatie over het configureren van diagnostische instellingen op schaal voor uw kluizen](https://docs.microsoft.com/azure/backup/configure-reports#get-started)

### <a name="view-backup-reports-in-the-backup-center-portal"></a>Back-uprapporten weer geven in de portal van het Back-upcentrum

Als u het menu **-item back-uprapporten** selecteert in Back-upcentrum, worden de rapporten geopend. Kies een of meer Log Analytics werk ruimten om de belangrijkste informatie over uw back-ups weer te geven en te analyseren.

![Back-uprapporten in Back-upcentrum](./media/backup-center-obtain-insights/backup-center-backup-reports.png)

Hieronder ziet u de beschik bare weer gaven:

1. **Samen vatting** : gebruik dit tabblad om een overzicht te krijgen van uw back-ups op hoog niveau. [Meer informatie](https://docs.microsoft.com/azure/backup/configure-reports#summary)

1. **Back-** upitems: gebruik dit tabblad om informatie en trends in Cloud opslag te bekijken die worden gebruikt op een back-upitemniveau. [Meer informatie](https://docs.microsoft.com/azure/backup/configure-reports#backup-items)

1. **Gebruik** : gebruik dit tabblad om de para meters voor de sleutel facturering voor uw back-ups weer te geven. [Meer informatie](https://docs.microsoft.com/azure/backup/configure-reports#usage)

1. **Taken** : gebruik dit tabblad om langlopende trends voor taken weer te geven, zoals het aantal mislukte taken per dag en de belangrijkste oorzaken van een mislukte taak. [Meer informatie](https://docs.microsoft.com/azure/backup/configure-reports#jobs)

1. **Beleid** : gebruik dit tabblad om informatie weer te geven over al uw actieve beleids regels, zoals het aantal gekoppelde items en de totale Cloud opslag die wordt gebruikt door items waarvan een back-up is gemaakt onder een bepaald beleid. [Meer informatie](https://docs.microsoft.com/azure/backup/configure-reports#policies)

1. **Optimaliseer** : gebruik dit tabblad om inzicht te krijgen in potentiële mogelijkheden voor kosten optimalisatie voor uw back-ups. [Meer informatie](https://docs.microsoft.com/azure/backup/configure-reports#optimize)

## <a name="next-steps"></a>Volgende stappen

- [Back-ups controleren en uitvoeren](backup-center-monitor-operate.md)
- [Uw back-up maken](backup-center-govern-environment.md)
- [Acties uitvoeren met behulp van Back-upcentrum](backup-center-actions.md)
