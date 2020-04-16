---
title: Verificatie op basis van x.509-certificaten in een cluster van servicestructuur
description: Meer informatie over verificatie op basis van certificaten in servicefabricclusters en hoe u certificaatgerelateerde problemen detecteren, beperken en oplossen.
ms.topic: conceptual
ms.date: 03/16/2020
ms.custom: sfrev
ms.openlocfilehash: 699015e322c599dea996b3a8b9dbc0a4589440ab
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429666"
---
# <a name="x509-certificate-based-authentication-in-service-fabric-clusters"></a>X.509 Verificatie op basis van certificaten in servicefabricclusters

Dit artikel is een aanvulling op de inleiding tot [servicestructuurclusterbeveiliging](service-fabric-cluster-security.md)en gaat in op de details van op certificaten gebaseerde verificatie in servicefabricclusters. We gaan ervan uit dat de lezer bekend is met fundamentele beveiligingsconcepten, en ook met de besturingselementen die Service Fabric blootstelt om de beveiliging van een cluster te controleren.  

Onderwerpen die onder deze titel:

* Basisbeginselen voor verificatie op basis van certificaten
* Identiteiten en hun respectieve rollen
* Certificaatconfiguratieregels
* Probleemoplossing en veelgestelde vragen

## <a name="certificate-based-authentication-basics"></a>Basisbeginselen voor verificatie op basis van certificaten
Als een korte opfriscursus, in veiligheid, een certificaat is een instrument bedoeld om informatie over een entiteit (het onderwerp) te binden aan hun bezit van een paar asymmetrische cryptografische sleutels, en dus vormt een kernconstructie van public key cryptografie. De sleutels die door een certificaat worden vertegenwoordigd, kunnen worden gebruikt voor de bescherming van gegevens of voor het aantonen van de identiteit van sleutelhouders; wanneer een certificaat wordt gebruikt in combinatie met een PKI-systeem (Public Key Infrastructure), kan een certificaat aanvullende kenmerken van het onderwerp vertegenwoordigen, zoals de eigendom van een internetdomein of bepaalde bevoegdheden die de uitgever van het certificaat (bekend als certificeringsinstantie of CA) aan het certificaat heeft verleend. Een veelgebruikte toepassing van certificaten ondersteunt het TLS-protocol (Transport Layer Security), waardoor veilige communicatie via een computernetwerk mogelijk is. Specifiek, de client en server gebruiken certificaten om de privacy en integriteit van hun communicatie te waarborgen, en ook om wederzijdse authenticatie uit te voeren.

In Service Fabric bouwt de fundamentele laag van een cluster (Federation) ook voort op TLS (onder andere protocollen) om een betrouwbaar, veilig netwerk van deelnemende knooppunten te bereiken. Verbindingen in het cluster via Service Fabric Client API's gebruiken TLS ook om het verkeer te beschermen, en ook om de identiteit van de partijen vast te stellen. Wanneer een certificaat wordt gebruikt voor verificatie in Service Fabric, kan een certificaat worden gebruikt om de volgende beweringen te bewijzen: a) de presentator van de certificaatreferentie in het bezit is van de privésleutel van het certificaat b) de SHA-1-hash van het certificaat ('duimafdruk') komt overeen met een verklaring die is opgenomen in de clusterdefinitie, of c) de voorname algemene naam van het certificaat komt overeen met een verklaring die is opgenomen in de clusterdefinitie , en de uitgever van het certificaat is bekend of vertrouwd.

In de bovenstaande lijst wordt "b" in de volksmond 'duimafdrukpinning' genoemd; in dit geval verwijst de verklaring naar een specifiek certificaat en berust de sterkte van het authenticatieschema op de veronderstelling dat het computationeel onhaalbaar is om een certificaat te vervalsen dat dezelfde hashwaarde produceert als een ander certificaat, terwijl het in alle andere opzichten nog steeds een geldig, goed gevormd object is. Punt "c" is een alternatieve vorm van het verklaren van een certificaat, waarbij de sterkte van de regeling berust op de combinatie van het voorwerp van het certificaat en de instantie van afgifte. In dit geval verwijst de verklaring naar een klasse van certificaten - twee certificaten met dezelfde kenmerken worden als volledig gelijkwaardig beschouwd. 

In de volgende secties wordt uitgebreid uitgelegd hoe de runtime van de Service Fabric certificaten gebruikt en valideert om de clusterbeveiliging te garanderen.

## <a name="identities-and-their-respective-roles"></a>Identiteiten en hun respectieve rollen
Voordat u indedetails van authenticatie of het beveiligen van communicatiekanalen, is het belangrijk om de deelnemende actoren en de bijbehorende rollen die zij spelen in een cluster op te sommen:
- de runtime van de Service Fabric, aangeduid als 'systeem': de reeks services die de abstracties en functionaliteit bieden die het cluster vertegenwoordigen. Wanneer we verwijzen naar in-clustercommunicatie tussen systeeminstanties, gebruiken we de term 'clusteridentiteit'; Wanneer we verwijzen naar het cluster als de ontvanger/het doel van verkeer van buiten het cluster, gebruiken we de term 'serveridentiteit'.
- gehoste toepassingen, aangeduid als 'toepassingen': code die wordt verstrekt door de eigenaar van het cluster, die wordt georkestreerd en uitgevoerd in het cluster
- clients: entiteiten die verbinding kunnen maken met en functionaliteit in een cluster kunnen uitvoeren, afhankelijk van de clusterconfiguratie. We maken onderscheid tussen twee niveaus van privileges - 'gebruiker' en 'admin', respectievelijk. Een 'gebruikersclient' is voornamelijk beperkt tot alleen-lezen bewerkingen (maar niet alle alleen-lezen functionaliteit), terwijl een 'admin'-client onbeperkte toegang heeft tot de functionaliteit van het cluster. (Raadpleeg [beveiligingsrollen in een cluster servicestructuur](service-fabric-cluster-security-roles.md)voor meer informatie.)
- (Azure-only) de Service Fabric-services die besturingselementen orkestreren en blootleggen voor de werking en het beheer van Service Fabric-clusters, simpelweg 'service' genoemd. Afhankelijk van de omgeving kan de 'service' verwijzen naar de Azure Service Fabric Resource Provider of andere Resource Providers die eigendom zijn van en worden beheerd door het Service Fabric-team.

