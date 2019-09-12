---
title: Azure Key Vault in azure Australië
description: Richt lijnen voor het configureren en gebruiken van Azure Key Vault voor sleutel beheer binnen de Australische regio's om te voldoen aan de specifieke vereisten van het beleid voor de Australische overheid, de regelgeving en de wetgeving.
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 263765c777f994134befc52f0c63c7f18e590b39
ms.sourcegitcommit: 15f7b641a67f3d6cf4fb4b4c11eaee18cf335923
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68602127"
---
# <a name="azure-key-vault-in-azure-australia"></a>Azure Key Vault in azure Australië

De veilige opslag van cryptografische sleutels en het beheer van de levens cyclus van de cryptografische sleutels zijn essentiële elementen binnen cryptografische systemen.  De service die deze mogelijkheid biedt in azure, is het Azure Key Vault. Key Vault is IRAP-beveiliging geopend en ACSC gecertificeerd voor beveiligd.  In dit artikel vindt u een overzicht van de belangrijkste aandachtspunten bij het gebruik van Key Vault om te voldoen aan de vereisten voor [hand matige controle](https://acsc.gov.au/infosec/ism/) (ISM) van het Australische signalen (ASD).

Azure Key Vault is een Cloud service die de versleutelings sleutels en geheimen beveiligt. Omdat deze gegevens gevoelig en zakelijk kritiek zijn, maakt Key Vault beveiligde toegang tot sleutel kluizen, waardoor alleen geautoriseerde gebruikers en toepassingen worden toegestaan. Er zijn drie hoofd artefacten die worden beheerd en beheerd door Key Vault:

- sleutels
- geheimen
- certificaten

Dit artikel is gericht op het beheer van sleutels met behulp van Key Vault.

![Azure Key Vault](media/azure-key-vault-overview.png)

*Diagram 1 – Azure Key Vault*

## <a name="key-design-considerations"></a>Belangrijkste overwegingen bij het ontwerpen

### <a name="deployment-options"></a>Implementatieopties

Er zijn twee opties voor het maken van Azure-sleutel kluizen. Beide opties gebruiken de Thales nShield-familie van hardware security modules (HSM), zijn FIPS (Federal Information Processing Standards) gevalideerd en zijn goedgekeurd voor het opslaan van sleutels in beveiligde omgevingen. De opties zijn:

- **Met software beveiligde kluizen:** FIPS 140-2 level 1 gevalideerd. Sleutels die zijn opgeslagen in een HSM. Bewerkingen voor versleuteling en ontsleuteling worden uitgevoerd in reken resources op Azure.
- **Met HSM beveiligde kluizen:** FIPS 140-2 level 2 gevalideerd. Sleutels die zijn opgeslagen in een HSM. Bewerkingen voor versleuteling en ontsleuteling worden uitgevoerd op de HSM.

Key Vault ondersteunt Rivest-Shamir-Adleman-(RSA) en elliptische-coderings sleutels (ECC). De standaard instelling is RSA 2048-bits sleutels, maar er is een geavanceerde optie voor RSA 3072-bits, RSA 4096-bits en ECC-sleutels.  Alle sleutels voldoen aan de ISM-besturings elementen, maar elliptische curve sleutels worden aanbevolen.

### <a name="resource-operations"></a>Resource bewerkingen

Er zijn verschillende personen betrokken bij Azure Key Vault:

- **Key Vault beheerder:** Beheert de levens cyclus van de kluis
- **Sleutel beheerder:** Hiermee beheert u de levens cyclus van sleutels in de kluis
- **Ontwikkelaar/operator:** Sleutels van de kluis integreren in toepassingen en services
- **Accountant** Het sleutel gebruik en de toegang controleren
- **Toepassingen** Sleutels gebruiken om informatie te beveiligen

Azure Key Vault is beveiligd met twee afzonderlijke interfaces:

- **Beheer vlak:** Dit vlak heeft betrekking op het beheer van de kluis en beveiligd door RBAC.
- **Gegevens vlak:** Dit vlak behandelt het beheer en de toegang tot de artefacten in de kluis.  Beveiligd met Key Vault toegangs beleid.

Zoals vereist door de ISM, zijn de juiste verificatie en autorisatie vereist voor een aanroeper (een gebruiker of een toepassing) voordat ze toegang kunnen krijgen tot de sleutel kluis per vlieg tuig.

Azure RBAC heeft één ingebouwde rol voor Key Vault, [Key Vault Inzender](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#key-vault-contributor), om het beheer van de sleutel kluizen te beheren. Het is raadzaam om aangepaste rollen te maken die zijn afgestemd op meer gedetailleerde rollen voor het beheren van uw sleutel kluizen.

>[!WARNING]
>Wanneer toegang tot sleutels is ingeschakeld via Key Vault toegangs beleid, heeft de gebruiker of toepassing die toegang heeft tot alle sleutels in de sleutel kluis (bijvoorbeeld als een gebruiker de toegang verwijderen heeft, dan kunnen alle sleutels worden verwijderd).  Daarom moeten meerdere sleutel kluizen worden geïmplementeerd om te worden uitgelijnd met beveiligings domeinen/-grenzen.

### <a name="networking"></a>Netwerken

U kunt Key Vault firewalls en virtuele netwerken configureren om de toegang tot het gegevens vlak te beheren.  U kunt toegang verlenen aan gebruikers of toepassingen op opgegeven netwerken terwijl u geen toegang meer hebt tot gebruikers of toepassingen in alle andere netwerken. [Vertrouwde services](https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints#trusted-services) zijn een uitzonde ring op dit besturings element als ' Trusted Services toestaan ' is ingeschakeld.  Het besturings element virtuele netwerken is niet van toepassing op het beheer vlak.

Toegang tot sleutel kluizen moet expliciet worden beperkt tot de minimale set netwerken met gebruikers of toepassingen die toegang tot sleutels vereisen.

### <a name="bring-your-own-key-byok"></a>Bring Your Own Key (BYOK)

Key Vault ondersteunt BYOK.  Met BYOK kunnen gebruikers sleutels importeren uit hun bestaande Key infrastructuren.  Thales biedt een [Australische toolset](https://www.microsoft.com/download/details.aspx?id=45345) ter ondersteuning van de beveiligde overdracht en het importeren van sleutels van een externe HSM (bijvoorbeeld sleutels die zijn gegenereerd met een offline werk station) in Key Vault.

### <a name="key-vault-auditing-and-logging"></a>Controle en logboek registratie Key Vault

De ACSC vereist Gemenebests entiteiten om de juiste Azure-Services te gebruiken voor het uitvoeren van real-time bewaking en rapportage over hun Azure-workloads.

Logboek registratie is ingeschakeld door de diagnostische instelling **_' audit event '_** in te scha kelen voor sleutel waarden.  Controle gebeurtenissen worden geregistreerd in het opgegeven opslag account.  De periode **_' RetentionInDays '_** moet worden ingesteld volgens het Bewaar beleid voor gegevens.  [Bewerkingen](https://docs.microsoft.com/azure/key-vault/key-vault-logging#interpret) op het vlak van het beheer en het gegevens vlak worden gecontroleerd en geregistreerd. De [Azure Key Vault oplossing in azure monitor](https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault) kan worden gebruikt om Key Vault audit event-logboeken te controleren.  Een aantal andere Azure-Services kan worden gebruikt om Key Vault AuditEvents te verwerken en te distribueren.

### <a name="key-rotation"></a>Sleutelroulatie

Het opslaan van sleutels in Key Vault voorziet in één punt voor het onderhouden van sleutels buiten toepassingen waarmee sleutels kunnen worden bijgewerkt zonder dat dit van invloed is op het gedrag van de toepassingen. Het opslaan van sleutels in Azure Key Vault biedt verschillende strategieën voor de ondersteuning van het draaien van sleutels:

- Handmatig
- Programmatisch via Api's
- Automatiserings scripts (bijvoorbeeld met behulp van Power shell en Azure Automation)

Met deze opties kunnen sleutels periodiek worden geroteerd om te voldoen aan nalevings vereisten of op basis van ad hoc als er problemen zijn die mogelijk zijn aangetast.

#### <a name="key-rotation-strategies"></a>Strategieën voor het draaien van sleutels

Het is belang rijk dat u een geschikte methode voor het draaien van sleutels ontwikkelt voor de sleutel die in de sleutel kluis zijn opgeslagen.  Het gebruik van de verkeerde sleutel leidt ertoe dat de gegevens onjuist worden ontsleuteld en het verlies van sleutels kan leiden tot volledig verlies van toegang tot gegevens.  Voor beelden van belang rijke rotatie strategieën voor verschillende scenario's zijn:

- **Invlucht gegevens:** er wordt vluchtige informatie verzonden tussen twee partijen.  Wanneer een sleutel wordt geroteerd, moeten beide partijen beschikken over een mechanisme voor het synchroon ophalen van de bijgewerkte sleutels van de sleutel kluis.
- **Gegevens als rest:** Een partij slaat versleutelde gegevens op en ontsleutelt deze in de toekomst voor gebruik.  Wanneer een sleutel wordt gedraaid, moeten de gegevens worden ontsleuteld met de oude sleutel en vervolgens worden versleuteld met de nieuwe, geroteerde sleutel.  Er zijn benaderingen om de impact van het ontsleutelen/versleutelen te minimaliseren met sleutel versleutelings sleutels (Zie voor beeld).  Micro soft beheert het meren deel van het proces met betrekking tot het draaien van sleutels voor Azure Storage (zie...)
- **Toegangs sleutels:** een aantal Azure-Services heeft toegangs sleutels die kunnen worden opgeslagen in Key Vault (bijvoorbeeld CosmosDB).  De Azure-Services hebben primaire en secundaire toegangs sleutels.  Het is belang rijk dat beide sleutels op hetzelfde moment niet worden gedraaid.  Daarom moet één sleutel, na een periode, worden gedraaid en de sleutel bewerking is gecontroleerd, dan kan de tweede sleutel worden gedraaid.

### <a name="high-availability"></a>Hoge beschikbaarheid

De ISM heeft verschillende besturings elementen die betrekking hebben op bedrijfs continuïteit.
Azure Key Vault heeft meerdere lagen met redundantie met inhoud die wordt gerepliceerd binnen de regio en naar de secundaire, [gekoppelde regio](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

Wanneer de sleutel kluis een failover-status heeft, bevindt deze zich in de modus alleen-lezen en wordt de primaire service teruggezet naar de modus lezen-schrijven.

De ISM heeft verschillende besturings elementen die betrekking hebben op back-up.  Het is belang rijk dat u de juiste plannen voor back-ups en herstel ontwikkelt en uitvoert voor kluizen en hun sleutels.

## <a name="key-lifecycle"></a>Levens cyclus van sleutels

### <a name="key-operations"></a>Belang rijke bewerkingen

Key Vault ondersteunen de volgende bewerkingen op een sleutel:

- **creëren** Hiermee kan een client een sleutel maken in Key Vault. De waarde van de sleutel wordt gegenereerd door Key Vault en opgeslagen en wordt niet vrijgegeven aan de client. Er kunnen asymmetrische sleutels worden gemaakt in Key Vault.
- **wederinvoer** Hiermee kan een client een bestaande sleutel importeren in Key Vault. Asymmetrische sleutels kunnen worden geïmporteerd in Key Vault met behulp van een aantal verschillende pakket methoden in een JWK-constructie.
- **bijwerk** Hiermee kan een client met voldoende machtigingen de meta gegevens (sleutel kenmerken) wijzigen die zijn gekoppeld aan een sleutel die eerder is opgeslagen in Key Vault.
- **verwijderd** Hiermee kan een client met voldoende machtigingen een sleutel uit Key Vault verwijderen.
- **orderverzamellijst** Hiermee kan een client alle sleutels in een bepaalde Key Vault weer geven.
- **versies weer geven:** Hiermee kan een client alle versies van een bepaalde sleutel in een bepaalde Key Vault weer geven.
- **Toevoegen:** Hiermee kan een client de open bare onderdelen van een bepaalde sleutel in een Key Vault ophalen.
- **programma** Hiermee wordt een sleutel in een beveiligd formulier geëxporteerd.
- **standaard** Hiermee wordt een eerder gemaakte back-upsleutel geïmporteerd.

Er is een bijbehorende set machtigingen die kan worden verleend aan gebruikers, service-principals of toepassingen die gebruikmaken van Key Vault Access Control-vermeldingen om ze in staat te stellen sleutel bewerkingen uit te voeren.

Key Vault beschikt over een functie voor het herstellen van verwijderde kluizen en sleutels. Standaard is **_' voorlopig verwijderen '_** niet ingeschakeld, maar als dit eenmaal is ingeschakeld, worden de objecten gedurende 90 dagen (de Bewaar periode) bewaard terwijl ze worden verwijderd.  Met een extra machtiging **_' opschonen '_** kunnen sleutels permanent worden verwijderd als de optie Beveiliging opschonen is uitgeschakeld.

Als u een bestaande sleutel maakt of importeert, wordt een nieuwe versie van de sleutel gemaakt.

### <a name="cryptographic-operations"></a>Cryptografische bewerkingen

Key Vault biedt ook ondersteuning voor cryptografische bewerkingen met behulp van sleutels:

- **ondertekenen en controleren:** deze bewerking is een "hash ondertekenen" of "hash controleren". Key Vault biedt geen ondersteuning voor hashing van inhoud als onderdeel van het maken van een hand tekening.
- **sleutel versleuteling/-Wrapping:** deze bewerking wordt gebruikt om een andere sleutel te beveiligen.
- **versleutelen en ontsleutelen:** de opgeslagen sleutel wordt gebruikt voor het versleutelen of ontsleutelen van één gegevens blok

Er is een bijbehorende set machtigingen die kan worden verleend aan gebruikers, service-principals of toepassingen die gebruikmaken van Key Vault Access Control Entries om ze in staat te stellen cryptografische bewerkingen uit te voeren.

Er zijn drie belang rijke kenmerken die kunnen worden ingesteld om te bepalen of een sleutel is ingeschakeld en bruikbaar is voor cryptografische bewerkingen:

- **ingeschakeld**
- **NBF:** niet vóór ingeschakeld vóór opgegeven datum
- **Exp:** verval datum

## <a name="storage-and-keys"></a>Opslag en sleutels

Door de klant beheerde sleutels zijn meer flexibiliteit en kunnen evalueren en beheren van de sleutels die moeten worden beheerd. Ze kunnen ook de versleutelings sleutels voor het beveiligen van gegevens controleren.
Er zijn drie aspecten van opslag en sleutels opgeslagen in Key Vault:

- Sleutels voor beheerde-opslag account Key Vault
- Azure Storage-service versleuteling (SSE) voor Data-at-rest
- Beheerde schijven en Azure Disk Encryption

Het sleutel beheer van Key Vault-Azure Storage account is een uitbrei ding van de sleutel service van Key Vault die synchronisatie en het opnieuw genereren van opslag account sleutels ondersteunt.  [Integratie met Azure Active Directory Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-auth-aad) (preview) wordt aanbevolen wanneer het een superieure beveiliging en gebruiks vriendelijkheid biedt.
SSE gebruikt twee sleutels voor het beheren van versleuteling van gegevens in rust:

- Sleutel versleutelings sleutels (KEK)
- Gegevens versleutelings sleutels (DEK)

Terwijl micro soft de DEKs beheert, heeft SSE een optie voor het gebruik van door de klant beheerde KEKs die kunnen worden opgeslagen in Key Vault. Hiermee wordt de draaiing van sleutels in Azure Key Vault conform de juiste nalevings beleidsregels ingeschakeld. Wanneer sleutels worden geroteerd, Azure Storage de versleutelings sleutel van het account voor dat opslag account opnieuw versleutelen. Dit resulteert niet in het opnieuw versleutelen van alle gegevens en er is geen andere actie vereist.

SSE wordt gebruikt voor beheerde schijven, maar door de klant beheerde sleutels worden niet ondersteund.  Versleuteling van Managed disks kan worden uitgevoerd met behulp van Azure Disk Encryption met door de klant beheerde KEK-sleutels in Key Vault.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg het artikel over [identiteits Federatie](identity-federation.md)

Aanvullende Azure Key Vault-documentatie en zelf studies in de [referentie bibliotheek](reference-library.md) bekijken
