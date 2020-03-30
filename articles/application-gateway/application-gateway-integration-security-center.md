---
title: Application Gateway-integratie met Azure Security Center | Microsoft Documenten
description: Op deze pagina vindt u informatie over de integratie van Application Gateway in Azure Security Center.
services: application-gateway
author: vhorne
ms.author: victorh
ms.assetid: e5ea5cf9-3b41-4b85-a12c-e758bff7f3ec
ms.service: application-gateway
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 06/07/2017
ms.openlocfilehash: f5ecd2334ca80f5561c0611239b5bb00d222112a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277195"
---
# <a name="overview-of-integration-between-application-gateway-and-azure-security-center"></a>Overzicht van integratie tussen Application Gateway en Azure Security Center

Ontdek hoe Application Gateway en Security Center uw bronnen van webtoepassingen helpen beschermen. Application gateway web application firewall (WAF) integreert met [Security Center](../security-center/security-center-intro.md) om een naadloze weergave te bieden om bedreigingen voor onbeveiligde webapplicaties in uw omgeving te voorkomen, detecteren en erop te reageren.

## <a name="overview"></a>Overzicht

Application Gateway WAF is een aanbeveling in Security Center voor het beschermen van webapplicaties tegen exploits en kwetsbaarheden. Web-enabled resources die niet worden beschermd door WAF worden weergegeven in het beveiligingscentrum als aanbevelingen met hoge ernst. Aanbevelingen voor firewalls voor webtoepassingen worden weergegeven op de pagina **Overzicht** onder **Toepassingen**.

![integratie met security center][1]

Als u op aanbevelingen met betrekking tot de firewall van de webtoepassing klikt, wordt een nieuwe pagina geopend met de details van de aanbeveling.

## <a name="add-a-web-application-firewall-to-an-existing-resource"></a>Een firewall voor webtoepassingen toevoegen aan een bestaande bron

Navigeer naar **Beveiliging van alle services** > + het**identiteitsbeveiligingscentrum** **Security + Identity** > en in **het beveiligingscentrum - Overzicht**, klik op **Toepassingen**. In **Beveiligingscentrum - Toepassingen**bevat de tabel een lijst met toepassingen die Security Center in uw abonnement heeft gedetecteerd.

![webtoepassingen][3]

Door op een webtoepassing met een kritiek probleem te klikken, krijgt u de pagina **Beveiligingsstatus toepassing.** In de afbeelding hieronder wordt de webtoepassing die niet wordt beschermd door een firewall voor webtoepassingen. 

![webbronnen niet beveiligd][2]

Klik **op Een firewall voor webtoepassingen toevoegen** onder **Aanbevelingen** om de pagina **Firewall voor webtoepassingen toevoegen** te openen.

Als u geen bestaande toepassingsgateway hebt of een nieuwe wilt maken, klikt u op **Nieuw maken** en klikt u op Firewall **voor webtoepassingen maken**en klikt u op Microsoft - Application **Gateway**. Dit neemt u mee door de stappen om een toepassingsgateway te maken. Op dit moment wordt uw webtoepassing toegevoegd als een beveiligde bron, Security Center houdt nu bij dat deze bron wordt beschermd door een firewall voor webtoepassingen. Dit voegt het niet toe als een backend pool lid.

Als u een bestaande toepassingsgateway hebt, u deze kiezen onder **Bestaande oplossing gebruiken**

![Pagina om een firewall voor webtoepassingen toe te voegen][4]

Als u een webtoepassing toevoegt aan een toepassingsgateway via Security Center, wordt de bron niet toegevoegd als backendpoollid. Dit moet rechtstreeks worden gedaan op de gatewaybron van de toepassing.

## <a name="add-a-resource-to-an-existing-web-application-firewall"></a>Een bron toevoegen aan een bestaande firewall voor webtoepassingen

Navigeer naar **Alle services** > **Security + Identity** > **Security Center** en op Security Center - **Overzicht**, klik op **Partneroplossingen**. Bestaande beveiligingscentrumbewuste toepassingsgateways worden weergegeven op de pagina **Partneroplossingen.**

![partneroplossingen][7]

Klik **op Koppeling-app** om **Koppelingstoepassingen**te openen, hier krijgt u de opties om bestaande toepassingen te selecteren. Kies de toepassingen die u wilt beveiligen en klik op **OK**. Dit voegt de webtoepassing niet toe aan de backendpool van de toepassingsgateway. Hiermee worden de resources ingesteld als een beveiligde bron, zodat Security Center deze kan volgen. Als u de bron wilt toevoegen als backendpoollid, moet dit worden gedaan op de toepassingsgateway, vanaf de huidige pagina u op **Oplossingsconsole** klikken om naar de bron van de toepassingsgateway te worden geleid waar u de webtoepassing toevoegen aan de backendgroep.

![partneroplossingen toepassingen][6]

## <a name="finalize-configuration"></a>Configuratie afronden

Security Center traceert toepassingen die zijn toegevoegd aan een toepassingsgateway als een beveiligde bron.  Het controleert de status van deze bron en zorgt ervoor dat deze wordt beschermd door een toepassingsgateway. De volgende stap is het toevoegen van de private IP, openbare IP of NIC van uw virtuele machine aan de backend pool van de toepassingsgateway. Totdat dit is gedaan, wordt een aanvullende aanbeveling **voor toepassingsbeveiliging afronden** weergegeven totdat de bron is toegevoegd.

![Pagina om een firewall voor webtoepassingen toe te voegen][5]

## <a name="security-alerts"></a>Beveiligingswaarschuwingen

Navigeer binnen beveiligingscentrum naar > **detectiebeveiligingswaarschuwingen.** **DETECTION**  Hier vindt u WAF-waarschuwingen voor uw toepassingsgateways. Waarschuwingen worden opgesplitst op WAF-regel.

![beveiligingswaarschuwingen][8]

Als u een regel selecteert, wordt een lijst met waarschuwingen voor die specifieke WAF-regel weergegeven. Elke waarschuwing toont aanvullende details over de bevinding. De details bieden een link naar de toepassingsgateway.
 
![waarschuwingsgegevens][9]

## <a name="next-steps"></a>Volgende stappen

Ga voor meer informatie over het inschakelen van firewall van webtoepassingen op een bestaande toepassingsgateway naar [Een Azure Application Gateway maken of bijwerken met firewall voor webtoepassingen.](application-gateway-web-application-firewall-portal.md)

[1]: ./media/application-gateway-integration-security-center/figure1.png
[2]: ./media/application-gateway-integration-security-center/figure2.png
[3]: ./media/application-gateway-integration-security-center/figure3.png
[4]: ./media/application-gateway-integration-security-center/figure4.png
[5]: ./media/application-gateway-integration-security-center/figure5.png
[6]: ./media/application-gateway-integration-security-center/figure6.png
[7]: ./media/application-gateway-integration-security-center/figure7.png
[8]: ./media/application-gateway-integration-security-center/securitycenter.png
[9]: ./media/application-gateway-integration-security-center/figure9.png