In een beveiligd cluster kan elk van deze rollen worden geconfigureerd met hun eigen, afzonderlijke identiteit, gedeclareerd als het koppelen van een vooraf gedefinieerde rolnaam en de bijbehorende referentie. Service Fabric ondersteunt het declareren van referenties als certificaten of domeingebaseerde serviceprincipal. (Op Windows/Kerberos gebaseerde identiteiten worden ook ondersteund, maar vallen buiten het bereik van dit artikel; raadpleeg [windows-gebaseerde beveiliging in servicefabricclusters](service-fabric-windows-cluster-windows-security.md).) In Azure-clusters kunnen clientrollen ook worden gedeclareerd als [azure Active Directory-gebaseerde identiteiten](service-fabric-cluster-creation-setup-aad.md).

Zoals hierboven wordt gezinspeeld, definieert de runtime van Service Fabric twee niveaus van bevoegdheden in een cluster: 'admin' en 'user'. Een beheerdersclient en een 'systeemcomponent' werken beide met 'beheerdersrechten' en zijn dus niet van elkaar te onderscheiden. Bij het tot stand brengen van een verbinding in/met het cluster, wordt een geverifieerde beller verleend door de Runtime van de Service Fabric als basis voor de volgende autorisatie. We onderzoeken de verificatie in de volgende secties.

## <a name="certificate-configuration-rules"></a>Certificaatconfiguratieregels
### <a name="validation-rules"></a>Validatieregels
De beveiligingsinstellingen van een Service Fabric-cluster beschrijven in principe de volgende aspecten:
- het verificatietype; dit is een creatietijd, onveranderlijk kenmerk van het cluster. Voorbeelden van dergelijke instellingen zijn 'ClusterCredentialType', 'ServerCredentialType' en toegestane waarden zijn 'none', 'x509' of 'windows'. Dit artikel richt zich op de x509-type authenticatie.
- de validatieregels (authenticatie); deze instellingen worden ingesteld door de clustereigenaar en beschrijven welke referenties voor een bepaalde rol worden geaccepteerd. Voorbeelden zullen direct hieronder grondig worden onderzocht.
- instellingen die worden gebruikt om het resultaat van verificatie te tweaken of subtiel te wijzigen; voorbeelden hier zijn vlaggen (de-)beperken de handhaving van certificaat intrekking lijsten etc.

> [!NOTE]
> Voorbeelden van clusterconfiguratie hieronder zijn fragmenten uit het clustermanifest in XML-indeling, als de meest verteerbare indeling die direct de servicestructuurfunctionaliteit ondersteunt die in dit artikel wordt beschreven. Dezelfde instellingen kunnen rechtstreeks worden uitgedrukt in de JSON-weergaven van een clusterdefinitie, of het nu gaat om een zelfstandig json-clustermanifest of een Azure Resource Mangement-sjabloon.

Een validatieregel voor certificaten bevat de volgende elementen:
- de bijbehorende rol: client, admin client (privileged role)
- de voor de rol geaccepteerde referentie, aangegeven door duimafdruk of algemene naam

#### <a name="thumbprint-based-certificate-validation-declarations"></a>Op duimafdruk gebaseerde certificaatvalidatiedeclaratie
In het geval van validatieregels op basis van duimafdruk worden de referenties die worden gepresenteerd door een beller die een verbinding in/met het cluster aanvraagt, als volgt gevalideerd:
  - de referentie is een geldig, goed gevormd certificaat: de keten kan worden gebouwd, handtekeningen overeenkomen
  - het certificaat is tijd geldig (NotBefore <= nu < NotAfter)
  - de SHA-1-hash van het certificaat komt overeen met de verklaring, als een hoofdletter-ongevoelige tekenreeksvergelijking met uitzondering van alle witruimten

Vertrouwensfouten die zich voordoen tijdens het bouwen of valideren van kettingen, worden onderdrukt voor op duimdruk gebaseerde declaratie, met uitzondering van verlopen certificaten - hoewel er ook voor die aanvraag bepalingen bestaan. In het bijzonder worden fouten die verband houden met: intrekkingsstatus die onbekend of offline zijn, niet-vertrouwde root, ongeldig sleutelgebruik, gedeeltelijke keten worden beschouwd als niet-fatale fouten; het uitgangspunt, in dit geval, is dat het certificaat is slechts een envelop voor een sleutelpaar - de veiligheid ligt in het feit dat de cluster eigenaar heeft ingesteld op plaatsen maatregel om de prive-sleutel te beschermen.

Het volgende fragment uit een clustermanifest is een voorbeeld van een dergelijke set op duimdruk gebaseerde validatieregels:

```xml
<Section Name="Security">
  <Parameter Name="ClusterCredentialType" Value="X509" />
  <Parameter Name="ServerAuthCredentialType" Value="X509" />
  <Parameter Name="AdminClientCertThumbprints" Value="d5ec...4264" />
  <Parameter Name="ClientCertThumbprints" Value="7c8f...01b0" />
  <Parameter Name="ClusterCertThumbprints" Value="abcd...1234,ef01...5678" />
  <Parameter Name="ServerCertThumbprints" Value="ef01...5678" />
</Section>
```

