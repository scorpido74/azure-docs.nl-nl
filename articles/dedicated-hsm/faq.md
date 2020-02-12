---
title: Veelgestelde vragen - Azure toegewezen HSM | Microsoft Docs
description: Veelgestelde vragen over die betrekking hebben op andere onderwerpen over Azure toegewezen HSM
services: dedicated-hsm
author: johncdawson
manager: rkarlin
tags: azure-resource-manager
ms.custom: mvc
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/05/2020
ms.author: mbaldwin
ms.openlocfilehash: 947dc6eb8060d2d229ee7984f719cb837e638490
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/06/2020
ms.locfileid: "77045931"
---
# <a name="frequently-asked-questions-faq"></a>Veelgestelde vragen

Vind antwoorden op veelgestelde vragen over Microsoft Azure toegewezen HSM.

## <a name="the-basics"></a>De basisbeginselen

### <a name="q-what-is-a-hardware-security-module-hsm"></a>V: Wat is er een hardware security module (HSM)?

Een Hardware Security Module (HSM) is een fysieke computer of fysiek apparaat gebruikt om te beveiligen en beheren van cryptografische sleutels. Sleutels die zijn opgeslagen in HSM's kunnen worden gebruikt voor cryptografische bewerkingen. Het sleutelmateriaal blijft veilig in vervalsen, verzegelde hardware-modules. De HSM kan alleen geverifieerde en gemachtigde toepassingen om de sleutels te gebruiken. De sleutels blijven altijd binnen het bereik van de HSM-beveiliging.

### <a name="q-what-is-the-azure-dedicated-hsm-offering"></a>V: wat is de voor Azure speciale HSM-aanbieding?

Azure toegewezen HSM is een cloud-gebaseerde service waarmee HSM's die worden gehost in Azure-datacenters die rechtstreeks zijn verbonden met het virtuele netwerk van een klant. Deze HSM's zijn toegewezen netwerkapparaten (de Gemalto SafeNet netwerk HSM 7 Model A790). Ze zijn geïmplementeerd rechtstreeks naar een klanten privé IP-adresruimte en Microsoft heeft geen toegang heeft tot de cryptografische functionaliteit van de HSM's. Alleen de klant heeft volledige administratieve en cryptografische controle over deze apparaten. Klanten zijn verantwoordelijk voor het beheer van het apparaat en ze volledig activiteitenlogboeken rechtstreeks vanaf hun apparaten kunnen krijgen. Toegewezen HSM's helpen klanten te voldoen aan naleving/wettelijke vereisten zoals FIPS 140-2 Level 3, HIPAA, PCI-DSS en eIDAS en nog veel meer.

### <a name="q-what-hardware-is-used-for-dedicated-hsm"></a>V: welke hardware wordt gebruikt voor een toegewezen HSM?

Micro soft heeft een partnerschap met Gemalto voor het leveren van de toegewezen HSM-service van Azure. Het specifieke apparaat dat wordt gebruikt, is het [SafeNet Luna Network HSM 7 model A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/). Dit apparaat wordt niet alleen biedt FIPS 140-2 Level 3 gevalideerd firmware, maar er wordt een biedt ook met lage latentie, hoge prestaties en hoge capaciteit via 10 partities. 

### <a name="q-what-is-an-hsm-used-for"></a>V: Wat is een HSM gebruikt?

HSM's worden gebruikt voor het opslaan van versleutelingssleutels die worden gebruikt voor versleutelingsfunctionaliteit, zoals SSL (Secure Socket Layer), het versleutelen van gegevens, PKI (Public Key Infrastructure), DRM (Digital Rights Management) en het ondertekenen van documenten.

### <a name="q-how-does-dedicated-hsm-work"></a>V: hoe werkt de toegewezen HSM?

