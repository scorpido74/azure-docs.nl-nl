---
title: De gebruikersinterface van de oplossing voor externe bewaking aanpassen - Azure | Microsoft Documenten
description: In dit artikel vindt u informatie over hoe u toegang hebt tot de broncode voor de gebruikersinterface van de remote monitoring-oplossingsversneller en enkele aanpassingen aanbrengen.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/09/2018
ms.topic: conceptual
ms.openlocfilehash: eb3d5fea68b5b1b6e648943cb3dbaab5857e9e07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68608002"
---
# <a name="customize-the-remote-monitoring-solution-accelerator"></a>De versneller van de oplossing voor externe bewaking aanpassen

In dit artikel vindt u informatie over hoe u toegang krijgen tot de broncode en de gebruikersinterface van de remote monitoring-oplossingsversneller aanpassen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-a-local-development-environment-for-the-ui"></a>Een lokale ontwikkelingsomgeving voorbereiden op de gebruikersinterface

De Remote Monitoring solution accelerator UI-code wordt geïmplementeerd met behulp van het React.js framework. U vindt de broncode in de [azure-iot-pcs-remote-monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) GitHub repository.

Als u wijzigingen wilt aanbrengen in de gebruikersinterface, u een kopie ervan lokaal uitvoeren. Als u acties zoals het ophalen van telemetrie wilt uitvoeren, maakt de lokale kopie verbinding met een geïmplementeerdexemplaar van de oplossing.

In de volgende stappen wordt het proces beschreven voor het instellen van een lokale omgeving voor ui-ontwikkeling:

1. Implementeer een **basisexemplaar** van de **oplossingsversneller** met de pcs CLI. Noteer de naam van uw implementatie en de referenties die u voor de virtuele machine hebt opgegeven. Zie [Implementeren met de CLI](iot-accelerators-remote-monitoring-deploy-cli.md)voor meer informatie.

1. Als u SSH-toegang wilt inschakelen tot de virtuele machine die de microservices in uw oplossing host, gebruikt u de Azure-portal of de Azure Cloud Shell. Bijvoorbeeld:

    ```azurecli-interactive
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

    Alleen SSH-toegang inschakelen tijdens de test en ontwikkeling. Als u SSH inschakelt, [moet u deze uitschakelen zodra u klaar bent met het gebruik](../security/fundamentals/network-best-practices.md#disable-rdpssh-access-to-virtual-machines)ervan.

1. Gebruik de Azure-portal of de Azure Cloud Shell om de naam en het openbare IP-adres van uw virtuele machine te vinden. Bijvoorbeeld:

    ```azurecli-interactive
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. Gebruik SSH om verbinding te maken met uw virtuele machine. Gebruik het IP-adres van de vorige stap en de referenties die u hebt opgegeven toen u **pc's** hebt uitgevoerd om de oplossing te implementeren. De `ssh` opdracht is beschikbaar in de Azure Cloud Shell.

1. Als u de lokale UX verbinding wilt laten maken, voert u de volgende opdrachten uit op de bashshell in de virtuele machine:

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. Nadat u de opdracht hebt voltooid en de website wordt gestart, u de verbinding met de virtuele machine verbreken.

1. Bewerk in uw lokale kopie van de [azure-iot-pcs-remote-monitoring-webui repository](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) het **.env-bestand** om de URL van uw geïmplementeerde oplossing toe te voegen:

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

1. Navigeer bij een opdrachtprompt naar uw `azure-iot-pcs-remote-monitoring-webui` lokale kopie van de map.

1. Voer de volgende opdrachten uit om de vereiste bibliotheken te installeren en de gebruikersinterface lokaal uit te voeren:

    ```cmd/sh
    npm install
    npm start
    ```

1. Met de vorige opdracht wordt de\/gebruikersinterface lokaal uitgevoerd op http: /localhost:3000/dashboard. U de code bewerken terwijl de site wordt uitgevoerd en deze dynamisch bijwerken.

## <a name="customize-the-layout"></a>De indeling aanpassen

