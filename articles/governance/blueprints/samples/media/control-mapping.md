---
title: Voorbeeldbesturingselementen voor mediablauwdrukken
description: Beheer het in kaart brengen van de blauwdrukvoorbeelden van media. Elk besturingselement wordt toegewezen aan een of meer Azure-beleidsregels die helpen bij de beoordeling.
ms.date: 02/24/2020
ms.topic: sample
ms.openlocfilehash: bdc4ef65c6d61a9d4217d7873bb29bd787842e71
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78201904"
---
# <a name="control-mapping-of-the-media-blueprint-sample"></a>Het toewijzen van het voorbeeld van de mediablauwdruk beheren

In het volgende artikel wordt beschreven hoe het blueprintvoorbeeld van Azure Blueprints Media wordt toegewezen aan de besturingselementen media. Zie [Media](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html)voor meer informatie over de besturingselementen.

De volgende toewijzingen **Media** zijn naar de mediabesturingselementen. Gebruik de navigatie aan de rechterkant om direct naar een specifieke besturingstoewijzing te springen. Veel van de toegewezen besturingselementen worden geïmplementeerd met een [Azure Policy-initiatief.](../../../policy/overview.md) Als u het volledige initiatief wilt bekijken, opent u **Beleid** in de Azure-portal en selecteert u de pagina **Definities.** Zoek en selecteer vervolgens het ** \[ingebouwde beleidsinitiatief Voorbeeld:\]Controlemedia.**

> [!IMPORTANT]
> Elk besturingselement hieronder is gekoppeld aan een of meer [Azure Policy-definities.](../../../policy/overview.md) Met dit beleid u [beoordelen of de](../../../policy/how-to/get-compliance-data.md) controle is nageleefd; Er is echter vaak geen 1:1 of volledige overeenkomst tussen een besturingselement en een of meer beleidsregels. **Compliant** in Azure Policy verwijst daarom alleen naar het beleid zelf. Dit zorgt er niet voor dat u volledig voldoet aan alle vereisten van een controle. Bovendien bevat de nalevingsstandaard besturingselementen die op dit moment niet worden behandeld door azure-beleidsdefinities. Naleving in Azure Policy is daarom slechts een gedeeltelijke weergave van uw algemene nalevingsstatus. De koppelingen tussen besturingselementen en Azure Policy-definities voor dit voorbeeld van nalevingsblauwdrukken kunnen in de loop van de tijd veranderen. Zie de [GitHub Commit History](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/media/control-mapping.md)om de wijzigingsgeschiedenis te bekijken.

## <a name="access-control"></a>Toegangsbeheer

### <a name="ac-11--ensure-no-root-access-key-exists"></a>AC-1.1- Zorg ervoor dat er geen root access-sleutel bestaat

- \[Voorbeeld:\]Vereiste vereisten implementeren om Windows VM's te controleren die de opgegeven certificaten niet bevatten in Trusted Root

### <a name="ac-12---passwords-pins-and-tokens-must-be-protected"></a>AC-1.2 - Wachtwoorden, pincodes en tokens moeten worden beveiligd

- \[Voorbeeld:\]Vereiste vereisten implementeren om Windows VM's te controleren die de minimale wachtwoordlengte niet beperken tot 14 tekens

### <a name="ac-18---shared-account-access-is-prohibited"></a>AC-1.8 - Toegang tot gedeelde account is verboden

- Alle autorisatieregels behalve RootManageSharedAccessKey moeten worden verwijderd uit de naamruimte van servicebus

### <a name="ac-19--system-must-restrict-access-to-authorized-users"></a>AC-1.9 -System moet de toegang tot geautoriseerde gebruikers beperken.

- Onbeperkte netwerktoegang tot opslagaccounts controleren

### <a name="ac--114--system-must-enforce-access-rights"></a>AC- 1.14 -System moet toegangsrechten afdwingen.

- \[Voorbeeld:\]Vereiste vereisten implementeren voor het controleren van Windows VM's-configuraties in 'Toewijzing van gebruikersrechten'

### <a name="ac--115--prevent-unauthorized-access-to-security-relevant-information-or-functions"></a>AC- 1.15 -Voorkom ongeautoriseerde toegang tot relevante beveiligingsinformatie of -functies.

- \[Voorbeeld:\]Controleresultaten van Windows VM's weergeven in 'Beveiligingsopties - Systeeminstellingen'

