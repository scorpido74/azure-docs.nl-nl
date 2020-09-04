---
title: 'Zelfstudie: De geografische verkeersrouteringsmethode configureren met Azure Traffic Manager'
description: In deze zelfstudie wordt uitgelegd hoe u de methode voor de routering van geografisch verkeer configureert in Azure Traffic Manager
services: traffic-manager
author: duongau
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: duau
ms.openlocfilehash: 71219eea1c7cdeeefbf63e5a5715fb10fcf68682
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/02/2020
ms.locfileid: "89401075"
---
# <a name="tutorial-configure-the-geographic-traffic-routing-method-using-traffic-manager"></a>Zelfstudie: De geografische verkeersrouteringsmethode configureren met Traffic Manager

Met de geografische verkeersrouteringsmethode kunt u verkeer omleiden naar specifieke eindpunten op basis van de geografische locatie waar de aanvragen vandaan komen. In deze zelfstudie wordt uitgelegd hoe u een Traffic Manager-profiel maakt met deze routeringsmethode en hoe u de eindpunten zo configureert dat verkeer van specifieke geografische locaties wordt ontvangen.

## <a name="create-a-traffic-manager-profile"></a>Een Traffic Manager-profiel maken

1. Meld u vanuit een browser aan bij [Azure Portal](https://portal.azure.com). Als u nog geen account hebt, kunt u zich registreren voor een [gratis proefversie van één maand](https://azure.microsoft.com/free/).
2. Klik op **Een resource maken** > **Netwerken** > **Traffic Manager-profiel** > **Maken**.
4. In het **Traffic Manager-profiel**:
    1. Geef een naam op voor het profiel. Deze naam moet uniek zijn binnen de trafficmanager.net-zone. Voor toegang tot uw Traffic Manager-profiel gebruikt u de DNS-naam `<profilename>.trafficmanager.net`.
    2. Selecteer de **Geografische** routeringsmethode.
    3. Selecteer het abonnement waarvoor u dit profiel wilt maken.
    4. Gebruik een bestaande resourcegroep of maak een nieuwe resourcegroep om dit profiel voor te maken. Als u ervoor kiest om een nieuwe resourcegroep te maken, gebruikt u de vervolgkeuzelijst **Locatie van resourcegroep** om de locatie van de resourcegroep op te geven. Deze instelling verwijst naar de locatie van de resourcegroep en heeft geen invloed op het Traffic Manager-profiel dat wereldwijd wordt geïmplementeerd.
    5. Nadat u op **Maken** hebt geklikt, wordt uw Traffic Manager-profiel gemaakt en wereldwijd geïmplementeerd.

![Een Traffic Manager-profiel maken](./media/traffic-manager-geographic-routing-method/create-traffic-manager-profile.png)

## <a name="add-endpoints"></a>Eindpunten toevoegen

1. Zoek naar de naam van het Traffic Manager-profiel dat u in de zoekbalk van de portal hebt gemaakt en klik op het resultaat wanneer dit wordt weergegeven.
2. Navigeer naar **Instellingen** -> **Eindpunten** in Traffic Manager.
3. Klik op **Toevoegen** om **Eindpunt toevoegen** weer te geven.
3. Klik op **Toevoegen** en voer in het veld **Eindpunt toevoegen** dat wordt weergegeven het volgende in:
4. Selecteer **Type**, afhankelijk van het type eindpunt dat u toevoegt. Voor geografische routeringsprofielen die worden gebruikt in productie, raden we u ten zeerste aan gebruik te maken van geneste eindpunttypen die een onderliggend profiel met meer dan één eindpunt bevatten. Zie [Veelgestelde vragen over geografische verkeersrouteringsmethoden](traffic-manager-FAQs.md) voor meer informatie.
5. Geef een **Naam** op waarmee u dit eindpunt kunt herkennen.
6. Bepaalde velden op deze pagina zijn afhankelijk van het type eindpunt dat u toevoegt:
    1. Als u een Azure-eindpunt toevoegt, selecteert u het **Doelresourcetype** en het **Doel** op basis van de resource waarnaar u verkeer wilt omleiden
    2. Als u een **Extern** eindpunt toevoegt, geeft u de **FQDN (Fully Qualified Domain Name)** voor uw eindpunt op.
    3. Als u een **Genest eindpunt** toevoegt, selecteert u de **Doelresource** die overeenkomt met het onderliggende profiel dat u wilt gebruiken en geeft u het **Minimale aantal onderliggende eindpunten** op.
7. Gebruik in het gedeelte Geo-toewijzing de vervolgkeuzelijst om de regio's toe te voegen van waaruit u wilt dat verkeer naar dit eindpunt wordt gestuurd. U moet ten minste één regio toevoegen en u kunt meerdere regio's toewijzen.
8. Herhaal dit voor alle eindpunten die u wilt toevoegen onder dit profiel

![Traffic Manager-eindpunt toevoegen](./media/traffic-manager-geographic-routing-method/add-traffic-manager-endpoint.png)

## <a name="use-the-traffic-manager-profile"></a>Het Traffic Manager-profiel gebruiken
1.  Zoek in de zoekbalk van de portal de naam van het **Traffic Manager-profiel** dat u in het vorige gedeelte hebt gemaakt en klik op het Traffic Manager-profiel in de weergegeven resultaten.
2. Klik op **Overzicht**.
3. Het **Traffic Manager-profiel** geeft de DNS-naam weer van het Traffic Manager-profiel dat u zojuist hebt gemaakt. Dit kan door clients worden gebruikt (bijvoorbeeld door ernaar te navigeren met een webbrowser) om naar het juiste eindpunt te gaan, zoals wordt bepaald door het routeringstype.  Bij geografische routering kijkt Traffic Manager naar de bron-IP van de binnenkomende aanvraag en bepaalt het de regio van oorsprong. Als die regio is toegewezen aan een eindpunt, wordt er verkeer naar gerouteerd. Als deze regio niet aan een eindpunt is toegewezen, retourneert Traffic Manager een NODATA-queryreactie.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [De geografische verkeersrouteringsmethode](traffic-manager-routing-methods.md#geographic).
- Meer informatie over het [testen van Traffic Manager-instellingen](traffic-manager-testing-settings.md).
