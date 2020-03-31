---
title: De OPC Vault-certificaatbeheerservice veilig uitvoeren - Azure | Microsoft Documenten
description: Beschrijft hoe u de OPC Vault-certificaatbeheerservice veilig uitvoeren in Azure en beoordeelt andere beveiligingsrichtlijnen waarmee u rekening moet houden.
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 88f8188779c5fb6b3cd07c67e9f35a6b8f9ad97d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271705"
---
# <a name="run-the-opc-vault-certificate-management-service-securely"></a>De OPC Vault-certificaatbeheerservice veilig uitvoeren

In dit artikel wordt uitgelegd hoe u de OPC Vault-certificaatbeheerservice veilig uitvoeren in Azure en worden andere beveiligingsrichtlijnen beoordeeld waarmee u rekening moet houden.

## <a name="roles"></a>Rollen

### <a name="trusted-and-authorized-roles"></a>Vertrouwde en geautoriseerde rollen

De OPC Vault-microservice zorgt voor verschillende rollen om toegang te krijgen tot verschillende delen van de service.

> [!IMPORTANT]
> Tijdens de implementatie voegt het script alleen de gebruiker toe die het implementatiescript als gebruiker voor alle rollen uitvoert. Voor een productie-implementatie moet u deze roltoewijzing bekijken en op de juiste manier configureren door de onderstaande richtlijnen te volgen. Deze taak vereist handmatige toewijzing van rollen en services in de Azure Active Directory (Azure AD) Enterprise Applications-portal.

### <a name="certificate-management-service-roles"></a>Servicerollen certificaatbeheer

De OPC Vault-microservice definieert de volgende rollen:

- **Lezer**: Standaard heeft elke geverifieerde gebruiker in de tenant leestoegang. 
  - Lees toegang tot aanvragen en certificaataanvragen. Kan een lijst en query voor toepassingen en certificaataanvragen. Ook informatie over apparaatdetectie en openbare certificaten zijn toegankelijk met leestoegang.
- **Schrijver**: De rol Schrijver is toegewezen aan een gebruiker om schrijfmachtigingen toe te voegen voor bepaalde taken. 
  - Lees/schrijf toegang tot aanvragen en certificaataanvragen. Kan toepassingen registreren, bijwerken en uitschrijven. Kan certificaataanvragen maken en goedgekeurde privésleutels en certificaten verkrijgen. Kan ook privésleutels verwijderen.
- **Keurauto:** de rol van goedkeurder is toegewezen aan een gebruiker om certificaataanvragen goed te keuren of af te wijzen. De rol bevat geen andere rol.
  - Naast de rol van goedgekeurde rol om toegang te krijgen tot de OPC Vault-microservice-API, moet de gebruiker ook de machtiging voor het ondertekenen van sleutels in Azure Key Vault hebben om de certificaten te kunnen ondertekenen.
  - De rol Schrijver en goedkeuring moet aan verschillende gebruikers worden toegewezen.
  - De belangrijkste rol van de keurgever is de goedkeuring van het genereren en afwijzen van certificaataanvragen.
- **Beheerder**: de functie Administrator is toegewezen aan een gebruiker om de certificaatgroepen te beheren. De rol ondersteunt de rol van goedkeurder niet, maar omvat de rol Schrijver.
  - De beheerder kan de certificaatgroepen beheren, de configuratie wijzigen en toepassingscertificaten intrekken door een nieuwe Certificate Revocation List (CRL) uit te geven.
  - Idealiter worden de rollen Schrijver, Keurhier en Beheerder toegewezen aan verschillende gebruikers. Voor extra beveiliging heeft een gebruiker met de rol Approver of Administrator ook toestemming nodig voor het ondertekenen van sleutels in Key Vault, om certificaten uit te geven of om een CA-certificaat van de uitgever te vernieuwen.
  - Naast de rol van microservicebeheer omvat de rol, maar is deze niet beperkt tot:
    - Verantwoordelijkvoor het beheer van de implementatie van de beveiligingspraktijken van de CA.
    - Beheer van de generatie, intrekking en opschorting van certificaten. 
    - Cryptografische sleutellevenscyclusbeheer (bijvoorbeeld de vernieuwing van de CA-sleutels van de uitgever).
    - Installatie, configuratie en onderhoud van services die de CA bedienen.
    - Dagelijkse werking van de diensten. 
    - CA en database back-up en herstel.

### <a name="other-role-assignments"></a>Andere roltoewijzingen

Houd ook rekening met de volgende rollen wanneer u de service uitvoert:

