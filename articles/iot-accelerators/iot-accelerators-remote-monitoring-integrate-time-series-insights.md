---
title: Time Series Insights integreren met remote monitoring - Azure | Microsoft Documenten
description: In deze how-to leert u hoe u Time Series Insights configureert voor een bestaande oplossing voor bewaking op afstand die nog geen Time Series Insights bevat.
author: Philmea
manager: timlt
ms.author: philmea
ms.date: 09/12/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 752529454a5b6293d9cbfdf8378b46947aed5a0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77564641"
---
# <a name="integrate-azure-time-series-insights-with-remote-monitoring"></a>Integreer Azure Time Series Insights met Externe bewaking

Azure Time Series Insights is een volledig beheerde analyse-, opslag- en visualisatieservice voor het beheren van iot-scale tijdreeksgegevens in de cloud. U Time Series Insights gebruiken om tijdreeksgegevens op te slaan en te beheren, gebeurtenissen tegelijkertijd te verkennen en te visualiseren, root-cause-analyses uit te voeren en meerdere sites en assets te vergelijken.

De Remote Monitoring oplossingsversneller biedt nu automatische implementatie en integratie met Time Series Insights. In deze how-to leert u hoe u Time Series Insights configureert voor een bestaande oplossing voor externe bewaking die nog geen Time Series Insights bevat.

> [!NOTE]
> Time Series Insights is momenteel niet beschikbaar in de Azure China-cloud. Nieuwe remote monitoring oplossing sversneller implementaties in de Azure China cloud gebruik Cosmos DB voor alle opslag.

## <a name="prerequisites"></a>Vereisten

Om deze how-to te voltooien, moet u al een oplossing voor externe bewaking hebben geïmplementeerd:

* [De remote monitoring oplossingsversneller implementeren](quickstart-remote-monitoring-deploy.md)

## <a name="create-a-consumer-group"></a>Een consumentengroep maken

Maak een speciale consumentengroep in uw IoT-hub om gegevens te streamen naar Time Series Insights.

> [!NOTE]
> Consumentengroepen worden door toepassingen gebruikt om gegevens uit Azure IoT Hub te halen. Elke consumentengroep staat maximaal vijf outputconsumenten toe. U moet een nieuwe consumentengroep maken voor elke vijf uitgangsputten en u maximaal 32 consumentengroepen maken.

1. Klik in de Azure-portal op de knop Cloud Shell.

1. Voer de volgende opdracht uit om een nieuwe consumentengroep te maken. Gebruik de naam van de IoT-hub in uw implementatie met externe bewaking en de naam van uw implementatie op afstandbewaking als de naam van de brongroep:

```azurecli-interactive
az iot hub consumer-group create --hub-name contosorm30526 --name timeseriesinsights --resource-group ContosoRM
```

## <a name="deploy-time-series-insights"></a>Inzichten in tijdreeksen implementeren

Implementeer vervolgens Time Series Insights als extra bron in uw oplossing voor externe bewaking en verbind deze met de IoT-hub.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. Selecteer Inzicht in een > bron**internet van dingen** > **voor timeseries** **maken.**

    ![Inzichten in nieuwe tijdseries](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/new-time-series-insights.png)

1. Als u uw timeseries insights-omgeving wilt maken, gebruikt u de waarden in de volgende tabel:

    | Instelling | Waarde |
    | ------- | ----- |
    | Naam van omgeving | De volgende screenshot maakt gebruik van de naam **contorosrmtsi**. Kies uw eigen unieke naam wanneer u deze stap voltooit. |
    | Abonnement | Selecteer uw Azure-abonnement in de vervolgkeuzelijst. |
    | Resourcegroep | **Bestaande gebruiken**. Selecteer de naam van uw bestaande externe bewakingsbrongroep. |
    | Locatie | We gebruiken **Oost-VS.** Maak uw omgeving in dezelfde regio als uw oplossing voor externe bewaking, indien mogelijk. |
    | Sku |**S1** |
    | Capaciteit | **1** |

    ![Inzichten in de tijdreeks maken](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/new-time-series-insights-create.png)

