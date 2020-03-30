---
title: Problemen met azure-point-to-site-verbindingen oplossen
titleSuffix: Azure VPN Gateway
description: Meer informatie over het oplossen van problemen met de verbinding tussen punten en site.Learn how to troubleshoot point-to-site connection problems.
services: vpn-gateway
author: chadmath
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 03/26/2020
ms.author: genli
ms.openlocfilehash: 119f9c28b5413b8d2db5fa14ea839d1743f3d64a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297626"
---
# <a name="troubleshooting-azure-point-to-site-connection-problems"></a>Probleemoplossing: azure point-to-site verbindingsproblemen

In dit artikel worden veelvoorkomende verbindingsproblemen weergegeven die u mogelijk ondervindt. Ook worden mogelijke oorzaken en oplossingen voor deze problemen besproken.

## <a name="vpn-client-error-a-certificate-could-not-be-found"></a>FOUT vpn-client: er kon geen certificaat worden gevonden

### <a name="symptom"></a>Symptoom

Wanneer u verbinding probeert te maken met een virtueel Azure-netwerk met behulp van de VPN-client, ontvangt u het volgende foutbericht:

**Er kan geen certificaat worden gevonden dat kan worden gebruikt met dit Extensible Authentication Protocol. (Fout 798)**

### <a name="cause"></a>Oorzaak

Dit probleem treedt op als het clientcertificaat ontbreekt in **Certificaten - Huidige gebruiker\Persoonlijk\Certificaten**.

### <a name="solution"></a>Oplossing

Voer de volgende stappen uit om dit probleem op te lossen:

1. Certificaatbeheer openen: klik op **Start,** typ **computercertificaten beheren**en klik vervolgens op **Computercertificaten beheren** in het zoekresultaat.

2. Controleer of de volgende certificaten zich op de juiste locatie bevinden:

    | Certificaat | Locatie |
    | ------------- | ------------- |
    | AzureClient.pfx  | Huidige gebruiker\Persoonlijk\Certificaten |
    | AzureRoot.cer AzureRoot.cer    | Lokale computer\Vertrouwde rootcertificeringsinstanties|

3. Ga naar C:\Users\<UserName>\AppData\Roaming\Microsoft\Network\Connections\Cm\<GUID>, installeer het certificaat (*.cer-bestand) handmatig in het archief van de gebruiker en de computer.

Zie Certificaten genereren en exporteren voor [point-to-site verbindingen voor](vpn-gateway-certificates-point-to-site.md)meer informatie over het installeren van het clientcertificaat.

> [!NOTE]
> Wanneer u het clientcertificaat importeert, selecteert u niet de optie **Sterke beveiliging van privésleutels inschakelen.**

## <a name="the-network-connection-between-your-computer-and-the-vpn-server-could-not-be-established-because-the-remote-server-is-not-responding"></a>De netwerkverbinding tussen uw computer en de VPN-server kan niet tot stand worden gebracht omdat de externe server niet reageert

### <a name="symptom"></a>Symptoom

Wanneer u probeert verbinding te maken met een Azure-virtuele netwerkgateway met IKEv2 op Windows, krijgt u het volgende foutbericht:

**De netwerkverbinding tussen uw computer en de VPN-server kan niet tot stand worden gebracht omdat de externe server niet reageert**

### <a name="cause"></a>Oorzaak
 
 Het probleem treedt op als de versie van Windows geen ondersteuning heeft voor IKE-fragmentatie
 
### <a name="solution"></a>Oplossing

IKEv2 wordt ondersteund op Windows 10 en Server 2016. Als u IKEv2 wilt gebruiken, moet u lokaal updates installeren en een registersleutelwaarde instellen. Besturingssysteemversies voor Windows 10 worden niet ondersteund en kunnen alleen SSTP gebruiken.

Windows 10 of Server 2016 voorbereiden voor IKEv2:

1. Installeer de update.

   | Versie van het besturingssysteem | Date | Aantal/koppeling |
   |---|---|---|---|
   | Windows Server 2016<br>Windows 10 versie 1607 | 17 januari 2018 | [KB4057142](https://support.microsoft.com/help/4057142/windows-10-update-kb4057142) |
   | Windows 10 versie 1703 | 17 januari 2018 | [KB4057144](https://support.microsoft.com/help/4057144/windows-10-update-kb4057144) |
   | Windows 10 Versie 1709 | 22 maart 2018 | [KB4089848](https://www.catalog.update.microsoft.com/search.aspx?q=kb4089848) |
   |  |  |  |  |

2. De registersleutelwaarde instellen. Maak of `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\RasMan\ IKEv2\DisableCertReqPayload` stel REG_DWORD sleutel in het register in op 1.

## <a name="vpn-client-error-the-message-received-was-unexpected-or-badly-formatted"></a>FOUT van VPN-client: Het ontvangen bericht was onverwacht of slecht geformatteerd

### <a name="symptom"></a>Symptoom

Wanneer u verbinding probeert te maken met een virtueel Azure-netwerk met behulp van de VPN-client, ontvangt u het volgende foutbericht:

**Het ontvangen bericht is onverwacht of slecht geformatteerd. (Fout 0x80090326)**

### <a name="cause"></a>Oorzaak

Dit probleem treedt op als een van de volgende voorwaarden waar is:

- De door gebruikers gedefinieerde routes (UDR) gebruiken met standaardroute op het Gateway-subnet is onjuist ingesteld.
- De openbare hoofdcertificaatsleutel wordt niet geüpload naar de Azure VPN-gateway. 
- De sleutel is beschadigd of verlopen.

### <a name="solution"></a>Oplossing

Voer de volgende stappen uit om dit probleem op te lossen:

1. Verwijder UDR op het gatewaysubnet. Zorg ervoor dat UDR al het verkeer correct doorstuurt.
2. Controleer de status van het hoofdcertificaat in de Azure-portal om te zien of het is ingetrokken. Als deze niet wordt ingetrokken, probeert u het basiscertificaat te verwijderen en opnieuw te uploaden. Zie [Certificaten maken](vpn-gateway-howto-point-to-site-classic-azure-portal.md#generatecerts)voor meer informatie .

## <a name="vpn-client-error-a-certificate-chain-processed-but-terminated"></a>FOUT vpn-client: een certificaatketen verwerkt maar beëindigd 

### <a name="symptom"></a>Symptoom 

Wanneer u verbinding probeert te maken met een virtueel Azure-netwerk met behulp van de VPN-client, ontvangt u het volgende foutbericht:

**Een certificaatketen die is verwerkt, maar beëindigd in een rootcertificaat dat niet wordt vertrouwd door de vertrouwensprovider.**

### <a name="solution"></a>Oplossing

1. Controleer of de volgende certificaten zich op de juiste locatie bevinden:

    | Certificaat | Locatie |
    | ------------- | ------------- |
    | AzureClient.pfx  | Huidige gebruiker\Persoonlijk\Certificaten |
    | Azuregateway-*GUID*.cloudapp.net  | Huidige gebruiker\Vertrouwde rootcertificeringsinstanties|
    | *AzureGateway-GUID*.cloudapp.net, AzureRoot.cer    | Lokale computer\Vertrouwde rootcertificeringsinstanties|

2. Als de certificaten zich al op de locatie bevinden, probeert u de certificaten te verwijderen en ze opnieuw te installeren. Het **azuregateway-GUID.cloudapp.net-certificaat*GUID*** bevindt zich in het VPN-clientconfiguratiepakket dat u hebt gedownload van de Azure-portal. U archiefbestanden gebruiken om de bestanden uit het pakket te extraheren.

## <a name="file-download-error-target-uri-is-not-specified"></a>Fout bij het downloaden van bestanden: doel-URI is niet opgegeven

### <a name="symptom"></a>Symptoom

U ontvangt het volgende foutbericht:

**Fout bij het downloaden van bestanden. Doel-URI is niet opgegeven.**

### <a name="cause"></a>Oorzaak 

Dit probleem treedt op als gevolg van een onjuist gatewaytype. 

### <a name="solution"></a>Oplossing

Het VPN-gatewaytype moet **VPN**zijn en het VPN-type moet **RouteBased**zijn.

## <a name="vpn-client-error-azure-vpn-custom-script-failed"></a>FOUT VPN-client: aangepast script azure VPN is mislukt 

### <a name="symptom"></a>Symptoom

Wanneer u verbinding probeert te maken met een virtueel Azure-netwerk met behulp van de VPN-client, ontvangt u het volgende foutbericht:

**Aangepast script (om uw routeringstabel bij te werken) is mislukt. (Fout 8007026f)**

### <a name="cause"></a>Oorzaak

Dit probleem kan zich voordoen als u de VPN-verbinding van site-to-point probeert te openen met behulp van een snelkoppeling.

### <a name="solution"></a>Oplossing 

Open het VPN-pakket rechtstreeks in plaats van het te openen vanaf de snelkoppeling.

## <a name="cannot-install-the-vpn-client"></a>Kan de VPN-client niet installeren

### <a name="cause"></a>Oorzaak 

Een extra certificaat is vereist om de VPN-gateway voor uw virtuele netwerk te vertrouwen. Het certificaat is opgenomen in het VPN-clientconfiguratiepakket dat wordt gegenereerd vanuit de Azure-portal.

### <a name="solution"></a>Oplossing

Haal het VPN-clientconfiguratiepakket eruit en zoek het .cer-bestand. Voer de volgende stappen uit om het certificaat te installeren:

1. Open mmc.exe.
2. Voeg de module **Certificaten** toe.
3. Selecteer het **computeraccount** voor de lokale computer.
4. Klik met de rechtermuisknop op het knooppunt **Vertrouwde rootcertificeringsinstanties.** Klik op **All-Task** > **Import**en blader naar het .cer-bestand dat u uit het configuratiepakket van de VPN-client hebt gehaald.
5. Start de computer opnieuw op. 
6. Probeer de VPN-client te installeren.

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-data-is-invalid"></a>Azure-portalfout: kan de VPN-gateway niet opslaan en de gegevens zijn ongeldig

### <a name="symptom"></a>Symptoom

Wanneer u de wijzigingen voor de VPN-gateway in de Azure-portal probeert op te slaan, ontvangt u het volgende foutbericht:

**Kan de naam &lt;&gt;van de virtuele *netwerkgateway niet*opslaan. Gegevens voor &lt; *certificaat-id* &gt; zijn ongeldig.**

### <a name="cause"></a>Oorzaak 

Dit probleem kan optreden als de openbare hoofdcertificaatsleutel die u hebt geüpload een ongeldig teken bevat, zoals een spatie.

### <a name="solution"></a>Oplossing

Zorg ervoor dat de gegevens in het certificaat geen ongeldige tekens bevatten, zoals regeleinden (vervoerretouren). De volledige waarde moet een lange lijn. De volgende tekst is een voorbeeld van het certificaat:

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

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-resource-name-is-invalid"></a>Azure-portalfout: kan de VPN-gateway niet opslaan en de naam van de bron is ongeldig

### <a name="symptom"></a>Symptoom

Wanneer u de wijzigingen voor de VPN-gateway in de Azure-portal probeert op te slaan, ontvangt u het volgende foutbericht: 

**Kan de naam &lt;&gt;van de virtuele *netwerkgateway niet*opslaan. De *naam van het broncertificaat dat u probeert te uploaden,* &gt; is ongeldig. &lt;**

### <a name="cause"></a>Oorzaak

Dit probleem treedt op omdat de naam van het certificaat een ongeldig teken bevat, zoals een spatie. 

## <a name="azure-portal-error-vpn-package-file-download-error-503"></a>Azure portal fout: VPN-pakket bestand download fout 503

### <a name="symptom"></a>Symptoom

Wanneer u het configuratiepakket van de VPN-client probeert te downloaden, ontvangt u het volgende foutbericht:

**Kan het bestand niet downloaden. Foutdetails: fout 503. De server is bezet.**
 
### <a name="solution"></a>Oplossing

Deze fout kan worden veroorzaakt door een tijdelijk netwerkprobleem. Probeer het VPN-pakket na een paar minuten opnieuw te downloaden.

## <a name="azure-vpn-gateway-upgrade-all-point-to-site-clients-are-unable-to-connect"></a>Azure VPN-gateway-upgrade: alle Point-to-siteclients kunnen geen verbinding maken

### <a name="cause"></a>Oorzaak

Als het certificaat gedurende de levensduur meer dan 50 procent is, wordt het certificaat omgerold.

### <a name="solution"></a>Oplossing

Als u dit probleem wilt oplossen, downloadt u het pakket Point to Site opnieuw en implementeert u het pakket Point to Site op alle clients.

## <a name="too-many-vpn-clients-connected-at-once"></a>Te veel VPN-clients tegelijk verbonden

Het maximum aantal toegestane verbindingen wordt bereikt. U het totale aantal verbonden clients in de Azure-portal zien.

## <a name="vpn-client-cannot-access-network-file-shares"></a>VPN-client heeft geen toegang tot netwerkbestandsshares

### <a name="symptom"></a>Symptoom

De VPN-client is verbonden met het virtuele Azure-netwerk. De client heeft echter geen toegang tot netwerkshares.

### <a name="cause"></a>Oorzaak

Het SMB-protocol wordt gebruikt voor toegang tot bestandsshare. Wanneer de verbinding wordt gestart, voegt de VPN-client de sessiereferenties toe en treedt de fout op. Nadat de verbinding is gemaakt, wordt de client gedwongen om de cachereferenties te gebruiken voor Kerberos-verificatie. Met dit proces worden query's gestart naar het Key Distribution Center (een domeincontroller) om een token te krijgen. Omdat de client verbinding maakt vanaf het internet, kan deze mogelijk niet bij de domeincontroller komen. Daarom kan de client niet mislukken van Kerberos naar NTLM. 

De enige keer dat de client wordt gevraagd om een referentie is wanneer deze een geldig certificaat (met SAN=UPN) heeft uitgegeven door het domein waaraan de client is gekoppeld. De client moet ook fysiek verbonden zijn met het domeinnetwerk. In dit geval probeert de client het certificaat te gebruiken en bereikt hij de domeincontroller. Vervolgens geeft het sleuteldistributiecentrum een fout 'KDC_ERR_C_PRINCIPAL_UNKNOWN' als resultaat. De klant wordt gedwongen om over te gaan naar NTLM. 

### <a name="solution"></a>Oplossing

Schakel het incachemaken van domeinreferenties uit de volgende registersubsleutel uit om het probleem te omzeilen: 

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\DisableDomainCreds - Set the value to 1 


## <a name="cannot-find-the-point-to-site-vpn-connection-in-windows-after-reinstalling-the-vpn-client"></a>Kan de point-to-site VPN-verbinding in Windows niet vinden na het opnieuw installeren van de VPN-client

### <a name="symptom"></a>Symptoom

U verwijdert de point-to-site VPN-verbinding en installeert vervolgens de VPN-client opnieuw. In deze situatie is de VPN-verbinding niet geconfigureerd. U ziet de VPN-verbinding niet in de instellingen **voor netwerkverbindingen** in Windows.

### <a name="solution"></a>Oplossing

Als u het probleem wilt oplossen, verwijdert u de oude VPN-clientconfiguratiebestanden van **C:\Users\UserName\AppData\Roaming\Microsoft\Network\Connections\<VirtualNetworkId>** en voert u het installatieprogramma van de VPN-client opnieuw uit.

## <a name="point-to-site-vpn-client-cannot-resolve-the-fqdn-of-the-resources-in-the-local-domain"></a>Point-to-site VPN-client kan de FQDN van de bronnen in het lokale domein niet oplossen

### <a name="symptom"></a>Symptoom

Wanneer de client verbinding maakt met Azure via een point-to-site VPN-verbinding, kan deze de FQDN van de bronnen in uw lokale domein niet oplossen.

### <a name="cause"></a>Oorzaak

De point-to-site VPN-client maakt gebruik van Azure DNS-servers die zijn geconfigureerd in het virtuele Azure-netwerk. De Azure DNS-servers hebben voorrang op de lokale DNS-servers die in de client zijn geconfigureerd, zodat alle DNS-query's naar de Azure DNS-servers worden verzonden. Als de Azure DNS-servers niet over de records voor de lokale bronnen beschikken, mislukt de query.

### <a name="solution"></a>Oplossing

Als u het probleem wilt oplossen, moet u ervoor zorgen dat de Azure DNS-servers die in het virtuele Azure-netwerk zijn gebruikt, de DNS-records voor lokale bronnen kunnen oplossen. Hiervoor u DNS Forwarders of Conditional forwarders gebruiken. Zie [Naamomzetting met uw eigen DNS-server](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) voor meer informatie

## <a name="the-point-to-site-vpn-connection-is-established-but-you-still-cannot-connect-to-azure-resources"></a>De point-to-site VPN-verbinding is tot stand gekomen, maar u nog steeds geen verbinding maken met Azure-bronnen 

### <a name="cause"></a>Oorzaak

Dit probleem kan optreden als de VPN-client de routes van de Azure VPN-gateway niet krijgt.

### <a name="solution"></a>Oplossing

Als u dit probleem wilt oplossen, [stelt u de Azure VPN-gateway opnieuw in.](vpn-gateway-resetgw-classic.md) Om ervoor te zorgen dat de nieuwe routes worden gebruikt, moeten de Point-to-Site VPN-clients opnieuw worden gedownload nadat virtuele netwerkpeering met succes is geconfigureerd.

## <a name="error-the-revocation-function-was-unable-to-check-revocation-because-the-revocation-server-was-offlineerror-0x80092013"></a>Fout: "De intrekkingsfunctie kan intrekking niet controleren omdat de intrekkingsserver offline was. (Fout 0x80092013)"

### <a name="causes"></a>Oorzaken
Dit foutbericht treedt op http://crl3.digicert.com/ssca-sha2-g1.crl als http://crl4.digicert.com/ssca-sha2-g1.crlde client geen toegang heeft en .  De intrekkingscontrole vereist toegang tot deze twee sites.  Dit probleem treedt meestal op bij de client waarop de proxyserver is geconfigureerd. In sommige omgevingen, als de aanvragen niet via de proxyserver gaan, wordt deze geweigerd bij de Edge Firewall.

### <a name="solution"></a>Oplossing

Controleer de proxyserverinstellingen, controleer of http://crl3.digicert.com/ssca-sha2-g1.crl de http://crl4.digicert.com/ssca-sha2-g1.crlclient toegang heeft en .

## <a name="vpn-client-error-the-connection-was-prevented-because-of-a-policy-configured-on-your-rasvpn-server-error-812"></a>VPN-clientfout: de verbinding is verhinderd vanwege een beleid dat is geconfigureerd op uw RAS/VPN-server. (Fout 812)

### <a name="cause"></a>Oorzaak

Deze fout treedt op als de RADIUS-server die u hebt gebruikt voor het verifiëren van de VPN-client onjuiste instellingen heeft of Azure Gateway de Radius-server niet kan bereiken.

### <a name="solution"></a>Oplossing

Controleer of de RADIUS-server correct is geconfigureerd. Zie [RADIUS-verificatie integreren met Azure Multi-Factor Authentication Server](../active-directory/authentication/howto-mfaserver-dir-radius.md)voor meer informatie.

## <a name="error-405-when-you-download-root-certificate-from-vpn-gateway"></a>"Fout 405" wanneer u rootcertificaat downloadt van VPN Gateway

### <a name="cause"></a>Oorzaak

Rootcertificaat was niet geïnstalleerd. Het hoofdcertificaat is geïnstalleerd in het **vertrouwde certificatenarchief van** de client.

## <a name="vpn-client-error-the-remote-connection-was-not-made-because-the-attempted-vpn-tunnels-failed-error-800"></a>VPN-clientfout: de externe verbinding is niet gemaakt omdat de geprobeerde VPN-tunnels zijn mislukt. (Fout 800) 

### <a name="cause"></a>Oorzaak

De NIC-chauffeur is verouderd.

### <a name="solution"></a>Oplossing

Update het NIC-stuurprogramma:

1. Klik **op Start,** typ **Apparaatbeheer**en selecteer deze in de lijst met resultaten. Als u wordt gevraagd om een beheerderswachtwoord of -bevestiging, typt u het wachtwoord of geeft u een bevestiging.
2. Zoek in de categorieën **Netwerkadapters** de NIC die u wilt bijwerken.  
3. Dubbelklik op de naam van het apparaat, selecteer **Stuurprogramma bijwerken**, selecteer Automatisch zoeken naar **bijgewerkte stuurprogrammasoftware**.
4.  Als Windows geen nieuw stuurprogramma kan vinden, kunt u ernaar zoeken op de website van de fabrikant van het apparaat en de aanwijzingen aldaar opvolgen.
5. Start de computer opnieuw op en probeer de verbinding opnieuw.

## <a name="vpn-client-error-dialing-vpn-connection-vpn-connection-name-status--vpn-platform-did-not-trigger-connection"></a>VPN-clientfout: VPN-verbinding <VPN Connection Name>bellen , Status = VPN-platform heeft geen verbinding geactiveerd

U ook de volgende fout in Logboeken <User> van RasClient <VPN Connection Name> zien: De gebruiker heeft een verbinding met de naam gekozen die is mislukt. De foutcode die bij een storing wordt geretourneerd, is 1460."

### <a name="cause"></a>Oorzaak

De Azure VPN-client heeft niet de app-machtiging 'Achtergrond-apps' ingeschakeld in App-instellingen voor Windows.

### <a name="solution"></a>Oplossing

1. Ga in Windows naar Instellingen -> Privacy -> Achtergrond-apps
2. De 'Apps op de achtergrond laten draaien' inschakelen op Aan

## <a name="error-file-download-error-target-uri-is-not-specified"></a>Fout: 'Bestandsdownloadfout Target URI is niet opgegeven'

### <a name="cause"></a>Oorzaak

Dit wordt veroorzaakt doordat een onjuist gatewaytype is geconfigureerd.

### <a name="solution"></a>Oplossing

Het type Azure VPN-gateway moet VPN zijn en het VPN-type moet **RouteBased**zijn.

## <a name="vpn-package-installer-doesnt-complete"></a>VPN-pakket installer niet compleet

### <a name="cause"></a>Oorzaak

Dit probleem kan worden veroorzaakt door de vorige VPN-clientinstallaties. 

### <a name="solution"></a>Oplossing

Verwijder de oude VPN-clientconfiguratiebestanden van **C:\Users\UserName\AppData\Roaming\Microsoft\Network\Connections\<VirtualNetworkId>** en voer het vpn-clientprogramma opnieuw uit. 

## <a name="the-vpn-client-hibernates-or-sleep-after-some-time"></a>De VPN-client overwintert of slaapt na enige tijd

### <a name="solution"></a>Oplossing

Controleer de instellingen voor slaapstand en sluimerstand in de computer waarop de VPN-client wordt uitgevoerd.
