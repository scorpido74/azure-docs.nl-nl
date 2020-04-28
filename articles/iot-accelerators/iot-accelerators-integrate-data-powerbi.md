---
title: Externe bewakings gegevens visualiseren met Power BI-Azure | Microsoft Docs
description: In deze zelf studie wordt gebruikgemaakt van Power BI Desktop en Cosmos DB voor het integreren van gegevens van een oplossing voor externe controle in een aangepaste visualisatie. Op deze manier kunnen gebruikers hun eigen aangepaste Dash boards bouwen en deze delen met gebruikers die geen oplossing hebben.
author: dominicbetts
manager: hegate
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/01/2018
ms.topic: conceptual
ms.openlocfilehash: 8b0f395936c8628ff7b8dab601168538f97ccdb7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74184245"
---
# <a name="visualize-remote-monitoring-data-using-power-bi"></a>Externe bewakings gegevens visualiseren met behulp van Power BI

In deze zelf studie leert u hoe u uw gegevens voor uw externe bewakings oplossing kunt aansluiten bij CosmosDB in Power BI. Als deze verbinding tot stand is gebracht, kunt u vervolgens uw eigen aangepaste Dash boards maken en deze weer toevoegen aan het dash board van de externe bewakings oplossing. Met deze workstream kunnen er meer gespecialiseerde grafieken worden gemaakt, naast het vak. U kunt deze zelf studie vervolgens gebruiken om te integreren met andere gegevens stromen of om aangepaste Dash boards te bouwen die buiten uw oplossing voor externe controle worden verbruikt. Als u Dash boards in Power BI bouwt, kunt u elk paneel ook met elkaar communiceren wanneer u specifieke onderdelen selecteert. U kunt bijvoorbeeld een filter hebben dat alleen informatie over uw gesimuleerde vracht wagens laat zien en elk deel van uw dash board zou er alleen voor hebben gesimuleerde truck gegevens weer te geven. Als u een andere hulp middelen wilt gebruiken dan Power BI, kunt u deze stappen ook uitvoeren om uw visualisatie hulpmiddel van Choice en Hook in de Cosmos-data base of aangepaste data base te gebruiken als u er een hebt ingesteld. 

## <a name="prerequisites"></a>Vereisten

