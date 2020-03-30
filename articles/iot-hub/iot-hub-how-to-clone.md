---
title: Een Azure IoT-hub klonen
description: Een Azure IoT-hub klonen
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: robinsh
ms.openlocfilehash: c54853717f7e0b234df013e5aee575682d0d3d97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75429157"
---
# <a name="how-to-clone-an-azure-iot-hub-to-another-region"></a>Een Azure IoT-hub klonen naar een andere regio

In dit artikel worden manieren verkend om een IoT-hub te klonen en worden enkele vragen gegeven die u moet beantwoorden voordat u begint. Hier volgen verschillende redenen waarom u een IoT-hub wilt klonen:
 
* U verplaatst uw bedrijf van de ene regio naar de andere, zoals van Europa naar Noord-Amerika (of omgekeerd), en u wilt dat uw bronnen en gegevens geografisch dicht bij uw nieuwe locatie liggen, dus u moet uw hub verplaatsen.

* Je bent bezig met het opzetten van een hub voor een ontwikkel- versus productieomgeving.

* U wilt een aangepaste implementatie van multi-hub hoge beschikbaarheid doen. Zie voor meer informatie de [HA-sectie over de regio van IoT Hub met hoge beschikbaarheid en herstel na noodgevallen.](iot-hub-ha-dr.md#achieve-cross-region-ha)

* U wilt het aantal [partities](iot-hub-scaling.md#partitions) dat is geconfigureerd voor uw hub verhogen. Dit wordt ingesteld wanneer u uw hub voor het eerst maakt en kan niet worden gewijzigd. U de informatie in dit artikel gebruiken om uw hub te klonen en wanneer de kloon wordt gemaakt, verhoogt u het aantal partities.

Als u een hub wilt klonen, hebt u een abonnement met administratieve toegang tot de oorspronkelijke hub nodig. U de nieuwe hub in een nieuwe brongroep en -regio plaatsen, in hetzelfde abonnement als de oorspronkelijke hub of zelfs in een nieuw abonnement. Je gewoon niet dezelfde naam gebruiken omdat de hubnaam wereldwijd uniek moet zijn.

> [!NOTE]
> Op dit moment is er geen functie beschikbaar voor het automatisch klonen van een IoT-hub. Het is vooral een handmatig proces, en dus is vrij foutgevoelig. De complexiteit van het klonen van een hub is direct evenredig met de complexiteit van de hub. Het klonen van een IoT-hub zonder berichtroutering is bijvoorbeeld vrij eenvoudig. Als u berichtroutering toevoegt als slechts één complexiteit, wordt het klonen van de hub op zijn minst een orde van grootte ingewikkelder. Als u ook de resources verplaatst die worden gebruikt voor het routeren van eindpunten, is dit een andere volgorde van magisatuur die ingewikkelder is. 

## <a name="things-to-consider"></a>Dingen om te overwegen

Er zijn verschillende dingen om te overwegen voordat u een IoT-hub kloont.

* Zorg ervoor dat alle functies die beschikbaar zijn op de oorspronkelijke locatie ook beschikbaar zijn op de nieuwe locatie. Sommige services zijn in preview, en niet alle functies zijn overal beschikbaar.

* Verwijder de oorspronkelijke resources niet voordat u de gekloonde versie maakt en verifieert. Zodra u een hub verwijdert, is deze vooraltijd verdwenen en is er geen manier om deze te herstellen om de instellingen of gegevens te controleren om ervoor te zorgen dat de hub correct is gerepliceerd.

* Veel resources vereisen wereldwijd unieke namen, dus u moet verschillende namen gebruiken voor de gekloonde versies. U moet ook een andere naam gebruiken voor de resourcegroep waartoe de gekloonde hub behoort. 

* Gegevens voor de oorspronkelijke IoT-hub worden niet gemigreerd. Dit omvat telemetrieberichten, C2D-opdrachten (cloud-to-device) en taakgerelateerde informatie zoals planningen en geschiedenis. Statistieken en logboekresultaten worden ook niet gemigreerd. 

* Voor gegevens of berichten die naar Azure Storage worden doorgestuurd, u de gegevens in het oorspronkelijke opslagaccount achterlaten, die gegevens overbrengen naar een nieuw opslagaccount in de nieuwe regio of de oude gegevens op hun plaats laten en een nieuw opslagaccount maken op de nieuwe locatie voor de nieuwe gegevens. Zie [Aan de slag met AzCopy](../storage/common/storage-use-azcopy-v10.md)voor meer informatie over het verplaatsen van gegevens in Blob-opslag.

* Gegevens voor gebeurtenishubs en servicebusonderwerpen en -wachtrijen kunnen niet worden gemigreerd. Dit zijn point-in-time gegevens en worden niet opgeslagen nadat de berichten zijn verwerkt.

* U moet downtime plannen voor de migratie. Het klonen van de apparaten naar de nieuwe hub kost tijd. Als u de methode Importeren/exporteren gebruikt, is uit benchmarktests gebleken dat het ongeveer twee uur kan duren om 500.000 apparaten te verplaatsen en vier uur om een miljoen apparaten te verplaatsen. 

* U de apparaten naar de nieuwe hub kopiëren zonder de apparaten af te sluiten of te wijzigen. 

    * Als de apparaten oorspronkelijk waren ingericht met DPS, wordt de verbindingsgegevens die in elk apparaat zijn opgeslagen, bijgewerkt. 
    
    * Anders moet u de methode Importeren/exporteren gebruiken om de apparaten te verplaatsen en vervolgens moeten de apparaten worden gewijzigd om de nieuwe hub te gebruiken. U bijvoorbeeld uw apparaat zo instellen dat de naam van de IoT Hub-host wordt gebruikt van de twee gewenste eigenschappen. Het apparaat neemt die IoT Hub-hostnaam, koppelt het apparaat los van de oude hub en sluit het opnieuw aan op de nieuwe hub.
    
* U moet alle certificaten die u gebruikt bijwerken, zodat u ze gebruiken met de nieuwe bronnen. Ook hebt u waarschijnlijk de hub gedefinieerd in een DNS-tabel ergens - u moet die DNS-informatie bijwerken.

## <a name="methodology"></a>Methodologie

Dit is de algemene methode die we aanbevelen voor het verplaatsen van een IoT-hub van de ene regio naar de andere. Voor berichtroutering gaat dit ervan uit dat de resources niet naar de nieuwe regio worden verplaatst. Zie de sectie [berichtroutering](#how-to-handle-message-routing)voor meer informatie .

   1. Exporteer de hub en de instellingen ervan naar een resourcemanagersjabloon. 
   
   1. Breng de nodige wijzigingen aan in de sjabloon, zoals het bijwerken van alle exemplaren van de naam en de locatie voor de gekloonde hub. Werk de sleutel in de sjabloon voor die bron bij voor resources in de sjabloon die wordt gebruikt voor eindpunten voor het routeren van berichten.
   
   1. Importeer de sjabloon in een nieuwe resourcegroep op de nieuwe locatie. Dit creëert de kloon.

   1. Debug als dat nodig is. 
   
   1. Voeg alles toe dat niet naar de sjabloon is geëxporteerd. 
   
       Consumentengroepen worden bijvoorbeeld niet geëxporteerd naar de sjabloon. U moet de consumentengroepen handmatig aan de sjabloon toevoegen of de [Azure-portal](https://portal.azure.com) gebruiken nadat de hub is gemaakt. Er is een voorbeeld van het toevoegen van één consumentengroep aan een sjabloon in het artikel [Gebruik een Azure Resource Manager-sjabloon om de routevan iot-hub-berichten te configureren.](tutorial-routing-config-message-routing-rm-template.md)
       
   1. Kopieer de apparaten van de oorspronkelijke hub naar de kloon. Dit wordt behandeld in de sectie [Beheer van de apparaten die zijn geregistreerd op de IoT-hub.](#managing-the-devices-registered-to-the-iot-hub)

## <a name="how-to-handle-message-routing"></a>Omgaan met berichtroutering

Als uw hub [aangepaste routering](iot-hub-devguide-messages-read-custom.md)gebruikt, bevat het exporteren van de sjabloon voor de hub de routeringsconfiguratie, maar bevat deze niet de resources zelf. U moet kiezen of u de routeringsresources naar de nieuwe locatie wilt verplaatsen of ze op hun plaats wilt laten en ze "as is" wilt blijven gebruiken. 

Stel dat u een hub in West-US hebt die berichten naar een opslagaccount routert (ook in West-US) en dat u de hub naar Oost-VS wilt verplaatsen. U de hub verplaatsen en nog steeds berichten naar het opslagaccount in West-VS laten leiden, of u de hub verplaatsen en ook het opslagaccount verplaatsen. Er kan een kleine prestatiehit optreden van routeringsberichten tot eindpuntbronnen in een andere regio.

U een hub verplaatsen die berichtroutering vrij eenvoudig gebruikt als u de resources die worden gebruikt voor de routeringseindpunten niet ook verplaatst. 

Als de hub berichtroutering gebruikt, hebt u twee opties. 

1. Verplaats de resources die worden gebruikt voor de routeringseindpunten naar de nieuwe locatie.

    * U moet de nieuwe resources zelf maken, handmatig in de [Azure-portal](https://portal.azure.com) of via het gebruik van Resource Manager-sjablonen. 

    * U moet de naam van alle resources wijzigen wanneer u ze op de nieuwe locatie maakt, omdat ze wereldwijd unieke namen hebben. 
     
    * U moet de bronnamen en de resourcesleutels in de sjabloon van de nieuwe hub bijwerken voordat u de nieuwe hub maakt. De resources moeten aanwezig zijn wanneer de nieuwe hub wordt gemaakt.

1. Verplaats de resources die worden gebruikt voor de routeringseindpunten niet. Gebruik ze "op zijn plaats".

   * In de stap waarin u de sjabloon bewerkt, moet u de sleutels voor elke routeringsbron ophalen en deze in de sjabloon plaatsen voordat u de nieuwe hub maakt. 

   * De hub verwijst nog steeds naar de oorspronkelijke routeringsbronnen en stuurt berichten naar deze bronnen zoals geconfigureerd.

   * U krijgt een kleine prestatiehit omdat de hub en de routeringseindpuntresources zich niet op dezelfde locatie bevinden.

## <a name="prepare-to-migrate-the-hub-to-another-region"></a>Voorbereiden om de hub naar een andere regio te migreren

In deze sectie vindt u specifieke instructies voor het migreren van de hub.

### <a name="find-the-original-hub-and-export-it-to-a-resource-template"></a>Zoek de oorspronkelijke hub en exporteer deze naar een resourcesjabloon.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). 

1. Ga naar **Resourcegroepen** en selecteer de resourcegroep met de hub die u wilt verplaatsen. U ook naar **Resources** gaan en de hub op die manier vinden. Selecteer de hub.

1. Selecteer **Sjabloon exporteren** in de lijst met eigenschappen en instellingen voor de hub. 

   ![Schermafbeelding met de opdracht voor het exporteren van de sjabloon voor de IoT-hub.](./media/iot-hub-how-to-clone/iot-hub-export-template.png)

1. Selecteer **Downloaden** om de sjabloon te downloaden. Sla het bestand ergens op waar u het weer vinden. 

   ![Schermafbeelding met de opdracht voor het downloaden van de sjabloon voor de IoT-hub.](./media/iot-hub-how-to-clone/iot-hub-download-template.png)

### <a name="view-the-template"></a>De sjabloon weergeven 

1. Ga naar de map Downloads (of naar de map die u hebt gebruikt toen u de sjabloon exporteerde) en zoek het zip-bestand. Open het zip-bestand en `template.json`zoek het bestand genaamd . Selecteer deze en selecteer Ctrl+C om de sjabloon te kopiëren. Ga naar een andere map die niet in het zip-bestand staat en plak het bestand (Ctrl+V). Nu u het bewerken.
 
    Het volgende voorbeeld is voor een generieke hub zonder routeringsconfiguratie. Het is een S1 tier hub (met 1 eenheid) genaamd **ContosoTestHub29358** in regio **Westus**. Hier is de geëxporteerde sjabloon.

    ``` json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "IotHubs_ContosoTestHub29358_name": {
                "defaultValue": "ContosoTestHub29358",
                "type": "String"
            }
        },
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Devices/IotHubs",
                "apiVersion": "2018-04-01",
                "name": "[parameters('IotHubs_ContosoTestHub29358_name')]",
                "location": "westus",
                "sku": {
                    "name": "S1",
                    "tier": "Standard",
                    "capacity": 1
                },
                "properties": {
                    "operationsMonitoringProperties": {
                        "events": {
                            "None": "None",
                            "Connections": "None",
                            "DeviceTelemetry": "None",
                            "C2DCommands": "None",
                            "DeviceIdentityOperations": "None",
                            "FileUploadOperations": "None",
                            "Routes": "None"
                        }
                    },
                    "ipFilterRules": [],
                    "eventHubEndpoints": {
                        "events": {
                            "retentionTimeInDays": 1,
                            "partitionCount": 2,
                            "partitionIds": [
                                "0",
                                "1"
                            ],
                            "path": "contosotesthub29358",
                            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
                        },
                        "operationsMonitoringEvents": {
                            "retentionTimeInDays": 1,
                            "partitionCount": 2,
                            "partitionIds": [
                                "0",
                                "1"
                            ],
                            "path": "contosotesthub29358-operationmonitoring",
                            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
                        }
                    },
                    "routing": {
                        "endpoints": {
                            "serviceBusQueues": [],
                            "serviceBusTopics": [],
                            "eventHubs": [],
                            "storageContainers": []
                        },
                        "routes": [],
                        "fallbackRoute": {
                            "name": "$fallback",
                            "source": "DeviceMessages",
                            "condition": "true",
                            "endpointNames": [
                                "events"
                            ],
                            "isEnabled": true
                        }
                    },
                    "storageEndpoints": {
                        "$default": {
                            "sasTtlAsIso8601": "PT1H",
                            "connectionString": "",
                            "containerName": ""
                        }
                    },
                    "messagingEndpoints": {
                        "fileNotifications": {
                            "lockDurationAsIso8601": "PT1M",
                            "ttlAsIso8601": "PT1H",
                            "maxDeliveryCount": 10
                        }
                    },
                    "enableFileUploadNotifications": false,
                    "cloudToDevice": {
                        "maxDeliveryCount": 10,
                        "defaultTtlAsIso8601": "PT1H",
                        "feedback": {
                            "lockDurationAsIso8601": "PT1M",
                            "ttlAsIso8601": "PT1H",
                            "maxDeliveryCount": 10
                        }
                    },
                    "features": "None"
                }
            }
        ]
    }
    ```

### <a name="edit-the-template"></a>De sjabloon bewerken 

U moet enkele wijzigingen aanbrengen voordat u de sjabloon gebruiken om de nieuwe hub in de nieuwe regio te maken. Gebruik [VS-code](https://code.visualstudio.com) of een teksteditor om de sjabloon te bewerken.

#### <a name="edit-the-hub-name-and-location"></a>De naam en locatie van de hub bewerken

1. Verwijder de parameterssectie bovenaan - het is veel eenvoudiger om gewoon de naam van de hub te gebruiken, omdat we niet meerdere parameters zullen hebben. 

    ``` json
        "parameters": {
            "IotHubs_ContosoTestHub29358_name": {
                "defaultValue": "ContosoTestHub29358",
                "type": "String"
            }
        },
    ```

1. Wijzig de naam om de werkelijke (nieuwe) naam te gebruiken in plaats van deze op te halen uit een parameter (die u in de vorige stap hebt verwijderd). 

    Gebruik voor de nieuwe hub de naam van de oorspronkelijke hub plus de *tekenreekskloon* om de nieuwe naam te vormen. Begin met het opruimen van de naam en locatie van de hub.
    
    Oude versie:

    ``` json 
    "name": "[parameters('IotHubs_ContosoTestHub29358_name')]",
    "location": "westus",
    ```
    
    Nieuwe versie: 

    ``` json 
    "name": "ContosoTestHub29358clone",
    "location": "eastus",
    ```

    Vervolgens zult u merken dat de waarden voor **het pad** de oude hubnaam bevatten. Verander ze om de nieuwe te gebruiken. Dit zijn de padwaarden onder **gebeurtenisHubEndpoints** genaamd **gebeurtenissen** en **OperationsMonitoringEvents**.

    Wanneer u klaar bent, moet de sectie eindpunten van de gebeurtenishub er als volgt uitzien:

    ``` json
    "eventHubEndpoints": {
        "events": {
            "retentionTimeInDays": 1,
            "partitionCount": 2,
            "partitionIds": [
                "0",
                "1"
            ],
            "path": "contosotesthub29358clone",
            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
        },
        "operationsMonitoringEvents": {
            "retentionTimeInDays": 1,
            "partitionCount": 2,
            "partitionIds": [
                "0",
                "1"
            ],
            "path": "contosotesthub29358clone-operationmonitoring",
            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
        }
    ```

#### <a name="update-the-keys-for-the-routing-resources-that-are-not-being-moved"></a>De sleutels bijwerken voor de routeringsresources die niet worden verplaatst

Wanneer u de resourcebeheersjabloon exporteert voor een hub die routering heeft geconfigureerd, ziet u dat de sleutels voor deze resources niet zijn opgenomen in de geëxporteerde sjabloon: de plaatsing ervan wordt aangeduid met sterretjes. U moet ze invullen door naar die bronnen in de portal te gaan en de sleutels op te halen **voordat** u de sjabloon van de nieuwe hub importeert en de hub maakt. 

1. Haal de sleutels op die nodig zijn voor een van de routeringsbronnen en zet ze in de sjabloon. U de sleutel(s) ophalen uit de bron in de [Azure-portal.](https://portal.azure.com) 

   Als u bijvoorbeeld berichten naar een opslagcontainer routert, zoekt u het opslagaccount in de portal. Selecteer onder de sectie Instellingen de optie **Toegangssleutels**en kopieer vervolgens een van de toetsen. Zo ziet de sleutel eruit wanneer u de sjabloon voor het eerst exporteert:

   ```json
   "connectionString": "DefaultEndpointsProtocol=https;
   AccountName=fabrikamstorage1234;AccountKey=****",
   "containerName": "fabrikamresults",
   ```

1. Nadat u de accountsleutel voor het opslagaccount hebt opgehaald, plaatst u deze in de sjabloon in de component `AccountKey=****` in de plaats van de sterretjes. 

1. Download de gedeelde toegangssleutel die overeenkomt met de SharedAccessKeyName voor wachtrijen voor servicebussen. Hier is de `SharedAccessKeyName` sleutel en de in de json:

   ```json
   "connectionString": "Endpoint=sb://fabrikamsbnamespace1234.servicebus.windows.net:5671/;
   SharedAccessKeyName=iothubroutes_FabrikamResources;
   SharedAccessKey=****;
   EntityPath=fabrikamsbqueue1234",
   ```

1. Hetzelfde geldt voor de Service Bus Topics en Event Hub verbindingen.

#### <a name="create-the-new-routing-resources-in-the-new-location"></a>De nieuwe routeringsresources op de nieuwe locatie maken

Deze sectie is alleen van toepassing als u de resources verplaatst die door de hub worden gebruikt voor de routeringseindpunten.

Als u de routeringsresources wilt verplaatsen, moet u de resources handmatig instellen op de nieuwe locatie. U de routeringsresources maken met behulp van de [Azure-portal](https://portal.azure.com)of door de resourcemanagersjabloon te exporteren voor elk van de resources die worden gebruikt door de berichtroutering, ze te bewerken en te importeren. Nadat de resources zijn ingesteld, u de sjabloon van de hub importeren (inclusief de routeringsconfiguratie).

1. Maak elke resource die wordt gebruikt door de routering. U dit handmatig doen met de [Azure-portal](https://portal.azure.com)of de resources maken met Resource Manager-sjablonen. Als u sjablonen wilt gebruiken, zijn dit de volgende stappen:

    1. Exporteer deze voor elke resource die door de routering wordt gebruikt naar een resourcemanagersjabloon.
    
    1. Werk de naam en locatie van de resource bij. 

    1. Werk kruisverwijzingen tussen de resources bij. Als u bijvoorbeeld een sjabloon maakt voor een nieuw opslagaccount, moet u de naam van het opslagaccount in die sjabloon en elke andere sjabloon waarop naar verwijst, bijwerken. In de meeste gevallen is de routeringssectie in de sjabloon voor de hub de enige andere sjabloon die naar de bron verwijst. 

    1. Importeer elk van de sjablonen, die elke resource implementeert.

    Zodra de resources die door de routering worden gebruikt, zijn ingesteld en uitgevoerd, u doorgaan.

1. Wijzig in de sjabloon voor de IoT-hub de naam van elk van de routeringsbronnen in de nieuwe naam en werk de locatie indien nodig bij. 

Nu heb je een sjabloon die een nieuwe hub maakt die bijna precies lijkt op de oude hub, afhankelijk van hoe je hebt besloten om de routering te verwerken.

## <a name="move----create-the-new-hub-in-the-new-region-by-loading-the-template"></a>Verplaatsen : de nieuwe hub in het nieuwe gebied maken door de sjabloon te laden

Maak de nieuwe hub op de nieuwe locatie met behulp van de sjabloon. Als u routeringsresources hebt die worden verplaatst, moeten de resources worden ingesteld op de nieuwe locatie en de verwijzingen in de sjabloon die worden bijgewerkt. Als u de routeringsresources niet verplaatst, moeten deze in de sjabloon met de bijgewerkte sleutels staan.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Selecteer **Een resource maken**. 

1. Plaats in het zoekvak 'sjabloonimplementatie' en selecteer Enter.

1. Selecteer **sjabloonimplementatie (implementeren met aangepaste sjablonen)**. Dit brengt u naar een scherm voor de implementatie van de sjabloon. Selecteer **Maken**. U ziet dit scherm:

   ![Schermafbeelding van de opdracht voor het bouwen van uw eigen sjabloon](./media/iot-hub-how-to-clone/iot-hub-custom-deployment.png)

1. Selecteer **Uw eigen sjabloon maken in de editor,** waarmee u uw sjabloon vanuit een bestand uploaden. 

1. Selecteer **Bestand laden**. 

   ![Schermafbeelding van de opdracht voor het uploaden van een sjabloonbestand](./media/iot-hub-how-to-clone/iot-hub-upload-file.png)

1. Blader naar de nieuwe sjabloon die u hebt bewerkt en selecteer deze en selecteer **Openen**. Het laadt uw sjabloon in het bewerkingsvenster. Selecteer **Opslaan**. 

   ![Schermafbeelding van het laden van de sjabloon](./media/iot-hub-how-to-clone/iot-hub-loading-template.png)

1. Vul de volgende velden in.

   **Abonnement**: selecteer het te gebruiken abonnement.

   **Resourcegroep:** maak een nieuwe resourcegroep op een nieuwe locatie. Als u al een nieuwe set hebt ingesteld, u deze selecteren in plaats van een nieuwe te maken.

   **Locatie:** Als u een bestaande resourcegroep hebt geselecteerd, wordt deze ingevuld om de locatie van de resourcegroep te kunnen matchen. Als u een nieuwe resourcegroep hebt gemaakt, is dit de locatie.

   **Ik ben het eens checkbox:** dit zegt in principe dat u akkoord gaat om te betalen voor de bron (s) die u maakt.

1. Selecteer de knop **Aankoop.**

De portal valideert nu uw sjabloon en implementeert uw gekloonde hub. Als u routeringsconfiguratiegegevens hebt, wordt deze opgenomen in de nieuwe hub, maar wordt deze op de bronnen op de vorige locatie gericht.

## <a name="managing-the-devices-registered-to-the-iot-hub"></a>De apparaten beheren die zijn geregistreerd op de IoT-hub

Nu je je kloon operationeel hebt, moet je alle apparaten van de oorspronkelijke hub naar de kloon kopiëren. 

Er zijn meerdere manieren om dit te bereiken. U hebt oorspronkelijk [de Device Provisioning Service (DPS)](/azure/iot-dps/about-iot-dps)gebruikt om de apparaten in te richten, of u hebt dat niet gedaan. Als je dat deed, is dit niet moeilijk. Als je dat niet deed, kan dit erg ingewikkeld zijn. 

Als u DPS niet hebt gebruikt om uw apparaten in te richten, u de volgende sectie overslaan en beginnen met [Importeren/exporteren gebruiken om de apparaten naar de nieuwe hub te verplaatsen.](#using-import-export-to-move-the-devices-to-the-new-hub)

## <a name="using-dps-to-re-provision-the-devices-in-the-new-hub"></a>DPS gebruiken om de apparaten opnieuw in te richten in de nieuwe hub

Zie [Apparaten opnieuw inrichten](../iot-dps/how-to-reprovision.md)als u DPS wilt gebruiken om de apparaten naar de nieuwe locatie te verplaatsen. Wanneer u klaar bent, u de apparaten in de [Azure-portal](https://portal.azure.com) bekijken en controleren of ze zich op de nieuwe locatie bevinden.

Ga naar de nieuwe hub via de [Azure-portal.](https://portal.azure.com) Selecteer uw hub en selecteer **Vervolgens IoT-apparaten**. U ziet de apparaten die opnieuw zijn ingericht op de gekloonde hub. U ook de eigenschappen voor de gekloonde hub bekijken. 

Als u routering hebt geïmplementeerd, moet u testen en ervoor zorgen dat uw berichten correct naar de bronnen worden doorgestuurd.

### <a name="committing-the-changes-after-using-dps"></a>De wijzigingen doorvoeren na het gebruik van DPS

Deze wijziging is vastgelegd door de DPS-service.

### <a name="rolling-back-the-changes-after-using-dps"></a>De wijzigingen terugdraaien na het gebruik van DPS. 

Als u de wijzigingen wilt terugdraaien, moet u de apparaten opnieuw inrichten van de nieuwe hub naar de oude hub.

U bent nu klaar met het migreren van uw hub en zijn apparaten. Je naar [Clean-up](#clean-up)gaan.

## <a name="using-import-export-to-move-the-devices-to-the-new-hub"></a>Import-export gebruiken om de apparaten naar de nieuwe hub te verplaatsen

De toepassing is gericht op .NET Core, zodat u deze uitvoeren op Windows of Linux. U het voorbeeld downloaden, uw verbindingstekenreeksen ophalen, de vlaggen instellen voor welke bits u wilt uitvoeren en uitvoeren. U dit doen zonder ooit het openen van de code.

### <a name="downloading-the-sample"></a>Het voorbeeld downloaden

1. Gebruik de IoT C#-voorbeelden van deze pagina: [Azure IoT-voorbeelden voor C#](https://azure.microsoft.com/resources/samples/azure-iot-samples-csharp/). Download het zip-bestand en rits het uit op uw computer. 

1. De relevante code bevindt zich in ./iot-hub/Samples/service/ImportExportDevicesSample. U hoeft de code niet te bekijken of te bewerken om de toepassing uit te voeren.

1. Als u de toepassing wilt uitvoeren, geeft u drie verbindingstekenreeksen en vijf opties op. U geeft deze gegevens door als opdrachtregelargumenten of gebruikt omgevingsvariabelen of gebruikt een combinatie van beide. We gaan de opties doorgeven als opdrachtregelargumenten en de verbindingstekenreeksen als omgevingsvariabelen. 

   De reden hiervoor is dat de verbindingstekenreeksen lang en onbesuisd zijn en waarschijnlijk niet zullen veranderen, maar u wilt de opties misschien wijzigen en de toepassing meerdere tijd uitvoeren. Als u de waarde van een omgevingsvariabele wilt wijzigen, moet u het opdrachtvenster en Visual Studio of VS Code sluiten, ongeacht welke u gebruikt. 

### <a name="options"></a>Opties

Dit zijn de vijf opties die u opgeeft wanneer u de toepassing uitvoert. We zetten deze zo op de commandolijn.

*   **addDevices** (argument 1) - stel dit in op true als u virtuele apparaten wilt toevoegen die voor u zijn gegenereerd. Deze worden toegevoegd aan de bronhub. Stel ook **numToAdd** (argument 2) in om op te geven hoeveel apparaten u wilt toevoegen. Het maximum aantal apparaten dat u registreren bij een hub is een miljoen. Het doel van deze optie is om te testen - u een specifiek aantal apparaten genereren en deze vervolgens kopiëren naar een andere hub.

*   **copyDevices** (argument 3) - stel dit in op true om de apparaten van de ene hub naar de andere te kopiëren. 

*   **deleteSourceDevices** (argument 4) - stel dit in op true om alle apparaten te verwijderen die zijn geregistreerd op de bronhub. We raden u aan te wachten totdat u zeker weet dat alle apparaten zijn overgedragen voordat u dit uitvoert. Zodra u de apparaten verwijdert, u ze niet meer terugkrijgen.

*   **deleteDestDevices** (argument 5) - stel dit in op true om alle apparaten te verwijderen die zijn geregistreerd op de doelhub (de kloon). U dit doen als u de apparaten meerdere exemplaren meer dan één keer wilt kopiëren. 

De basisopdracht wordt *dotnet run* - dit vertelt .NET om het lokale csproj-bestand te bouwen en het vervolgens uit te voeren. U voegt uw opdrachtregelargumenten toe aan het einde voordat u deze uitvoert. 

Uw opdrachtregel ziet er als volgt uit:

``` console 
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices

    // Add 1000 devices, don't copy them to the other hub, or delete them. 
    // The first argument is true, numToAdd is 50, and the other arguments are false.
    dotnet run true 1000 false false false 

    // Copy the devices you just added to the other hub; don't delete anything.
    // The first argument is false, numToAdd is 0, copy-devices is true, and the delete arguments are both false
    dotnet run false 0 true false false 
```

### <a name="using-environment-variables-for-the-connection-strings"></a>Omgevingsvariabelen gebruiken voor de verbindingstekenreeksen

1. Als u het voorbeeld wilt uitvoeren, hebt u de verbindingstekenreeksen nodig met de oude en nieuwe IoT-hubs en met een opslagaccount dat u gebruiken voor tijdelijke werkbestanden. We slaan de waarden hiervoor op in omgevingsvariabelen.

1. Als u de waarden van de verbindingstekenreeks wilt krijgen, meldt u zich aan bij de [Azure-portal.](https://portal.azure.com) 

1. Plaats de verbindingstekenreeksen ergens waar u ze ophalen, zoals Kladblok. Als u het volgende kopieert, u de verbindingstekenreeksen direct plakken waar ze naartoe gaan. Voeg geen spaties toe rond het gelijkteken of het wijzigt de variabele naam. Ook hebt u geen dubbele aanhalingstekens nodig rond de verbindingstekenreeksen. Als u offertes rond de tekenreeks voor de verbinding met het opslagaccount plaatst, werkt dit niet.

   Voor Windows stelt u de omgevingsvariabelen in:

   ``` console  
   SET IOTHUB_CONN_STRING=<put connection string to original IoT Hub here>
   SET DEST_IOTHUB_CONN_STRING=<put connection string to destination or clone IoT Hub here>
   SET STORAGE_ACCT_CONN_STRING=<put connection string to the storage account here>
   ```
 
   Voor Linux definieert u zo de omgevingsvariabelen:

   ``` console  
   export IOTHUB_CONN_STRING="<put connection string to original IoT Hub here>"
   export DEST_IOTHUB_CONN_STRING="<put connection string to destination or clone IoT Hub here>"
   export STORAGE_ACCT_CONN_STRING="<put connection string to the storage account here>"
   ```

1. Ga voor de iemverbindingen van de IoT-hub naar elke hub in de portal. U zoeken in **Resources naar** de hub. Als u de resourcegroep kent, u naar **Resourcegroepen**gaan, uw resourcegroep selecteren en vervolgens de hub selecteren in de lijst met elementen in die resourcegroep. 

1. Selecteer **Beleid voor gedeelde toegang** in de instellingen voor de hub, selecteer vervolgens **iothubowner** en kopieer een van de verbindingstekenreeksen. Doe hetzelfde voor de doelhub. Voeg ze toe aan de juiste SET-opdrachten.

1. Zoek voor de tekenreeks voor de opslagaccount de opslagaccount in **Resources** of onder de **resourcegroep** en open deze. 
   
1. Selecteer onder de sectie Instellingen de optie **Toegangstoetsen** en kopieer een van de verbindingstekenreeksen. Plaats de verbindingstekenreeks in uw tekstbestand voor de juiste opdracht SET. 

Nu hebt u de omgevingsvariabelen in een bestand met de opdrachten SET en weet u wat uw opdrachtregelargumenten zijn. Laten we het monster nemen.

### <a name="running-the-sample-application-and-using-command-line-arguments"></a>De voorbeeldtoepassing uitvoeren en opdrachtregelargumenten gebruiken

1. Open een opdrachtpromptvenster. Selecteer Windows en `command prompt` typ in om het opdrachtpromptvenster op te halen.

1. Kopieer de opdrachten die de omgevingsvariabelen één voor één instellen en plak ze in het opdrachtpromptvenster en selecteer Enter. Wanneer u klaar bent, typt u `SET` het opdrachtpromptvenster om uw omgevingsvariabelen en hun waarden weer te geven. Nadat u deze hebt gekopieerd naar het opdrachtpromptvenster, hoeft u ze niet meer opnieuw te kopiëren, tenzij u een nieuw opdrachtpromptvenster opent.

1. Wijzig in het opdrachtpromptvenster mappen totdat u zich in ./ImportExportDevicesSample bevindt (waar het bestand ImportExportDevicesSample.csproj bestaat). Typ vervolgens het volgende en voeg de argumenten voor de opdrachtregel toe.

    ``` console
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices
    dotnet run arg1 arg2 arg3 arg4 arg5
    ```

    De dotnet-opdracht bouwt en voert de toepassing uit. Omdat u de opties doorgeeft wanneer u de toepassing uitvoert, u de waarden ervan wijzigen telkens wanneer u de toepassing uitvoert. U het bijvoorbeeld één keer uitvoeren en nieuwe apparaten maken, het vervolgens opnieuw uitvoeren en deze apparaten kopiëren naar een nieuwe hub, enzovoort. U ook alle stappen in dezelfde uitvoering uitvoeren, hoewel we raden u aan geen apparaten te verwijderen totdat u zeker weet dat u klaar bent met het klonen. Hier is een voorbeeld dat 1000 apparaten maakt en deze vervolgens kopieert naar de andere hub.

    ``` console
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices

    // Add 1000 devices, don't copy them to the other hub or delete them. 
    dotnet run true 1000 false false false 

    // Do not add any devices. Copy the ones you just created to the other hub; don't delete anything.
    dotnet run false 0 true false false 
    ```

    Nadat u hebt gecontroleerd of de apparaten zijn gekopieerd, u de apparaten als volgt uit de bronhub verwijderen:

   ``` console
   // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices
   // Delete the devices from the source hub.
   dotnet run false 0 false true false 
   ```

### <a name="running-the-sample-application-using-visual-studio"></a>De voorbeeldtoepassing uitvoeren met Visual Studio

1. Als u de toepassing wilt uitvoeren in Visual Studio, wijzigt u uw huidige map in de map waarin het IoTHubServiceSamples.sln-bestand zich bevindt. Voer deze opdracht vervolgens uit in het opdrachtpromptvenster om de oplossing in Visual Studio te openen. U moet dit doen in hetzelfde opdrachtvenster waar u de omgevingsvariabelen instelt, zodat deze variabelen bekend zijn.

   ``` console       
   IoTHubServiceSamples.sln
   ```
    
1. Klik met de rechtermuisknop op het project *ImportExportDevicesSample* en selecteer **Instellen als opstartproject**.    
    
1. Stel de variabelen boven aan Program.cs in de map ImportExportDevicesSample in voor de vijf opties.

   ``` csharp
   // Add randomly created devices to the source hub.
   private static bool addDevices = true;
   //If you ask to add devices, this will be the number added.
   private static int numToAdd = 0; 
   // Copy the devices from the source hub to the destination hub.
   private static bool copyDevices = false;
   // Delete all of the devices from the source hub. (It uses the IoTHubConnectionString).
   private static bool deleteSourceDevices = false;
   // Delete all of the devices from the destination hub. (Uses the DestIotHubConnectionString).
   private static bool deleteDestDevices = false;
   ```

1. Selecteer F5 om de toepassing uit te voeren. Nadat het hardloopwerk is voltooid, u de resultaten bekijken.

### <a name="view-the-results"></a>De resultaten bekijken 

U de apparaten in de [Azure-portal](https://portal.azure.com) bekijken en controleren of ze zich op de nieuwe locatie bevinden.

1. Ga naar de nieuwe hub via de [Azure-portal.](https://portal.azure.com) Selecteer uw hub en selecteer **Vervolgens IoT-apparaten**. U ziet de apparaten die u zojuist hebt gekopieerd van de oude hub naar de gekloonde hub. U ook de eigenschappen voor de gekloonde hub bekijken. 

1. Controleer op import-/exportfouten door naar het Azure-opslagaccount `devicefiles` in de `ImportErrors.log` [Azure-portal](https://portal.azure.com) te gaan en in de container te zoeken naar de . Als dit bestand leeg is (de grootte is 0), zijn er geen fouten. Als u hetzelfde apparaat meerdere keer probeert te importeren, wordt het apparaat de tweede keer afgewezen en wordt er een foutbericht aan het logboekbestand toegevoegd.

### <a name="committing-the-changes"></a>Het plegen van de wijzigingen 

Op dit moment hebt u uw hub gekopieerd naar de nieuwe locatie en de apparaten gemigreerd naar de nieuwe kloon. Nu moet u wijzigingen aanbrengen, zodat de apparaten werken met de gekloonde hub.

Om de wijzigingen te plegen, zijn hier de stappen die u moet uitvoeren: 

* Werk elk apparaat bij om de naam van de IoT Hub-host te wijzigen om de naam van de IoT Hub-host naar de nieuwe hub te wijzen. U moet dit doen met dezelfde methode die u hebt gebruikt toen u het apparaat voor het eerst ingerichte.

* Wijzig alle toepassingen die u hebt die verwijzen naar de oude hub om naar de nieuwe hub te verwijzen.

* Nadat u klaar bent, moet de nieuwe hub operationeel zijn. De oude hub mag geen actieve apparaten hebben en in een verbroken staat zijn. 

### <a name="rolling-back-the-changes"></a>De wijzigingen terugdraaien

Als u besluit de wijzigingen terug te draaien, zijn hier de stappen die u moet uitvoeren:

* Werk elk apparaat bij om de Hostname van de IoT Hub te wijzigen om de Hostname van de IoT Hub voor de oude hub aan te wijzen. U moet dit doen met dezelfde methode die u hebt gebruikt toen u het apparaat voor het eerst ingerichte.

* Wijzig alle toepassingen die u hebt die verwijzen naar de nieuwe hub om naar de oude hub te verwijzen. Als u bijvoorbeeld Azure Analytics gebruikt, moet u mogelijk uw [Azure Stream Analytics-invoer](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub)opnieuw configureren.

* Verwijder de nieuwe hub. 

* Als u routeringsbronnen hebt, moet de configuratie op de oude hub nog steeds wijzen op de juiste routeringsconfiguratie en moet deze resources worden gebruikt nadat de hub opnieuw is opgestart.

### <a name="checking-the-results"></a>De resultaten controleren 

Als u de resultaten wilt controleren, wijzigt u uw IoT-oplossing om naar uw hub op de nieuwe locatie te wijzen en deze uit te voeren. Met andere woorden, voer dezelfde acties uit met de nieuwe hub die u met de vorige hub hebt uitgevoerd en zorg ervoor dat ze correct werken. 

Als u routering hebt geïmplementeerd, moet u testen en ervoor zorgen dat uw berichten correct naar de bronnen worden doorgestuurd.

## <a name="clean-up"></a>Opruimen

Niet opruimen totdat u er echt zeker van bent dat de nieuwe hub operationeel is en de apparaten correct werken. Zorg er ook voor dat u de routering test als u die functie gebruikt. Wanneer u klaar bent, ruimt u de oude resources op door de volgende stappen uit te voeren:

* Als u dat nog niet hebt gedaan, verwijdert u de oude hub. Hiermee worden alle actieve apparaten uit de hub verwijderd.

* Als u routeringsbronnen hebt die u naar de nieuwe locatie hebt verplaatst, u de oude routeringsbronnen verwijderen.

## <a name="next-steps"></a>Volgende stappen

U hebt een IoT-hub gekloond in een nieuwe hub in een nieuwe regio, compleet met de apparaten. Zie [IE-identiteiten van IoT Hub-apparaten importeren en exporteren in bulk](iot-hub-bulk-identity-mgmt.md)voor meer informatie over het uitvoeren van bulkbewerkingen tegen het identiteitsregister in een IoT-hub.

Zie de volgende artikelen voor meer informatie over IoT Hub en ontwikkeling voor de hub.

* [Handleiding voor IoT Hub-ontwikkelaars](iot-hub-devguide.md)

* [Zelfstudie voor het routeren van IoT-hub](tutorial-routing.md)

* [Overzicht van IoT Hub-apparaatbeheer](iot-hub-device-management-overview.md)

* Als u de voorbeeldtoepassing wilt implementeren, raadpleegt u [.NET Core-toepassingsimplementatie](https://docs.microsoft.com/dotnet/core/deploying/index).
