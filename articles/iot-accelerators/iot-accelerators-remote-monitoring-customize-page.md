---
title: Een pagina toevoegen aan de gebruikersinterface van de oplossing voor externe bewaking - Azure | Microsoft Documenten
description: In dit artikel ziet u hoe u een nieuwe pagina toevoegt aan de webgebruikersinterface voor het versnellersweb van de afstandsbedieningsoplossing.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/02/2018
ms.topic: conceptual
ms.openlocfilehash: 0228f317e2d3380f2387dd557a27203eb3abc4ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240269"
---
# <a name="add-a-custom-page-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Een aangepaste pagina toevoegen aan de webgebruikersinterface voor de versneller van de oplossing voor externe bewaking

In dit artikel ziet u hoe u een nieuwe pagina toevoegt aan de webgebruikersinterface voor het versnellersweb van de afstandsbedieningsoplossing. Het artikel beschrijft:

- Hoe maak je een lokale ontwikkelingsomgeving voor te bereiden.
- Een nieuwe pagina toevoegen aan de webgebruikersinterface.

Andere handleidingen breiden dit scenario uit om meer functies toe te voegen aan de pagina die u toevoegt.

## <a name="prerequisites"></a>Vereisten

Als u de stappen in deze handleiding wilt uitvoeren, hebt u de volgende software nodig die op uw lokale ontwikkelingsmachine is geïnstalleerd:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="prepare-a-local-development-environment-for-the-ui"></a>Een lokale ontwikkelingsomgeving voorbereiden op de gebruikersinterface

