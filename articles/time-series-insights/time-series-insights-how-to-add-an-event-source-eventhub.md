---
title: Gebeurtenisbron gebeurtenishubs toevoegen - Azure Time Series Insights | Microsoft Documenten
description: Meer informatie over het toevoegen van een gebeurtenisbron van Azure Event Hubs aan uw Time Series Insights-omgeving.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905407"
---
# <a name="add-an-event-hub-event-source-to-your-time-series-insights-environment"></a>Een gebeurtenisbron voor gebeurtenishubs toevoegen aan uw Time Series Insights-omgeving

In dit artikel wordt beschreven hoe u de Azure-portal gebruiken om een gebeurtenisbron toe te voegen die gegevens uit Azure Event Hubs leest aan uw Azure Time Series Insights-omgeving.

> [!NOTE]
> De stappen die in dit artikel worden beschreven, zijn van toepassing op zowel de Time Series Insights GA- als de Time Series Insights Preview-omgevingen.

## <a name="prerequisites"></a>Vereisten

- Maak een Time Series Insights-omgeving zoals beschreven in [Een Azure Time Series Insights-omgeving maken.](./time-series-insights-update-create-environment.md)
- Maak een Event Hub. Lees [De naamruimte van Een gebeurtenishubs en een gebeurtenishub maken met behulp van de Azure-portal.](../event-hubs/event-hubs-create.md)
- De gebeurtenishub moet actieve berichtgebeurtenissen naar de gebeurtenis hebben verzonden. Meer informatie over het [verzenden van gebeurtenissen naar Azure Event Hubs met behulp van het .NET Framework](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md).
- Maak een speciale consumentengroep in de gebeurtenishub waarvan de Time Series Insights-omgeving kan worden gebruikt. Elke Time Series Insights-gebeurtenisbron moet een eigen speciale consumentengroep hebben die niet wordt gedeeld met andere consumenten. Als meerdere lezers gebeurtenissen uit dezelfde consumentengroep consumeren, zullen alle lezers waarschijnlijk fouten vertonen. Er is een limiet van 20 consumentengroepen per eventhub. Lees voor meer informatie de [programmeerhandleiding Event Hubs.](../event-hubs/event-hubs-programming-guide.md)

### <a name="add-a-consumer-group-to-your-event-hub"></a>Een consumentengroep toevoegen aan uw gebeurtenishub

Toepassingen gebruiken consumentengroepen om gegevens uit Azure Event Hubs te halen. Als u gegevens uit uw gebeurtenishub betrouwbaar wilt lezen, biedt u een speciale consumentengroep die alleen wordt gebruikt door deze Time Series Insights-omgeving.

Ga als volgende over een nieuwe consumentengroep in uw eventhub:

