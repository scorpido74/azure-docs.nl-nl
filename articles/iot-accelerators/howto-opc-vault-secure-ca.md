---
title: De OPC kluis Certificate Management-service veilig uitvoeren-Azure | Microsoft Docs
description: Hierin wordt beschreven hoe u de OPC kluis Certificate Management-service veilig kunt uitvoeren in Azure en andere beveiligings richtlijnen om rekening mee te houden.
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: b08358680793ccdadca27c5f2aa57fbffe89b53a
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69973790"
---
# <a name="how-to-run-the-opc-ua-certificate-management-service-securely"></a>De OPC UA Certificate Management-service veilig uitvoeren

In dit artikel wordt uitgelegd hoe u de OPC UA Certificate Management-service veilig kunt uitvoeren in Azure en andere beveiligings richtlijnen waarmee u rekening moet houden.

## <a name="roles"></a>Rollen

### <a name="trusted-and-authorized-roles"></a>Vertrouwde en geautoriseerde rollen

De OPC kluis micro service is zo geconfigureerd dat afzonderlijke rollen toegang hebben tot verschillende onderdelen van de service.

> [!IMPORTANT]
> Tijdens de implementatie voegt het script alleen de gebruiker toe die het implementatie script uitvoert als een gebruiker voor alle rollen.
> Deze roltoewijzing moet worden geëvalueerd voor een productie-implementatie en opnieuw worden geconfigureerd volgens de onderstaande richt lijnen.
> Deze taak vereist hand matige toewijzing van rollen en services in de Azure AD-portal voor bedrijfs toepassingen.

### <a name="certificate-management-service-roles"></a>Service rollen voor certificaat beheer

De micro service definieert de volgende rollen:

- **Lezer**: Een geverifieerde gebruiker in de Tenant heeft standaard lees toegang. 
  - Lees toegang tot toepassingen en certificaat aanvragen. Kan toepassingen en certificaat aanvragen weer geven en er query's op uitvoeren. Detectie gegevens van apparaten en open bare certificaten zijn ook toegankelijk met lees toegang.
- **Schrijver**: De rol schrijver wordt toegewezen aan een gebruiker om schrijf machtigingen voor bepaalde taken toe te voegen. 
  - Lees-/schrijftoegang tot toepassingen en certificaat aanvragen. Kan toepassingen registreren, bijwerken en verwijderen. Kan certificaat aanvragen maken en goedgekeurde persoonlijke sleutels en certificaten verkrijgen. Kan ook persoonlijke sleutels verwijderen.
- **Goed keurder**: De rol van de goed keurder wordt toegewezen aan een gebruiker om certificaat aanvragen goed te keuren of af te wijzen. De rol bevat geen andere functies.
  - Naast de rol fiatteur om toegang te krijgen tot de OPC kluis micro Service-API, moet de gebruiker ook beschikken over de machtiging voor sleutel ondertekening in Key Vault om de certificaten te kunnen ondertekenen.
  - De rol schrijver en goed keurder moet aan verschillende gebruikers worden toegewezen.
  - De belangrijkste rol van de goed keurder is het goed keuren van het genereren en afkeuren van certificaat aanvragen.
- **Beheerder**: De beheerdersrol wordt toegewezen aan een gebruiker om de certificaat groepen te beheren. De rol biedt geen ondersteuning voor de functie goed keurder, maar bevat de rol schrijver.
  - De beheerder kan de certificaat groepen beheren, de configuratie wijzigen en toepassings certificaten intrekken door een nieuwe CRL uit te geven.
  - IDEA liter, schrijver, goed keurder en beheerders rollen worden toegewezen aan verschillende gebruikers. Voor extra beveiliging moet een gebruiker met goed keurder of beheerder ook de machtiging voor het ondertekenen van sleutels in de sleutel kluis verlenen voor het uitgeven van certificaten of het vernieuwen van een CA-certificaat van de verlener.
  - Naast de rol van micro Service-beheer, bevat de rol ook maar niet beperkt tot:
    - Verantwoordelijk voor het beheer van de implementatie van de beveiligings procedures van de CA.
    - Beheer van het genereren, intrekken en opschorten van certificaten. 
    - Beheer van de levens cyclus van cryptografische sleutels (bijvoorbeeld de verlenging van de CA-sleutels van de uitgever).
    - Installatie, configuratie en onderhoud van services die de CA uitvoeren.
    - Dagelijkse werking van de services. 
    - Certificerings instantie en back-up en herstel van de data base.

