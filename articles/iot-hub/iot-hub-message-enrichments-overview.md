---
title: Overzicht van Azure IoT Hub-berichtverrijkingen
description: In dit artikel ziet u de verrijkingen van berichten, die de IoT Hub de mogelijkheid geven om berichten met aanvullende informatie te stempelen voordat de berichten naar het aangewezen eindpunt worden verzonden.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: c3dbd01faf61c164c88f09b0da03c07be4abd187
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75429126"
---
# <a name="message-enrichments-for-device-to-cloud-iot-hub-messages"></a>Berichtverrijkingen voor IoT-hubberichten van device-to-cloud

*Berichtverrijkingen* is de mogelijkheid van de IoT Hub om berichten met aanvullende informatie te *stempelen* voordat de berichten naar het aangewezen eindpunt worden verzonden. Een van de redenen om berichtverrijkingen te gebruiken, is het opnemen van gegevens die kunnen worden gebruikt om downstreamverwerking te vereenvoudigen. Als u bijvoorbeeld telemetrieberichten van apparaten verrijkt met een dubbele apparaattag, kan de belasting van klanten worden verminderd om apparaatdubbele API-oproepen voor deze informatie te maken.

![Stroom van berichtverrijkingen](./media/iot-hub-message-enrichments-overview/message-enrichments-flow.png)

Een berichtverrijking heeft drie belangrijke elementen:

* Verrijkingsnaam of -sleutel

* Een waarde

* Een of meer [eindpunten](iot-hub-devguide-endpoints.md) waarvoor de verrijking moet worden toegepast.

De **sleutel** is een touwtje. Een toets kan alleen alfanumerieke tekens of deze`-`speciale tekens`_`bevatten:`.`koppelteken ( ), onderstrepen ( ) en punt ( ).

De **waarde** kan een van de volgende voorbeelden zijn:

* Elke statische tekenreeks. Dynamische waarden zoals voorwaarden, logica, bewerkingen en functies zijn niet toegestaan. Als u bijvoorbeeld een SaaS-toepassing ontwikkelt die door meerdere klanten wordt gebruikt, u aan elke klant een id toewijzen en die id beschikbaar maken in de toepassing. Wanneer de toepassing wordt uitgevoerd, stempelt IoT Hub de telemetrieberichten van het apparaat met de id van de klant, waardoor het mogelijk is om de berichten voor elke klant anders te verwerken.

* De naam van de IoT-hub die het bericht verzendt. Deze waarde is *$iothubname*.

* Informatie van de apparaattweeling, zoals het pad. Voorbeelden hiervan zijn *$twin.tags.field* en *$twin.tags.latitude*.

   > [!NOTE]
   > Op dit moment worden alleen $iothubname, $twin.tags, $twin.properties.desired en $twin.properties.reported ondersteunde variabelen voor berichtverrijking.

Berichtverrijkingen worden toegevoegd als toepassingseigenschappen aan berichten die naar gekozen eindpunt(en) worden verzonden.  

## <a name="applying-enrichments"></a>Toepassing van verrijkingen

De berichten kunnen afkomstig zijn van elke gegevensbron die wordt ondersteund door [het routeren van IoT Hub-berichten,](iot-hub-devguide-messages-d2c.md)inclusief de volgende voorbeelden:

* telemetrie van het apparaat, zoals temperatuur of druk
* apparaat twin change meldingen - veranderingen in het apparaat twin
* gebeurtenissen tijdens de levenscyclus van het apparaat, zoals wanneer het apparaat wordt gemaakt of verwijderd

U verrijkingen toevoegen aan berichten die naar het ingebouwde eindpunt van een IoT-hub gaan, of berichten die worden doorgestuurd naar aangepaste eindpunten zoals Azure Blob-opslag, een wachtrij voor servicebus of een servicebusonderwerp.

U verrijkingen toevoegen aan berichten die worden gepubliceerd in gebeurtenisraster door het eindpunt als gebeurtenisraster te selecteren. We maken een standaardroute in IoT Hub naar apparaattelemetrie, op basis van uw Event Grid-abonnement. Deze ene route kan al uw Event Grid-abonnementen verwerken. U verrijkingen configureren voor het eindpunt van het gebeurtenisraster nadat u het gebeurtenisrasterabonnement op apparaattelemetrie hebt gemaakt. Zie [Iot Hub en Event Grid](iot-hub-event-grid.md)voor meer informatie.

