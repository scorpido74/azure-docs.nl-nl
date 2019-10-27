---
title: Uw Azure IoT Central-gegevens visualiseren in een Power BI dash board | Microsoft Docs
description: Gebruik de Power BI-oplossing voor Azure IoT Central om uw IoT Central gegevens te visualiseren en te analyseren.
ms.service: iot-central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 10/4/2019
ms.topic: conceptual
ms.openlocfilehash: de22b4431da61af9dbd22ccc024cbd58b6ae4a89
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72954303"
---
# <a name="visualize-and-analyze-your-azure-iot-central-data-in-a-power-bi-dashboard"></a>Uw Azure IoT Central-gegevens visualiseren en analyseren in een Power BI dash board

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

*Dit onderwerp is van toepassing op beheerders.*

![Power BI solution-pijp lijn](media/howto-connect-powerbi/iot-continuous-data-export.png)

Gebruik de Power BI-oplossing voor Azure IoT Central om een krachtig Power BI dash board te maken voor het bewaken van de prestaties van uw IoT-apparaten. In uw Power BI dash board kunt u het volgende doen:
- Bijhouden hoeveel gegevens uw apparaten na verloop van tijd worden verzonden
- Gegevens volume vergelijken tussen telemetrie, statussen en gebeurtenissen
- Apparaten identificeren die een groot aantal metingen rapporteren
- Historische trends van de metingen van apparaten observeren
- Problematische apparaten identificeren die veel kritieke gebeurtenissen verzenden

Met deze oplossing wordt de pijp lijn zo ingesteld dat de gegevens in uw Azure Blob-opslag account worden gebruikt voor [continue gegevens export](howto-export-data-blob-storage.md). Deze gegevens worden door gegeven aan Azure Functions, Azure Data Factory en Azure SQL Database om de gegevens te verwerken en te transformeren. De uitvoer kan worden gevisualiseerd en geanalyseerd in een Power BI rapport dat u als een PBIX-bestand kunt downloaden. Al deze resources worden gemaakt in uw Azure-abonnement, zodat u elk onderdeel kunt aanpassen aan uw behoeften.

> [!Note] 
> De Power BI-oplossing voor Azure IoT Central werkt met IoT Central-apps die geen ondersteuning bieden voor IoT Plug en Play (preview apps vandaag nog)

## <a name="get-the-power-bi-solution-for-azure-iot-centralhttpsakamsiotcentralpowerbisolutiontemplate-from-microsoft-appsource"></a>Down load de [Power BI-oplossing voor Azure IOT Central](https://aka.ms/iotcentralpowerbisolutiontemplate) van Microsoft AppSource.

## <a name="prerequisites"></a>Vereisten
Voor het instellen van de oplossing is het volgende vereist:
- Toegang tot een Azure-abonnement
- IoT Central toepassing die geen ondersteuning biedt voor IoT Plug en Play (preview-apps vandaag nog)
- Continue gegevens export ingesteld op Azure Blob Storage vanuit uw IoT Central-app
    - Zorg ervoor dat de gegevens indeling Avro is
    - U wordt aangeraden metingen, apparaten en Device-sjabloon stromen in te scha kelen om optimaal gebruik te maken van het Power BI dash board.
    - Meer informatie [over het instellen van continue gegevens export](howto-export-data-blob-storage.md)
- Power BI Desktop (meest recente versie)
- Power BI Pro (als u het dash board met anderen wilt delen)

## <a name="reports"></a>Rapporten

Er worden automatisch twee rapporten gegenereerd. 

Het eerste rapport bevat een historisch overzicht van de metingen die door apparaten worden gerapporteerd en de verschillende soorten metingen en apparaten die het hoogste aantal metingen hebben verzonden.

![Power BI rapport pagina 1](media/howto-connect-powerbi/template-page1-hasdata.PNG)

Het tweede rapport Dives dieper gebeurtenissen en toont een historisch overzicht van de gerapporteerde fouten en waarschuwingen. Daarnaast ziet u op welke apparaten het hoogste aantal gebeurtenissen worden gerapporteerd, evenals specifieke fout gebeurtenissen en waarschuwings gebeurtenissen.

![Power BI rapport pagina 2](media/howto-connect-powerbi/template-page2-hasdata.PNG)

## <a name="architecture"></a>Architectuur
Alle resources die zijn gemaakt, kunnen worden geopend in de Azure Portal. Alles moet zich onder één resource groep bevinden.

![Azure portal-weer gave van resource groep](media/howto-connect-powerbi/azure-deployment.PNG)

De specifieke informatie van elke resource en hoe deze wordt gebruikt, wordt hieronder beschreven.

### <a name="azure-functions"></a>Azure Functions
De functie-app van Azure wordt geactiveerd wanneer een nieuw bestand wordt geschreven naar de Blob-opslag. Met de functies worden de velden in elke meting, apparaten en het bestand met Apparaatinstellingen extra heren en worden er diverse tussenliggende SQL-tabellen ingevuld die door Azure Data Factory worden gebruikt.

### <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory maakt verbinding met de SQL database als een gekoppelde service. Er worden opgeslagen procedure activiteiten uitgevoerd die de gegevens verwerken en opslaan in de analyse tabellen.

### <a name="azure-sql-database"></a>Azure SQL Database
Deze tabellen worden automatisch gemaakt om de standaard rapporten in te vullen. Verken deze schema's in Power BI en u kunt uw eigen visualisaties maken op basis van deze gegevens.

| Tabelnaam |
|------------|
|[Analytics]. Meten|
|[Analytics]. Meldingen|
|[fase]. Meten|
|[Analytics]. Eigenschappen|
|[Analytics]. [PropertyDefinitions]|
|[Analytics]. [MeasurementDefinitions]|
|[Analytics]. Apparaten|
|[Analytics]. [DeviceTemplates]|
|[dbo]. vallen|
|[dbo]. Change tracking|

## <a name="estimated-costs"></a>Geschatte kosten

Hier volgt een schatting van de Azure-kosten (Azure function, Data Factory, Azure SQL) betrokken. Alle prijzen zijn in USD. Houd er rekening mee dat de prijzen per regio verschillen, dus u moet altijd de nieuwste prijzen van de afzonderlijke services opzoeken om de werkelijke prijzen te verkrijgen.
De volgende standaard waarden worden voor u ingesteld in de sjabloon (u kunt deze wijzigen nadat u de instellingen hebt ingesteld):

- Azure Functions: App Service plan S1, $74.40/maand
- Azure SQL S1, ~ $30/maand

We raden u aan om vertrouwd te raken met de diverse prijs opties en wat u kunt aanpassen aan uw behoeften.

## <a name="resources"></a>Bronnen

Ga naar AppSource om de [Power BI-oplossing voor Azure IOT Central](https://aka.ms/iotcentralpowerbisolutiontemplate)op te halen.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u uw gegevens in Power BI kunt visualiseren, is dit de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Apparaten beheren](howto-manage-devices.md)