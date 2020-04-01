---
title: 'Zelfstudie: WAF-beleid maken voor Azure Front Door - Azure-portal'
description: In deze zelfstudie leert u hoe u een WAF-beleid (Web Application Firewall) maakt met behulp van de Azure-portal.
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 03/10/2020
ms.author: victorh
ms.openlocfilehash: be66a93ea4a518b26d973d222caf58e73b6986a3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79475838"
---
# <a name="tutorial-create-a-web-application-firewall-policy-on-azure-front-door-using-the-azure-portal"></a>Zelfstudie: Een webtoepassingsfirewallbeleid maken op Azure Front Door met de Azure-portal

In deze zelfstudie ziet u hoe u een basisbeleid voor Azure Web Application Firewall (WAF) maken en toepassen op een front-endhost bij Azure Front Door.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een WAF-beleid maken
> * Associëren met een frontend host
> * WAF-regels configureren

## <a name="prerequisites"></a>Vereisten

Maak een voordeurprofiel door de instructies te volgen die in [Quickstart zijn beschreven: Maak een voordeurprofiel.](../../frontdoor/quickstart-create-front-door.md) 

## <a name="create-a-web-application-firewall-policy"></a>Een firewallbeleid voor webtoepassingen maken

Maak eerst een basisWAF-beleid met beheerde Standaardregelset (DRS) met behulp van de portal. 

1. Selecteer linksboven in het scherm de optie **Een bron maken**>zoeken naar **WAF**>**selecteer webtoepassingsfirewall (Preview)** > selecteer **Maken**.
2. Voer op het tabblad **Basisbeginselen** van de **beleidspagina Een WAF maken** de volgende gegevens in of selecteer deze, accepteer de standaardinstellingen voor de overige instellingen en selecteer **Vervolgens Controleren + maken:**

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | Abonnement            |Selecteer de naam van uw voordeurabonnement.|
    | Resourcegroep          |Selecteer de naam van de brongroep voordeur.|
    | Beleidsnaam             |Voer een unieke naam in voor uw WAF-beleid.|

   ![Een WAF-beleid maken](../media/waf-front-door-create-portal/basic.png)

3. Selecteer op het tabblad **Koppeling** van de **beleidspagina Een WAF maken** de optie **Frontend-host toevoegen,** voer de volgende instellingen in en selecteer **Vervolgens Toevoegen:**

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | Voordeur              | Selecteer de naam van het voordeurprofiel.|
    | Frontend-host           | Selecteer de naam van de host van de voordeur en selecteer **Toevoegen**.|
    
    > [!NOTE]
    > Als de frontendhost is gekoppeld aan een WAF-beleid, wordt deze weergegeven als grijs weergegeven. U moet eerst de frontendhost uit het bijbehorende beleid verwijderen en vervolgens de frontendhost opnieuw koppelen aan een nieuw WAF-beleid.
1. Selecteer **Controleren + maken**en selecteer Vervolgens **Maken**.

## <a name="configure-web-application-firewall-rules-optional"></a>Firewallregels voor webtoepassingen configureren (optioneel)

### <a name="change-mode"></a>De modus wijzigen

Wanneer u een WAF-beleid maakt, bevindt u zich met het standaard WAF-beleid in de **detectiemodus.** In **de detectiemodus** blokkeert WAF geen aanvragen, in plaats daarvan worden aanvragen die overeenkomen met de WAF-regels geregistreerd bij WAF-logboeken.
Als u WAF in actie wilt zien, u de modusinstellingen wijzigen van **Detectie** naar **Preventie.** In **de preventiemodus** worden aanvragen die overeenkomen met regels die zijn gedefinieerd in DrS(Default Rule Set) geblokkeerd en geregistreerd bij WAF-logboeken.

 ![WAF-beleidsmodus wijzigen](../media/waf-front-door-create-portal/policy.png)

### <a name="custom-rules"></a>Aangepaste regels

U een aangepaste regel maken door **aangepaste regel toevoegen** te selecteren onder de sectie Aangepaste **regels.** Hiermee wordt de aangepaste pagina voor de configuratie van regels gestart. Hieronder vindt u een voorbeeld van het configureren van een aangepaste regel om een aanvraag te blokkeren als de querytekenreeks **blokkering**bevat.

![WAF-beleidsmodus wijzigen](../media/waf-front-door-create-portal/customquerystring2.png)

### <a name="default-rule-set-drs"></a>DrS (Standaardregelset)

Standaardregelset met Azure is standaard ingeschakeld. Als u een afzonderlijke regel binnen een regelgroep wilt uitschakelen, vouwt u de regels binnen die regelgroep uit, schakelt u het **selectievakje** voor het regelnummer in en selecteert **u Uitschakelen** op het bovenstaande tabblad. Als u actietypen voor afzonderlijke regels in de regelset wilt wijzigen, schakelt u het selectievakje voor het regelnummer in en schakelt u het tabblad **Actie wijzigen** hierboven in.

 ![WAF-regelset wijzigen](../media/waf-front-door-create-portal/managed2.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over Azure Web Application Firewall](../overview.md)
> [Meer informatie over Azure Front Door](../../frontdoor/front-door-overview.md)