### <a name="ac-1-21---separation-of-duties-must-be-enforced-through-appropriate-assignment-of-role"></a>AC-1-21 - Scheiding van taken moet worden afgedwongen door middel van passende toewijzing van de rol.

- [Preview\]: Role-Based Access Control (RBAC) moet worden gebruikt op Kubernetes Services

### <a name="ac-140--ensure-that-systems-are-not-connecting-trusted-network-and-untrusted-networks-at-the-same-time"></a>AC-1.40- Zorg ervoor dat systemen niet tegelijkertijd vertrouwde netwerk- en niet-vertrouwde netwerken met elkaar verbinden.

- \[Voorbeeld:\]Vereiste vereisten implementeren voor het controleren van Windows VM's configuraties in 'Beveiligingsopties - Netwerktoegang'

### <a name="ac-142--ac--143---remote-access-for-non-employees-must-be-restricted-to-allow-access-only-to-specifically-approved-information-systems"></a>AC-1.42 & AC- 1.43 - Toegang op afstand voor niet-werknemers moet worden beperkt om alleen toegang te verlenen tot specifiek goedgekeurde informatiesystemen

- \[Preview:\]Controleresultaten van Linux VM's weergeven die externe verbindingen van accounts zonder wachtwoorden mogelijk maken

### <a name="ac-150--log-security-related-events-for-all-information-system-components"></a>AC-1.50- Log beveiligingsgerelateerde gebeurtenissen voor alle onderdelen van het informatiesysteem.

- Diagnostische logboeken in Logische Apps moeten zijn ingeschakeld

### <a name="ac-154--ensure-multi-factor-authentication-mfa-is-enabled-for-all-cloud-console-users"></a>AC-1.54- Zorg ervoor dat multi-factor authenticatie (MFA) is ingeschakeld voor alle cloudconsolegebruikers.

- MFA moet accounts zijn ingeschakeld met schrijfmachtigingen voor uw abonnement
- Multi-Factor Authentication (MFA) moet zijn ingeschakeld voor alle abonnementsaccounts met schrijfrechten om een schending van accounts of bronnen te voorkomen.

## <a name="auditing--logging"></a>Controle & logboekregistratie

### <a name="al-21--successful-and-unsuccessful-events-must-be-logged"></a>AL-2.1- Succesvolle en mislukte gebeurtenissen moeten worden geregistreerd.

- Diagnostische logboeken in zoekservices moeten zijn ingeschakeld

### <a name="al--216---network-devicesinstances-must-log-any-event-classified-as-a-critical-security-event-by-that-network-deviceinstance-elbs-web-application-firewalls-etc"></a>AL -2.16 - Netwerkapparaten/-instanties moeten elke gebeurtenis registreren die door dat netwerkapparaat/-exemplaar (ELB's, firewalls van webtoepassingen, enz.) is geclassificeerd als een kritieke beveiligingsgebeurtenis.

- \[Voorbeeld:\]Controleresultaten van Windows VM's weergeven in 'Beveiligingsopties - accounts'

### <a name="al-217--serversinstances-must-log-any-event-classified-as-a-critical-security-event-by-that-serverinstance"></a>AL-2.17- Servers/instances moeten elke gebeurtenis registreren die door die server/instantie is geclassificeerd als een kritieke beveiligingsgebeurtenis

- \[Voorbeeld:\]Controleresultaten van Windows VM's weergeven in 'Beveiligingsopties - accounts'

### <a name="al-219---domain-events-must-log-any-event-classified-as-a-critical-or-high-security-event-by-the-domain-management-software"></a>AL-2.19 - Domeingebeurtenissen moeten elke gebeurtenis registreren die door de domeinbeheersoftware is geclassificeerd als een kritieke of hoogbeveiligde gebeurtenis

- \[Voorbeeld:\]Controleresultaten van Windows VM's weergeven in 'Beveiligingsopties - accounts'
- \[Voorbeeld:\]Vereisten implementeren voor het controleren van Windows VM's in 'Beveiligingsopties - Microsoft Network Client'

### <a name="al-220--domain-events-must-log-any-event-classified-as-a-critical-security-event-by-domain-security-controls"></a>AL-2.20- Domeingebeurtenissen moeten elke gebeurtenis registreren die is geclassificeerd als een kritieke beveiligingsgebeurtenis door domeinbeveiligingsbesturingselementen

- \[Voorbeeld:\]Controleresultaten van Windows VM's weergeven in 'Beveiligingsopties - accounts'

### <a name="al-221--domain-events-must-log-any-access-or-changes-to-the-domain-log"></a>AL-2.21- Domeingebeurtenissen moeten elke toegang of wijzigingen in het domeinlogboek registreren

