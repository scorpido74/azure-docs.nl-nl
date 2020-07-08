---
title: Application Gateway integratie met Azure Security Center | Microsoft Docs
description: Deze pagina bevat informatie over de integratie van Application Gateway in Azure Security Center.
services: application-gateway
author: vhorne
ms.author: victorh
ms.assetid: e5ea5cf9-3b41-4b85-a12c-e758bff7f3ec
ms.service: application-gateway
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 06/07/2017
ms.openlocfilehash: f5ecd2334ca80f5561c0611239b5bb00d222112a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "76277195"
---
# <a name="overview-of-integration-between-application-gateway-and-azure-security-center"></a>Overzicht van de integratie tussen Application Gateway en Azure Security Center

Lees hoe u met Application Gateway en Security Center uw webtoepassingen kunt beveiligen. Application Gateway Web Application Firewall (WAF) integreert met [Security Center](../security-center/security-center-intro.md) om een naadloze weer gave te bieden voor het voor komen, detecteren en reageren op bedreigingen voor onbeveiligde webtoepassingen in uw omgeving.

## <a name="overview"></a>Overzicht

Application Gateway WAF is een aanbeveling in Security Center voor het beveiligen van webtoepassingen tegen aanvallen en beveiligings problemen. Webingeschakelde resources die niet worden beveiligd door WAF, worden weer gegeven in het Security Center als aanbevelingen met hoge urgentie. Aanbevelingen voor Web Application firewalls worden weer gegeven op de pagina **overzicht** onder **toepassingen**.

![integratie met Security Center][1]

Als u op aanbevelingen met betrekking tot Web Application Firewall klikt, wordt een nieuwe pagina geopend met de details van de aanbeveling.

## <a name="add-a-web-application-firewall-to-an-existing-resource"></a>Een Web Application Firewall toevoegen aan een bestaande resource

Ga naar **alle services**  >  **beveiliging en identiteit**  >  **Security Center** en op **Security Center-overzicht**en klik op **toepassingen**. In **Security Center-toepassingen**bevat de tabel een lijst met toepassingen die Security Center gedetecteerd in uw abonnement.

![webtoepassingen][3]

Als u op een webtoepassing met een kritiek probleem klikt, wordt de pagina **beveiligings status** van de toepassing weer geven. In de onderstaande afbeelding wordt de webtoepassing die niet wordt beveiligd door een Web Application Firewall. 

![niet-beveiligde webbronnen][2]

Klik op **een Web Application firewall toevoegen** onder **aanbevelingen** om de pagina **een Web Application firewall toevoegen** te openen.

Als u geen bestaande Application Gateway hebt of een nieuwe wilt maken, klikt u op **Nieuw** en **maakt u een nieuwe Web Application firewall**en klikt u op **micro soft-Application Gateway**. U gaat dan door de stappen voor het maken van een toepassings gateway. Op dit moment wordt uw webtoepassing toegevoegd als een beveiligde resource. Security Center nu volgt dat deze bron wordt beveiligd door een Web Application Firewall. Dit voegt deze niet toe als lid van een back-end-groep.

Als u een bestaande toepassings gateway hebt, kunt u deze kiezen onder **bestaande oplossing gebruiken**

![Pagina om een Web Application Firewall toe te voegen][4]

Als u een webtoepassing toevoegt aan een toepassings gateway via Security Center, wordt de resource niet toegevoegd als lid van een back-end-groep. Dit moet rechtstreeks op de toepassings gateway resource worden uitgevoerd.

## <a name="add-a-resource-to-an-existing-web-application-firewall"></a>Een resource toevoegen aan een bestaande Web Application Firewall

Ga naar **alle services**  >  **beveiliging en identiteit**  >  **Security Center** en klik op **Security Center-overzicht**op **partner oplossingen**. Bestaande Security Center-compatibele toepassings gateways worden weer gegeven op de pagina **partner oplossingen** .

![partner oplossingen][7]

Klik op **App koppelen** om **koppelings toepassingen**te openen. hier ziet u de opties voor het selecteren van bestaande toepassingen. Kies de toepassingen die u wilt beveiligen en klik op **OK**. Hiermee wordt de webtoepassing niet toegevoegd aan de back-end-groep van de toepassings gateway. Hiermee stelt u de resources in als een beveiligde resource, zodat Security Center deze kunt volgen. Als u de resource als lid van een back-endadresgroep wilt toevoegen, moet u dit doen op de Application Gateway vanaf de huidige pagina. u **kunt klikken op** de toepassings gateway-resource waar u de webtoepassing kunt toevoegen aan de back-end-groep.

![toepassingen voor partner oplossingen][6]

## <a name="finalize-configuration"></a>Configuratie volt ooien

Security Center houdt toepassingen bij die zijn toegevoegd aan een toepassings gateway als een beveiligde resource.  Hiermee wordt de status van deze resource gecontroleerd en wordt gegarandeerd dat deze wordt beveiligd door een toepassings gateway. De volgende stap is het toevoegen van het privé-IP-adres, het open bare IP-adres of de NIC van uw virtuele machine aan de back-end-groep van de toepassings gateway. Tot dit is gebeurd, wordt een extra aanbeveling van het **volt ooien** van de beveiliging van de toepassing weer gegeven totdat de resource is toegevoegd.

![Pagina om een Web Application Firewall toe te voegen][5]

## <a name="security-alerts"></a>Beveiligings waarschuwingen

In Security Center navigeert **DETECTION**u naar  >  **beveiligings waarschuwingen**voor detectie.  Hier vindt u WAF-waarschuwingen voor uw toepassings gateways. Waarschuwingen worden opgesplitst op WAF regel.

![beveiligings waarschuwingen][8]

Als u een regel selecteert, wordt er een lijst met waarschuwingen voor deze specifieke WAF-regel weer geven. Elke waarschuwing bevat aanvullende details over het zoeken. De details bieden een koppeling naar de toepassings gateway.
 
![waarschuwings Details][9]

## <a name="next-steps"></a>Volgende stappen

Ga voor meer informatie over het inschakelen van Web Application Firewall op een bestaande toepassings gateway naar [een Azure-toepassing gateway maken of bijwerken met Web Application firewall](application-gateway-web-application-firewall-portal.md).

[1]: ./media/application-gateway-integration-security-center/figure1.png
[2]: ./media/application-gateway-integration-security-center/figure2.png
[3]: ./media/application-gateway-integration-security-center/figure3.png
[4]: ./media/application-gateway-integration-security-center/figure4.png
[5]: ./media/application-gateway-integration-security-center/figure5.png
[6]: ./media/application-gateway-integration-security-center/figure6.png
[7]: ./media/application-gateway-integration-security-center/figure7.png
[8]: ./media/application-gateway-integration-security-center/securitycenter.png
[9]: ./media/application-gateway-integration-security-center/figure9.png