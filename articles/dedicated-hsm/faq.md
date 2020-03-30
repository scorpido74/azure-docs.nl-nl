---
title: Veelgestelde vragen - Azure Dedicated HSM | Microsoft Documenten
description: Veelgestelde vragen over verschillende onderwerpen op Azure Dedicated HSM
services: dedicated-hsm
author: johncdawson
manager: rkarlin
tags: azure-resource-manager
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/05/2020
ms.author: mbaldwin
ms.openlocfilehash: a0cb7957008308425d91abb3e0f828cc40301736
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064930"
---
# <a name="frequently-asked-questions-faq"></a>Veelgestelde vragen

Antwoorden vinden op veelgestelde vragen over Microsoft Azure Dedicated HSM.

## <a name="the-basics"></a>De basisbeginselen

### <a name="q-what-is-a-hardware-security-module-hsm"></a>V: Wat is een hardware beveiligingsmodule (HSM)?

Een Hardware Security Module (HSM) is een fysiek computerapparaat dat wordt gebruikt om cryptografische sleutels te beveiligen en te beheren. Sleutels die zijn opgeslagen in HSM's kunnen worden gebruikt voor cryptografische bewerkingen. Het belangrijkste materiaal blijft veilig in tamper-resistente, tamper-evident hardware modules. De HSM staat alleen geverifieerde en geautoriseerde toepassingen toe om de sleutels te gebruiken. Het belangrijkste materiaal verlaat nooit de HSM-beschermingsgrens.

### <a name="q-what-is-the-azure-dedicated-hsm-offering"></a>V: Wat is het Azure Dedicated HSM-aanbod?

