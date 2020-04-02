---
title: Beveiligingspraktijken voor fabrikanten - Azure IoT Device Provisioning Service
description: Overzichten van algemene beveiligingsprocedures voor OEM's en apparaatfabrikanten die apparaten voorbereiden om zich in te schrijven voor De Azure IoT Device Provisioning Service (DPS).
author: timlt
ms.author: timlt
ms.date: 3/02/2020
ms.topic: conceptual
ms.service: iot-dps
ms.custom: iot-p0-scenario, iot-devices-deviceOEM
ms.reviewer: nberdy
ms.openlocfilehash: 3854f353e4ea0b78c0162681e0b89d37419105d8
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529527"
---
# <a name="security-practices-for-azure-iot-device-manufacturers"></a>Beveiligingspraktijken voor Azure IoT-apparaatfabrikanten
Naarmate meer fabrikanten IoT-apparaten uitbrengen, is het handig om richtlijnen te identificeren rond gangbare praktijken. In dit artikel worden aanbevolen beveiligingsprocedures samengevat waarmee u rekening moet houden wanneer u apparaten produceert voor gebruik met de Azure IoT Device Provisioning Service (DPS).  

> [!div class="checklist"]
> * Opties voor apparaatverificatie selecteren
> * Certificaten installeren op IoT-apparaten
> * Integratie van een Trusted Platform Module (TPM) in het productieproces

