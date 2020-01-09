---
title: Azure Event Hubs-integratie van virtuele netwerken en firewalls inschakelen
description: In deze zelfstudie leert u hoe u Event Hubs integreert met virtuele netwerken en Firewalls om toegang te beveiligen.
services: event-hubs
author: axisc
manager: darosa
ms.author: aschhab
ms.date: 12/20/2019
ms.topic: tutorial
ms.service: event-hubs
ms.custom: mvc
ms.openlocfilehash: f911a1513c6f89180ea51cc0de96dc8a475c7fc8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75437110"
---
# <a name="tutorial-enable-virtual-networks-integration-and-firewalls-on-event-hubs-namespace"></a>Zelf studie: integratie van virtuele netwerken en firewalls op Event Hubs naam ruimte inschakelen

Met [service-eindpunten van Virtual Network (VNet)](../virtual-network/virtual-network-service-endpoints-overview.md) kunt u de privé-adresruimte van uw virtuele netwerk en de identiteit van uw VNet uitbreiden naar Azure-services, via een directe verbinding. Met eindpunten kunt u uw kritieke Azure-serviceresources alleen beveiligen naar uw virtuele netwerken. Verkeer van uw VNet naar de Azure-service blijft altijd in het Microsoft Azure-backbonenetwerk.

Met behulp van firewalls kunt u de toegang tot de Event Hubs-naamruimte van specifieke IP-adressen (of IP-adresbereiken) beperken

Deze zelfstudie laat zien hoe u service-eindpunten voor virtuele netwerken integreert en firewalls (IP-filtering) instelt met uw bestaande Azure Event Hubs-naamruimte met behulp van de portal.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Service-eindpunten voor virtuele netwerken integreren met uw Event Hubs-naamruimte.
> * Firewalls (IP-filtering) instellen met uw Event Hubs-naamruimte.

>[!WARNING]
> De implementatie van de integratie van virtuele netwerken kan voorkomen dat andere Azure-services interactie hebben met Event Hubs.
>
> Eigen integraties worden niet ondersteund wanneer virtuele netwerken zijn ingeschakeld.
> Algemene Azure-scenario's die niet met virtuele netwerken werken -
> * Diagnostische gegevens en logboekregistratie van Azure
> * Azure Stream Analytics
> * Event Grid-integratie
> * De aanwezigheid van Web Apps en Functions is vereist in een virtueel netwerk.
> * IoT Hub Routes
> * IoT Device Explorer


> [!IMPORTANT]
> Virtuele netwerken worden ondersteund in de lagen **Standard** en **Dedicated** van Event Hubs. Deze worden niet ondersteund in de Basic-laag.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account][] aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

We maken gebruik van een bestaande Event Hubs-naamruimte, dus zorg ervoor dat u over een beschikbare Event Hubs-naamruimte beschikt. Raadpleeg [deze zelfstudie](./event-hubs-create.md) als u er geen hebt

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Ga eerst naar de [Azure Portal][Azure portal] en meld u aan met uw Azure-abonnement.

## <a name="select-event-hubs-namespace"></a>Event Hubs-naamruimte selecteren

In deze zelfstudie hebben we een Event Hubs-naamruimte gemaakt en gaan we hier naartoe navigeren.

## <a name="navigate-to-firewalls-and-virtual-networks-experience"></a>Navigeren naar firewalls en virtuele netwerken

Gebruik het navigatiemenu in het linkerdeelvenster in de portal om de optie **'Firewalls en virtuele netwerken'** te kiezen.

  ![Naar het menu navigeren](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-landing-page.png)

  De eerste keer dat u deze pagina bezoekt, moet het keuzerondje **Alle netwerken** worden geselecteerd. Dit betekent dat de Event Hubs-naamruimte alle binnenkomende verbindingen toestaat.

## <a name="add-virtual-network-service-endpoint"></a>Service-eindpunt voor virtueel netwerk toevoegen

Om toegang te beperken, moet u het service-eindpunt voor virtuele netwerken voor deze Event Hubs-naamruimte integreren.