1. Zoek en open uw gebeurtenishub-instantie in de [Azure-portal](https://portal.azure.com)in het **deelvenster Overzicht** van de naamruimte van uw gebeurtenishub. Selecteer **Entiteiten > gebeurtenishubs** of zoek uw instantie onder **Naam**.

    [![Naamruimte van de gebeurtenishub openen](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-connect-event-hub-namespace.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-connect-event-hub-namespace.png#lightbox)

1. Selecteer in de instantie gebeurtenishub **entiteiten > consumentengroepen**. Selecteer vervolgens **+ Consumentengroep** om een nieuwe consumentengroep toe te voegen. 

   [![Gebeurtenishub - Een consumentengroep toevoegen](media/time-series-insights-how-to-add-an-event-source-eventhub/add-event-hub-consumer-group.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/add-event-hub-consumer-group.png#lightbox)

   Selecteer anders een bestaande consumentengroep en ga naar de volgende sectie.

1. Voer op de pagina **Consumentengroepen** een nieuwe unieke waarde in voor **Naam**.  Gebruik dezelfde naam wanneer u een nieuwe gebeurtenisbron maakt in de time series Insights-omgeving.

1. Selecteer **Maken**.

## <a name="add-a-new-event-source"></a>Een nieuwe gebeurtenisbron toevoegen

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Zoek uw bestaande Time Series Insights-omgeving. Selecteer in het linkermenu **Alle bronnen**en selecteer vervolgens de omgeving Time Series Insights.

1. Selecteer **Gebeurtenisbronnen**en selecteer **Toevoegen**.

   [![Selecteer onder Gebeurtenisbronnen de knop Toevoegen](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-add-an-event-source.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-add-an-event-source.png#lightbox)

1. Voer een waarde in voor **de bronnaam van gebeurtenissen** `Contoso-TSI-GA-Event-Hub-ES`die uniek is voor deze Time Series Insights-omgeving, zoals .

1. Selecteer **gebeurtenishub**voor **Bron**.

1. Selecteer de juiste waarden voor **de optie Importeren:**

   * Als u een bestaande gebeurtenishub in een van uw abonnementen hebt, selecteert u **Gebeurtenishub gebruiken via beschikbare abonnementen**. Deze optie is de makkelijkste aanpak.

     [![Een optie voor het importeren van gebeurtenisbron selecteren](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-event-hub-select-import-option.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-event-hub-select-import-option.png#lightbox)

    *  In de volgende tabel worden de vereiste eigenschappen voor de optie **Gebeurtenishub gebruiken beschreven vanuit de** optie Beschikbare abonnementen:

       [![Details van abonnements- en gebeurtenishub](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-configure-create-confirm.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-configure-create-confirm.png#lightbox)

       | Eigenschap | Beschrijving |
       | --- | --- |
       | Abonnement | Het abonnement van de gewenste gebeurtenishub-instantie en naamruimte behoort tot. |
       | Event hub-naamruimte | De naamruimte van de gebeurtenishub waartoe de gewenste gebeurtenishub-instantie behoort. |
       | Event Hub-naam | De naam van de gewenste gebeurtenishub-instantie. |
       | Beleidswaarde gebeurtenishub | Selecteer het gewenste beleid voor gedeelde toegang. U het beleid voor gedeelde toegang maken op het tabblad **Configureer configureren.** Elk beleid voor gedeelde toegang heeft een naam, machtigingen die u instelt en toegangssleutels. Het beleid voor gedeelde toegang voor uw gebeurtenisbron *moet* **leesmachtigingen** hebben. |
       | Beleidssleutel gebeurtenishub | Vooraf ingevuld vanuit de geselecteerde beleidswaarde van gebeurtenishub. |

    * Als de gebeurtenishub buiten uw abonnementen staat of als u geavanceerde opties wilt selecteren, selecteert u **Gebeurtenishub-instellingen handmatig opgeven.**

       In de volgende tabel worden de vereiste eigenschappen voor de optie **Gebeurtenishub-instellingen handmatig opgeven beschreven:**
 
       | Eigenschap | Beschrijving |
       | --- | --- |
       | Abonnements-id | Het abonnement van de gewenste gebeurtenishub-instantie en naamruimte behoort tot. |
       | Resourcegroep | De resourcegroep van de gewenste gebeurtenishub-instantie en naamruimte behoort tot. |
       | Event hub-naamruimte | De naamruimte van de gebeurtenishub waartoe de gewenste gebeurtenishub-instantie behoort. |
       | Event Hub-naam | De naam van de gewenste gebeurtenishub-instantie. |
       | Beleidswaarde gebeurtenishub | Selecteer het gewenste beleid voor gedeelde toegang. U het beleid voor gedeelde toegang maken op het tabblad **Configureer configureren.** Elk beleid voor gedeelde toegang heeft een naam, machtigingen die u instelt en toegangssleutels. Het beleid voor gedeelde toegang voor uw gebeurtenisbron *moet* **leesmachtigingen** hebben. |
       | Beleidssleutel gebeurtenishub | De gedeelde toegangssleutel die wordt gebruikt om de toegang tot de naamruimte van de servicebus te verifiëren. Voer hier de primaire of secundaire sleutel in. |

    * Beide opties delen de volgende configuratieopties:

       | Eigenschap | Beschrijving |
       | --- | --- |
       | Event Hub-consumentengroep | De consumentengroep die gebeurtenissen leest vanuit de gebeurtenishub. We raden u ten zeerste aan een speciale consumentengroep te gebruiken voor uw gebeurtenisbron. |
       | Serialisatie-indeling voor gebeurtenissen | Momenteel is JSON de enige beschikbare serialisatie-indeling. Gebeurtenisberichten moeten in deze indeling staan of gegevens kunnen niet worden gelezen. |
       | Naam van de timestamp-eigenschap | Als u deze waarde wilt bepalen, moet u inzicht krijgen in de berichtindeling van de berichtgegevens die naar de gebeurtenishub worden verzonden. Deze waarde is de **naam** van de specifieke gebeurteniseigenschap in de berichtgegevens die u als gebeurtenistijdstempel wilt gebruiken. De waarde is hoofdlettergevoelig. Als deze niet leeg is gelaten, wordt de **gebeurteniswachtrijtijd** in de gebeurtenisbron gebruikt als gebeurtenistijdstempel. |

1. Voeg de speciale time series Insights-groepsnaam toe die u aan uw gebeurtenishub hebt toegevoegd.

1. Selecteer **Maken**.

   Nadat de gebeurtenisbron is gemaakt, wordt Time Series Insights automatisch gestart met het streamen van gegevens naar uw omgeving.

## <a name="next-steps"></a>Volgende stappen

* [Definieer beleid voor gegevenstoegang](time-series-insights-data-access.md) om de gegevens te beveiligen.

* [Gebeurtenissen naar](time-series-insights-send-events.md) de gebeurtenisbron verzenden.

* Toegang tot uw omgeving in de [Time Series Insights explorer.](https://insights.timeseries.azure.com)