### <a name="other-role-assignments"></a>Andere roltoewijzingen

De volgende rollen moeten ook worden overwogen en toegewezen wanneer de service wordt uitgevoerd:

- Zakelijke eigenaar van het certificaat voor de inkoop van certificaten met de externe basis certificerings instantie (in het geval dat de eigenaar certificaten koopt van een externe CA of een CA ondergaat die ondergeschikt is aan een externe CA).
- Ontwikkeling en validatie van de certificerings instantie.
- Controle records controleren.
- Mede werkers die ondersteuning bieden voor de CA of het beheren van de fysieke en Cloud faciliteiten, maar niet rechtstreeks worden vertrouwd om CA-bewerkingen uit te voeren, worden gedefinieerd als de gemachtigde rol. De set taken die in de gemachtigde rol kunnen worden uitgevoerd, moet ook worden gedocumenteerd.

### <a name="memberships-of-trusted-and-authorized-roles-must-be-reviewed-annually"></a>Lidmaatschappen van vertrouwde en geautoriseerde rollen moeten jaarlijks worden beoordeeld

Het lidmaatschap van vertrouwde en geautoriseerde rollen moet ten minste één kwar taal worden gecontroleerd om ervoor te zorgen dat de set gebruikers (voor hand matige processen) of service-identiteiten (voor automatische processen) in elke rol tot een minimum wordt beperkt.

### <a name="certificate-issuance-process-must-enforce-role-separation-between-certificate-requester-and-approver"></a>Het certificaat uitgifte proces moet functie scheiding afdwingen tussen de certificaat aanvrager en de goed keurder

Het certificaat uitgifte proces moet functie scheiding afdwingen tussen de certificaat aanvrager en de rollen van de goed keurder van het certificaat (personen of geautomatiseerde systemen). Certificaat uitgifte moet worden geautoriseerd door een rol van een certificaat goed keurder die verifieert of de certificaat aanvrager toestemming heeft om certificaten te verkrijgen. De personen die de rol van certificaat goed keurder hebben, moeten een formeel gemachtigde persoon zijn.

### <a name="privileged-role-management-must-restrict-access-and-be-reviewed-quarterly"></a>Privileged Role Management moet de toegang beperken en per kwar taal worden geëvalueerd

Toewijzing van geprivilegieerde rollen, zoals lidmaatschap van de groep Administrators en goed keurders, moet worden beperkt tot een beperkt aantal geautoriseerde mede werkers. Alle wijzigingen van geprivilegieerde rollen moeten binnen 24 uur toegang hebben ingetrokken. Ten slotte moeten geprivilegieerde roltoewijzingen op elk kwar taal worden gecontroleerd en moeten eventuele overbodige of verlopen toewijzingen worden verwijderd.

### <a name="privileged-roles-should-use-two-factor-authentication"></a>Geprivilegieerde rollen moeten gebruikmaken van twee ledige verificatie

Multi-factor Authentication (twee ledige verificatie, MFA of TFA) moet worden gebruikt voor interactieve aanmeldingen van goed keurders en beheerders voor de service.

## <a name="certificate-service-operation-guidelines"></a>Richt lijnen voor de werking van de certificaat service

### <a name="operational-contacts"></a>Operationele contact personen

De Certificate-Service moet een up-to-date beveiligings antwoord plan hebben dat gedetailleerde contact personen voor operationele incidenten bevat.

### <a name="security-updates"></a>Beveiligingsupdates