1. Klik **op Maken**. Het kan even duren voordat de omgeving wordt gecreëerd.

## <a name="create-event-source"></a>Gebeurtenisbron maken

Maak een nieuwe gebeurtenisbron om verbinding te maken met uw IoT-hub. Zorg ervoor dat u de consumentengroep gebruikt die in de vorige stappen is gemaakt. Time Series Insights vereist dat elke service een speciale consumentengroep heeft die niet door een andere service wordt gebruikt.

1. Navigeer naar uw nieuwe Time Series Insights-omgeving.

1. Selecteer aan de linkerkant **Gebeurtenisbronnen**.

    ![Gebeurtenisbronnen weergeven](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-sources.png)

1. Klik op**toevoegen**.

    ![Gebeurtenisbron toevoegen](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-sources-add.png)

1. Als u uw IoT-hub wilt configureren als een nieuwe gebeurtenisbron, gebruikt u de waarden in de volgende tabel:

    | Instelling | Waarde |
    | ------- | ----- |
    | Naam van gebeurtenisbron | De volgende screenshot maakt gebruik van de naam **contosorm-iot-hub**. Gebruik je eigen unieke naam wanneer je deze stap voltooit. |
    | Bron | **IoT-hub** |
    | Importoptie | **IoT Hub gebruiken via beschikbare abonnementen** |
    | Abonnements-id | Selecteer uw Azure-abonnement in de vervolgkeuzelijst. |
    | Iot-hubnaam | **contosorma57a6**. Gebruik de naam van uw IoT-hub vanuit uw oplossing voor externe bewaking. |
    | Naam van het IoT-hub-beleid | **iothubowner** Controleer of het gebruikte beleid een eigenaarbeleid is. |
    | Iot hub beleidssleutel | Dit veld wordt automatisch ingevuld. |
    | IoT-hub-consumentengroep | **tijdreekseninzichten** |
    | Serialisatie-indeling voor gebeurtenissen | **Json**     | 
    | Naam van de timestamp-eigenschap | Leeg laten |

    ![Gebeurtenisbron maken](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-source-create.png)

1. Klik **op Maken**.

## <a name="configure-the-data-access-policy"></a>Het beleid voor gegevenstoegangs configureren

Voeg uw toepassing en gebruikers toe onder het beleid voor gegevenstoegang in de Azure-portal om ervoor te zorgen dat alle gebruikers die toegang hebben tot uw oplossing voor externe bewaking gegevens kunnen verkennen in de Time Series Insights explorer. 

1. Kies **Resourcegroepen** in de navigatielijst.

1. Kies de **contosorm-brongroep.**

1. Kies **contosormtsi** in de lijst met Azure-bronnen.

1. Kies **Beleid voor gegevenstoegang** om de huidige lijst met roltoewijzingen weer te geven.

1. Kies **Toevoegen** om het deelvenster **Gebruikersregel selecteren te** openen.

   Als u geen machtigingen hebt om rollen toe te wijzen, ziet u de optie **Toevoegen** niet.

1. Selecteer **in** de vervolgkeuzelijst Rol een rol zoals **Reader** en **Contributor**.

1. Selecteer in de lijst **Selecteren** een gebruiker, groep of toepassing. Als u de beveiligings-principal niet in de lijst ziet staan, kunt u tekst typen in het vak **Selecteren** om te zoeken naar weergavenamen, e-mailadressen en object-id's.

1. Kies **Opslaan** om de roltoewijzing te maken. Na enkele ogenblikken krijgt de beveiligingsprincipal de rol toegewezen in het beleid voor gegevenstoegang.

