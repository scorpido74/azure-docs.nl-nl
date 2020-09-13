---
title: Meldt zich aan Azure Monitor | Microsoft Docs
description: Hierin worden de logboeken in Azure Monitor beschreven, die worden gebruikt voor geavanceerde analyse van bewakings gegevens.
documentationcenter: ''
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 09/09/2020
ms.author: bwren
ms.openlocfilehash: e08c649b9a1d7e8b909a413ee435fce30a8d7e48
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2020
ms.locfileid: "90032763"
---
# <a name="azure-monitor-logs-overview"></a>Overzicht van Azure Monitor logboeken
Azure Monitor-Logboeken is een functie van Azure Monitor waarmee logboek gegevens van verschillende bronnen worden verzameld en ingedeeld en beschikbaar worden gemaakt voor analyse met behulp van een geavanceerde query taal. Gegevens uit verschillende bronnen kunnen worden geconsolideerd in één werk ruimte en samen worden geanalyseerd om dergelijke taken en trending analyses, waarschuwingen en visualisaties uit te voeren.

## <a name="relationship-to-azure-monitor-metrics"></a>Relatie met Azure Monitor metrieken
Met Azure Monitor metrieken worden numerieke gegevens opgeslagen in een Data Base van time series, waardoor deze gegevens lichter worden dan Azure Monitor-logboeken en de bijna realtime scenario's kunnen ondersteunen, waardoor ze bijzonder nuttig zijn voor waarschuwingen en snelle detectie van problemen. Metrieken kunnen alleen numerieke gegevens in een bepaalde structuur opslaan, terwijl Logboeken verschillende verschillende gegevens typen met hun eigen structuur kunnen opslaan. U kunt ook complexe analyses uitvoeren op logboek gegevens met behulp van logboek query's die niet kunnen worden gebruikt voor de analyse van metrische gegevens.

Numerieke gegevens worden vaak van gegevens bronnen naar Logboeken verzonden, naast de metrieken. Hoewel er extra kosten in rekening worden gebracht voor het verzamelen en bewaren van deze gegevens in Logboeken, kunt u metrische gegevens in logboek query's toevoegen en analyseren met uw andere bewakings gegevens.

## <a name="relationship-to-azure-data-explorer"></a>Relatie met Azure Data Explorer
Azure Monitor-Logboeken is gebaseerd op Azure Data Explorer. Een Log Analytics werk ruimte is ongeveer het equivalent van een data base in azure Data Explorer, tabellen worden gestructureerd en beide gebruiken dezelfde Kusto query language (KQL). De ervaring van het gebruik van Log Analytics om met Azure Monitor query's in de Azure Portal te werken, is vergelijkbaar met de ervaring met behulp van de Web-UI van Azure Data Explorer. U kunt zelfs [gegevens uit een log Analytics-werk ruimte in een Azure Data Explorer-query toevoegen](/azure/data-explorer/query-monitor-data). 


## <a name="structure-of-data"></a>Structuur van gegevens
Gegevens die worden verzameld door Azure Monitor logboeken worden opgeslagen in een [log Analytics-werk ruimte](./design-logs-deployment.md) die meerdere tabellen bevat die elke gegevens van een bepaalde bron bevatten. De werk ruimte definieert de geografische locatie van de gegevens, toegangs rechten die bepalen welke gebruikers toegang hebben tot gegevens en configuratie-instellingen, zoals de prijs categorie en gegevens retentie. U kunt één werk ruimte gebruiken voor al uw bewakings gegevens of meerdere werk ruimten maken, afhankelijk van uw vereisten. Zie [de implementatie van uw Azure monitor-logboeken ontwerpen](design-logs-deployment.md) voor overwegingen bij het maken van meerdere werk ruimten.

Elke werk ruimte bevat meerdere tabellen die zijn ingedeeld in afzonderlijke kolommen met meerdere gegevens rijen. Elke tabel wordt gedefinieerd door een unieke set kolommen die worden gedeeld door de gegevens rijen die door de gegevens bron worden opgegeven. 

