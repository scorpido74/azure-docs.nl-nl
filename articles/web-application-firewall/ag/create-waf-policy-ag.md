---
title: WAF-beleid (Web Application Firewall) maken voor toepassingsgateway
description: Meer informatie over het maken van webtoepassingsfirewallbeleid voor application gateway.
services: web-application-firewall
ms.topic: conceptual
author: vhorne
ms.service: web-application-firewall
ms.date: 02/08/2020
ms.author: victorh
ms.openlocfilehash: e3738da806ff36cdb7e8d561b88a457a5264eb76
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886922"
---
# <a name="create-web-application-firewall-policies-for-application-gateway"></a>WebapplicationFirewallbeleid maken voor toepassingsgateway

Door een WAF-beleid te koppelen aan luisteraars kunnen meerdere sites achter één WAF worden beschermd door verschillende beleidsregels. Als er bijvoorbeeld vijf sites achter uw WAF staan, u vijf afzonderlijke WAF-beleidsregels (één voor elke listener) hebben om de uitsluitingen, aangepaste regels en beheerde regelsets voor één site aan te passen zonder de andere vier te beïnvloeden. Als u wilt dat één beleid van toepassing is op alle sites, u het beleid gewoon koppelen aan de Application Gateway, in plaats van de individuele listeners, om het wereldwijd te laten toepassen. Beleidsregels kunnen ook worden toegepast op een routeringsregel op basis van paden. 

U zoveel beleid maken als u wilt. Zodra u een beleid hebt gemaakt, moet het worden gekoppeld aan een toepassingsgateway om in werking te treden, maar het kan worden gekoppeld aan elke combinatie van toepassingsgateways en listeners. 

Als uw application gateway een beleid heeft toegepast en u vervolgens een ander beleid toepast op een listener op die Application Gateway, wordt het beleid van de listener van kracht, maar alleen voor de listener(s) waaraan deze is toegewezen. Het toepassingsgatewaybeleid is nog steeds van toepassing op alle andere luisteraars die geen specifiek beleid aan hen hebben toegewezen. 

   > [!NOTE]
   > Per site en per URI WAF-beleid staan in Public Preview. Dat betekent dat deze functie is onderworpen aan microsoft's aanvullende gebruiksvoorwaarden. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.
   > [!NOTE]
   > Zodra een firewallbeleid is gekoppeld aan een WAF, moet er altijd een beleid zijn gekoppeld aan die WAF. U dat beleid overschrijven, maar het volledig loskoppelen van een beleid van de WAF wordt niet ondersteund. 