Alle systemen moeten voortdurend worden bewaakt en bijgewerkt met de nieuwste beveiligings updates/patch naleving.

> [!IMPORTANT]
> De GitHub-opslag plaats van de OPC-kluis service wordt voortdurend bijgewerkt met beveiligings patches. De updates op GitHub moeten worden bewaakt en de updates worden op regel matige intervallen toegepast op de service.

### <a name="security-monitoring"></a>Beveiligingsbewaking

Abonneer u op of implementeer de juiste beveiligings bewaking, bijvoorbeeld door u te abonneren op een centrale bewakings oplossing (bijvoorbeeld Azure Security Center, O365-bewakings oplossing) en deze op de juiste wijze te configureren om ervoor te zorgen dat beveiligings gebeurtenissen worden verzonden naar de bewakings oplossing.

> [!IMPORTANT]
> Standaard wordt de OPC-kluis service geïmplementeerd met de [Azure-toepassing inzichten](https://docs.microsoft.com/azure/azure-monitor/app/devops) als een bewakings oplossing. Het is raadzaam om een beveiligings oplossing toe te voegen, zoals [Azure Security Center](https://azure.microsoft.com/services/security-center/) .

### <a name="assess-security-of-open-source-software-components"></a>Beveiliging van open-source software onderdelen beoordelen

Alle open source-onderdelen die in een product of service worden gebruikt, mogen geen beveiligings problemen met een gemiddelde of een grotere beveiliging hebben.

> [!IMPORTANT]
> De GitHub-opslag plaats van de OPC kluis-service is het scannen van alle onderdelen tijdens doorlopende integratie builds voor beveiligings problemen. De updates op GitHub moeten worden bewaakt en de updates worden op regel matige intervallen toegepast op de service.

### <a name="maintain-an-inventory"></a>Een inventaris onderhouden

Een inventarisatie van een activum moet worden onderhouden voor alle productie-hosts (met inbegrip van permanente virtuele machines), apparaten, alle interne IP-adresbereiken, Vip's en open bare DNS-domein namen. Deze inventaris moet worden bijgewerkt met toevoeging of verwijdering van een systeem, IP-adres van apparaat, VIP of openbaar DNS-domein binnen 30 dagen.

#### <a name="inventory-of-the-default-azure-opc-vault-microservice-production-deployment"></a>Inventarisatie van de standaard implementatie van Azure OPC kluis micro Services productie: 

In **Azure**:
- **App service plan**: App service-plan voor service-hosts. Standaard S1.
- **App service** voor micro service: De OPC kluis servicehost.
- **App service** voor voorbeeld toepassing: De OPC-kluis voor beeld van een toepassingshost.
- **Standaard sleutel kluis**: Voor het opslaan van geheimen en Cosmos DB sleutels voor de webservices.
- **Premium**voor de sleutel kluis: Voor het hosten van de CA-sleutels van de verlener, voor de ondertekening service, voor de kluis configuratie en opslag van persoonlijke sleutels van de toepassing.
- **Cosmos DB**: Data Base voor toepassings-en certificaat aanvragen. 
- **Application Insights**: (optioneel) bewakings oplossing voor de webservice en toepassing.
- **Registratie van AzureAD-toepassing**: Een registratie voor de voorbeeld toepassing, de service en de Edge-module.

Voor de Cloud Services worden alle hostnamen, resource groepen, resource namen, abonnements-ID, TenantId gebruikt om de service te implementeren, gedocumenteerd. 

In **IOT Edge** of een lokale **IOT Edge Server**:
- **OPC-kluis IOT Edge module**: Ter ondersteuning van een Factory Network OPC UA Global Discovery-server. 

Voor de IoT Edge apparaten moeten de hostnamen en IP-adressen worden gedocumenteerd. 

### <a name="document-the-certification-authorities-cas"></a>De certificerings instanties (Ca's) documenteren

De documentatie van de CA-hiërarchie moet alle gehoste Ca's bevatten, inclusief alle gerelateerde onderliggende ca's, bovenliggende Ca's en basis certificerings instanties, zelfs wanneer deze niet door de service worden beheerd. Er kan een volledige set van alle niet-verlopen CA-certificaten worden opgegeven in plaats van formele documentatie.

> [!IMPORTANT]
> De OPC-kluis voorbeeld toepassing ondersteunt het downloaden van alle certificaten die in de service voor documentatie worden gebruikt en gemaakt.

### <a name="document-the-issued-certificates-by-all-certification-authorities-cas"></a>De verleende certificaten documenteren door alle certificerings instanties (Ca's)

Voor documentatie moet een volledige set van alle certificaten worden verstrekt die in de afgelopen twaalf maanden zijn uitgegeven.

> [!IMPORTANT]
> De OPC-kluis voorbeeld toepassing ondersteunt het downloaden van alle certificaten die in de service voor documentatie worden gebruikt en gemaakt.

### <a name="document-the-sop-for-securely-deleting-cryptographic-keys"></a>De VKA documenteren voor het veilig verwijderen van cryptografische sleutels

Het verwijderen van sleutels wordt mogelijk slechts zelden uitgevoerd tijdens de levens duur van een CA. Daarom is er geen gebruiker die het certificaat van de sleutel kluis heeft verwijderd en waarom er geen Api's beschikbaar zijn voor het verwijderen van een certificaat van een certificerings instantie. De hand matige standaard procedure voor het veilig verwijderen van cryptografische sleutels voor certificerings instanties is alleen beschikbaar door rechtstreeks toegang te krijgen tot de sleutel kluis in de Azure Portal en door de certificaat groep te verwijderen in de sleutel kluis. Om ervoor te zorgen dat de tijdelijke verwijdering van de sleutel [kluis direct verwijderen](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) moet worden uitgeschakeld.

## <a name="certificates"></a>Certificaten

### <a name="certificates-must-comply-with-minimum-certificate-profile"></a>Certificaten moeten voldoen aan het minimum certificaat profiel

De OPC-kluis service is een online certificerings instantie (CAs) die eind entiteits certificaten verleent aan abonnees.
De OPC-kluis micro service volgt deze richt lijnen in de standaard implementatie.

- Alle certificaten moeten de volgende X. 509-velden bevatten, zoals hieronder is aangegeven:
  - De inhoud van het versie veld moet v3 zijn. 
  - De inhoud van het veld serialNumber moet ten minste 8 bytes van de entropie bevatten die is verkregen van een FIPS (Federal Information Processing Standards) 140 goedgekeurde wille keurige code ring.<br>
    > [!IMPORTANT]
    > Het OPC-nummer van de kluis is standaard 20 bytes en verkregen van het besturings systeem cryptografische wille keurig aantal generator. De generator voor wille keurige getallen is FIPS 140 goedgekeurd op Windows-apparaten, maar niet op Linux-smaak. Dit feit moet worden overwogen bij het kiezen van een service-implementatie, die gebruikmaakt van Linux Vm's of Linux docker-containers, waarbij de onderliggende technologie OpenSSL niet is goedgekeurd voor FIPS 140.
  - De velden issuerUniqueID en subjectUniqueID mogen niet aanwezig zijn.
  - Certificaten van eind entiteiten moeten worden aangeduid met de uitbrei ding voor basis beperkingen in overeenstemming met IETF RFC 5280.
  - Het veld pathLenConstraint moet worden ingesteld op 0 voor het certificaat van de verlenende CA. 
  - De uitbrei ding voor uitgebreide-sleutel gebruik moet aanwezig zijn en de minimale set met object-id's (Oid's) voor uitgebreide-sleutel gebruik bevatten. De OID van de anyExtendedKeyUsage (2.5.29.37.0) moet niet worden opgegeven. 
  - De CRL-distributie punt (CDP) uitbrei ding moet aanwezig zijn in het CA-certificaat van de certificaat verlener.<br>
    > [!IMPORTANT]
    > De uitbrei ding CRL-distributie punt (CDP) is aanwezig in OPC-kluis CA-certificaten, maar OPC UA-apparaten gebruiken aangepaste methoden voor het distribueren van Crl's.
  - De extensie voor toegang tot CA-gegevens moet aanwezig zijn in de abonnee certificaten.<br>
    > [!IMPORTANT]
    > De extensie voor toegang tot CA-gegevens is aanwezig in OPC kluis abonnee certificaten, maar OPC UA-apparaten gebruiken aangepaste methoden voor het distribueren van CA-gegevens van de verlener.
