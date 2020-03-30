---
title: Integratieserviceomgevingen beheren in Azure Logic Apps
description: Controleer de netwerkstatus en beheer logische apps, verbindingen, aangepaste connectors en integratieaccounts in uw integratieserviceomgeving (ISE) voor Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: f48106be67763c093a183be01098cab74391752e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284198"
---
# <a name="manage-your-integration-service-environment-ise-in-azure-logic-apps"></a>Uw integratieserviceomgeving (ISE) beheren in Azure Logic Apps

In dit artikel ziet u hoe u beheertaken uitvoert voor uw [integratieserviceomgeving (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)bijvoorbeeld:

* Beheer de bronnen zoals logische apps, verbindingen, integratieaccounts en connectoren in uw ISE.
* Controleer de netwerkstatus van uw ISE.
* Voeg capaciteit toe, start uw ISE opnieuw op of verwijder uw ISE, volg de stappen in dit onderwerp. Zie [Artefacten toevoegen aan uw integratieserviceomgeving](../logic-apps/add-artifacts-integration-service-environment-ise.md)om deze artefacten aan uw ISE toe te voegen.

## <a name="view-your-ise"></a>Bekijk uw ISE

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Voer in het zoekvak van de portal 'integratieserviceomgevingen' in en selecteer **vervolgens Integratieserviceomgevingen.**

   ![Integratieserviceomgevingen zoeken](./media/ise-manage-integration-service-environment/find-integration-service-environment.png)

1. Selecteer in de resultatenlijst uw integratieserviceomgeving.

   ![Integratieserviceomgeving selecteren](./media/ise-manage-integration-service-environment/select-integration-service-environment.png)

1. Ga door naar de volgende secties om logische apps, verbindingen, connectors of integratieaccounts in uw ISE te vinden.

<a name="check-network-health"></a>

## <a name="check-network-health"></a>Netwerkstatus controleren

Selecteer **netwerkstatus**in het menu ISE onder **Instellingen**. In dit deelvenster ziet u de status van uw subnetten en uitgaande afhankelijkheden van andere services.

![Netwerkstatus controleren](./media/ise-manage-integration-service-environment/ise-check-network-health.png)

<a name="find-logic-apps"></a>

## <a name="manage-your-logic-apps"></a>Uw logische apps beheren

U de logische apps in uw ISE bekijken en beheren.

1. Selecteer in het menu ISE onder **Instellingen**de optie **Logische apps**.

   ![Logische apps weergeven](./media/ise-manage-integration-service-environment/ise-find-logic-apps.png)

1. Als u logische apps wilt verwijderen die u niet meer nodig hebt in uw ISE, selecteert u deze logische apps en selecteert u **Verwijderen**. Als u wilt bevestigen dat u wilt verwijderen, selecteert u **Ja**.

<a name="find-api-connections"></a>

## <a name="manage-api-connections"></a>API-verbindingen beheren

U de verbindingen bekijken en beheren die zijn gemaakt door de logische apps die in uw ISE worden uitgevoerd.

1. Selecteer **API-verbindingen**in het menu ISE onder **Instellingen**.

   ![API-verbindingen weergeven](./media/ise-manage-integration-service-environment/ise-find-api-connections.png)

1. Als u verbindingen wilt verwijderen die u niet meer nodig hebt in uw ISE, selecteert u deze verbindingen en selecteert u **Verwijderen**. Als u wilt bevestigen dat u wilt verwijderen, selecteert u **Ja**.

<a name="manage-api-connectors"></a>

## <a name="manage-ise-connectors"></a>ISE-connectoren beheren

U de API-connectors die zijn geïmplementeerd op uw ISE bekijken en beheren.

1. Selecteer **beheerde connectors**in het menu ISE onder **Instellingen**.

   ![Beheerde connectors weergeven](./media/ise-manage-integration-service-environment/ise-view-managed-connectors.png)

1. Als u connectors wilt verwijderen die u niet beschikbaar wilt hebben in uw ISE, selecteert u deze connectoren en selecteert u **Verwijderen**. Als u wilt bevestigen dat u wilt verwijderen, selecteert u **Ja**.

<a name="find-custom-connectors"></a>

## <a name="manage-custom-connectors"></a>Aangepaste connectors beheren

U de aangepaste connectors bekijken en beheren die u op uw ISE hebt geïmplementeerd.

1. Selecteer in het menu ISE onder **Instellingen**de optie **Aangepaste connectoren**.

   ![Aangepaste connectoren vinden](./media/ise-manage-integration-service-environment/ise-find-custom-connectors.png)

1. Als u aangepaste connectors wilt verwijderen die u niet meer nodig hebt in uw ISE, selecteert u deze connectoren en selecteert u **Verwijderen**. Als u wilt bevestigen dat u wilt verwijderen, selecteert u **Ja**.

<a name="find-integration-accounts"></a>

## <a name="manage-integration-accounts"></a>Integratieaccounts beheren

1. Selecteer in het menu ISE onder **Instellingen**de optie **Integratieaccounts**.

   ![Integratieaccounts zoeken](./media/ise-manage-integration-service-environment/ise-find-integration-accounts.png)

1. Als u integratieaccounts uit uw ISE wilt verwijderen wanneer dit niet meer nodig is, selecteert u deze integratieaccounts en selecteert u **Verwijderen**.

<a name="add-capacity"></a>

## <a name="add-ise-capacity"></a>ISE-capaciteit toevoegen

De Premium ISE-basiseenheid heeft een vaste capaciteit, dus als u meer doorvoer nodig hebt, u meer schaaleenheden toevoegen, tijdens het maken of achteraf. De Ontwikkelaar SKU bevat niet de mogelijkheid om schaaleenheden toe te voegen.

1. Ga in de [Azure-portal](https://portal.azure.com)naar uw ISE.

1. Als u de gebruiks- en prestatiestatistieken voor uw ISE wilt bekijken, selecteert u **Overzicht**in uw ISE-menu .

   ![Gebruik voor ISE weergeven](./media/ise-manage-integration-service-environment/integration-service-environment-usage.png)

1. Selecteer **onder Instellingen**De optie **Uitschalen**. Selecteer in het deelvenster **Configureren** de volgende opties:

   * [**Handmatige schaal:**](#manual-scale)Schaal op basis van het aantal verwerkingseenheden dat u wilt gebruiken.
   * [**Aangepaste automatische schaal**](#custom-autoscale): Schaal op basis van prestatiestatistieken door te selecteren op basis van verschillende criteria en de drempelvoorwaarden op te geven om aan die criteria te voldoen.

   ![Selecteer het gewenste schaaltype](./media/ise-manage-integration-service-environment/select-scale-out-options.png)

<a name="manual-scale"></a>

### <a name="manual-scale"></a>Handmatigschalen

1. Nadat u **Handmatigschalen**hebt geselecteerd voor **Extra capaciteit,** selecteert u het aantal schaaleenheden dat u wilt gebruiken.

   ![Selecteer het gewenste schaaltype](./media/ise-manage-integration-service-environment/select-manual-scale-out-units.png)

1. Selecteer **Opslaan** als u klaar bent.

<a name="custom-autoscale"></a>

### <a name="custom-autoscale"></a>Aangepaste automatische schaal

1. Nadat u **Aangepaste automatische schaal hebt**geselecteerd , voor De **instellingsnaam Automatisch schalen**, geeft u een naam op voor uw instelling en selecteert u optioneel de Azure-brongroep waar de instelling thuishoort.

   ![Naam opgeven voor instelling automatisch schalen en resourcegroep selecteren](./media/ise-manage-integration-service-environment/select-custom-autoscale.png)

1. Selecteer **voor** de standaardvoorwaarde **Schaal op basis van een statistiek** of Schaal naar een specifiek aantal **instanties**.

   * Als u instantiegebaseerd kiest, voert u het nummer in voor de verwerkingseenheden, een waarde van 0 tot 10.

   * Als u op metrische basis kiest, voert u de volgende stappen uit:

     1. Selecteer in de sectie **Regels** de optie **Een regel toevoegen**.

     1. Stel in het venster **Regelschalen** uw criteria en actie in die u wilt uitvoeren wanneer de regel wordt geactiveerd.

     1. Geef **bijvoorbeeld de**volgende waarden op:

        * **Minimum**: Het minimum aantal te gebruiken verwerkingseenheden
        * **Maximum**: Het maximum aantal te gebruiken verwerkingseenheden
        * **Standaard:** als er problemen optreden tijdens het lezen van de resourcestatistieken en de huidige capaciteit onder de standaardcapaciteit ligt, worden automatisch schalen naar het standaardaantal verwerkingseenheden. Als de huidige capaciteit echter de standaardcapaciteit overschrijdt, wordt automatisch schalen niet inschaald.

1. Als u een andere voorwaarde wilt toevoegen, selecteert u **Schaalvoorwaarde toevoegen**.

1. Wanneer u klaar bent met de instellingen voor automatisch schalen, slaat u de wijzigingen op.

<a name="restart-ISE"></a>

## <a name="restart-ise"></a>ISE opnieuw starten

Als u de dns-server- of DNS-serverinstellingen wijzigt, moet u uw ISE opnieuw opstarten, zodat de ISE deze wijzigingen kan oppikken. Het opnieuw opstarten van een Premium SKU ISE leidt niet tot downtime als gevolg van redundantie en onderdelen die een voor een opnieuw opstarten tijdens recycling. Een Ontwikkelaar SKU ISE ervaart echter downtime omdat er geen redundantie bestaat. Zie [ISE SKU's voor](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)meer informatie.

1. Ga in de [Azure-portal](https://portal.azure.com)naar uw ISE.

1. Selecteer **Overzicht**in het menu ISE . Start op de werkbalk Overzicht **opnieuw op.**

   ![Integratieserviceomgeving opnieuw opstarten](./media/connect-virtual-network-vnet-isolated-environment/restart-integration-service-environment.png)

<a name="delete-ise"></a>

## <a name="delete-ise"></a>ISE verwijderen

Voordat u een ISE verwijdert die u niet meer nodig hebt of een Azure-brongroep die een ISE bevat, controleert u of er geen beleid of vergrendelingen zijn op de Azure-brongroep die deze bronnen bevat of op uw Virtuele Azure-netwerk, omdat deze items verwijdering kunnen blokkeren.

Nadat u uw ISE hebt verwijderd, moet u mogelijk tot 9 uur wachten voordat u uw virtuele Azure-netwerk of subnetten probeert te verwijderen.

## <a name="next-steps"></a>Volgende stappen

* [Resources toevoegen aan integratieserviceomgevingen](../logic-apps/add-artifacts-integration-service-environment-ise.md)