- \[Voorbeeld:\]Controleresultaten van Windows VM's weergeven in 'Beveiligingsopties - herstelconsole'

## <a name="cryptographic-controls"></a>Cryptografische besturingselementen

### <a name="cc-42--applications-and-systems-must-use-current-cryptographic-solutions-for-protecting-data"></a>CC-4.2- Toepassingen en systemen moeten de huidige cryptografische oplossingen gebruiken voor het beschermen van gegevens.

- Transparante gegevensversleuteling in SQL-databases moet worden ingeschakeld
- Transparante gegevensversleuteling moet worden ingeschakeld om gegevens-rust te beschermen en te voldoen aan de nalevingsvereisten

### <a name="cc-45--digital-certificates-must-be-signed-by-an-approved-certificate-authority"></a>CC-4.5- Digitale certificaten moeten worden ondertekend door een erkende certificaatautoriteit.

- \[Voorbeeld:\]controleresultaten van Windows VM's weergeven die certificaten bevatten die binnen het opgegeven aantal dagen zijn verlopen

### <a name="cc-46--digital-certificates-must-be-uniquely-assigned-to-a-user-or-device"></a>CC-4.6- Digitale certificaten moeten uniek worden toegewezen aan een gebruiker of apparaat.

- \[Voorbeeld:\]Vereiste vereisten implementeren om Windows VM's te controleren die certificaten bevatten die binnen het opgegeven aantal dagen zijn verlopen

### <a name="cc-47--cryptographic-material-must-be-stored-to-enable-decryption-of-the-records-for-the-length-of-time-the-records-are-retained"></a>CC-4.7- Cryptografisch materiaal moet worden opgeslagen om decryptie van de records mogelijk te maken voor de duur van de tijd dat de records worden bewaard.

- Schijfversleuteling moet worden toegepast op virtuele machines
- VM's zonder ingeschakelde schijfversleuteling worden gecontroleerd door Azure Security Center als aanbevelingen

### <a name="cc-48--secret-and-private-keys-must-be-stored-securely"></a>CC-4.8- Geheime en privésleutels moeten veilig worden opgeslagen.

- Transparante gegevensversleuteling in SQL-databases moet worden ingeschakeld
- Transparante gegevensversleuteling moet worden ingeschakeld om gegevens-rust te beschermen en te voldoen aan de nalevingsvereisten

## <a name="change--config-management"></a>Verandering & Config Management

### <a name="cm-52--only-authorized-users-may-implement-approved-changes-on-the-system"></a>CM-5.2- Alleen geautoriseerde gebruikers mogen goedgekeurde wijzigingen op het systeem doorvoeren.

- Er moeten systeemupdates op uw computers zijn geïnstalleerd
- Ontbrekende beveiligingssysteemupdates op uw servers worden gecontroleerd door Azure Security Center als aanbevelingen

### <a name="cm-512--maintain-an-up-to-date-complete-accurate-and-readily-available-baseline-configuration-of-the-information-system"></a>CM-5.12- Onderhoud een up-to-date, complete, nauwkeurige en direct beschikbare basislijnconfiguratie van het informatiesysteem.

- Er moeten systeemupdates op uw computers zijn geïnstalleerd
- Ontbrekende beveiligingssysteemupdates op uw servers worden gecontroleerd door Azure Security Center als aanbevelingen

### <a name="cm-513--employ-automated-tools-to-maintain-a-baseline-configuration-of-the-information-system"></a>CM-5.13- Gebruik geautomatiseerde tools om een basislijnconfiguratie van het informatiesysteem te behouden.

- Er moeten systeemupdates op uw computers zijn geïnstalleerd
- Ontbrekende beveiligingssysteemupdates op uw servers worden gecontroleerd door Azure Security Center als aanbevelingen

### <a name="cm-514--identify-and-disable-unnecessary-andor-non-secure-functions-ports-protocols-and-services"></a>CM-5.14- Identificeren en uitschakelen van onnodige en/of niet-beveiligde functies, poorten, protocollen en services.

- Netwerkinterfaces moeten IP-forwarding uitschakelen
- \[Voorbeeld:\]IP Forwarding op uw virtuele machine moet worden uitgeschakeld

### <a name="cm-519--monitor-changes-to-the-security-configuration-settings"></a>CM-5.19- Monitor wijzigingen in de instellingen voor beveiligingsconfiguratie.

