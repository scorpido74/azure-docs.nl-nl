---
title: 'Azure ExpressRoute privé-peering: IPsec-transport modus configureren-Windows-hosts'
description: Klik hier voor meer informatie over het inschakelen van IPsec-transportmodus tussen Azure Windows VM's en on-premises Windows-hosts via ExpressRoute persoonlijke peering met behulp van groepsbeleidsobjecten en organisatie-eenheden.
services: expressroute
author: fabferri
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/17/2018
ms.author: fabferri
ms.custom: seodec18
ms.openlocfilehash: 1bc33047d31262af443cddc418853fbacd88aec1
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74022013"
---
# <a name="configure-ipsec-transport-mode-for-expressroute-private-peering"></a>IPsec-transportmodus voor ExpressRoute-privépeering configureren

Dit artikel helpt u bij het maken van IPsec-tunnels in de transportmodus via ExpressRoute persoonlijke peering tussen virtuele Azure-machines waarop Windows wordt uitgevoerd en on-premises Windows-hosts. De stappen in dit artikel maakt u deze configuratie met behulp van groepsbeleidsobjecten. Hoewel het mogelijk om deze configuratie wordt gemaakt zonder gebruik van de organisatie-eenheden (OU's) en groepsbeleidsobjecten (GPO's), wordt de combinatie van organisatie-eenheden en GPO's vereenvoudigt het beheer van uw beveiligingsbeleid en kunt u snel de schaal van. De stappen in dit artikel wordt ervan uitgegaan dat u al een Active Directory-configuratie hebt en dat u bekend bent met behulp van organisatie-eenheden en GPO's.

## <a name="about-this-configuration"></a>Over deze configuratie

De configuratie in de volgende stappen gebruiken een enkel Azure-netwerk (VNet) met de ExpressRoute-privépeering. Deze configuratie kan echter meer Azure vnet's en on-premises netwerken omspannen. In dit artikel helpt u bij het definiëren van een beleid voor IPSec-versleuteling en pas deze toe aan een groep van virtuele Azure-machines en hosts on-premises die deel van dezelfde organisatie-eenheid uitmaken. U configureert versleuteling tussen de Azure VM's (vm1 en vm2) en de on-premises host1 alleen voor HTTP-verkeer met bestemmingspoort 8080. Verschillende typen IPsec-beleid kan worden gemaakt op basis van uw vereisten.

### <a name="working-with-ous"></a>Werken met organisatie-eenheden 

Het beveiligingsbeleid dat is gekoppeld aan een organisatie-eenheid wordt doorgestuurd naar de computers via groepsbeleidsobjecten. Enkele voordelen als u met behulp van de organisatie-eenheden, in plaats van de beleidsregels toepassen op één host, zijn:

* Een beleid koppelen aan een organisatie-eenheid zorgt ervoor dat computers die deel uitmaken van dezelfde organisatie-eenheid hetzelfde beleid ophalen.
* Als u het beveiligingsbeleid dat is gekoppeld aan de organisatie-eenheid wijzigt, wordt de wijzigingen toepassen op alle hosts in de organisatie-eenheid.

### <a name="diagrams"></a>Diagrammen

Het volgende diagram toont de onderlinge verbinding en de toegewezen IP-adresruimte. Windows 2016 worden uitgevoerd door de Azure VM's en de on-premises-host. De Azure VM's en de on-premises host1 uitmaken deel van hetzelfde domein bevinden. De Azure VM's en de on-premises-hosts kunnen omzetten van namen goed met behulp van DNS.

[![1]][1]

Dit diagram toont de IPsec-tunnels tijdens de overdracht in het ExpressRoute-privépeering.

[![4]][4]

### <a name="working-with-ipsec-policy"></a>Werken met IPsec-beleid

