---
title: Verbinding maken met Azure Blob Storage-Azure Logic Apps
description: Blobs maken en beheren in azure Storage met Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 06/20/2019
tags: connectors
ms.openlocfilehash: ce59c238e50a1be6879b07e959b236f6181a8ce4
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703259"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-with-azure-logic-apps"></a>Blobs in Azure Blob-opslag maken en beheren met Azure Logic Apps

In dit artikel wordt uitgelegd hoe u bestanden die zijn opgeslagen als blobs in uw Azure Storage-account kunt openen en beheren in een logische app met de Azure Blob Storage-connector. Op die manier kunt u logische apps maken voor het automatiseren van taken en werk stromen voor het beheren van uw bestanden. U kunt bijvoorbeeld Logic apps bouwen die bestanden in uw opslag account maken, ophalen, bijwerken en verwijderen.

Stel dat u een hulp programma hebt dat wordt bijgewerkt op een Azure-website. die fungeert als de trigger voor uw logische app. Als deze gebeurtenis zich voordoet, kunt u uw logische app een bestand in de BLOB storage-container laten bijwerken. Dit is een actie in uw logische app.

> [!NOTE]
>
> Logic apps hebben geen directe toegang tot Azure Storage-accounts die [firewall regels](../storage/common/storage-network-security.md) hebben en zich in dezelfde regio bevinden. Logic apps hebben echter toegang tot Azure Storage-accounts die zich in een andere regio bevinden, omdat een openbaar IP-adres wordt gebruikt voor de communicatie tussen regio's. Zorg ervoor dat u de [uitgaande IP-adressen voor beheerde connectors in uw regio](../logic-apps/logic-apps-limits-and-config.md#outbound)toestaat. U kunt ook meer geavanceerde opties gebruiken:
>
> * Een [integratie service omgeving](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)maken waarmee verbinding kan worden gemaakt met bronnen in een virtueel Azure-netwerk.
>
> * Als u een toegewezen laag gebruikt voor API Management, kunt u de opslag-API vooraan gebruiken door gebruik te maken van API Management en de IP-adressen van de laatste via de firewall toe te staan. Voeg in principe het virtuele Azure-netwerk toe dat wordt gebruikt door API Management aan de firewall instelling van het opslag account. U kunt vervolgens de API Management actie of de HTTP-actie gebruiken om de Azure Storage-Api's aan te roepen. Als u deze optie kiest, moet u echter zelf het verificatie proces afhandelen. Zie [eenvoudige architectuur voor ondernemings integratie](https://aka.ms/aisarch)voor meer informatie.

Als u geen ervaring hebt met Logic apps, raadpleegt u [Wat is Azure Logic apps](../logic-apps/logic-apps-overview.md) en [Quick Start: Maak uw eerste logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md). Zie de naslag informatie voor [Azure Blob Storage-connector](/connectors/azureblobconnector/)voor connector-specifieke technische gegevens.

## <a name="limits"></a>Limieten

* Met Azure Blob Storage-acties kunnen standaard bestanden worden gelezen of geschreven die *50 MB of kleiner*zijn. Voor het afhandelen van bestanden die groter zijn dan 50 MB maar Maxi maal 1024 MB, worden door Azure Blob Storage acties ondersteuning gegeven voor het delen van [berichten](../logic-apps/logic-apps-handle-large-messages.md). De actie **blob-inhoud ophalen** maakt impliciet gebruik van Chunking.

* Azure Blob Storage-Triggers bieden geen ondersteuning voor het delen van segmenten. Bij het aanvragen van bestands inhoud selecteren triggers alleen bestanden van 50 MB of kleiner. Als u bestanden groter dan 50 MB wilt ophalen, volgt u dit patroon:

  * Gebruik een Azure Blob Storage-trigger die bestands eigenschappen retourneert, zoals **Wanneer een BLOB wordt toegevoegd of gewijzigd (alleen eigenschappen)** .

  * Volg de trigger met de bewerking voor het **ophalen van blob-inhoud** in Azure Blob Storage, die het volledige bestand leest en impliciet gebruikmaakt van Chunking.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Een [Azure-opslag account en een opslag container](../storage/blobs/storage-quickstart-blobs-portal.md)

* De logische app waar u toegang nodig hebt tot uw Azure Blob-opslag account. Als u uw logische app wilt starten met een Azure Blob Storage-trigger, hebt u een [lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md)nodig.

<a name="add-trigger"></a>

## <a name="add-blob-storage-trigger"></a>Trigger voor Blob-opslag toevoegen

In Azure Logic Apps moet elke logische app beginnen met een [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts), die wordt geactiveerd wanneer een bepaalde gebeurtenis plaatsvindt of wanneer aan een bepaalde voor waarde wordt voldaan. Telkens wanneer de trigger wordt geactiveerd, maakt de Logic Apps-Engine een exemplaar van een logische app en begint de werk stroom van uw app uit te voeren.

