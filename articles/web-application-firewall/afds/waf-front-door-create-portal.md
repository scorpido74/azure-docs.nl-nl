---
title: 'Zelfstudie: WAF-beleid maken voor Azure Front Door - Azure Portal'
description: In deze zelfstudie leert u hoe u een WAF-beleid (Web Application Firewall) kunt maken met behulp van de Azure Portal.
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: victorh
ms.openlocfilehash: 7c7ea5297276ed9a1d1f2ca8f4190997dcab57c3
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/16/2020
ms.locfileid: "90602214"
---
# <a name="tutorial-create-a-web-application-firewall-policy-on-azure-front-door-using-the-azure-portal"></a>Zelfstudie: Een Web Application Firewall-beleid maken op Azure Front Door met behulp van de Azure Portal

In deze zelfstudie leert u hoe u een basis Azure Web Application Firewall-beleid (WAF) kunt maken en kunt toepassen op een frontend-host bij Azure Front Door.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een WAF-beleid maken
> * Dit koppelen aan een frontend-host
> * WAF-regels configureren

## <a name="prerequisites"></a>Vereisten

Maak een Front Door-profiel door de instructies op te volgen in [Quickstart: Een Front Door-profiel maken](../../frontdoor/quickstart-create-front-door.md). 

## <a name="create-a-web-application-firewall-policy"></a>Een Web Application Firewall-beleid maken

Maak eerst een basis WAF-beleid met beheerde standaardregelset (DRS) met behulp van de portal. 

1. Selecteer in de linkerbovenhoek van het scherm **Een resource maken**> zoek naar **WAF**> selecteer **Web Application Firewall (Preview)** > selecteer **Maken**.
2. Voer op het tabblad **Basis** van de pagina **WAF-beleid maken** de volgende gegevens in of selecteer deze, accepteer de standaardwaarden voor de overige instellingen en selecteer vervolgens **Controleren + maken**:

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | Abonnement            |Selecteer de naam van uw Front Door-abonnement.|
    | Resourcegroep          |Selecteer de naam van uw Front Door-resourcegroep.|
    | Beleidsnaam             |Voer een unieke naam voor uw WAF-beleid in.|

   ![Een WAF-beleid maken](../media/waf-front-door-create-portal/basic.png)

3. Selecteer op het tabblad **Koppeling** van de pagina **Een WAF-beleid maken** de optie **Frontend-host toevoegen**, voer de volgende instellingen in en selecteer **Toevoegen**:

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | Front Door              | Selecteer de naam van uw Front Door-profiel.|
    | Frontend-host           | Selecteer de naam van de Front Door-host en selecteer vervolgens **Toevoegen**.|
    
    > [!NOTE]
    > Als de frontend-host aan een WAF-beleid is gekoppeld, wordt deze grijs weergegeven. U moet eerst de frontend-host uit het bijbehorende beleid verwijderen en vervolgens de frontend-host opnieuw koppelen aan een nieuw WAF-beleid.
1. Selecteer **Controleren en maken** en selecteer vervolgens **Maken**.

## <a name="configure-web-application-firewall-rules-optional"></a>Web Application Firewall-regels configureren (optioneel)

### <a name="change-mode"></a>Modus wijzigen

Wanneer u een WAF-beleid maakt, bevindt het WAF-beleid zich standaard in de modus **Detectie**. In de modus **Detectie** blokkeert WAF geen aanvragen, maar worden aanvragen die overeenkomen met de WAF-regels vastgelegd in WAF-logboeken.
Als u WAF in actie wilt zien, kunt u de modusinstellingen van **Detectie** wijzigen in **Preventie**. In de modus **Preventie** worden aanvragen die overeenkomen met de regels die zijn gedefinieerd in de standaardregelset (DRS) geblokkeerd en vastgelegd in WAF-logboeken.

 ![WAF-beleidsmodus wijzigen](../media/waf-front-door-create-portal/policy.png)

### <a name="custom-rules"></a>Aangepaste regels

U kunt een aangepaste regel maken door **Aangepaste regel toevoegen** onder het gedeelte **Aangepaste regels** te selecteren. Hiermee opent u de pagina voor de configuratie van aangepaste regels. Hieronder ziet u een voorbeeld van het configureren van een aangepaste regel voor het blokkeren van een aanvraag als de queryreeks **blockme** bevat.

![Aangepaste regels](../media/waf-front-door-create-portal/customquerystring2.png)

### <a name="default-rule-set-drs"></a>Standaardregelset (DRS)

De standaardregelset die door Azure wordt beheerd, is standaard ingeschakeld. Als u een afzonderlijke regel binnen een regelgroep wilt uitschakelen, vouwt u de regels binnen die regelgroep uit, schakelt u het **selectievakje** vóór het regelnummer in en selecteert u **Uitschakelen** op het bovenstaande tabblad. Als u de actietypen voor afzonderlijke regels in de regelset wilt wijzigen, schakelt u het selectievakje vóór het regelnummer in en selecteert u vervolgens het bovenstaande tabblad **Actie wijzigen**.

 ![WAF-regelset wijzigen](../media/waf-front-door-create-portal/managed2.png)

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de resourcegroep en alle gerelateerde resources als u deze niet meer nodig hebt.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over Azure Front Door](../../frontdoor/front-door-overview.md)