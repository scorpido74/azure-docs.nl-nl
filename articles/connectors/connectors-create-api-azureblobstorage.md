---
title: Verbinding maken met Azure Blob Storage
description: Blobs maken en beheren in Azure-opslagaccounts met Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/21/2020
tags: connectors
ms.openlocfilehash: eb943bfe36be10d1e95d569a5c1bf48563e909c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247356"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-by-using-azure-logic-apps"></a>Blobs maken en beheren in Azure Blob Storage met Azure Logic Apps

In dit artikel ziet u hoe u bestanden die zijn opgeslagen als blobs in uw Azure-opslagaccount openen en beheren vanuit een logische app met de Azure Blob Storage-connector. Op die manier u logische apps maken die taken en werkstromen automatiseren voor het beheren van uw bestanden. U kunt bijvoorbeeld logische apps maken waarmee bestanden worden gemaakt, opgehaald, bijgewerkt en verwijderd in uw opslagaccount.

Stel dat u een tool hebt die wordt bijgewerkt op een Azure-website. die fungeert als de trigger voor uw logica app. Wanneer deze gebeurtenis plaatsvindt, u uw logische app een bestand laten bijwerken in uw blob-opslagcontainer, wat een actie is in uw logische app.

Als u nieuw bent in logische apps, controleert u [Wat is Azure Logic Apps](../logic-apps/logic-apps-overview.md) en [Quickstart: Uw eerste logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md). Zie de [naslagverwijzing naar azure blob-opslagconnector](https://docs.microsoft.com/connectors/azureblobconnector/)voor technische gegevens met een connector .

> [!IMPORTANT]
> Logische apps hebben geen directe toegang tot opslagaccounts die zich achter firewalls bevinden als ze zich beide in dezelfde regio bevinden. Als tijdelijke oplossing u uw logische apps en opslagaccount in verschillende regio's hebben. Zie de sectie [Access-opslagaccounts achter firewalls](#storage-firewalls) later in dit onderwerp voor meer informatie over het inschakelen van toegang van Azure Logic Apps tot opslagaccounts achter firewalls.

<a name="blob-storage-limits"></a>

## <a name="limits"></a>Limieten

* Standaard kunnen Azure Blob Storage-acties bestanden lezen of schrijven die *50 MB of kleiner*zijn. Als u bestanden van meer dan 50 MB maar tot 1024 MB wilt verwerken, ondersteunen Azure Blob Storage-acties [berichtchunking](../logic-apps/logic-apps-handle-large-messages.md). Met de actie **Blob-inhoud genereren** wordt impliciet chunking gebruikt.

* Azure Blob Storage-triggers bieden geen ondersteuning voor chunking. Wanneer u bestandsinhoud opvraagt, selecteren triggers alleen bestanden die 50 MB of kleiner zijn. Volg dit patroon om bestanden van meer dan 50 MB te krijgen:

  * Gebruik een Azure Blob Storage-trigger die bestandseigenschappen retourneert, zoals **Wanneer een blob wordt toegevoegd of gewijzigd (alleen eigenschappen).**

  * Volg de trigger met de actie Azure Blob Storage **Get blob-inhoud,** die het volledige bestand leest en impliciet chunking gebruikt.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Een [Azure-opslagaccount en opslagcontainer](../storage/blobs/storage-quickstart-blobs-portal.md)

* De logische app waar u toegang nodig hebt tot uw Azure blob-opslagaccount. Als u uw logische app wilt starten met een Azure Blob Storage-trigger, hebt u een [lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md)nodig.

<a name="add-trigger"></a>

## <a name="add-blob-storage-trigger"></a>Blob-opslagtrigger toevoegen

In Azure Logic Apps moet elke logische app beginnen met een [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts), die wordt geactiveerd wanneer een specifieke gebeurtenis plaatsvindt of wanneer aan een specifieke voorwaarde is voldaan. Elke keer dat de trigger wordt geactiveerd, maakt de Logic Apps-engine een logische app-instantie en wordt de werkstroom van uw app gestart.

In dit voorbeeld ziet u hoe u een werkstroom voor logische apps starten met de **trigger Wanneer een blob wordt toegevoegd of gewijzigd (alleen eigenschappen)** wanneer de eigenschappen van een blob worden toegevoegd of bijgewerkt in uw opslagcontainer.

1. Maak in de [Azure-portal](https://portal.azure.com) of Visual Studio een lege logische app, waarmee Logic App Designer wordt geopend. In dit voorbeeld wordt de Azure-portal gebruikt.

2. Voer in het zoekvak 'azure blob' in als filter. Selecteer in de lijst triggers de gewenste trigger.

   In dit voorbeeld wordt deze trigger gebruikt: **wanneer een blob wordt toegevoegd of gewijzigd (alleen eigenschappen)**

   ![De trigger voor Azure Blob Storage selecteren](./media/connectors-create-api-azureblobstorage/add-azure-blob-storage-trigger.png)

3. Als u wordt gevraagd om verbindingsgegevens, [maakt u nu de blob-opslagverbinding](#create-connection). Of, als uw verbinding al bestaat, de nodige informatie voor de trigger.

   Selecteer in dit voorbeeld de container en map die u wilt controleren.

   1. Selecteer in het vak **Container** het mappictogram.

   2. Kies in de mappenlijst de haakhaak () **>** en blader totdat u de gewenste map vindt en selecteert u de gewenste map.

      ![Opslagmap selecteren die met trigger moet worden gebruikt](./media/connectors-create-api-azureblobstorage/trigger-select-folder.png)

   3. Selecteer het interval en de frequentie voor hoe vaak u wilt dat de trigger de map controleert op wijzigingen.

4. Wanneer u klaar bent, kiest u op de werkbalk van de ontwerper de optie **Opslaan**.

5. Ga nu verder met het toevoegen van een of meer acties aan uw logische app voor de taken die u wilt uitvoeren met de triggerresultaten.

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>Actie blobopslag toevoegen

In Azure Logic Apps is een [actie](../logic-apps/logic-apps-overview.md#logic-app-concepts) een stap in uw werkstroom die een trigger of een andere actie volgt. In dit voorbeeld begint de logische app met de [recidieftrigger](../connectors/connectors-native-recurrence.md).

1. Open uw logische app in de [Azure-portal](https://portal.azure.com) of Visual Studio in Logic App Designer. In dit voorbeeld wordt de Azure-portal gebruikt.

2. Kies in de Logic App Designer onder de trigger of actie de optie **Nieuwe stap**.

   ![Nieuwe stap toevoegen aan de werkstroom van logische apps](./media/connectors-create-api-azureblobstorage/add-new-step-logic-app-workflow.png) 

   Als u een actie tussen bestaande stappen wilt toevoegen, verplaatst u de muis over de verbindingspijl. Kies het plusteken (**+**) dat wordt weergegeven en selecteer Een actie **toevoegen**.

3. Voer in het zoekvak 'azure blob' in als filter. Selecteer in de lijst met acties de gewenste actie.

   In dit voorbeeld wordt de actie gebruikt: **Blob-inhoud opmaken**

   ![De actie Azure Blob Storage selecteren](./media/connectors-create-api-azureblobstorage/add-azure-blob-storage-action.png)

4. Als u wordt gevraagd om verbindingsgegevens, [maakt u nu uw Azure Blob Storage-verbinding](#create-connection).
Of, als uw verbinding al bestaat, de nodige informatie voor de actie.

   Selecteer in dit voorbeeld het gewenste bestand.

   1. Selecteer in het vak **Klodder** het mappictogram.
  
      ![Opslagmap selecteren die u met actie wilt gebruiken](./media/connectors-create-api-azureblobstorage/action-select-folder.png)

   2. Zoek en selecteer het gewenste bestand op basis van het **id-nummer** van de blob. U dit **ID-nummer** vinden in de metagegevens van de blob die worden geretourneerd door de eerder beschreven blobopslagtrigger.

5. Wanneer u klaar bent, kiest u op de werkbalk van de ontwerper de optie **Opslaan**.
Als u uw logica-app wilt testen, moet u ervoor zorgen dat de geselecteerde map een blob bevat.

In dit voorbeeld wordt alleen de inhoud voor een blob optewel. Als u de inhoud wilt weergeven, voegt u een andere actie toe die een bestand met de blob maakt met behulp van een andere connector. Voeg bijvoorbeeld een OneDrive-actie toe waarmee een bestand wordt gemaakt op basis van de blob-inhoud.

<a name="create-connection"></a>

## <a name="connect-to-storage-account"></a>Verbinding maken met een opslagaccount

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Wanneer u wordt gevraagd de verbinding te maken, geeft u deze informatie op:

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **Verbindingsnaam** | Ja | <*verbindingsnaam*> | De naam die u wilt maken voor uw verbinding |
   | **Opslagaccount** | Ja | <*opslagaccount*> | Selecteer uw opslagaccount in de lijst. |
   ||||

   Bijvoorbeeld:

   ![Azure Blob-opslagaccountverbinding maken](./media/connectors-create-api-azureblobstorage/create-storage-account-connection.png) 

1. Wanneer u er klaar voor bent, selecteert u **Maken**

1. Nadat u uw verbinding hebt gemaakt, gaat u verder met [Blob-opslagtrigger toevoegen](#add-trigger) of [Blob-opslagactie toevoegen](#add-action).

## <a name="connector-reference"></a>Connector-verwijzing

Zie de [referentiepagina van](https://docs.microsoft.com/connectors/azureblobconnector/)de connector voor meer technische details over deze connector, zoals triggers, acties en limieten zoals beschreven in het Swagger-bestand van de connector.

> [!NOTE]
> Voor logische apps in een [integratieserviceomgeving (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)gebruikt de ISE-versie met HET LABEL ISE in plaats daarvan de [ISE-berichtlimieten.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

<a name="storage-firewalls"></a>

## <a name="access-storage-accounts-behind-firewalls"></a>Toegang tot opslagaccounts achter firewalls

U netwerkbeveiliging toevoegen aan een Azure-opslagaccount door de toegang te beperken met een [firewall en firewallregels.](../storage/common/storage-network-security.md) Deze instelling creëert echter een uitdaging voor Azure en andere Microsoft-services die toegang tot het opslagaccount nodig hebben. Lokale communicatie in het datacenter abstrahert de interne IP-adressen, zodat u geen firewallregels instellen met IP-beperkingen. Raadpleeg [Firewalls en virtuele netwerken voor Azure Storage configureren](../storage/common/storage-network-security.md) voor meer informatie.

Hier volgen verschillende opties voor toegang tot opslagaccounts achter firewalls van Azure Logic Apps met behulp van de Azure Blob Storage-connector of andere oplossingen:

* Azure Storage Blob-connector

  * [Toegang tot opslagaccounts in andere regio's](#access-other-regions)
  * [Toegang tot opslagaccounts via een vertrouwd virtueel netwerk](#access-trusted-virtual-network)

* Andere oplossingen

  * [Toegang tot opslagaccounts als een vertrouwde service met beheerde identiteiten](#access-trusted-service)
  * [Toegang tot opslagaccounts via Azure API-beheer](#access-api-management)

<a name="access-other-regions"></a>

### <a name="problems-accessing-storage-accounts-in-the-same-region"></a>Problemen met de toegang tot opslagaccounts in dezelfde regio

Logische apps hebben geen directe toegang tot opslagaccounts achter firewalls wanneer ze zich beide in dezelfde regio bevinden. Als tijdelijke oplossing plaatst u uw logische apps in een regio die verschilt van uw opslagaccount en geeft u toegang tot de [uitgaande IP-adressen voor de beheerde connectors in uw regio.](../logic-apps/logic-apps-limits-and-config.md#outbound)

> [!NOTE]
> Deze oplossing is niet van toepassing op de Azure Table Storage-connector en Azure Queue Storage-connector. Gebruik in plaats daarvan de ingebouwde HTTP-trigger en acties om toegang te krijgen tot de tabelopslag of wachtrijopslag.

<a name="access-trusted-virtual-network"></a>

### <a name="access-storage-accounts-through-a-trusted-virtual-network"></a>Toegang tot opslagaccounts via een vertrouwd virtueel netwerk

U het opslagaccount in een virtueel Azure-netwerk plaatsen dat u beheert en dat virtuele netwerk vervolgens toevoegen aan de lijst met vertrouwde virtuele netwerken. Als u uw logische app toegang wilt geven tot het opslagaccount via een [vertrouwd virtueel netwerk,](../virtual-network/virtual-networks-overview.md)moet u die logische app implementeren in een [integratieserviceomgeving (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)die verbinding kan maken met bronnen in een virtueel netwerk. Vervolgens u de subnetten in die ISE toevoegen aan de vertrouwde lijst. Azure Storage-connectors, zoals de Blob Storage-connector, hebben rechtstreeks toegang tot de opslagcontainer. Deze instelling is dezelfde ervaring als het gebruik van de serviceeindpunten van een ISE.

<a name="access-trusted-service"></a>

### <a name="access-storage-accounts-as-a-trusted-service-with-managed-identities"></a>Toegang tot opslagaccounts als een vertrouwde service met beheerde identiteiten

Als u Microsoft trusted services toegang wilt geven tot een opslagaccount via een firewall, u een uitzondering instellen op dat opslagaccount voor die services. Met deze oplossing kunnen Azure-services [beheerde identiteiten voor verificatie](../active-directory/managed-identities-azure-resources/overview.md) ondersteunen om toegang te krijgen tot opslagaccounts achter firewalls als vertrouwde services. Voor een logische app in het wereldwijde Azure met meerdere tenant's om toegang te krijgen tot deze opslagaccounts, schakelt u eerst [beheerde identiteitsondersteuning in](../logic-apps/create-managed-service-identity.md) in de logische app. Vervolgens gebruikt u de HTTP-actie of trigger in uw logische app en [stelt u het verificatietype in om de beheerde identiteit van uw logische app te gebruiken.](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity) Voor dit scenario u *alleen* de HTTP-actie of -trigger gebruiken.

Voer de volgende algemene stappen uit om de uitzondering en beheerde identiteitsondersteuning in te stellen:

1. Selecteer **firewalls en virtuele netwerken**in uw opslagaccount onder **Instellingen**. Selecteer **onder Toegang toestaan vanuit**de optie Geselecteerde **netwerken** selecteren zodat de gerelateerde instellingen worden weergegeven.

1. Selecteer **onder Uitzonderingen**de optie Vertrouwde **Microsoft-services toestaan om toegang te krijgen tot dit opslagaccount**en selecteer **Opslaan**.

   ![Uitzondering selecteren waarmee vertrouwde microsoft-services kunnen worden](./media/connectors-create-api-azureblobstorage/allow-trusted-services-firewall.png)

1. Schakel in de instellingen van uw logische app [ondersteuning in voor de beheerde identiteit.](../logic-apps/create-managed-service-identity.md)

1. Voeg in de werkstroom van uw logische app de HTTP-actie of -trigger toe en stel deze in om toegang te krijgen tot het opslagaccount of de opslagentiteit.

   > [!IMPORTANT]
   > Controleer ervoor dat de hoofdtekst de eigenschap en de `x-ms-version` API-versie bevat voor de bewerking die u op het opslagaccount wilt uitvoeren, voor uitgaande HTTP-actie of oproepen naar Azure Storage-accounts activeren. Zie [Toegang verifiëren met beheerde identiteit](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity) en [Versioning voor Azure Storage-services voor](https://docs.microsoft.com/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests)meer informatie.

1. Selecteer bij die actie [de beheerde identiteit](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity) die u wilt gebruiken voor verificatie.

<a name="access-api-management"></a>

### <a name="access-storage-accounts-through-azure-api-management"></a>Toegang tot opslagaccounts via Azure API-beheer

Als u een speciale laag voor [API-beheer](../api-management/api-management-key-concepts.md)gebruikt, u de Opslag-API vooraan zetten met API-beheer en de IP-adressen van deze laatste toestaan via de firewall. Voeg in principe het virtuele Azure-netwerk toe dat door API-beheer wordt gebruikt, aan de firewall-instelling van het opslagaccount. U vervolgens de actie API-beheer of de HTTP-actie gebruiken om de Azure Storage API's aan te roepen. Als u echter voor deze optie kiest, moet u het verificatieproces zelf afhandelen. Zie [Eenvoudige architectuur voor bedrijfsintegratie](https://aka.ms/aisarch)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic Apps-connectors](../connectors/apis-list.md)
