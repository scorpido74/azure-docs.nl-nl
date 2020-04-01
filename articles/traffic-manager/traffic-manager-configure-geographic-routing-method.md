---
title: Zelfstudie - Routering van geografisch verkeer configureren met Azure Traffic Manager
description: In deze zelfstudie wordt uitgelegd hoe u de methode voor het routeren van geografisch verkeer configureert met Azure Traffic Manager
services: traffic-manager
author: rohinkoul
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: rohink
ms.openlocfilehash: 3eb3f354d51833e55f405ed35679f1a5882c057a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76938794"
---
# <a name="tutorial-configure-the-geographic-traffic-routing-method-using-traffic-manager"></a>Zelfstudie: De routeringsmethode voor geografisch verkeer configureren met Traffic Manager

Met de methode voor het routeren van geografisch verkeer u verkeer naar specifieke eindpunten leiden op basis van de geografische locatie waar de aanvragen vandaan komen. In deze zelfstudie ziet u hoe u een Traffic Manager-profiel maakt met deze routeringsmethode en de eindpunten configureert om verkeer uit specifieke regio's te ontvangen.

## <a name="create-a-traffic-manager-profile"></a>Een profiel van verkeersbeheer maken

1. Meld u vanuit een browser aan bij [Azure Portal](https://portal.azure.com). Als u nog geen account hebt, kunt u zich registreren voor een [gratis proefversie van één maand](https://azure.microsoft.com/free/).
2. Klik op Een**netwerkbeheerprofiel** > maken voor een > **resourcenetwerkbeheer** > **maken**. **Create a resource**
4. Maak in **het profiel Verkeersbeheer maken:**
    1. Geef een naam op voor je profiel. Deze naam moet uniek zijn binnen de trafficmanager.net zone. Als u toegang wilt krijgen tot `<profilename>.trafficmanager.net`uw Traffic Manager-profiel, gebruikt u de DNS-naam .
    2. Selecteer de **methode voor geografische** routering.
    3. Selecteer hieronder het abonnement dat u wilt maken.
    4. Gebruik een bestaande resourcegroep of maak een nieuwe resourcegroep om dit profiel onder te plaatsen. Als u ervoor kiest een nieuwe resourcegroep te maken, gebruikt u de **vervolgkeuzelijst Resourcegroep om** de locatie van de resourcegroep op te geven. Deze instelling verwijst naar de locatie van de resourcegroep en heeft geen invloed op het traffic manager-profiel dat wereldwijd wordt geïmplementeerd.
    5. Nadat u op **Maken**hebt geklikt, wordt uw Traffic Manager-profiel wereldwijd gemaakt en geïmplementeerd.

![Een Traffic Manager-profiel maken](./media/traffic-manager-geographic-routing-method/create-traffic-manager-profile.png)

## <a name="add-endpoints"></a>Eindpunten toevoegen

1. Zoek naar de profielnaam van Traffic Manager die u hebt gemaakt in de zoekbalk van de portal en klik op het resultaat wanneer deze wordt weergegeven.
2. Navigeer naar**Eindpunten** **instellingen** -> in Traffic Manager.
3. Klik **op Toevoegen** om het eindpunt **toevoegen**weer te geven .
3. Klik **op Toevoegen** en in het weergegeven eindpunt **Toevoegen** als volgt:
4. Selecteer **Tekst,** afhankelijk van het type eindpunt dat u toevoegt. Voor geografische routeringsprofielen die in de productie worden gebruikt, raden we ten zeerste aan geneste eindpunttypen te gebruiken die een onderliggend profiel bevatten met meer dan één eindpunt. Zie [veelgestelde vragen over methoden voor het routeren van geografisch verkeer](traffic-manager-FAQs.md)voor meer informatie.
5. Geef een **Naam** op waarmee u dit eindpunt kunt herkennen.
6. Bepaalde velden op deze pagina zijn afhankelijk van het type eindpunt dat u toevoegt:
    1. Als u een Azure-eindpunt toevoegt, selecteert u het **doelbrontype** en het **doel** op basis van de bron waarnaar u verkeer wilt leiden
    2. Als u een **extern** eindpunt toevoegt, geeft u de **volledig gekwalificeerde domeinnaam (FQDN)** op voor uw eindpunt.
    3. Als u een **genest eindpunt**toevoegt, selecteert u de **doelbron** die overeenkomt met het onderliggende profiel dat u wilt gebruiken en geeft u het **aantal minimumaantal onderliggende eindpunten op.**
7. Gebruik in de sectie Geo-toewijzing de vervolgkeuzelijst om de regio's toe te voegen van waaruit u wilt dat verkeer naar dit eindpunt wordt verzonden. U moet ten minste één regio toevoegen en u meerdere regio's in kaart brengen.
8. Herhaal dit voor alle eindpunten die u onder dit profiel wilt toevoegen

![Traffic Manager-eindpunt toevoegen](./media/traffic-manager-geographic-routing-method/add-traffic-manager-endpoint.png)

## <a name="use-the-traffic-manager-profile"></a>Het profiel Verkeersbeheer gebruiken
1.  Zoek in de zoekbalk van de portal naar de **profielnaam Van Verkeersbeheer** die u in de vorige sectie hebt gemaakt en klik op het profiel van de verkeersbeheerder in de resultaten die de weergegeven resultaten hebben weergegeven.
2. Klik op **Overzicht**.
3. Het **Traffic Manager-profiel** geeft de DNS-naam weer van het Traffic Manager-profiel dat u zojuist hebt gemaakt. Dit kan door alle clients worden gebruikt (bijvoorbeeld door er met een webbrowser naar toe te navigeren) om naar het juiste eindpunt te worden geleid, zoals bepaald door het routeringstype.  In het geval van geografische routering kijkt Traffic Manager naar het bron-IP van de binnenkomende aanvraag en bepaalt het gebied waaruit deze afkomstig is. Als dat gebied is toegewezen aan een eindpunt, wordt het verkeer daar naartoe gerouteerd. Als dit gebied niet is toegewezen aan een eindpunt, retourneert Traffic Manager een NODATA-queryantwoord.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [de methode voor het routeren van geografisch verkeer](traffic-manager-routing-methods.md#geographic).
- Meer informatie over het testen van [traffic manager-instellingen](traffic-manager-testing-settings.md).
