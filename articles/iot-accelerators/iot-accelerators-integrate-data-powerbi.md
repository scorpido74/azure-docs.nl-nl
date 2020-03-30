---
title: Gegevens voor externe bewaking visualiseren met Power BI - Azure | Microsoft Documenten
description: Deze zelfstudie maakt gebruik van Power BI Desktop en Cosmos DB om gegevens van een remote monitoring-oplossing te integreren in een aangepaste visualisatie. Op deze manier kunnen gebruikers hun eigen aangepaste dashboards bouwen en delen met gebruikers die niet op de oplossing staan.
author: dominicbetts
manager: hegate
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/01/2018
ms.topic: conceptual
ms.openlocfilehash: 8b0f395936c8628ff7b8dab601168538f97ccdb7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74184245"
---
# <a name="visualize-remote-monitoring-data-using-power-bi"></a>Externe bewakingsgegevens visualiseren met Power BI

In deze zelfstudie leest u hoe u uw remote monitoring-oplossingsgegevens van CosmosDB aansluiten op Power BI. Met deze verbinding u vervolgens uw eigen aangepaste dashboards maken en deze weer toevoegen aan uw dashboard met externe bewakingsoplossingen. Deze workstream maakt het mogelijk om meer gespecialiseerde grafieken te maken, naast die out-of-the-box. U deze zelfstudie vervolgens gebruiken om te integreren met andere gegevensstromen of aangepaste dashboards te bouwen die buiten uw oplossing voor externe bewaking moeten worden verbruikt. Het bouwen van dashboards in Power BI betekent dat u elk paneel ook met elkaar laten communiceren terwijl u specifieke stukken selecteert. U bijvoorbeeld een filter hebben waarmee u alleen informatie over uw gesimuleerde trucks weergeeft en elk onderdeel van uw dashboard zou samenwerken om u alleen gesimuleerde truckinformatie te laten zien. Als u een ander hulpmiddel dan Power BI wilt gebruiken, u deze stappen ook uitbreiden om uw visualisatietool naar keuze te gebruiken en aan te sluiten op de Cosmos Database of aangepaste database als u er een hebt ingesteld. 

## <a name="prerequisites"></a>Vereisten