- Bedrijfseigenaar van het certificaataanbestedingscontract met de externe rootcertificeringsinstantie (bijvoorbeeld wanneer de eigenaar certificaten van een externe CA koopt of een CA beheert die ondergeschikt is aan een externe CA).
- Ontwikkeling en validatie van de Certificeringsinstantie.
- Herziening van de controledossiers.
- Personeel dat helpt bij het ondersteunen van de CA of het beheren van de fysieke en cloudfaciliteiten, maar niet direct wordt vertrouwd om CA-bewerkingen uit te voeren, bevindt zich in de *geautoriseerde* rol. De takendie personen in de geautoriseerde rol mogen uitvoeren, moeten ook worden gedocumenteerd.

### <a name="review-memberships-of-trusted-and-authorized-roles-quarterly"></a>Het aantal lidmaatschappen van vertrouwde en geautoriseerde rollen per kwartaal bekijken

Bekijk het lidmaatschap van vertrouwde en geautoriseerde rollen ten minste driemaandelijks. Zorg ervoor dat de set mensen (voor handmatige processen) of serviceidentiteiten (voor geautomatiseerde processen) in elke rol tot een minimum wordt beperkt.

### <a name="role-separation-between-certificate-requester-and-approver"></a>Rolscheiding tussen certificaataanvrager en keurgever

Het certificaatuitgifteproces moet de scheiding van rollen tussen de certificaataanvrager en de certificaatgoedkeuringrol (personen of geautomatiseerde systemen) afdwingen. De afgifte van certificaten moet worden toegestaan door een rol van de goedkeuring van certificaten die controleert of de certificaataanvrager gemachtigd is certificaten te verkrijgen. De personen die de rol van de certificaatkeurgever bezitten, moeten een formeel gemachtigde persoon zijn.

### <a name="restrict-assignment-of-privileged-roles"></a>Toewijzing van bevoorrechte rollen beperken

U moet de toewijzing van bevoorrechte rollen, zoals het toestaan van lidmaatschap van de groep Administrators en Fiatteurs, beperken tot een beperkte set geautoriseerd personeel. Wijzigingen in een bevoorrechte rol moeten binnen 24 uur de toegang hebben ingetrokken. Tot slot u geprivilegieerde roltoewijzingen op kwartaalbasis bekijken en eventuele onnodige of verlopen toewijzingen verwijderen.

### <a name="privileged-roles-should-use-two-factor-authentication"></a>Bevoorrechte rollen moeten tweestapsverificatie gebruiken

Gebruik multi-factor authenticatie (ook wel tweestapsverificatie genoemd) voor interactieve aanmeldingen van fiatteurs en beheerders bij de service.

## <a name="certificate-service-operation-guidelines"></a>Richtlijnen voor de werking van certificaatservice

### <a name="operational-contacts"></a>Operationele contacten

De certificaatservice moet beschikken over een up-to-date beveiligingsresponsplan in het bestand, dat gedetailleerde contactpersonen voor operationele incidentrespons bevat.

### <a name="security-updates"></a>Beveiligingsupdates

Alle systemen moeten continu worden gecontroleerd en bijgewerkt met de nieuwste beveiligingsupdates.

> [!IMPORTANT]
> De GitHub repository van de OPC Vault-service wordt voortdurend bijgewerkt met beveiligingspatches. Controleer deze updates en pas ze regelmatig toe op de service.

### <a name="security-monitoring"></a>Beveiligingsbewaking

Abonneer u op of implementeer de juiste beveiligingsbewaking. U zich bijvoorbeeld abonneren op een centrale bewakingsoplossing (zoals Azure Security Center of Office 365-bewakingsoplossing) en deze op de juiste manier configureren om ervoor te zorgen dat beveiligingsgebeurtenissen naar de bewakingsoplossing worden verzonden.

