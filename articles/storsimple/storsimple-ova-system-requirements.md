---
title: Microsoft Azure StorSimple Virtual Array-systeemvereisten
description: Meer informatie over de software- en netwerkvereisten voor uw StorSimple Virtual Array
author: alkohli
ms.assetid: ea1d3bca-e71b-453d-aa82-440d2638f5e3
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 020208a8b67d248c02fc659d4dc48fa22d333839
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298810"
---
# <a name="storsimple-virtual-array-system-requirements"></a>Systeemvereisten voor StorSimple virtuele array

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

## <a name="overview"></a>Overzicht

In dit artikel worden de belangrijke systeemvereisten beschreven voor uw Microsoft Azure StorSimple Virtual Array en voor de opslagclients die toegang hebben tot de array. We raden u aan de informatie zorgvuldig te bekijken voordat u uw StorSimple-systeem implementeert en deze vervolgens zo nodig terugte verwijzen naar de informatie tijdens de implementatie en de daaropvolgende bewerking.

De systeemvereisten omvatten:

* **Softwarevereisten voor opslagclients** - beschrijft de ondersteunde virtualisatieplatforms, webbrowsers, iSCSI-initiators, MKB-clients, minimale vereisten voor virtuele apparaten en eventuele aanvullende vereisten voor deze besturingssystemen.
* **Netwerkvereisten voor het StorSimple-apparaat** bieden informatie over de poorten die in uw firewall moeten worden geopend om iSCSI-, cloud- of beheerverkeer mogelijk te maken.

De StorSimple systeemvereisten informatie gepubliceerd in dit artikel is alleen van toepassing op StorSimple Virtual Arrays.

