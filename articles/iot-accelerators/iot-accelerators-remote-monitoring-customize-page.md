---
title: Een pagina toevoegen aan de gebruikers interface van de oplossing voor externe bewaking-Azure | Microsoft Docs
description: In dit artikel leest u hoe u een nieuwe pagina kunt toevoegen aan de Web-UI van de oplossing voor externe bewaking.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/02/2018
ms.topic: conceptual
ms.openlocfilehash: c90f4166bf88a8df18a93e84903c93461b904d2c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187259"
---
# <a name="add-a-custom-page-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Een aangepaste pagina toevoegen aan de gebruikers interface van de oplossings versneller voor externe controle

In dit artikel leest u hoe u een nieuwe pagina kunt toevoegen aan de Web-UI van de oplossing voor externe bewaking. In het artikel worden de volgende informatie beschreven:

- Het voorbereiden van een lokale ontwikkel omgeving.
- Een nieuwe pagina toevoegen aan de Web-UI.

Andere hand leidingen kunnen dit scenario uitbreiden om meer functies toe te voegen aan de pagina die u toevoegt.

## <a name="prerequisites"></a>Vereisten

Als u de stappen in deze hand leiding wilt uitvoeren, moet u de volgende software op uw lokale ontwikkel computer installeren:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="prepare-a-local-development-environment-for-the-ui"></a>Een lokale ontwikkel omgeving voorbereiden voor de gebruikers interface