- Er moet een externe bewakings oplossing op dit moment worden uitgevoerd
- U moet toegang hebben tot [Azure Portal](https://portal.azure.com) en uw abonnement waarop de IOT hub en de oplossing worden uitgevoerd
- U moet [Power bi bureau blad](https://powerbi.microsoft.com) hebben geïnstalleerd, alle versies kunnen


## <a name="information-needed-from-azure-portal"></a>Informatie die nodig is van Azure Portal

1. Ga naar [Azure Portal](https://portal.azure.com) en meld u indien nodig aan.

2. Klik in het linkerdeel venster op resource groepen

    ![Navigatie van het paneel aan de zijkant](./media/iot-accelerators-integrate-data-powerbi/side_panel.png)

3. Ga naar de resource groep waar uw IOT-oplossing wordt uitgevoerd en klik op de pagina overzicht van de resource groep. 

4. Klik op de pagina overzicht op het item met het type Azure Cosmos DB account. vervolgens wordt u naar de pagina overzicht van de Cosmos DB-stroom geleid voor die IoT-oplossing.

    ![Resourcegroep](./media/iot-accelerators-integrate-data-powerbi/resource_groups.png)

5. Klik in het deel venster aan de linkerkant op de sectie ' sleutels ' en noteer de volgende waarden die moeten worden gebruikt in Power BI:

   - URI
   - Primaire sleutel

     ![keys](./media/iot-accelerators-integrate-data-powerbi/keys.png)

## <a name="setting-up-the-stream-in-power-bi"></a>De stroom instellen in Power BI
  
1. Open de Power BI bureau blad-app en klik op gegevens ophalen in de linkerbovenhoek. 

    ![Gegevens ophalen](./media/iot-accelerators-integrate-data-powerbi/get_data.png)

2. Wanneer u wordt gevraagd gegevens in te voeren, kiest u voor ' Azure Cosmos DB ' en selecteert u deze connector. Met deze connector worden gegevens in feite direct opgehaald uit de Cosmos-data base van uw Azure IoT-oplossing
  
    ![Cosmos DB](./media/iot-accelerators-integrate-data-powerbi/cosmos_db.png)
  
3. Voer de informatie in die u hierboven hebt vastgelegd:

    * URI
    * Primaire sleutel

4. Selecteer alle tabellen die u wilt importeren in Power BI. Met deze actie wordt het laden van de gegevens gestart. Hoe langer uw oplossing actief is, hoe langer het duurt om de gegevens te laden (tot een paar uur). 

    ![Tabellen importeren](./media/iot-accelerators-integrate-data-powerbi/import_tables.png)

5. Zodra de gegevens zijn geladen, klikt u op Query's bewerken in de bovenste rij van Power BI en vouwt u alle tabellen uit door te klikken op de pijlen in de gele balk voor elke tabel. Dit wordt in wezen uitgebreid om alle kolommen weer te geven. U zult merken dat gegevens voor zaken zoals vochtigheid, snelheid, enzovoort, niet van het juiste type zijn.

    ![Nieuwe kolom](./media/iot-accelerators-integrate-data-powerbi/new_column.png)
  
    De gegevens die in Power BI worden ontvangen, zijn bijvoorbeeld gewijzigd in UNIX-tijd, toen deze zich bevond via de connector. Als u deze conversie wilt aanpassen, kunt u een nieuwe kolom maken en deze vergelijking gebruiken om deze op te halen in datum-en tijd notatie: 

    ```text
    #datetime(1970, 1, 1, 0, 0, 0) + #duration(0, 0, 0, [Document.device.msg.received]/1000)
    ```

    ![Bijgewerkte tabel](./media/iot-accelerators-integrate-data-powerbi/updated_table.png)
  
    Document. device. msg. Receive is slechts een van de kolommen met UNIX-indeling en kan worden vervangen door anderen die moeten worden geconverteerd. 
  
    Andere gegevens punten zijn geconverteerd naar een type teken reeks kunnen waar nodig worden gewijzigd in dubbels of int, indien van toepassing, met behulp van dezelfde stappen als hierboven.

## <a name="creating-a-dashboard"></a>Een dash board maken

Zodra de stroom is verbonden, bent u klaar om uw persoonlijke Dash boards te maken. Het onderstaande dash board is een voor beeld van het maken van de telemetrie die door onze gesimuleerde apparaten wordt verzonden en het weer geven van verschillende draaiingen eromheen, zoals: 

* De locatie van het apparaat op een kaart (rechts)
* Apparaten met hun status en ernst. (linksboven)
* Apparaten met regels en als er waarschuwingen voor hen worden uitgevoerd (linksonder)

![Power bi-visualisatie](./media/iot-accelerators-integrate-data-powerbi/visual_data.png)

## <a name="publishing-the-dashboard-and-refreshing-the-data"></a>Het dash board publiceren en de gegevens vernieuwen

Nadat u uw Dash boards hebt gemaakt, raden we u aan om [uw Power bi-Dash boards te publiceren](https://docs.microsoft.com/power-bi/desktop-upload-desktop-files) en te delen met anderen.

U moet ook [de gegevens](https://docs.microsoft.com/power-bi/refresh-data) op het gepubliceerde dash board vernieuwen om ervoor te zorgen dat u over de nieuwste gegevensset beschikt.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u externe bewakings gegevens kunt visualiseren met behulp van Power BI

Zie voor meer informatie over het aanpassen van de oplossing voor externe controle:

* [De gebruikers interface van de oplossing voor externe controle aanpassen](iot-accelerators-remote-monitoring-customize.md)
* [Snelzoekgids voor ontwikkelaars](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Ontwikkelaarsgids voor het oplossen van problemen](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