In dit voor beeld ziet u hoe u een werk stroom van een logische app kunt starten met de trigger **Wanneer een BLOB wordt toegevoegd of gewijzigd (alleen eigenschappen)** wanneer de eigenschappen van een BLOB worden toegevoegd of bijgewerkt in uw opslag container.

1. Maak in de [Azure Portal](https://portal.azure.com) of Visual Studio een lege logische app, waarmee u de ontwerp functie voor logische apps kunt openen. In dit voor beeld wordt de Azure Portal gebruikt.

2. Voer in het zoekvak ' Azure Blob ' in als uw filter. Selecteer in de lijst triggers de gewenste trigger.

   In dit voor beeld wordt deze trigger gebruikt: **Wanneer een BLOB wordt toegevoegd of gewijzigd (alleen eigenschappen)**

   ![Trigger selecteren](./media/connectors-create-api-azureblobstorage/azure-blob-trigger.png)

3. Als u wordt gevraagd om de verbindings gegevens, [maakt u de verbinding voor Blob Storage nu](#create-connection). Als uw verbinding al bestaat, geeft u de benodigde informatie op voor de trigger.

   Selecteer voor dit voor beeld de container en de map die u wilt bewaken.

   1. Selecteer in het vak **container** het mappictogram.

   2. Kies in de lijst met mappen de rechter hoek ( **>** ), en blader vervolgens tot u de gewenste map hebt gevonden en geselecteerd.

      ![Map selecteren](./media/connectors-create-api-azureblobstorage/trigger-select-folder.png)

   3. Selecteer het interval en de frequentie voor hoe vaak de trigger de map moet controleren op wijzigingen.

4. Wanneer u klaar bent, kiest u **Opslaan**op de werk balk van de ontwerp functie.

5. Ga nu verder met het toevoegen van een of meer acties aan uw logische app voor de taken die u wilt uitvoeren met de trigger resultaten.

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>Blob-opslag actie toevoegen

In Azure Logic Apps is een [actie](../logic-apps/logic-apps-overview.md#logic-app-concepts) een stap in uw werk stroom die volgt op een trigger of een andere actie. Voor dit voor beeld begint de logische app met de [trigger voor terugkeer patroon](../connectors/connectors-native-recurrence.md).

1. Open in de [Azure Portal](https://portal.azure.com) of Visual Studio uw logische app in de ontwerp functie voor logische apps. In dit voor beeld wordt de Azure Portal gebruikt.

2. Kies **nieuwe stap**onder de trigger of actie in de ontwerp functie voor logische apps.

   ![Een actie toevoegen](./media/connectors-create-api-azureblobstorage/add-action.png) 

   Als u een actie wilt toevoegen tussen de bestaande stappen, plaatst u de muis aanwijzer op de verbindings pijl. Kies het plus teken ( **+** ) dat wordt weer gegeven en selecteer **een actie toevoegen**.

3. Voer in het zoekvak ' Azure Blob ' in als uw filter. Selecteer in de lijst acties de gewenste actie.

   In dit voor beeld wordt deze actie gebruikt: **Blob-inhoud ophalen**

   ![Actie selecteren](./media/connectors-create-api-azureblobstorage/azure-blob-action.png)

4. Als u wordt gevraagd om de verbindings gegevens, [maakt u nu uw Azure Blob Storage-verbinding](#create-connection).
Als uw verbinding al bestaat, geeft u de benodigde informatie op voor de actie.

   Voor dit voor beeld selecteert u het gewenste bestand.

   1. Selecteer het mappictogram in het vak **BLOB** .
  
      ![Map selecteren](./media/connectors-create-api-azureblobstorage/action-select-folder.png)

   2. Zoek en selecteer het gewenste bestand op basis van het **id-** nummer van de blob. U vindt dit **id-** nummer in de meta gegevens van de blob die worden geretourneerd door de eerder beschreven Blob Storage-trigger.

5. Wanneer u klaar bent, kiest u **Opslaan**op de werk balk van de ontwerp functie.
Als u uw logische app wilt testen, moet u ervoor zorgen dat de geselecteerde map een BLOB bevat.

In dit voor beeld wordt alleen de inhoud van een BLOB opgehaald. Als u de inhoud wilt weer geven, voegt u een andere actie toe die een bestand met de BLOB maakt met behulp van een andere connector. U kunt bijvoorbeeld een OneDrive-actie toevoegen waarmee een bestand wordt gemaakt op basis van de inhoud van de blob.

<a name="create-connection"></a>

## <a name="connect-to-storage-account"></a>Verbinding maken met het opslag account

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to Azure blob storage](../../includes/connectors-create-api-azureblobstorage.md)]

## <a name="connector-reference"></a>Connector-verwijzing

Zie de [referentie pagina van de connector](/connectors/azureblobconnector/)voor technische details, zoals triggers, acties en limieten, zoals wordt beschreven in het open API-bestand van de connector (voorheen Swagger).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic apps](../connectors/apis-list.md) -connectors
