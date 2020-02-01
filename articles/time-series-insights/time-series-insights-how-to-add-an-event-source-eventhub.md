---
title: Een Event Hubs gebeurtenis bron toevoegen-Azure Time Series Insights | Microsoft Docs
description: Meer informatie over het toevoegen van een Azure Event Hubs gebeurtenis bron aan uw Time Series Insights omgeving.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/30/2020
ms.custom: seodec18
ms.openlocfilehash: c3b06289ba6ce98d4307a8255981ecdba069fdfa
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76905407"
---
# <a name="add-an-event-hub-event-source-to-your-time-series-insights-environment"></a>Een event hub-gebeurtenisbron toevoegen aan uw Time Series Insights-omgeving

In dit artikel wordt beschreven hoe u de Azure portal gebruiken voor het toevoegen van een gebeurtenisbron die gegevens uit Azure Event Hubs op uw Azure Time Series Insights-omgeving leest.

> [!NOTE]
> De stappen die in dit artikel worden beschreven, zijn van toepassing op de voorbeeld omgevingen Time Series Insights GA en Time Series Insights.

## <a name="prerequisites"></a>Vereisten

- Een Time Series Insights omgeving maken zoals beschreven in [een Azure time series Insights omgeving maken](./time-series-insights-update-create-environment.md).
- Maak een Event Hub. Lees [een event hubs naam ruimte en een event hub maken met behulp van de Azure Portal](../event-hubs/event-hubs-create.md).
- De event hub moet actieve berichtgebeurtenissen verzonden naar deze hebben. Meer informatie over [het verzenden van gebeurtenissen naar Azure Event hubs met behulp van de .NET Framework](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md).
- Maak een speciale klantengroep in de event hub die de Time Series Insights-omgeving kan verbruiken. Elke Time Series Insights-gebeurtenisbron beschikken over een eigen speciale klantengroep die niet worden gedeeld met alle andere consumenten. Als meerdere lezers gebeurtenissen van dezelfde consumenten groep gebruiken, kunnen alle lezers waarschijnlijk fouten vertonen. Er is een limiet van 20 consumergroepen per event hub. Lees de [Event hubs-programmeer gids](../event-hubs/event-hubs-programming-guide.md)voor meer informatie.

### <a name="add-a-consumer-group-to-your-event-hub"></a>Een consumentengroep toevoegen aan uw event hub

Toepassingen gebruiken consumentengroepen gegevens wilt halen uit Azure Event Hubs. Als u gegevens op een betrouw bare manier van uw Event Hub wilt lezen, geeft u een speciale consumenten groep op die alleen door deze Time Series Insights omgeving wordt gebruikt.

Om toe te voegen een nieuwe consumentengroep in uw event hub:

1. Zoek en open in het [Azure Portal](https://portal.azure.com)het event hub-exemplaar in het deel venster **overzicht** van uw event hub naam ruimte. Selecteer **entiteiten > Event hubs** of Zoek uw instantie onder **naam**.

    [![uw Event Hub-naam ruimte openen](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-connect-event-hub-namespace.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-connect-event-hub-namespace.png#lightbox)

1. Selecteer in uw Event Hub-exemplaar **entiteiten > consumenten groepen**. Selecteer vervolgens **+ consumenten groep** om een nieuwe consumenten groep toe te voegen. 

   [Event hub ![-een Consumer groep toevoegen](media/time-series-insights-how-to-add-an-event-source-eventhub/add-event-hub-consumer-group.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/add-event-hub-consumer-group.png#lightbox)

   Anders selecteert u een bestaande consumenten groep en gaat u naar de volgende sectie.

1. Op de **consumentengroepen** pagina, voert u een nieuwe unieke waarde voor **naam**.  Gebruik deze dezelfde naam als u een nieuwe gebeurtenisbron in de Time Series Insights-omgeving maken.

1. Selecteer **Maken**.

## <a name="add-a-new-event-source"></a>Een nieuwe gebeurtenisbron toevoegen

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).

1. Ga naar uw bestaande Time Series Insights-omgeving. Selecteer in het menu links **alle resources**, en selecteer vervolgens uw Time Series Insights-omgeving.

1. Selecteer **gebeurtenis bronnen**en selecteer vervolgens **toevoegen**.

   [Selecteer ![onder gebeurtenis bronnen de knop toevoegen](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-add-an-event-source.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-add-an-event-source.png#lightbox)

1. Voer een waarde in voor de **gebeurtenis bron naam** die uniek is voor deze time series Insights omgeving, zoals `Contoso-TSI-GA-Event-Hub-ES`.

1. Voor **bron**, selecteer **Event Hub**.

1. Selecteer de juiste waarden voor **importoptie**:

   * Als u een bestaande event hub in een van uw abonnementen hebt, selecteert u **gebruik Event Hub uit de beschikbare abonnementen**. Deze optie is de eenvoudigste oplossing.

     [Selecteer een optie voor het importeren van de gebeurtenis bron ![](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-event-hub-select-import-option.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-event-hub-select-import-option.png#lightbox)

    *  De volgende tabel beschrijft de vereiste eigenschappen voor de **gebruik Event Hub uit de beschikbare abonnementen** optie:

       [![abonnement en Event Hub Details](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-configure-create-confirm.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-configure-create-confirm.png#lightbox)

       | Eigenschap | Beschrijving |
       | --- | --- |
       | Abonnement | Het abonnement waartoe de gewenste Event Hub instantie en naam ruimte behoort. |
       | Event hub-naamruimte | De naam ruimte van de Event Hub waarvan de gewenste Event Hub instantie deel uitmaakt. |
       | Event Hub-naam | De naam van de gewenste Event Hub-instantie. |
       | Waarde van Event hub-beleid | Selecteer het gewenste beleid voor gedeelde toegang. U kunt het beleid voor gedeelde toegang maken op het tabblad Event Hub **configureren** . Elk gedeeld toegangs beleid heeft een naam, machtigingen die u instelt en toegangs sleutels. Het beleid voor gedeelde toegang voor uw gebeurtenisbron *moet* hebben **lezen** machtigingen. |
       | Sleutel voor het Event Hub-beleid | Vooraf ingevuld op basis van de geselecteerde Event hub-beleids waarde. |

    * Als de event hub bevindt zich buiten uw abonnementen of als u wilt geavanceerde opties selecteert, selecteert u **bieden Event Hub-instellingen handmatig**.

       De volgende tabel beschrijft de vereiste eigenschappen voor de **bieden Event Hub-instellingen handmatig** optie:
 
       | Eigenschap | Beschrijving |
       | --- | --- |
       | Abonnements-id | Het abonnement waartoe de gewenste Event Hub instantie en naam ruimte behoort. |
       | Resourcegroep | De resource groep waarvan de gewenste Event Hub instantie en naam ruimte behoort. |
       | Event hub-naamruimte | De naam ruimte van de Event Hub waarvan de gewenste Event Hub instantie deel uitmaakt. |
       | Event Hub-naam | De naam van de gewenste Event Hub-instantie. |
       | Waarde van Event hub-beleid | Selecteer het gewenste beleid voor gedeelde toegang. U kunt het beleid voor gedeelde toegang maken op het tabblad Event Hub **configureren** . Elk gedeeld toegangs beleid heeft een naam, machtigingen die u instelt en toegangs sleutels. Het beleid voor gedeelde toegang voor uw gebeurtenisbron *moet* hebben **lezen** machtigingen. |
       | Sleutel voor het Event Hub-beleid | De gedeelde toegangssleutel die wordt gebruikt voor het verifiëren van toegang tot de Service Bus-naamruimte. Voer de primaire of secundaire sleutel hier in. |

    * Beide opties delen de volgende configuratie opties:

       | Eigenschap | Beschrijving |
       | --- | --- |
       | Event Hub-consumentengroep | De consumentengroep die gebeurtenissen uit de event hub kan lezen. Het is raadzaam dat u een speciale klantengroep voor uw gebeurtenisbron gebruiken. |
       | Serialisatie-indeling voor gebeurtenissen | JSON is momenteel de enige beschikbare serialisatie-indeling. Gebeurtenis berichten moeten de volgende indeling hebben of gegevens kunnen niet worden gelezen. |
       | Naam van de eigenschap timestamp | Om te bepalen deze waarde, moet u inzicht in de indeling van het bericht van de berichtgegevens die worden verzonden naar de event hub. Deze waarde is de **naam** van de eigenschap specifieke gebeurtenis in de berichtgegevens die u wilt gebruiken als de tijdstempel van de gebeurtenis. De waarde is hoofdlettergevoelig. Als dit veld leeg blijft, de **tijd van de gebeurtenis in de wachtrij plaatsen** in de gebeurtenis bron wordt gebruikt als de tijdstempel van de gebeurtenis. |

1. De toegewezen Time Series Insights consument groepsnaam die u hebt toegevoegd aan uw event hub toevoegen.

1. Selecteer **Maken**.

   Nadat de gebeurtenis bron is gemaakt, begint Time Series Insights automatisch gegevens stromen naar uw omgeving.

## <a name="next-steps"></a>Volgende stappen

* [Gegevenstoegangsbeleid definiëren](time-series-insights-data-access.md) om de gegevens te beveiligen.

* [Gebeurtenissen verzenden](time-series-insights-send-events.md) naar de gebeurtenisbron.

* Toegang tot uw omgeving in de [Verkenner van Time Series Insights](https://insights.timeseries.azure.com).
