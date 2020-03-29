---
title: Azure Stream Analytics Edge-taken in Visual Studio
description: In dit artikel wordt beschreven hoe u uw Stream Analytics-taken op IoT Edge-taken maakt met behulp van de tools Voor Stream Analytics voor Visual Studio.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 07f109b3d5539f7cd87a12fb42a36803573c2bdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75354554"
---
# <a name="develop-stream-analytics-edge-jobs-using-visual-studio-tools"></a>Stream Analytics Edge-taken ontwikkelen met behulp van Visual Studio-hulpprogramma's

In deze zelfstudie leert u hoe u Stream Analytics-tools gebruiken voor Visual Studio. U leert hoe u uw Stream Analytics Edge-taken maken, debuggen en het maken van uw Stream Analytics Edge-taken. Nadat u de taak hebt gemaakt en getest, u naar de Azure-portal gaan om deze op uw apparaten te implementeren. 

## <a name="prerequisites"></a>Vereisten

Je hebt de volgende vereisten nodig om deze zelfstudie te voltooien:

* Visual [Studio 2019,](https://visualstudio.microsoft.com/downloads/) [Visual Studio 2015](https://www.visualstudio.com/vs/older-downloads/)of [Visual Studio 2013 Update 4 installeren](https://www.microsoft.com/download/details.aspx?id=45326). Enterprise- (Ultimate/Premium), Professional- en Community-edities worden ondersteund. Express-editie wordt niet ondersteund.  

* Volg de [installatie-instructies](stream-analytics-tools-for-visual-studio-edge-jobs.md) om Stream Analytics-hulpprogramma's voor Visual Studio te installeren.
 
## <a name="create-a-stream-analytics-edge-project"></a>Een Stream Analytics Edge-project maken 

Selecteer **in** > Visual Studio Bestand**Nieuw** > **project**. Navigeer naar de lijst **Sjablonen** aan de linkerkant > **Azure Stream Analytics** > **Stream Analytics Edge** > **Azure Stream Analytics Edge Application Edge uit te breiden.** Geef een naam, locatie en oplossingsnaam op voor uw project en selecteer **OK.**

![Nieuw Stream Analytics Edge-project in Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/new-stream-analytics-edge-project.png)

Nadat het project is gemaakt, navigeert u naar de **Oplossingsverkenner** om de maphiërarchie weer te geven.

![Oplossingsverkenner weergave van de taak Stream Analytics Edge](./media/stream-analytics-tools-for-visual-studio-edge-jobs/edge-project-in-solution-explorer.png)

 
## <a name="choose-the-correct-subscription"></a>Kies het juiste abonnement

1. Selecteer **Server Explorer**in het menu Visual Studio **View** .  

2. Klik met de rechtermuisknop op **Azure** > Selecteer **verbinding maken met Microsoft Azure-abonnement** > en meld u vervolgens aan met uw Azure-account.

## <a name="define-inputs"></a>Invoer opgeven

1. Vouw in de **Solution Explorer**het knooppunt **Inputs** uit, u moet een ingang met de naam **EdgeInput.json zien.** Dubbelklik om de instellingen weer te geven.  

2. Brontype instellen op **gegevensstroom**. Stel vervolgens Bron in op **Edge Hub,** Gebeurtenisserialisatie-indeling op **Json**en Codering op **UTF8**. Optioneel u de naam van de **invoeralias**wijzigen, laten we deze zoals in dit voorbeeld het is. Als u de naam van de invoeralias wijzigt, gebruikt u de naam die u hebt opgegeven bij het definiëren van de query. Selecteer **Opslaan** om de instellingen op te slaan.  
   ![Configuratie van de taakinvoer van Stream Analytics](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-input-configuration.png)
 


## <a name="define-outputs"></a>Uitvoer definiëren

1. Vouw in de **Solution Explorer**het knooppunt **Uitvoer** uit dat een uitvoer met de naam **EdgeOutput.json**moet zien. Dubbelklik om de instellingen weer te geven.  

2. Zorg ervoor dat Sink wordt ingesteld op **Edge Hub,** stel gebeurtenisserialisatie-indeling in **op Json,** stel Codering in op **UTF8**en stel **Opmaakarray**in . Optioneel u de naam van de **uitvoeralias**wijzigen, laten we deze laten zoals in dit voorbeeld het is. Als u de naam van de uitvoeralias wijzigt, gebruikt u de naam die u hebt opgegeven bij het definiëren van de query. Selecteer **Opslaan** om de instellingen op te slaan. 
   ![Configuratie van de taakuitvoer van Stream Analytics](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-output-configuration.png)
 
## <a name="define-the-transformation-query"></a>De transformatiequery definiëren

Stream Analytics-taken die zijn geïmplementeerd in de IoT Edge-omgevingen van Stream Analytics ondersteunen het grootste deel van [de naslagtaalverwijzing](https://msdn.microsoft.com/azure/stream-analytics/reference/stream-analytics-query-language-reference?f=255&MSPPError=-2147217396)van Stream Analytics. De volgende bewerkingen worden echter nog niet ondersteund voor Stream Analytics Edge-taken: 


|**Categorie**  | **Opdracht**  |
|---------|---------|
|Andere marktdeelnemers | <ul><li>PARTITIE DOOR</li><li>TIMESTAMP DOOR OVER</li><li>JavaScript UDF</li><li>Door de gebruiker gedefinieerde aggregaten (UDA)</li><li>GetMetadataPropertyValue</li><li>Meer dan 14 aggregaten in één stap gebruiken</li></ul>   |

Wanneer u een Stream Analytics Edge-taak maakt in de portal, waarschuwt de compiler u automatisch als u geen ondersteunde operator gebruikt.

Definieer vanuit uw Visual Studio de volgende transformatiequery in de queryeditor **(script.asaql-bestand)**

```sql
SELECT * INTO EdgeOutput
FROM EdgeInput 
```

## <a name="test-the-job-locally"></a>Test de taak lokaal

Als u de query lokaal wilt testen, moet u de voorbeeldgegevens uploaden. U voorbeeldgegevens ophalen door registratiegegevens uit de [GitHub-opslagplaats](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/Registration.json) te downloaden en op te slaan op uw lokale computer. 

1. Als u voorbeeldgegevens wilt uploaden, klikt u met de rechtermuisknop op het bestand **EdgeInput.json** en kiest u **Lokale invoer toevoegen**  

2. Blader in het pop-upvenster > **Blader door** de voorbeeldgegevens van uw lokale pad > Selecteer **Opslaan**.
   ![Configuratie van lokale invoer in Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-local-input-configuration.png)
 
3. Een bestand met de naam **local_EdgeInput.json** wordt automatisch toegevoegd aan uw invoermap.  
4. U het lokaal uitvoeren of indienen bij Azure. Als u de query wilt testen, selecteert u **Lokaal uitvoeren**.  
   ![Opties voor het uitvoeren van vacatures voor Stream Analytics in Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-visual-stuidio-run-options.png)
 
5. In het opdrachtpromptvenster wordt de status van de taak weergegeven. Wanneer de taak succesvol wordt uitgevoerd, wordt een map gemaakt die lijkt op '2018-02-23-11-31-42' in uw projectmappad 'Visual Studio 2015\Projects\MyASAEdgejob\MyASAEdgejob\ASALocalRun\2018-02-23-11-31-42". Navigeer naar het mappad om de resultaten in de lokale map weer te geven:

   U zich ook aanmelden bij de Azure-portal en controleren of de taak is gemaakt. 

   ![Map met het resultaat van de taak Stream Analytics](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-job-result-folder.png)

## <a name="submit-the-job-to-azure"></a>De taak verzenden naar Azure

1. Voordat u de taak indient bij Azure, moet u verbinding maken met uw Azure-abonnement. Open **Server Explorer** > met de rechtermuisknop op **Azure** > **Connect to Microsoft Azure-abonnement** klikt > zich aanmelden bij uw Azure-abonnement.  

2. Als u de taak wilt verzenden naar Azure, navigeert u naar de queryeditor > selecteert **u Verzenden naar Azure**.  

3. Er wordt een pop-upvenster geopend. Kies ervoor om een bestaande Stream Analytics Edge-taak bij te werken of een nieuwe taak te maken. Wanneer u een bestaande taak bijwerkt, wordt alle taakconfiguratie vervangen, in dit scenario publiceert u een nieuwe taak. Selecteer **Een nieuwe Azure Stream Analytics-taak maken** > een naam voor uw taak invoeren, iets als **MyASAEdgeJob** > het vereiste **abonnement,** **resourcegroep**en **locatie** > Selecteer **Verzenden kiezen**.

   ![Stream Analytics-taak verzenden naar Azure vanuit Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/submit-stream-analytics-job-to-azure.png)
 
   Nu is je Stream Analytics Edge-taak gemaakt. U verwijzen naar de [zelfstudie Taken uitvoeren op IoT Edge](stream-analytics-edge.md) om te leren hoe u deze op uw apparaten implementeren. 

## <a name="manage-the-job"></a>De taak beheren 

U de status van de taak en het taakdiagram bekijken in de Server Explorer. Breid vanuit **Stream Analytics** in **Server Explorer**het abonnement en de resourcegroep uit waarin u de taak Stream Analytics Edge hebt geïmplementeerd. U de MyASAEdge-taak bekijken met de status **Gemaakt**. Vouw uw taakknooppunt uit en dubbelklik erop om de taakweergave te openen.

![Opties voor taakbeheer server explorer](./media/stream-analytics-tools-for-visual-studio-edge-jobs/server-explorer-options.png)
 
In het venster taakweergave u bewerkingen uitvoeren, zoals het vernieuwen van de taak, het verwijderen van de taak en het openen van de taak vanuit azure-portal.

![Taakdiagram en andere opties in Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/job-diagram-and-other-options.png) 

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over Azure IoT Edge](../iot-edge/about-iot-edge.md)
* [ASA op IoT Edge-zelfstudie](../iot-edge/tutorial-deploy-stream-analytics.md)
* [Feedback sturen naar het team met behulp van deze enquête](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) 