[![Structuur van Azure Monitor logboeken](media/data-platform-logs/logs-structure.png)](media/data-platform-logs/logs-structure.png#lightbox)


Logboek gegevens van Application Insights worden ook opgeslagen in Azure Monitor logboeken, maar worden opgeslagen, afhankelijk van hoe uw toepassing is geconfigureerd. Voor een op werk ruimte gebaseerde toepassing worden gegevens opgeslagen in een Log Analytics-werk ruimte in een standaardset met gegevens, zoals toepassings aanvragen, uitzonde ringen en pagina weergaven. Meerdere toepassingen kunnen dezelfde werk ruimte gebruiken. Voor een klassieke toepassing worden de gegevens niet opgeslagen in een Log Analytics-werk ruimte. De query taal wordt gebruikt en u kunt query's maken en uitvoeren met behulp van hetzelfde Log Analytics-hulp programma in de Azure Portal. Gegevens voor klassieke toepassingen worden onafhankelijk van elkaar opgeslagen. De algemene structuur is hetzelfde als op werk ruimte gebaseerde toepassingen, hoewel de tabel-en kolom namen verschillend zijn. Zie [resource wijzigingen op basis van een werk ruimte](../app/apm-tables.md) voor een gedetailleerde vergelijking van de twee.


> [!NOTE]
> We bieden nog steeds volledige compatibiliteit voor uw Application Insights klassieke resource query's, werkmappen en waarschuwingen op basis van Logboeken in de Application Insights ervaring. Als u wilt zoeken/weer geven op [basis van de nieuwe, op werk ruimte gebaseerde tabel structuur/schema,](../app/apm-tables.md) moet u eerst naar uw log Analytics-werk ruimte navigeren. Tijdens de preview-periode selecteert u **Logboeken** in het deel venster Application Insights. u krijgt dan toegang tot de klassieke Application Insights query-ervaring. Zie [query bereik](../log-query/scope.md) voor meer informatie.


[![Structuur van Azure Monitor logboeken voor Application Insights](media/data-platform-logs/logs-structure-ai.png)](media/data-platform-logs/logs-structure-ai.png#lightbox)

## <a name="log-queries"></a>Logboekquery's
Gegevens worden opgehaald uit een Log Analytics-werk ruimte met behulp van een [logboek query](../log-query/log-query-overview.md) die een alleen-lezen aanvraag is voor het verwerken van gegevens en het retour neren van resultaten. Logboek query's worden geschreven in [Kusto query language (KQL)](/azure/data-explorer/kusto/query/). Dit is de query taal die wordt gebruikt door Azure Data Explorer. Gebruik Log Analytics, een hulp programma in de Azure Portal om logboek query's te bewerken en uit te voeren en de resultaten interactief te analyseren. U kunt vervolgens de query's gebruiken die u hebt gemaakt ter ondersteuning van andere functies in Azure Monitor, zoals waarschuwingen voor logboek query's en werkmappen.


## <a name="sources-of-data-for-azure-monitor-logs"></a>Gegevens bronnen voor Azure Monitor-logboeken
Azure Monitor verzamelt logboek gegevens uit verschillende bronnen, inclusief bronnen in Azure Monitor en agents die worden uitgevoerd op virtuele machines. Zie [wat wordt bewaakt door Azure monitor?](../monitor-reference.md) voor een volledige lijst met gegevens bronnen waarmee gegevens naar een log Analytics-werk ruimte worden verzonden.



## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [logboek query's](../log-query/log-query-overview.md) voor het ophalen en analyseren van gegevens uit een log Analytics-werk ruimte.
- Meer informatie over [metrische gegevens in azure monitor](data-platform-metrics.md).
- Meer informatie over de [beschik bare bewakings gegevens](data-sources.md) voor verschillende bronnen in Azure.

