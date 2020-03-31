---
title: 'Azure ExpressRoute private peering: IPsec-transportmodus configureren - Windows-hosts'
description: De IPsec-transportmodus inschakelen tussen Azure Windows VM's en on-premises Windows-hosts via ExpressRoute private peering met Behulp van OPO's en OU's.
services: expressroute
author: fabferri
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/17/2018
ms.author: fabferri
ms.custom: seodec18
ms.openlocfilehash: 1bc33047d31262af443cddc418853fbacd88aec1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022013"
---
# <a name="configure-ipsec-transport-mode-for-expressroute-private-peering"></a>IPsec-transportmodus configureren voor privé-peering via ExpressRoute

Met dit artikel u IPsec-tunnels maken in de transportmodus via privé-peering van ExpressRoute tussen Azure VM's met Windows en on-premises Windows-hosts. De stappen in dit artikel maken deze configuratie met groepsbeleidsobjecten. Hoewel het mogelijk is om deze configuratie te maken zonder gebruik te maken van organisatie-eenheden (OUs' s) en groepsbeleidsobjecten (GPO's), zal de combinatie van OE's en GPO's de controle over uw beveiligingsbeleid vereenvoudigen en u snel opschalen. De stappen in dit artikel gaan ervan uit dat u al een Active Directory-configuratie hebt en dat u bekend bent met het gebruik van OE's en GPO's.

## <a name="about-this-configuration"></a>Over deze configuratie

De configuratie in de volgende stappen maakt gebruik van één Extern Netwerk (Azure) met privépeering van ExpressRoute. Deze configuratie kan echter meer Azure VNets en on-premises netwerken omvatten. In dit artikel u een IPsec-versleutelingsbeleid definiëren en toepassen op een groep Azure-VM's en hosts die on-premises zijn en die deel uitmaken van dezelfde organisatie. U configureert versleuteling tussen de Azure VM's (vm1 en vm2) en de on-premises host1 alleen voor HTTP-verkeer met doelpoort 8080. Verschillende soorten IPsec-beleid kunnen worden gemaakt op basis van uw vereisten.

### <a name="working-with-ous"></a>Werken met OU's 

Het beveiligingsbeleid dat aan een organisatie-eenheid is gekoppeld, wordt via GPO naar de computers geduwd. Een paar voordelen voor het gebruik van OU's, in plaats van het toepassen van beleid op één host, zijn:

* Het koppelen van een beleid aan een organisatie-eenheid garandeert dat computers die tot dezelfde organisatie behoren hetzelfde beleid krijgen.
* Als u het beveiligingsbeleid wijzigt dat aan u is gekoppeld, worden de wijzigingen toegepast op alle hosts in de organisatie-eenheid.

### <a name="diagrams"></a>Diagrammen

In het volgende diagram ziet u de interconnectie en toegewezen IP-adresruimte. De Azure VM's en de on-premises host draaien Windows 2016. De Azure VM's en de on-premises host1 maken deel uit van hetzelfde domein. De Azure VM's en de on-premises hosts kunnen namen correct oplossen met DNS.

[![1]][1 1.]

Dit diagram toont de IPsec tunnels in transit in ExpressRoute private peering.

[![4]][4.]

### <a name="working-with-ipsec-policy"></a>Werken met IPsec-beleid

In Windows is versleuteling gekoppeld aan het IPsec-beleid. IPsec-beleid bepaalt welk IP-verkeer is beveiligd en het beveiligingsmechanisme dat wordt toegepast op de IP-pakketten.
**Ipsec-beleid** bestaat uit de volgende items: **Filterlijsten,** **Filteracties**en **beveiligingsregels**.

Bij het configureren van IPsec-beleid is het belangrijk om de volgende IPsec-beleidsterminologie te begrijpen:

* **IPsec-beleid:** Een verzameling regels. Er kan slechts één beleid actief zijn ("toegewezen") op een bepaald moment. Elk beleid kan een of meer regels hebben, die allemaal gelijktijdig actief kunnen zijn. Aan een computer kan op een bepaald moment slechts één actief IPsec-beleid worden toegewezen. Binnen het IPsec-beleid u echter meerdere acties definiëren die in verschillende situaties kunnen worden uitgevoerd. Elke set IPsec-regels is gekoppeld aan een filterlijst die van invloed is op het type netwerkverkeer waarop de regel van toepassing is.

* **Filterlijsten:** Filterlijsten zijn een bundel van een of meer filters. Eén lijst kan meerdere filters bevatten. Filter definieert of de communicatie is toegestaan, beveiligd of geblokkeerd, volgens de IP-adresbereiken, protocollen of zelfs specifieke protocolpoorten. Elk filter komt overeen met een bepaalde set voorwaarden; pakketten die bijvoorbeeld van een bepaald subnet naar een bepaalde computer op een specifieke doelpoort worden verzonden. Wanneer de netwerkomstandigheden overeenkomen met een of meer van deze filters, wordt de filterlijst geactiveerd. Elk filter wordt gedefinieerd in een specifieke filterlijst. Filters kunnen niet worden gedeeld tussen filterlijsten. Een bepaalde filterlijst kan echter worden opgenomen in verschillende IPsec-beleidsregels. 

* **Filteracties:** Een beveiligingsmethode definieert een set beveiligingsalgoritmen, protocollen en sleuteldie een computer biedt tijdens IKE-onderhandelingen. Filteracties zijn lijsten met beveiligingsmethoden, gerangschikt in volgorde van voorkeur.  Wanneer een computer onderhandelt over een IPsec-sessie, accepteert of verzendt hij voorstellen op basis van de beveiligingsinstelling die is opgeslagen in de lijst met filteracties.

* **Beveiligingsregels:** Regels bepalen hoe en wanneer een IPsec-beleid communicatie beschermt. Het maakt gebruik van **filterlijst-** en **filteracties** om een IPsec-regel te maken om de IPsec-verbinding op te bouwen. Elk beleid kan een of meer regels hebben, die allemaal gelijktijdig actief kunnen zijn. Elke regel bevat een lijst met IP-filters en een verzameling beveiligingsacties die plaatsvinden op een overeenkomst met die filterlijst:
  * IP-filteracties
  * Verificatiemethoden
  * IP-tunnelinstellingen
  * Verbindingstypen

[![5]][5.]

## <a name="before-you-begin"></a>Voordat u begint

Zorg ervoor dat u aan de volgende voorwaarden voldoet:

* U moet een werkende Active Directory-configuratie hebben die u gebruiken om groepsbeleidsinstellingen te implementeren. Zie [Groepsbeleidsobjecten](https://msdn.microsoft.com/library/windows/desktop/aa374162(v=vs.85).aspx)voor meer informatie over GPO's .

* U moet een actief ExpressRoute-circuit hebben.
  * Zie [Een ExpressRoute-circuit maken voor](expressroute-howto-circuit-arm.md)informatie over het maken van een ExpressRoute-circuit. 
  * Controleer of het circuit is ingeschakeld door uw connectiviteitsprovider. 
  * Controleer of Azure private peering is geconfigureerd voor uw circuit. Zie het [routeringsartikel configureren](expressroute-howto-routing-arm.md) voor routeringsinstructies. 
  * Controleer of u een VNet en een virtuele netwerkgateway hebt gemaakt en volledig is ingericht. Volg de instructies om [een virtuele netwerkgateway voor ExpressRoute](expressroute-howto-add-gateway-resource-manager.md)te maken. Een virtuele netwerkgateway voor ExpressRoute maakt gebruik van de GatewayType 'ExpressRoute', niet VPN.

* De virtuele netwerkgateway ExpressRoute moet zijn aangesloten op het ExpressRoute-circuit. Zie [Een VNet aansluiten op een ExpressRoute-circuit](expressroute-howto-linkvnet-arm.md)voor meer informatie.

* Controleer of de Azure Windows VM's zijn geïmplementeerd op het VNet.

* Controleer of er verbinding is tussen de on-premises hosts en de Azure VM's.

* Controleer of de Azure Windows VM's en de on-premises hosts DNS kunnen gebruiken om namen correct op te lossen.

### <a name="workflow"></a>Werkstroom

1. Maak een GPO en koppel deze aan de organisatie.Create a GPO and associate it to the OU.
2. Een **filteractie**voor IPsec definiëren .
3. Een IPsec-filterlijst **definiëren**.
4. Maak een IPsec-beleid met **beveiligingsregels**.
5. Wijs de IPsec GPO toe aan de OU.

### <a name="example-values"></a>Voorbeeldwaarden

* **Domeinnaam:** ipsectest.com

* **Ou:** IPSecOU

* **On-premises Windows-computer:** host1

* **Azure Windows VM's:** vm1, vm2

## <a name="1-create-a-gpo"></a><a name="creategpo"></a>1. Een GPO maken

1. Als u een nieuwe GPO wilt maken die is gekoppeld aan een organisatieorganisatie, opent u de module Groepsbeleidsbeheer en zoekt u de organisatie-eenheid waaraan de GPO wordt gekoppeld. In het voorbeeld wordt de organisatie-eenheid **IPSecOU genoemd.** 

   [![9]][9 9]
2. Selecteer in de module Groepsbeleidsbeheer de organisatie-eenheid en klik met de rechtermuisknop. Klik in de vervolgkeuzelijst op "**Maak een GPO in dit domein en koppel het hier...**".

   [![10]][10]
3. Noem de GPO een intuïtieve naam, zodat u deze later gemakkelijk vinden. Klik op **OK** om de GPO te maken en te koppelen.

   [![11]][11]

## <a name="2-enable-the-gpo-link"></a><a name="enablelink"></a>2. De GPO-koppeling inschakelen

Om de GPO toe te passen op de organisatie-eenheid, moet de GPO niet alleen aan de organisatie van de organisatie worden gekoppeld, maar moet de koppeling ook zijn ingeschakeld.

1. Zoek de GPO die u hebt gemaakt, klik met de rechtermuisknop en selecteer **Bewerken** in de vervolgkeuzelijst.
2. Als u de GPO wilt toepassen op de organisatie-eenheid, selecteert u **Koppeling ingeschakeld**.

   [![12]][12]

## <a name="3-define-the-ip-filter-action"></a><a name="filteraction"></a>3. De IP-filteractie definiëren

1. Klik in de vervolgkeuzelijst met de rechtermuisknop **op IP-beveiligingsbeleid in Active Directory**en klik vervolgens op **IP-filterlijsten beheren en filteracties...**.

   [![15]][15]
2. Klik op het tabblad**Filteracties beheren**op **Toevoegen**.

   [![16]][16]

3. Klik in de **wizard Actie voor IP-beveiligingfilter**op **Volgende**.

   [![17]][17]
4. Geef de filteractie een intuïtieve naam, zodat u deze later vinden. In dit voorbeeld wordt de filteractie **myEncryption**genoemd. U ook een beschrijving toevoegen. Klik vervolgens op **Volgende.**

   [![18]][18]
5. **Met Onderhandelen over beveiliging** u het gedrag definiëren als IPsec niet kan worden vastgesteld met een andere computer. Selecteer **Beveiliging onderhandelen**en klik op **Volgende**.

   [![19]][19.]
6. Selecteer op de pagina **Communiceren met computers die geen IPsec ondersteunen** de optie **Onbeveiligde communicatie niet toestaan**en klik op **Volgende**.

   [![20]][20]
7. Selecteer **op** de pagina IP Verkeer en beveiliging de optie **Aangepast**en klik vervolgens op **Instellingen...**.

   [![21]][21]
8. Selecteer op de pagina **Instellingen voor aangepaste beveiligingsmethoden** de optie **Gegevensintegriteit en -versleuteling (ESP): SHA1, 3DES**. Klik vervolgens op **OK.**

   [![22]][22]
9. Op de pagina **Filteracties beheren** u zien dat het filter **myEncryption** is toegevoegd. Klik op **Sluiten**.

   [![23]][23]

## <a name="4-define-an-ip-filter-list"></a><a name="filterlist1"></a>4. Een IP-filterlijst definiëren

Maak een filterlijst die versleuteld HTTP-verkeer opgeeft met doelpoort 8080.

1. Als u wilt weten welke typen verkeer moeten worden versleuteld, gebruikt u een **IP-filterlijst**. Klik op het tabblad **IP-filterlijsten beheren** op **Toevoegen** om een nieuwe IP-filterlijst toe te voegen.

   [![24]][24]
2. Typ in het veld **Naam:** een naam voor uw IP-filterlijst. **Azure-onpremises-HTTP8080**. Klik vervolgens op **Toevoegen**.

   [![25]][25]
3. Selecteer **Gespiegeld**op de **eigenschappenpagina IP-filterbeschrijving en gespiegeld** . De gespiegelde instelling komt overeen met pakketten die in beide richtingen gaan, wat zorgt voor tweerichtingscommunicatie. Klik vervolgens op **Volgende**.

   [![26]][26]
4. Kies op de pagina **IP-verkeersbron** in de **vervolgkeuzelijst Bron:** vervolgkeuzelijst **Een specifiek IP-adres of subnet**. 

   [![27]][27]
5. Geef het **ip-adres of subnet** van het bronadres op: van het IP-verkeer klik op **Volgende**.

   [![28]][28]
6. Geef het **adres Doel op:** IP-adres of subnet. Klik vervolgens op **Volgende.**

   [![29]][29]
7. Selecteer **TCP**op de pagina **IP-protocoltype** . Klik vervolgens op **Volgende.**

   [![30]][30]
8. Selecteer op de pagina **IP-protocolpoort** de optie **Van elke poort** en naar deze **poort:**. Typ **8080** in het tekstvak. Deze instellingen geven alleen het HTTP-verkeer op bestemmingspoort 8080 op. Klik vervolgens op **Volgende.**

   [![31]][31.]
9. Bekijk de ip-filterlijst.  De configuratie van de **IP-filterlijst azure-onpremises-HTTP8080** activeert versleuteling voor al het verkeer dat aan de volgende criteria voldoet:

   * Elk bronadres in 10.0.1.0/24 (Azure Subnet2)
   * Elk bestemmingsadres in 10.2.27.0/25 (on-premises subnet)
   * TCP-protocol
   * Bestemmingspoort 8080

   [![32]][32]

## <a name="5-edit-the-ip-filter-list"></a><a name="filterlist2"></a>5. De ip-filterlijst bewerken

Als u hetzelfde type verkeer in tegengestelde richting wilt versleutelen (van de on-premises host tot de Azure VM), hebt u een tweede IP-filter nodig. Het proces van het instellen van het nieuwe filter is hetzelfde proces dat u hebt gebruikt om het eerste IP-filter in te stellen. De enige verschillen zijn het subnet en het doelsubnet van de bron.

1. Als u een nieuw IP-filter wilt toevoegen aan de IP-filterlijst, selecteert u **Bewerken**.

   [![33]][33]
2. Klik op de pagina **IP-filterlijst** op **Toevoegen**.

   [![34]][34]
3. Maak een tweede IP-filter met de instellingen in het volgende voorbeeld:

   [![35]][35]
4. Nadat u het tweede IP-filter hebt gemaakt, ziet de IP-filterlijst er als volgt uit:

   [![36]][36]

Als versleuteling vereist is tussen een on-premises locatie en een Azure-subnet om een toepassing te beschermen, u in plaats van de bestaande IP-filterlijst wijzigen, in plaats daarvan een nieuwe IP-filterlijst toevoegen. Het koppelen van 2 IP-filterlijsten aan hetzelfde IP-beleid biedt een betere flexibiliteit omdat een specifieke IP-filterlijst op elk gewenst moment kan worden gewijzigd of verwijderd zonder dat dit gevolgen heeft voor de andere IP-filterlijsten.

## <a name="6-create-an-ipsec-security-policy"></a><a name="ipsecpolicy"></a>6. Een IPsec-beveiligingsbeleid maken 

Maak een IPsec-beleid met beveiligingsregels.

1. Selecteer het **IP-beveiligingsbeleid in de Active-map** dat is gekoppeld aan de organisatie-eenheid. Klik met de rechtermuisknop en selecteer **IP-beveiligingsbeleid maken**.

   [![37]][37]
2. Noem het beveiligingsbeleid. Bijvoorbeeld **beleids-azure-onpremises**. Klik vervolgens op **Volgende.**

   [![38]][38]
3. Klik **op Volgende** zonder het selectievakje in te schakelen.

   [![39]][39]
4. Controleer of het selectievakje **Eigenschappen bewerken** is ingeschakeld en klik op **Voltooien**.

   [![40]][40]

## <a name="7-edit-the-ipsec-security-policy"></a><a name="editipsec"></a>7. Het iPsec-beveiligingsbeleid bewerken

Voeg aan het IP-filterbeleid de **IP-filterlijst** en **filteractie** toe die u eerder hebt geconfigureerd.

1. Klik op het tabblad **Eigenschappenregels van** HTTP-beleid op **Toevoegen**.

   [![41]][41]
2. Klik op de pagina Welkom op **Volgende**.

   [![42]][42]
3. Een regel biedt de optie om de IPsec-modus te definiëren: tunnelmodus of transportmodus.

   * In de tunnelmodus wordt het oorspronkelijke pakket ingekapseld door een set IP-headers. De tunnelmodus beschermt de interne routeringsgegevens door de IP-header van het oorspronkelijke pakket te versleutelen. Tunnel modus wordt op grote schaal geïmplementeerd tussen gateways in site-to-site VPN-scenario's. Tunnel modus wordt in de meeste gevallen gebruikt voor end-to-end encryptie tussen hosts.

   * Transportmodus versleutelt alleen de payload en ESP trailer; de IP-header van het oorspronkelijke pakket is niet versleuteld. In de transportmodus blijven de IP-bron en IP-bestemming van de pakketten ongewijzigd.

   Selecteer **Deze regel geeft geen tunnel op**en klik op **Volgende**.

   [![43]][43]
4. **Netwerktype** definieert welke netwerkverbinding in verband staat met het beveiligingsbeleid. Selecteer **Alle netwerkverbindingen**en klik op **Volgende**.

   [![44]][44]
5. Selecteer de IP-filterlijst die u eerder hebt gemaakt, **azure-onpremises-HTTP8080**en klik op **Volgende**.

   [![45]][45]
6. Selecteer de bestaande **myEncryption** voor filteractie die u eerder hebt gemaakt.

   [![46]][46]
7. Windows ondersteunt vier verschillende soorten verificaties: Kerberos, certificaten, NTLMv2 en vooraf gedeelde sleutel. Omdat we werken met domeingekoppelde hosts, selecteert u **Active Directory standaard (Kerberos V5-protocol)** en klikt u op **Volgende**.

   [![47]][47]
8. Met het nieuwe beleid wordt de beveiligingsregel gemaakt: **azure-onpremises-HTTP8080**. Klik op **OK**.

   [![48]][48]

Het IPsec-beleid vereist dat alle HTTP-verbindingen op de doelpoort 8080 de IPsec-transportmodus gebruiken. Omdat HTTP een duidelijk tekstprotocol is, zorgt het ingeschakeld hebben van het beveiligingsbeleid ervoor dat gegevens worden versleuteld wanneer ze worden overgedragen via de privépeering van ExpressRoute. IP-beveiligingsbeleid voor Active Directory is complexer te configureren dan Windows Firewall met geavanceerde beveiliging, maar het maakt het wel mogelijk om de IPsec-verbinding aan te passen.

## <a name="8-assign-the-ipsec-gpo-to-the-ou"></a><a name="assigngpo"></a>8. De IPsec-gpo toewijzen aan de

1. Bekijk het beleid. Het beveiligingsgroepsbeleid is gedefinieerd, maar nog niet toegewezen.

   [![49]][49]
2. Als u het beveiligingsgroepsbeleid wilt toewijzen aan de OU **IPSecOU,** klikt u met de rechtermuisknop op het beveiligingsbeleid en kiest u **Toewijzen**.
   Elke computer tht behoort tot de OU zal de beveiligingsgroep beleid toegewezen.

   [![50]][50]

## <a name="check-traffic-encryption"></a><a name="checktraffic"></a>Verkeersversleuteling controleren

Als u de versleuteling wilt controleren die GPO op de organisatieorganisatie is toegepast, installeert u IIS op alle Azure VM's en in de host1. Elke IIS is aangepast om te reageren op HTTP-verzoeken op poort 8080.
Als u versleuteling wilt verifiëren, u een netwerksniffer (zoals Wireshark) installeren in alle computers in de organisatie.
Een powershell-script werkt als een HTTP-client om HTTP-aanvragen te genereren op poort 8080:

```powershell
$url = "http://10.0.1.20:8080"
while ($true) {
try {
[net.httpWebRequest]
$req = [net.webRequest]::create($url)
$req.method = "GET"
$req.ContentType = "application/x-www-form-urlencoded"
$req.TimeOut = 60000

$start = get-date
[net.httpWebResponse] $res = $req.getResponse()
$timetaken = ((get-date) - $start).TotalMilliseconds

Write-Output $res.Content
Write-Output ("{0} {1} {2}" -f (get-date), $res.StatusCode.value__, $timetaken)
$req = $null
$res.Close()
$res = $null
} catch [Exception] {
Write-Output ("{0} {1}" -f (get-date), $_.ToString())
}
$req = $null

# uncomment the line below and change the wait time to add a pause between requests
#Start-Sleep -Seconds 1
}

```
De volgende netwerkopname toont de resultaten voor on-premises host1 met displayfilter ESP die alleen overeenkomen met het versleutelde verkeer:

[![51]][51]

Als u de powershell-script-on-premisies (HTTP-client) uitvoert, wordt in de Azure VM een vergelijkbaar spoor weergegeven.

## <a name="next-steps"></a>Volgende stappen

Zie de Veelgestelde vragen over [ExpressRoute voor](expressroute-faqs.md)meer informatie over ExpressRoute.

<!--Image References-->

[1]: ./media/expressroute-howto-ipsec-transport-private-windows/network-diagram.png "netwerkdiagram IPsec-transportmodus via ExpressRoute"
[4]: ./media/expressroute-howto-ipsec-transport-private-windows/ipsec-interesting-traffic.png "IPsec interessant verkeer"
[5]: ./media/expressroute-howto-ipsec-transport-private-windows/windows-ipsec.png "Windows IPsec-beleid"
[9]: ./media/expressroute-howto-ipsec-transport-private-windows/ou.png "Organisatie-eenheid in het groepsbeleid"
[10]: ./media/expressroute-howto-ipsec-transport-private-windows/create-gpo-ou.png "maak een GPO die is gekoppeld aan de"
[11]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-name.png "een naam toewijzen aan de GPO die is gekoppeld aan de"
[12]: ./media/expressroute-howto-ipsec-transport-private-windows/edit-gpo.png "bewerken van de GPO"
[15]: ./media/expressroute-howto-ipsec-transport-private-windows/manage-ip-filter-list-filter-actions.png "IP-filterlijsten en filteracties beheren"
[16]: ./media/expressroute-howto-ipsec-transport-private-windows/add-filter-action.png "Filteractie toevoegen"
[17]: ./media/expressroute-howto-ipsec-transport-private-windows/action-wizard.png "17 Actiewizard"
[18]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action-name.png "Naam filteractie"
[19]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action.png "Filteractie"
[20]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action-no-ipsec.png "geef op dat het gedrag een onveilige verbinding is"
[21]: ./media/expressroute-howto-ipsec-transport-private-windows/security-method.png "beveiligingsmechanisme"
[22]: ./media/expressroute-howto-ipsec-transport-private-windows/custom-security-method.png "Aangepaste beveiligingsmethode"
[Actielijst 23]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-actions-list.png "filters"
[24]: ./media/expressroute-howto-ipsec-transport-private-windows/add-new-ip-filter.png "Een nieuwe IP-filterlijst toevoegen"
[25]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-http-traffic.png "HTTP-verkeer toevoegen aan het IP-filter"
[26]: ./media/expressroute-howto-ipsec-transport-private-windows/match-both-direction.png "matchpakket in beide richtingen"
[27]: ./media/expressroute-howto-ipsec-transport-private-windows/source-address.png "selectie van het subnet Bron"
[28]: ./media/expressroute-howto-ipsec-transport-private-windows/source-network.png "Bronnetwerk"
[29]: ./media/expressroute-howto-ipsec-transport-private-windows/destination-network.png "Bestemmingsnetwerk"
[30]: ./media/expressroute-howto-ipsec-transport-private-windows/protocol.png "Protocol"
[31]: ./media/expressroute-howto-ipsec-transport-private-windows/source-port-and-destination-port.png "bronpoort en bestemmingspoort"
[32]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-list.png "filterlijst"
[33]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-for-http.png "IP-filterlijst met HTTP-verkeer"
[34]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-add-second-entry.png "Een tweede IP-filter toevoegen"
[35]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-second-entry.png "IP-filter lijst-seconde item"
[36]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-list-2entries.png "IP-filter lijst-seconde item"
[37]: ./media/expressroute-howto-ipsec-transport-private-windows/create-ip-security-policy.png "het IP-beveiligingsbeleid maken"
[38]: ./media/expressroute-howto-ipsec-transport-private-windows/ipsec-policy-name.png "naam van het IPsec-beleid"
[Wizard 39]: ./media/expressroute-howto-ipsec-transport-private-windows/security-policy-wizard.png "IPsec-beleid"
[40]: ./media/expressroute-howto-ipsec-transport-private-windows/edit-security-policy.png "bewerken van het IPsec-beleid"
[41]: ./media/expressroute-howto-ipsec-transport-private-windows/add-new-rule.png "nieuwe beveiligingsregel toevoegen aan het IPsec-beleid"
[42]: ./media/expressroute-howto-ipsec-transport-private-windows/create-security-rule.png "een nieuwe beveiligingsregel maken"
[43]: ./media/expressroute-howto-ipsec-transport-private-windows/transport-mode.png "Transportmodus"
[44]: ./media/expressroute-howto-ipsec-transport-private-windows/network-type.png "Netwerktype"
[45]: ./media/expressroute-howto-ipsec-transport-private-windows/selection-filter-list.png "selectie van bestaande IP-filterlijst"
[46]: ./media/expressroute-howto-ipsec-transport-private-windows/selection-filter-action.png "selectie van bestaande filteractie"
[47]: ./media/expressroute-howto-ipsec-transport-private-windows/authentication-method.png "selectie van de verificatiemethode"
[48]: ./media/expressroute-howto-ipsec-transport-private-windows/security-policy-completed.png "einde van het proces van de oprichting van het veiligheidsbeleid"
[49]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-not-assigned.png "IPsec-beleid gekoppeld aan het GPO, maar niet toegewezen"
[50]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-assigned.png "IPsec-beleid toegewezen aan het GPO"
[51]: ./media/expressroute-howto-ipsec-transport-private-windows/encrypted-traffic.png "Vastleggen van IPsec versleuteld verkeer"
