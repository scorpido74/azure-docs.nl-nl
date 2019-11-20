---
title: 'Zelf studie: WAF-beleid maken voor Azure front-deur-Azure Portal'
description: In deze zelf studie leert u hoe u een WAF-beleid (Web Application firewall) kunt maken met behulp van de Azure Portal.
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 09/07/2019
ms.author: victorh
ms.openlocfilehash: 991111e01713afe48355aac44a151b98fa828c5f
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186730"
---
# <a name="tutorial-create-a-web-application-firewall-policy-on-azure-front-door-using-the-azure-portal"></a>Zelf studie: een firewall beleid voor webtoepassingen maken op de voor deur van Azure met behulp van de Azure Portal

In deze zelf studie leert u hoe u een basis beleid voor Azure Web Application firewall (WAF) maakt en dit toepast op een front-end-host op Azure front deur.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een WAF-beleid maken
> * Deze koppelen aan een frontend-host
> * WAF-regels configureren

## <a name="prerequisites"></a>Vereisten

Maak een voor deur profiel door de instructies te volgen die worden beschreven in [Quick Start: een front deur-profiel maken](../../frontdoor/quickstart-create-front-door.md). 

## <a name="create-a-web-application-firewall-policy"></a>Een firewall beleid voor webtoepassingen maken

Maak eerst een basis-WAF-beleid met beheerde standaard regelset (DRS) met behulp van de portal. 

1. Selecteer in de linkerbovenhoek van het scherm de optie **een resource maken**> zoek naar **WAF**> Selecteer **Web application firewall (preview)** > Selecteer **Create**.
2. Voer op het tabblad **basis beginselen** van de pagina **een WAF-beleid maken** de volgende informatie in of Selecteer deze, accepteer de standaard waarden voor de overige instellingen en selecteer vervolgens **controleren + maken**:

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | Abonnement            |Selecteer de naam van uw front-deur abonnement.|
    | Resourcegroep          |Selecteer de naam van de resource groep voor de voor deur.|
    | Beleids naam             |Voer een unieke naam in voor uw WAF-beleid.|

   ![Een WAF-beleid maken](../media/waf-front-door-create-portal/basic.png)

3. Op het tabblad **koppeling** van de pagina **een WAF-beleid maken** selecteert u **frontend-host toevoegen**, voert u de volgende instellingen in en selecteert u **toevoegen**:

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | Voor deur              | Selecteer de naam van uw front deur-profiel.|
    | Frontend-host           | Selecteer de naam van de voor deur van de host en selecteer vervolgens **toevoegen**.|
    
    > [!NOTE]
    > Als de frontend-host aan een WAF-beleid is gekoppeld, wordt deze als grijs weer gegeven. U moet eerst de frontend-host uit het bijbehorende beleid verwijderen en vervolgens de frontend-host opnieuw koppelen aan een nieuw WAF-beleid.
1. Selecteer **controleren + maken**en selecteer vervolgens **maken**.

## <a name="configure-web-application-firewall-rules-optional"></a>Firewall regels voor webtoepassingen configureren (optioneel)

### <a name="change-mode"></a>Modus wijzigen

Wanneer u een WAF-beleid maakt, bevindt het standaard WAF-beleid zich in de **detectie** modus. In de **detectie** modus worden in plaats daarvan geen aanvragen door WAF geblokkeerd. aanvragen die overeenkomen met de WAF-regels worden in WAF-logboeken vastgelegd.
Als u WAF in actie wilt zien, kunt u de modus instellingen wijzigen van **detectie** in **preventie**. In de **preventie** modus worden aanvragen die overeenkomen met de regels die zijn gedefinieerd in de standaardregelset (drs) geblokkeerd en geregistreerd bij WAF-Logboeken.

 ![WAF-beleids modus wijzigen](../media/waf-front-door-create-portal/policy.png)

### <a name="custom-rules"></a>Aangepaste regels

U kunt een aangepaste regel maken door **aangepaste regel toevoegen** te selecteren onder de sectie **aangepaste regels** . Hiermee opent u de pagina aangepaste regel configuratie. Hieronder ziet u een voor beeld van het configureren van een aangepaste regel voor het blok keren van een aanvraag als de query reeks **blockme**bevat.

![WAF-beleids modus wijzigen](../media/waf-front-door-create-portal/customquerystring2.png)

### <a name="default-rule-set-drs"></a>Standaardregelset (DRS)

De standaard regelset die door Azure wordt beheerd, is standaard ingeschakeld. Als u een afzonderlijke regel binnen een regel groep wilt uitschakelen, vouwt u de regels binnen die regel groep uit, selecteert u het **selectie vakje** vóór het regel nummer en selecteert u **uitschakelen** op het bovenstaande tabblad. Als u de typen acties voor afzonderlijke regels in de regelset wilt wijzigen, schakelt u het selectie vakje vóór het regel nummer in en selecteert u vervolgens het tabblad **wijzigings actie** hierboven.

 ![WAF Rule set wijzigen](../media/waf-front-door-create-portal/managed2.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> Meer informatie [over de firewall van Azure Web Application](../overview.md)
> [meer informatie over de voor deur van Azure](../../frontdoor/front-door-overview.md)