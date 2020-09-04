---
title: 'Zelfstudie: WAF-beleid maken voor Azure CDN - Azure Portal'
description: In deze zelfstudie leert u hoe u een WAF-beleid (Web Application Firewall) kunt maken op Azure CDN met behulp van de Azure Portal.
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 03/18/2020
ms.author: victorh
ms.openlocfilehash: 7a9e0cc3977892fd899b4a25e17ad72f13481506
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/25/2020
ms.locfileid: "82608810"
---
# <a name="tutorial-create-a-waf-policy-on-azure-cdn-using-the-azure-portal"></a>Zelfstudie: Een WAF-beleid maken op Azure CDN met behulp van de Azure Portal

In deze zelfstudie leert u hoe u een basis Azure Web Application Firewall-beleid (WAF) kunt maken en kunt toepassen op een eindpunt op Azure Content Delivery Network (CDN).

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een WAF-beleid maken
> * Dit aan een CDN-eindpunt koppelen. U kunt een WAF-beleid alleen koppelen aan eindpunten die worden gehost op de **Azure CDN Standard van Microsoft** SKU.
> * WAF-regels configureren

## <a name="prerequisites"></a>Vereisten

Maak een Azure CDN-profiel en -eindpunt door de instructies te volgen in [Snelstartgids: Een Azure CDN-profiel en een eindpunt maken](../../cdn/cdn-create-new-endpoint.md). 

## <a name="create-a-web-application-firewall-policy"></a>Een Web Application Firewall-beleid maken

Maak eerst een basis WAF-beleid met beheerde standaardregelset (DRS) met behulp van de portal.

1. Selecteer in de linkerbovenhoek van het scherm **Een resource maken**> zoek naar **WAF**> selecteer **Web Application Firewall** > selecteer **Maken**.
2. Voer op het tabblad **Basis** van de pagina **WAF-beleid maken** de volgende gegevens in of selecteer deze, accepteer de standaardwaarden voor de overige instellingen en selecteer vervolgens **Controleren + maken**:

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | Beleid voor            |Azure CDN selecteren (Preview).|
    | Abonnement            |Selecteer de naam van uw Front Door-abonnement.|
    | Resourcegroep          |Selecteer de naam van uw Front Door-resourcegroep.|
    | Beleidsnaam             |Voer een unieke naam voor uw WAF-beleid in.|

   ![Een WAF-beleid maken](../media/waf-cdn-create-portal/basic.png)

3. Selecteer op het tabblad **Koppeling** van de pagina **Een WAF-beleid maken** de optie **CDN-eindpunt toevoegen**, voer de volgende instellingen in en selecteer **Toevoegen**:

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | CDN-profiel              | Selecteer de naam van uw CDN-profiel.|
    | Eindpunt           | Selecteer de naam van uw eindpunt en selecteer vervolgens **Toevoegen**.|
    
    > [!NOTE]
    > Als het eindpunt is gekoppeld aan een WAF-beleid, wordt dit grijs weergegeven. U moet eerst het eindpunt uit het bijbehorende beleid verwijderen en vervolgens het eindpunt opnieuw koppelen aan een nieuw WAF-beleid.
1. Selecteer **Controleren en maken** en selecteer vervolgens **Maken**.

## <a name="configure-web-application-firewall-policy-optional"></a>Web Application Firewall-beleid configureren (optioneel)

### <a name="change-mode"></a>Modus wijzigen

Wanneer u een WAF-beleid maakt, bevindt het WAF-beleid zich standaard in de modus *Detectie*. In de modus *Detectie* worden er door WAF geen aanvragen geblokkeerd. Aanvragen die overeenkomen met de WAF-regels worden in plaats daarvan vastgelegd in WAF-logboeken.

Als u WAF in actie wilt zien, kunt u de modusinstellingen van *Detectie* wijzigen in *Preventie*. In de modus *Preventie* worden aanvragen die overeenkomen met de regels die zijn gedefinieerd in de standaardregelset (DRS) geblokkeerd en vastgelegd in WAF-logboeken.

 ![WAF-beleidsmodus wijzigen](../media/waf-cdn-create-portal/policy.png)

### <a name="custom-rules"></a>Aangepaste regels

Als u een aangepaste regel wilt maken, selecteert u **Aangepaste regel toevoegen** onder het gedeelte **Aangepaste regels**. Hiermee opent u de pagina voor de configuratie van aangepaste regels. Er zijn twee soorten aangepaste regels: **overeenkomstregel** en **frequentielimiet**regel.

De volgende schermopname toont een aangepaste overeenkomstregel voor het blokkeren van een aanvraag als de querytekenreeks de waarde **blockme** bevat.

![WAF-beleidsmodus wijzigen](../media/waf-cdn-create-portal/custommatch.png)

Voor frequentielimietregels zijn twee extra velden vereist: **Frequentielimietduur** en **Drempelwaarde voor frequentielimietlimiet (aanvragen)** zoals wordt weergegeven in het volgende voorbeeld:

![WAF-beleidsmodus wijzigen](../media/waf-cdn-create-portal/customrate.png)

### <a name="default-rule-set-drs"></a>Standaardregelset (DRS)

De standaardregelset die door Azure wordt beheerd, is standaard ingeschakeld. Als u een afzonderlijke regel binnen een regelgroep wilt uitschakelen, vouwt u de regels binnen die regelgroep uit, schakelt u het selectievakje vóór het regelnummer in en selecteert u **Uitschakelen** op het bovenstaande tabblad. Als u de actietypen voor afzonderlijke regels in de regelset wilt wijzigen, schakelt u het selectievakje vóór het regelnummer in en selecteert u vervolgens het bovenstaande tabblad **Actie wijzigen**.

 ![WAF-regelset wijzigen](../media/waf-cdn-create-portal/managed2.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over Web Application Firewall](../overview.md)