- Diagnostische instellingen voor netwerkbeveiligingsgroepen implementeren

### <a name="cm-522--ensure-that-only-authorized-software-and-updates-are-installed-on-company-systems"></a>CM-5.22- Zorg ervoor dat alleen geautoriseerde software en updates worden geïnstalleerd op bedrijfssystemen.

- Er moeten systeemupdates op uw computers zijn geïnstalleerd
- Ontbrekende beveiligingssysteemupdates op uw servers worden gecontroleerd door Azure Security Center als aanbevelingen

## <a name="identity--authentication"></a>Verificatie & identiteit

### <a name="ia-71--user-accounts-must-be-uniquely-assigned-to-individuals-for-access-to-information-that-is-not-classified-as-public-account-ids-must-be-constructed-using-a-standardized-logical-format"></a>IA-7.1- Gebruikersaccounts moeten uniek zijn toegewezen aan personen voor toegang tot informatie die niet als Openbaar is geclassificeerd. Account-id's moeten worden geconstrueerd met behulp van een gestandaardiseerde logische indeling.

- Externe accounts met eigenaarmachtigingen moeten uit uw abonnement worden verwijderd
- Externe accounts met machtigingen van de eigenaar moeten uit uw abonnement worden verwijderd om niet-bewaakte toegang te voorkomen.

## <a name="network-security"></a>Netwerkbeveiliging

### <a name="ns-92--access-to-network-device-management-functionality-is-restricted-to-authorized-users"></a>NS-9.2- Toegang tot netwerkapparaatbeheerfunctionaliteit is beperkt tot geautoriseerde gebruikers.

- \[Voorbeeld:\]Vereiste vereisten implementeren voor het controleren van Windows VM's configuraties in 'Beveiligingsopties - Netwerktoegang'

### <a name="ns-93--all-network-devices-must-be-configured-using-their-most-secure-configurations"></a>NS-9.3- Alle netwerkapparaten moeten zijn geconfigureerd met behulp van hun meest veilige configuraties.

- \[Voorbeeld:\]Vereiste vereisten implementeren voor het controleren van Windows VM's configuraties in 'Beveiligingsopties - Netwerktoegang'

### <a name="ns-95--all-network-connections-to-a-system-through-a-firewall-must-be-approved-and-audited-on-a-regular-basis"></a>NS-9.5- Alle netwerkverbindingen met een systeem via een firewall moeten regelmatig worden goedgekeurd en gecontroleerd.

- \[Voorbeeld:\]Controleresultaten van Windows VM's weergeven in 'Windows Firewall Properties'

### <a name="ns-97--appropriate-controls-must-be-present-at-any-boundary-between-a-trusted-network-and-any-untrusted-or-public-network"></a>NS-9.7- Geschikte besturingselementen moeten aanwezig zijn op elke grens tussen een vertrouwd netwerk en een niet-vertrouwd of openbaar netwerk.

- \[Voorbeeld:\]Vereisten implementeren voor het controleren van Windows VM's configuraties in 'Windows Firewall Properties'

## <a name="security-planning"></a>Beveiliging plannen

### <a name="sp-113--threats-must-be-identified-that-could-negatively-impact-the-confidentiality-integrity-or-availability-of-company-information-and-content-along-with-the-likelihood-of-their-occurrence"></a>SP-11.3- Bedreigingen moeten worden geïdentificeerd die een negatieve invloed kunnen hebben op de vertrouwelijkheid, integriteit of beschikbaarheid van bedrijfsinformatie en -inhoud, samen met de waarschijnlijkheid van het optreden ervan.

- Geavanceerde typen bedreigingsbeveiliging moeten worden ingesteld op 'Alles' in SQL-beheerde instantie Advanced Data Security-instellingen

### <a name="security-continuity"></a>Continuïteit van de beveiliging

## <a name="sc-125--data-in-long-term-storage-must-be-accessible-throughout-the-retention-period-and-protected-against-media-degradation-and-technology-changes"></a>SC-12.5- Gegevens in langdurige opslag moeten gedurende de bewaarperiode toegankelijk zijn en beschermd zijn tegen mediadegradatie en technologische veranderingen.

- SQL-servers moeten worden geconfigureerd met controleretentiedagen van meer dan 90 dagen.
- Controleer SQL-servers geconfigureerd met een controlebewaartijd van minder dan 90 dagen.

## <a name="system-integrity"></a>Systeemintegriteit