- Goedgekeurde asymmetrische algoritmen, sleutel lengten, hash-functies en opvullings modi moeten worden gebruikt.
  - **RSA** en **SHA-2** zijn de enige algoritmen die worden ondersteund (*).
  - RSA kan worden gebruikt voor versleuteling, sleutel uitwisseling en hand tekening.
  - RSA-versleuteling moet alleen de opvullings modi OAEP, RSA-KEM of RSA-PSS gebruiken.
  - Sleutel lengten > = 2048 bits zijn vereist.
  - Gebruik de SHA-2-familie van hash-algoritmen (SHA256, SHA384 en SHA512 gebruikt).
  - RSA-basis-CA-sleutels met een typische levens duur > = 20 jaar moet 4096 bits of meer zijn.
  - CA-sleutels voor RSA-verlener moeten ten minste 2048 bits zijn. Als de verval datum van het CA-certificaat na 2030 is, moet de CA-sleutel 4096 bits of hoger zijn.
- Levens duur van certificaat
  - Basis-CA-certificaten: De maximale geldigheids duur van het certificaat voor basis-Ca's mag niet langer zijn dan 25 jaar.
  - CA-certificaten voor subcertificerings instanties of online uitgevers: De maximale geldigheids duur van het certificaat voor Ca's die online zijn en alleen abonnee certificaten verlenen, mag niet langer zijn dan zes jaar. Voor deze Ca's mag de gerelateerde persoonlijke handtekening sleutel niet langer dan drie jaar worden gebruikt voor het uitgeven van nieuwe certificaten.<br>
    > [!IMPORTANT]
    > Het certificaat van de certificerings instantie wordt gegenereerd in de standaard OPC-kluis micro service zonder externe basis-CA wordt behandeld als een online subca met de betreffende vereisten en levens duur. De standaard levensduur wordt ingesteld op vijf jaar met een sleutel lengte > = 2048.
  - Alle asymmetrische sleutels moeten een maximale levens duur van vijf jaar hebben, met een levens duur van één jaar.<br>
    > [!IMPORTANT]
    > De levens duur van toepassings certificaten die met OPC-kluis is uitgegeven, hebben standaard een levens duur van twee jaar en moeten elk jaar worden vervangen. 
  - Telkens wanneer een certificaat wordt vernieuwd, wordt het vernieuwd met een nieuwe sleutel.
