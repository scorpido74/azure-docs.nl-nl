---
title: WAF-beleid (Web Application firewall) maken voor Application Gateway
description: Meer informatie over het maken van een firewall beleid voor webtoepassingen voor Application Gateway.
services: web-application-firewall
ms.topic: conceptual
author: vhorne
ms.service: web-application-firewall
ms.date: 10/26/2019
ms.author: victorh
ms.openlocfilehash: 31a5ad92942b40e42b66e404df2d09cd8158f7a2
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73606490"
---
# <a name="create-web-application-firewall-policies-for-application-gateway"></a>Een firewall beleid voor webtoepassingen voor Application Gateway maken

Als u een WAF-beleid aan listeners koppelt, kunnen meerdere sites achter één WAF worden beveiligd door verschillende beleids regels. Als er bijvoorbeeld vijf sites achter uw WAF zijn, kunt u vijf afzonderlijke WAF-beleids regels (één voor elke listener) hebben om de uitsluitingen, aangepaste regels en beheerde Rules voor één site aan te passen zonder dat dit van invloed is op de andere. Als u een beleid wilt Toep assen op alle sites, kunt u het beleid alleen koppelen aan de Application Gateway, in plaats van de afzonderlijke listeners, om het globaal van toepassing te laten zijn. Beleids regels kunnen ook worden toegepast op een op een pad gebaseerde routerings regel. 

   > [!NOTE]
   > WAF-beleid per site is beschikbaar in Zuid-Centraal VS en Europa-noord. Als u toegang wilt krijgen tot de portals, gebruikt u [deze koppeling](https://aka.ms/AppgwwafWithAllFeatureFlags) totdat deze Live voor iedereen actief is.  

U kunt zoveel beleids regels maken als u wilt. Als u een beleid hebt gemaakt, moet dit zijn gekoppeld aan een Application Gateway om van kracht te worden, maar dit kan worden gekoppeld aan een combi natie van toepassings gateways en listeners. 

Als op uw Application Gateway een beleid is toegepast en u vervolgens een ander beleid toepast op een listener op die Application Gateway, wordt het beleid van de listener van kracht, maar alleen voor de listener (s) waaraan ze zijn toegewezen. Het Application Gateway beleid is nog steeds van toepassing op alle andere listeners waaraan geen specifiek beleid is toegewezen. 

   > [!NOTE]
   > WAF-beleid per site en per URI zijn beschikbaar als open bare preview. Dit betekent dat deze functie is onderhevig aan de aanvullende gebruiks voorwaarden van micro soft. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Alle nieuwe WAF-instellingen van de firewall voor webtoepassingen (aangepaste regels, beheerde rulset-configuraties, uitsluitingen, enzovoort) Live binnen een WAF-beleid. Als u een bestaande WAF hebt, zijn deze instellingen mogelijk nog aanwezig in de WAF-configuratie. Zie [uw WAF config migreren naar een WAF-beleid](#migrate) verderop in dit artikel voor meer informatie over het overstappen op het nieuwe WAF-beleid. 

## <a name="create-a-policy"></a>Beleid maken

Maak eerst een Basic-WAF-beleid met een beheerde standaard regelset (DRS) met behulp van de Azure Portal.

1. Selecteer in de linkerbovenhoek van de Portal de optie **een resource maken**. Zoek naar **WAF**, selecteer **Web Application firewall**en selecteer vervolgens **maken**.
2. Op de pagina **een WAF-beleid maken** , tabblad **basis** gegevens, voer de volgende informatie in of Selecteer deze, accepteer de standaard waarden voor de overige instellingen en selecteer vervolgens **controleren + maken**:

   |Instelling  |Waarde  |
   |---------|---------|
   |Beleid voor     |Regionale WAF (Application Gateway)|
   |Abonnement     |De naam van uw abonnement selecteren|
   |Resourcegroep     |Uw resourcegroep selecteren|
   |Beleids naam     |Typ een unieke naam voor uw WAF-beleid.|
3. Voer een van de volgende instellingen in op het tabblad **koppeling** en selecteer vervolgens **toevoegen**:

   |Instelling  |Waarde  |
   |---------|---------|
   |Application Gateway koppelen     |Selecteer de naam van uw Application Gateway profiel.|
   |Listeners koppelen     |Selecteer de naam van uw Application Gateway-listener en selecteer vervolgens **toevoegen**.|

   > [!NOTE]
   > Als u een beleid toewijst aan uw Application Gateway (of listener) waaraan al een beleid is toegewezen, wordt het oorspronkelijke beleid overschreven en vervangen door het nieuwe beleid.
4. Selecteer **controleren + maken**en selecteer vervolgens **maken**.

   ![Basis beginselen van WAF-beleid](../media/create-waf-policy-ag/waf-policy-basics.png)

## <a name="configure-waf-rules-optional"></a>WAF-regels configureren (optioneel)

Wanneer u een WAF-beleid maakt, is dit standaard in de *detectie* modus. In de detectie modus worden door WAF geen aanvragen geblokkeerd. In plaats daarvan worden de overeenkomende WAF-regels vastgelegd in de WAF-Logboeken. Als u WAF in actie wilt zien, kunt u de modus instellingen wijzigen in *preventie*. In de preventie modus worden overeenkomende regels die zijn gedefinieerd in de CRS-regelset die u hebt geselecteerd, geblokkeerd en/of geregistreerd in de WAF-Logboeken.

## <a name="managed-rules"></a>Beheerde regels

Door Azure beheerde OWASP-regels zijn standaard ingeschakeld. Als u een afzonderlijke regel binnen een regel groep wilt uitschakelen, vouwt u de regels binnen die regel groep uit, selecteert u het selectie vakje vóór het regel nummer en selecteert u **uitschakelen** op het bovenstaande tabblad.

![Beheerde regels](../media/create-waf-policy-ag/managed-rules.png)

## <a name="custom-rules"></a>Aangepaste regels

Als u een aangepaste regel wilt maken, selecteert u **aangepaste regel toevoegen** onder het tabblad **aangepaste regels** . Hiermee opent u de pagina aangepaste regel configuratie. De volgende scherm afbeelding toont een voor beeld van een aangepaste regel die is geconfigureerd voor het blok keren van een aanvraag als de query reeks de tekst *blockme*bevat.

![Aangepaste regel bewerken](../media/create-waf-policy-ag/edit-custom-rule.png)

## <a name="migrate"></a>Uw WAF-configuratie migreren naar een WAF-beleid

Als u een bestaande WAF hebt, hebt u mogelijk enkele wijzigingen in de portal gezien. Eerst moet u bepalen welk soort beleid u hebt ingeschakeld op uw WAF. Er zijn drie mogelijke statussen:

- Geen WAF-beleid
- Beleid voor aangepaste regels alleen
- WAF-beleid

U kunt zien in welke staat uw WAF zich bevindt door deze te bekijken in de portal. Als de WAF-instellingen zichtbaar zijn en vanuit de Application Gateway weer gave kunnen worden gewijzigd, is uw WAF de status 1.

![WAF-configuratie](../media/create-waf-policy-ag/waf-configure.png)

Als u **Web Application firewall** selecteert en er een bijbehorend beleid wordt weer gegeven, is de WAF status 2 of State 3. Als er is genavigeerd naar het beleid, worden **alleen** aangepaste regels en gekoppelde toepassings gateways weer gegeven, dan is het beleid voor aangepaste regels alleen van toepassing.

![Aangepaste WAF-regels](../media/create-waf-policy-ag/waf-custom-rules.png)

Als ook beleids instellingen en beheerde regels worden weer gegeven, is het een volledig firewall beleid voor webtoepassingen. 

![WAF-beleids instellingen](../media/create-waf-policy-ag/waf-policy-settings.png)

## <a name="migrate-to-waf-policy"></a>Migreren naar WAF-beleid

Als u een aangepaste regel alleen WAF-beleid hebt, kunt u het beste overschakelen naar het nieuwe WAF-beleid. Het firewall beleid biedt ondersteuning voor WAF-beleids instellingen, beheerde rules, uitsluitingen en uitgeschakelde regel groepen. In wezen worden alle WAF-configuraties die eerder zijn uitgevoerd in de Application Gateway nu uitgevoerd via het WAF-beleid. 

Wijzigingen in de aangepaste regel worden alleen WAF-beleid uitgeschakeld. Voor het bewerken van WAF-instellingen, zoals het uitschakelen van regels, het toevoegen van uitsluitingen, enzovoort, moet u migreren naar een nieuwe firewall-beleids bron op het hoogste niveau.

Als u dit wilt doen, maakt u een *firewall beleid voor webtoepassingen* en koppelt u dit aan uw Application Gateway (en) en luister (en). Dit nieuwe beleid **moet** exact hetzelfde zijn als de huidige WAF-configuratie, wat betekent dat elke aangepaste regel, uitsluiting, uitgeschakelde regel, enzovoort moet worden gekopieerd naar het nieuwe beleid dat u maakt. Zodra er een beleid is gekoppeld aan uw Application Gateway, kunt u door gaan met het wijzigen van uw WAF-regels en-instellingen. 

U kunt dit ook doen met Azure PowerShell. Zie [een WAF-beleid koppelen aan een bestaande Application Gateway](associate-waf-policy-existing-gateway.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de [regel groepen en regels voor het CRS-programma van Web Application firewall](application-gateway-crs-rulegroups-rules.md).