Elk van de bovenstaande vermeldingen verwijzen naar een specifieke identiteit zoals eerder beschreven; elk item maakt het ook mogelijk om meerdere waarden op te geven, als door komma's gescheiden lijst met tekenreeksen. In dit voorbeeld, bij het succesvol valideren van de inkomende referenties, de presentator van een certificaat met de SHA-1 duimafdruk 'd5ec... 4264' krijgt de rol van "admin"; omgekeerd, een beller authenticeren met certificaat '7c8f ... 01b0' krijgt een 'gebruikersrol' die beperkt is tot voornamelijk alleen-lezen bewerkingen. Een inkomende beller die een certificaat presenteert waarvan de duimafdruk is ofwel 'abcd ... 1234' of 'ef01... 5678' wordt geaccepteerd als een peer node in het cluster. Ten slotte verwacht een client die verbinding maakt met een beheereindpunt van het cluster dat de duimafdruk van het servercertificaat 'ef01... 5678'. 

Zoals eerder vermeld, stelt Service Fabric wel bepalingen voor het accepteren van verlopen certificaten; de reden hiervoor is dat certificaten een beperkte levensduur hebben en, wanneer aangegeven door duimafdruk (die verwijst naar een specifieke certificaatinstantie), waardoor een certificaat verloopt, zal resulteren in ofwel het niet verbinden met het cluster, of een regelrechte ineenstorting van het cluster. Het is maar al te gemakkelijk om te vergeten of te verwaarlozen het draaien van een duimafdruk-vastgemaakt certificaat, en helaas het herstel van een dergelijke situatie is moeilijk.

Daartoe kan de clustereigenaar uitdrukkelijk aangeven dat zelfondertekende certificaten die door duimafdruk zijn aangegeven, als volgt als geldig worden beschouwd:

```xml
  <Section Name="Security">
    <Parameter Name="AcceptExpiredPinnedClusterCertificate" Value="true" />
  </Section>
```
Dit gedrag strekt zich niet uit tot door CA uitgegeven certificaten; als dat het geval zou zijn, zou een ingetrokken, tot nu toe ingetrokken verlopen certificaat 'geldig' kunnen worden zodra het niet meer in de certificaatintrekkingslijst van de CA zou staan en dus een beveiligingsrisico zou opleveren. Met zelfondertekende certificaten wordt de clustereigenaar beschouwd als de enige partij die verantwoordelijk is voor het beveiligen van de privésleutel van het certificaat, wat niet het geval is met door CA uitgegeven certificaten - de clustereigenaar is zich mogelijk niet bewust van hoe of wanneer zijn certificaat is gedeclareerd.

#### <a name="common-name-based-certificate-validation-declarations"></a>Gemeenschappelijke certificaatvalidatiedeclaratie op basis van naam
Gemeenschappelijke naamgebonden verklaringen nemen een van de volgende formulieren aan:
- algemene naam onderwerp (alleen)
- gemeenschappelijke naam met uitgevende instelling vastmaken

Laten we eerst een fragment uit een clustermanifest overwegen dat beide declaratiestijlen illustreren:
```xml
    <Section Name="Security/ServerX509Names">
      <Parameter Name="server.demo.system.servicefabric.azure-int" Value="" />
    </Section>
    <Section Name="Security/ClusterX509Names">
      <Parameter Name="cluster.demo.system.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
```
De verklaringen verwijzen naar respectievelijk de server- en clusteridentiteiten; merk op dat de cn-gebaseerde verklaringen hun eigen secties in het clustermanifest hebben, los van de standaard 'Beveiliging'. In beide aangiften vertegenwoordigt de 'Naam' de gemeenschappelijke naam van het certificaat en vertegenwoordigt het veld 'Waarde' de verwachte emittent als volgt:

- in het eerste geval wordt in de verklaring gesteld dat het gemeenschappelijke naamelement van het gedistingeerde onderwerp van het servercertificaat naar verwachting overeenkomt met de tekenreeks "server.demo.system.servicefabric.azure-int"; het lege veld 'Waarde' geeft de verwachting aan dat de hoofdmap van de certificaatketen wordt vertrouwd op het knooppunt/de machine waar het servercertificaat wordt gevalideerd; op Windows betekent dit dat het certificaat kan worden geketend tot een van de certificaten die zijn geïnstalleerd in het 'Trusted Root CA'-archief;
- in het tweede geval wordt in de verklaring vermeld dat de presentator van een certificaat wordt geaccepteerd als een peer-node in het cluster als de algemene naam van het certificaat overeenkomt met de tekenreeks "cluster.demo.system.servicefabric.azure-int", *en* de duimafdruk van de directe uitgever van het certificaat overeenkomt met een van de door komma's gescheiden vermeldingen in het veld 'Waarde'. (Dit regeltype wordt in de volksmond 'gemeenschappelijke naam met emittentpinning' genoemd.)

In beide gevallen wordt de keten van het certificaat gebouwd en zal naar verwachting foutloos zijn; dat wil zeggen dat intrekkingsfouten, gedeeltelijke keten- of tijdongeldig vertrouwensfouten als fataal worden beschouwd en de certificaatvalidatie mislukt. Het vastzetten van de emittenten zal resulteren in het beschouwen van de 'niet-vertrouwde root'-status als een niet-fatale fout; ondanks de schijn is dit een striktere vorm van validatie, omdat de clustereigenaar hierdoor de set geautoriseerde/geaccepteerde emittenten kan beperken tot hun eigen PKI.