- OPC UA-specifieke uitbrei dingen in toepassings exemplaar certificaten
  - De subjectAltName-extensie bevat de toepassings-URI en hostnamen, die ook FQDN-, IPv4-en IPv6-adressen kunnen bevatten.
  - Het gebruik van de digitalSignature, afwijzing, keyEncipherment en dataEncipherment.
  - De extendedKeyUsage omvat serverAuth en/of clientAuth.
  - De authorityKeyIdentifier wordt opgegeven in ondertekende certificaten.

### <a name="certificate-authority-ca-keys-and-certificates-must-meet-minimum-requirements"></a>De sleutels en certificaten van de certificerings instantie (CA) moeten voldoen aan de minimum vereisten

- **Persoonlijke sleutels**: **RSA** -sleutels moeten ten minste 2048 bits zijn. Als de verval datum van het CA-certificaat na 2030 is, moet de CA-sleutel 4096 bits of hoger zijn.
- **Levens duur**: De maximale geldigheids duur van het certificaat voor Ca's die online zijn en alleen abonnee certificaten verlenen, mag niet langer zijn dan zes jaar. Voor deze Ca's mag de gerelateerde persoonlijke handtekening sleutel niet langer dan drie jaar worden gebruikt voor het uitgeven van nieuwe certificaten.