De Gebruikersinterfacecode voor externe bewaking-oplossingsversneller wordt geïmplementeerd met behulp van het [React](https://reactjs.org/) JavaScript-framework. U vindt de broncode in de [Remote Monitoring WebUI](https://github.com/Azure/pcs-remote-monitoring-webui) GitHub repository.

Als u wijzigingen in de gebruikersinterface wilt aanbrengen en testen, u deze uitvoeren op uw lokale ontwikkelingsmachine. Optioneel kan de lokale kopie verbinding maken met een geïmplementeerdexemplaar van de oplossingsversneller om deze te kunnen communiceren met uw echte of gesimuleerde apparaten.

Gebruik Git om de [WebUI-opslagplaats voor externe monitoring](https://github.com/Azure/pcs-remote-monitoring-webui) te klonen naar uw lokale machine om uw lokale ontwikkelingsomgeving voor te bereiden:

```cmd/sh
git clone https://github.com/Azure/pcs-remote-monitoring-webui.git
```

## <a name="add-a-page"></a>Een pagina toevoegen

Als u een pagina wilt toevoegen aan de webgebruikersinterface, moet u de bronbestanden toevoegen die de pagina definiëren en enkele bestaande bestanden wijzigen om de webgebruikersinterface op de hoogte te houden van de nieuwe pagina.

### <a name="add-the-new-files-that-define-the-page"></a>De nieuwe bestanden toevoegen die de pagina definiëren

Om u op weg te helpen, bevat de **src/walkthrough/components/pages/basicPage** map vier bestanden die een eenvoudige pagina definiëren:

**basicPage.container.js**

[!code-javascript[Page container source](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.container.js?name=container "Page container source")]

**basicPage.js**

[!code-javascript[Basic page](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.js?name=page "Basic page")]

**basicPage.scss**

[!code-javascript[Page styling](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.scss?name=styles "Page styling")]

**basicPage.test.js**

[!code-javascript[Test code for basic page](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.test.js?name=test "Test code for basic page")]

Maak een nieuwe map **src/ componenten / pagina's / voorbeeld** en kopieer deze vier bestanden in.

### <a name="add-the-new-page-to-the-web-ui"></a>De nieuwe pagina toevoegen aan de webgebruikersinterface

Als u de nieuwe pagina aan de webgebruikersinterface wilt toevoegen, voert u de volgende wijzigingen aan in bestaande bestanden:

1. Voeg de nieuwe paginacontainer toe aan het bestand **src/components/pages/index.js:**

    ```js
    export * from './example/basicPage.container';
    ```

1. (Optioneel)  Voeg een SVG-pictogram toe voor de nieuwe pagina. Zie [webui/src/utilities/README.md](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/utilities/README.md)voor meer informatie. U een bestaand SVG-bestand gebruiken.

1. Voeg de paginanaam toe aan het vertaalbestand, **openbaar/landint/en/translations.json**. De web-gebruikersinterface gebruikt [i18next](https://www.i18next.com/) voor internationalisering.

    ```json
    "tabs": {
      "basicPage": "Example",
    },
    ```

1. Open het **bestand src/components/app.js** dat de toepassingspagina op het hoogste niveau definieert. Voeg de nieuwe pagina toe aan de lijst met invoer:

    ```javascript
    // Page Components
    import  {
      //...
      BasicPageContainer
    } from './pages';
    ```

1. Voeg in hetzelfde bestand de `pagesConfig` nieuwe pagina toe aan de array. Stel `to` het adres voor de route in, verwijs naar het `component` SVG-pictogram en de eerder toegevoegde vertalingen en stel het in op de container van de pagina:

    ```js
    const pagesConfig = [
      //...
      {
        to: '/basicpage',
        exact: true,
        svg: svgs.tabs.example,
        labelId: 'tabs.basicPage',
        component: BasicPageContainer
      },
      //...
    ];
    ```

1. Voeg nieuwe broodkruimels `crumbsConfig` toe aan de array:

    ```js
    const crumbsConfig = [
      //...
      {
        path: '/basicpage', crumbs: [
          { to: '/basicpage', labelId: 'tabs.basicPage' }
        ]
      },
      //...
    ];
    ```

    Deze voorbeeldpagina heeft slechts één paneermeel, maar sommige pagina's hebben mogelijk meer.

Sla al uw wijzigingen op. U bent klaar om de web-gebruikersinterface uit te voeren met uw nieuwe pagina toegevoegd.

### <a name="test-the-new-page"></a>De nieuwe pagina testen

Navigeer bij een opdrachtprompt naar de hoofdtekst van uw lokale kopie van de opslagplaats en voer de volgende opdrachten uit om de vereiste bibliotheken te installeren en de web-gebruikersinterface lokaal uit te voeren:

```cmd/sh
npm install
npm start
```

Met de vorige opdracht wordt [http://localhost:3000/dashboard](http://localhost:3000/dashboard)de gebruikersinterface lokaal uitgevoerd op .

Zonder uw lokale instantie van de web-gebruikersinterface te verbinden met een geïmplementeerd exemplaar van de oplossingsversneller, ziet u fouten op het dashboard. Deze fouten hebben geen invloed op uw vermogen om uw nieuwe pagina te testen.

U de code nu bewerken terwijl de site lokaal wordt uitgevoerd en de web-uinterface-update dynamisch bekijken.

## <a name="optional-connect-to-deployed-instance"></a>[Optioneel] Verbinding maken met geïmplementeerde instantie

Optioneel u uw lokale hardloopkopie van de web-gebruikersinterface verbinden met de versneller van de oplossing voor externe bewaking in de cloud:

1. Implementeer een **basisexemplaar** van de **oplossingsversneller** met de pcs CLI. Noteer de naam van uw implementatie en de referenties die u voor de virtuele machine hebt opgegeven. Zie [Implementeren met de CLI](iot-accelerators-remote-monitoring-deploy-cli.md)voor meer informatie.

1. Gebruik de Azure-portal of de [az CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) om SSH-toegang te verlenen tot de virtuele machine die de microservices in uw oplossing host. Bijvoorbeeld:

    ```azurecli
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

    U moet alleen SSH-toegang inschakelen tijdens de test en ontwikkeling. Als u SSH inschakelt, [moet u het zo snel mogelijk opnieuw uitschakelen.](../security/fundamentals/network-best-practices.md)

1. Gebruik de Azure-portal of de [az CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) om de naam en het openbare IP-adres van uw virtuele machine te vinden. Bijvoorbeeld:

    ```azurecli
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. Gebruik SSH om verbinding te maken met uw virtuele machine met behulp van het IP-adres van de vorige stap en de referenties die u hebt opgegeven toen u **pc's** gebruikte om de oplossing te implementeren.

1. Als u de lokale UX verbinding wilt laten maken, voert u de volgende opdrachten uit op de bashshell in de virtuele machine:

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. Nadat u de opdracht hebt voltooid en de website wordt gestart, u de verbinding met de virtuele machine verbreken.

1. Bewerk in uw lokale kopie van de [WebUI-opslagplaats voor externe bewaking](https://github.com/Azure/pcs-remote-monitoring-webui) het **.env-bestand** om de URL van uw geïmplementeerde oplossing toe te voegen:

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

## <a name="next-steps"></a>Volgende stappen

In dit artikel leert u over de beschikbare bronnen om de web-gebruikersinterface aan te passen in de versneller van de oplossing voor externe bewaking.

Nu u een pagina hebt gedefinieerd, is de volgende stap het [toevoegen van een aangepaste service aan de webgebruikersinterface voor het versnellersweb](iot-accelerators-remote-monitoring-customize-service.md) op afstand, waarmee gegevens worden opgehaald die in de gebruikersinterface worden weergegeven.

Zie [Remote Monitoring architecture](iot-accelerators-remote-monitoring-sample-walkthrough.md)voor meer conceptuele informatie over de remote monitoring oplossingsversneller.
