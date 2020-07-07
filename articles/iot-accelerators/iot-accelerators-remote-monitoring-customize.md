---
title: De gebruikers interface van de oplossing voor externe controle aanpassen-Azure | Microsoft Docs
description: Dit artikel bevat informatie over hoe u toegang kunt krijgen tot de bron code voor de gebruikers interface van de externe bewakings versneller en een aantal aanpassingen maakt.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/09/2018
ms.topic: conceptual
ms.openlocfilehash: eb3d5fea68b5b1b6e648943cb3dbaab5857e9e07
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "68608002"
---
# <a name="customize-the-remote-monitoring-solution-accelerator"></a>De oplossings versneller voor externe controle aanpassen

In dit artikel vindt u informatie over hoe u toegang kunt krijgen tot de bron code en hoe u de gebruikers interface van de externe bewakings verwerkings oplossing aanpassen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-a-local-development-environment-for-the-ui"></a>Een lokale ontwikkel omgeving voorbereiden voor de gebruikers interface

De code van de gebruikers interface voor de externe controle oplossing wordt geïmplementeerd met behulp van het React.js-Framework. U vindt de bron code in de [Azure-IOT-PCs-Remote-Monitoring-webui github-](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) opslag plaats.

Als u wijzigingen wilt aanbrengen in de gebruikers interface, kunt u een kopie van het bestand lokaal uitvoeren. Voor het volt ooien van acties zoals het ophalen van telemetrie, maakt de lokale kopie verbinding met een geïmplementeerd exemplaar van de oplossing.

De volgende stappen beschrijven het proces voor het instellen van een lokale omgeving voor UI-ontwikkeling:

1. Implementeer een **basis** exemplaar van de oplossings versneller met de **PCs** -cli. Noteer de naam van uw implementatie en de referenties die u hebt ingevoerd voor de virtuele machine. Zie [implementeren met de CLI](iot-accelerators-remote-monitoring-deploy-cli.md)voor meer informatie.

1. Als u SSH-toegang wilt inschakelen voor de virtuele machine die als host fungeert voor de micro Services in uw oplossing, gebruikt u de Azure Portal of de Azure Cloud Shell. Bijvoorbeeld:

    ```azurecli-interactive
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

    Schakel SSH-toegang alleen in tijdens testen en ontwikkeling. Als u SSH inschakelt, [moet u deze uitschakelen zodra u klaar bent met het gebruik ervan](../security/fundamentals/network-best-practices.md#disable-rdpssh-access-to-virtual-machines).

1. Gebruik de Azure Portal of de Azure Cloud Shell om de naam en het open bare IP-adres van uw virtuele machine te vinden. Bijvoorbeeld:

    ```azurecli-interactive
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. Gebruik SSH om verbinding te maken met uw virtuele machine. Gebruik het IP-adres uit de vorige stap en de referenties die u hebt gegeven tijdens het uitvoeren van **pc's** voor het implementeren van de oplossing. De `ssh` opdracht is beschikbaar in de Azure Cloud shell.

1. Als u de lokale UX wilt toestaan verbinding te maken, voert u de volgende opdrachten uit in de bash-shell in de virtuele machine:

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. Nadat de opdracht is voltooid en de website wordt gestart, kunt u de verbinding met de virtuele machine verbreken.

1. Bewerk in uw lokale kopie van de opslag plaats [Azure-IOT-PCs-Remote-Monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) het **. env** -bestand om de URL van uw geïmplementeerde oplossing toe te voegen:

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

1. Ga bij een opdracht prompt naar de lokale kopie van de `azure-iot-pcs-remote-monitoring-webui` map.

1. Voer de volgende opdrachten uit om de vereiste bibliotheken te installeren en de gebruikers interface lokaal uit te voeren:

    ```cmd/sh
    npm install
    npm start
    ```

1. Met de vorige opdracht wordt de gebruikers interface lokaal uitgevoerd op http: \/ /localhost: 3000/dash board. U kunt de code bewerken terwijl de site wordt uitgevoerd en de update dynamisch weer geven.

## <a name="customize-the-layout"></a>De lay-out aanpassen