## <a name="selecting-device-authentication-options"></a>Opties voor apparaatverificatie selecteren
Het uiteindelijke doel van elke IoT-apparaatbeveiligingsmaatregel is het creëren van een veilige IoT-oplossing. Maar problemen zoals hardwarebeperkingen, kosten en beveiligingsexpertise hebben allemaal invloed op welke opties u kiest. Bovendien heeft uw benadering van beveiliging invloed op de manier waarop uw IoT-apparaten verbinding maken met de cloud. Hoewel er [verschillende elementen van IoT-beveiliging](https://www.microsoft.com/research/publication/seven-properties-highly-secure-devices/) te overwegen, een belangrijk element dat elke klant tegenkomt is wat authenticatie type te gebruiken. 

Drie veelgebruikte verificatietypen zijn X.509-certificaten, Trusted Platform Modules (TPM) en symmetrische sleutels. Hoewel er andere verificatietypen bestaan, gebruiken de meeste klanten die oplossingen bouwen op Azure IoT een van deze drie typen. De rest van dit artikel enquêtes voors en tegens van het gebruik van elk authenticatietype.

### <a name="x509-certificate"></a>X.509-certificaat
X.509-certificaten zijn een soort digitale identiteit die u gebruiken voor verificatie. De X.509 certificaatstandaard is gedocumenteerd in [IETF RFC 5280](https://tools.ietf.org/html/rfc5280). In Azure IoT zijn er twee manieren om certificaten te verifiëren:
- Vingerafdruk. Een algoritme voor duimafdruk wordt uitgevoerd op een certificaat om een hexadecimale tekenreeks te genereren. De gegenereerde tekenreeks is een unieke identifer voor het certificaat. 
- CA-verificatie op basis van een volledige keten. Een certificaatketen is een hiërarchische lijst van alle certificaten die nodig zijn om een EE-certificaat (end-entity) te verifiëren. Als u een EE-certificaat wilt verifiëren, moet elk certificaat in de keten worden geverifieerd, inclusief een vertrouwde hoofd-CA. 

Pluspunten voor X.509:
- X.509 is het veiligste verificatietype dat wordt ondersteund in Azure IoT.
- X.509 maakt een hoog niveau van controle mogelijk ten behoeve van certificaatbeheer.
- Veel leveranciers zijn beschikbaar om op X.509 gebaseerde verificatieoplossingen te bieden.

Nadelen voor X.509:
- Veel klanten moeten mogelijk vertrouwen op externe leveranciers voor hun certificaten.
- Certificaatbeheer kan kostbaar zijn en draagt bij aan de totale oplossingskosten.
- Certificaat levenscyclusbeheer kan moeilijk zijn als de logistiek niet goed doordacht is. 

### <a name="trusted-platform-module-tpm"></a>Trusted Platform Module (TPM)
TPM, ook bekend als [ISO/IEC 11889,](https://www.iso.org/standard/66510.html)is een standaard voor het veilig genereren en opslaan van cryptografische sleutels. TPM verwijst ook naar een virtueel of fysiek I/O-apparaat dat samenwerkt met modules die de standaard implementeren. Een TPM-apparaat kan bestaan als discrete hardware, geïntegreerde hardware, een firmware-gebaseerde module of een softwaregebaseerde module. 

Er zijn twee belangrijke verschillen tussen TPM's en symmetrische toetsen: 
- TPM-chips kunnen ook X.509-certificaten opslaan.
- TPM-attest in DPS maakt gebruik van de TPM-goedkeuringssleutel (EK), een vorm van asymmetrische verificatie. Met asymmetrische verificatie wordt een openbare sleutel gebruikt voor versleuteling en wordt een aparte privésleutel gebruikt voor decryptie. Symmetrische sleutels maken daarentegen gebruik van symmetrische verificatie, waarbij de privésleutel wordt gebruikt voor zowel versleuteling als decryptie. 

Pluspunten voor TPM:
- TPM's zijn opgenomen als standaardhardware op veel Windows-apparaten, met ingebouwde ondersteuning voor het besturingssysteem. 
- TPM-attestation is gemakkelijker te beveiligen dan sas-tokengebaseerde sleutelattest (Shared Access Signature) .TPM attestation is easier to be secure than shared access signature (SAS) token-based symmetric key attestation.
- U eenvoudig verlopen en apparaatreferenties vernieuwen of rollen. DPS rolt automatisch de IoT Hub-referenties wanneer een TPM-apparaat opnieuw moet worden ingericht.

Nadelen voor TPM: 
- TPM's zijn complex en kunnen moeilijk te gebruiken zijn. 
- Applicatieontwikkeling met TPM's is moeilijk, tenzij u een fysieke TPM of een kwaliteitsemulator hebt.
- Mogelijk moet u het bord van uw apparaat opnieuw ontwerpen om een TPM in de hardware op te nemen. 
- Als u de EK op een TPM rolt, vernietigt deze de identiteit van de TPM en wordt een nieuwe gemaakt. Hoewel de fysieke chip hetzelfde blijft, heeft hij een nieuwe identiteit in uw IoT-oplossing.

### <a name="symmetric-key"></a>Symmetrische sleutel
Met symmetrische sleutels wordt dezelfde sleutel gebruikt om berichten te versleutelen en te decoderen. Als gevolg hiervan is dezelfde sleutel bekend bij zowel het apparaat als de service die het verifieert. Azure IoT ondersteunt op SAS-token gebaseerde symmetrische sleutelverbindingen. Symmetrische sleutelverificatie vereist een aanzienlijke verantwoordelijkheid van de eigenaar om de sleutels te beveiligen en een gelijk beveiligingsniveau te bereiken met X.509-verificatie. Als u symmetrische toetsen gebruikt, is het raadzaam om de toetsen te beschermen met behulp van een hardwarebeveiligingsmodule (HSM).

Pluspunten voor symmetrische sleutel:
- Het gebruik van symmetrische sleutels is de eenvoudigste, laagste manier om aan de slag te gaan met authenticatie.
- Het gebruik van symmetrische toetsen stroomlijnt uw proces omdat er niets extra's te genereren zijn. 

Nadelen voor symmetrische sleutel: 
- Symmetrische toetsen nemen een aanzienlijke mate van inspanning om de sleutels te beveiligen. Dezelfde sleutel wordt gedeeld tussen apparaat en cloud, wat betekent dat de sleutel op twee plaatsen moet worden beschermd. In tegenstelling, de uitdaging met TPM en X.509 certificaten is het bewijs van het bezit van de openbare sleutel zonder het onthullen van de private key.
- Symmetrische toetsen maken het gemakkelijk om slechte beveiligingspraktijken te volgen. Een veel voorkomende tendens met symmetrische sleutels is om de onversleutelde sleutels op apparaten hard te coderen. Hoewel deze praktijk handig is, laat het de toetsen kwetsbaar. U risico's beperken door de symmetrische toets op het apparaat veilig op te slaan. Als uw prioriteit uiteindelijk beveiliging in plaats van gemak is, gebruikt u X.509-certificaten of TPM voor verificatie. 

### <a name="shared-symmetric-key"></a>Gedeelde symmetrische sleutel
Er is een variatie van symmetrische sleutelauthenticatie bekend als gedeelde symmetrische sleutel. Deze aanpak omvat het gebruik van dezelfde symmetrische sleutel in alle apparaten. De aanbeveling is om te voorkomen dat het gebruik van gedeelde symmetrische sleutels op uw apparaten. 

Pro voor gedeelde symmetrische sleutel:
- Eenvoudig te implementeren en goedkoop te produceren op schaal. 

Nadelen voor gedeelde symmetrische sleutel: 
- Zeer kwetsbaar voor aanvallen. Het voordeel van een eenvoudige implementatie wordt veel gecompenseerd door het risico. 
- Iedereen kan zich voordoen als uw apparaten als ze de gedeelde sleutel verkrijgen.
- Als u vertrouwt op een gedeelde symmetrische sleutel die wordt aangetast, verliest u waarschijnlijk de controle over de apparaten. 

### <a name="making-the-right-choice-for-your-devices"></a>De juiste keuze maken voor uw apparaten
Als u een verificatiemethode wilt kiezen, moet u rekening houden met de voordelen en kosten van elke aanpak voor uw unieke productieproces.  Voor apparaatverificatie is er meestal een omgekeerde relatie tussen hoe veilig een bepaalde aanpak is en hoe handig deze is.  

## <a name="installing-certificates-on-iot-devices"></a>Certificaten installeren op IoT-apparaten
Als u X.509-certificaten gebruikt om uw IoT-apparaten te verifiëren, biedt deze sectie richtlijnen voor het integreren van certificaten in uw productieproces. Je moet verschillende beslissingen nemen.  Deze omvatten beslissingen over algemene certificaatvariabelen, wanneer u certificaten moet genereren en wanneer ze moeten worden geïnstalleerd. 

Als u gewend bent wachtwoorden te gebruiken, u vragen waarom u niet hetzelfde certificaat op al uw apparaten gebruiken, op dezelfde manier dat u op al uw apparaten hetzelfde wachtwoord gebruiken. Ten eerste, met behulp van hetzelfde wachtwoord overal is gevaarlijk. De praktijk heeft blootgesteld bedrijven aan grote DDoS-aanvallen, met inbegrip van degene die haalde DNS op de Amerikaanse oostkust enkele jaren geleden. Gebruik nooit overal hetzelfde wachtwoord, zelfs niet met persoonlijke accounts. Ten tweede, een certificaat is geen wachtwoord, het is een unieke identiteit. Een wachtwoord is als een geheime code die iedereen kan gebruiken om een deur te openen in een beveiligd gebouw.  Het is iets wat je weet, en je zou het wachtwoord kunnen geven aan iedereen om toegang te krijgen.  Een certificaat is als een rijbewijs met uw foto en andere gegevens, die u laten zien aan een bewaker te krijgen in een beveiligd gebouw. Het hangt samen met wie je bent.  Op voorwaarde dat de bewaker nauwkeurig overeenkomt met mensen met een rijbewijs, alleen u uw rijbewijs (identiteit) gebruiken om toegang te krijgen. 

### <a name="variables-involved-in-certificate-decisions"></a>Variabelen die betrokken zijn bij certificaatbeslissingen
Houd rekening met de volgende variabelen en hoe elk van deze variabelen het totale productieproces beïnvloedt. 

#### <a name="where-the-certificate-root-of-trust-comes-from"></a>Waar de certificaatbasis van vertrouwen vandaan komt
Het kan duur en complex zijn om een openbare sleutelinfrastructuur (PKI) te beheren.  Vooral als uw bedrijf geen ervaring heeft met het beheren van een PKI. Uw opties zijn:
- Gebruik een PKI van derden. U tussentijdse ondertekeningscertificaten kopen bij een externe certificaatleverancier. U ook gebruik maken van een private Certificate Authority (CA). 
- Gebruik een zelfbeheerde PKI. U uw eigen PKI-systeem onderhouden en uw eigen certificaten genereren.
- Gebruik de beveiligingsservice [Azure Sphere.](https://azure.microsoft.com/services/azure-sphere/) Deze optie is alleen van toepassing op Azure Sphere-apparaten. 

#### <a name="where-certificates-are-stored"></a>Waar certificaten worden opgeslagen
Er zijn een paar factoren die van invloed zijn op de beslissing over waar certificaten worden opgeslagen. Deze factoren omvatten het type apparaat, verwachte winstmarges (of u zich veilige opslag veroorloven), apparaatmogelijkheden en bestaande beveiligingstechnologie op het apparaat dat u mogelijk gebruiken. Overweeg de volgende opties:
- In een hardware beveiligingsmodule (HSM). Het gebruik van een HSM is een aanrader. Controleer of op het bedieningsbord van uw apparaat al een HSM is geïnstalleerd. Als u weet dat u geen HSM hebt, werkt u samen met uw hardwarefabrikant om een HSM te identificeren die aan uw behoeften voldoet.
- Op een beveiligde plaats op schijf, zoals een vertrouwde uitvoeringsomgeving (TEE).
- In het lokale bestandssysteem of een certificaatarchief. Bijvoorbeeld het Windows-certificaatarchief. 

#### <a name="connectivity-at-the-factory"></a>Connectiviteit in de fabriek
Connectiviteit in de fabriek bepaalt hoe en wanneer u de certificaten op de apparaten krijgt geïnstalleerd. Connectiviteitsopties zijn als volgt:
- Connectiviteit. Het hebben van connectiviteit is optimaal, het stroomlijnt het proces van het lokaal genereren van certificaten. 
- Geen connectiviteit. In dit geval gebruikt u een ondertekend certificaat van een CA om apparaatcertificaten lokaal en offline te genereren. 
- Geen connectiviteit. In dit geval u certificaten verkrijgen die van tevoren zijn gegenereerd. U ook een offline PKI gebruiken om lokaal certificaten te genereren.

#### <a name="audit-requirement"></a>Controle-eis
Afhankelijk van het type apparaten dat u produceert, hebt u mogelijk een wettelijke verplichting om een controlespoor te maken van hoe apparaatidentiteiten op uw apparaten zijn geïnstalleerd. Auditing brengt aanzienlijke productiekosten met zich mee. Dus in de meeste gevallen, alleen doen als dat nodig is. Als u niet zeker weet of een audit vereist is, neem dan contact op met de juridische afdeling van uw bedrijf. Controleopties zijn: 
- Geen gevoelige industrie. Er is geen controle vereist.
- Gevoelige industrie. Certificaten moeten worden geïnstalleerd in een beveiligde ruimte volgens de vereisten voor nalevingscertificering. Als u een beveiligde ruimte nodig hebt om certificaten te installeren, bent u waarschijnlijk al op de hoogte van de manier waarop certificaten in uw apparaten worden geïnstalleerd. En je hebt waarschijnlijk al een audit systeem op zijn plaats. 

#### <a name="length-of-certificate-validity"></a>Duur van de geldigheidsduur van het certificaat
Net als een rijbewijs hebben certificaten een vervaldatum die is ingesteld wanneer ze worden gemaakt. Hier zijn de opties voor de duur van de geldigheid van het certificaat:
- Verlenging niet vereist.  Deze benadering maakt gebruik van een lange verlengingsperiode, zodat u het certificaat nooit hoeft te verlengen tijdens de levensduur van het apparaat. Hoewel een dergelijke aanpak handig is, is het ook riskant.  U het risico verminderen door veilige opslag te gebruiken zoals een HSM op uw apparaten. De aanbevolen praktijk is echter om te voorkomen dat het gebruik van langlevende certificaten.
- Verlenging vereist.  U moet het certificaat verlengen tijdens de levensduur van het apparaat. De duur van de geldigheid van het certificaat is afhankelijk van de context en u hebt een strategie voor vernieuwing nodig.  De strategie moet omvatten waar u certificaten krijgt en welk type over-the-air-functionaliteit uw apparaten moeten gebruiken in het vernieuwingsproces. 

### <a name="when-to-generate-certificates"></a>Wanneer certificaten genereren
De mogelijkheden voor internetconnectiviteit in uw fabriek hebben invloed op uw proces voor het genereren van certificaten. U hebt verschillende opties voor het genereren van certificaten: 

- Vooraf geladen certificaten.  Sommige HSM-leveranciers bieden een premium service waarbij de HSM-leverancier certificaten voor de klant installeert. Ten eerste geven klanten de HSM-leverancier toegang tot een ondertekeningscertificaat. Vervolgens installeert de HSM-leverancier certificaten die door dat ondertekeningscertificaat zijn ondertekend op elke HSM die de klant koopt. Het enige wat de klant hoeft te doen is het installeren van de HSM op het apparaat. Hoewel deze service kosten toevoegt, helpt het om uw productieproces te stroomlijnen.  En het lost de vraag op wanneer certificaten moeten worden geïnstalleerd.
- Door apparaten gegenereerde certificaten.  Als uw apparaten certificaten intern genereren, moet u het openbare X.509-certificaat uit het apparaat halen om het in te schrijven in DPS. 
- Aangesloten fabriek.  Als uw fabriek connectiviteit heeft, u apparaatcertificaten genereren wanneer u ze nodig hebt.
- Offline fabriek met je eigen PKI. Als uw fabriek geen connectiviteit heeft en u uw eigen PKI gebruikt met offline ondersteuning, u de certificaten genereren wanneer u ze nodig hebt.
- Offline fabriek met PKI van derden. Als uw fabriek geen connectiviteit heeft en u een PKI van derden gebruikt, moet u de certificaten van tevoren genereren. En het zal nodig zijn om de certificaten te genereren vanaf een locatie die connectiviteit heeft. 

### <a name="when-to-install-certificates"></a>Wanneer certificaten installeren
Nadat u certificaten voor uw IoT-apparaten hebt gegenereerd, u deze op de apparaten installeren. 

Als u vooraf geladen certificaten met een HSM gebruikt, wordt het proces vereenvoudigd. Nadat de HSM in het apparaat is geïnstalleerd, heeft de apparaatcode er toegang toe. Vervolgens belt u de HSM API's om toegang te krijgen tot het certificaat dat is opgeslagen in de HSM. Deze aanpak is het handigst voor uw productieproces. 

Als u geen vooraf geladen certificaat gebruikt, moet u het certificaat installeren als onderdeel van uw productieproces. De eenvoudigste aanpak is om het certificaat te installeren in de HSM op hetzelfde moment dat u de eerste firmware-afbeelding knipperen. Uw proces moet een stap toevoegen om de afbeelding op elk apparaat te installeren. Na deze stap u de laatste kwaliteitscontroles en alle andere stappen uitvoeren voordat u het apparaat verpakt en verzendt. 

Er zijn softwaretools beschikbaar waarmee u het installatieproces en de uiteindelijke kwaliteitscontrole in één stap uitvoeren. U deze gereedschappen wijzigen om een certificaat te genereren of om een certificaat uit een vooraf gegenereerd certificaatarchief te halen. Dan kan de software het certificaat installeren waar u het moet installeren. Met dit soort softwaretools u productiekwaliteit op schaal uitvoeren. 

Nadat u certificaten op uw apparaten hebt geïnstalleerd, is de volgende stap om te leren hoe u de apparaten inschrijven bij [DPS.](about-iot-dps.md) 

## <a name="integrating-a-tpm-into-the-manufacturing-process"></a>Een TPM integreren in het productieproces
Als u een TPM gebruikt om uw IoT-apparaten te verifiëren, biedt deze sectie richtlijnen. De richtlijnen hebben betrekking op de veelgebruikte TPM 2.0-apparaten die hmac-sleutelondersteuning (hash-based message authentication code) hebben. De TPM-specificatie voor TPM-chips is een ISO-standaard die wordt onderhouden door de Trusted Computing Group. Zie voor meer informatie over TPM de specificaties voor [TPM 2.0](https://trustedcomputinggroup.org/tpm-library-specification/) en [ISO/IEC 11889](https://www.iso.org/standard/66510.html). 

### <a name="taking-ownership-of-the-tpm"></a>Eigenaar worden van de TPM
Een cruciale stap in de productie van een apparaat met een TPM-chip is om de eigendom van de TPM te nemen. Deze stap is vereist, zodat u een sleutel verstrekken aan de eigenaar van het apparaat. De eerste stap is om de goedkeuringssleutel (EK) uit het apparaat te halen. De volgende stap is om daadwerkelijk aanspraak te maken op eigendom. Hoe u dit bereikt, hangt af van welk TPM en besturingssysteem u gebruikt. Neem indien nodig contact op met de TPM-fabrikant of de ontwikkelaar van het besturingssysteem voor het apparaat om te bepalen hoe u het eigendom claimen. 

In uw productieproces u de EK extraheren en op verschillende tijdstippen eigenaar worden, wat flexibiliteit toevoegt. Veel fabrikanten profiteren van deze flexibiliteit door een hardware beveiligingsmodule (HSM) toe te voegen om de beveiliging van hun apparaten te verbeteren. In dit gedeelte vindt u richtlijnen over wanneer u de EK moet extraheren, wanneer u het eigendom van de TPM moet claimen, en overwegingen voor het integreren van deze stappen in een productietijdlijn. 

> [!IMPORTANT]
> In de volgende richtlijnen wordt ervan uitgegaan dat u een discrete firmware of geïntegreerde TPM gebruikt. Op plaatsen waar het van toepassing is, voegt de richtlijnen notities toe over het gebruik van een niet-discrete of software-TPM. Als u een software-TPM gebruikt, kunnen er aanvullende stappen zijn die deze richtlijnen niet bevatten. Software TPM's hebben een verscheidenheid aan implementaties die buiten het bereik van dit artikel vallen.  In het algemeen is het mogelijk om een software TPM te integreren in de volgende algemene productietijdlijn. Hoewel een software geëmuleerde TPM geschikt is voor prototypen en testen, kan het niet hetzelfde beveiligingsniveau bieden als een discrete, firmware of geïntegreerde TPM. Als een algemene praktijk, vermijd het gebruik van een software TPM in productie.

### <a name="general-manufacturing-timeline"></a>Tijdlijn algemene productie
In de volgende tijdlijn ziet u hoe een TPM een productieproces doorloopt en in een apparaat terechtkomt. Elk productieproces is uniek en deze tijdlijn toont de meest voorkomende patronen. De tijdlijn biedt richtlijnen over wanneer bepaalde acties met de toetsen moeten worden uitgevoerd. 

#### <a name="step-1-tpm-is-manufactured"></a>Stap 1: TPM wordt vervaardigd
- Als u TPM's bij een fabrikant koopt voor gebruik in uw apparaten, moet u zien of ze openbare goedkeuringssleutels (EK_pubs) voor u extraheren. Het is handig als de fabrikant de lijst met EK_pubs met de verzonden apparaten levert. 
    > [!NOTE]
    > U de TPM-fabrikant schrijftoegang geven tot uw inschrijvingslijst met behulp van beleid voor gedeelde toegang in uw inrichtingsservice.  Met deze aanpak kunnen ze de TPM's voor u toevoegen aan uw inschrijvingslijst.  Maar dat is vroeg in het productieproces, en het vereist vertrouwen in de TPM fabrikant. Doe dit op eigen risico. 

- Als u TPM's produceert om te verkopen aan fabrikanten van apparaten, u overwegen uw klanten een lijst met EK_pubs samen met hun fysieke TPM's te geven.  Het bieden van EK_pubs aan klanten bespaart een stap in hun proces. 
- Als u TPM's produceert om te gebruiken met uw eigen apparaten, u bepalen welk punt in uw proces het handigst is om de EK_pub te extraheren. U de EK_pub op een van de resterende punten in de tijdlijn extraheren. 

#### <a name="step-2-tpm-is-installed-into-a-device"></a>Stap 2: TPM is geïnstalleerd in een apparaat
Op dit punt in het productieproces moet u weten met welke DPS-instantie het apparaat wordt gebruikt. Als gevolg hiervan u apparaten toevoegen aan de inschrijvingslijst voor geautomatiseerde inrichting. Zie de [DPS-documentatie](about-iot-dps.md)voor meer informatie over het inrichten van automatische apparaten.
- Als u niet hebt gehaald de EK_pub, nu is een goed moment om dit te doen. 
- Afhankelijk van het installatieproces van de TPM is deze stap ook een goed moment om eigenaar te worden van de TPM. 

#### <a name="step-3-device-has-firmware-and-software-installed"></a>Stap 3: Apparaat heeft firmware en software geïnstalleerd
Installeer op dit moment in het proces de DPS-client samen met de ID-scope en de globale URL voor inprovisioning.
- Nu is de laatste kans om de EK_pub te halen. Als een derde partij de software op uw apparaat installeert, is het een goed idee om eerst de EK_pub te extraheren.
- Dit punt in het productieproces is ideaal om eigenaar te worden van de TPM.  
    > [!NOTE]
    > Als u een software-TPM gebruikt, u deze nu installeren.  Haal tegelijkertijd de EK_pub.

#### <a name="step-4-device-is-packaged-and-sent-to-the-warehouse"></a>Stap 4: Apparaat wordt verpakt en naar het magazijn verzonden
Een apparaat kan 6-12 maanden in een magazijn zitten voordat het wordt geïmplementeerd. 

#### <a name="step-5-device-is-installed-into-the-location"></a>Stap 5: Apparaat is geïnstalleerd op de locatie
Nadat het apparaat op de definitieve locatie is aangekomen, gaat het door middel van geautomatiseerde inrichting met DPS.

Zie [Autoprovisioning concepts](concepts-auto-provisioning.md) en [TPM attestation](concepts-tpm-attestation.md)voor meer informatie. 

## <a name="resources"></a>Resources

Naast de aanbevolen beveiligingsprocedures in dit artikel biedt Azure IoT resources om te helpen bij het selecteren van veilige hardware en het maken van veilige IoT-implementaties: 
- Azure [IoT-beveiligingsaanbevelingen](../iot-fundamentals/security-recommendations.md) om het implementatieproces te begeleiden. 
- Het [Azure Security Center](https://azure.microsoft.com/services/security-center/) biedt een service om veilige IoT-implementaties te maken. 
- Zie de whitepaper Evaluatie van uw [IoT-beveiliging](https://download.microsoft.com/download/D/3/9/D3948E3C-D5DC-474E-B22F-81BA8ED7A446/Evaluating_Your_IOT_Security_whitepaper_EN_US.pdf)voor hulp bij het evalueren van uw hardwareomgeving. 
- Zie De juiste beveiligde [hardware voor uw IoT-implementatie voor](https://download.microsoft.com/download/C/0/5/C05276D6-E602-4BB1-98A4-C29C88E57566/The_right_secure_hardware_for_your_IoT_deployment_EN_US.pdf)hulp bij het selecteren van beveiligde hardware. 