Verrijkingen worden per eindpunt toegepast. Als u vijf verrijkingen opgeeft die moeten worden gestempeld voor een specifiek eindpunt, worden alle berichten die naar dat eindpunt gaan, voorzien van dezelfde vijf verrijkingen.

Verrijkingen kunnen worden geconfigureerd met de volgende methoden:

| **Methode** | **Opdracht** |
| ----- | -----| 
| Portal | [Azure-portal](https://portal.azure.com) | De [zelfstudie voor het verrijken van berichten bekijken](tutorial-message-enrichments.md) | 
| Azure-CLI   | [az iot hub-signaalverrijking](https://docs.microsoft.com/cli/azure/iot/hub/message-enrichment?view=azure-cli-latest) |
| Azure PowerShell | [Add-AzIotHubMessageEnrichment](https://docs.microsoft.com/powershell/module/az.iothub/add-aziothubmessageenrichment?view=azps-2.8.0) |

Als u berichtverrijkingen toevoegt, wordt geen latentie toegevoegd aan de berichtroutering.

Zie de [zelfstudie voor berichtverrijkingen](tutorial-message-enrichments.md) als u berichtverrijkingen wilt uitproberen

## <a name="limitations"></a>Beperkingen

* U maximaal 10 verrijkingen per IoT-hub toevoegen voor die hubs in de standaard- of basislaag. Voor IoT-hubs in de gratis laag u maximaal 2 verrijkingen toevoegen.

* In sommige gevallen, als u een verrijking toepast met een waarde die is ingesteld op een tag of eigenschap in de apparaattweeling, wordt de waarde gestempeld als een tekenreekswaarde. Als een verrijkingswaarde bijvoorbeeld is ingesteld op $twin.tags.field, worden de berichten gestempeld met de tekenreeks '$twin.tags.field' in plaats van de waarde van dat veld van de tweeling. Dit gebeurt in de volgende gevallen:

   * Uw IoT-hub bevindt zich in de basislaag. IoT-hubs in het basisniveau ondersteunen geen apparaattweelingen.

   * Uw IoT-hub bevindt zich in de standaardlaag, maar het apparaat dat het bericht verzendt, heeft geen apparaattweeling.

   * Uw IoT-hub bevindt zich in de standaardlaag, maar het dubbele pad van het apparaat dat wordt gebruikt voor de waarde van de verrijking, bestaat niet. Als de verrijkingswaarde bijvoorbeeld is ingesteld op $twin.tags.locatie en de apparaattweeling geen locatieeigenschap onder tags heeft, wordt het bericht gestempeld met de tekenreeks '$twin.tags.locatie'. 

* Het kan tot vijf minuten duren voordat updates van een apparaattweeling worden weergegeven in de bijbehorende verrijkingswaarde.

* De totale berichtgrootte, inclusief de verrijkingen, mag niet hoger zijn dan 256 KB. Als een berichtgrootte hoger is dan 256 KB, wordt het bericht in de IoT-hub weergegeven. U [IoT Hub-statistieken](iot-hub-metrics.md) gebruiken om fouten te identificeren en te debuggen wanneer berichten worden verwijderd. U bijvoorbeeld d2c.telemetry.egress.invalid controleren.

* Berichtverrijkingen zijn niet van toepassing op gebeurtenissen voor digitale tweelingverandering (onderdeel van de openbare preview van [IoT Plug and Play).](../iot-pnp/overview-iot-plug-and-play.md)

## <a name="pricing"></a>Prijzen

Berichtverrijkingen zijn gratis beschikbaar. Momenteel worden er kosten in rekening gebracht wanneer u een bericht verzendt naar een IoT-hub. Voor dat bericht worden slechts één keer kosten in rekening gebracht, zelfs als het bericht naar meerdere eindpunten gaat.

## <a name="next-steps"></a>Volgende stappen

Bekijk deze artikelen voor meer informatie over het routeren van berichten naar een IoT-hub:

* [Zelfstudie voor verrijkingen van berichten](tutorial-message-enrichments.md)

* [IoT Hub-berichtroutering gebruiken om device-to-cloudberichten naar verschillende eindpunten te verzenden](iot-hub-devguide-messages-d2c.md)

* [Zelfstudie: Routering van IoT-hub](tutorial-routing.md)
