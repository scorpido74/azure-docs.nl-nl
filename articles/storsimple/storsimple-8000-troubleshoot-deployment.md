---
title: Problemen met de implementatie van storSimple 8000-reeksen oplossen | Microsoft Documenten
description: Beschrijft hoe u fouten diagnosticeren en oplossen die optreden wanneer u StorSimple voor het eerst implementeert.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: f2b454e812db1eea686f82e92841163f1129b6c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267623"
---
# <a name="troubleshoot-storsimple-device-deployment-issues"></a>Problemen met de implementatie van StorSimple-apparaten oplossen
## <a name="overview"></a>Overzicht
Dit artikel biedt handige richtlijnen voor het oplossen van problemen voor uw Microsoft Azure StorSimple-implementatie. Het beschrijft veelvoorkomende problemen, mogelijke oorzaken en aanbevolen stappen om u te helpen problemen op te lossen die u zou kunnen ondervinden wanneer u StorSimple configureert. 

Deze informatie is van toepassing op zowel het fysieke apparaat uit de StorSimple 8000-serie als het StorSimple Cloud Appliance.

> [!NOTE]
> Problemen met apparaatconfiguratie die u ondervinden, kunnen optreden wanneer u het apparaat voor de eerste keer implementeert, of wanneer het apparaat operationeel is, kunnen deze later optreden. Dit artikel richt zich op het oplossen van problemen met de eerste implementatie. Als u een operationeel apparaat wilt oplossen, gaat u naar [Het hulpprogramma Diagnostische gegevens gebruiken om een operationeel apparaat op te lossen](storsimple-8000-diagnostics.md).

In dit artikel worden ook de hulpprogramma's beschreven voor het oplossen van problemen met StorSimple-implementaties en wordt een stapsgewijs voorbeeld van het oplossen van problemen gegeven.

## <a name="first-time-deployment-issues"></a>Problemen met de eerste implementatie
Als u een probleem tegenkomt bij het voor de eerste keer implementeren van uw apparaat, moet u rekening houden met het volgende:

* Als u een fysiek apparaat probleemoplost, controleert u of de hardware is geïnstalleerd en geconfigureerd zoals beschreven in [Uw StorSimple 8100-apparaat installeren](storsimple-8100-hardware-installation.md) of [uw StorSimple 8600-apparaat installeren.](storsimple-8600-hardware-installation.md)
* Controleer de vereisten voor implementatie. Zorg ervoor dat u alle informatie hebt beschreven in de [checklist voor implementatieconfiguratie.](storsimple-8000-deployment-walkthrough-u2.md#deployment-configuration-checklist)
* Bekijk de StorSimple Release Notes om te zien of het probleem wordt beschreven. De release notes bevatten oplossingen voor bekende installatieproblemen. 

Tijdens de implementatie van het apparaat treden de meest voorkomende problemen op wanneer ze de wizard setup uitvoeren en wanneer ze het apparaat registreren via Windows PowerShell voor StorSimple. (U gebruikt Windows PowerShell voor StorSimple om uw StorSimple-apparaat te registreren en te configureren. Zie [Stap 3: Uw apparaat configureren en registreren via Windows PowerShell voor StorSimple voor](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)meer informatie over apparaatregistratie.

In de volgende secties u problemen oplossen die u tegenkomt wanneer u het StorSimple-apparaat voor de eerste keer configureert.

## <a name="first-time-setup-wizard-process"></a>Wizardproces voor het eerst instellen
In de volgende stappen wordt het installatiewizardproces samengevat. Zie [Uw on-premises StorSimple-apparaat implementeren](storsimple-8000-deployment-walkthrough-u2.md)voor gedetailleerde installatiegegevens.

1. Voer de cmdlet [Invoke-HcsSetupWizard](https://technet.microsoft.com/library/dn688135.aspx) uit om de wizard Setup te starten die u door de resterende stappen leidt. 
2. Configureer het netwerk: met de wizard Setup u netwerkinstellingen configureren voor de DATA 0-netwerkinterface op uw StorSimple-apparaat. Deze instellingen omvatten het volgende:
   * Virtual IP (VIP), subnet masker en gateway – De [Set-HcsNetInterface](https://technet.microsoft.com/library/dn688161.aspx) cmdlet wordt op de achtergrond uitgevoerd. Het configureert het IP-adres, subnetmasker en gateway voor de DATA 0-netwerkinterface op uw StorSimple-apparaat.
   * Primaire DNS-server – De cmdlet [Set-HcsDnsClientServerAddress](https://technet.microsoft.com/library/dn688172.aspx) wordt op de achtergrond uitgevoerd. Het configureert de DNS-instellingen voor uw StorSimple-oplossing.
   * NTP-server – De [cmdlet Set-HcsNtpClientServerAddress](https://technet.microsoft.com/library/dn688138.aspx) wordt op de achtergrond uitgevoerd. Het configureert de NTP-serverinstellingen voor uw StorSimple-oplossing.
   * Optionele webproxy : de cmdlet [Set-HcsWebProxy](https://technet.microsoft.com/library/dn688154.aspx) wordt op de achtergrond uitgevoerd. Het stelt en maakt de webproxyconfiguratie voor uw StorSimple-oplossing in.
3. Het wachtwoord instellen: de volgende stap is het instellen van het wachtwoord van de apparaatbeheerder.
   Het wachtwoord van de apparaatbeheerder wordt gebruikt om u aan te melden bij uw apparaat. Het standaardapparaatwachtwoord is **Password1**.
        
     > [!IMPORTANT]
     > Wachtwoorden worden verzameld voor registratie, maar pas toegepast nadat u het apparaat hebt geregistreerd. Als er geen wachtwoord wordt toegepast, wordt u gevraagd het wachtwoord opnieuw te verstrekken totdat de vereiste wachtwoorden (die voldoen aan de complexiteitsvereisten) zijn verzameld.
     
4. Registreer het apparaat: de laatste stap is het registreren van het apparaat met de StorSimple Device Manager-service die wordt uitgevoerd in Microsoft Azure. Voor de registratie moet u [de serviceregistratiesleutel](storsimple-8000-manage-service.md#get-the-service-registration-key) ophalen van de Azure-portal en deze in de wizard Setup verstrekken. **Nadat het apparaat is geregistreerd, wordt u een versleutelingssleutel voor servicegegevens verstrekt. Zorg ervoor dat u deze versleutelingssleutel op een veilige locatie bewaart, omdat deze nodig is om alle volgende apparaten bij de service te registreren.**

## <a name="common-errors-during-device-deployment"></a>Veelvoorkomende fouten tijdens de implementatie van het apparaat
In de volgende tabellen worden de veelvoorkomende fouten weergegeven die u tegenkomen wanneer u:

* Configureer de vereiste netwerkinstellingen.
* Configureer de optionele webproxy-instellingen.
* Stel het wachtwoord van de apparaatbeheerder in.
* Registreer het apparaat.

## <a name="errors-during-the-required-network-settings"></a>Fouten tijdens de vereiste netwerkinstellingen
| Nee. | Foutbericht | Mogelijke oorzaken | Aanbevolen actie |
| --- | --- | --- | --- |
| 1 |Invoke-HcsSetupWizard: deze opdracht kan alleen worden uitgevoerd op de actieve controller. |Configuratie werd uitgevoerd op de passieve controller. |Voer deze opdracht uit vanaf de actieve controller. Zie [Een actieve controller op uw apparaat identificeren](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device)voor meer informatie . |
| 2 |Invoke-HcsSetupWizard: Apparaat nog niet klaar. |Er zijn problemen met de netwerkconnectiviteit op DATA 0. |Controleer de fysieke netwerkconnectiviteit op DATA 0. |
| 3 |Invoke-HcsSetupWizard: Er is een IP-adres conflict met een ander systeem op het netwerk (Uitzondering van HRESULT: 0x80070263). |Het ip-adres dat voor DATA 0 werd geleverd, was al in gebruik door een ander systeem. |Geef een nieuw IP-adres op dat niet in gebruik is. |
| 4 |Invoke-HcsSetupWizard: een clusterbron is mislukt. (Uitzondering van HRESULT: 0x800713AE). |Dubbele VIP. Het meegeleverde IP is al in gebruik. |Geef een nieuw IP-adres op dat niet in gebruik is. |
| 5 |Invoke-HcsSetupWizard: Ongeldig IPv4-adres. |Het IP-adres wordt in een onjuiste indeling opgegeven. |Controleer de indeling en lever uw IP-adres opnieuw aan. Zie [Ipv4 Addressing][1]voor meer informatie. |
| 6 |Invoke-HcsSetupWizard: Ongeldig IPv6-adres. |Het IP-adres wordt in een onjuiste indeling opgegeven. |Controleer de indeling en lever uw IP-adres opnieuw aan. Zie [Ipv6 Addressing][2]voor meer informatie. |
| 7 |Invoke-HcsSetupWizard: Er zijn geen eindpunten meer beschikbaar vanaf de eindpuntmapper. (Uitzondering van HRESULT: 0x800706D9) |De clusterfunctionaliteit werkt niet. |[Neem contact op met Microsoft Support](storsimple-8000-contact-microsoft-support.md) voor de volgende stappen. |

## <a name="errors-during-the-optional-web-proxy-settings"></a>Fouten tijdens de optionele webproxy-instellingen
| Nee. | Foutbericht | Mogelijke oorzaken | Aanbevolen actie |
| --- | --- | --- | --- |
| 1 |Invoke-HcsSetupWizard: Ongeldige parameter (Uitzondering van HRESULT: 0x80070057) |Een van de parameters voor de proxy-instellingen is niet geldig. |De URI is niet in het juiste formaat aanwezig. Gebruik de volgende indeling: http://*\<IP-adres of FQDN van de webproxyserver>*:*\<TCP-poortnummer>* |
| 2 |Invoke-HcsSetupWizard: RPC-server niet beschikbaar (uitzondering van HRESULT: 0x800706ba) |De hoofdoorzaak is een van de volgende:<ol><li>Het cluster is niet omhoog.</li><li>De passieve controller kan niet communiceren met de actieve controller en de opdracht wordt uitgevoerd vanaf de passieve controller.</li></ol> |Afhankelijk van de oorzaak:<ol><li>[Neem contact op met Microsoft Support](storsimple-8000-contact-microsoft-support.md) om ervoor te zorgen dat het cluster is opgenomen.</li><li>Voer de opdracht uit vanaf de actieve controller. Als u de opdracht vanaf de passieve controller wilt uitvoeren, moet u ervoor zorgen dat de passieve controller met de actieve controller kan communiceren. U moet [contact](storsimple-8000-contact-microsoft-support.md) opnemen met Microsoft Support als deze verbinding is verbroken.</li></ol> |
| 3 |Invoke-HcsSetupWizard: RPC-aanroep is mislukt (uitzondering van HRESULT: 0x800706be) |Cluster is uitgevallen. |[Neem contact op met Microsoft Support](storsimple-8000-contact-microsoft-support.md) om ervoor te zorgen dat het cluster is opgenomen. |
| 4 |Invoke-HcsSetupWizard: Clusterbron niet gevonden (uitzondering van HRESULT: 0x8007138f) |De clusterbron wordt niet gevonden. Dit kan gebeuren wanneer de installatie niet correct was. |Mogelijk moet u het apparaat opnieuw instellen op de standaardinstellingen van de fabriek. [Neem contact op met Microsoft Support](storsimple-8000-contact-microsoft-support.md) om een clusterbron te maken. |
| 5 |Invoke-HcsSetupWizard: Clusterbron niet online (uitzondering van HRESULT: 0x8007138c) |Clusterbronnen zijn niet online. |[Neem contact op met Microsoft Support](storsimple-8000-contact-microsoft-support.md) voor de volgende stappen. |

## <a name="errors-related-to-device-administrator-password"></a>Fouten in verband met het wachtwoord van apparaatbeheerders
Het standaardwachtwoord voor apparaatbeheerders is **Wachtwoord1**. Dit wachtwoord verloopt na de eerste aanmelding; Daarom moet u de wizard setup gebruiken om deze te wijzigen. U moet een nieuw apparaatbeheerderswachtwoord opgeven wanneer u het apparaat voor de eerste keer registreert. 

Zorg ervoor dat uw wachtwoorden aan de volgende vereisten voldoen:

* Het wachtwoord van uw apparaatbeheerder moet tussen de 8 en 15 tekens lang zijn.
* Wachtwoorden moeten 3 van de volgende 4 tekentypen bevatten: kleine letters, hoofdletters, numerieke en speciale. 
* Uw wachtwoord kan niet hetzelfde zijn als de laatste 24 wachtwoorden.

Houd er bovendien rekening mee dat wachtwoorden elk jaar verlopen en alleen kunnen worden gewijzigd nadat u het apparaat hebt geregistreerd. Als de registratie om welke reden dan ook mislukt, worden de wachtwoorden niet gewijzigd.

Ga voor meer informatie over het wachtwoord van apparaatbeheerders naar [De StorSimple Device Manager-service gebruiken om uw StorSimple-wachtwoord te wijzigen.](storsimple-8000-change-passwords.md)

U een of meer van de volgende fouten tegenkomen bij het instellen van de apparaatbeheerder en De SorSimple Snapshot Manager-wachtwoorden.

| Nee. | Foutbericht | Aanbevolen actie |
| --- | --- | --- |
| 1 |Het wachtwoord overschrijdt de maximale lengte. |Het wachtwoord van uw apparaatbeheerder moet tussen de 8 en 15 tekens lang zijn. |
| 2 |Het wachtwoord voldoet niet aan de vereiste lengte. |Het wachtwoord van uw apparaatbeheerder moet tussen de 8 en 15 tekens lang zijn.|
| 3 |Het wachtwoord moet kleine letters bevatten. |Wachtwoorden moeten 3 van de volgende 4 tekentypen bevatten: kleine letters, hoofdletters, numerieke en speciale. Zorg ervoor dat uw wachtwoord aan deze vereisten voldoet. |
| 4 |Het wachtwoord moet numerieke tekens bevatten. |Wachtwoorden moeten 3 van de volgende 4 tekentypen bevatten: kleine letters, hoofdletters, numerieke en speciale. Zorg ervoor dat uw wachtwoord aan deze vereisten voldoet. |
| 5 |Het wachtwoord moet speciale tekens bevatten. |Wachtwoorden moeten 3 van de volgende 4 tekentypen bevatten: kleine letters, hoofdletters, numerieke en speciale. Zorg ervoor dat uw wachtwoord aan deze vereisten voldoet. |
| 6 |Het wachtwoord moet 3 van de volgende 4 tekentypen bevatten: hoofdletters, kleine letters, numerieke en speciale. |Uw wachtwoord bevat niet de vereiste typen tekens. Zorg ervoor dat uw wachtwoord aan deze vereisten voldoet. |
| 7 |Parameter komt niet overeen met de bevestiging. |Zorg ervoor dat uw wachtwoord aan alle vereisten voldoet en dat u het correct hebt ingevoerd. |
| 8 |Uw wachtwoord kan niet overeenkomen met de standaardwaarde. |Het standaardwachtwoord is *Password1*. U moet dit wachtwoord wijzigen nadat u zich voor de eerste keer hebt aangemeld. |
| 9 |Het wachtwoord dat u hebt ingevoerd, komt niet overeen met het apparaatwachtwoord. Typ het wachtwoord opnieuw. |Controleer het wachtwoord en typ het opnieuw. |

Wachtwoorden worden verzameld voordat het apparaat wordt geregistreerd, maar worden pas toegepast na een succesvolle registratie. De workflow voor wachtwoordherstel vereist dat het apparaat wordt geregistreerd.

> [!IMPORTANT]
> In het algemeen, als een poging om een wachtwoord toe te passen mislukt, dan is de software herhaaldelijk probeert om het wachtwoord te verzamelen totdat het succesvol is. In zeldzame gevallen kan het wachtwoord niet worden toegepast. In deze situatie u het apparaat registreren en doorgaan, maar de wachtwoorden worden niet gewijzigd. U het wachtwoord van de apparaatbeheerder wijzigen na de registratie vanuit de Azure-portal.


U het wachtwoord in de Azure-portal opnieuw instellen via de StorSimple Device Manager-service. Ga voor meer informatie naar [Het wachtwoord van de apparaatbeheerder wijzigen.](storsimple-8000-change-passwords.md#change-the-device-administrator-password)

## <a name="errors-during-device-registration"></a>Fouten tijdens het registreren van apparaten
U gebruikt de StorSimple Device Manager-service die wordt uitgevoerd in Microsoft Azure om het apparaat te registreren. Tijdens de registratie van het apparaat u een of meer van de volgende problemen tegenkomen.

| Nee. | Foutbericht | Mogelijke oorzaken | Aanbevolen actie |
| --- | --- | --- | --- |
| 1 |Fout 350027: kan het apparaat niet registreren bij StorSimple Device Manager. | |Wacht een paar minuten en probeer de bewerking opnieuw. Als het probleem blijft bestaan, neemt [u contact op met Microsoft Support](storsimple-8000-contact-microsoft-support.md). |
| 2 |Fout 350013: er is een fout opgetreden bij het registreren van het apparaat. Dit kan te wijten zijn aan een onjuiste serviceregistratiesleutel. | |Registreer het apparaat opnieuw met de juiste serviceregistratiesleutel. Zie [De serviceregistratiesleutel ophalen voor](storsimple-8000-manage-service.md#get-the-service-registration-key) meer informatie. |
| 3 |Fout 350063: Verificatie naar StorSimple Device Manager-service is geslaagd, maar de registratie is mislukt. Probeer de bewerking na verloop van tijd opnieuw. |Deze fout geeft aan dat de verificatie met ACS is geslaagd, maar dat de registeroproep naar de service is mislukt. Dit kan een gevolg zijn van een sporadische netwerkstoring. |Als het probleem blijft bestaan, neemt u [contact op met Microsoft Support](storsimple-8000-contact-microsoft-support.md). |
| 4 |Fout 350049: De service was niet bereikbaar tijdens de registratie. |Wanneer de service wordt gebeld, wordt een webuitzondering ontvangen. In sommige gevallen kan dit worden opgelost door de bewerking later opnieuw uit te proberen. |Controleer uw IP-adres en DNS-naam en probeer de bewerking opnieuw. Als het probleem zich blijft voordoen, neemt [u contact op met Microsoft Support.](storsimple-8000-contact-microsoft-support.md) |
| 5 |Fout 350031: Het apparaat is al geregistreerd. | |Geen actie nodig. |
| 6 |Fout 350016: Apparaatregistratie is mislukt. | |Controleer of de registratiesleutel correct is. |
| 7 |Invoke-HcsSetupWizard: er is een fout opgetreden tijdens het registreren van uw apparaat; dit kan te wijten zijn aan een onjuiste IP-adres of DNS-naam. Controleer uw netwerkinstellingen en probeer het opnieuw. Als het probleem zich blijft voordoen, neemt [u contact op met Microsoft Support](storsimple-8000-contact-microsoft-support.md). (Fout 350050) |Zorg ervoor dat uw apparaat het externe netwerk kan pingen. Als u geen verbinding hebt met het externe netwerk, kan de registratie mislukken met deze fout. Deze fout kan een combinatie zijn van een of meer van de volgende:<ul><li>Onjuist IP-adres</li><li>Onjuist subnet</li><li>Onjuiste gateway</li><li>Onjuiste DNS-instellingen</li></ul> |Bekijk de stappen in het [voorbeeld van stapsgewijze probleemoplossing](#step-by-step-storsimple-troubleshooting-example). |
| 8 |Invoke-HcsSetupWizard: de huidige bewerking is mislukt door een interne servicefout [0x1FBE2]. Probeer de operatie na een keer opnieuw. Als het probleem blijft bestaan, neemt u contact op met Microsoft Support. |Dit is een algemene fout die wordt gegenereerd voor alle onzichtbare fouten van de gebruiker van service of agent. De meest voorkomende reden kan zijn dat de ACS-verificatie is mislukt. Een mogelijke oorzaak voor de storing is dat er problemen zijn met de NTP-serverconfiguratie en dat de tijd op het apparaat niet correct is ingesteld. |Corrigeer de tijd (als er problemen zijn) en probeer vervolgens de registratiebewerking opnieuw. Als u de opdracht Set-HcsSystem -Timezone gebruikt om de tijdzone aan te passen, kapitaliseert u elk woord in de tijdzone (bijvoorbeeld 'Pacific Standard Time').  Als dit probleem zich blijft voordoen, neemt [u contact op met Microsoft Support](storsimple-8000-contact-microsoft-support.md) voor de volgende stappen. |
| 9 |Waarschuwing: Kan het apparaat niet activeren. De wachtwoorden van uw apparaatbeheerder en StorSimple Snapshot Manager zijn niet gewijzigd. |Als de registratie mislukt, worden de wachtwoorden van de apparaatbeheerder en StorSimple Snapshot Manager niet gewijzigd. | |

## <a name="tools-for-troubleshooting-storsimple-deployments"></a>Hulpprogramma's voor het oplossen van problemen met StorSimple-implementaties
StorSimple bevat verschillende tools die u gebruiken om uw StorSimple-oplossing op te lossen. Deze omvatten:

* Ondersteuningspakketten en apparaatlogboeken.
* Cmdlets speciaal ontworpen voor het oplossen van problemen.

## <a name="support-packages-and-device-logs-available-for-troubleshooting"></a>Ondersteuningspakketten en apparaatlogboeken beschikbaar voor probleemoplossing
Een ondersteuningspakket bevat alle relevante logboeken die het Microsoft Support-team kunnen helpen bij het oplossen van apparaatproblemen. U Windows PowerShell voor StorSimple gebruiken om een versleuteld ondersteuningspakket te genereren dat u vervolgens delen met ondersteuningspersoneel.

### <a name="to-view-the-logs-or-the-contents-of-the-support-package"></a>De logboeken of de inhoud van het ondersteuningspakket weergeven
1. Gebruik Windows PowerShell voor StorSimple om een ondersteuningspakket te genereren zoals beschreven in [Een ondersteuningspakket maken en beheren.](storsimple-8000-create-manage-support-package.md)
2. Download het [decryptiescript](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) lokaal op uw clientcomputer.
3. Gebruik deze [stapsgewijze procedure](storsimple-8000-create-manage-support-package.md#edit-a-support-package) om het ondersteuningspakket te openen en te decoderen.
4. De gedecodeerde support package logs zijn in etw/ etvx formaat. U de volgende stappen uitvoeren om deze bestanden weer te geven in Windows Logboeken:
   
   1. Voer de opdracht **eventvwr** uit op uw Windows-client. Hiermee wordt de logboeken gestart.
   2. Klik **in** het deelvenster Handelingen op **Opgeslagen logboek openen** en wijs de logbestanden in de etvx/etw-indeling (het ondersteuningspakket) aan. U het bestand nu bekijken. Nadat u het bestand hebt geopend, u met de rechtermuisknop klikken en het bestand opslaan als tekst.
      
      > [!IMPORTANT]
      > U de **cmdlet Get-WinEvent** ook gebruiken om deze bestanden in Windows PowerShell te openen. Zie [Get-WinEvent](https://technet.microsoft.com/library/hh849682.aspx) in de referentiedocumentatie voor cmdlet van Windows PowerShell voor meer informatie.
     
5. Wanneer de logboeken worden geopend in Logboeken, zoekt u naar de volgende logboeken die problemen bevatten die verband houden met de apparaatconfiguratie:
   
   * hcs_pfconfig/operationeel logboek
   * hcs_pfconfig/Config
6. Zoek in de logboekbestanden naar tekenreeksen die betrekking hebben op de cmdlets die door de wizard Setup worden aangeroepen. Zie [Wizardproces](#first-time-setup-wizard-process) voor de eerste keer instellen voor een lijst met deze cmdlets.
7. Als u de oorzaak van het probleem niet achterhalen, u [contact opnemen met Microsoft Support](storsimple-8000-contact-microsoft-support.md) voor de volgende stappen. Gebruik de stappen in [Een ondersteuningsverzoek maken](storsimple-8000-contact-microsoft-support.md#create-a-support-request) wanneer u contact opneemt met Microsoft Support voor hulp.

## <a name="cmdlets-available-for-troubleshooting"></a>Cmdlets beschikbaar voor het oplossen van problemen
Gebruik de volgende Windows PowerShell-cmdlets om verbindingsfouten te detecteren.

* `Get-NetAdapter`: Gebruik deze cmdlet om de status van netwerkinterfaces te detecteren.
* `Test-Connection`: Gebruik deze cmdlet om de netwerkverbinding binnen en buiten het netwerk te controleren.
* `Test-HcsmConnection`: Gebruik deze cmdlet om de connectiviteit van een met succes geregistreerd apparaat te controleren.
* `Sync-HcsTime`: Gebruik deze cmdlet om de apparaattijd weer te geven en een tijdsynchronisatie met de NTP-server af te dwingen.
* `Enable-HcsPing`en `Disable-HcsPing`: Gebruik deze cmdlets om de hosts in staat te stellen de netwerkinterfaces op uw StorSimple-apparaat te pingen. Standaard reageren de StorSimple-netwerkinterfaces niet op pingverzoeken.
* `Trace-HcsRoute`: Gebruik deze cmdlet als routetracingtool. Het stuurt pakketten naar elke router op weg naar een eindbestemming over een periode van tijd, en vervolgens berekent resultaten op basis van de pakketten geretourneerd van elke hop. Aangezien `Trace-HcsRoute` de mate van pakketverlies op een bepaalde router of koppeling wordt weergegeven, u bepalen welke routers of koppelingen netwerkproblemen kunnen veroorzaken.
* `Get-HcsRoutingTable`: Gebruik deze cmdlet om de lokale IP-routeringstabel weer te geven.

## <a name="troubleshoot-with-the-get-netadapter-cmdlet"></a>Problemen oplossen met de cmdlet Get-NetAdapter
Wanneer u netwerkinterfaces configureert voor een nieuwe apparaatimplementatie, is de hardwarestatus niet beschikbaar in de gebruikersinterface van de StorSimple Device Manager-service omdat het apparaat nog niet is geregistreerd bij de service. Bovendien kan het is dat het **hardware-statusblad** niet altijd de status van het apparaat correct weergeeft, vooral als er problemen zijn die van invloed zijn op de synchronisatie van de service. In deze situaties `Get-NetAdapter` u de cmdlet gebruiken om de status en status van uw netwerkinterfaces te bepalen.

### <a name="to-see-a-list-of-all-the-network-adapters-on-your-device"></a>Een lijst met alle netwerkadapters op uw apparaat weergeven
1. Start Windows PowerShell voor StorSimple `Get-NetAdapter`en typ . 
2. Gebruik de uitvoer `Get-NetAdapter` van de cmdlet en de volgende richtlijnen om de status van uw netwerkinterface te begrijpen.
   
   * Als de interface in orde is en is ingeschakeld, wordt de **ifIndex-status** weergegeven als **Up**.
   * Als de interface in orde is, maar niet fysiek is verbonden (via een netwerkkabel), wordt de **ifIndex** weergegeven als **Uitgeschakeld**.
   * Als de interface gezond is, maar niet is ingeschakeld, wordt de **ifIndex-status** weergegeven als **Niet Aanwezig**.
   * Als de interface niet bestaat, wordt deze niet weergegeven in deze lijst. De gebruikersinterface van de StorSimple Device Manager-service toont deze interface nog steeds in een mislukte status.

Ga voor meer informatie over het gebruik van deze cmdlet naar [Get-NetAdapter](https://docs.microsoft.com/powershell/module/netadapter/get-netadapter?view=win10-ps) in de Windows PowerShell-cmdlet-verwijzing.

De volgende secties tonen monsters `Get-NetAdapter` van de output van de cmdlet.

 In deze monsters was controller 0 de passieve controller en werd deze als volgt geconfigureerd:

* Data 0, DATA 1, DATA 2 en DATA 3 netwerkinterfaces bestonden op het apparaat.
* DATA 4- en DATA 5-netwerkinterfacekaarten waren niet aanwezig; daarom worden ze niet vermeld in de output.
* GEGEVENS 0 is ingeschakeld.

Controller 1 was de actieve controller en is als volgt geconfigureerd:

* Data 0, DATA 1, DATA 2, DATA 3, DATA 4 en DATA 5 netwerkinterfaces bestonden op het apparaat.
* GEGEVENS 0 is ingeschakeld.

**Voorbeelduitvoer – controller 0**

Het volgende is de output van controller 0 (de passieve controller). GEGEVENS 1, GEGEVENS 2 en GEGEVENS 3 zijn niet met elkaar verbonden. GEGEVENS 4 en DATA 5 worden niet vermeld omdat ze niet op het apparaat aanwezig zijn.

     Controller0>Get-NetAdapter
     Name                 InterfaceDescription                        ifIndex  Status
     ------               --------------------                        -------  ----------
     DATA3                Mellanox ConnectX-3 Ethernet Adapter #2     17       NotPresent
     DATA2                Mellanox ConnectX-3 Ethernet Adapter        14       NotPresent
     Ethernet 2           HCS VNIC                                    13       Up
     DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
     DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up


**Voorbeelduitvoer – controller 1**

Het volgende is de uitvoer van controller 1 (de actieve controller). Alleen de DATA 0-netwerkinterface op het apparaat is geconfigureerd en werkt.

     Controller1>Get-NetAdapter
     Name                 InterfaceDescription                        ifIndex  Status
     ------               --------------------                        -------  ----------
     DATA3                Mellanox ConnectX-3 Ethernet Adapter        18       NotPresent
     DATA2                Mellanox ConnectX-3 Ethernet Adapter #2     19       NotPresent
     DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
     DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up
     Ethernet 2           HCS VNIC                                    13       Up
     DATA5                Intel(R) Gigabit ET Dual Port Server...     14       NotPresent
     DATA4                Intel(R) Gigabit ET Dual Port Serv...#2     17       NotPresent


## <a name="troubleshoot-with-the-test-connection-cmdlet"></a>Problemen oplossen met de cmdlet Test-Connection
U `Test-Connection` de cmdlet gebruiken om te bepalen of uw StorSimple-apparaat verbinding kan maken met het externe netwerk. Als alle netwerkparameters, inclusief de DNS, correct zijn geconfigureerd in de `Test-Connection` wizard Setup, u de cmdlet gebruiken om een bekend adres buiten het netwerk te pingen, zoals outlook.com.

U moet ping inschakelen om verbindingsproblemen met deze cmdlet op te lossen als ping is uitgeschakeld.

Zie de volgende voorbeelden `Test-Connection` van de output van de cmdlet.

> [!NOTE]
> In het eerste voorbeeld is het apparaat geconfigureerd met een onjuiste DNS. In het tweede voorbeeld is de DNS correct.

**Voorbeelduitvoer – onjuiste DNS**

In het volgende voorbeeld is er geen uitvoer voor de IPV4- en IPV6-adressen, wat aangeeft dat de DNS niet is opgelost. Dit betekent dat er geen verbinding is met het externe netwerk en dat er een juiste DNS moet worden geleverd.

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com

**Voorbeelduitvoer – juiste DNS**

In het volgende voorbeeld retourneert de DNS het IPV4-adres, wat aangeeft dat de DNS correct is geconfigureerd. Dit bevestigt dat er verbinding is met het externe netwerk.

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194

## <a name="troubleshoot-with-the-test-hcsmconnection-cmdlet"></a>Problemen oplossen met de cmdlet Test-HcsmConnection
Gebruik `Test-HcsmConnection` de cmdlet voor een apparaat dat al is verbonden met en geregistreerd is bij uw StorSimple Device Manager-service. Met deze cmdlet u de verbinding tussen een geregistreerd apparaat en de bijbehorende StorSimple Device Manager-service controleren. U deze opdracht uitvoeren op Windows PowerShell voor StorSimple.

### <a name="to-run-the-test-hcsmconnection-cmdlet"></a>De test-hcsmConnection-cmdlet uitvoeren
1. Zorg ervoor dat het apparaat is geregistreerd.
2. Controleer de status van het apparaat. Als het apparaat is gedeactiveerd, in de onderhoudsmodus of offline is, ziet u mogelijk een van de volgende fouten:
   
   * ErrorCode.CiSDeviceDecommissioned – dit geeft aan dat het apparaat is gedeactiveerd.
   * ErrorCode.DeviceNotReady – dit geeft aan dat het apparaat in de onderhoudsmodus staat.
   * ErrorCode.DeviceNotReady – dit geeft aan dat het apparaat niet online is.
3. Controleer of de StorSimple Device Manager-service wordt uitgevoerd (gebruik de cmdlet [Get-ClusterResource).](https://technet.microsoft.com/library/ee461004.aspx) Als de service niet wordt uitgevoerd, ziet u mogelijk de volgende fouten:
   
   * ErrorCode.cisapplianceagentniet online
   * ErrorCode.CisPowershellScriptHcsError – dit geeft aan dat er een uitzondering was toen u Get-ClusterResource gebruikte.
4. Controleer het ACS-token (Access Control Service) .Check the Access Control Service (ACS) token. Als er een webuitzondering wordt gemaakt, kan dit het gevolg zijn van een gatewayprobleem, een ontbrekende proxyverificatie, een onjuiste DNS of een verificatiefout. Mogelijk ziet u de volgende fouten:
   
   * ErrorCode.CiSApplianceGateway – dit duidt op een httpStatusCode.BadGateway-uitzondering: de naamresolver-service kan de hostnaam niet oplossen.
   * ErrorCode.CiSApplianceProxy – dit duidt op een HttpStatusCode.ProxyAuthenticationRequired exception (HTTP-statuscode 407): de client kan zich niet verifiëren met de proxyserver.
   * ErrorCode.CiSApplianceDNSError – dit duidt op een uitzondering voor WebExceptionStatus.NameResolutionFailure: de naamresolver-service kan de hostnaam niet oplossen.
   * ErrorCode.CiSApplianceACSError – dit geeft aan dat de service een verificatiefout heeft geretourneerd, maar er is verbinding.
     
     Als er geen webuitzondering wordt gegenereerd, controleert u op ErrorCode.CiSApplianceFailure. Dit geeft aan dat het apparaat is mislukt.
5. Controleer de cloudserviceconnectiviteit. Als de service een webuitzondering maakt, ziet u mogelijk de volgende fouten:
   
   * ErrorCode.CiSApplianceGateway – dit duidt op een httpStatusCode.BadGateway uitzondering: een intermediate proxy server heeft een slecht verzoek ontvangen van een andere proxy of van de oorspronkelijke server.
   * ErrorCode.CiSApplianceProxy – dit duidt op een HttpStatusCode.ProxyAuthenticationRequired exception (HTTP-statuscode 407): de client kan zich niet verifiëren met de proxyserver.
   * ErrorCode.CiSApplianceDNSError – dit duidt op een uitzondering voor WebExceptionStatus.NameResolutionFailure: de naamresolver-service kan de hostnaam niet oplossen.
   * ErrorCode.CiSApplianceACSError – dit geeft aan dat de service een verificatiefout heeft geretourneerd, maar er is verbinding.
     
     Als er geen webuitzondering wordt gegenereerd, controleert u op ErrorCode.CiSApplianceSaasServiceError. Dit duidt op een probleem met de StorSimple Device Manager-service.
6. Controleer de Azure Service Bus-connectiviteit. ErrorCode.CiSApplianceServiceBusError geeft aan dat het apparaat geen verbinding kan maken met de Service Bus.

De logbestanden CiSCommandletLog0Curr.errlog en CiSAgentsvc0Curr.errlog hebben meer informatie, zoals uitzonderingsdetails.

Ga voor meer informatie over het gebruik van de cmdlet naar [Test-HcsmConnection](https://technet.microsoft.com/library/dn715782.aspx) in de referentiedocumentatie van Windows PowerShell.

> [!IMPORTANT]
> U deze cmdlet uitvoeren voor zowel de actieve als de passieve controller.

Zie de volgende voorbeelden `Test-HcsmConnection` van de output van de cmdlet.

**Voorbeelduitvoer – met succes geregistreerd apparaat met StorSimple Update 3**

      Controller1>Test-HcsmConnection

      Checking device registration state  ... Success
      Device registered successfully

      Checking primary NTP server [time.windows.com] ... Success

      Checking web proxy  ... NOT SET

      Checking primary IPv4 DNS server [10.222.118.154] ... Success
      Checking primary IPv6 DNS server  ... NOT SET
      Checking secondary IPv4 DNS server [10.222.120.24] ... Success
      Checking secondary IPv6 DNS server  ... NOT SET

      Checking device online  ... Success

      Checking device authentication  ... This will take a few minutes.
      Checking device authentication  ... Success

      Checking connectivity from device to service  ... This will take a few minutes.

      Checking connectivity from device to service  ... Success

      Checking connectivity from service to device  ... Success

      Checking connectivity to Microsoft Update servers  ... Success
      Controller1>

**Voorbeelduitvoer – offline apparaat** 

Dit voorbeeld is afkomstig van een apparaat met de status **Offline** in de Azure-portal.

     Checking device registrationstate: Success
     Device is registered successfully
     Checking connectivity from device to SaaS.. Failure

Het apparaat kan geen verbinding maken met de huidige webproxyconfiguratie. Dit kan een probleem zijn met de webproxyconfiguratie of een probleem met de netwerkverbinding. In dit geval moet u ervoor zorgen dat uw webproxy-instellingen correct zijn en dat uw webproxyservers online en bereikbaar zijn.

## <a name="troubleshoot-with-the-sync-hcstime-cmdlet"></a>Problemen oplossen met de sync-hcstime-cmdlet
Gebruik deze cmdlet om de tijd van het apparaat weer te geven. Als de apparaattijd een verschuiving heeft met de NTP-server, u deze cmdlet gebruiken om de tijd met uw NTP-server te forceren.
- Als de verschuiving tussen het apparaat en de NTP-server langer is dan 5 minuten, ziet u een waarschuwing. 
- Als de verschuiving meer dan 15 minuten bedraagt, gaat het apparaat offline. U deze cmdlet nog steeds gebruiken om een tijdsynchronisatie te forceren. 
- Als de verschuiving echter meer dan 15 uur bedraagt, u de tijd niet forceren en wordt er een foutbericht weergegeven.

**Voorbeelduitvoer – tijdsynchronisatie met Sync-HcsTime**

     Controller0>Sync-HcsTime
     The current device time is 4/24/2015 4:05:40 PM UTC.

     Time difference between NTP server and appliance is 00.0824069 seconds. Do you want to resync time with NTP server?
     [Y] Yes [N] No (Default is "Y"): Y
     Controller0>

## <a name="troubleshoot-with-the-enable-hcsping-and-disable-hcsping-cmdlets"></a>Problemen oplossen met de cmdlets Enable-HcsPing en Disable-HcsPing
Gebruik deze cmdlets om ervoor te zorgen dat de netwerkinterfaces op uw apparaat reageren op ICMP-pingverzoeken. Standaard reageren de StorSimple-netwerkinterfaces niet op pingverzoeken. Het gebruik van deze cmdlet is de eenvoudigste manier om te weten of uw apparaat online en bereikbaar is.

**Voorbeelduitvoer – Inschakelen-HcsPing en Disable-HcsPing**

     Controller0>
     Controller0>Enable-HcsPing
     Successfully enabled ping.
     Controller0>
     Controller0>
     Controller0>Disable-HcsPing
     Successfully disabled ping.
     Controller0>

## <a name="troubleshoot-with-the-trace-hcsroute-cmdlet"></a>Problemen oplossen met de trace-hcsroute-cmdlet
Gebruik deze cmdlet als routetraceringstool. Het stuurt pakketten naar elke router op weg naar een eindbestemming over een periode van tijd, en vervolgens berekent resultaten op basis van de pakketten geretourneerd van elke hop. Omdat de cmdlet de mate van pakketverlies op een bepaalde router of koppeling weergeeft, u bepalen welke routers of koppelingen netwerkproblemen kunnen veroorzaken.

**Voorbeelduitvoer die laat zien hoe u de route van een pakket traceren met Trace-HcsRoute**

     Controller0>Trace-HcsRoute -Target 10.126.174.25

     Tracing route to contoso.com [10.126.174.25]
     over a maximum of 30 hops:
       0  HCSNode0 [10.126.173.90]
       1  contoso.com [10.126.174.25]

     Computing statistics for 25 seconds...
                 Source to Here   This Node/Link
     Hop  RTT    Lost/Sent = Pct  Lost/Sent = Pct  Address
       0                                           HCSNode0 [10.126.173.90]
                                     0/ 100 =  0%   |
       1    0ms     0/ 100 =  0%     0/ 100 =  0%  contoso.com
      [10.126.174.25]

     Trace complete.

## <a name="troubleshoot-with-the-get-hcsroutingtable-cmdlet"></a>Problemen oplossen met de cmdlet Get-HcsRoutingTable
Gebruik deze cmdlet om de routeringstabel voor uw StorSimple-apparaat weer te geven. Een routeringstabel is een set regels waarmee kan worden bepaald waar gegevenspakketten die via een IP-netwerk (Internet Protocol) reizen, worden geleid.

De routeringstabel toont de interfaces en de gateway die de gegevens naar de opgegeven netwerken leidt. Het geeft ook de routeringsstatistiek die de beslisser is voor het pad dat is genomen om een bepaalde bestemming te bereiken. Hoe lager de routeringsstatistiek, hoe hoger de voorkeur.

Als u bijvoorbeeld 2 netwerkinterfaces, DATA 2 en DATA 3 hebt, die zijn verbonden met internet. Als de routeringsstatistieken voor respectievelijk GEGEVENS 2 en DATA 3 15 en 261 zijn, is DATA 2 met de lagere routeringsstatistiek de voorkeursinterface die wordt gebruikt om het internet te bereiken.

Als u Update 1 uitvoert op uw StorSimple-apparaat, heeft uw DATA 0-netwerkinterface de hoogste voorkeur voor het cloudverkeer. Dit houdt in dat zelfs als er andere cloud-interfaces zijn, het cloudverkeer zou worden doorgestuurd via DATA 0.

Als u `Get-HcsRoutingTable` de cmdlet uitvoert zonder parameters op te geven (zoals in het volgende voorbeeld wordt weergegeven), wordt in de cmdlet zowel IPv4- als IPv6-routeringstabellen uitgevoerd. U ook `Get-HcsRoutingTable -IPv4` een `Get-HcsRoutingTable -IPv6` relevante routeringstabel opgeven of deze opte nemen.

      Controller0>
      Controller0>Get-HcsRoutingTable
      ===========================================================================
      Interface List
       14...00 50 cc 79 63 40 ......Intel(R) 82574L Gigabit Network Connection
       12...02 9a 0a 5b 98 1f ......Microsoft Failover Cluster Virtual Adapter
       13...28 18 78 bc 4b 85 ......HCS VNIC
        1...........................Software Loopback Interface 1
       21...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #2
       22...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #3
      ===========================================================================

      IPv4 Route Table
      ===========================================================================
      Active Routes:
      Network Destination        Netmask          Gateway       Interface  Metric
                0.0.0.0          0.0.0.0  192.168.111.100  192.168.111.101     15
              127.0.0.0        255.0.0.0         On-link         127.0.0.1    306
              127.0.0.1  255.255.255.255         On-link         127.0.0.1    306
        127.255.255.255  255.255.255.255         On-link         127.0.0.1    306
            169.254.0.0      255.255.0.0         On-link     169.254.1.235    261
          169.254.1.235  255.255.255.255         On-link     169.254.1.235    261
        169.254.255.255  255.255.255.255         On-link     169.254.1.235    261
          192.168.111.0    255.255.255.0         On-link   192.168.111.101    266
        192.168.111.101  255.255.255.255         On-link   192.168.111.101    266
        192.168.111.255  255.255.255.255         On-link   192.168.111.101    266
              224.0.0.0        240.0.0.0         On-link         127.0.0.1    306
              224.0.0.0        240.0.0.0         On-link     169.254.1.235    261
              224.0.0.0        240.0.0.0         On-link   192.168.111.101    266
        255.255.255.255  255.255.255.255         On-link         127.0.0.1    306
        255.255.255.255  255.255.255.255         On-link     169.254.1.235    261
        255.255.255.255  255.255.255.255         On-link   192.168.111.101    266
      ===========================================================================
      Persistent Routes:
        Network Address          Netmask  Gateway Address  Metric
                0.0.0.0          0.0.0.0  192.168.111.100       5
      ===========================================================================

      IPv6 Route Table
      ===========================================================================
      Active Routes:
       If Metric Network Destination      Gateway
        1    306 ::1/128                  On-link
       13    276 fd99:4c5b:5525:d80b::/64 On-link
       13    276 fd99:4c5b:5525:d80b::1/128
                                          On-link
       13    276 fd99:4c5b:5525:d80b::3/128
                                          On-link
       13    276 fe80::/64                On-link
       12    261 fe80::/64                On-link
       13    276 fe80::17a:4eba:7c80:727f/128
                                          On-link
       12    261 fe80::fc97:1a53:e81a:3454/128
                                          On-link
        1    306 ff00::/8                 On-link
       13    276 ff00::/8                 On-link
       12    261 ff00::/8                 On-link
       14    266 ff00::/8                 On-link
      ===========================================================================
      Persistent Routes:
        None

      Controller0>

## <a name="step-by-step-storsimple-troubleshooting-example"></a>Stapsgewijs voorbeeld van het oplossen van problemen met StorSimple
In het volgende voorbeeld wordt stapsgewijs problemen met het oplossen van problemen met een StorSimple-implementatie weergegeven. In het voorbeeldscenario mislukt de apparaatregistratie met een foutbericht dat aangeeft dat de netwerkinstellingen of de DNS-naam onjuist zijn.

Het geretourneerde foutbericht is:

     Invoke-HcsSetupWizard: An error has occurred while registering the device. This could be due to incorrect IP address or DNS name. Please check your network settings and try again. If the problems persist, contact Microsoft Support.
     +CategoryInfo: Not specified
     +FullyQualifiedErrorID: CiSClientCommunicationErros, Microsoft.HCS.Management.PowerShell.Cmdlets.InvokeHcsSetupWizardCommand

De fout kan worden veroorzaakt door een van de volgende:

* Onjuiste hardware-installatie
* Defecte netwerkinterface(en)
* Onjuist IP-adres, subnetmasker, gateway, primaire DNS-server of webproxy
* Onjuiste registratiesleutel
* Onjuiste firewall-instellingen

### <a name="to-locate-and-fix-the-device-registration-problem"></a>Het probleem met de registratie van het apparaat lokaliseren en oplossen
1. Controleer de configuratie van uw apparaat: voer op de actieve controller uit `Invoke-HcsSetupWizard`.
   
   > [!NOTE]
   > De wizard Setup moet worden uitgevoerd op de actieve controller. Als u wilt controleren of u bent verbonden met de actieve controller, bekijkt u de banner die in de seriële console wordt weergegeven. De banner geeft aan of u bent verbonden met controller 0 of controller 1 en of de controller actief of passief is. Ga voor meer informatie naar [Een actieve controller op uw apparaat identificeren.](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device)
   
2. Controleer of het apparaat correct is bekabeld: controleer de netwerkbekabeling op het achtervlak van het apparaat. De bekabeling is specifiek voor het apparaatmodel. Ga voor meer informatie naar [Installeer uw StorSimple 8100-apparaat](storsimple-8100-hardware-installation.md) of [installeer uw StorSimple 8600-apparaat.](storsimple-8600-hardware-installation.md)
   
   > [!NOTE]
   > Als u 10 GbE-netwerkpoorten gebruikt, moet u de meegeleverde QSFP-SFP-adapters en SFP-kabels gebruiken. Zie voor meer informatie de [lijst met kabels, switches en transceivers die worden aanbevolen voor de 10 GbE-poorten.](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)
  
3. Controleer de status van de netwerkinterface:
   
   * Gebruik de cmdlet Get-NetAdapter om de status van de netwerkinterfaces voor DATA 0 te detecteren. 
   * Als de koppeling niet werkt, geeft de **ifindex-status** aan dat de interface is uitgeschakeld. U moet dan de netwerkverbinding van de poort met het toestel en de schakelaar controleren. U moet ook slechte kabels uitsluiten. 
   * Als u vermoedt dat de DATA 0-poort op de actieve controller is mislukt, u dit bevestigen door verbinding te maken met de DATA 0-poort op controller 1. Om dit te bevestigen, koppelt u de netwerkkabel los van de achterkant van het apparaat vanaf controller 0, sluit u de kabel aan op controller 1 en voert u de cmdlet Get-NetAdapter opnieuw uit.
     Als de DATA 0-poort op een controller mislukt, neemt [u contact op met Microsoft Support](storsimple-8000-contact-microsoft-support.md) voor de volgende stappen. Mogelijk moet u de controller op uw systeem vervangen.
4. Controleer de verbinding met de switch:
   
   * Zorg ervoor dat DATA 0-netwerkinterfaces op controller 0 en controller 1 in uw primaire behuizing op hetzelfde subnet staan. 
   * Controleer de hub of router. Doorgaans moet u beide controllers aansluiten op dezelfde hub of router. 
   * Zorg ervoor dat de switches die u voor de verbinding gebruikt, GEGEVENS 0 hebben voor beide controllers in hetzelfde vLAN.
5. Elimineer eventuele gebruikersfouten:
   
   * Voer de wizard Setup opnieuw uit (voer **Invoke-HcsSetupWizard**uit) en voer de waarden opnieuw in om ervoor te zorgen dat er geen fouten zijn. 
   * Controleer de gebruikte registratiesleutel. Dezelfde registratiesleutel kan worden gebruikt om meerdere apparaten aan te sluiten op een StorSimple Device Manager-service. Gebruik de procedure in [Download de serviceregistratiesleutel](storsimple-8000-manage-service.md#get-the-service-registration-key) om ervoor te zorgen dat u de juiste registratiesleutel gebruikt.
     
     > [!IMPORTANT]
     > Als u meerdere services hebt, moet u ervoor zorgen dat de registratiesleutel voor de juiste service wordt gebruikt om het apparaat te registreren. Als u een apparaat hebt geregistreerd bij de verkeerde StorSimple Device Manager-service, moet u contact opnemen [met Microsoft Support](storsimple-8000-contact-microsoft-support.md) voor de volgende stappen. Mogelijk moet u een fabrieksreset van het apparaat uitvoeren (wat kan leiden tot gegevensverlies) om het vervolgens aan te sluiten op de beoogde service.
     > 
     > 
6. Gebruik de cmdlet Test-Connection om te controleren of u verbinding hebt met het externe netwerk. Ga voor meer informatie naar [Probleem oplossen met de cmdlet Test-Connection](#troubleshoot-with-the-test-connection-cmdlet).
7. Controleer op firewallinterferentie. Als u hebt geverifieerd dat de instellingen voor virtueel IP (VIP), subnet, gateway en DNS allemaal correct zijn en u nog steeds verbindingsproblemen ziet, is het mogelijk dat uw firewall de communicatie tussen uw apparaat en het externe netwerk blokkeert. U moet ervoor zorgen dat poorten 80 en 443 beschikbaar zijn op uw StorSimple-apparaat voor uitgaande communicatie. Zie [Netwerkvereisten voor uw StorSimple-apparaat voor](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device)meer informatie.
8. Kijk naar de logboeken. Ga naar [Ondersteuningspakketten en apparaatlogboeken die beschikbaar zijn voor het oplossen van problemen.](#support-packages-and-device-logs-available-for-troubleshooting)
9. Als het probleem in de voorgaande stappen niet is opgelost, neemt [u contact op met Microsoft Support](storsimple-8000-contact-microsoft-support.md) voor hulp.

## <a name="next-steps"></a>Volgende stappen
[Meer informatie over het gebruik van het hulpprogramma Diagnostische gegevens om een StorSimple-apparaat op te lossen.](storsimple-8000-diagnostics.md)

<!--Link references-->

[1]: https://technet.microsoft.com/library/dd379547(v=ws.10).aspx
[2]: https://technet.microsoft.com/library/dd392266(v=ws.10).aspx 