- U moet een oplossing voor externe bewaking hebben die momenteel wordt uitgevoerd
- U moet toegang hebben tot [de Azure-portal](https://portal.azure.com) en uw abonnement waarop de IoT Hub en -oplossing worden uitgevoerd
- U moet [Power BI-bureaublad](https://powerbi.microsoft.com) hebben geïnstalleerd, elke versie is


## <a name="information-needed-from-azure-portal"></a>Informatie die nodig is via Azure-portal

1. Navigeren naar [Azure-portal](https://portal.azure.com) en aanmelden indien nodig

2. Klik in het linkerdeelvenster op Resourcegroepen

    ![Navigatie van het zijpaneel](./media/iot-accelerators-integrate-data-powerbi/side_panel.png)

3. Navigeer naar de resourcegroep waarop uw Iot-oplossing wordt uitgevoerd en klik om naar de overzichtspagina van die resourcegroep te worden geleid. 

4. Klik op die overzichtspagina op het item, met het type "Azure Cosmos DB-account", en vervolgens wordt u naar de overzichtspagina van de Cosmos DB-stream voor die IoT-oplossing geleid.

    ![Resourcegroep](./media/iot-accelerators-integrate-data-powerbi/resource_groups.png)

5. Klik in het deelvenster aan de linkerkant op de sectie 'Sleutels' en noteer de volgende waarden die in Power BI moeten worden gebruikt:

   - URI
   - Primaire sleutel

     ![keys](./media/iot-accelerators-integrate-data-powerbi/keys.png)

## <a name="setting-up-the-stream-in-power-bi"></a>De stream instellen in Power BI
  
1. Open de Power BI-bureaubladapp en klik op 'Gegevens ophalen' in de linkerbovenhoek. 

    ![Gegevens ophalen](./media/iot-accelerators-integrate-data-powerbi/get_data.png)

2. Wanneer u wordt gevraagd gegevens in te voeren, kiest u voor Zoeken naar 'Azure Cosmos DB' en selecteert u deze connector. Deze connector haalt gegevens in wezen rechtstreeks uit de cosmos-database van uw Azure IoT-oplossing
  
    ![Cosmos DB](./media/iot-accelerators-integrate-data-powerbi/cosmos_db.png)
  
3. Voer de gegevens in die u hierboven hebt geregistreerd:

    * URI
    * Primaire sleutel

4. Selecteer alle tabellen die in Power BI moeten worden geïmporteerd. Deze actie zal het laden van de gegevens van start gaan. Hoe langer uw oplossing is uitgevoerd, hoe langer het kan duren voordat de gegevens worden geladen (tot een paar uur). 

    ![Tabellen importeren](./media/iot-accelerators-integrate-data-powerbi/import_tables.png)

5. Zodra de gegevens zijn geladen, klikt u op Query's bewerken op de bovenste rij van Power BI en vouwt u alle tabellen uit door op de pijlen in de gele balk voor elke tabel te klikken. Dit zal in wezen uit te breiden om alle kolommen weer te geven. U zult merken hoe gegevens voor zaken als vochtigheid, snelheid, etc. niet van het juiste type zijn.

    ![Nieuwe kolom](./media/iot-accelerators-integrate-data-powerbi/new_column.png)
  
    De gegevens die in Power BI binnenkomen, zijn bijvoorbeeld gewijzigd in UNIX-tijd toen deze via de connector binnenkwamen. Als u deze conversie wilt aanpassen, u in de toekomst een nieuwe kolom maken en deze vergelijking gebruiken om deze in datumtijdnotatie te krijgen: 

    ```text
    #datetime(1970, 1, 1, 0, 0, 0) + #duration(0, 0, 0, [Document.device.msg.received]/1000)
    ```

    ![Bijgewerkte tabel](./media/iot-accelerators-integrate-data-powerbi/updated_table.png)
  
    Document.device.msg.received is slechts een van de kolommen met UNIX-opmaak en kan worden vervangen door anderen die conversie nodig hebben. 
  
    Andere gegevenspunten die zijn omgezet in type Tekenreeks kunnen worden gewijzigd in Doubles of Int, waar nodig met dezelfde stappen als hierboven.

## <a name="creating-a-dashboard"></a>Een dashboard maken

Zodra de stream is aangesloten, bent u klaar om uw gepersonaliseerde dashboards te maken! Het dashboard hieronder is een voorbeeld van het nemen van de telemetrie wordt uitgezonden door onze gesimuleerde apparaten, en met verschillende draaipunten eromheen, zoals: 

* Apparaatlocatie op een kaart (rechts)
* Apparaten met hun status en ernst. (linksboven)
* Apparaten met regels op zijn plaats, en als er waarschuwingen af gaan voor hen (linksonder)

![PowerBi-visualisatie](./media/iot-accelerators-integrate-data-powerbi/visual_data.png)

## <a name="publishing-the-dashboard-and-refreshing-the-data"></a>Het dashboard publiceren en de gegevens vernieuwen

Nadat u uw dashboards hebt gemaakt, raden we u aan [uw Power BI-dashboards](https://docs.microsoft.com/power-bi/desktop-upload-desktop-files) te publiceren om met anderen te delen.

U wilt ook [de gegevens](https://docs.microsoft.com/power-bi/refresh-data) op het gepubliceerde dashboard vernieuwen om ervoor te zorgen dat u over de nieuwste gegevensset beschikt.

## <a name="next-steps"></a>Volgende stappen

In dit artikel leert u hoe u gegevens over bewaking op afstand visualiseren met Behulp van Power BI

Zie voor meer informatie over het aanpassen van de oplossing voor externe bewaking:

* [De gebruikersinterface van de externe bewakingsoplossing aanpassen](iot-accelerators-remote-monitoring-customize.md)
* [Snelzoekgids voor ontwikkelaars](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Ontwikkelaarsgids voor het oplossen van problemen](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