Elke pagina in de oplossing voor controle op afstand bestaat uit een set besturings elementen, aangeduid als *panels* in de bron code. De **Dashboard** pagina bestaat uit vijf deel Vensters: overzicht, kaart, waarschuwingen, telemetrie en analyses. U vindt de bron code waarmee elke pagina en de bijbehorende panelen worden gedefinieerd in de [pc's-externe controle-webui github-](https://github.com/Azure/pcs-remote-monitoring-webui) opslag plaats. De code die de **Dashboard** pagina, de indeling en de panelen op de pagina definieert, bevindt zich bijvoorbeeld in de map [src/Components/pages/dash board](https://github.com/Azure/pcs-remote-monitoring-webui/tree/master/src/components/pages/dashboard) .

Omdat de panelen hun eigen indeling en grootte beheren, kunt u eenvoudig de lay-out van een pagina wijzigen. Breng de volgende wijzigingen aan in het **page content** -element in het `src/components/pages/dashboard/dashboard.js` bestand:

* Hiermee wordt de positie van de deel Vensters kaart en telemetrie gewisseld.
* De relatieve breedten van de deel Vensters kaart en analyse wijzigen.

```javascript
<PageContent className="dashboard-container">
  <Grid>
    <Cell className="col-1 devices-overview-cell">
      <OverviewPanel
        activeDeviceGroup={activeDeviceGroup}
        openWarningCount={openWarningCount}
        openCriticalCount={openCriticalCount}
        onlineDeviceCount={onlineDeviceCount}
        offlineDeviceCount={offlineDeviceCount}
        isPending={analyticsIsPending || devicesIsPending}
        error={deviceGroupError || devicesError || analyticsError}
        t={t} />
    </Cell>
    <Cell className="col-6">
      <TelemetryPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        telemetry={telemetry}
        isPending={telemetryIsPending}
        lastRefreshed={lastRefreshed}
        error={deviceGroupError || telemetryError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <AlertsPanel
        alerts={currentActiveAlertsWithName}
        isPending={analyticsIsPending || rulesIsPending}
        error={rulesError || analyticsError}
        t={t}
        deviceGroups={deviceGroups} />
    </Cell>
    <Cell className="col-4">
      <PanelErrorBoundary msg={t('dashboard.panels.map.runtimeError')}>
        <MapPanel
          analyticsVersion={analyticsVersion}
          azureMapsKey={azureMapsKey}
          devices={devices}
          devicesInAlert={devicesInAlert}
          mapKeyIsPending={azureMapsKeyIsPending}
          isPending={devicesIsPending || analyticsIsPending}
          error={azureMapsKeyError || devicesError || analyticsError}
          t={t} />
      </PanelErrorBoundary>
    </Cell>
    <Cell className="col-6">
      <AnalyticsPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        topAlerts={topAlertsWithName}
        alertsPerDeviceId={alertsPerDeviceType}
        criticalAlertsChange={criticalAlertsChange}
        isPending={analyticsIsPending || rulesIsPending || devicesIsPending}
        error={devicesError || rulesError || analyticsError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    {
      Config.showWalkthroughExamples &&
      <Cell className="col-4">
        <ExamplePanel t={t} />
      </Cell>
    }
  </Grid>
</PageContent>
```

![Indeling van het deel venster wijzigen](./media/iot-accelerators-remote-monitoring-customize/layout.png)

U kunt ook meerdere exemplaren van hetzelfde paneel of meerdere versies toevoegen als u [een paneel dupliceert en aanpast](#duplicate-and-customize-an-existing-control). In het volgende voor beeld ziet u hoe u twee exemplaren van het deel venster telemetrie toevoegt. Bewerk het bestand om deze wijzigingen aan te brengen `src/components/pages/dashboard/dashboard.js` :

```javascript
<PageContent className="dashboard-container">
  <Grid>
    <Cell className="col-1 devices-overview-cell">
      <OverviewPanel
        activeDeviceGroup={activeDeviceGroup}
        openWarningCount={openWarningCount}
        openCriticalCount={openCriticalCount}
        onlineDeviceCount={onlineDeviceCount}
        offlineDeviceCount={offlineDeviceCount}
        isPending={analyticsIsPending || devicesIsPending}
        error={deviceGroupError || devicesError || analyticsError}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <TelemetryPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        telemetry={telemetry}
        isPending={telemetryIsPending}
        lastRefreshed={lastRefreshed}
        error={deviceGroupError || telemetryError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <TelemetryPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        telemetry={telemetry}
        isPending={telemetryIsPending}
        lastRefreshed={lastRefreshed}
        error={deviceGroupError || telemetryError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <AlertsPanel
        alerts={currentActiveAlertsWithName}
        isPending={analyticsIsPending || rulesIsPending}
        error={rulesError || analyticsError}
        t={t}
        deviceGroups={deviceGroups} />
    </Cell>
    <Cell className="col-4">
      <PanelErrorBoundary msg={t('dashboard.panels.map.runtimeError')}>
        <MapPanel
          analyticsVersion={analyticsVersion}
          azureMapsKey={azureMapsKey}
          devices={devices}
          devicesInAlert={devicesInAlert}
          mapKeyIsPending={azureMapsKeyIsPending}
          isPending={devicesIsPending || analyticsIsPending}
          error={azureMapsKeyError || devicesError || analyticsError}
          t={t} />
      </PanelErrorBoundary>
    </Cell>
    <Cell className="col-6">
      <AnalyticsPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        topAlerts={topAlertsWithName}
        alertsPerDeviceId={alertsPerDeviceType}
        criticalAlertsChange={criticalAlertsChange}
        isPending={analyticsIsPending || rulesIsPending || devicesIsPending}
        error={devicesError || rulesError || analyticsError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    {
      Config.showWalkthroughExamples &&
      <Cell className="col-4">
        <ExamplePanel t={t} />
      </Cell>
    }
  </Grid>
</PageContent>
```

U kunt vervolgens verschillende telemetrie in elk deel venster bekijken:

![Meerdere telemetrie-panelen](./media/iot-accelerators-remote-monitoring-customize/multiple-telemetry.png)

## <a name="duplicate-and-customize-an-existing-control"></a>Een bestaand besturings element dupliceren en aanpassen

In de volgende stappen wordt beschreven hoe u een bestaand paneel dupliceert, wijzigt en hoe u de gewijzigde versie gebruikt. In de stappen wordt het deel venster **waarschuwingen** als voor beeld gebruikt:

1. Maak in uw lokale kopie van de opslag plaats een kopie van de map met **waarschuwingen** in de `src/components/pages/dashboard/panels` map. Geef de nieuwe kopie **cust_alerts**een naam.

1. Bewerk in het **alertsPanel.js** -bestand in de map **cust_alerts** de naam van de klasse **CustAlertsPanel**:

    ```javascript
    export class CustAlertsPanel extends Component {
    ```

1. Voeg de volgende regel toe aan het `src/components/pages/dashboard/panels/index.js` bestand:

    ```javascript
    export * from './cust_alerts';
    ```

1. Vervangen `alertsPanel` door `CustAlertsPanel` in het `src/components/pages/dashboard/dashboard.js` bestand:

    ```javascript
    import {
      OverviewPanel,
      CustAlertsPanel,
      TelemetryPanel,
      KpisPanel,
      MapPanel,
      transformTelemetryResponse,
      chartColors
    } from './panels';

    ...

    <Cell className="col-3">
      <CustAlertsPanel
        alerts={currentActivealertsWithName}
        isPending={kpisIsPending || rulesIsPending}
        error={rulesError || kpisError}
        t={t} />
    </Cell>
    ```

U hebt nu het deel venster met de oorspronkelijke **waarschuwingen** vervangen door een kopie met de naam **CustAlerts**. Deze kopie is hetzelfde als het origineel. U kunt de kopie nu wijzigen. Als u bijvoorbeeld de volg orde van de kolommen in het paneel **waarschuwingen** wilt wijzigen:

1. Open het `src/components/pages/dashboard/panels/cust_alerts/alertsPanel.js`-bestand.

1. Wijzig de kolom definities, zoals wordt weer gegeven in het volgende code fragment:

    ```javascript
    this.columnDefs = [
      rulesColumnDefs.severity,
      {
        headerName: 'rules.grid.count',
        field: 'count'
      },
      {
        ...rulesColumnDefs.ruleName,
        minWidth: 200
      },
      rulesColumnDefs.explore
    ];
    ```

In de volgende scherm afbeelding ziet u de nieuwe versie van het paneel **waarschuwingen** :

![waarschuwingen paneel bijgewerkt](./media/iot-accelerators-remote-monitoring-customize/reorder-columns.png)

## <a name="customize-the-telemetry-chart"></a>De telemetrie-grafiek aanpassen

De bestanden in de `src/components/pages/dashboard/panels/telemtry` map definiëren de telemetrie-grafiek op de **Dashboard** pagina. De gebruikers interface haalt de telemetrie op uit de back-end van de oplossing in het `src/services/telemetryService.js` bestand. De volgende stappen laten zien hoe u de periode van 15 tot 5 minuten kunt wijzigen die in het telemetrie-diagram wordt weer gegeven:

1. Zoek in het `src/services/telemetryService.js` bestand de functie met de naam **getTelemetryByDeviceIdP15M**. Maak een kopie van deze functie en wijzig de kopie als volgt:

    ```javascript
    static getTelemetryByDeviceIdP5M(devices = []) {
      return TelemetryService.getTelemetryByMessages({
        from: 'NOW-PT5M',
        to: 'NOW',
        order: 'desc',
        devices
      });
    }
    ```

1. Als u deze nieuwe functie wilt gebruiken om de telemetrie-grafiek te vullen, opent u het `src/components/pages/dashboard/dashboard.js` bestand. Zoek de regel die de telemetrie-stroom initialiseert en wijzig deze als volgt:

    ```javascript
    const getTelemetryStream = ({ deviceIds = [] }) => TelemetryService.getTelemetryByDeviceIdP5M(deviceIds)
    ```

De telemetrie-grafiek bevat nu de vijf minuten van telemetriegegevens:

![Telemetrie-grafiek met één dag](./media/iot-accelerators-remote-monitoring-customize/telemetry-period.png)

## <a name="add-a-new-kpi"></a>Een nieuwe KPI toevoegen

Op de **Dashboard** pagina worden kpi's weer gegeven in het deel venster **analyse** . Deze Kpi's worden in het `src/components/pages/dashboard/dashboard.js` bestand berekend. De Kpi's worden weer gegeven door het `src/components/pages/dashboard/panels/analytics/analyticsPanel.js` bestand. In de volgende stappen wordt beschreven hoe u een nieuwe KPI-waarde op de **Dashboard** pagina berekent en weergeeft. Het voor beeld dat wordt weer gegeven, is het toevoegen van een nieuwe percentage wijziging in de KPI waarschuwingen:

1. Open het `src/components/pages/dashboard/dashboard.js`-bestand. Wijzig het **initialState** -object zodanig dat een eigenschap **warningAlertsChange** als volgt wordt toegevoegd:

    ```javascript
    const initialState = {
      ...

      // Analytics data
      analyticsVersion: 0,
      currentActiveAlerts: [],
      topAlerts: [],
      alertsPerDeviceId: {},
      criticalAlertsChange: 0,
      warningAlertsChange: 0,
      analyticsIsPending: true,
      analyticsError: null

      ...
    };
    ```

1. Wijzig het **currentAlertsStats** -object zodat **totalWarningCount** als eigenschap moet worden toegevoegd:

    ```javascript
    return {
      openWarningCount: (acc.openWarningCount || 0) + (isWarning && isOpen ? 1 : 0),
      openCriticalCount: (acc.openCriticalCount || 0) + (isCritical && isOpen ? 1 : 0),
      totalWarningCount: (acc.totalWarningCount || 0) + (isWarning ? 1 : 0),
      totalCriticalCount: (acc.totalCriticalCount || 0) + (isCritical ? 1 : 0),
      alertsPerDeviceId: updatedAlertsPerDeviceId
    };
    ```

1. De nieuwe KPI berekenen. De berekening voor het aantal kritieke waarschuwingen zoeken. Dupliceer de code en wijzig de kopie als volgt:

    ```javascript
    // ================== Warning Alerts Count - START
    const currentWarningAlerts = currentAlertsStats.totalWarningCount;
    const previousWarningAlerts = previousAlerts.reduce(
      (cnt, { severity }) => severity === Config.ruleSeverity.warning ? cnt + 1 : cnt,
      0
    );
    const warningAlertsChange = ((currentWarningAlerts - previousWarningAlerts) / currentWarningAlerts * 100).toFixed(2);
    // ================== Warning Alerts Count - END
    ```

1. Voeg de nieuwe **warningAlertsChange** -KPI toe aan de KPI-stroom:

    ```javascript
    return ({
      analyticsIsPending: false,
      analyticsVersion: this.state.analyticsVersion + 1,

      // Analytics data
      currentActiveAlerts,
      topAlerts,
      criticalAlertsChange,
      warningAlertsChange,
      alertsPerDeviceId: currentAlertsStats.alertsPerDeviceId,

      ...
    });
    ```

1. De nieuwe **warningAlertsChange** -KPI toevoegen aan de status gegevens die worden gebruikt om de gebruikers interface weer te geven:

    ```javascript
    const {
      ...

      analyticsVersion,
      currentActiveAlerts,
      topAlerts,
      alertsPerDeviceId,
      criticalAlertsChange,
      warningAlertsChange,
      analyticsIsPending,
      analyticsError,

      ...
    } = this.state;
    ```

1. De gegevens bijwerken die zijn door gegeven aan het paneel Kpi's:

    ```javascript
    <AnalyticsPanel
      timeSeriesExplorerUrl={timeSeriesParamUrl}
      topAlerts={topAlertsWithName}
      alertsPerDeviceId={alertsPerDeviceType}
      criticalAlertsChange={criticalAlertsChange}
      warningAlertsChange={warningAlertsChange}
      isPending={analyticsIsPending || rulesIsPending || devicesIsPending}
      error={devicesError || rulesError || analyticsError}
      theme={theme}
      colors={chartColorObjects}
      t={t} />
    ```

U hebt nu de wijzigingen in het `src/components/pages/dashboard/dashboard.js` bestand voltooid. In de volgende stappen worden de wijzigingen beschreven die in het bestand moeten worden aangebracht `src/components/pages/dashboard/panels/analytics/analyticsPanel.js` om de nieuwe KPI weer te geven:

1. Wijzig de volgende regel code om de nieuwe KPI-waarde als volgt op te halen:

    ```javascript
    const { t, isPending, criticalAlertsChange, warningAlertsChange, alertsPerDeviceId, topAlerts, timeSeriesExplorerUrl, error } = this.props;
    ```

1. Wijzig de opmaak zodat de nieuwe KPI-waarde als volgt wordt weer gegeven:

    ```javascript
    <div className="analytics-cell">
      <div className="analytics-header">{t('dashboard.panels.analytics.criticalAlerts')}</div>
      <div className="critical-alerts">
        {
          !showOverlay &&
            <div className="analytics-percentage-container">
              <div className="analytics-value">{ !isNaN(criticalAlertsChange) ? criticalAlertsChange : 0 }</div>
              <div className="analytics-percentage-sign">%</div>
            </div>
        }
      </div>
      <div className="critical-alerts">
        {
          !showOverlay &&
            <div className="analytics-percentage-container">
              <div className="analytics-value">{ !isNaN(warningAlertsChange) ? warningAlertsChange : 0 }</div>
              <div className="analytics-percentage-sign">%</div>
            </div>
        }
      </div>
    </div>
    ```

Op de **Dashboard** pagina wordt nu de nieuwe KPI-waarde weer gegeven:

![Waarschuwings-KPI](./media/iot-accelerators-remote-monitoring-customize/new-kpi.png)

## <a name="customize-the-map"></a>De kaart aanpassen

Zie de pagina [toewijzing aanpassen](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map) in github voor meer informatie over de kaart onderdelen in de oplossing.

<!--
### Connect an external visualization tool

See the [Connect an external visualization tool](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to connect an external visualization tool.

-->

## <a name="other-customization-options"></a>Andere aanpassings opties

Als u de laag voor presentaties en visualisaties in de oplossing voor externe controle verder wilt aanpassen, kunt u de code bewerken. De relevante GitHub-opslag plaatsen zijn:

* [De configuratie micro service voor Azure IoT-oplossingen (.NET)](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/config)
* [De configuratie micro service voor Azure IoT-oplossingen (Java)](https://github.com/Azure/remote-monitoring-services-java/tree/master/config)
* [Web-UI voor externe bewaking van Azure IoT-PC'S](https://github.com/Azure/pcs-remote-monitoring-webui)

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd over de resources die beschikbaar zijn om u te helpen bij het aanpassen van de webgebruikersinterface in de oplossings versneller voor externe controle. Raadpleeg de volgende artikelen voor meer informatie over het aanpassen van de gebruikers interface:

* [Een aangepaste pagina toevoegen aan de gebruikers interface van de oplossings versneller voor externe controle](iot-accelerators-remote-monitoring-customize-page.md)
* [Een aangepaste service toevoegen aan de webgebruikersinterface voor externe bewaking oplossings versneller](iot-accelerators-remote-monitoring-customize-service.md)
* [Een aangepast raster toevoegen aan de webgebruikersinterface van de oplossings versneller voor externe controle](iot-accelerators-remote-monitoring-customize-grid.md)
* [Een aangepaste flyout toevoegen aan de Web-UI van de externe bewakings oplossing](iot-accelerators-remote-monitoring-customize-flyout.md)
* [Een aangepast paneel toevoegen aan het dash board in de Web-UI van de oplossing voor externe bewaking](iot-accelerators-remote-monitoring-customize-panel.md)

Zie [architectuur voor externe bewaking](iot-accelerators-remote-monitoring-sample-walkthrough.md) voor meer conceptuele informatie over de oplossings versneller voor externe controle

Zie [een micro service aanpassen en opnieuw implementeren](iot-accelerators-microservices-example.md)voor meer informatie over het aanpassen van de micro Services voor externe bewakings oplossing.
<!-- Next tutorials in the sequence -->
