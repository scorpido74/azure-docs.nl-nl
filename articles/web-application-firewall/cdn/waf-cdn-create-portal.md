---
title: 'Zelfstudie: WAF-beleid maken voor Azure CDN - Azure-portal'
description: In deze zelfstudie leert u hoe u een WAF-beleid (Web Application Firewall) maakt op Azure CDN met behulp van de Azure-portal.
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 03/18/2020
ms.author: victorh
ms.openlocfilehash: 738be1361bfbd944575abceb08781b241336f6e8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79485592"
---
# <a name="tutorial-create-a-waf-policy-on-azure-cdn-using-the-azure-portal"></a>Zelfstudie: Een WAF-beleid maken op Azure CDN met de Azure-portal

In deze zelfstudie ziet u hoe u een basisbeleid voor Azure Web Application Firewall (WAF) maakt en toepast op een eindpunt op het Azure Content Delivery Network (CDN).

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een WAF-beleid maken
> * Associëren met een CDN-eindpunt
> * WAF-regels configureren

## <a name="prerequisites"></a>Vereisten

Maak een Azure CDN-profiel en eindpunt door de instructies in Quickstart te [volgen: Maak een Azure CDN-profiel en eindpunt](../../cdn/cdn-create-new-endpoint.md). 

## <a name="create-a-web-application-firewall-policy"></a>Een firewallbeleid voor webtoepassingen maken

Maak eerst een basisWAF-beleid met een beheerde Standaardregelset (DRS) met behulp van de portal.

1. Selecteer linksboven in het scherm de optie **Een bron maken**>zoeken naar **WAF**>selecteer firewall voor **webtoepassingen** > selecteer **Maken**.
2. Voer op het tabblad **Basisbeginselen** van de **beleidspagina Een WAF maken** de volgende gegevens in of selecteer deze, accepteer de standaardinstellingen voor de overige instellingen en selecteer **Vervolgens Controleren + maken:**

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | Beleid voor            |Selecteer Azure CDN (Preview).|
    | Abonnement            |Selecteer de naam van uw voordeurabonnement.|
    | Resourcegroep          |Selecteer de naam van de brongroep voordeur.|
    | Beleidsnaam             |Voer een unieke naam in voor uw WAF-beleid.|

   ![Een WAF-beleid maken](../media/waf-cdn-create-portal/basic.png)

3. Selecteer op het tabblad **Koppeling** van de **beleidspagina Een WAF maken** de optie **CDN-eindpunt toevoegen,** voer de volgende instellingen in en selecteer **Vervolgens Toevoegen:**

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | CDN-profiel              | Selecteer uw CDN-profielnaam.|
    | Eindpunt           | Selecteer de naam van uw eindpunt en selecteer **Toevoegen**.|
    
    > [!NOTE]
    > Als het eindpunt is gekoppeld aan een WAF-beleid, wordt het grijs weergegeven. U moet eerst het eindpunt uit het bijbehorende beleid verwijderen en vervolgens het eindpunt opnieuw koppelen aan een nieuw WAF-beleid.
1. Selecteer **Controleren + maken**en selecteer Vervolgens **Maken**.

## <a name="configure-web-application-firewall-policy-optional"></a>Firewallbeleid voor webtoepassingen configureren (optioneel)

### <a name="change-mode"></a>De modus wijzigen

Standaard bevindt het WAF-beleid zich in *de detectiemodus* wanneer u een WAF-beleid maakt. In *de detectiemodus* blokkeert WAF geen aanvragen. In plaats daarvan worden aanvragen die overeenkomen met de WAF-regels geregistreerd bij WAF-logboeken.

Als u WAF in actie wilt zien, u de modusinstellingen wijzigen van *Detectie* naar *Preventie.* In *de preventiemodus* worden aanvragen die overeenkomen met regels die zijn gedefinieerd in DrS(Default Rule Set) geblokkeerd en geregistreerd bij WAF-logboeken.

 ![WAF-beleidsmodus wijzigen](../media/waf-cdn-create-portal/policy.png)

### <a name="custom-rules"></a>Aangepaste regels

Als u een aangepaste regel wilt maken, selecteert u **Aangepaste regel toevoegen** onder de sectie Aangepaste **regels.** Hiermee wordt de aangepaste pagina voor de configuratie van regels geopend. Er zijn twee soorten aangepaste regels: **regel** regel en **tarieflimiet** regel.

In de volgende schermafbeelding wordt een aangepaste overeenkomstregel weergegeven om een aanvraag te blokkeren als de querytekenreeks het **waardeblok bevat.**

![WAF-beleidsmodus wijzigen](../media/waf-cdn-create-portal/custommatch.png)

Regels voor tarieflimiet vereisen twee extra velden: **Duur van de tarieflimiet** en **drempelwaarde voor tariefgrenzen (aanvragen),** zoals weergegeven in het volgende voorbeeld:

![WAF-beleidsmodus wijzigen](../media/waf-cdn-create-portal/customrate.png)

### <a name="default-rule-set-drs"></a>DrS (Standaardregelset)

De standaardregelset voor Azure is standaard ingeschakeld. Als u een afzonderlijke regel binnen een regelgroep wilt uitschakelen, vouwt u de regels binnen die regelgroep uit, schakelt u het selectievakje voor het regelnummer in en selecteert **u Uitschakelen** op het bovenstaande tabblad. Als u actietypen voor afzonderlijke regels in de regelset wilt wijzigen, schakelt u het selectievakje voor het regelnummer in en schakelt u het tabblad **Actie wijzigen** hierboven in.

 ![WAF-regelset wijzigen](../media/waf-cdn-create-portal/managed2.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over Azure Web Application Firewall](../overview.md)