De code van de gebruikers interface voor de externe controle oplossing wordt geïmplementeerd met het [reagerende](https://reactjs.org/) java script-Framework. U vindt de bron code in de WebUI github-opslag plaats voor [externe bewaking](https://github.com/Azure/pcs-remote-monitoring-webui) .

Als u wijzigingen in de gebruikers interface wilt aanbrengen en wilt testen, kunt u deze op uw lokale ontwikkel computer uitvoeren. De lokale kopie kan worden gebruikt om verbinding te maken met een geïmplementeerd exemplaar van de oplossings versneller, zodat deze kan communiceren met uw echte of gesimuleerde apparaten.

Als u uw lokale ontwikkel omgeving wilt voorbereiden, gebruikt u Git om de WebUI-opslag plaats voor [externe bewaking](https://github.com/Azure/pcs-remote-monitoring-webui) te klonen op uw lokale computer:

```cmd/sh
git clone https://github.com/Azure/pcs-remote-monitoring-webui.git
```

## <a name="add-a-page"></a>Een pagina toevoegen

Als u een pagina wilt toevoegen aan de webgebruikersinterface, moet u de bron bestanden toevoegen die de pagina definiëren en een aantal bestaande bestanden wijzigen om de webgebruikersinterface op de hoogte te stellen van de nieuwe pagina.

### <a name="add-the-new-files-that-define-the-page"></a>De nieuwe bestanden toevoegen waarmee de pagina wordt gedefinieerd

Om aan de slag te gaan, bevat de map **src/walkthrough/onderdelen/pages/basicPage** vier bestanden die een eenvoudige pagina definiëren:

**basicPage. container. js**

[!code-javascript[Page container source](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.container.js?name=container "Page container source")]

**basicPage. js**

[!code-javascript[Basic page](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.js?name=page "Basic page")]

**basicPage. scss**

[!code-javascript[Page styling](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.scss?name=styles "Page styling")]

**basicPage. test. js**

[!code-javascript[Test code for basic page](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.test.js?name=test "Test code for basic page")]

Maak een nieuwe map **brondoc/onderdelen/pagina's/voor beeld** en kopieer deze vier bestanden hierin.

### <a name="add-the-new-page-to-the-web-ui"></a>De nieuwe pagina toevoegen aan de Web-UI

Als u de nieuwe pagina wilt toevoegen aan de Web-UI, moet u de volgende wijzigingen aanbrengen in bestaande bestanden:

1. Voeg de nieuwe pagina container toe aan het bestand **src/Components/pages/index. js** :

    ```js
    export * from './example/basicPage.container';
    ```

1. Beschrijving  Voeg een SVG-pictogram toe voor de nieuwe pagina. Zie [webui/src/Utilities/README. MD](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/utilities/README.md)(Engelstalig) voor meer informatie. U kunt een bestaand SVG-bestand gebruiken.

1. Voeg de pagina naam toe aan het Vertaal bestand, **open bare/land instellingen/en/of-vertalingen. json**. De Web-UI maakt gebruik van [i18next](https://www.i18next.com/) voor verschillende talen.

    ```json
    "tabs": {
      "basicPage": "Example",
    },
    ```

1. Open het bestand **src/Components/app. js** waarmee de toepassings pagina op het hoogste niveau wordt gedefinieerd. Voeg de nieuwe pagina toe aan de lijst met import bewerkingen:

    ```javascript
    // Page Components
    import  {
      //...
      BasicPageContainer
    } from './pages';
    ```

1. Voeg in hetzelfde bestand de nieuwe pagina toe aan de `pagesConfig` matrix. Stel het `to` adres voor de route in, wijs het SVG-pictogram en de gemaakte vertalingen eerder toe `component` en stel de in op de container van de pagina:

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

1. Voeg nieuwe brood kruimels toe aan `crumbsConfig` de matrix:

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

    Deze voorbeeld pagina heeft slechts één brood kruimel, maar sommige pagina's hebben mogelijk meer.

Sla al uw wijzigingen op. U bent klaar om de Web-UI uit te voeren met de nieuwe pagina die is toegevoegd.

### <a name="test-the-new-page"></a>De nieuwe pagina testen

Ga bij een opdracht prompt naar de hoofdmap van uw lokale exemplaar van de opslag plaats en voer de volgende opdrachten uit om de vereiste bibliotheken te installeren en de webgebruikersinterface lokaal uit te voeren:

```cmd/sh
npm install
npm start
```

Met de vorige opdracht wordt de gebruikers interface `http://localhost:3000/dashboard`lokaal uitgevoerd op.

Zonder dat u uw lokale exemplaar van de Web-UI verbindt met een geïmplementeerd exemplaar van de oplossings versneller, worden er fouten weer geven op het dash board. Deze fouten hebben geen invloed op de mogelijkheid om uw nieuwe pagina te testen.

U kunt de code nu bewerken terwijl de site lokaal wordt uitgevoerd en de Web-UI-update dynamisch weer geven.

## <a name="optional-connect-to-deployed-instance"></a>Beschrijving Verbinding maken met het geïmplementeerde exemplaar

U kunt eventueel uw lokale actieve kopie van de Web-UI koppelen aan de oplossings versneller voor externe controle in de Cloud:

1. Implementeer een **basis** exemplaar van de oplossings versneller met de **PCs** -cli. Noteer de naam van uw implementatie en de referenties die u hebt ingevoerd voor de virtuele machine. Zie [implementeren met de CLI](iot-accelerators-remote-monitoring-deploy-cli.md)voor meer informatie.

1. Gebruik de Azure Portal of [AZ cli](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) om SSH-toegang tot de virtuele machine in te scha kelen die als host fungeert voor de micro Services in uw oplossing. Bijvoorbeeld:

    ```azurecli
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

    Schakel SSH-toegang alleen in tijdens testen en ontwikkeling. Als u SSH inschakelt, [moet u dit zo snel mogelijk weer uitschakelen](../security/fundamentals/network-best-practices.md).

1. Gebruik de Azure Portal of [AZ cli](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) om de naam en het open bare IP-adres van uw virtuele machine te vinden. Bijvoorbeeld:

    ```azurecli
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. Gebruik SSH om verbinding te maken met uw virtuele machine met behulp van het IP-adres uit de vorige stap en de referenties die u hebt ingevoerd tijdens het uitvoeren van **pc's** om de oplossing te implementeren.

1. Als u de lokale UX wilt toestaan verbinding te maken, voert u de volgende opdrachten uit in de bash-shell in de virtuele machine:

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. Nadat de opdracht is voltooid en de website wordt gestart, kunt u de verbinding met de virtuele machine verbreken.

1. Bewerk in het lokale exemplaar van de WebUI-opslag plaats voor [externe bewaking](https://github.com/Azure/pcs-remote-monitoring-webui) het **. env** -bestand om de URL van uw geïmplementeerde oplossing toe te voegen:

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd over de resources die beschikbaar zijn om u te helpen bij het aanpassen van de webgebruikersinterface in de oplossings versneller voor externe controle.

Nu u een pagina hebt gedefinieerd, is de volgende stap het [toevoegen van een aangepaste service aan de Web-UI voor externe controle oplossing](iot-accelerators-remote-monitoring-customize-service.md) waarmee gegevens worden opgehaald die in de gebruikers interface worden weer gegeven.

Zie [architectuur voor externe bewaking](iot-accelerators-remote-monitoring-sample-walkthrough.md)voor meer conceptuele informatie over de oplossings versneller voor externe controle.