In Windows is versleuteling gekoppeld aan een IPsec-beleid. IPsec-beleid bepaalt welke IP-verkeer is beveiligd en het beveiligingsmechanisme toegepast op de IP-pakketten.
**IPSec-beleid** zijn samengesteld uit de volgende items: **lijsten voor het Filter**, **filteracties**, en **beveiligingsregels**.

Bij het configureren van IPsec-beleid, is het belangrijk om te begrijpen van de volgende terminologie voor IPsec-beleid:

* **IPsec-beleid:** een verzameling van regels. Slechts één beleid kan worden actieve ('toegewezen') op een bepaald moment. Elk beleid kan hebben een of meer regels, die allemaal tegelijkertijd actief kan zijn. Een computer slechts één actieve IPSec-beleid kan worden toegewezen op basis van tijd. Echter, binnen het IPsec-beleid kunt u meerdere acties die kunnen worden uitgevoerd in verschillende situaties. Elke set van IPsec-regels is gekoppeld aan de lijst van een filter dat betrekking heeft op het type netwerkverkeer waarop de regel van toepassing is.

* **Een lijst met filteren:** filterlijsten zijn bundel van een of meer filters. Een lijst kan meerdere filters bevatten. Filter wordt gedefinieerd als de communicatie is toegestaan, beveiligd of geblokkeerd op basis van de IP-adresbereiken, protocollen, of zelfs bepaalde poorten. Elk filter overeenkomt met een bepaalde set voorwaarden. bijvoorbeeld: pakketten verzonden vanuit een bepaald subnet naar een bepaalde computer op een specifieke doelpoort. Wanneer netwerkomstandigheden overeenkomt met een of meer van deze filters, wordt de lijst met filters wordt geactiveerd. Elk filter wordt gedefinieerd binnen een specifiek filter-lijst. Filters kunnen niet worden gedeeld tussen de lijsten met filters. Een lijst met bepaalde filters kan echter worden opgenomen in verschillende IPsec-beleid. 

* **Filteracties:** een beveiligingsmethode definieert een reeks beveiligingsalgoritmen, protocollen, en sleutel van een computer biedt tijdens de onderhandelingen IKE. Filteracties zijn lijsten met beveiligingsmethoden, gerangschikt in volgorde van voorkeur.  Wanneer een computer wordt onderhandeld over een IPsec-sessie, accepteert of voorstellen op basis van de beveiligingsinstellingen die zijn opgeslagen in de lijst met acties van filter verzendt.

* **Beveiligingsregels:** regels bepalen hoe en wanneer een beleid voor IPSec-communicatie beveiligt. Hierbij **filterlijst** en **filteracties** te maken van een IPsec-regel voor het bouwen van de IPsec-verbinding. Elk beleid kan hebben een of meer regels, die allemaal tegelijkertijd actief kan zijn. Elke regel bevat een lijst met IP-filters en een verzameling van beveiligingsacties die na een overeenkomst met de filterlijst plaatsvinden:
  * IP-filteracties
  * Verificatiemethoden
  * Instellingen voor IP-tunnel
  * Verbindingstypen

[![5]][5]

## <a name="before-you-begin"></a>Voordat u begint

Zorg ervoor dat u voldoet aan de volgende vereisten:

