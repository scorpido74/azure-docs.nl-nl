---
title: 'Zelf studie: geografische verkeers routering configureren met Azure Traffic Manager'
description: In deze zelf studie wordt uitgelegd hoe u de methode voor geografische verkeers routering configureert met Azure Traffic Manager
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
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/01/2020
ms.locfileid: "76938794"
---
# <a name="tutorial-configure-the-geographic-traffic-routing-method-using-traffic-manager"></a>Zelf studie: de geografische verkeers routerings methode configureren met behulp van Traffic Manager

Met de geografische verkeers routerings methode kunt u verkeer omleiden naar specifieke eind punten op basis van de geografische locatie waar de aanvragen vandaan komen. In deze zelf studie wordt uitgelegd hoe u een Traffic Manager profiel maakt met deze routerings methode en hoe u de eind punten zo configureert dat verkeer van specifieke geografi wordt ontvangen.

## <a name="create-a-traffic-manager-profile"></a>Een Traffic Manager profiel maken

1. Meld u vanuit een browser aan bij [Azure Portal](https://portal.azure.com). Als u nog geen account hebt, kunt u zich registreren voor een [gratis proefversie van één maand](https://azure.microsoft.com/free/).
2. Klik op **Een resource maken** > **Netwerken** > **Traffic Manager-profiel** > **Maken**.
4. In het **profiel Traffic Manager maken**:
    1. Geef een naam op voor uw profiel. Deze naam moet uniek zijn binnen de zone trafficmanager.net. Als u toegang wilt krijgen tot uw Traffic Manager-profiel, gebruikt u de DNS-naam `<profilename>.trafficmanager.net`.
    2. Selecteer de **geografische** routerings methode.
    3. Selecteer het abonnement waarvoor u dit profiel wilt maken.
    4. Gebruik een bestaande resource groep of maak een nieuwe resource groep om dit profiel in te plaatsen. Als u ervoor kiest om een nieuwe resource groep te maken, gebruikt u de vervolg keuzelijst **locatie van resource groep** om de locatie van de resource groep op te geven. Deze instelling verwijst naar de locatie van de resource groep en heeft geen invloed op het Traffic Manager profiel dat wereld wijd wordt geïmplementeerd.
    5. Nadat u op **maken**hebt geklikt, wordt uw Traffic Manager profiel wereld wijd gemaakt en geïmplementeerd.

![Een Traffic Manager-profiel maken](./media/traffic-manager-geographic-routing-method/create-traffic-manager-profile.png)

## <a name="add-endpoints"></a>Eind punten toevoegen

1. Zoek de naam van het Traffic Manager profiel dat u hebt gemaakt in de zoek balk van de portal en klik op het resultaat wanneer het wordt weer gegeven.
2. Navigeer naar **instellingen** -> **eind punten** in Traffic Manager.
3. Klik op **toevoegen** om het **eind punt toevoegen**weer te geven.
3. Klik op **toevoegen** en voer in het **eind punt toevoegen** dat wordt weer gegeven, als volgt in:
4. Selecteer **type** , afhankelijk van het type eind punt dat u wilt toevoegen. Voor geografische routerings profielen die worden gebruikt in productie, raden we u ten zeerste aan gebruik te maken van geneste eindpunt typen die een onderliggend profiel met meer dan één eind punt Zie [Veelgestelde vragen over geografische verkeers routerings methoden](traffic-manager-FAQs.md)voor meer informatie.
5. Geef een **Naam** op waarmee u dit eindpunt kunt herkennen.
6. Bepaalde velden op deze pagina zijn afhankelijk van het type eind punt dat u toevoegt:
    1. Als u een Azure-eind punt toevoegt, selecteert u het **doel resource type** en het **doel** op basis van de resource waarnaar u verkeer wilt omleiden
    2. Als u een **extern** eind punt toevoegt, geeft u de FQDN- **naam (Fully Qualified Domain Name)** voor uw eind punt op.
    3. Als u een **genest eind punt**toevoegt, selecteert u de **doel resource** die overeenkomt met het onderliggende profiel dat u wilt gebruiken en geeft u het **minimum aantal van het onderliggende eind punt**op.
7. Gebruik in de sectie Geo-mapping de vervolg keuzelijst om de regio's toe te voegen van waaruit u wilt dat verkeer naar dit eind punt wordt verzonden. U moet ten minste één regio toevoegen en u kunt meerdere regio's toewijzen.
8. Herhaal dit voor alle eind punten die u wilt toevoegen onder dit profiel

![Traffic Manager-eindpunt toevoegen](./media/traffic-manager-geographic-routing-method/add-traffic-manager-endpoint.png)

## <a name="use-the-traffic-manager-profile"></a>Het Traffic Manager profiel gebruiken
1.  Zoek in de zoek balk van de portal naar de naam van het **Traffic Manager profiel** dat u in de voor gaande sectie hebt gemaakt en klik op het Traffic Manager-profiel in de resultaten die worden weer gegeven.
2. Klik op **Overzicht**.
3. Het **Traffic Manager-profiel** geeft de DNS-naam weer van het Traffic Manager-profiel dat u zojuist hebt gemaakt. Dit kan worden gebruikt door clients (bijvoorbeeld door ernaar te navigeren met een webbrowser) om naar het juiste eind punt te gaan, zoals bepaald door het routerings type.  In het geval van geografische route ring zoekt Traffic Manager naar het bron-IP-adres van de binnenkomende aanvraag en bepaalt u de regio van waaruit deze afkomstig is. Als die regio is toegewezen aan een eind punt, wordt er verkeer naar de betreffende Als deze regio niet aan een eind punt is toegewezen, retourneert Traffic Manager een query reactie zonder gegevens.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [methode voor geografische verkeers routering](traffic-manager-routing-methods.md#geographic).
- Meer informatie over het [testen van Traffic Manager instellingen](traffic-manager-testing-settings.md).