Nadat de certificaatketen is gebouwd, wordt deze gevalideerd op basis van een standaard TLS/SSL-beleid met het gedeclareerde onderwerp als de externe naam; een certificaat wordt als een overeenkomst beschouwd als de gemeenschappelijke naam van het onderwerp of een van de alternatieve namen van het onderwerp overeenkomt met de GN-verklaring uit het clustermanifest. Jokertekens worden in dit geval ondersteund en de tekenreeksmatching is hoofdletters ongevoelig.

(We moeten verduidelijken dat de bovenstaande volgorde kan worden uitgevoerd voor elk type sleutelgebruik dat door het certificaat wordt gedeclareerd; als het certificaat het gebruik van de clientverificatiesleutel opgeeft, wordt de keten eerst gebouwd en geëvalueerd voor een clientrol. In geval van succes is de evaluatie voltooid en is de validatie succesvol. Als het certificaat niet het gebruik van clientverificatie heeft of als de validatie is mislukt, wordt de runtime van Service Fabric gebouwd en geëvalueerd voor serververificatie.)

Als u het voorbeeld wilt voltooien, wordt in het volgende fragment de verklaring van clientcertificaten met algemene naam geïllustreerd:
```xml
    <Section Name="Security/AdminClientX509Names">
      <Parameter Name="admin.demo.client.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
    <Section Name="Security/ClientX509Names">
      <Parameter Name="user.demo.client.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
```

De bovenstaande verklaringen komen overeen met respectievelijk de beheerders- en gebruikersidentiteiten; validatie van certificaten die op deze manier zijn gedeclareerd, is precies zoals beschreven in de vorige voorbeelden van cluster- en servercertificaten.

> [!NOTE]
> Gemeenschappelijke naamgebaseerde declaraties zijn bedoeld om de rotatie te vereenvoudigen en in het algemeen het beheer van clustercertificaten. Het wordt echter aanbevolen om de volgende aanbevelingen na te leven om de voortdurende beschikbaarheid en beveiliging van het cluster te garanderen:
  * liever uitgevende instellingen vastpinnen boven vertrouwen op vertrouwde wortels
  * vermijd het mengen van emittenten van verschillende PK's
  * ervoor te zorgen dat alle verwachte emittenten op de certificaatverklaring worden vermeld; een niet-overeenkomende emittent zal resulteren in een mislukte validatie
  * ervoor te zorgen dat de eindpunten van het certificaatbeleid van de PKI vindbaar, beschikbaar en toegankelijk zijn - dit betekent dat de AIA-, CRL- of OCSP-eindpunten op het bladcertificaat worden gedeclareerd en dat ze toegankelijk zijn zodat het gebouw van de certificaatketen kan worden voltooid.

Als u alles bij elkaar houdt, gebruikt de Runtime van de Service Fabric de beveiligingsinstellingen van het cluster om de referenties van de externe partij zoals hierboven beschreven te valideren wanneer u het geheel bij elkaar houdt. als dit lukt, wordt de beller/externe partij als geverifieerd beschouwd. Als de referentie overeenkomt met meerdere validatieregels, geeft de runtime de beller de hoogst bevoorrechte rol van een van de overeenkomende regels. 

### <a name="presentation-rules"></a>Presentatieregels
In de vorige sectie werd beschreven hoe verificatie werkt in een cluster met certificaatbeheer. in deze sectie wordt uitgelegd hoe de runtime van de Service Fabric zelf de certificaten detecteert en laadt die het gebruikt voor in-clustercommunicatie; we noemen dit de "presentatie" regels.

Net als bij de validatieregels geven de presentatieregels een rol en de bijbehorende referentieverklaring op, uitgedrukt door duimafdruk of gemeenschappelijke naam. In tegenstelling tot de validatieregels hebben gemeenschappelijke op naam gebaseerde verklaringen geen voorzieningen voor het vastmaken van uitgevende instellingen; dit zorgt voor meer flexibiliteit en betere prestaties. De presentatieregels worden aangegeven in de sectie 'NodeType' van het clustermanifest, voor elk afzonderlijk knooppunttype; de instellingen worden gesplitst van de beveiligingssecties van het cluster, zodat elk knooppunttype zijn volledige configuratie in één sectie kan hebben. In azure servicefabric-clusters worden de certificaatdeclaratie van het knooppunttype standaard weergegeven in de bijbehorende instellingen in de sectie Beveiliging van de definitie van het cluster.

#### <a name="thumbprint-based-certificate-presentation-declarations"></a>Verklaringen voor certificaatpresentatie op basis van duimafdruk
Zoals eerder beschreven, maakt de runtime van de servicestructuur onderscheid tussen de rol als peer van andere knooppunten in het cluster en als de server voor clusterbeheerbewerkingen. In principe kunnen deze instellingen duidelijk worden geconfigureerd, maar in de praktijk hebben ze de neiging om uit te lijnen. Voor de rest van dit artikel gaan we ervan uit dat de instellingen overeenkomen met eenvoud.

Laten we eens kijken naar het volgende fragment uit een clustermanifest:
```xml
  <NodeTypes>
    <NodeType Name="nt1vm">
      <Certificates>
        <ClusterCertificate X509FindType="FindByThumbprint" X509FindValue="cc71...1984" X509FindValueSecondary="49e2...19d6" X509StoreName="my" Name="ClusterCertificate" />
        <ServerCertificate X509FindValue="cc71...1984" Name="ServerCertificate" />
        <ClientCertificate X509FindValue="cc71...1984" Name="ClientCertificate" />
      </Certificates>
    </NodeType>
  </NodeTypes>
```
Het element 'Clustercertificaat' toont het volledige schema, inclusief optionele parameters ('X509FindValueSecondary') of parameters met de juiste standaardwaarden ('X509StoreName'). de andere aangiften een verkorte vorm vertonen. In de bovenstaande clustercertificaatverklaring staat dat de beveiligingsinstellingen van knooppunten van het type 'nt1vm' zijn geïnitialiseerd met certificaat 'cc71.. 1984' als de primaire, en de '49e2.. 19d6' certificaat als secundair; beide certificaten worden verwacht te vinden\'in de LocalMachine My' certificaat op te slaan (of de Linux equivalent pad, *var / lib / sfcerts*).

