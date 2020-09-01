---
title: Controle lijst voor implementaties voor het implementeren van Azure Stack edge-apparaat | Microsoft Docs
description: In dit artikel worden de gegevens beschreven die kunnen worden verzameld voordat u uw Azure Stack edge-apparaat implementeert.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/26/2020
ms.author: alkohli
ms.openlocfilehash: 065a6595ba89e00310ff41cd63b929f6d6d83222
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2020
ms.locfileid: "89084468"
---
# <a name="deployment-checklist-for-your-azure-stack-edge-device"></a>Controle lijst voor implementaties voor uw Azure Stack edge-apparaat  

In dit artikel wordt beschreven welke informatie kan worden verzameld vóór de werkelijke implementatie van uw Azure Stack edge-apparaat. 

Gebruik de volgende controle lijst om ervoor te zorgen dat u deze informatie hebt nadat u een bestelling voor een Azure Stack edge-apparaat hebt geplaatst en voordat u het apparaat hebt ontvangen. 

## <a name="deployment-checklist"></a>Controle lijst voor implementatie 

| Fase                             | Parameter                                                                                                                                                                                                                           | Details                                                                                                           |
|-----------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| Apparaatbeheer               | <li>Microsoft Azure abonnement</li><li>Azure Active Directory Graph API</li><li>Microsoft Azure Storage-account</li>|<li>Inzender machtigingen zijn ingeschakeld voor Azure Stack EDGE/Data Box Gateway</li><li>Zorg ervoor dat de beheerder of de gebruiker toegang heeft</li><li>Toegangs referenties vereist</li> |
| Installatie van apparaat               | Energie kabels in het pakket. <br>Voor ons werd een SVE 18/3-kabel met een nominale ingang van 125 V en 15 ampère met een NEMA 5-15P naar C13 (input to output) verzonden.                                                                                                                                                                                                          | Meegeleverd met het apparaat.<br>Zie de lijst met [ondersteunde stroom kabels per land](azure-stack-edge-technical-specifications-power-cords-regional.md) voor meer informatie.                                                                                        |
|                                   | <li>Ten minste 1 X 1-GbE RJ-45-netwerk kabel voor poort 1 </li><li> Ten minste 1 X 25 GbE SFP + koper kabel voor poort 3, poort 4, poort 5 of poort 6</li>| De klant moet deze kabels aanschaffen.<br>Zie voor een volledige lijst met ondersteunde netwerk kabels, switches en transceivers voor netwerk kaarten van de [Cavium 41000 FastlinQ-serie.](https://www.marvell.com/documents/xalflardzafh32cfvi0z/)                                                                          | 
| Eerste keer dat het apparaat is verbonden      | Poort 1 heeft een vast IP-adres (192.168.100.10/24) voor de eerste verbinding. <li>Een laptop vereisen voor directe verbinding met poort 1, met een IP-adres op 192.168.100.0/24-netwerk.</li><li> Lokale gebruikers interface van het apparaat gebruiken op: `https://192.168.100.10` voor verdere configuratie.</li>|                                                                                                                   |
| Aanmelding met apparaat                      | Het beheerders wachtwoord van het apparaat moet tussen 8 en 16 tekens lang zijn. <br>Het wachtwoord moet drie van de volgende tekens bevatten: kleine letters, hoofdletters, cijfers en speciale tekens.                                            | Het standaard wachtwoord is *Wachtwoord1* die verloopt bij de eerste aanmelding.                                                     |
| Netwerkinstellingen                  | Het apparaat wordt geleverd met 2 x 1 GbE, 4 x 25 GbE-netwerk poorten. <li>Poort 1 wordt alleen gebruikt voor het configureren van beheer instellingen. Een of meer gegevens poorten kunnen worden verbonden en geconfigureerd. </li><li> Ten minste één gegevens netwerk interface van poort 2-poort 6 moet worden verbonden met internet (met verbinding met Azure).</li><li> IP-instellingen ondersteunen DHCP/statische IPv4-configuratie. | Voor de statische IPv4-configuratie zijn IP-, DNS-server en standaard gateway vereist.                                                                                                                  |
| Berekenings netwerk instellingen     | <li>2 statische open bare Ip's vereisen voor Kubernetes-knoop punten en ten minste één statisch IP-adres voor Azure Stack Edge hub-service voor toegang tot Compute-modules.</li><li>Een IP-adres vereisen voor elke extra service of container die extern toegankelijk moet zijn van buiten het Kubernetes-cluster.</li>                                                                                                                       | Alleen statische IPv4-configuratie wordt ondersteund.                                                                      |
| Beschrijving Webproxy-instellingen     | <li>IP/FQDN-naam van webproxyserver, poort </li><li>Gebruikers naam webproxy, wacht woord</li>                                                                                                                                                                                                    | Wordt momenteel niet ondersteund met Compute Setup.                                                                     |
| Firewall-en poort instellingen        | Gebruik de [weer gegeven url's en de poorten](azure-stack-edge-system-requirements.md#networking-port-requirements) die zijn toegestaan voor IP-adressen van apparaten.                                                                                                                                                  |                                                                                                                   |
| Beschrijving MAC-adres            | Als het MAC-adres moet worden white list, haalt u het adres van de verbonden poort op uit de lokale gebruikers interface van het apparaat. |                                                                                                                   |
| Beschrijving Netwerk switch poort    | Het apparaat fungeert als host voor Hyper-V-Vm's voor compute. Sommige netwerk switch poort configuraties voldoen niet aan deze standaard instellingen.                                                                                                        |                                                                                                                   |
| Aanbevelingen Tijd instellingen       | Configureer tijd zone, primaire NTP-server, secundaire NTP-server.                                                                                                                                                                    | Configureer de primaire en secundaire NTP-server in het lokale netwerk.<br>Als de lokale server niet beschikbaar is, kunnen open bare NTP-servers worden geconfigureerd.                                                    |
| Beschrijving Server instellingen bijwerken | <li>Het IP-adres van de update server vereisen op het lokale netwerk, het pad naar de WSUS-server. </li> | Standaard wordt de open bare Windows Update-Server gebruikt.|
| Apparaatinstellingen                   | <li>De apparaatnaam die in de DNS-organisatie is geregistreerd </li><li>DNS-domein</li> |                                                                                                                   |
| Beschrijving Bewijzen                      | De door het apparaat gegenereerde certificaten gebruiken <br><br> Als u uw eigen certificaten wilt overbrengen, hebt u het volgende nodig: <li>Vertrouwd basis handtekening certificaat in de vorm van met *. CER* </li><li>Eindpunt certificaten in *PFX* -indeling</li>|Eindpunt certificaten zijn voor Azure Resource Manager, Blob Storage, lokale webgebruikersinterface.                                                                                                                   |
| Activering                        | De activerings sleutel van de Azure Stack EDGE/Data Box Gateway resource is vereist.                                                                                                                                                       | Nadat de sleutel is gegenereerd, verloopt deze over drie dagen.                                                                        |


## <a name="next-steps"></a>Volgende stappen

Bereid u voor op het implementeren van uw [Azure stack edge-apparaat](azure-stack-edge-gpu-deploy-prep.md).