Klanten kunnen inrichten HSM's in specifieke regio's met PowerShell of opdrachtregelinterface. De klant geeft aan welke virtuele netwerk de HSM's worden verbonden met en één keer ingericht de HSM's zijn beschikbaar in het opgegeven subnet op toegewezen IP-adressen in de persoonlijke IP-adresruimte van de klant. Vervolgens klanten verbinding kunnen maken met de HSM's voor het beheer van het apparaat met behulp van SSH en beheer, instellen van de HSM-clientverbindingen, initialiseren van HSM's, partities maken, definiëren en rollen, zoals partitie officer, crypto officer en crypto-gebruiker toe te wijzen. Vervolgens wordt de klant Gemalto opgegeven HSM-hulpprogramma's / SDK/clientsoftware gebruiken om uit te voeren cryptografische bewerkingen via hun toepassingen.

### <a name="q-what-software-is-provided-with-the-dedicated-hsm-service"></a>V: voor welke software wordt geleverd met de toegewezen HSM-service?

Gemalto geeft alle software voor de HSM-apparaat eenmaal is ingericht door Microsoft. De software is beschikbaar op de [Gemalto-portal voor klant ondersteuning](https://supportportal.gemalto.com/csm/). Klanten die gebruikmaken van de toegewezen HSM-service zijn vereist om te worden geregistreerd voor Gemalto ondersteunen en een klant-ID waarmee de toegang en downloaden van de relevante software hebben. De ondersteunde client software is versie 7,2, die compatibel is met de gevalideerde firmware versie van de FIPS 140-2 level 3-7.0.3. 

### <a name="q-does-azure-dedicated-hsm-offer-password-based-and-ped-based-authentication"></a>V: heeft Azure toegewezen HSM een op wacht woord gebaseerde en PED verificatie aangeboden?

Op dit moment biedt Azure dedicated HSM alleen Hsm's met verificatie op basis van wacht woorden.

### <a name="q-will-azure-dedicated-hsm-host-my-hsms-for-me"></a>V: zal Azure dedicated HSM host mijn Hsm's voor mij?

Micro soft biedt alleen de Gemalto SafeNet Luna Network HSM via de toegewezen HSM-service en kan geen door klanten geleverde apparaten hosten.

### <a name="q-does-azure-dedicated-hsm-support-payment-pineft-features"></a>V: heeft Azure exclusieve HSM-functies voor de ondersteuning van betalingen (PINCODEs/EFT)?

De specifieke HSM-service van Azure maakt gebruik van SafeNet Luna Network HSM 7-apparaten (model A790). Deze apparaten bieden geen ondersteuning voor de specifieke functionaliteit voor betalings-HSM (zoals pincode of EFT) of certificeringen. Als u wilt dat de exclusieve HSM-service van Azure in de toekomst betaling Hsm's ondersteunt, geeft u de feedback door aan uw micro soft-account vertegenwoordiger.

### <a name="q-which-azure-regions-is-dedicated-hsm-available-in"></a>V: in welke Azure-regio's is een speciale HSM beschikbaar?

Vanaf eind maart 2019 is toegewezen HSM beschikbaar in de volgende 14 regio's. Verdere regio's worden gepland en kunnen worden besproken via de vertegenwoordiger van uw micro soft-account.

* VS - oost
* VS - oost 2
* VS - west
* VS - zuid-centraal
* Azië - zuidoost
* Azië - oost
* India - centraal
* India - zuid
* Japan - oost
* Japan - west
* Europa - noord
* Europa -west
* Verenigd Koninkrijk Zuid
* Verenigd Koninkrijk West
* Canada - midden
* Canada - oost
* Australië - oost
* Australië - zuidoost

## <a name="interoperability"></a>Interoperabiliteit

### <a name="q-how-does-my-application-connect-to-a-dedicated-hsm"></a>V: hoe mijn toepassing verbinden met een HSM toegewezen?

U Gemalto opgegeven client-hulpprogramma's / SDK/software van de HSM om uit te voeren cryptografische bewerkingen van uw toepassingen. De software is beschikbaar op de [Gemalto-portal voor klant ondersteuning](https://supportportal.gemalto.com/csm/). Klanten die gebruikmaken van de toegewezen HSM-service zijn vereist om te worden geregistreerd voor Gemalto ondersteunen en een klant-ID waarmee de toegang en downloaden van de relevante software hebben.

### <a name="q-can-an-application-connect-to-dedicated-hsm-from-a-different-vnet-in-or-across-regions"></a>V: kan een toepassing verbinding maken met toegewezen HSM vanuit een ander VNET in of meerdere regio's?

Ja, u moet [VNET-peering](../virtual-network/virtual-network-peering-overview.md) binnen een regio gebruiken om verbinding te maken tussen virtuele netwerken. Voor connectiviteit tussen regio's moet u [VPN gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)gebruiken.

### <a name="q-can-i-synchronize-dedicated-hsm-with-on-premises-hsms"></a>V: kan ik toegewezen HSM synchroniseren met on-premises HSM's?

Ja, kunt u on-premises HSM's met toegewezen HSM synchroniseren. [Point-to-Point VPN-verbinding of punt-naar-site](../vpn-gateway/vpn-gateway-about-vpngateways.md) -connectiviteit kan worden gebruikt om verbinding te maken met uw on-premises netwerk.

### <a name="q-can-i-encrypt-data-used-by-other-azure-services-using-keys-stored-in-dedicated-hsm"></a>V: kan ik gegevens die worden gebruikt door andere Azure-services met behulp van sleutels die zijn opgeslagen in HSM toegewezen versleutelen?

Nee. Azure toegewezen HSM's zijn alleen toegankelijk binnen het virtuele netwerk.

### <a name="q-can-i-import-keys-from-an-existing-on-premises-hsm-to-dedicated-hsm"></a>V: kan ik sleutels importeren uit een bestaande On-premises HSM naar HSM toegewezen?

Ja, als u on-premises Gemalto SafeNet HSM's. Er zijn meerdere methoden. Raadpleeg de documentatie Gemalto HSM.

### <a name="q-what-operating-systems-are-supported-by-dedicated-hsm-client-software"></a>V: voor welke besturingssystemen worden ondersteund door de clientsoftware van toegewezen HSM?

* Windows, Linux, Solaris, AIX, HP-UX, FreeBSD
* Virtuele: De VMware en Hyper-v, Xen, KVM

### <a name="q-how-do-i-configure-my-client-application-to-create-a-high-availability-configuration-with-multiple-partitions-from-multiple-hsms"></a>V: hoe kan ik mijn-clienttoepassing voor het maken van een maximaal beschikbaarheidsconfiguratie met meerdere partities uit meerdere HSM's configureren?

Als u wilt een hoge beschikbaarheid hebben, moet u uw HSM-clientconfiguratie toepassing ingesteld voor het gebruik van de partities van de HSM. Raadpleeg de documentatie van Gemalto HSM client software.

### <a name="q-what-authentication-mechanisms-are-supported-by-dedicated-hsm"></a>V: voor welke verificatiemechanismen worden ondersteund door toegewezen HSM?

Azure toegewezen HSM gebruikt SafeNet netwerk-HSM-7-apparaten (model A790) en ondersteuning van verificatie op basis van wachtwoorden.

### <a name="q-what-sdks-apis-client-software-is-available-to-use-with-dedicated-hsm"></a>V: voor welke software van de client SDK's, API's, is beschikbaar voor gebruik met toegewezen HSM?

PKCS #11, Java (JCA/JCE), Microsoft CAPI en CNG, OpenSSL

### <a name="q-can-i-importmigrate-keys-from-luna-56-hsms-to-azure-dedicated-hsms"></a>V: kan ik importeren/migreren sleutels uit Luna 5/6 HSM's naar Azure toegewezen HSM's?

Ja. Raadpleeg de Gemalto-migratie handleiding. 

## <a name="using-your-hsm"></a>Met behulp van uw HSM

### <a name="q-how-do-i-decide-whether-to-use-azure-key-vault-or-azure-dedicated-hsm"></a>V: hoe bepaal ik of Azure Key Vault of Azure toegewezen HSM wilt gebruiken?

Azure toegewezen HSM is de juiste keuze voor ondernemingen migreert naar Azure on-premises toepassingen die gebruikmaken van HSM's. Toegewezen HSM's bieden de mogelijkheid voor het migreren van een toepassing met minimale wijzigingen. Als in de toepassingscode die wordt uitgevoerd in een virtuele machine van Azure of een Web-App worden cryptografische bewerkingen uitgevoerd, kunnen ze toegewezen HSM gebruiken. In het algemeen krimp software die wordt uitgevoerd in de modellen van IaaS (infrastructuur als een service), die ondersteuning bieden voor HSM's zoals een sleutelarchief kunt reserveren HSM, zoals Application gateway of traffic manager voor een SSL, AD CS (Active Directory Certificate Services), of vergelijkbare PKI-hulpprogramma's, hulpprogramma's / toepassingen die worden gebruikt voor het ondertekenen van documenten, ondertekening van programmacode of een SQL Server (IaaS) is geconfigureerd met TDE (transparent databaseversleuteling) met de hoofdsleutel in een HSM met behulp van een EKM-provider (extensible key management). Azure Key Vault is geschikt voor 'geboren-in-cloud'-toepassingen of voor versleuteling-at-rest-scenario's waar klantgegevens worden verwerkt door PaaS (platform als een service) of SaaS (Software als een service)-scenario's zoals de sleutel in de Office 365-klant, Azure Information Protection , Azure Disk Encryption, Azure Data Lake Store-versleuteling met door de klant beheerde sleutel, Azure Storage met de klant beheerde sleutel, en met SQL Azure met de klant beheerde sleutel.

### <a name="q-what-usage-scenarios-best-suit-azure-dedicated-hsm"></a>V: voor welke gebruiksscenario's voor aanbevolen Azure toegewezen HSM aanpassen?

Azure toegewezen HSM is het meest geschikt voor scenario's voor migratie. Dit betekent dat als u on-premises toepassingen naar Azure die al van HSM's gebruikmaken migreert. Dit biedt een lage wrijving optie om te migreren naar Azure met minimale wijzigingen in de toepassing. Als in de toepassingscode die wordt uitgevoerd in Azure VM of Web-App worden cryptografische bewerkingen uitgevoerd, kunnen toegewezen HSM kan worden gebruikt. In het algemeen krimp software die wordt uitgevoerd in de modellen van IaaS (infrastructuur als een service), die ondersteuning bieden voor HSM's zoals een sleutelarchief een speciale HSM, zoals gebruiken kan:

* Application gateway of traffic manager voor een SSL
* AD CS (Active Directory certificaatservices)
* Vergelijkbare PKI-hulpprogramma 's
* Hulpprogramma's / toepassingen die worden gebruikt voor het ondertekenen van documenten
* Ondertekening van programmacode
* SQL Server (IaaS) is geconfigureerd met TDE (transparent databaseversleuteling) met de hoofdsleutel in een HSM met behulp van een EKM-provider (extensible key management)

### <a name="q-can-dedicated-hsm-be-used-with-office-365-customer-key-azure-information-protection-azure-data-lake-store-disk-encryption-azure-storage-encryption-azure-sql-tde"></a>Vraag: kan toegewezen HSM worden gebruikt met de sleutel voor Office 365-klant, Azure Information Protection, Azure Data Lake Store, schijfversleuteling, versleuteling in Azure Storage, Azure SQL TDE?

Nee. Toegewezen HSM wordt ingericht rechtstreeks in privé-IP-adresruimte van een klant, zodat deze niet toegankelijk is voor andere Azure- of Microsoft-services.

## <a name="administration-access-and-control"></a>Beheer, toegang tot en beheer

### <a name="q-does-the-customer-get-full-exclusive-control-over-the-hsms-with-dedicated-hsms"></a>V: helpt de klant volledige exclusieve controle over de HSM's met toegewezen HSM's krijgen?

Ja. Elke HSM-apparaat is volledig toegewezen voor een afzonderlijke klant en niemand anders heeft beheer eenmaal ingericht en de administrator-wachtwoord gewijzigd.

### <a name="q-what-level-of-access-does-microsoft-have-to-my-hsm"></a>V: voor welke toegangsniveau beschikt Microsoft over naar Mijn HSM?

Microsoft heeft geen eventuele administratieve of cryptografische controle over de HSM. Microsoft heeft toegang via een seriële poortverbinding om op te halen van basic telemetriegegevens zoals temperatuur en onderdeel health monitor. Hierdoor kan Microsoft voor proactieve meldingen van statusproblemen. Als dat nodig is, kan de klant dit account uitschakelen.

### <a name="q-what-is-the-tenantadmin-account-microsoft-uses-i-am-used-to-the-admin-user-being-admin-on-safenet-hsms"></a>V: wat is het ' tenantadmin-account dat wordt gebruikt door micro soft, ik heb de beheerder van de gebruiker ' admin ' op SafeNet Hsm's?

Het HSM-apparaat wordt geleverd met een standaard gebruiker van de beheerder met het gebruikelijke standaard wachtwoord. Micro soft wil geen standaard wachtwoorden gebruiken terwijl een apparaat in een groep wacht om te worden ingericht door klanten. Dit voldoet niet aan onze strikte beveiligings vereisten. Daarom stellen we een sterk wacht woord in dat wordt verwijderd bij de inrichtings tijd. Bij het inrichten van de tijd maken we ook een nieuwe gebruiker in de rol Admin met de naam ' tenantadmin '. Deze gebruiker heeft het standaard wachtwoord en klanten wijzigen dit als de eerste actie wanneer u zich voor het eerst aanmeldt bij het nieuw ingerichte apparaat. Dit proces garandeert een hoge mate van beveiliging en onderhoudt onze belofte van exclusieve administratieve controle voor onze klanten. Houd er rekening mee dat de gebruiker ' tenantadmin ' kan worden gebruikt om het wacht woord van de gebruikers beheerder opnieuw in te stellen als een klant dat account liever gebruikt. 

### <a name="q-can-microsoft-or-anyone-at-microsoft-access-keys-in-my-dedicated-hsm"></a>V: kan Microsoft of iemand bij Microsoft access-toetsen in Mijn toegewezen HSM?

Nee. Microsoft heeft geen toegang heeft tot de sleutels die zijn opgeslagen in klant toegewezen HSM toegewezen.

### <a name="q-can-i-upgrade-softwarefirmware-on-hsms-allocated-to-me"></a>V: kan ik software/firmware op HSM's die aan mij toegewezen upgraden?

Als u de beste ondersteuning, wordt aangeraden niet bij te werken software/firmware op de HSM. De klant heeft echter volledig beheren, inclusief het upgraden van software/firmware als specifieke functies nodig zijn uit verschillende firmware-versies zijn. Voordat u wijzigingen aanbrengt, moeten de implicaties worden begrepen, omdat dit bijvoorbeeld van invloed is op de FIPS-gevalideerde status. 

### <a name="q-how-do-i-manage-dedicated-hsm"></a>V: hoe kan ik toegewezen HSM beheren?

U kunt de toegewezen HSM's beheren door deze met behulp van SSH.

### <a name="q-how-do-i-manage-partitions-on-the-dedicated-hsm"></a>V: hoe beheer ik partities op de HSM toegewezen?

De clientsoftware Gemalto HSM wordt gebruikt voor het beheren van de HSM's en partities.

### <a name="q-how-do-i-monitor-my-hsm"></a>V: hoe bewaak ik mijn HSM?

Een klant heeft volledige toegang tot HSM-activiteitenlogboeken via syslog- en SNMP. Een klant moet voor het instellen van een syslog-server of een SNMP-server de logboeken of gebeurtenissen ontvangen van de HSM's.

### <a name="q-can-i-get-full-access-log-of-all-hsm-operations-from-dedicated-hsm"></a>V: kan ik volledige toegang tot logboek van alle HSM-bewerkingen van toegewezen HSM krijgen?

Ja. U kunt Logboeken van de HSM-apparaat verzenden naar een syslog-server

## <a name="high-availability"></a>Hoge beschikbaarheid

### <a name="q-is-it-possible-to-configure-high-availability-in-the-same-region-or-across-multiple-regions"></a>V: is het mogelijk om hoge Beschik baarheid te configureren in dezelfde regio of in meerdere regio's?

Ja. Hoge beschikbaarheid, configuratie en installatie uitgevoerd binnen de HSM-clientsoftware die is geleverd door Gemalto. Hsm's van hetzelfde VNET of andere VNETs in dezelfde regio of in verschillende regio's, of on-premises Hsm's die zijn verbonden met een VNET met behulp van site-naar-site-of Point-to-Point VPN, kunnen worden toegevoegd aan dezelfde configuratie voor hoge Beschik baarheid. U moet erop gewezen dat het sleutel materiaal alleen synchroniseert en geen specifieke configuratie-items zoals rollen.

### <a name="q-can-i-add-hsms-from-my-on-premises-network-to-a-high-availability-group-with-azure-dedicated-hsm"></a>V: kan ik Hsm's van mijn on-premises netwerk toevoegen aan een groep met hoge Beschik baarheid met een toegewezen HSM van Azure?

Ja. Ze moeten voldoen aan de vereisten voor hoge beschikbaarheid voor 7 SafeNet Luna netwerk-HSM.

### <a name="q-can-i-add-luna-56-hsms-from-on-premises-networks-to-a-high-availability-group-with-azure-dedicated-hsm"></a>V: kan ik Luna 5/6 Hsm's van on-premises netwerken toevoegen aan een groep met hoge Beschik baarheid met een toegewezen HSM van Azure?

Nee.

### <a name="q-how-many-hsms-can-i-add-to-the-same-high-availability-configuration-from-one-single-application"></a>V: hoe veel HSM's kan ik toevoegen aan de dezelfde configuratie voor hoge beschikbaarheid van één enkele toepassing?

16 leden van een HA-groep hebben onder-weg, Full-Throttle tests met uitstekende resultaten.

## <a name="support"></a>Ondersteuning

### <a name="q-what-is-the-sla-for-dedicated-hsm-service"></a>V: Wat is de SLA voor toegewezen HSM-service?

Er is geen specifieke uptime-garantie opgegeven voor de speciale HSM-service. Microsoft zorgt ervoor dat niveau netwerktoegang tot het apparaat en kan daarom standard Azure networking Sla's van toepassing.

### <a name="q-how-are-the-hsms-used-in-azure-dedicated-hsm-protected"></a>V: hoe worden de HSM's in Azure toegewezen HSM-beveiligde gebruikt?

Azure-datacenters hebben besturingselementen uitgebreide beveiliging voor fysieke en procedures. Daarnaast worden toegewezen HSM's gehost in een gebied verder met beperkte toegang in het datacenter. Deze gebieden zijn extra fysieke toegangscontrole en videocamera bewaking voor extra beveiliging.

### <a name="q-what-happens-if-there-is-a-security-breach-or-hardware-tampering-event"></a>V: Wat gebeurt er als er een schending van de beveiliging of manipulatie van gebeurtenis hardware?

Toegewezen HSM-service maakt gebruik van SafeNet netwerk-HSM-7-apparaten. Deze apparaten ondersteuning voor detectie van fysieke en logische onrechtmatige wijzigingen. Als er een gebeurtenis onrechtmatige wijzigingen is worden automatisch de HSM's zeroized.

### <a name="q-how-do-i-ensure-that-keys-in-my-dedicated-hsms-are-not-lost-due-to-error-or-a-malicious-insider-attack"></a>V: hoe kan ik ervoor zorgen dat er geen sleutels in Mijn toegewezen HSM verloren vanwege een fout of een kwaadwillende insider-aanval?

Het is raadzaam een back-up on-premises HSM-apparaat gebruiken om uit te voeren van reguliere periodieke back-up van de HSM's voor herstel na noodgevallen. U moet een peer-to-peer of site-naar-site VPN-verbinding naar een on-premises werkstation dat is verbonden met een back-HSM-apparaat gebruiken.

### <a name="q-how-do-i-get-support-for-dedicated-hsm"></a>V: hoe krijg ik ondersteuning voor toegewezen HSM?

Ondersteuning wordt geboden door zowel micro soft als Gemalto.  Als u een probleem hebt met de hardware-of netwerk toegang, verhoogt u een ondersteunings aanvraag met micro soft en als u een probleem ondervindt met HSM-configuratie, software en toepassings ontwikkeling, wordt een ondersteunings aanvraag met Gemalto gegenereerd. Als u een onbepaald probleem hebt, kunt u een ondersteunings aanvraag indienen bij micro soft en kan Gemalto indien nodig worden ingeschakeld. 

### <a name="q-how-do-i-get-the-client-software-documentation-and-access-to-integration-guidance-for-the-safenet-luna-7-hsm"></a>V: Hoe kan ik de client software, documentatie en toegang verkrijgen tot integratie richtlijnen voor de SafeNet Luna 7 HSM?

Nadat u zich hebt geregistreerd voor de service, wordt er een Gemalto-klant-ID gegeven die registratie toestaat in de Gemalto-portal voor klanten ondersteuning. Hiermee wordt toegang tot alle software en documentatie ingeschakeld en worden ondersteunings aanvragen rechtstreeks met Gemalto ingeschakeld.

### <a name="q-if-there-is-a-security-vulnerability-found-and-a-patch-is-released-by-gemalto-who-is-responsible-for-upgradingpatching-osfirmware"></a>Vraag: als er een beveiligingsprobleem gevonden en een patch is uitgegeven door Gemalto, die verantwoordelijk is voor upgraden/patchen OS/Firmware?

Microsoft heeft niet de mogelijkheid om te verbinden met HSM's die zijn toegewezen aan klanten. Klanten moeten upgraden en vullen van de HSM's.

### <a name="q-what-if-i-need-to-reboot-my-hsm"></a>V: wat moet ik doen als ik mijn HSM opnieuw opstart?

De HSM heeft een optie voor opnieuw opstarten van de opdracht regel. er zijn echter problemen met het opnieuw opstarten van de computer en daarom wordt het aanbevolen om de veiligste keer dat u een ondersteunings aanvraag met micro soft hebt uitgevoerd om het apparaat fysiek opnieuw op te starten. 

## <a name="cryptography-and-standards"></a>Cryptografie en standaarden

### <a name="q-is-it-safe-to-store-encryption-keys-for-my-most-important-data-in-dedicated-hsm"></a>Vraag: is veilig om op te versleutelingssleutels voor de meest belangrijke gegevens opslaan in toegewezen HSM?

Ja, bepalingen toegewezen HSM SafeNet netwerk-HSM-7-apparaten die gebruik FIPS 140-2 Level 3 HSM's gevalideerde. 

### <a name="q-what-cryptographic-keys-and-algorithms-are-supported-by-dedicated-hsm"></a>V: voor welke cryptografische sleutels en de algoritmen worden ondersteund door toegewezen HSM?

Toegewezen HSM-service bepalingen SafeNet netwerk-HSM-7-apparaten. Deze ondersteuning bieden voor een breed scala aan cryptografische sleuteltypen en algoritmen, met inbegrip van: volledige Suite B-ondersteuning

* Asymmetrische:
  * RSA
  * DSA
  * Diffie-Hellman-
  * Elliptic Curve
  * Cryptografie (ECDSA, ECDH, Ed25519, ECIES) met naam, de gebruiker gedefinieerde en Brainpool curven, KCDSA
* Symmetrische:
  * AES-GCM
  * Drievoudig DES
  * DES
  * ARIA, SEED
  * RC2
  * RC4
  * RC5
  * CAST
  * HMAC/hash-/ Message Digest: SHA-1, SHA-2, SM3
  * Belangrijkste afleiding: SP800 108 prestatiemeteritem modus
  * Sleutel verpakken: SP800-38F
  * Genereren van willekeurige getallen: FIPS 140-2 goedgekeurd DRBG (SP 800-90 CTR modus), die voldoet aan Institution DRG.4

### <a name="q-is-dedicated-hsm-fips-140-2-level-3-validated"></a>V: Is toegewezen HSM FIPS die 140-2 Level 3 is gevalideerd?

Ja. Toegewezen HSM-service bepalingen SafeNet netwerk-HSM-7-apparaten die gebruikmaken van FIPS 140-2 Level 3 gevalideerde HSM's.

### <a name="q-what-do-i-need-to-do-to-make-sure-i-operate-dedicated-hsm-in-fips-140-2-level-3-validated-mode"></a>V: wat moet ik doen om ervoor te zorgen dat ik werken toegewezen HSM in FIPS 140-2 Level 3 modus gevalideerd?

De toegewezen HSM-service inricht SafeNet Luna netwerk-HSM 7-apparaten. Deze apparaten gebruik FIPS 140-2 Level 3 gevalideerde HSM's. De standaardconfiguratie geïmplementeerd, het besturingssysteem en de firmware zijn ook FIPS-gevalideerde modules. U hoeft niet te doen voor FIPS 140-2 Level 3-compatibiliteit.

### <a name="q-how-does-a-customer-ensure-that-when-an-hsm-is-deprovisioned-all-the-key-material-is-wiped-out"></a>V: Hoe zorgt een klant ervoor dat wanneer de inrichting van een HSM ongedaan wordt gemaakt, het hoofd materiaal wordt gewist?

Voordat u aanvraagt ongedaan maken van inrichting, moet de HSM Gemalto opgegeven HSM clienthulpprogramma's gebruiken op een klant hebt zeroized.

## <a name="performance-and-scale"></a>Prestaties en schaal

### <a name="q-how-many-cryptographic-operations-are-supported-per-second-with-dedicated-hsm"></a>V: hoe veel cryptografische bewerkingen per seconde met toegewezen HSM worden ondersteund?

Toegewezen HSM bepalingen SafeNet netwerk-HSM-7-apparaten (model A790). Hier volgt een samenvatting van de maximale prestaties voor bepaalde bewerkingen: 

* RSA-2048: 10.000 transacties per seconde
* ECC-P256: 20.000 transacties per seconde
* AES-GCM: 17.000 transacties per seconde

### <a name="q-how-many-partitions-can-be-created-in-dedicated-hsm"></a>V: hoe veel partities kunnen worden gemaakt in de toegewezen HSM?

Het SafeNet Luna HSM 7 model A790 gebruikt een licentie voor 10 partities in de kosten van de service. Het apparaat heeft een limiet van 100 partities en het toevoegen van partities tot deze limiet zou extra licentie kosten in beslag nemen en de installatie van een nieuw licentie bestand op het apparaat vereisen.

### <a name="q-how-many-keys-can-be-supported-in-dedicated-hsm"></a>V: hoe veel sleutels kunnen worden ondersteund in toegewezen HSM?

Het maximum aantal sleutels is een functie van het beschik bare geheugen. Het SafeNet Luna 7 model A790 in gebruik heeft 32 MB geheugen. De volgende nummers zijn ook van toepassing op sleutel paren als asymmetrische sleutels worden gebruikt.

* RSA-2048 - 19.000
* ECC-P256 - 91,000

Capaciteit varieert, afhankelijk van bepaalde belangrijke kenmerken instellen in de sjabloon voor genereren van sleutels en het aantal partities.