* U moet een werkende configuratie van Active Directory die u gebruiken kunt voor het implementeren van instellingen voor Groepsbeleid hebben. Zie voor meer informatie over groepsbeleidsobjecten [Group Policy Objects](https://msdn.microsoft.com/library/windows/desktop/aa374162(v=vs.85).aspx).

* U moet een actief ExpressRoute-circuit hebben.
  * Zie voor meer informatie over het maken van een ExpressRoute-circuit [maken van een ExpressRoute-circuit](expressroute-howto-circuit-arm.md). 
  * Controleer of het circuit is ingeschakeld door de connectiviteitsprovider. 
  * Controleer of u hebt Azure private peering is geconfigureerd voor uw circuit. Zie de [routering configureren](expressroute-howto-routing-arm.md) artikel voor routeringsinstructies. 
  * Controleer of u hebt een VNet en een virtuele netwerkgateway gemaakt en volledig is ingericht. Volg de instructies voor [een virtuele netwerkgateway maken voor ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). Het GatewayType 'ExpressRoute', niet VPN maakt gebruik van een virtuele netwerkgateway voor ExpressRoute.

* De ExpressRoute-gateway voor virtueel netwerk moet zijn verbonden met het ExpressRoute-circuit. Zie voor meer informatie, [een VNet verbinden met een ExpressRoute-circuit](expressroute-howto-linkvnet-arm.md).

* Controleer of de Windows Azure-VM's zijn geïmplementeerd met het VNet.

* Controleer of er een verbinding tussen de on-premises-hosts en de Azure VM's is.

* Controleer of de Windows Azure-VM's en de on-premises-hosts kunnen DNS gebruiken om op te lossen correct namen zijn.

### <a name="workflow"></a>Werkstroom

1. Een groepsbeleidsobject maken en koppelen aan de organisatie-eenheid.
2. Definieer een IPsec **filteractie**.
3. Definieer een IPsec **filterlijst**.
4. Maken van een IPSec-beleid met **beveiligingsregels**.
5. De IPsec-groepsbeleidsobject aan de organisatie-eenheid toewijzen.

### <a name="example-values"></a>Voorbeeldwaarden

* **Domeinnaam:** ipsectest.com

* **Organisatie-eenheid:** IPSecOU

* **On-premises Windows-computer:** host1

* **Azure Windows VM's:** vm1, vm2

## <a name="creategpo"></a>1. een groeps beleidsobject maken

1. Voor het maken van een nieuw GPO dat is gekoppeld aan een organisatie-eenheid, open de module Groepsbeleidsbeheer en Ga naar de organisatie-eenheid waaraan het groepsbeleidsobject wordt gekoppeld. In het voorbeeld wordt de organisatie-eenheid met de naam **IPSecOU**. 

   [![9]][9]
2. Selecteer de organisatie-eenheid in de Group Policy Management-module en met de rechtermuisknop op. In de vervolgkeuzelijst, klikt u op '**een groepsbeleidsobject in dit domein maken en hier een koppeling...** ".

   [![10]][10]
3. Naam voor het groepsbeleidsobject de naam van een intuïtieve zodat u deze later eenvoudig kan vinden. Klik op **OK** te maken en koppelen van het groepsbeleidsobject.

   [![11]][11]

## <a name="enablelink"></a>2. Schakel de groeps beleidsobject koppeling in

Als u wilt het groepsbeleidsobject van toepassing op de organisatie-eenheid, het groepsbeleidsobject moet niet alleen worden gekoppeld aan de organisatie-eenheid, maar de koppeling moet ook worden ingeschakeld.

1. Ga naar het groepsbeleidsobject dat u hebt gemaakt, klik met de rechtermuisknop en selecteer **bewerken** in de vervolgkeuzelijst.
2. Als u wilt het groepsbeleidsobject van toepassing op de organisatie-eenheid, selecteert u **koppeling ingeschakeld**.

   [![12]][12]

## <a name="filteraction"></a>3. de IP-filter actie definiëren

1. In de vervolgkeuzelijst met de rechtermuisknop op **IP-beveiligingsbeleid op Active Directory**, en klik vervolgens op **beheren IP-filter een lijst met en acties te filteren...** .

   [![15]][15]
2. Op de '**beheren filter acties**' en klik op **toevoegen**.

   [![16]][16]

3. Op de **wizard IP-beveiliging-filteractie**, klikt u op **volgende**.

   [![17]][17]
4. Naam de filteractie de naam van een intuïtieve, zodat u deze later kunt terugvinden. In dit voorbeeld wordt de filteractie heet **myEncryption**. U kunt ook een beschrijving toevoegen. Klik op **Volgende**.

   [![18]][18]
5. **Onderhandelen over de beveiliging** kunt u het gedrag definiëren als IPsec kan worden gemaakt met een andere computer. Selecteer **onderhandelen over de beveiliging**, klikt u vervolgens op **volgende**.

   [![19]][19]
6. Op de **Communicating met computers die geen ondersteuning voor IPsec** weergeeft, schakelt **staan niet toe dat niet-beveiligde communicatie**, klikt u vervolgens op **volgende**.

   [![20]][20]
7. Op de **IP-verkeer en beveiliging** weergeeft, schakelt **aangepaste**, klikt u vervolgens op **instellingen...** .

   [![21]][21]
8. Op de **aangepaste methode beveiligingsinstellingen** weergeeft, schakelt **integriteit van gegevens en -codering (ESP): SHA1, 3DES**. Klik vervolgens op **OK**.

   [![22]][22]
9. Op de **filteracties beheren** pagina, kunt u zien dat de **myEncryption** filter is toegevoegd. Klik op **Sluiten**.

   [![23]][23]

## <a name="filterlist1"></a>4. een IP-filter lijst definiëren

Maak een filterlijst met versleutelde HTTP-verkeer met bestemmingspoort 8080.

1. Gebruiken om te komen welke typen verkeer moeten worden versleuteld, een **IP-filterlijst**. In de **IP-filterlijsten beheren** tabblad **toevoegen** om toe te voegen een nieuwe lijst met IP-filter.

   [![24 uur per dag]][24 uur per dag]
2. In de **naam:** veld, typt u een naam op voor uw lijst met IP-filter. Bijvoorbeeld, **azure-on-HTTP8080**. Klik vervolgens op **Toevoegen**.

   [![25]][25]
3. Op de **beschrijving van het IP-Filter en gespiegelde eigenschap** weergeeft, schakelt **gespiegeld**. De instelling van de gespiegelde komt overeen met pakketten naar in beide richtingen, waarmee voor communicatie in twee richtingen. Klik op **Volgende**.

   [![26]][26]
4. Op de **IP-Verkeersbron** pagina van de **bronadres:** vervolgkeuzelijst kiezen **een specifiek IP-adres of Subnet**. 

   [![27]][27]
5. Geef het bronadres **IP-adres of Subnet:** van de IP-verkeer, klikt u vervolgens op **volgende**.

   [![28]][28]
6. Geef de **doeladres:** IP-adres of Subnet. Klik op **Volgende**.

   [![29]][29]
7. Op de **Type IP-Protocol** weergeeft, schakelt **TCP**. Klik op **Volgende**.

   [![30]][30]
8. Op de **IP-protocolpoort** weergeeft, schakelt **van een willekeurige poort** en **op deze poort:** . Type **8080** in het tekstvak in. Deze instellingen bepalen dat alleen de HTTP-verkeer op bestemmingspoort 8080, worden versleuteld. Klik op **Volgende**.

   [![31]][31]
9. Bekijk de lijst met IP-filter.  De configuratie van de lijst met IP-Filter **azure-on-HTTP8080** activeert versleuteling voor al het verkeer dat overeenkomt met de volgende criteria:

   * Elke bron-adres in 10.0.1.0/24 (Azure Subnet2)
   * Alle doeladres in 10.2.27.0/25 (on-premises subnet)
   * TCP-protocol
   * Bestemmingspoort 8080

   [![32]][32]

## <a name="filterlist2"></a>5. de IP-filter lijst bewerken

U moet een tweede IP-filter voor het versleutelen van verkeer in de tegengestelde richting (van de host van de on-premises naar de Azure-VM) van hetzelfde type. Het proces voor het instellen van het nieuwe filter is de hetzelfde proces dat u hebt gebruikt voor het instellen van het eerste IP-filter. De enige verschillen zijn het Bronsubnet en bestemming subnet.

1. Selecteer een nieuw IP-filter toevoegen aan de lijst met IP-Filter, **bewerken**.

   [![33]][33]
2. Op de **IP-filterlijst** pagina, klikt u op **toevoegen**.

   [![34]][34]
3. Maak een tweede IP-filter met de instellingen in het volgende voorbeeld:

   [![35]][35]
4. Nadat u het tweede IP-filter gemaakt, wordt de lijst met IP-filter er als volgt:

   [![36]][36]

Als versleuteling vereist tussen een on-premises locatie en een Azure-subnet is voor het beveiligen van een toepassing, in plaats van de bestaande IP-filterlijst wijzigen kunt u in plaats daarvan een nieuwe lijst met IP-filter toevoegen. 2 IP koppelen filter een lijst met de dezelfde IPSec-beleid meer flexibiliteit biedt omdat een specifieke lijst met een IP-filter kan worden gewijzigd of verwijderd op elk gewenst moment zonder enige impact op de andere IP-filter weergegeven.

## <a name="ipsecpolicy"></a>6. een IPsec-beveiligings beleid maken 

Maak een IPSec-beleid met beveiligingsregels.

1. Selecteer de **IPSecurity beleidsregels op Active directory** dat is gekoppeld aan de organisatie-eenheid. Met de rechtermuisknop op en selecteer **IP-beveiligingsbeleid maken**.

   [![37]][37]
2. De naam van het beveiligingsbeleid. Bijvoorbeeld, **beleid-azure-on**. Klik op **Volgende**.

   [![38]][38]
3. Klik op **volgende** zonder dat het selectievakje te selecteren.

   [![39]][39]
4. Controleer de **eigenschappen bewerken** selectievakje is ingeschakeld en klik vervolgens op **voltooien**.

   [![40]][40]

## <a name="editipsec"></a>7. het IPsec-beveiligings beleid bewerken

Toevoegen aan het IPsec-beleid de **IP-filterlijst** en **filteractie** die u eerder hebt geconfigureerd.

1. In het beleid dat HTTP-eigenschappen **regels** tabblad **toevoegen**.

   [![41]][41]
2. Klik op de pagina Welkom **volgende**.

   [![42]][42]
3. Een regel biedt de mogelijkheid voor het definiëren van de IPsec-modus: tunnelmodus of transportmodus.

   * In de tunnelmodus, wordt het oorspronkelijke pakket ingekapseld met een reeks IP-headers. De interne routinggegevens beveiligt tunnelmodus door het versleutelen van de IP-header van het oorspronkelijke pakket. -Tunnelmodus is breed tussen gateways op site-naar-site VPN-scenario's geïmplementeerd. -Tunnelmodus is in de meeste gevallen gebruikt voor end-to-end versleuteling tussen hosts.

   * Transportmodus versleutelt alleen de nettolading en ESP aanhangwagen; de IP-header van het oorspronkelijke pakket is niet versleuteld. In de transportmodus zijn de IP-bron en doel-IP van de pakketten die ongewijzigd.

   Selecteer **met deze regel geeft geen een tunnel**, en klik vervolgens op **volgende**.

   [![43]][43]
4. **Netwerktype** definieert u de verbinding wordt met het beveiligingsbeleid voor het netwerk. Selecteer **alle netwerkverbindingen**, en klik vervolgens op **volgende**.

   [![44]][44]
5. Selecteer de lijst met IP-filter dat u eerder hebt gemaakt, **azure-on-HTTP8080**, en klik vervolgens op **volgende**.

   [![45]][45]
6. Selecteer de bestaande filteractie **myEncryption** die u eerder hebt gemaakt.

   [![46]][46]
7. Windows ondersteunt vier verschillende soorten verificaties: Kerberos, certificaten, NTLMv2 en vooraf gedeelde sleutel. Omdat we met domein-hosts werken, selecteer **Active Directory-standaard (Kerberos V5-protocol)** , en klik vervolgens op **volgende**.

   [![47]][47]
8. Het nieuwe beleid wordt gemaakt van de beveiligingsregel: **azure-on-HTTP8080**. Klik op **OK**.

   [![48]][48]

Het IPsec-beleid is vereist voor alle HTTP-verbindingen op de bestemmingspoort 8080 gebruik van IPsec-transportmodus. Omdat HTTP een niet-versleutelde tekst-protocol is, met het beveiligingsbeleid ingeschakeld zorgt ervoor dat gegevens worden versleuteld wanneer het wordt overgedragen via het ExpressRoute-privépeering. IP-beveiligingsbeleid voor Active Directory is complexer om te configureren dan Windows Firewall met geavanceerde beveiliging, maar het is toegestaan voor meer aanpassing van de IPsec-verbinding.

## <a name="assigngpo"></a>8. Wijs het IPsec-groeps beleidsobject toe aan de OE

1. Bekijk het beleid. Het beveiligingsbeleid van de groep is gedefinieerd, maar nog niet zijn toegewezen.

   [![49]][49]
2. Het beveiligingsbeleid van de groep toewijzen aan de organisatie-eenheid **IPSecOU**, met de rechtermuisknop op het beveiligingsbeleid en hebt gekozen **toewijzen**.
   Elke computer tht hoort bij de organisatie-eenheid heeft het beveiligingsbeleid van de groep toegewezen.

   [![50]][50]

## <a name="checktraffic"></a>Versleuteling van verkeer controleren

Om te zien wat de versleuteling GPO toegepast op de organisatie-eenheid, moet u IIS installeren op alle Azure-VM's en in de host1. Elke IIS kan worden aangepast om te reageren op HTTP-aanvragen op poort 8080.
Als u wilt controleren of versleuteling, kunt u een netwerksniffer (zoals Wireshark) installeren op alle computers in de organisatie-eenheid.
Een powershell-script werkt als een HTTP-client voor het genereren van HTTP-aanvragen op poort 8080:

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
De volgende netwerkopname ziet u dat de resultaten voor on-premises host1 met ESP filteren zodat deze overeenkomt met alleen de versleuteld verkeer weergeven:

[![51]][51]

Als u de powershell-script op-premisies (HTTP-client) uitvoert, ziet u de netwerkopname in de Azure-virtuele machine een vergelijkbare tracering.

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over ExpressRoute raadpleegt u de [Veelgestelde vragen over ExpressRoute](expressroute-faqs.md).

<!--Image References-->

[1]: ./media/expressroute-howto-ipsec-transport-private-windows/network-diagram.png "netwerk Diagram IPsec-transportmodus via ExpressRoute"
[4]: ./media/expressroute-howto-ipsec-transport-private-windows/ipsec-interesting-traffic.png "IPsec verkeer interessante"
[5]: ./media/expressroute-howto-ipsec-transport-private-windows/windows-ipsec.png "Windows IPsec-beleid"
[9]: ./media/expressroute-howto-ipsec-transport-private-windows/ou.png "organisatie-eenheid in het groepsbeleid"
[10]: ./media/expressroute-howto-ipsec-transport-private-windows/create-gpo-ou.png "een groepsbeleidsobject dat is gekoppeld aan de organisatie-eenheid maken"
[11]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-name.png "een naam toewijzen aan het groepsbeleidsobject dat is gekoppeld aan de organisatie-eenheid"
[12]: ./media/expressroute-howto-ipsec-transport-private-windows/edit-gpo.png "Bewerk het GPO"
[15]: ./media/expressroute-howto-ipsec-transport-private-windows/manage-ip-filter-list-filter-actions.png "een lijst met IP-Filter beheren en acties filteren"
[16]: ./media/expressroute-howto-ipsec-transport-private-windows/add-filter-action.png "filteractie toevoegen"
[17]: ./media/expressroute-howto-ipsec-transport-private-windows/action-wizard.png "actie Wizard"
[18]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action-name.png "filter actienaam"
[19]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action.png "filteractie"
[20]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action-no-ipsec.png "Geef het gedrag is een niet-beveiligde verbinding tot stand is gebracht"
[21]: ./media/expressroute-howto-ipsec-transport-private-windows/security-method.png "beveiligingsmechanisme"
[22]: ./media/expressroute-howto-ipsec-transport-private-windows/custom-security-method.png "aangepaste beveiligingsmethode"
[23]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-actions-list.png "actie filterlijst"
[24 uur per dag]: ./media/expressroute-howto-ipsec-transport-private-windows/add-new-ip-filter.png "een nieuwe lijst met IP-filter toevoegen"
[25]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-http-traffic.png "toevoegen HTTP-verkeer naar het IP-filter"
[26]: ./media/expressroute-howto-ipsec-transport-private-windows/match-both-direction.png "overeenkomst-pakket in beide richtingen"
[27]: ./media/expressroute-howto-ipsec-transport-private-windows/source-address.png "selectie van het subnet bron"
[28]: ./media/expressroute-howto-ipsec-transport-private-windows/source-network.png "netwerk van bron"
[29]: ./media/expressroute-howto-ipsec-transport-private-windows/destination-network.png "doelnetwerk"
[30]: ./media/expressroute-howto-ipsec-transport-private-windows/protocol.png "protocol"
[31]: ./media/expressroute-howto-ipsec-transport-private-windows/source-port-and-destination-port.png "bron-poort en doelpoort"
[32]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-list.png "filterlijst"
[33]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-for-http.png "IP-filterlijst met HTTP-verkeer"
[34]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-add-second-entry.png "toevoegen van een tweede IP-Filter"
[35]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-second-entry.png "IP-Filter lijst tweede vermelding"
[36]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-list-2entries.png "IP-Filter lijst tweede vermelding"
[37]: ./media/expressroute-howto-ipsec-transport-private-windows/create-ip-security-policy.png "het IP-beveiligingsbeleid maken"
[38]: ./media/expressroute-howto-ipsec-transport-private-windows/ipsec-policy-name.png "naam van het IPsec-beleid"
[39]: ./media/expressroute-howto-ipsec-transport-private-windows/security-policy-wizard.png "wizard beleid voor IPsec"
[40]: ./media/expressroute-howto-ipsec-transport-private-windows/edit-security-policy.png "bewerken van het IPsec-beleid"
[41]: ./media/expressroute-howto-ipsec-transport-private-windows/add-new-rule.png "nieuwe regel toevoegen aan het IPsec-beleid"
[42]: ./media/expressroute-howto-ipsec-transport-private-windows/create-security-rule.png "een nieuwe regel maken"
[43]: ./media/expressroute-howto-ipsec-transport-private-windows/transport-mode.png "transportmodus"
[44]: ./media/expressroute-howto-ipsec-transport-private-windows/network-type.png "netwerktype"
[45]: ./media/expressroute-howto-ipsec-transport-private-windows/selection-filter-list.png "selectie van de bestaande IP-filterlijst"
[46]: ./media/expressroute-howto-ipsec-transport-private-windows/selection-filter-action.png "selectie van bestaande filteractie"
[47]: ./media/expressroute-howto-ipsec-transport-private-windows/authentication-method.png "selectie van de methode voor netwerkauthenticatie"
[48]: ./media/expressroute-howto-ipsec-transport-private-windows/security-policy-completed.png "einde van proces voor het maken van het beveiligingsbeleid"
[49]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-not-assigned.png "IPSec-beleid is gekoppeld aan het groepsbeleidsobject, maar niet toegewezen"
[50]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-assigned.png "IPSec-beleid is toegewezen aan het groepsbeleidsobject"
[51]: ./media/expressroute-howto-ipsec-transport-private-windows/encrypted-traffic.png "vastleggen van IPSec-versleuteld verkeer"