> [!NOTE]
> Als u extra gebruikers toegang moet verlenen tot de Time Series Insights explorer, u deze stappen gebruiken om [gegevenstoegang te verlenen.](../time-series-insights/time-series-insights-data-access.md#grant-data-access)

## <a name="configure-azure-stream-analytics"></a>Azure Stream Analytics configureren 

De volgende stap is het configureren van de microservice Azure Stream Analytics Manager om te stoppen met het verzenden van berichten naar Cosmos DB en ze alleen op te slaan in Time Series Insights. Sla deze stap over als u uw berichten wilt dupliceren in Cosmos DB.

1. Kies **Resourcegroepen** in de navigatielijst.

1. Kies de **contosorm-brongroep.**

1. Zoek de streamingtaak Azure Stream Analytics (ASA) in de lijst met bronnen. De naam van de bron begint met **streamingjobs-**.

1. Klik bovenaan op de knop om de ASA-streamingtaken te stoppen.

1. Bewerk de ASA-query en verwijder de **SELECT,** **INTO**en **VAN-clausules** die wijzen op de berichtenstream in Cosmos DB. Deze clausules moeten onder aan de query staan en lijken op het volgende voorbeeld:

    ```sql
    SELECT
            CONCAT(T.IoTHub.ConnectionDeviceId, ';', CAST(DATEDIFF(millisecond, '1970-01-01T00:00:00Z', T.EventEnqueuedUtcTime) AS nvarchar(max))) as id,
            1 as [doc.schemaVersion],
            'd2cmessage' as [doc.schema],
            T.IoTHub.ConnectionDeviceId as [device.id],
            'device-sensors;v1' as [device.msg.schema],
            'StreamingJobs' as [data.schema],
            DATEDIFF(millisecond, '1970-01-01T00:00:00Z', System.Timestamp) as [device.msg.created],
            DATEDIFF(millisecond, '1970-01-01T00:00:00Z', T.EventEnqueuedUtcTime) as [device.msg.received],
            udf.removeUnusedProperties(T) as Data
    INTO
        Messages
    FROM
        DeviceTelemetry T PARTITION BY PartitionId TIMESTAMP BY T.EventEnqueuedUtcTime
    ```

6. Start de streamingtaken van Azure Stream Analytics opnieuw.

7. Trek de laatste wijzigingen in de microservice van Azure Stream Analytics manager door de volgende opdracht te typen bij de opdrachtprompt:

.NET: 

```cmd/sh
docker pull azureiotpcs/asa-manager-dotnet:1.0.2
```

Java:

```cmd/sh
docker pull azureiotpcs/asa-manager-java:1.0.2
```

## <a name="configure-the-telemetry-microservice"></a>De microservice Telemetrie configureren

Trek de nieuwste microservice telemetrie uit door de volgende opdracht in de opdrachtprompt te typen:

.NET:

```cmd/sh
docker pull azureiotpcs/telemetry-dotnet:1.0.2
```

Java:

```cmd/sh
docker pull azureiotpcs/telemetry-java:1.0.2
```

## <a name="optional-configure-the-web-ui-to-link-to-the-time-series-insights-explorer"></a>*[Optioneel]* De webgebruikersinterface configureren om een koppeling te maken naar de Time Series Insights-verkenner

Om uw gegevens eenvoudig te bekijken in de Time Series Insights-verkenner, raden we u aan de gebruikersinterface aan te werken om eenvoudig een koppeling naar de omgeving te maken. Als u dit wilt doen, trekt u de nieuwste wijzigingen in de webgebruikersinterface uit met de volgende opdracht:

```cmd/sh
docker pull azureiotpcs/pcs-remote-monitoring-webui:1.0.2
```

## <a name="configure-the-environment-variables"></a>De omgevingsvariabelen configureren

Als u de Time Series Insights-integratie wilt voltooien, moet u de omgeving van uw implementatie configureren voor de bijgewerkte microservices.

### <a name="basic-deployments"></a>Basisimplementaties

Configureer de `basic` omgeving van implementatie voor de bijgewerkte microservices.

1. Klik in de Azure-portal op het tabblad **Azure Active Directory** in het linkerdeelvenster.

1. Klik op **App-registraties**.

1. Zoek naar en klik op uw **ContosoRM** applicatie.

1. Navigeer naar > **Instellingentoetsen** en maak vervolgens een nieuwe sleutel voor uw toepassing. **Settings** Zorg ervoor dat u de sleutelwaarde naar een veilige locatie kopieert.

1. Trek de [nieuwste docker componeren yaml bestand](https://github.com/Azure/pcs-cli/tree/5a9b4e0dbe313172eff19236e54a4d461d4f3e51/solutions/remotemonitoring/single-vm) van GitHub repo met behulp van de nieuwste tag. 

1. SSH in de VM door de stappen te volgen die zijn beschreven voor [het maken en gebruiken van SSH-toetsen.](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. Als u `cd /app`verbonden bent, typt u .

1. Voeg de volgende omgevingsvariabelen toe aan elke microservice in het `env-setup` docker samenstellen yaml-bestand en het script in de VM:

    ```sh
    PCS_TELEMETRY_STORAGE_TYPE=tsi
    PCS_TSI_FQDN={TSI Data Access FQDN}
    PCS_AAD_TENANT={AAD Tenant Id}
    PCS_AAD_APPID={AAD application Id}
    PCS_AAD_APPSECRET={AAD application key}
    ```

1. Navigeer naar de **telemetrieservice** en bewerk ook het docker-samenstellingsbestand door dezelfde omgevingsvariabelen hierboven toe te voegen.

1. Navigeer naar de **ASA-managerservice** en bewerk `PCS_TELEMETRY_STORAGE_TYPE`het bestand voor het samenstellen van de docker door .

1. Start de dockercontainers opnieuw met behulp `sudo ./start.sh` van de VM.

> [!NOTE]
> De bovenstaande configuratie van omgevingsvariabelen is geldig voor versies met externe bewaking vóór 1.0.2

### <a name="standard-deployments"></a>Standaardimplementaties

De omgeving `standard` van implementatie configureren voor de bijgewerkte microservices hierboven

1. Voer bij de `kubectl proxy`opdrachtregel uit. Zie voor meer informatie [toegang tot de Kubernetes API](https://kubernetes.io/docs/tasks/access-kubernetes-api/http-proxy-access-api/#using-kubectl-to-start-a-proxy-server).

1. Open de Kubernetes-beheerconsole.

1. Zoek de configuratiekaart om de volgende nieuwe omgevingsvariabelen voor TSI toe te voegen:

    ```yaml
    telemetry.storage.type: "tsi"
    telemetry.tsi.fqdn: "{TSI Data Access FQDN}"
    security.auth.serviceprincipal.secret: "{AAD application service principal secret}"
    ```

4. Bewerk het yaml-bestand voor de inhoudsservice:

    ```yaml
    - name: PCS_AAD_TENANT
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: security.auth.tenant
    - name: PCS_AAD_APPID
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: security.auth.audience
    - name: PCS_AAD_APPSECRET
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: security.auth.serviceprincipal.secret
    - name: PCS_TELEMETRY_STORAGE_TYPE
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.storage.type
    - name: PCS_TSI_FQDN
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.tsi.fqdn
    ```

5. Bewerk het yaml-bestand voor de sjabloon voor asa-beheerservicepod:

    ```yaml
    - name: PCS_TELEMETRY_STORAGE_TYPE
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.storage.type
    ```

## <a name="next-steps"></a>Volgende stappen

* Zie onze zelfstudie over het uitvoeren van een analyse van de [oorzaak](iot-accelerators-remote-monitoring-root-cause-analysis.md)voor meer informatie over het verkennen van uw gegevens en het diagnosticeren van een waarschuwing in de Time Series Insights-verkenner.

* Zie documentatie over de Azure Time Series Insights explorer voor meer informatie over het verkennen en opvragen van gegevens in de Verkenner van Time Series [Insights.](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer)