Alle nieuwe WAF-instellingen van Web Application Firewall (aangepaste regels, beheerde rulset-configuraties, uitsluitingen, enz.) leven binnen een WAF-beleid. Als u een bestaande WAF hebt, kunnen deze instellingen nog steeds bestaan in uw WAF-config. Zie Uw WAF Config later in dit artikel [migreren naar een WAF-beleid voor](#migrate) stappen over hoe u naar het nieuwe WAF-beleid gaan. 

## <a name="create-a-policy"></a>Beleid maken

Maak eerst een basisWAF-beleid met een beheerde Standaardregelset (DRS) met behulp van de Azure-portal.

1. Selecteer linksboven in de portal de optie **Een resource maken**. Zoek naar **WAF,** selecteer **Web Application Firewall**en selecteer Vervolgens **Maken**.
2. Ga **op De beleidspagina van De WAF een waf-beleidspagina** maken, **de** volgende gegevens invoeren of selecteren, accepteer de standaardinstellingen voor de overige instellingen en selecteer Vervolgens Controleren **+ maken:**

   |Instelling  |Waarde  |
   |---------|---------|
   |Beleid voor     |Regionale WAF (Application Gateway)|
   |Abonnement     |Uw abonnementsnaam selecteren|
   |Resourcegroep     |Uw resourcegroep selecteren|
   |Beleidsnaam     |Typ een unieke naam voor uw WAF-beleid.|
3. Voer op het tabblad **Koppeling** een van de volgende instellingen in en selecteer **Toevoegen:**

   |Instelling  |Waarde  |
   |---------|---------|
   |Toepassingsgateway koppelen     |Selecteer de profielnaam van de Application Gateway.|
   |Luisteraars koppelen     |Selecteer de naam van de toepassingsgatewaylistener en selecteer **Toevoegen**.|

   > [!NOTE]
   > Als u een beleid toewijst aan uw toepassingsgateway (of listener) dat al een beleid heeft, wordt het oorspronkelijke beleid overschreven en vervangen door het nieuwe beleid.
4. Selecteer **Controleren + maken**en selecteer Vervolgens **Maken**.

   ![Basisprincipes van waf-beleid](../media/create-waf-policy-ag/waf-policy-basics.png)

## <a name="configure-waf-rules-optional"></a>WAF-regels configureren (optioneel)

Wanneer u een WAF-beleid maakt, bevindt dit zich standaard in de *detectiemodus.* In de detectiemodus blokkeert WAF geen aanvragen. In plaats daarvan worden de overeenkomende WAF-regels vastgelegd in de WAF-logboeken. Als u WAF in actie wilt zien, u de modusinstellingen wijzigen in *Preventie.* In de preventiemodus worden overeenkomende regels die zijn gedefinieerd in de door u geselecteerde CRS-regelset geblokkeerd en/of aangemeld in de WAF-logboeken.

## <a name="managed-rules"></a>Beheerde regels

Door Azure beheerde OWASP-regels zijn standaard ingeschakeld. Als u een afzonderlijke regel binnen een regelgroep wilt uitschakelen, vouwt u de regels binnen die regelgroep uit, schakelt u het selectievakje voor het regelnummer in en selecteert **u Uitschakelen** op het bovenstaande tabblad.

[![Beheerde](../media/create-waf-policy-ag/managed-rules.png) regels](../media/create-waf-policy-ag/managed-rules-lrg.png#lightbox)

## <a name="custom-rules"></a>Aangepaste regels

Als u een aangepaste regel wilt maken, selecteert u **Aangepaste regel toevoegen** onder het tabblad Aangepaste **regels.** Hiermee wordt de aangepaste pagina voor de configuratie van regels geopend. In de volgende schermafbeelding wordt een aangepaste regel weergegeven die is geconfigureerd om een aanvraag te blokkeren als de querytekenreeks het *tekstblok bevat.*

[![Aangepaste regel](../media/create-waf-policy-ag/edit-custom-rule.png) bewerken](../media/create-waf-policy-ag/edit-custom-rule-lrg.png#lightbox)

## <a name="migrate-your-waf-config-to-a-waf-policy"></a><a name="migrate"></a>Migreer uw WAF Config naar een WAF-beleid

Als u een bestaande WAF hebt, hebt u mogelijk enkele wijzigingen in de portal opgemerkt. Eerst moet u bepalen wat voor soort beleid u hebt ingeschakeld op uw WAF. Er zijn drie potentiële staten:

- Geen WAF-beleid
- Alleen beleid voor aangepaste regels
- WAF-beleid

U zien in welke staat uw WAF zich bevindt door ernaar te kijken in het portaal. Als de WAF-instellingen zichtbaar zijn en kunnen worden gewijzigd vanuit de weergave Application Gateway, bevindt uw WAF zich in staat 1.

[![WAF-configuratie](../media/create-waf-policy-ag/waf-configure.png)](../media/create-waf-policy-ag/waf-configure-lrg.png#lightbox)

Als u **WebApplication Firewall** selecteert en u een bijbehorend beleid toont, bevindt de WAF zich in staat 2 of staat 3. Nadat u naar het beleid hebt genavigeerd, als er **alleen** aangepaste regels en bijbehorende toepassingsgateways worden weergegeven, is dit een alleen aangepast beleid voor regels.

![Aangepaste WAF-regels](../media/create-waf-policy-ag/waf-custom-rules.png)

Als er ook beleidsinstellingen en beheerregels worden weergegeven, is het een volledig firewallbeleid voor webtoepassingen. 

![WAF-beleidsinstellingen](../media/create-waf-policy-ag/waf-policy-settings.png)

## <a name="migrate-to-waf-policy"></a>Migreren naar WAF-beleid

Als u een WAF-beleid voor aangepaste regels hebt, u overstappen naar het nieuwe WAF-beleid. In de toekomst ondersteunt het firewallbeleid WAF-beleidsinstellingen, beheerde regelsets, uitsluitingen en uitgeschakelde regelgroepen. In wezen worden alle WAF-configuraties die eerder binnen de Application Gateway zijn uitgevoerd, nu uitgevoerd via het WAF-beleid. 

Bewerkingen van de aangepaste regel alleen WAF-beleid zijn uitgeschakeld. Als u WAF-instellingen wilt bewerken, zoals het uitschakelen van regels, het toevoegen van uitsluitingen, enz.

Maak hiervoor een *firewallbeleid voor webtoepassingen* en koppelt dit aan uw toepassingsgateway(s) en luisteraar(s) naar keuze. Dit nieuwe beleid **moet** precies hetzelfde zijn als de huidige WAF-config, wat betekent dat elke aangepaste regel, uitsluiting, uitgeschakelde regel, enz. Zodra u een beleid hebt gekoppeld aan uw toepassingsgateway, u uw WAF-regels en -instellingen blijven wijzigen. U dit ook doen met Azure PowerShell. Zie [Een WAF-beleid koppelen aan een bestaande Application Gateway](associate-waf-policy-existing-gateway.md)voor meer informatie.

Optioneel u een migratiescript gebruiken om te migreren naar een WAF-beleid. Zie [Firewall-beleid voor webtoepassingen migreren met Azure PowerShell](migrate-policy.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [CRS-regelgroepen en -regels](application-gateway-crs-rulegroups-rules.md)voor webtoepassingen firewall.