Azure Dedicated HSM is een cloudservice die HSM's biedt die worden gehost in Azure-datacenters en die rechtstreeks zijn verbonden met het virtuele netwerk van een klant. Deze HSM's zijn speciale netwerkapparaten (Gemalto's SafeNet Network HSM 7 Model A790). Ze worden rechtstreeks geïmplementeerd in de privé-IP-adresruimte van een klant en Microsoft heeft geen toegang tot de cryptografische functionaliteit van de HSM's. Alleen de klant heeft volledige administratieve en cryptografische controle over deze apparaten. Klanten zijn verantwoordelijk voor het beheer van het apparaat en ze kunnen volledige activiteitslogboeken rechtstreeks van hun apparaten ontvangen. Speciale HSM's helpen klanten te voldoen aan nalevings-/regelgevingsvereisten zoals FIPS 140-2 Level 3, HIPAA, PCI-DSS en eIDAS en vele anderen.

### <a name="q-what-hardware-is-used-for-dedicated-hsm"></a>V: Welke hardware wordt gebruikt voor Dedicated HSM?

Microsoft werkt samen met Gemalto om de Azure Dedicated HSM-service te leveren. Het specifieke apparaat dat wordt gebruikt is het [SafeNet Luna Network HSM 7 Model A790.](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/) Dit apparaat biedt niet alleen FIPS 140-2 Level 3 gevalideerde firmware, maar biedt ook lage latentie, hoge prestaties en hoge capaciteit via 10 partities. 

### <a name="q-what-is-an-hsm-used-for"></a>V: Waar wordt een HSM voor gebruikt?

HSM's worden gebruikt voor het opslaan van cryptografische sleutels die worden gebruikt voor cryptografische functionaliteit, zoals SSL (secure socket layer), het versleutelen van gegevens, PKI (public key infrastructure), DRM (digital rights management) en het ondertekenen van documenten.

### <a name="q-how-does-dedicated-hsm-work"></a>V: Hoe werkt Dedicated HSM?

Klanten kunnen hsm's in specifieke regio's inrichten met PowerShell of command-line interface. De klant geeft aan op welk virtueel netwerk de HSM's zullen worden aangesloten en zodra de HSM's zijn ingericht, zijn de HSM's beschikbaar in het aangewezen subnet op toegewezen IP-adressen in de privé-IP-adresruimte van de klant. Vervolgens kunnen klanten verbinding maken met de HSM's met Behulp van SSH voor toestelbeheer en -beheer, HSM-clientverbindingen instellen, HSM's initialiseren, partities maken, rollen definiëren en toewijzen, zoals partition officer, crypto-officer en crypto-gebruiker. Dan zal de klant Gemalto meegeleverde HSM client tools / SDK / software gebruiken om cryptografische bewerkingen uit te voeren vanuit hun applicaties.

### <a name="q-what-software-is-provided-with-the-dedicated-hsm-service"></a>V: Welke software wordt geleverd met de Dedicated HSM-service?

Gemalto levert alle software voor het HSM-apparaat eenmaal ingericht door Microsoft. De software is beschikbaar op de [Gemalto customer support portal.](https://supportportal.gemalto.com/csm/) Klanten die gebruik maken van de Dedicated HSM-service moeten worden geregistreerd voor Gemalto-ondersteuning en beschikken over een klant-id waarmee u toegang en download van relevante software mogelijk maakt. De ondersteunde clientsoftware is versie 7.2, die compatibel is met de FIPS 140-2 Level 3 gevalideerde firmwareversie 7.0.3. 

### <a name="q-does-azure-dedicated-hsm-offer-password-based-and-ped-based-authentication"></a>V: Biedt Azure Dedicated HSM verificatie op basis van wachtwoorden en PED?

Op dit moment biedt Azure Dedicated HSM alleen HSM's met verificatie op basis van wachtwoorden.

### <a name="q-will-azure-dedicated-hsm-host-my-hsms-for-me"></a>V: Host Azure Dedicated HSM mijn HSM's voor mij?

Microsoft biedt de Gemalto SafeNet Luna Network HSM alleen via de Dedicated HSM-service en kan geen apparaten van de klant hosten.

### <a name="q-does-azure-dedicated-hsm-support-payment-pineft-features"></a>V: Ondersteunt Azure Dedicated HSM betalingsfuncties (PIN/EFT) ?

De Azure Dedicated HSM-service maakt gebruik van SafeNet Luna Network HSM 7 -apparaten (model A790). Deze apparaten bieden geen ondersteuning voor specifieke betalingshsm-specifieke functionaliteit (zoals pincode of EFT) of certificeringen. Als u wilt dat Azure Dedicated HSM-service in de toekomst de hsm's voor betalingen ondersteunt, geeft u de feedback door aan uw Microsoft-accountvertegenwoordiger.

### <a name="q-which-azure-regions-is-dedicated-hsm-available-in"></a>V: In welke Azure-regio's is Dedicated HSM beschikbaar?

Vanaf eind maart 2019 is Dedicated HSM beschikbaar in de 14 onderstaande regio's. Verdere regio's zijn gepland en kunnen worden besproken via uw Microsoft-accountvertegenwoordiger.

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

### <a name="q-how-does-my-application-connect-to-a-dedicated-hsm"></a>V: Hoe maakt mijn applicatie verbinding met een Dedicated HSM?

U gebruikt gemalto-clienttools/SDK/software om cryptografische bewerkingen uit te voeren vanuit uw toepassingen. De software is beschikbaar op de [Gemalto customer support portal.](https://supportportal.gemalto.com/csm/) Klanten die gebruik maken van de Dedicated HSM-service moeten worden geregistreerd voor Gemalto-ondersteuning en beschikken over een klant-id waarmee u toegang en download van relevante software mogelijk maakt.

### <a name="q-can-an-application-connect-to-dedicated-hsm-from-a-different-vnet-in-or-across-regions"></a>V: Kan een toepassing verbinding maken met Dedicated HSM vanuit een andere VNET in of tussen regio's?

Ja, u moet [VNET-peering](../virtual-network/virtual-network-peering-overview.md) binnen een regio gebruiken om connectiviteit tussen virtuele netwerken tot stand te brengen. Voor connectiviteit tussen regio's moet u [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)gebruiken.

### <a name="q-can-i-synchronize-dedicated-hsm-with-on-premises-hsms"></a>V: Kan ik Dedicated HSM synchroniseren met on-premises HSM's?

Ja, u on-premises HSM's synchroniseren met Dedicated HSM. [Point-to-point VPN of point-to-site](../vpn-gateway/vpn-gateway-about-vpngateways.md) connectiviteit kan worden gebruikt om connectiviteit met uw on-premises netwerk vast te stellen.

### <a name="q-can-i-encrypt-data-used-by-other-azure-services-using-keys-stored-in-dedicated-hsm"></a>V: Kan ik gegevens die door andere Azure-services worden gebruikt versleutelen met sleutels die zijn opgeslagen in Dedicated HSM?

Nee. Azure Dedicated HSM's zijn alleen toegankelijk vanuit uw virtuele netwerk.

### <a name="q-can-i-import-keys-from-an-existing-on-premises-hsm-to-dedicated-hsm"></a>V: Kan ik sleutels importeren van een bestaande on-premises HSM naar Dedicated HSM?

Ja, als u gemalto SafeNet HSM's ter plaatse hebt. Er zijn meerdere methoden. Raadpleeg de Gemalto HSM documentatie.

### <a name="q-what-operating-systems-are-supported-by-dedicated-hsm-client-software"></a>V: Welke besturingssystemen worden ondersteund door Dedicated HSM-clientsoftware?

* Windows, Linux, Solaris, AIX, HP-UX, FreeBSD
* Virtueel: VMware, hyperv, Xen, KVM

### <a name="q-how-do-i-configure-my-client-application-to-create-a-high-availability-configuration-with-multiple-partitions-from-multiple-hsms"></a>V: Hoe configureer ik mijn clienttoepassing om een configuratie met hoge beschikbaarheid te maken met meerdere partities van meerdere HSM's?

Als u een hoge beschikbaarheid wilt hebben, moet u de configuratie van uw HSM-clienttoepassing instellen om partities van elke HSM te gebruiken. Raadpleeg de Documentatie van de Gemalto HSM clientsoftware.

### <a name="q-what-authentication-mechanisms-are-supported-by-dedicated-hsm"></a>V: Welke verificatiemechanismen worden ondersteund door Dedicated HSM?

Azure Dedicated HSM maakt gebruik van SafeNet Network HSM 7-toestellen (model A790) en ondersteunen verificatie op basis van wachtwoorden.

### <a name="q-what-sdks-apis-client-software-is-available-to-use-with-dedicated-hsm"></a>V: Welke SDKs, API's, clientsoftware is beschikbaar voor gebruik met Dedicated HSM?

PKCS#11, Java (JCA/JCE), Microsoft CAPI en CNG, OpenSSL

### <a name="q-can-i-importmigrate-keys-from-luna-56-hsms-to-azure-dedicated-hsms"></a>V: Kan ik sleutels importeren/migreren van Luna 5/6 HSM's naar Azure Dedicated HSM's?

Ja. Raadpleeg de Gemalto migratiegids. 

## <a name="using-your-hsm"></a>Uw HSM gebruiken

### <a name="q-how-do-i-decide-whether-to-use-azure-key-vault-or-azure-dedicated-hsm"></a>V: Hoe bepaal ik of ik Azure Key Vault of Azure Dedicated HSM wil gebruiken?

Azure Dedicated HSM is de juiste keuze voor bedrijven die migreren naar on-premises Azure-toepassingen die HSM's gebruiken. Speciale HSM's bieden een optie om een toepassing te migreren met minimale wijzigingen. Als cryptografische bewerkingen worden uitgevoerd in de code van de toepassing die wordt uitgevoerd in een Azure VM of Web App, kunnen ze Dedicated HSM gebruiken. Over het algemeen worden krimpfoliesoftware uitgevoerd in IaaS-modellen (infrastructure as a service), die HSM's als sleutelarchief ondersteunen, kunnen Dedicated HSM gebruiken, zoals Application gateway of traffic manager voor keyless SSL, ADCS (Active Directory Certificate Services) of vergelijkbare PKI-hulpprogramma's, hulpprogramma's/toepassingen die worden gebruikt voor het ondertekenen van documenten, codeondertekening of een SQL Server (IaaS) geconfigureerd met TDE (transparante databaseversleuteling) met hoofdsleutel in een HSM met behulp van een EKM -leverancier (uitbreidbare sleutelbeheerprovider). Azure Key Vault is geschikt voor 'born-in-cloud'-toepassingen of voor versleuteling in rustscenario's waarbij klantgegevens worden verwerkt door PaaS (platform as a service) of SaaS (Software as a service) scenario's zoals Office 365 Customer Key, Azure Information Protection , Azure Disk Encryption, Azure Data Lake Store-versleuteling met door de klant beheerde sleutel, Azure Storage-versleuteling met door de klant beheerde sleutel en Azure SQL met door de klant beheerde sleutel.

### <a name="q-what-usage-scenarios-best-suit-azure-dedicated-hsm"></a>V: Welke gebruiksscenario's passen het beste bij Azure Dedicated HSM?

Azure Dedicated HSM is het meest geschikt voor migratiescenario's. Dit betekent dat als u on-premises toepassingen migreert naar Azure en die al HSM's gebruiken. Dit biedt een optie voor weinig wrijving om te migreren naar Azure met minimale wijzigingen in de toepassing. Als cryptografische bewerkingen worden uitgevoerd in de code van de toepassing die wordt uitgevoerd in Azure VM of Web App, kan Dedicated HSM worden gebruikt. Over het algemeen kunnen in krimpenverpakte software die wordt uitgevoerd in IaaS-modellen (infrastructure as a service) die HSM's als sleutelwinkel ondersteunen, Dedicated HSM gebruiken, zoals:

* Toepassingsgateway of verkeersbeheerder voor keyless SSL
* ADCS (Active Directory Certificate Services)
* Vergelijkbare PKI-hulpprogramma's
* Hulpprogramma's/toepassingen die worden gebruikt voor documentondertekening
* Code ondertekening
* SQL Server (IaaS) geconfigureerd met TDE (transparante databaseversleuteling) met hoofdsleutel in een HSM met behulp van een EKM-provider (uitbreidbaar sleutelbeheer)

### <a name="q-can-dedicated-hsm-be-used-with-office-365-customer-key-azure-information-protection-azure-data-lake-store-disk-encryption-azure-storage-encryption-azure-sql-tde"></a>V: Kan Dedicated HSM worden gebruikt met Office 365-klantensleutel, Azure Information Protection, Azure Data Lake Store, Schijfversleuteling, Azure Storage-versleuteling, Azure SQL TDE?

Nee. Dedicated HSM is rechtstreeks ingericht in de privé-IP-adresruimte van een klant, zodat deze niet toegankelijk is voor andere Azure- of Microsoft-services.

## <a name="administration-access-and-control"></a>Beheer, toegang en controle

### <a name="q-does-the-customer-get-full-exclusive-control-over-the-hsms-with-dedicated-hsms"></a>V: Krijgt de klant volledige exclusieve controle over de HSM's met Dedicated HSM's?

Ja. Elk HSM-toestel is volledig gewijd aan één klant en niemand anders heeft administratieve controle zodra het wachtwoord is ingericht en het beheerderswachtwoord is gewijzigd.

### <a name="q-what-level-of-access-does-microsoft-have-to-my-hsm"></a>V: Welk toegangsniveau heeft Microsoft tot mijn HSM?

Microsoft heeft geen administratieve of cryptografische controle over de HSM. Microsoft heeft wel toegang tot monitorniveau via seriële poortverbinding om basistelemetrie op te halen, zoals temperatuur en componentstatus. Hierdoor kan Microsoft proactief gezondheidsproblemen melden. Indien nodig kan de klant dit account uitschakelen.

### <a name="q-what-is-the-tenantadmin-account-microsoft-uses-i-am-used-to-the-admin-user-being-admin-on-safenet-hsms"></a>V: Wat is de "tenantadmin" account Microsoft gebruikt, ik ben gewend aan de admin gebruiker wordt "admin" op SafeNet HSMs?

Het HSM-apparaat wordt verzonden met een standaardgebruiker van beheerders met het gebruikelijke standaardwachtwoord. Microsoft wilde geen standaardwachtwoorden in gebruik hebben terwijl een apparaat zich in een pool bevindt die wacht om door klanten te worden ingericht. Dit zou niet voldoen aan onze strenge veiligheidseisen. Daarom stellen we een sterk wachtwoord in, dat wordt weggegooid bij het inrichten van de tijd. Ook maken we op inrichtende tijd een nieuwe gebruiker in de beheerdersrol genaamd "tenantadmin". Deze gebruiker heeft het standaardwachtwoord en klanten wijzigen dit als de eerste actie wanneer ze zich voor het eerst aanmelden bij het nieuw ingerichte apparaat. Dit proces zorgt voor een hoge mate van veiligheid en handhaaft onze belofte van enige administratieve controle voor onze klanten. Opgemerkt moet worden dat de gebruiker van de "tenantadmin" kan worden gebruikt om het beheerdersgebruikerswachtwoord opnieuw in te stellen als een klant dat account verkiest. 

### <a name="q-can-microsoft-or-anyone-at-microsoft-access-keys-in-my-dedicated-hsm"></a>V: Kan Microsoft of iemand bij Microsoft toegangssleutels in mijn Dedicated HSM?

Nee. Microsoft heeft geen toegang tot de sleutels die zijn opgeslagen in door de klant toegewezen Dedicated HSM.

### <a name="q-can-i-upgrade-softwarefirmware-on-hsms-allocated-to-me"></a>V: Kan ik software/firmware upgraden op hsm's die aan mij zijn toegewezen?

Om de beste ondersteuning te krijgen, raadt Microsoft ten zeerste aan om software/firmware op de HSM niet te upgraden. De klant heeft echter wel volledige administratieve controle, inclusief het upgraden van software/firmware als specifieke functies van verschillende firmwareversies vereist zijn. Voordat wijzigingen worden aangebracht, moeten de implicaties worden begrepen, omdat dit bijvoorbeeld van invloed kan zijn op de gevalideerde FIPS-status. 

### <a name="q-how-do-i-manage-dedicated-hsm"></a>V: Hoe beheer ik Dedicated HSM?

U dedicated HSM's beheren door ze te openen met SSH.

### <a name="q-how-do-i-manage-partitions-on-the-dedicated-hsm"></a>V: Hoe beheer ik partities op de Dedicated HSM?

De Gemalto HSM client software wordt gebruikt om de HSM's en partities te beheren.

### <a name="q-how-do-i-monitor-my-hsm"></a>V: Hoe houd ik mijn HSM in de gaten?

Een klant heeft volledige toegang tot HSM-activiteitenlogboeken via syslog en SNMP. Een klant moet een syslog-server of SNMP-server instellen om de logboeken of gebeurtenissen van de HSM's te ontvangen.

### <a name="q-can-i-get-full-access-log-of-all-hsm-operations-from-dedicated-hsm"></a>V: Kan ik volledig toegangslogboek krijgen van alle HSM-bewerkingen van Dedicated HSM?

Ja. U logboeken van het HSM-toestel naar een syslog-server verzenden

## <a name="high-availability"></a>Hoge beschikbaarheid

### <a name="q-is-it-possible-to-configure-high-availability-in-the-same-region-or-across-multiple-regions"></a>V: Is het mogelijk om hoge beschikbaarheid in dezelfde regio of in meerdere regio's te configureren?

Ja. Configuratie en installatie met hoge beschikbaarheid worden uitgevoerd in de HSM-clientsoftware van Gemalto. HSM's van dezelfde VNET of andere VNET's in dezelfde regio of in verschillende regio's, of on-premises HSM's die zijn aangesloten op een VNET met behulp van site-to-site of point-to-point VPN kunnen worden toegevoegd aan dezelfde configuratie met hoge beschikbaarheid. Opgemerkt moet worden dat dit alleen sleutelmateriaal synchroniseert en niet specifieke configuratie-items zoals rollen.

### <a name="q-can-i-add-hsms-from-my-on-premises-network-to-a-high-availability-group-with-azure-dedicated-hsm"></a>V: Kan ik HSM's uit mijn on-premises netwerk toevoegen aan een groep met hoge beschikbaarheid met Azure Dedicated HSM?

Ja. Ze moeten voldoen aan de hoge beschikbaarheidsvereisten voor SafeNet Luna Network HSM 7.

### <a name="q-can-i-add-luna-56-hsms-from-on-premises-networks-to-a-high-availability-group-with-azure-dedicated-hsm"></a>V: Kan ik Luna 5/6 HSM's van on-premises netwerken toevoegen aan een groep met hoge beschikbaarheid met Azure Dedicated HSM?

Nee.

### <a name="q-how-many-hsms-can-i-add-to-the-same-high-availability-configuration-from-one-single-application"></a>V: Hoeveel HSM's kan ik toevoegen aan dezelfde configuratie met hoge beschikbaarheid vanuit één toepassing?

16 leden van een HA-groep hebben onder-gegaan, full-throttle testen met uitstekende resultaten.

## <a name="support"></a>Ondersteuning

### <a name="q-what-is-the-sla-for-dedicated-hsm-service"></a>V: Wat is de SLA voor dedicated HSM-service?

Er is geen specifieke uptime garantie voor de Dedicated HSM-service. Microsoft zorgt ervoor dat toegang op netwerkniveau tot het apparaat wordt gewaarborgd en daarom zijn standaard AZURE-netwerk-SLA's van toepassing.

### <a name="q-how-are-the-hsms-used-in-azure-dedicated-hsm-protected"></a>V: Hoe worden de HSM's gebruikt in Azure Dedicated HSM-beveiliging?

Azure-datacenters hebben uitgebreide fysieke en procedurele beveiligingscontroles. Daarnaast worden dedicated HSM's gehost in een verder beperkt toegangsgebied van het datacenter. Deze gebieden hebben extra fysieke toegangscontroles en videocamerabewaking voor extra beveiliging.

### <a name="q-what-happens-if-there-is-a-security-breach-or-hardware-tampering-event"></a>V: Wat gebeurt er als er een inbreuk op de beveiliging of hardware knoeien gebeurtenis?

Speciale HSM-service maakt gebruik van SafeNet Network HSM 7-apparaten. Deze apparaten ondersteunen fysieke en logische sabotagedetectie. Als er ooit een sabotagegebeurtenis is, worden de HSM's automatisch gezeroiseerd.

### <a name="q-how-do-i-ensure-that-keys-in-my-dedicated-hsms-are-not-lost-due-to-error-or-a-malicious-insider-attack"></a>V: Hoe zorg ik ervoor dat sleutels in mijn Dedicated HSM's niet verloren gaan door fouten of een kwaadaardige insider-aanval?

Het wordt ten zeerste aanbevolen om een on-premises HSM-back-upapparaat te gebruiken om regelmatig periodieke back-ups van de HSM's uit te voeren voor noodherstel. U moet een peer-to-peer- of site-to-site VPN-verbinding gebruiken voor een on-premises werkstation dat is verbonden met een HSM-back-upapparaat.

### <a name="q-how-do-i-get-support-for-dedicated-hsm"></a>V: Hoe krijg ik ondersteuning voor Dedicated HSM?

Ondersteuning wordt geleverd door zowel Microsoft als Gemalto.  Als u een probleem hebt met de hardware- of netwerktoegang, dient u een ondersteuningsverzoek in bij Microsoft en als u een probleem hebt met de HSM-configuratie, software en applicatieontwikkeling, dient u een ondersteuningsverzoek in bij Gemalto. Als u een onbepaald probleem hebt, dient u een ondersteuningsverzoek in bij Microsoft en kan Gemalto naar behoefte worden ingeschakeld. 

### <a name="q-how-do-i-get-the-client-software-documentation-and-access-to-integration-guidance-for-the-safenet-luna-7-hsm"></a>V: Hoe krijg ik de clientsoftware, documentatie en toegang tot integratiebegeleiding voor de SafeNet Luna 7 HSM?

Na registratie voor de service wordt een Gemalto Customer ID verstrekt die registratie mogelijk maakt in het Gemalto customer support portal. Dit zal toegang tot alle software en documentatie mogelijk maken en ondersteuningsverzoeken rechtstreeks met Gemalto mogelijk maken.

### <a name="q-if-there-is-a-security-vulnerability-found-and-a-patch-is-released-by-gemalto-who-is-responsible-for-upgradingpatching-osfirmware"></a>V: Als er een beveiligingslek is gevonden en een patch wordt uitgebracht door Gemalto, wie is er dan verantwoordelijk voor het upgraden/patchen van OS/Firmware?

Microsoft heeft niet de mogelijkheid om verbinding te maken met HSM's die aan klanten zijn toegewezen. Klanten moeten hun HSM's upgraden en patchen.

### <a name="q-what-if-i-need-to-reboot-my-hsm"></a>V: Wat als ik mijn HSM opnieuw moet opstarten?

De HSM heeft een command-line reboot optie, maar we ervaren reboot hang problemen met tussenpozen en om deze reden is het raadzaam voor de veiligste reboot dat u een ondersteuningsverzoek bij Microsoft om het apparaat fysiek opnieuw opgestart. 

## <a name="cryptography-and-standards"></a>Cryptografie en normen

### <a name="q-is-it-safe-to-store-encryption-keys-for-my-most-important-data-in-dedicated-hsm"></a>V: Is het veilig om encryptiesleutels op te slaan voor mijn belangrijkste gegevens in Dedicated HSM?

Ja, speciale HSM-bepalingen SafeNet Network HSM 7-apparaten die FIPS 140-2 Level 3-gevalideerde HSM's gebruiken. 

### <a name="q-what-cryptographic-keys-and-algorithms-are-supported-by-dedicated-hsm"></a>V: Welke cryptografische sleutels en algoritmen worden ondersteund door Dedicated HSM?

Speciale HSM-servicevoorzieningen SafeNet Network HSM 7-apparaten. Ze ondersteunen een breed scala aan cryptografische sleuteltypen en algoritmen, waaronder: Full Suite B-ondersteuning

* Asymmetrische:
  * RSA
  * Dsa
  * Diffie-Hellman Diffie-Hellman
  * Elliptische curve
  * Cryptografie (ECDSA, ECDH, Ed25519, ECIES) met benoemde, door de gebruiker gedefinieerde en Brainpool-curven, KCDSA
* Symmetrische:
  * AES-GCM
  * Drievoudige DES
  * DES
  * ARIA, ZAAD
  * RC2 RC2
  * RC4 RC4
  * RC5 RC5
  * CAST
  * Hash/Message Digest/HMAC: SHA-1, SHA-2, SM3
  * Key Afleiding: SP800-108 Counter Mode
  * Sleutelverpakking: SP800-38F
  * Random Number Generation: FIPS 140-2 approved DRBG (SP 800-90 CTR mode), conform end to BSI DRG.4

### <a name="q-is-dedicated-hsm-fips-140-2-level-3-validated"></a>V: Is Dedicated HSM FIPS 140-2 Level 3 gevalideerd?

Ja. Speciale HSM-servicevoorzieningen SafeNet Network HSM 7-apparaten die FIPS 140-2 Level 3-gevalideerde HSM's gebruiken.

### <a name="q-what-do-i-need-to-do-to-make-sure-i-operate-dedicated-hsm-in-fips-140-2-level-3-validated-mode"></a>V: Wat moet ik doen om ervoor te zorgen dat ik Dedicated HSM in FIPS 140-2 Level 3 gevalideerde modus gebruik?

De speciale HSM-service voorziet in SafeNet Luna Network HSM 7-apparaten. Deze apparaten maken gebruik van FIPS 140-2 Level 3 gevalideerde HSM's. De standaard geïmplementeerde configuratie, het besturingssysteem en de firmware zijn ook FIPS-gevalideerd. U hoeft geen actie te ondernemen voor de naleving van FIPS 140-2 Level 3.

### <a name="q-how-does-a-customer-ensure-that-when-an-hsm-is-deprovisioned-all-the-key-material-is-wiped-out"></a>V: Hoe zorgt een klant ervoor dat wanneer een HSM wordt gedeprovisioneerd al het sleutelmateriaal wordt weggevaagd?

Voordat een klant deprovisioning aanvraagt, moet hij de HSM hebben gezeroiseerd met behulp van De HSM-clienttools.

## <a name="performance-and-scale"></a>Prestaties en schaal

### <a name="q-how-many-cryptographic-operations-are-supported-per-second-with-dedicated-hsm"></a>V: Hoeveel cryptografische bewerkingen worden per seconde ondersteund met Dedicated HSM?

Speciale HSM-voorzieningen SafeNet Network HSM 7 toestellen (model A790). Hier vindt u een overzicht van de maximale prestaties voor sommige bewerkingen: 

* RSA-2048: 10.000 transacties per seconde
* ECC P256: 20.000 transacties per seconde
* AES-GCM: 17.000 transacties per seconde

### <a name="q-how-many-partitions-can-be-created-in-dedicated-hsm"></a>V: Hoeveel partities kunnen worden gemaakt in Dedicated HSM?

De SafeNet Luna HSM 7 model A790 gebruikt omvat een licentie voor 10 partities in de kosten van de dienst. Het apparaat heeft een limiet van 100 partities en het toevoegen van partities tot deze limiet zou extra licentiekosten met zich meebrengen en de installatie van een nieuw licentiebestand op het apparaat vereisen.

### <a name="q-how-many-keys-can-be-supported-in-dedicated-hsm"></a>V: Hoeveel toetsen kunnen worden ondersteund in Dedicated HSM?

Het maximum aantal toetsen is een functie van het beschikbare geheugen. De SafeNet Luna 7 model A790 in gebruik heeft 32MB geheugen. De volgende getallen zijn ook van toepassing op sleutelparen als ze asymmetrische toetsen gebruiken.

* RSA-2048 - 19.000
* ECC-P256 - 91.000

De capaciteit is afhankelijk van specifieke sleutelkenmerken die zijn ingesteld in de sjabloon voor het genereren van sleutels en het aantal partities.