Elke pagina in de oplossing voor externe bewaking bestaat uit een set besturingselementen, aangeduid als *deelvensters* in de broncode. De **dashboardpagina** bestaat uit vijf deelvensters: Overzicht, Kaart, Waarschuwingen, Telemetrie en Analytics. U vindt de broncode die elke pagina en de panelen definieert in de [pcs-remote-monitoring-webui](https://github.com/Azure/pcs-remote-monitoring-webui) GitHub repository. De code die bijvoorbeeld de **dashboardpagina,** de indeling en de deelvensters op de pagina definieert, bevindt zich in de map [src/componenten/pagina's/dashboard.](https://github.com/Azure/pcs-remote-monitoring-webui/tree/master/src/components/pages/dashboard)

Omdat de panelen hun eigen lay-out en grootte beheren, u eenvoudig de lay-out van een pagina wijzigen. Breng de volgende wijzigingen aan in `src/components/pages/dashboard/dashboard.js` het element **PageContent** in het bestand in:

* Wissel de posities van de kaart en telemetriepanelen om.
* Wijzig de relatieve breedtes van de kaart- en analysepanelen.

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

![Indeling van het deelvenster wijzigen](./media/iot-accelerators-remote-monitoring-customize/layout.png)

U ook meerdere exemplaren van hetzelfde deelvenster of meerdere versies toevoegen als u [een deelvenster dupliceert en aanpast.](#duplicate-and-customize-an-existing-control) In het volgende voorbeeld ziet u hoe u twee exemplaren van het telemetriepaneel toevoegt. Als u deze wijzigingen `src/components/pages/dashboard/dashboard.js` wilt aanbrengen, bewerkt u het bestand:

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

Vervolgens u verschillende telemetrie in elk deelvenster bekijken:

![Meerdere telemetriepanelen](./media/iot-accelerators-remote-monitoring-customize/multiple-telemetry.png)

## <a name="duplicate-and-customize-an-existing-control"></a>Een bestaand besturingselement dupliceren en aanpassen

In de volgende stappen wordt beschreven hoe u een bestaand deelvenster dupliceren, wijzigen en vervolgens de gewijzigde versie gebruiken. De stappen gebruiken het deelvenster **Waarschuwingen** als voorbeeld:

1. Maak in uw lokale kopie van de opslagplaats een `src/components/pages/dashboard/panels` kopie van de **map waarschuwingen** in de map. Geef het nieuwe exemplaar **een naam cust_alerts**.

1. Bewerk in het bestand **alertsPanel.js** in de **map cust_alerts** de naam van de klasse als **CustAlertsPanel:**

    ```javascript
    export class CustAlertsPanel extends Component {
    ```

1. Voeg de volgende `src/components/pages/dashboard/panels/index.js` regel toe aan het bestand:

    ```javascript
    export * from './cust_alerts';
    ```

1. Vervang `alertsPanel` `CustAlertsPanel` door `src/components/pages/dashboard/dashboard.js` in het bestand:

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

U hebt nu het oorspronkelijke **waarschuwingspaneel** vervangen door een kopie met de naam **CustAlerts.** Deze kopie is hetzelfde als het origineel. U de kopie nu wijzigen. U bijvoorbeeld de volgorde van de kolom wijzigen in het **deelvenster Waarschuwingen:**

1. Open het `src/components/pages/dashboard/panels/cust_alerts/alertsPanel.js`-bestand.

1. Wijzig de kolomdefinities zoals weergegeven in het volgende codefragment:

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

De volgende schermafbeelding toont de nieuwe versie van het **deelvenster Waarschuwingen:**

![deelvenster waarschuwingen bijgewerkt](./media/iot-accelerators-remote-monitoring-customize/reorder-columns.png)

## <a name="customize-the-telemetry-chart"></a>Het telemetriediagram aanpassen

De bestanden `src/components/pages/dashboard/panels/telemtry` in de map definiëren de telemetriegrafiek op de **dashboardpagina.** De gebruikersinterface haalt de telemetrie uit `src/services/telemetryService.js` de back-end van de oplossing in het bestand. In de volgende stappen ziet u hoe u de periode in het telemetriediagram wijzigt van 15 naar 5 minuten:

1. Zoek `src/services/telemetryService.js` in het bestand de functie **getTelemetryByDeviceIdP15M**. Maak een kopie van deze functie en wijzig de kopie als volgt:

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

1. Als u deze nieuwe functie wilt gebruiken `src/components/pages/dashboard/dashboard.js` om het telemetriediagram te vullen, opent u het bestand. Zoek de lijn die de telemetriestroom initialiseert en wijzig deze als volgt:

    ```javascript
    const getTelemetryStream = ({ deviceIds = [] }) => TelemetryService.getTelemetryByDeviceIdP5M(deviceIds)
    ```

De telemetriegrafiek toont nu de vijf minuten telemetriegegevens:

![Telemetriediagram met één dag](./media/iot-accelerators-remote-monitoring-customize/telemetry-period.png)

## <a name="add-a-new-kpi"></a>Een nieuwe KPI toevoegen

Op **de dashboardpagina** worden KPI's weergegeven in het deelvenster **Analytics.** Deze KPI's worden `src/components/pages/dashboard/dashboard.js` berekend in het bestand. De KPI's worden `src/components/pages/dashboard/panels/analytics/analyticsPanel.js` weergegeven door het bestand. In de volgende stappen wordt beschreven hoe u een nieuwe KPI-waarde op de **dashboardpagina** berekent en rendert. Het getoonde voorbeeld is het toevoegen van een nieuwe procentuele wijziging in waarschuwingswaarschuwingen KPI:

1. Open het `src/components/pages/dashboard/dashboard.js`-bestand. Wijzig het **object initialState** om de eigenschap **WarningAlertsChange** als volgt op te nemen:

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

1. Wijzig het object **currentAlertsStats** om **totalWarningCount** als eigenschap op te nemen:

    ```javascript
    return {
      openWarningCount: (acc.openWarningCount || 0) + (isWarning && isOpen ? 1 : 0),
      openCriticalCount: (acc.openCriticalCount || 0) + (isCritical && isOpen ? 1 : 0),
      totalWarningCount: (acc.totalWarningCount || 0) + (isWarning ? 1 : 0),
      totalCriticalCount: (acc.totalCriticalCount || 0) + (isCritical ? 1 : 0),
      alertsPerDeviceId: updatedAlertsPerDeviceId
    };
    ```

1. Bereken de nieuwe KPI. Zoek de berekening voor het aantal kritieke waarschuwingen. Dupliceer de code en wijzig de kopie als volgt:

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

1. Neem de nieuwe **waarschuwingAlertsChange** KPI op in de KPI-stream:

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

1. Neem de nieuwe **waarschuwingAlertsChange** KPI op in de statusgegevens die worden gebruikt om de gebruikersinterface weer te geven:

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

1. Werk de gegevens bij die worden doorgegeven aan het deelvenster KPI's:

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

U bent nu klaar met `src/components/pages/dashboard/dashboard.js` de wijzigingen in het bestand. In de volgende stappen worden `src/components/pages/dashboard/panels/analytics/analyticsPanel.js` de wijzigingen beschreven die in het bestand moeten worden aangebracht om de nieuwe KPI weer te geven:

1. Wijzig de volgende coderegel om de nieuwe KPI-waarde als volgt op te halen:

    ```javascript
    const { t, isPending, criticalAlertsChange, warningAlertsChange, alertsPerDeviceId, topAlerts, timeSeriesExplorerUrl, error } = this.props;
    ```

1. Wijzig de opmaak om de nieuwe KPI-waarde als volgt weer te geven:

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

Op **de dashboardpagina** wordt nu de nieuwe KPI-waarde weergegeven:

![Waarschuwing KPI](./media/iot-accelerators-remote-monitoring-customize/new-kpi.png)

## <a name="customize-the-map"></a>De kaart aanpassen

Zie de [kaartpagina aanpassen](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map) in GitHub voor meer informatie over de kaartonderdelen in de oplossing.

<!--
### Connect an external visualization tool

See the [Connect an external visualization tool](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to connect an external visualization tool.

-->

## <a name="other-customization-options"></a>Andere aanpassingsopties

Als u de presentatie- en visualisatielaag in de oplossing Voor externe bewaking verder wilt wijzigen, u de code bewerken. De relevante GitHub repositories zijn:

* [De configuratiemicroservice voor Azure IoT Solutions (.NET)](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/config)
* [De configuratiemicroservice voor Azure IoT Solutions (Java)](https://github.com/Azure/remote-monitoring-services-java/tree/master/config)
* [Azure IoT PCS Remote Monitoring Web UI](https://github.com/Azure/pcs-remote-monitoring-webui)

## <a name="next-steps"></a>Volgende stappen

In dit artikel leert u over de beschikbare bronnen om de web-gebruikersinterface aan te passen in de versneller van de oplossing voor externe bewaking. Zie de volgende artikelen voor meer informatie over het aanpassen van de gebruikersinterface:

* [Een aangepaste pagina toevoegen aan de webgebruikersinterface voor de versneller van de oplossing voor externe bewaking](iot-accelerators-remote-monitoring-customize-page.md)
* [Een aangepaste service toevoegen aan de webgebruikersinterface voor de versneller van de oplossing voor externe bewaking](iot-accelerators-remote-monitoring-customize-service.md)
* [Een aangepast raster toevoegen aan de webgebruikersinterface voor de versneller van de oplossing voor externe bewaking](iot-accelerators-remote-monitoring-customize-grid.md)
* [Een aangepaste flyout toevoegen aan de webgebruikers interface voor de webserver van de oplossing voor externe bewaking](iot-accelerators-remote-monitoring-customize-flyout.md)
* [Een aangepast paneel toevoegen aan het dashboard in de web-gebruikersinterface van de oplossing voor externe bewaking](iot-accelerators-remote-monitoring-customize-panel.md)

Zie [Remote Monitoring-architectuur](iot-accelerators-remote-monitoring-sample-walkthrough.md) voor meer conceptuele informatie over de remote monitoring-oplossingsversneller

Zie [Een microservice aanpassen en opnieuw implementeren voor](iot-accelerators-microservices-example.md)meer informatie over het aanpassen van de microservices voor externe monitoringoplossingen.
<!-- Next tutorials in the sequence -->