1. Klik op het keuzerondje **Geselecteerde netwerken** boven aan de pagina om de rest van de pagina met menuopties in te schakelen.
  ![geselecteerde netwerken](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-selecting-selected-networks.png)
2. Selecteer in de sectie Virtueel netwerk van de pagina de optie ***Bestaand virtueel netwerk toevoegen***. Hiermee wordt het deelvenster weergegeven waarin u een bestaand virtueel netwerk kunt selecteren.
  ![bestaand virtueel netwerk toevoegen](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane.png)
3. Selecteer het virtuele netwerk in de lijst en kies het subnet.
   ![subnet selecteren](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane-with-subnet-query.png)
4. U moet het service-eindpunt inschakelen voordat u het virtuele netwerk toevoegt aan de lijst. Als het service-eindpunt niet is ingeschakeld, wordt u door de portal gevraagd dit in te schakelen.
  ![subnet selecteren en eindpunt inschakelen](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane-after-enabling.png)
    > [!NOTE]
    > Als u het service-eindpunt niet kunt inschakelen, kunt u het ontbrekende service-eindpunt voor het virtuele netwerk negeren met behulp van de ARM-sjabloon. Deze functionaliteit is niet beschikbaar in de portal.

5. Nadat het service-eindpunt is ingeschakeld in het geselecteerde subnet, kunt u doorgaan met het toevoegen ervan aan de lijst met toegestane virtuele netwerken.
  ![subnet toevoegen na het inschakelen van eindpunt](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane-after-adding.png)

6. Klik op de knop **Opslaan** op het lint boven om de configuratie van het virtuele netwerk in de service op te slaan. Wacht een paar minuten totdat de bevestiging wordt weergegeven in de portalmeldingen.

## <a name="add-firewall-for-specified-ip"></a>Firewall toevoegen voor opgegeven IP

We kunnen toegang tot de Event Hubs-naamruimte met behulp van firewallregels beperken voor een beperkt bereik van IP-adressen of een specifiek IP-adres.

1. Klik op het keuzerondje **Geselecteerde netwerken** boven aan de pagina om de rest van de pagina met menuopties in te schakelen.
  ![geselecteerde netwerken](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-selecting-selected-networks.png)
2. In de sectie **Firewall** onder het raster ***Adresbereik*** kunt u één of veel specifieke IP-adressen of bereiken van IP-adressen toevoegen.
  ![ip-adressen toevoegen](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-firewall.png)
3. Nadat u de meerdere IP-adressen (of IP-adresbereiken) hebt toegevoegd, klikt u op **Opslaan** in het lint boven om ervoor te zorgen dat de configuratie aan de servicezijde wordt opgeslagen. Wacht een paar minuten totdat de bevestiging wordt weergegeven in de portalmeldingen.
  ![ip-adressen toevoegen en op Opslaan klikken](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-firewall-hitting-save.png)

## <a name="adding-your-current-ip-address-to-the-firewall-rules"></a>Uw huidige IP-adres toevoegen aan de firewallregels

1. U kunt uw huidige IP-adres ook snel toevoegen door het selectievakje ***IP-adres van client (uw huidige IP-adres)*** net boven het raster ***Adresbereik*** in te schakelen.
  ![huidig IP-adres toevoegen](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-current-ip-hitting-save.png)
2. Nadat u uw huidige IP-adres aan de firewallregels hebt toegevoegd, klikt u op **Opslaan** in het lint boven om ervoor te zorgen dat de configuratie aan de servicezijde wordt opgeslagen. Wacht een paar minuten totdat de bevestiging wordt weergegeven in de portalmeldingen.
  ![huidig IP-adres toevoegen en op Opslaan klikken](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-current-ip-hitting-save-after-saving.png)

## <a name="conclusion"></a>Conclusie

In deze zelfstudie hebt u eindpunten van virtuele netwerken en firewallregels geïntegreerd met een bestaande Event Hubs-naamruimte. U hebt het volgende geleerd:
> [!div class="checklist"]
> * Service-eindpunten voor virtuele netwerken integreren met uw Event Hubs-naamruimte.
> * Firewalls (IP-filtering) instellen met uw Event Hubs-naamruimte.


[Azure portal]: https://portal.azure.com/