#### <a name="common-name-based-certificate-presentation-declarations"></a>Gemeenschappelijke certificaatpresentatieverklaringen op basis van naam
De certificaten van het knooppunttype kunnen ook worden aangegeven op gemeenschappelijke onderwerpnaam, zoals hieronder wordt geïllustreerd:

```xml
  <NodeTypes>
    <NodeType Name="nt1vm">
      <Certificates>
        <ClusterCertificate X509FindType="FindBySubjectName" X509FindValue="demo.cluster.azuredocpr.system.servicefabric.azure-int" Name="ClusterCertificate" />
      </Certificates>
    </NodeType>
  </NodeTypes>
```

Voor beide typen declaratie leest een servicefabricknooppunt de configuratie bij het opstarten, lokaliseren en laden van de opgegeven certificaten en sorteert deze in aflopende volgorde van het kenmerk NotAfter; verlopen certificaten worden genegeerd en het eerste element van de lijst wordt geselecteerd als de clientreferentie voor een Service Fabric-verbinding die door dit knooppunt wordt geprobeerd. (In feite is Service Fabric voorstander van het verst verlopende certificaat.)

Houd er rekening mee dat een certificaat voor presentatieverklaringen met een gemeenschappelijke naam wordt beschouwd als een overeenkomst als de algemene naam van het onderwerp gelijk is aan het veld X509FindValue (of X509FindValueSecondary) van de aangifte als een hoofdlettergevoelige, exacte tekenreeksvergelijking. Dit in tegenstelling tot de validatieregels, die wildcardmatching ondersteunen, evenals case-ongevoelige tekenreeksvergelijkingen.  

### <a name="miscellaneous-certificate-configuration-settings"></a>Diverse certificeringsconfiguratie-instellingen
Eerder werd al gezegd dat de beveiligingsinstellingen van een Service Fabric-cluster het ook mogelijk maken om het gedrag van de verificatiecode subtiel te wijzigen. Hoewel het artikel over [de clusterinstellingen](service-fabric-cluster-fabric-settings.md) van Service Fabric de uitgebreide en meest up-to-date lijst met instellingen vertegenwoordigt, zullen we de betekenis van een select aantal van de beveiligingsinstellingen hier uitbreiden om de volledige weergave van verificatie op basis van certificaten te voltooien. Voor elke instelling leggen we de intentie, standaardwaarde/gedrag uit, hoe deze van invloed is op verificatie en welke waarden acceptabel zijn.

