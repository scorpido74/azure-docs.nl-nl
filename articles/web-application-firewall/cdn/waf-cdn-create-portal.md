---
title: 'Zelf studie: WAF-beleid maken voor Azure CDN-Azure Portal'
description: In deze zelf studie leert u hoe u een WAF-beleid (Web Application firewall) maakt op Azure CDN met behulp van de Azure Portal.
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 03/18/2020
ms.author: victorh
ms.openlocfilehash: 738be1361bfbd944575abceb08781b241336f6e8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79485592"
---
# <a name="tutorial-create-a-waf-policy-on-azure-cdn-using-the-azure-portal"></a>Zelf studie: een WAF-beleid maken op Azure CDN met behulp van de Azure Portal

In deze zelf studie leert u hoe u een basis beleid voor Azure Web Application firewall (WAF) maakt en dit toepast op een eind punt op Azure Content Delivery Network (CDN).

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een WAF-beleid maken
> * Deze koppelen aan een CDN-eind punt
> * WAF-regels configureren

## <a name="prerequisites"></a>Vereisten

Maak een Azure CDN profiel en eind punt aan de hand van de instructies in [Quick Start: een Azure CDN profiel en eind punt maken](../../cdn/cdn-create-new-endpoint.md). 

## <a name="create-a-web-application-firewall-policy"></a>Een firewall beleid voor webtoepassingen maken

Maak eerst een Basic-WAF-beleid met een beheerde standaard regelset (DRS) met behulp van de portal.

1. Selecteer in de linkerbovenhoek van het scherm de optie **een resource maken**>Zoek naar **WAF**>selecteer **Web Application firewall** > Selecteer **Create**.
2. Voer op het tabblad **basis beginselen** van de pagina **een WAF-beleid maken** de volgende informatie in of Selecteer deze, accepteer de standaard waarden voor de overige instellingen en selecteer vervolgens **controleren + maken**:

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | Beleid voor            |Selecteer Azure CDN (preview).|
    | Abonnement            |Selecteer de naam van uw front-deur abonnement.|
    | Resourcegroep          |Selecteer de naam van de resource groep voor de voor deur.|
    | Beleidsnaam             |Voer een unieke naam in voor uw WAF-beleid.|

   ![Een WAF-beleid maken](../media/waf-cdn-create-portal/basic.png)

3. Op het tabblad **koppeling** van de pagina **een WAF-beleid maken** selecteert u **CDN-eind punt toevoegen**, voert u de volgende instellingen in en selecteert u **toevoegen**:

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | CDN-profiel              | Selecteer de naam van uw CDN-profiel.|
    | Eindpunt           | Selecteer de naam van het eind punt en selecteer vervolgens **toevoegen**.|
    
    > [!NOTE]
    > Als het eind punt is gekoppeld aan een WAF-beleid, wordt dit grijs weer gegeven. U moet eerst het eind punt verwijderen uit het bijbehorende beleid en het eind punt vervolgens opnieuw koppelen aan een nieuw WAF-beleid.
1. Selecteer **controleren + maken**en selecteer vervolgens **maken**.

## <a name="configure-web-application-firewall-policy-optional"></a>Web Application firewall-beleid configureren (optioneel)

### <a name="change-mode"></a>Modus wijzigen

Het WAF-beleid bevindt zich standaard in de *detectie* modus wanneer u een WAF-beleid maakt. In de *detectie* modus worden door WAF geen aanvragen geblokkeerd. Aanvragen die overeenkomen met de WAF-regels worden in plaats daarvan vastgelegd in WAF-Logboeken.

Als u WAF in actie wilt zien, kunt u de modus instellingen wijzigen van *detectie* in *preventie*. In de *preventie* modus worden aanvragen die overeenkomen met de regels die zijn gedefinieerd in de standaardregelset (drs) geblokkeerd en geregistreerd bij WAF-Logboeken.

 ![WAF-beleids modus wijzigen](../media/waf-cdn-create-portal/policy.png)

### <a name="custom-rules"></a>Aangepaste regels

Als u een aangepaste regel wilt maken, selecteert u **aangepaste regel toevoegen** onder de sectie **aangepaste regels** . Hiermee opent u de pagina aangepaste regel configuratie. Er zijn twee typen aangepaste regels: **overeenkomst regel** en **frequentie limiet** regel.

De volgende scherm afbeelding toont een aangepaste overeenkomst regel voor het blok keren van een aanvraag als de query reeks de waarde **blockme**bevat.

![WAF-beleids modus wijzigen](../media/waf-cdn-create-portal/custommatch.png)

Voor frequentie limiet regels zijn twee extra velden vereist: **frequentie limiet** en **drempel waarde voor frequentie limiet (aanvragen)** , zoals wordt weer gegeven in het volgende voor beeld:

![WAF-beleids modus wijzigen](../media/waf-cdn-create-portal/customrate.png)

### <a name="default-rule-set-drs"></a>Standaardregelset (DRS)

De set met door Azure beheerde standaard regels is standaard ingeschakeld. Als u een afzonderlijke regel binnen een regel groep wilt uitschakelen, vouwt u de regels binnen die regel groep uit, selecteert u het selectie vakje vóór het regel nummer en selecteert u **uitschakelen** op het bovenstaande tabblad. Als u de typen acties voor afzonderlijke regels in de regelset wilt wijzigen, schakelt u het selectie vakje vóór het regel nummer in en selecteert u vervolgens het tabblad **wijzigings actie** hierboven.

 ![WAF Rule set wijzigen](../media/waf-cdn-create-portal/managed2.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over de firewall van Azure Web Application](../overview.md)