* Ga voor apparaten uit de [StorSimple 8000-serie naar systeemvereisten voor uw StorSimple 8000-serie.](storsimple-system-requirements.md)
* Voor apparaten uit de 7000-serie gaat u naar [systeemvereisten voor uw StorSimple 5000-7000-serie apparaat.](http://onlinehelp.storsimple.com/1_StorSimple_System_Requirements)

## <a name="software-requirements"></a>Softwarevereisten
De softwarevereisten omvatten de informatie over de ondersteunde webbrowsers, SMB-versies, virtualisatieplatforms en de minimale vereisten voor virtuele apparaten.

### <a name="supported-virtualization-platforms"></a>Ondersteunde virtualisatieplatforms
| **Hypervisor** | **Versie** |
| --- | --- |
| Hyper-V |Windows Server 2008 R2 SP1 en hoger |
| VMware ESXi |5.0, 5.5, 6.0, en 6.5. |

> [!IMPORTANT]
> Installeer geen VMware-hulpprogramma's op uw StorSimple Virtual Array. dit zal resulteren in een niet-ondersteunde configuratie.

### <a name="virtual-device-requirements"></a>Vereisten voor virtuele apparaten
| **Component** | **Vereiste** |
| --- | --- |
| Minimumaantal virtuele processors (cores) |4 |
| Minimaal geheugen (RAM) |8 GB <br> Voor een bestandsserver, 8 GB voor minder dan 2 miljoen bestanden en 16 GB voor 2 - 4 miljoen bestanden|
| Schijfruimte<sup>1</sup> |OS-schijf - 80 GB <br></br>Gegevensschijf - 500 GB tot 8 TB |
| Minimumaantal netwerkinterface(s) |1 |
| Internetbandbreedte<sup>2</sup> |Minimale bandbreedte vereist: 5 Mbps <br> Aanbevolen bandbreedte: 100 Mbps <br> De snelheid van gegevensoverdracht schaalt met de internetbandbreedte. Bijvoorbeeld, 100 GB aan gegevens duurt 2 dagen om over te dragen op 5 Mbps, wat kan leiden tot back-upfouten, omdat dagelijkse back-ups niet zou worden voltooid in een dag. Met een bandbreedte van 100 Mbps kan 100 GB aan gegevens in 2,5 uur worden overgedragen.   |

<sup>1</sup> - Dun ingericht

<sup>2</sup> - Netwerkvereisten kunnen variëren afhankelijk van de dagelijkse gegevenswijzigingssnelheid. Als een apparaat bijvoorbeeld een back-up van 10 GB of meer per dag moet maken, kan de dagelijkse back-up over een verbinding van 5 Mbps tot 4,25 uur duren (als de gegevens niet kunnen worden gecomprimeerd of gededuplicate).

### <a name="supported-web-browsers"></a>Ondersteunde webbrowsers
| **Component** | **Versie** | **Aanvullende vereisten/notities** |
| --- | --- | --- |
| Microsoft Edge |Nieuwste versie | |
| Internet Explorer |Nieuwste versie |Getest met Internet Explorer 11 |
| Google Chrome |Nieuwste versie |Getest met Chrome 46 |

### <a name="supported-storage-clients"></a>Ondersteunde opslagclients
De volgende softwarevereisten zijn voor de iSCSI-initiators die toegang hebben tot uw StorSimple Virtual Array (geconfigureerd als iSCSI-server).

| **Ondersteunde besturingssystemen** | **Versie vereist** | **Aanvullende vereisten/notities** |
| --- | --- | --- |
| Windows Server |2008R2 SP1, 2012, 2012R2 |StorSimple kan dun ingerichte en volledig ingerichte volumes maken. Het kan geen gedeeltelijk ingerichte volumes maken. StorSimple iSCSI-volumes worden alleen ondersteund voor: <ul><li>Eenvoudige volumes op Windows basisschijven.</li><li>Windows NTFS voor het opmaken van een volume.</li> |

De volgende softwarevereisten zijn voor de SMB-clients die toegang hebben tot uw StorSimple Virtual Array (geconfigureerd als bestandsserver).

| **SMB-versie** |
| --- |
| SMB 2.x |
| SMB 3.0 |
| SMB 3,02 |

> [!IMPORTANT]
> Kopieer of sla geen bestanden die zijn beveiligd door EFS (Windows Encrypting File System) niet naar de StorSimple Virtual Array-bestandsserver. dit zal resulteren in een niet-ondersteunde configuratie.


### <a name="supported-storage-format"></a>Ondersteunde opslagindeling
Alleen de Azure block blob-opslag wordt ondersteund. Paginablobs worden niet ondersteund. Meer informatie [over blokblobs en paginablobs](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).

## <a name="networking-requirements"></a>Netwerkvereisten
In de volgende tabel worden de poorten weergegeven die in uw firewall moeten worden geopend om iSCSI-, SMB-, cloud- of beheerverkeer mogelijk te maken. In deze tabel verwijst *in* of *inbound* naar de richting van waaruit binnenkomende clientaanvragen toegang krijgen tot uw apparaat. *Out-* of *outbound* verwijst naar de richting waarin uw StorSimple-apparaat gegevens extern verzendt, buiten de implementatie: bijvoorbeeld uitgaand naar het internet.

| **Poort<sup>nr.</sup>** | **In of uit** | **Poortbereik** | **Vereist** | **Opmerkingen** |
| --- | --- | --- | --- | --- |
| TCP 80 (HTTP) |Uit |WAN |Nee |Uitgaande poort wordt gebruikt voor internettoegang om updates op te halen. <br></br>De uitgaande webproxy is configureerbaar voor de gebruiker. |
| TCP 443 (HTTPS) |Uit |WAN |Ja |Uitgaande poort wordt gebruikt voor toegang tot gegevens in de cloud. <br></br>De uitgaande webproxy is configureerbaar voor de gebruiker. |
| UDP 53 (DNS) |Uit |WAN |In sommige gevallen; zie notities. |Deze poort is alleen vereist als u een dns-server op internet gebruikt. <br></br> Houd er rekening mee dat als u een bestandsserver implementeert, we u aanraden lokale DNS-server te gebruiken. |
| UDP 123 (NTP) |Uit |WAN |In sommige gevallen; zie notities. |Deze poort is alleen vereist als u een NTP-server op internet gebruikt.<br></br> Als u een bestandsserver implementeert, raden we u aan de tijd te synchroniseren met uw Active Directory-domeincontrollers. |
| TCP 80 (HTTP) |In |LAN |Ja |Dit is de inkomende poort voor lokale gebruikersinterface op het StorSimple-apparaat voor lokaal beheer. <br></br> Houd er rekening mee dat de toegang tot de lokale gebruikersinterface via HTTP automatisch wordt omgeleid naar HTTPS. |
| TCP 443 (HTTPS) |In |LAN |Ja |Dit is de inkomende poort voor lokale gebruikersinterface op het StorSimple-apparaat voor lokaal beheer. |
| TCP 3260 (iSCSI) |In |LAN |Nee |Deze poort wordt gebruikt om toegang te krijgen tot gegevens via iSCSI. |

<sup>1</sup> Er hoeven geen inkomende poorten te worden geopend op het openbare internet.

> [!IMPORTANT]
> Zorg ervoor dat de firewall geen TLS-verkeer tussen het StorSimple-apparaat en Azure wijzigt of decodeert.
> 
> 

### <a name="url-patterns-for-firewall-rules"></a>URL-patronen voor firewallregels
Netwerkbeheerders kunnen vaak geavanceerde firewallregels configureren op basis van de URL-patronen om het binnenkomende en het uitgaande verkeer te filteren. Uw virtuele array en de StorSimple Device Manager-service zijn afhankelijk van andere Microsoft-toepassingen, zoals Azure Service Bus, Azure Active Directory Access Control, opslagaccounts en Microsoft Update-servers. De URL-patronen die aan deze toepassingen zijn gekoppeld, kunnen worden gebruikt om firewallregels te configureren. Het is belangrijk om te begrijpen dat de URL-patronen die aan deze toepassingen zijn gekoppeld, kunnen veranderen. Dit vereist op zijn beurt dat de netwerkbeheerder firewallregels voor uw StorSimple controleert en bijwerkt wanneer dat nodig is. 

We raden u aan uw firewallregels voor uitgaand verkeer in te stellen, op basis van vaste IP-adressen van StorSimple, in de meeste gevallen. U de onderstaande informatie echter gebruiken om geavanceerde firewallregels in te stellen die nodig zijn om beveiligde omgevingen te maken.

> [!NOTE]
> 
> * De IP's van het apparaat (bron) moeten altijd worden ingesteld op alle netwerkinterfaces met cloudvoorziening. 
> * De ip-bereiken van de bestemming moeten worden ingesteld op [IP-bereiken voor Azure-datacenters.](https://www.microsoft.com/download/confirmation.aspx?id=41653)
> 
> 

| URL-patroon | Component/functionaliteit |
| --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*` <br>`https://login.windows.net`|StorSimple-apparaatbeheerfunctie<br>Toegangscontroleservice<br>Azure Service Bus<br>Verificatieservice|
| `http://*.backup.windowsazure.com` |Apparaatregistratie |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Intrekking van certificaat |
| `https://*.core.windows.net/*`<br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Azure-opslagaccounts en -bewaking |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Microsoft Update-servers<br> |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |
| `https://*.partners.extranet.microsoft.com/*` |Ondersteuningspakket |
| `https://*.data.microsoft.com` |Telemetrieservice in Windows, zie de [update voor klantervaring en diagnostische telemetrie](https://support.microsoft.com/en-us/kb/3068708) |

## <a name="next-steps"></a>Volgende stappen
* [Bereid de portal voor om uw StorSimple Virtual Array te implementeren](storsimple-virtual-array-deploy1-portal-prep.md)