Zoals gezegd impliceert certificaatvalidatie altijd het bouwen en evalueren van de keten van het certificaat. Voor door CA uitgegeven certificaten brengt deze ogenschijnlijk eenvoudige OS API-aanroep meestal meerdere uitgaande oproepen met zich mee naar verschillende eindpunten van de uitgevende PKI, het incacheen van reacties enzovoort. Gezien de prevalentie van certificaatvalidatieoproepen in een Service Fabric-cluster, kunnen eventuele problemen in de eindpunten van de PKI resulteren in een verminderde beschikbaarheid van het cluster of een regelrechte uitsplitsing. Hoewel de uitgaande oproepen niet kunnen worden onderdrukt (zie hieronder in de faq-sectie voor meer informatie over dit), kunnen de volgende instellingen worden gebruikt om validatiefouten te maskeren die worden veroorzaakt door falende CRL-oproepen.

  * CrlCheckingFlag - onder de sectie 'Beveiliging' wordt tekenreeks omgezet naar UINT. De waarde van deze instelling wordt door Service Fabric gebruikt om statusfouten in de certificaatketen te maskeren door het gedrag van ketengebouw te wijzigen; het wordt doorgegeven aan de Win32 CryptoAPI [CertGetCertificateChain-aanroep](https://docs.microsoft.com/windows/win32/api/wincrypt/nf-wincrypt-certgetcertificatechain) als de parameter 'dwFlags' en kan worden ingesteld op elke geldige combinatie van vlaggen die door de functie worden geaccepteerd. Een waarde van 0 dwingt de Runtime van de Service Fabric om fouten in de vertrouwensstatus te negeren - dit wordt niet aanbevolen, omdat het gebruik ervan een aanzienlijke beveiligingsblootstelling zou vormen. De standaardwaarde is 0x40000000 (CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT).

  Wanneer te gebruiken: voor lokale tests, met zelfondertekende certificaten of ontwikkelaarscertificaten die niet volledig zijn gevormd/ niet over een goede infrastructuur voor openbare sleutels beschikken om de certificaten te ondersteunen. Kan ook worden gebruikt als mitigatie in lucht-gapped omgevingen tijdens de overgang tussen PKI's.

  Hoe te gebruiken: we nemen een voorbeeld dat intrekkingscontrole dwingt om alleen toegang te krijgen tot url's in de cache. Ervan uitgaande dat:
  ```C++
  #define CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY         0x80000000
  ```
  dan wordt de aangifte in het clustermanifest:
  ```xml
    <Section Name="Security">
      <Parameter Name="CrlCheckingFlag" Value="0x80000000" />
    </Section>
  ```

  * IgnoreCrlOfflineError - onder de sectie 'Beveiliging' booleaan met een standaardwaarde van 'false'. Vertegenwoordigt een snelkoppeling voor het onderdrukken van de foutstatus 'intrekking offline' van de keten (of een foutstatus van een volgende ketenbeleidsvalidatie).

  Wanneer te gebruiken: lokale tests, of met ontwikkelaarscertificaten die niet worden ondersteund door een goede PKI. Gebruik als mitigatie in lucht-gapped omgevingen of wanneer bekend is dat de PKI ontoegankelijk is.

  Hoe te gebruiken:
  ```xml
    <Section Name="Security">
      <Parameter Name="IgnoreCrlOfflineError" Value="true" />
    </Section>
  ```

  Andere opmerkelijke instellingen (allemaal onder de sectie 'Beveiliging'):
  * AcceptExpiredPinnedClusterCertificate - besproken in de sectie gewijd aan op duimdruk gebaseerde certificaatvalidatie; hiermee kan verlopen zelfondertekende clustercertificaten worden geaccepteerd. 
  * CertificateExpirySafetyMargin - interval, uitgedrukt in minuten voorafgaand aan de NotAfter-tijdstempel van het certificaat en gedurende welke het certificaat wordt beschouwd als risico voor vervaldatum. Service Fabric controleert clustercertificaten(en) en geeft periodiek gezondheidsrapporten over de resterende beschikbaarheid. Binnen het interval 'veiligheid' worden deze gezondheidsrapporten verhoogd tot de status 'waarschuwing'. De standaardinstelling is 30 dagen.
  * CertificateHealthReportingInterval - regelt de frequentie van statusrapporten met betrekking tot de resterende tijdgeldigheid van clustercertificaten. Rapporten worden slechts één keer per dit interval uitgestraald. De waarde wordt uitgedrukt in seconden, met een standaard van 8 uur.
  * EnforcePrevalidationOnSecurityChanges - booleaan, regelt het gedrag van de clusterupgrade bij het detecteren van wijzigingen in beveiligingsinstellingen. Als de clusterupgrade is ingesteld op 'true', wordt geprobeerd ervoor te zorgen dat ten minste één van de certificaten die overeenkomen met een van de presentatieregels, een overeenkomstige validatieregel kan passeren. De prevalidatie wordt uitgevoerd voordat de nieuwe instellingen worden toegepast op een knooppunt, maar wordt alleen uitgevoerd op het knooppunt dat de primaire replica van de clusterbeheerservice host op het moment dat de upgrade wordt gestart. Met betrekking tot dit schrijven, de instelling heeft een standaard van 'false', en zal worden ingesteld op 'true' voor nieuwe Azure Service Fabric clusters met een runtime versie te beginnen met 7.1.
 
### <a name="end-to-end-scenario-examples"></a>End-to-end scenario (voorbeelden)
We hebben gekeken naar presentatieregels, validatieregels en tweaken de vlaggen, maar hoe werkt dit allemaal samen? In deze sectie werken we door middel van twee end-to-end voorbeelden die laten zien hoe de beveiligingsinstellingen kunnen worden gebruikt voor veilige clusterupgrades. Merk op dat dit niet bedoeld is als een uitgebreid proefschrift over goed certificaatbeheer in Service Fabric, zoek naar een begeleidend artikel over dat onderwerp.

De scheiding van presentatie- en validatieregels stelt de voor de hand liggende vraag (of bezorgdheid) van de vraag of ze kunnen uiteenlopen, en wat de gevolgen zouden zijn. Het is inderdaad mogelijk dat de selectie van een verificatiecertificaat door een knooppunt niet door de validatieregels van een ander knooppunt gaat. In feite is deze discrepantie de belangrijkste oorzaak van authenticatie-gerelateerde incidenten. Tegelijkertijd zorgt de scheiding van deze regels ervoor dat een cluster kan blijven werken tijdens een upgrade die de beveiligingsinstellingen van het cluster wijzigt. Bedenk dat, door eerst de validatieregels als eerste stap uit te breiden, alle knooppunten van het cluster zullen convergeren op de nieuwe instellingen terwijl ze nog steeds de huidige referenties gebruiken. 

Bedenk dat in een cluster van ServiceFabric een upgrade wordt uitgevoerd via (maximaal 5) 'upgradedomeinen' of UD's. Alleen knooppunten in de huidige UD worden op een bepaald moment geüpgraded/gewijzigd en de upgrade gaat alleen door naar de volgende UD als de beschikbaarheid van het cluster dit toelaat. (Raadpleeg [clusterupgrades van Service Fabric](service-fabric-cluster-upgrade.md) en andere artikelen over hetzelfde onderwerp voor meer informatie.) Certificaat-/beveiligingswijzigingen zijn bijzonder riskant, omdat ze knooppunten uit het cluster kunnen isoleren of het cluster aan de rand van quorumverlies kunnen verlaten.

We gebruiken de volgende aantekening om de beveiligingsinstellingen van een knooppunt te beschrijven:

Nk: {P:{TP=A}, V:{TP=A}}, waar:
  - 'Nk' vertegenwoordigt een knooppunt in upgradedomein *k*
  - 'P' vertegenwoordigt de huidige presentatieregels van het knooppunt (ervan uitgaande dat we alleen naar clustercertificaten verwijzen); 
  - 'V' vertegenwoordigt de huidige validatieregels van het knooppunt (alleen clustercertificaat)
  - 'TP=A' staat voor een op duimdruk gebaseerde declaratie (TP), waarbij 'A' een certificaat duimafdruk is
  - "CN=B" vertegenwoordigt een gemeenschappelijke op naam gebaseerde verklaring (GN), waarbij "B" de gemeenschappelijke naam van het certificaat is 

#### <a name="rotating-a-cluster-certificate-declared-by-thumbprint"></a>Een clustercertificaat roteren dat is opgegeven met duimafdruk
In de volgende reeks wordt beschreven hoe een upgrade in 2 fasen kan worden gebruikt om veilig een secundair clustercertificaat te introduceren, gedeclareerd door duimafdruk; de eerste fase introduceert de nieuwe certificaatverklaring in de validatieregels en de tweede fase introduceert deze in de presentatieregels:
  - beginstatus: N0 = {P:{TP=A}, V:{TP=A}}, ... Nk = {P:{TP=A}, V:{TP=A}} - het cluster is in rust, alle knooppunten delen een gemeenschappelijke configuratie
  - bij het voltooien van upgradedomein 0: N0 = {P:{TP=A}, V:{TP=A, TP=B}}, ... Nk = {P:{TP=A}, V:{TP=A}} - knooppunten in UD0 presenteren certificaat A en accepteren certificaten A of B; alle andere knooppunten aanwezig en accepteren certificaat A alleen
  - bij het voltooien van het laatste upgradedomein: N0 = {P:{TP=A}, V:{TP=A, TP=B}}, ... Nk = {P:{TP=A}, V:{TP=A, TP=B}} - alle knooppunten presenteren certificaat A, alle knooppunten accepteren certificaat A of B
      
Op dit punt is het cluster weer in evenwicht en kan de tweede fase van de upgrade/veranderende beveiligingsinstellingen beginnen:
  - bij het voltooien van upgradedomein 0: N0 = {P:{TP=A, TP=B}, V:{TP=A, TP=B}}, ... Nk = {P:{TP=A}, V:{TP=A, TP=B}} - knooppunten in UD0 beginnen met het presenteren van B, die wordt geaccepteerd door een ander knooppunt in het cluster.
  - bij het voltooien van het laatste upgradedomein: N0 = {P:{TP=A, TP=B}, V:{TP=A, TP=B}}, ... Nk = {P:{TP=A, TP=B}, V:{TP=A, TP=B}} - alle knooppunten zijn overgestapt op het presenteren van certificaat B. Certificaat A kan nu worden teruggetrokken/verwijderd uit de clusterdefinitie met een volgende set upgrades.

#### <a name="converting-a-cluster-from-thumbprint--to-common-name-based-certificate-declarations"></a>Een cluster converteren van duimafdruk naar certificaatdeclaratie met algemene naam
Op dezelfde manier volgt het wijzigen van het type certificaatdeclaratie (van duimafdruk naar algemene naam) hetzelfde patroon als hierboven. Houd er rekening mee dat validatieregels het mogelijk maken om de certificaten van een bepaalde rol aan te geven door zowel duimafdruk als gemeenschappelijke naam in dezelfde clusterdefinitie. De presentatieregels staan daarentegen slechts één vorm van aangifte toe. Overigens is de veilige benadering van het converteren van een clustercertificaat van duimafdruk naar gemeenschappelijke naam om het beoogde doelcertificaat eerst door duimafdruk te introduceren en die verklaring vervolgens te wijzigen in een gemeenschappelijke naamgebaseerde certificaat. In het volgende voorbeeld gaan we ervan uit dat duimafdruk 'A' en de gemeenschappelijke naam 'B' naar hetzelfde certificaat verwijzen. 

  - beginstatus: N0 = {P:{TP=A}, V:{TP=A}}, ... Nk = {P:{TP=A}, V:{TP=A}} - het cluster is in rust, alle knooppunten delen een gemeenschappelijke configuratie, waarbij A de primaire duimafdruk van het certificaat is
  - bij het voltooien van upgradedomein 0: N0 = {P:{TP=A}, V:{TP=A, CN=B}}, ... Nk = {P:{TP=A}, V:{TP=A}} - knooppunten in UD0 presenteren certificaat A en accepteren certificaten met duimafdruk A of gemeenschappelijke naam B; alle andere knooppunten aanwezig en accepteren certificaat A alleen
  - bij het voltooien van het laatste upgradedomein: N0 = {P:{TP=A}, V:{TP=A, CN=B}}, ... Nk = {P:{TP=A}, V:{TP=A, CN=B}} - alle knooppunten presenteren certificaat A, alle knooppunten accepteren certificaat A (TP) of B (CN)

Op dit punt kunnen we doorgaan met het wijzigen van de presentatieregels met een volgende upgrade:
  - bij het voltooien van upgradedomein 0: N0 = {P:{CN=B}, V:{TP=A, CN=B}}, ... Nk = {P:{TP=A}, V:{TP=A, CN=B}} - knooppunten in UD0 presenteren certificaat B gevonden door CN en accepteren certificaten met duimafdruk A of gemeenschappelijke naam B; alle andere knooppunten aanwezig en accepteren certificaat A alleen, geselecteerd door duimafdruk
  - bij het voltooien van het laatste upgradedomein: N0 = {P:{CN=B}, V:{TP=A, CN=B}}, ... Nk = {P:{CN=B}, V:{TP=A, CN=B}} - alle knooppunten presenteren certificaat B gevonden door CN, alle knooppunten accepteren certificaat A (TP) of B (CN)
    
Voltooiing van fase 2 markeert ook de conversie van het cluster naar gemeenschappelijke op naam gebaseerde certificaten; de validatiedeclaratie op basis van duimafdruk kan worden verwijderd in een volgende clusterupgrade.

> [!NOTE]
> In azure servicefabricclusters worden de bovenstaande werkstromen georkestreerd door de Service Fabric Resource Provider. de clustereigenaar is nog steeds verantwoordelijk voor het inrichten van certificaten in het cluster volgens de aangegeven regels (presentatie of validatie) en wordt aangemoedigd om wijzigingen in meerdere stappen uit te voeren.

In een apart artikel gaan we in op het onderwerp van het beheren en inrichten van certificaten in een Service Fabric-cluster.

## <a name="troubleshooting-and-frequently-asked-questions"></a>Probleemoplossing en veelgestelde vragen
Hoewel het debuggen van verificatiegerelateerde problemen in Service Fabric-clusters niet eenvoudig is, zijn we hoopvol over de volgende tips en tips die kunnen helpen. De eenvoudigste manier om onderzoeken te starten is om de gebeurtenislogboeken van De Structuur van de Service op de knooppunten van het cluster te onderzoeken - niet noodzakelijkerwijs alleen die met symptomen, maar ook knooppunten die omhoog zijn, maar niet in staat zijn om verbinding te maken met een van hun buren. Op Windows worden gebeurtenissen van betekenis meestal geregistreerd onder respectievelijk de kanalen 'Applications and Services Logs\Microsoft-ServiceFabric\Admin' of 'Operational'. Soms kan het nuttig zijn om [CAPI2 logging in te schakelen,](https://docs.microsoft.com/archive/blogs/benjaminperkins/enable-capi2-event-logging-to-troubleshoot-pki-and-ssl-certificate-issues)om meer details over de certificaatvalidatie vast te leggen, ophalen van CRL / CTL etc. (Vergeet niet om het uit te schakelen na het voltooien van de repro, kan het heel verbose.)

Typische symptomen die zich manifesteren in een cluster met verificatieproblemen zijn: 
  - knooppunten zijn down/cycling 
  - verbindingspogingen worden afgewezen
  - verbindingspogingen zijn timing uit

Elk van de symptomen kan worden veroorzaakt door verschillende problemen, en dezelfde oorzaak kan verschillende manifestaties vertonen; als zodanig, we zullen gewoon een lijst van een kleine steekproef van typische problemen, met aanbevelingen voor de vaststelling van hen. 

* Knooppunten kunnen berichten uitwisselen, maar kunnen geen verbinding maken. Een mogelijke oorzaak voor verbindingspogingen om te worden beëindigd is de fout 'certificaat dat niet overeenkomt' - een van de partijen in een Service Fabric-to-Service Fabric-verbindingen presenteert een certificaat dat niet voldoet aan de validatieregels van de ontvanger. Kan vergezeld gaan van een van de volgende fouten: 
  ```C++
  0x80071c44    -2147017660 FABRIC_E_SERVER_AUTHENTICATION_FAILED
  ```
  Om verder te diagnosticeren/ te onderzoeken: op elk van de knooppunten die de verbinding proberen, bepaalt u welk certificaat wordt gepresenteerd; het certificaat te onderzoeken en de validatieregels te emuleren (controleer op duimafdruk of algemene naamgelijkheid, controleer de duimafdrukken van de uitgever indien opgegeven).

  Een andere veel voorkomende begeleidende foutcode kan zijn:
  ```C++
  0x800b0109    -2146762487 CERT_E_UNTRUSTEDROOT
  ```
  In dit geval wordt het certificaat met gemeenschappelijke naam aangegeven en is een van de volgende vermeldingen van toepassing:
    - de emittenten niet zijn vastgemaakt en het basiscertificaat wordt niet vertrouwd, of
    - de emittenten zijn vastgepind, maar de verklaring bevat niet de duimafdruk van de directe emittent van dit certificaat

* Een knooppunt is omhoog, maar kan geen verbinding maken met andere knooppunten; andere knooppunten ontvangen geen binnenkomend verkeer van het falende knooppunt. In dit geval is het mogelijk dat het laden van het certificaat mislukt op het lokale knooppunt. Zoek naar de volgende fouten:
  - certificaat niet gevonden - zorg ervoor dat de certificaten die zijn aangegeven in de presentatieregels kunnen worden opgelost door de inhoud van het localmachine\Mijn (of zoals opgegeven) certificaatarchief. 
    Mogelijke oorzaken voor het falen kunnen zijn: 
      - ongeldige tekens in de verklaring duimafdruk
      - het certificaat is niet geïnstalleerd
      - het certificaat is verlopen
      - de gemeenschappelijke naamverklaring bevat het voorvoegsel "CN="
      - de verklaring geeft een wildcard op en er bestaat geen exacte overeenkomst in de cert-winkel (declaratie: CN=*.mydomain.com, daadwerkelijk certificaat: CN=server.mydomain.com)

  - onbekende referenties - geeft een ontbrekende privésleutel aan die overeenkomt met het certificaat, meestal vergezeld van foutcode: 
    ```C++ 
    0x8009030d  -2146893043 SEC_E_UNKNOWN_CREDENTIALS
    0x8009030e  -2146893042 SEC_E_NO_CREDENTIALS
    ```
    Om te verhelpen, controleer het bestaan van de private key; controleren of SFAdmins toegang krijgt tot de privésleutel.verify SFAdmins is granted 'read|execute' access to the private key.

  - slechte provider type - geeft een Crypto New Generation (CNG) certificaat ("Microsoft Software Key Storage Provider"); Op dit moment ondersteunt Service Fabric alleen CAPI1-certificaten. Meestal vergezeld van foutcode:
    ```C++
    0x80090014  -2146893804 NTE_BAD_PROV_TYPE
    ```
    Om het clustercertificaat opnieuw te maken met behulp van een CAPI1-provider (bijvoorbeeld "Microsoft Enhanced RSA en AES Cryptographic Provider") provider. Voor meer informatie over cryptoproviders, [raadpleegt u Cryptografische providers begrijpen](https://docs.microsoft.com/windows/win32/seccertenroll/understanding-cryptographic-providers)