> [!IMPORTANT]
> Standaard wordt de OPC Vault-service geïmplementeerd met [Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/devops) als bewakingsoplossing. Het toevoegen van een beveiligingsoplossing zoals [Azure Security Center](https://azure.microsoft.com/services/security-center/) is een aanrader.

### <a name="assess-the-security-of-open-source-software-components"></a>De beveiliging van open-source softwarecomponenten beoordelen

Alle open-source componenten die binnen een product of dienst worden gebruikt, moeten vrij zijn van matige of grotere beveiligingsproblemen.

> [!IMPORTANT]
> Tijdens continue integratiebuilds scant de GitHub-repository van de OPC Vault-service alle componenten op kwetsbaarheden. Controleer deze updates op GitHub en pas ze regelmatig toe op de service.

### <a name="maintain-an-inventory"></a>Een voorraad bijhouden

Houd een assetinventory bij voor alle productiehosts (inclusief permanente virtuele machines), apparaten, alle interne IP-adresbereiken, VIP's en openbare DNS-domeinnamen. Wanneer u een systeem, ip-adres, VIP- of openbaar DNS-domein toevoegt of verwijdert, moet u de voorraad binnen 30 dagen bijwerken.

#### <a name="inventory-of-the-default-azure-opc-vault-microservice-production-deployment"></a>Voorraad van de standaard azure OPC Vault-implementatie van microservice 

In Azure:
- **App-serviceplan**: App-serviceplan voor servicehosts. Standaard S1.
- **App-service** voor microservice: de OPC Vault-servicehost.
- **App-service** voor voorbeeldtoepassing: de opc-vault-voorbeeldtoepassinghost.
- **Key Vault Standard:** Om geheimen en Azure Cosmos DB-sleutels voor de webservices op te slaan.
- **Key Vault Premium:** Als u de CA-sleutels van de uitgever wilt hosten, voor ondertekeningsservice en voor kluisconfiguratie en -opslag van privésleutels van toepassingen.
- **Azure Cosmos DB:** Database voor toepassings- en certificaataanvragen. 
- **Application Insights**: (optioneel) Monitoring oplossing voor webservice en applicatie.
- **Azure AD-toepassingsregistratie:** een registratie voor de voorbeeldtoepassing, de service en de randmodule.

Voor de cloudservices moeten alle hostnamen, resourcegroepen, resourcenamen, abonnements-id's en tenant-id's die worden gebruikt om de service te implementeren, worden gedocumenteerd. 

In Azure IoT Edge of een lokale IoT Edge-server:
- **OPC Vault IoT Edge-module:** ondersteuning bieden voor een fabrieksnetwerk OPC UA Global Discovery Server. 

Voor de IoT Edge-apparaten moeten de hostnamen en IP-adressen worden gedocumenteerd. 

### <a name="document-the-certification-authorities-cas"></a>Documenteer de certificeringsinstanties (CV's)

De CA-hiërarchiedocumentatie moet alle uitgevoerde CA's bevatten. Dit omvat alle gerelateerde ondergeschikte CA's, bovenliggende CA's en root-CA's, zelfs als ze niet door de service worden beheerd. In plaats van formele documentatie u een uitgebreide set van alle niet-verlopen CA-certificaten verstrekken.

> [!NOTE]
> De OPC Vault-voorbeeldtoepassing ondersteunt het downloaden van alle certificaten die in de service worden gebruikt en geproduceerd voor documentatie.

### <a name="document-the-issued-certificates-by-all-certification-authorities-cas"></a>Documenteer de afgegeven certificaten door alle certificeringsinstanties (CA's)

Geef een uitputtende set van alle certificaten die in de afgelopen 12 maanden zijn uitgegeven.

> [!NOTE]
> De OPC Vault-voorbeeldtoepassing ondersteunt het downloaden van alle certificaten die in de service worden gebruikt en geproduceerd voor documentatie.

### <a name="document-the-standard-operating-procedure-for-securely-deleting-cryptographic-keys"></a>De standaardprocedure documenteren voor het veilig verwijderen van cryptografische sleutels

Tijdens de levensduur van een CA kan het verwijderen van sleutels slechts zelden plaatsvinden. Dit is de reden waarom geen enkele gebruiker het rechter-verwijderen van Key Vault Certificate Delete heeft toegewezen en waarom er geen API's zijn blootgesteld om een CA-certificaat van een uitgever te verwijderen. De handmatige standaardprocedure voor het veilig verwijderen van cryptografische sleutels van certificeringsinstanties is alleen beschikbaar door rechtstreeks toegang te krijgen tot Key Vault in de Azure-portal. U de certificaatgroep ook verwijderen in Key Vault. Schakel de functionaliteit [voor soft delete van Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) uit om ervoor te zorgen dat u onmiddellijk wordt verwijderd.

## <a name="certificates"></a>Certificaten

### <a name="certificates-must-comply-with-minimum-certificate-profile"></a>Certificaten moeten voldoen aan minimaal certificaatprofiel

De OPC Vault-service is een online CA die eindentiteitcertificaten uitgeeft aan abonnees. De OPC Vault-microservice volgt deze richtlijnen in de standaardimplementatie.

- Alle certificaten moeten de volgende X.509-velden bevatten, zoals hieronder wordt aangegeven:
  - De inhoud van het veld versie moet v3 zijn. 
  - De inhoud van het elserienummerveld moet ten minste 8 bytes entropie bevatten die is verkregen uit een FIPS (Federal Information Processing Standards) 140 goedgekeurde random number generator.<br>
    > [!IMPORTANT]
    > Het OPC Vault serienummer is standaard 20 bytes, en wordt verkregen uit het besturingssysteem cryptografische random number generator. De random number generator is FIPS 140 goedgekeurd op Windows-apparaten, maar niet op Linux. Houd hier rekening mee bij het kiezen van een service-implementatie die gebruik maakt van Linux VM's of Linux dockercontainers, waarop de onderliggende technologie OpenSSL niet FIPS 140 is goedgekeurd.
  - De velden UniqueID en subjectUniqueID mogen niet aanwezig zijn.
  - Eindentiteitcertificaten moeten worden geïdentificeerd met de basisbeperkingenextensie, overeenkomstig IETF RFC 5280.
  - Het veld pathLenConstraint moet zijn ingesteld op 0 voor het CA-certificaat van uitgifte. 
  - De extensie Uitgebreid sleutelgebruik moet aanwezig zijn en moet de minimale set object-id's (Extended Key Usage) bevatten. De anyExtendedKeyUsage OID (2.5.29.37.0) mag niet worden opgegeven. 
  - De CRL Distribution Point (CDP)-extensie moet aanwezig zijn in het ISSUER CA-certificaat.<br>
    > [!IMPORTANT]
    > De CDP-extensie is aanwezig in OPC Vault CA-certificaten. Niettemin gebruiken OPC UA-apparaten aangepaste methoden om CRL's te distribueren.
  - De extensie Toegang tot autoriteitinformatie moet aanwezig zijn in de abonneecertificaten.<br>
    > [!IMPORTANT]
    > De extensie Toegang voor autoriteitinformatie is aanwezig in opc vault-abonneecertificaten. Niettemin gebruiken OPC UA-apparaten aangepaste methoden om emittent CA-informatie te distribueren.
- Goedgekeurde asymmetrische algoritmen, sleutellengtes, hashfuncties en opvulmodi moeten worden gebruikt.
  - RSA en SHA-2 zijn de enige ondersteunde algoritmen.
  - RSA kan worden gebruikt voor versleuteling, sleuteluitwisseling en handtekening.
  - RSA-versleuteling mag alleen de opvullingsmodi OAEP, RSA-KEM of RSA-PSS gebruiken.
  - Sleutellengtes groter dan of gelijk aan 2048 bits zijn vereist.
  - Gebruik de SHA-2-familie van hash-algoritmen (SHA256, SHA384 en SHA512).
  - RSA Root CA-toetsen met een typische levensduur van meer dan of gelijk aan 20 jaar moeten 4096 bits of meer zijn.
  - DE RSA Issuer CA-sleutels moeten ten minste 2048 bits zijn. Als de vervaldatum van het CA-certificaat na 2030 is, moet de CA-toets 4096 bits of meer zijn.
- Levensduur van certificaat
  - Root CA-certificaten: De maximale certificaatgeldigheidsperiode voor root-CA's mag niet langer zijn dan 25 jaar.
  - Sub CA- of online Issuer CA-certificaten: de maximale certificaatgeldigheidsperiode voor CA's die online zijn en alleen abonneecertificaten uitgeven, mag niet langer zijn dan 6 jaar. Voor deze CA's mag de bijbehorende private signature-sleutel niet langer dan 3 jaar worden gebruikt om nieuwe certificaten af te geven.<br>
    > [!IMPORTANT]
    > Het Issuer-certificaat, omdat het wordt gegenereerd in de standaard OPC Vault-microservice zonder externe Root CA, wordt behandeld als een online sub-CA, met respectievelijk vereisten en levensduur. De standaardlevensduur is ingesteld op 5 jaar, met een sleutellengte die groter is dan of gelijk is aan 2048.
  - Alle asymmetrische toetsen moeten een maximale levensduur van 5 jaar hebben en een aanbevolen levensduur van 1 jaar.<br>
    > [!IMPORTANT]
    > Standaard hebben de looptijden van toepassingscertificaten die zijn uitgegeven met OPC Vault een levensduur van 2 jaar en moeten ze elk jaar worden vervangen. 
  - Wanneer een certificaat wordt vernieuwd, wordt het vernieuwd met een nieuwe sleutel.
- OPC UA-specifieke extensies in toepassingsinstantiecertificaten
  - De onderwerpAltName-extensie bevat de toepassing Uri en hostnames. Deze kunnen ook FQDN-, IPv4- en IPv6-adressen bevatten.
  - De keyUsage omvat digitalSignature, nonRepudiation, keyEncipherment, and dataEncipherment.
  - De extendedKeyUsage bevat serverAuth en clientAuth.
  - De autoriteitKeyIdentifier is opgegeven in ondertekende certificaten.

### <a name="ca-keys-and-certificates-must-meet-minimum-requirements"></a>CA-sleutels en certificaten moeten voldoen aan minimumvereisten

- **Privésleutels**: RSA-toetsen moeten ten minste 2048 bits zijn. Als de vervaldatum van het CA-certificaat na 2030 is, moet de CA-toets 4096 bits of meer zijn.
- **Looptijd**: De maximale geldigheidsduur van het certificaat voor A's die online zijn en alleen abonneecertificaten uitgeven, mag niet langer zijn dan 6 jaar. Voor deze CA's mag de bijbehorende private signature-sleutel niet langer dan 3 jaar worden gebruikt om nieuwe certificaten af te geven.

### <a name="ca-keys-are-protected-using-hardware-security-modules"></a>CA-sleutels zijn beveiligd met hardwarebeveiligingsmodules

OpcVault maakt gebruik van Azure Key Vault Premium en sleutels worden beschermd door FIPS 140-2 Level 2 Hardware Security Modules (HSM). 

De cryptografische modules die Key Vault gebruikt, of het nu HSM of software is, zijn FIPS gevalideerd. Sleutels die zijn gemaakt of geïmporteerd als HSM-beveiligd, worden verwerkt in een HSM, gevalideerd naar FIPS 140-2 Level 2. Sleutels die zijn gemaakt of geïmporteerd als softwarebeveiligd, worden verwerkt in cryptografische modules die zijn gevalideerd naar FIPS 140-2 Niveau 1.

## <a name="operational-practices"></a>Operationele praktijken

### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-enrollment"></a>Standaard operationele PKI-praktijken documenteren en onderhouden voor certificaatinschrijving

Documenteer en onderhoud standaard operationele procedures (SOP's) voor de manier waarop DE's certificaten afgeven, waaronder: 
- Hoe de abonnee wordt geïdentificeerd en geverifieerd. 
- Hoe de certificaataanvraag wordt verwerkt en gevalideerd (indien van toepassing, ook hoe certificaatverlengingen en hersleutelaanvragen worden verwerkt). 
- Hoe uitgegeven certificaten worden uitgedeeld aan de abonnees. 

De OPC Vault microservice SOP wordt beschreven in [de OPC Vault-architectuur](overview-opc-vault-architecture.md) en [Beheer de OPC Vault-certificaatservice.](howto-opc-vault-manage.md) De praktijken volgen "OPC Unified Architecture Specification Part 12: Discovery and Global Services."


### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-revocation"></a>Standaard operationele PKI-praktijken voor certificaatintrekking documenteren en onderhouden

Het certificaatintrekkingsproces wordt beschreven in [de OPC Vault-architectuur](overview-opc-vault-architecture.md) en [Beheer de OPC Vault-certificaatservice.](howto-opc-vault-manage.md)
    
### <a name="document-ca-key-generation-ceremony"></a>Document CA-sleutelgeneratieceremonie 

De issuer CA-sleutelgeneratie in de OPC Vault-microservice is vereenvoudigd vanwege de beveiligde opslag in Azure Key Vault. Zie [De OPC Vault-certificaatservice beheren](howto-opc-vault-manage.md)voor meer informatie.

Wanneer u echter een externe rootcertificeringsinstantie gebruikt, moet een CA-sleutelgeneratieceremonie voldoen aan de volgende vereisten.

De CA-sleutelgeneratieceremonie moet worden uitgevoerd tegen een gedocumenteerd script dat ten minste de volgende items bevat: 
- Definitie van rollen en deelnemersverantwoordelijkheden.
- Goedkeuring voor het uitvoeren van de CA-sleutelgeneratieceremonie.
- Cryptografische hardware en activeringsmaterialen die nodig zijn voor de ceremonie.
- Hardwarevoorbereiding (inclusief het bijwerken en afmelden van asset/configuratie-informatie).
- Installatie van het besturingssysteem.
- Specifieke stappen uitgevoerd tijdens de CA-sleutelgeneratieceremonie, zoals: 
  - INSTALLATIE en configuratie van CA-toepassingen.
  - CA-sleutelgeneratie.
  - CA-sleutelback-up.
  - CA-certificaatondertekening.
  - Importeren van ondertekende sleutels in de beveiligde HSM van de service.
  - CA-systeem afsluiten.
  - Bereiding van materialen voor opslag.


## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u OPC Vault veilig beheren, u:

> [!div class="nextstepaction"]
> [Beveilig OPC UA-apparaten met OPC Vault](howto-opc-vault-secure.md)