### <a name="si-143--only-authorized-personnel-may-monitor-network-and-user-activities"></a>SI-14.3- Alleen geautoriseerd personeel mag de netwerk- en gebruikersactiviteiten controleren.

- Kwetsbaarheden in uw SQL-databases moeten worden verholpen
- Monitor Vulnerability Assessment scan resultaten en aanbevelingen voor het oplossen van database kwetsbaarheden.

### <a name="si-144--internet-facing-systems-must-have-intrusion-detection"></a>SI-14.4- Internet facing systemen moeten inbraakdetectie hebben.

- Bedreigingsdetectie implementeren op SQL-servers

### <a name="si-1413--standardized-centrally-managed-anti-malware-software-should-be-implemented-across-the-company"></a>SI-14.13- Gestandaardiseerde centraal beheerde anti-malware software moet worden geïmplementeerd in het hele bedrijf.

- Standaard Microsoft IaaSAntimalware-extensie implementeren voor Windows Server

### <a name="si-1414--anti-malware-software-must-scan-computers-and-media-weekly-at-a-minimum"></a>SI-14.14- Anti-malware software moet computers en media wekelijks scannen op een minimum.

- Standaard Microsoft IaaSAntimalware-extensie implementeren voor Windows Server

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

### <a name="vm-154--ensure-that-applications-are-scanned-for-vulnerabilities-on-a-monthly-basis"></a>VM-15.4- Zorg ervoor dat toepassingen maandelijks worden gescand op kwetsbaarheden.

- Kwetsbaarheden in beveiligingsconfiguratie op uw virtuele machineschaalsets moeten worden verholpen
- Controleer de beveiligingslekken van het besturingssysteem op uw virtuele machineschaalsets om ze te beschermen tegen aanvallen.

### <a name="vm-155--ensure-that-vulnerabilities-are-identified-paired-to-threats-and-evaluated-for-risk"></a>VM-15.5- Zorg ervoor dat kwetsbaarheden worden geïdentificeerd, gekoppeld aan bedreigingen en geëvalueerd op risico's.

- Kwetsbaarheden in beveiligingsconfiguratie op uw virtuele machineschaalsets moeten worden verholpen
- Controleer de beveiligingslekken van het besturingssysteem op uw virtuele machineschaalsets om ze te beschermen tegen aanvallen.

### <a name="vm-156--ensure-that-identified-vulnerabilities-have-been-remediated-within-a-mutually-agreed-upon-timeline"></a>VM-15.6- Zorg ervoor dat geïdentificeerde kwetsbaarheden zijn verholpen binnen een wederzijds overeengekomen tijdlijn.

- Kwetsbaarheden in beveiligingsconfiguratie op uw virtuele machineschaalsets moeten worden verholpen
- Controleer de beveiligingslekken van het besturingssysteem op uw virtuele machineschaalsets om ze te beschermen tegen aanvallen.

### <a name="vm-157--access-to-and-use-of-vulnerability-management-systems-must-be-restricted-to-authorized-personnel"></a>VM-15.7- Toegang tot en gebruik van systemen voor kwetsbaarheidsbeheer moet worden beperkt tot geautoriseerd personeel.

- Kwetsbaarheden in beveiligingsconfiguratie op uw virtuele machineschaalsets moeten worden verholpen
- Controleer de beveiligingslekken van het besturingssysteem op uw virtuele machineschaalsets om ze te beschermen tegen aanvallen.

> [!NOTE]
> De beschikbaarheid van specifieke Azure Policy-definities kan variëren in Azure Government en andere nationale clouds. 

## <a name="next-steps"></a>Volgende stappen

U hebt de controletoewijzing van het voorbeeld van de mediablauwdruk bekeken. Ga vervolgens naar de volgende artikelen voor meer informatie over het overzicht en hoe u dit voorbeeld implementeert:

> [!div class="next step action"]
> [Mediablauwdruk - Overzicht](./control-mapping.md)
> [Media blauwdruk - Stappen implementeren](./deploy.md)

Aanvullende artikelen over blauwdrukken en het gebruik hiervan:

- Meer informatie over de [levenscyclus van de blauwdruk](../../concepts/lifecycle.md).
- Begrijpen hoe [statische en dynamische parameters](../../concepts/parameters.md)te gebruiken.
- Leer de volgorde van de [blauwdrukvolgorde](../../concepts/sequencing-order.md)aan te passen.
- Ontdek hoe u gebruik maken van het vergrendelen van [blauwdrukbronnen.](../../concepts/resource-locking.md)
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../../how-to/update-existing-assignments.md).