### <a name="ca-keys-are-protected-using-hardware-security-modules-hsm"></a>CA-sleutels worden beveiligd met Hardware Security modules (HSM)

- OpcVault maakt gebruik van Azure sleutel kluis Premium en sleutels worden beveiligd door de HSM (Hardware Security modules) van FIPS 140-2 level 2. 

De cryptografische modules die Key Vault gebruikt, of HSM of software, FIPS (Federal Information Processing Standards) zijn gevalideerd.<br>
Sleutels die zijn gemaakt of geïmporteerd als met HSM beveiligd, worden verwerkt in een HSM, gevalideerd op FIPS 140-2 level 2.<br>
Sleutels die zijn gemaakt of geïmporteerd als software-beveiligd, worden verwerkt in cryptografische modules die zijn gevalideerd voor FIPS 140-2 level 1.

## <a name="operational-practices"></a>Operationele procedures

### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-enrollment"></a>Standaard operationele PKI-procedures voor certificaat inschrijving documenteren en onderhouden

Standaardwerk procedures (Vereisteverklaringen) documenteren en onderhouden voor de manier waarop CAs certificaten uitgeeft, waaronder: 
- Hoe de abonnee wordt geïdentificeerd en geverifieerd 
- Hoe de certificaat aanvraag wordt verwerkt en gevalideerd (indien van toepassing, moet u ook bepalen hoe certificaat vernieuwen en aanvragen voor opnieuw genereren worden verwerkt) 
- Hoe verleende certificaten worden gedistribueerd naar de abonnees 

De OPC kluis micro service VKA wordt beschreven in het [overzicht](overview-opc-vault-architecture.md) en de [manier waarop documenten worden beheerd](howto-opc-vault-manage.md) . De procedures volgen het OPC Unified Architecture-specificatie deel 12: Discovery-en Global-Services.


### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-revocation"></a>Standaard operationele PKI-procedures voor het intrekken van certificaten documenteren en onderhouden

Het intrekkings proces voor certificaten wordt beschreven in het [overzicht](overview-opc-vault-architecture.md) en de [manier waarop documenten worden beheerd](howto-opc-vault-manage.md) .
    
### <a name="document-certification-authority-key-generation-ceremony"></a>Ceremonie voor het genereren van certificerings instanties 

De CA-sleutel van de certificerings instantie die wordt gegenereerd in de OPC-kluis micro service, wordt vereenvoudigd door de beveiligde opslag in azure-sleutel kluis en wordt beschreven in de documentatie [over het beheren](howto-opc-vault-manage.md) van het beheer.

Wanneer een externe basis certificerings instantie wordt gebruikt, moet een certificerings instantie (CA) generatie ceremonie voldoen aan de volgende vereisten:

Het genereren van de CA-sleutel moet worden uitgevoerd op basis van een gedocumenteerde script dat ten minste de volgende items bevat: 
1. Definitie van rollen en verantwoordelijkheden van deel nemers
2. Goed keuring voor het genereren van een ceremonie van de CA-sleutel
3. Cryptografische hardware en activerings materialen die vereist zijn voor de ceremonie
4. Hardware-voor bereiding (inclusief activum/configuratie-informatie update en afmelden)
5. Installatie van besturings systeem
6. Specifieke stappen die worden uitgevoerd tijdens het genereren van de CA-sleutel, zoals: 
7. Installatie en configuratie van de CA-toepassing
8. CA-sleutel genereren
9. CA-sleutel back-up
10. CA-certificaat ondertekening
9. Het importeren van ondertekende sleutels in de beveiligde HSM van de service.
11. CA-systeem afsluiten
12. Voor bereiding van materialen voor opslag


## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u veilig OPC-kluis kunt beheren, volgt u de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Veilige OPC UA-apparaten met OPC-kluis](howto-opc-vault-secure.md)