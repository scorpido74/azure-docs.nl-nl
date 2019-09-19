---
title: Problemen met Azure Point-to-site-verbindingen oplossen | Microsoft Docs
description: Meer informatie over het oplossen van problemen met Point-to-site-verbindingen.
services: vpn-gateway
documentationcenter: na
author: chadmath
manager: dcscontentpm
editor: ''
tags: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: genli
ms.openlocfilehash: 0a32f9a9fde0983a5b97f7342a111d40ef01c686
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104822"
---
# <a name="troubleshooting-azure-point-to-site-connection-problems"></a>Meer Problemen met de verbinding met Azure Point-to-site

Dit artikel bevat een lijst met veelvoorkomende punt-naar-site-verbindings problemen die u kunt voordoen. Daarnaast worden mogelijke oorzaken en oplossingen voor deze problemen besproken.

## <a name="vpn-client-error-a-certificate-could-not-be-found"></a>VPN-client fout: Kan geen certificaat vinden

### <a name="symptom"></a>Symptoom

Wanneer u probeert verbinding te maken met een virtueel Azure-netwerk met behulp van de VPN-client, wordt het volgende fout bericht weer gegeven:

**Er is geen certificaat gevonden dat kan worden gebruikt met dit Extensible Authentication Protocol. (Fout 798)**

### <a name="cause"></a>Oorzaak

Dit probleem treedt op als het client certificaat ontbreekt in **Certificaten-Huidige gebruiker\Persoonlijk\Certificaten**.

### <a name="solution"></a>Oplossing

Voer de volgende stappen uit om dit probleem op te lossen:

1. Certificaat beheer openen: Klik op **Start**, typ **computer certificaten beheren**en klik vervolgens op **computer certificaten beheren** in het Zoek resultaat.

2. Zorg ervoor dat de volgende certificaten zich op de juiste locatie bevinden:

    | Certificaat | Location |
    | ------------- | ------------- |
    | AzureClient.pfx  | Huidige Gebruiker\persoonlijk\certificaten |
    | AzureRoot. CER    | Lokale Computer\Trusted basis certificerings instanties|

3. Ga naar C:\Users\<username > \AppData\Roaming\Microsoft\Network\Connections\Cm\<GUID >, installeer het certificaat (CER-bestand) hand matig op de opslag van de gebruiker en de computer.

Zie [certificaten voor punt-naar-site-verbindingen genereren en exporteren](vpn-gateway-certificates-point-to-site.md)voor meer informatie over het installeren van het client certificaat.

> [!NOTE]
> Wanneer u het client certificaat importeert, moet u niet de optie **zware persoonlijke sleutel beveiliging inschakelen** selecteren.

## <a name="the-network-connection-between-your-computer-and-the-vpn-server-could-not-be-established-because-the-remote-server-is-not-responding"></a>De netwerk verbinding tussen uw computer en de VPN-server kan niet tot stand worden gebracht omdat de externe server niet reageert

### <a name="symptom"></a>Symptoom

Wanneer u probeert en verbinding maakt met een virtuele Azure-netwerk gateway met behulp van IKEv2 in Windows, wordt het volgende fout bericht weer gegeven:

**De netwerk verbinding tussen uw computer en de VPN-server kan niet tot stand worden gebracht omdat de externe server niet reageert**

### <a name="cause"></a>Oorzaak
 
 Het probleem treedt op als de versie van Windows geen ondersteuning biedt voor IKE-fragmentatie
 
### <a name="solution"></a>Oplossing

IKEv2 wordt ondersteund op Windows 10 en Server 2016. Als u IKEv2 wilt gebruiken, moet u lokaal updates installeren en een registersleutelwaarde instellen. Besturingssysteemversies voor Windows 10 worden niet ondersteund en kunnen alleen SSTP gebruiken.

Windows 10 of Server 2016 voorbereiden voor IKEv2:

1. Installeer de update.

   | Versie van het besturingssysteem | Date | Aantal/koppeling |
   |---|---|---|---|
   | Windows Server 2016<br>Windows 10 versie 1607 | 17 januari 2018 | [KB4057142](https://support.microsoft.com/help/4057142/windows-10-update-kb4057142) |
   | Windows 10 versie 1703 | 17 januari 2018 | [KB4057144](https://support.microsoft.com/help/4057144/windows-10-update-kb4057144) |
   | Windows 10 Version 1709 | 22 maart 2018 | [KB4089848](https://www.catalog.update.microsoft.com/search.aspx?q=kb4089848) |
   |  |  |  |  |

2. De registersleutelwaarde instellen. Maak of stel `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\RasMan\ IKEv2\DisableCertReqPayload` de REG_DWORD-sleutel in het REGI ster in op 1.

## <a name="vpn-client-error-the-message-received-was-unexpected-or-badly-formatted"></a>VPN-client fout: Het ontvangen bericht is onverwacht of onjuist opgemaakt

### <a name="symptom"></a>Symptoom

Wanneer u probeert verbinding te maken met een virtueel Azure-netwerk met behulp van de VPN-client, wordt het volgende fout bericht weer gegeven:

**Het ontvangen bericht is onverwacht of onjuist opgemaakt. (Fout 0x80090326)**

### <a name="cause"></a>Oorzaak

Dit probleem treedt op als een van de volgende voor waarden van toepassing is:

- Het gebruik van door de gebruiker gedefinieerde routes (UDR) met de standaard route op het gateway-subnet is onjuist ingesteld.
- De open bare sleutel van het basis certificaat is niet geüpload naar de Azure VPN-gateway. 
- De sleutel is beschadigd of verlopen.

### <a name="solution"></a>Oplossing

Voer de volgende stappen uit om dit probleem op te lossen:

1. Verwijder UDR in het gateway-subnet. Zorg ervoor dat UDR alle verkeer correct stuurt.
2. Controleer de status van het basis certificaat in de Azure Portal om te zien of het is ingetrokken. Als deze niet is ingetrokken, probeert u het basis certificaat te verwijderen en opnieuw te uploaden. Zie [certificaten maken](vpn-gateway-howto-point-to-site-classic-azure-portal.md#generatecerts)voor meer informatie.

## <a name="vpn-client-error-a-certificate-chain-processed-but-terminated"></a>VPN-client fout: Er is een certificaat keten verwerkt, maar beëindigd 

### <a name="symptom"></a>Symptoom 

Wanneer u probeert verbinding te maken met een virtueel Azure-netwerk met behulp van de VPN-client, wordt het volgende fout bericht weer gegeven:

**Er is een certificaat keten verwerkt, maar deze is beëindigd in een basis certificaat dat niet wordt vertrouwd door de vertrouwens provider.**

### <a name="solution"></a>Oplossing

1. Zorg ervoor dat de volgende certificaten zich op de juiste locatie bevinden:

    | Certificaat | Location |
    | ------------- | ------------- |
    | AzureClient.pfx  | Huidige Gebruiker\persoonlijk\certificaten |
    | Azuregateway-*GUID*.cloudapp.net  | Huidige User\Trusted basis certificerings instanties|
    | AzureGateway-*GUID*.cloudapp.net, AzureRoot.cer    | Lokale Computer\Trusted basis certificerings instanties|

2. Als de certificaten zich al op de locatie bevinden, probeert u de certificaten te verwijderen en opnieuw te installeren. Het **azuregateway-*GUID*. cloudapp.net-** certificaat bevindt zich in het configuratie pakket voor de VPN-client dat u hebt gedownload van de Azure Portal. U kunt bestands archiverers gebruiken om de bestanden uit het pakket uit te pakken.

## <a name="file-download-error-target-uri-is-not-specified"></a>Fout bij downloaden van bestand: De doel-URI is niet opgegeven

### <a name="symptom"></a>Symptoom

Het volgende fout bericht wordt weer gegeven:

**Fout bij downloaden van bestand. De doel-URI is niet opgegeven.**

### <a name="cause"></a>Oorzaak 

Dit probleem treedt op vanwege een onjuist gateway type. 

### <a name="solution"></a>Oplossing

Het VPN-gateway type moet **VPN**zijn en het VPN-type moet **RouteBased**zijn.

## <a name="vpn-client-error-azure-vpn-custom-script-failed"></a>VPN-client fout: Het aangepaste Azure VPN-script is mislukt 

### <a name="symptom"></a>Symptoom

Wanneer u probeert verbinding te maken met een virtueel Azure-netwerk met behulp van de VPN-client, wordt het volgende fout bericht weer gegeven:

**Het aangepaste script (voor het bijwerken van de routerings tabel) is mislukt. (Error 8007026f)**

### <a name="cause"></a>Oorzaak

Dit probleem kan zich voordoen als u de site-naar-punt-VPN-verbinding probeert te openen met behulp van een snelkoppeling.

### <a name="solution"></a>Oplossing 

Open het VPN-pakket rechtstreeks in plaats van het te openen via de snelkoppeling.

## <a name="cannot-install-the-vpn-client"></a>De VPN-client kan niet worden geïnstalleerd

### <a name="cause"></a>Oorzaak 

Er is een aanvullend certificaat vereist om de VPN-gateway voor uw virtuele netwerk te vertrouwen. Het certificaat is opgenomen in het configuratie pakket voor de VPN-client dat is gegenereerd op basis van de Azure Portal.

### <a name="solution"></a>Oplossing

Pak het configuratie pakket voor de VPN-client uit en zoek het CER-bestand. Voer de volgende stappen uit om het certificaat te installeren:

1. Open MMC. exe.
2. Voeg de module **certificaten** toe.
3. Selecteer het **computer** account voor de lokale computer.
4. Klik met de rechter muisknop op het knoop punt **vertrouwde basis certificerings instanties** . Klik op **alle taken** > **importeren**en blader naar het CER-bestand dat u hebt geëxtraheerd uit het configuratie pakket voor de VPN-client.
5. Start de computer opnieuw op. 
6. Probeer de VPN-client te installeren.

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-data-is-invalid"></a>Azure Portal fout: De VPN-gateway is niet opgeslagen en de gegevens zijn ongeldig

### <a name="symptom"></a>Symptoom

Wanneer u probeert de wijzigingen voor de VPN-gateway op te slaan in de Azure Portal, wordt het volgende fout bericht weer gegeven:

**Kan de gateway *naam*&gt;van de &lt;virtuele netwerk gateway niet opslaan. De gegevens voor &lt;de certificaat *certificaat-id* &gt; zijn ongeldig.**

### <a name="cause"></a>Oorzaak 

Dit probleem kan optreden als de open bare sleutel van het basis certificaat die u hebt geüpload, een ongeldig teken bevat, zoals een spatie.

### <a name="solution"></a>Oplossing

Zorg ervoor dat de gegevens in het certificaat geen ongeldige tekens bevatten, zoals regel einden (carriage returns). De gehele waarde moet één lange regel zijn. De volgende tekst is een voor beeld van het certificaat:

    -----BEGIN CERTIFICATE-----
    MIIC5zCCAc+gAwIBAgIQFSwsLuUrCIdHwI3hzJbdBjANBgkqhkiG9w0BAQsFADAW
    MRQwEgYDVQQDDAtQMlNSb290Q2VydDAeFw0xNzA2MTUwMjU4NDZaFw0xODA2MTUw
    MzE4NDZaMBYxFDASBgNVBAMMC1AyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEF
    AAOCAQ8AMIIBCgKCAQEAz8QUCWCxxxTrxF5yc5uUpL/bzwC5zZ804ltB1NpPa/PI
    sa5uwLw/YFb8XG/JCWxUJpUzS/kHUKFluqkY80U+fAmRmTEMq5wcaMhp3wRfeq+1
    G9OPBNTyqpnHe+i54QAnj1DjsHXXNL4AL1N8/TSzYTm7dkiq+EAIyRRMrZlYwije
    407ChxIp0stB84MtMShhyoSm2hgl+3zfwuaGXoJQwWiXh715kMHVTSj9zFechYd7
    5OLltoRRDyyxsf0qweTFKIgFj13Hn/bq/UJG3AcyQNvlCv1HwQnXO+hckVBB29wE
    sF8QSYk2MMGimPDYYt4ZM5tmYLxxxvGmrGhc+HWXzMeQIDAQABozEwLzAOBgNVHQ8B
    Af8EBAMCAgQwHQYDVR0OBBYEFBE9zZWhQftVLBQNATC/LHLvMb0OMA0GCSqGSIb3
    DQEBCwUAA4IBAQB7k0ySFUQu72sfj3BdNxrXSyOT4L2rADLhxxxiK0U6gHUF6eWz
    /0h6y4mNkg3NgLT3j/WclqzHXZruhWAXSF+VbAGkwcKA99xGWOcUJ+vKVYL/kDja
    gaZrxHlhTYVVmwn4F7DWhteFqhzZ89/W9Mv6p180AimF96qDU8Ez8t860HQaFkU6
    2Nw9ZMsGkvLePZZi78yVBDCWMogBMhrRVXG/xQkBajgvL5syLwFBo2kWGdC+wyWY
    U/Z+EK9UuHnn3Hkq/vXEzRVsYuaxchta0X2UNRzRq+o706l+iyLTpe6fnvW6ilOi
    e8Jcej7mzunzyjz4chN0/WVF94MtxbUkLkqP
    -----END CERTIFICATE-----

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-resource-name-is-invalid"></a>Azure Portal fout: Het opslaan van de VPN-gateway is mislukt en de resource naam is ongeldig

### <a name="symptom"></a>Symptoom

Wanneer u probeert de wijzigingen voor de VPN-gateway op te slaan in de Azure Portal, wordt het volgende fout bericht weer gegeven: 

**Kan de gateway *naam*&gt;van de &lt;virtuele netwerk gateway niet opslaan. De naam &lt;van het resource naam *certificaat dat u wilt uploaden* &gt; , is ongeldig**.

### <a name="cause"></a>Oorzaak

Dit probleem treedt op omdat de naam van het certificaat een ongeldig teken bevat, zoals een spatie. 

## <a name="azure-portal-error-vpn-package-file-download-error-503"></a>Azure Portal fout: Fout bij downloaden van VPN-pakket bestand 503

### <a name="symptom"></a>Symptoom

Wanneer u het configuratie pakket voor de VPN-client probeert te downloaden, wordt het volgende fout bericht weer gegeven:

**Kan het bestand niet downloaden. Fout Details: fout 503. De server is bezet.**
 
### <a name="solution"></a>Oplossing

Deze fout kan worden veroorzaakt door een tijdelijk netwerk probleem. Probeer het VPN-pakket na een paar minuten opnieuw te downloaden.

## <a name="azure-vpn-gateway-upgrade-all-point-to-site-clients-are-unable-to-connect"></a>Upgrade van Azure VPN Gateway: Alle Point-to-site-clients kunnen geen verbinding maken

### <a name="cause"></a>Oorzaak

Als het certificaat langer is dan 50 procent gedurende de levens duur, wordt het certificaat doorgevoerd.

### <a name="solution"></a>Oplossing

U lost dit probleem op door het punt opnieuw te downloaden en te implementeren op een site pakket op alle clients.

## <a name="too-many-vpn-clients-connected-at-once"></a>Er zijn te veel VPN-clients tegelijk verbonden

Het maximum aantal toegestane verbindingen is bereikt. U kunt het totale aantal verbonden clients bekijken in de Azure Portal.

## <a name="point-to-site-vpn-incorrectly-adds-a-route-for-100008-to-the-route-table"></a>Punt-naar-site-VPN voegt op onjuiste wijze een route voor 10.0.0.0/8 toe aan de route tabel

### <a name="symptom"></a>Symptoom

Wanneer u de VPN-verbinding op de punt-naar-site-client maakt, moet de VPN-client een route toevoegen naar het virtuele netwerk van Azure. De IP-Helper-service moet een route toevoegen voor het subnet van de VPN-clients. 

Het bereik van de VPN-client hoort bij een kleiner subnet van 10.0.0.0/8, zoals 10.0.12.0/24. In plaats van een route voor 10.0.12.0/24 wordt een route voor 10.0.0.0/8 toegevoegd met een hogere prioriteit. 

Deze onjuiste route verbreekt de connectiviteit met andere on-premises netwerken die tot een ander subnet binnen het bereik 10.0.0.0/8 behoren, zoals 10.50.0.0/24, waarvoor geen specifieke route is gedefinieerd. 

### <a name="cause"></a>Oorzaak

Dit gedrag is inherent aan het ontwerp voor Windows-clients. Wanneer de client het PPP IPCP-protocol gebruikt, wordt het IP-adres voor de tunnel interface opgehaald van de server (de VPN-gateway in dit geval). Vanwege een beperking in het protocol heeft de client echter niet het subnetmasker. Omdat er geen andere manier is om deze op te halen, probeert de client het subnetmasker te raden op basis van de klasse van het IP-adres van de tunnel interface. 

Daarom wordt er een route toegevoegd op basis van de volgende statische toewijzing: 

Als het adres van klasse A hoort--> Toep assen/8

Als het adres behoort tot klasse B--> Toep assen/16

Als het adres behoort tot klasse C--> Toep assen/24

### <a name="solution"></a>Oplossing

Routes voor andere netwerken worden geïnjecteerd in de routerings tabel met de langste voorvoegsel overeenkomst of een lagere metrische waarde (dus hogere prioriteit) dan het punt naar de site. 

## <a name="vpn-client-cannot-access-network-file-shares"></a>VPN-client kan geen toegang krijgen tot netwerk bestands shares

### <a name="symptom"></a>Symptoom

De VPN-client is verbonden met het virtuele Azure-netwerk. De client heeft echter geen toegang tot netwerk shares.

### <a name="cause"></a>Oorzaak

Het SMB-protocol wordt gebruikt voor toegang tot bestands shares. Wanneer de verbinding wordt gestart, voegt de VPN-client de referenties van de sessie toe en treedt de fout op. Nadat de verbinding tot stand is gebracht, wordt de client gedwongen de cache referenties voor Kerberos-verificatie te gebruiken. Dit proces initieert query's naar de Key Distribution Center (een domein controller) om een token op te halen. Omdat de client verbinding maakt via internet, kan het zijn dat de domein controller niet kan worden bereikt. Daarom kan de client geen failover uitvoeren van Kerberos naar NTLM. 

Het enige tijdstip waarop de client wordt gevraagd om een referentie, is wanneer deze een geldig certificaat (met SAN = UPN) heeft dat is uitgegeven door het domein waaraan het is gekoppeld. De client moet ook fysiek zijn verbonden met het domein netwerk. In dit geval probeert de client het certificaat te gebruiken en de domein controller te bereiken. Vervolgens retourneert de Key Distribution Center de fout ' KDC_ERR_C_PRINCIPAL_UNKNOWN '. Failover wordt uitgevoerd voor de client. 

### <a name="solution"></a>Oplossing

U kunt het probleem omzeilen door het in cache opslaan van domein referenties uit de volgende registersubsleutel uit te scha kelen: 

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\DisableDomainCreds - Set the value to 1 


## <a name="cannot-find-the-point-to-site-vpn-connection-in-windows-after-reinstalling-the-vpn-client"></a>Kan de punt-naar-site-VPN-verbinding niet vinden in Windows nadat de VPN-client opnieuw is geïnstalleerd

### <a name="symptom"></a>Symptoom

U verwijdert de punt-naar-site-VPN-verbinding en installeert vervolgens de VPN-client opnieuw. In dit geval is de VPN-verbinding niet geconfigureerd. De VPN-verbinding wordt niet weer geven in de instellingen voor **netwerk verbindingen** in Windows.

### <a name="solution"></a>Oplossing

Om het probleem op te lossen, verwijdert u de oude VPN-client configuratie bestanden van **\<C:\Users\UserName\AppData\Roaming\Microsoft\Network\Connections VirtualNetworkId >** en voert u het installatie programma voor de VPN-client opnieuw uit.

## <a name="point-to-site-vpn-client-cannot-resolve-the-fqdn-of-the-resources-in-the-local-domain"></a>Punt-naar-site-VPN-client kan de FQDN van de bronnen in het lokale domein niet omzetten

### <a name="symptom"></a>Symptoom

Wanneer de client verbinding maakt met Azure via een punt-naar-site-VPN-verbinding, kan de FQDN-naam van de resources in uw lokale domein niet worden omgezet.

### <a name="cause"></a>Oorzaak

Punt-naar-site-VPN-client maakt gebruik van Azure DNS servers die zijn geconfigureerd in het virtuele Azure-netwerk. De Azure DNS-servers hebben voor rang op de lokale DNS-servers die zijn geconfigureerd in de client, zodat alle DNS-query's naar de Azure DNS-servers worden verzonden. Als de Azure DNS servers niet over de records voor de lokale resources beschikken, mislukt de query.

### <a name="solution"></a>Oplossing

Om het probleem op te lossen, moet u ervoor zorgen dat de Azure DNS servers die in het virtuele netwerk van Azure worden gebruikt, de DNS-records voor lokale bronnen kunnen omzetten. Hiervoor kunt u DNS-doorstuur servers of voorwaardelijke doorstuur servers gebruiken. Zie [naam omzetting met uw eigen DNS-server](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) voor meer informatie.

## <a name="the-point-to-site-vpn-connection-is-established-but-you-still-cannot-connect-to-azure-resources"></a>De punt-naar-site-VPN-verbinding is tot stand gebracht, maar u kunt nog steeds geen verbinding maken met Azure-resources 

### <a name="cause"></a>Oorzaak

Dit probleem kan zich voordoen als de VPN-client de routes van de Azure VPN-gateway niet ontvangt.

### <a name="solution"></a>Oplossing

U kunt dit probleem oplossen door de [Azure VPN-gateway opnieuw](vpn-gateway-resetgw-classic.md)in te stellen. Om ervoor te zorgen dat de nieuwe routes worden gebruikt, moeten de punt-naar-site-VPN-clients opnieuw worden gedownload nadat de peering van het virtuele netwerk is geconfigureerd.

## <a name="error-the-revocation-function-was-unable-to-check-revocation-because-the-revocation-server-was-offlineerror-0x80092013"></a>Fout: "De intrekkings functie kan het intrekken niet controleren omdat de intrekkings server offline is. (Fout 0x80092013) "

### <a name="causes"></a>Oorzaken
Dit fout bericht wordt weer gegeven als de client http://crl3.digicert.com/ssca-sha2-g1.crl geen http://crl4.digicert.com/ssca-sha2-g1.crl toegang heeft tot en.  Voor de intrekkings controle is toegang tot deze twee sites vereist.  Dit probleem doet zich meestal voor op de client waarop proxy server is geconfigureerd. Als de aanvragen van de proxy server niet worden door lopen, wordt deze in sommige omgevingen geweigerd op de Edge-firewall.

### <a name="solution"></a>Oplossing

Controleer de instellingen van de proxy server, zorg ervoor dat de- http://crl3.digicert.com/ssca-sha2-g1.crl client http://crl4.digicert.com/ssca-sha2-g1.crl toegang heeft tot en.

## <a name="vpn-client-error-the-connection-was-prevented-because-of-a-policy-configured-on-your-rasvpn-server-error-812"></a>VPN-client fout: De verbinding is verhinderd door een beleid dat op uw RAS/VPN-server is geconfigureerd. (Fout 812)

### <a name="cause"></a>Oorzaak

Deze fout treedt op als de RADIUS-server die u hebt gebruikt voor de verificatie van de VPN-client onjuiste instellingen heeft of als Azure gateway de RADIUS-server niet kan bereiken.

### <a name="solution"></a>Oplossing

Zorg ervoor dat de RADIUS-server correct is geconfigureerd. Zie [RADIUS-verificatie integreren met Azure multi-factor Authentication-Server](../active-directory/authentication/howto-mfaserver-dir-radius.md)voor meer informatie.

## <a name="error-405-when-you-download-root-certificate-from-vpn-gateway"></a>Fout 405 wanneer u het basis certificaat downloadt van VPN Gateway

### <a name="cause"></a>Oorzaak

Het basis certificaat is niet geïnstalleerd. Het basis certificaat is geïnstalleerd in het **vertrouwde certificaat** archief van de client.

## <a name="vpn-client-error-the-remote-connection-was-not-made-because-the-attempted-vpn-tunnels-failed-error-800"></a>VPN-client fout: De externe verbinding is niet gemaakt omdat de poging tot VPN-tunnels is mislukt. (Fout 800) 

### <a name="cause"></a>Oorzaak

Het NIC-stuur programma is verouderd.

### <a name="solution"></a>Oplossing

Het NIC-stuur programma bijwerken:

1. Klik op **Start**, typ **Apparaatbeheer**en selecteer deze in de lijst met resultaten. Als u wordt gevraagd om een beheerders wachtwoord of een bevestiging, typt u het wacht woord of geeft u een bevestiging.
2. Zoek in de categorieën **netwerk adapters** de NIC die u wilt bijwerken.  
3. Dubbel klik op de naam van het apparaat, selecteer **stuur programma bijwerken**, selecteer **automatisch zoeken naar bijgewerkte stuur programma-software**.
4. Als er geen nieuw stuur programma wordt gevonden, kunt u op de website van de fabrikant van het apparaat zoeken en de instructies volgen.
5. Start de computer opnieuw op en voer de verbinding opnieuw uit.

## <a name="error-file-download-error-target-uri-is-not-specified"></a>Fout: Fout bij het laden van de doel-URI van het bestand is niet opgegeven

### <a name="cause"></a>Oorzaak

Dit wordt veroorzaakt doordat er een onjuist gateway type is geconfigureerd.

### <a name="solution"></a>Oplossing

Het type van de Azure VPN-gateway moet VPN zijn en het VPN-type moet **RouteBased**zijn.

## <a name="vpn-package-installer-doesnt-complete"></a>Het installatie programma voor het VPN-pakket is niet voltooid

### <a name="cause"></a>Oorzaak

Dit probleem kan worden veroorzaakt door de vorige VPN-client installaties. 

### <a name="solution"></a>Oplossing

Verwijder de oude VPN-client configuratie bestanden **van\<C:\Users\UserName\AppData\Roaming\Microsoft\Network\Connections VirtualNetworkId >** en voer het installatie programma voor de VPN-client opnieuw uit. 

## <a name="the-vpn-client-hibernates-or-sleep-after-some-time"></a>De VPN-client wordt na enige tijd in de slaap stand gezet

### <a name="solution"></a>Oplossing

Controleer de instellingen voor de slaap stand en sluimer stand op de computer waarop de VPN-client wordt uitgevoerd.
