---
title: Besturings elementen voor voor beeld van media blauw drukken
description: De toewijzing van de voor beelden van media blauw beheer beheren. Elk besturings element wordt toegewezen aan een of meer Azure-beleids regels die helpen bij de evaluatie.
ms.date: 02/24/2020
ms.topic: sample
ms.openlocfilehash: bdc4ef65c6d61a9d4217d7873bb29bd787842e71
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78201904"
---
# <a name="control-mapping-of-the-media-blueprint-sample"></a>Toewijzing van het voor beeld van media blauw drukken

In het volgende artikel wordt uitgelegd hoe het voor beeld van Azure blauw drukken media blauw wordt toegewezen aan de media besturings elementen. Zie [Media](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html)voor meer informatie over de besturings elementen.

De volgende toewijzingen zijn de besturings elementen van **Media** . Gebruik de navigatie aan de rechter kant om rechtstreeks naar een specifieke besturings element koppeling te gaan. Veel van de toegewezen besturings elementen worden geïmplementeerd met een [Azure Policy](../../../policy/overview.md) -initiatief. Als u het complete initiatief wilt bekijken, opent u **beleid** in het Azure Portal en selecteert u de pagina **definities** . Zoek en selecteer vervolgens de **\[preview-\]: audit media Controls** ingebouwd Policy Initiative.

> [!IMPORTANT]
> Elk besturings element hieronder is gekoppeld aan een of meer [Azure Policy](../../../policy/overview.md) definities. Met deze beleids regels kunt u de naleving van het besturings element [beoordelen](../../../policy/how-to/get-compliance-data.md) . Er is echter vaak geen 1:1-of volledige overeenkomst tussen een besturings element en een of meer beleids regels. Als zodanig is de **naleving** in azure Policy alleen bedoeld voor het beleid zelf. Dit garandeert niet dat u volledig compatibel bent met alle vereisten van een besturings element. Daarnaast bevat de nalevings standaard besturings elementen die niet worden behandeld door Azure Policy definities op dit moment. Daarom is naleving in Azure Policy slechts een gedeeltelijke weer gave van uw algemene nalevings status. De koppelingen tussen de besturings elementen en Azure Policy definities voor dit voor beeld van deze naleving blauw druk kunnen na verloop van tijd veranderen. Als u de wijzigings geschiedenis wilt weer geven, raadpleegt u de [github commit-geschiedenis](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/media/control-mapping.md).

## <a name="access-control"></a>Toegangsbeheer

### <a name="ac-11--ensure-no-root-access-key-exists"></a>AC-1,1-Zorg ervoor dat er geen hoofd toegangs sleutel bestaat

- \[preview\]: vereisten implementeren voor het controleren van Windows-Vm's die de opgegeven certificaten niet in de vertrouwde basis bevatten

### <a name="ac-12---passwords-pins-and-tokens-must-be-protected"></a>AC-1,2: wacht woorden, pincodes en tokens moeten worden beveiligd

- \[preview\]: vereisten implementeren om Windows-Vm's te controleren die de minimale wachtwoord lengte niet beperken tot 14 tekens

### <a name="ac-18---shared-account-access-is-prohibited"></a>AC-1,8-toegang tot gedeeld account is niet toegestaan

- Alle autorisatie regels behalve RootManageSharedAccessKey moeten worden verwijderd uit Service Bus naam ruimte

### <a name="ac-19--system-must-restrict-access-to-authorized-users"></a>AC-1,9-het systeem moet de toegang tot gemachtigde gebruikers beperken.

- Onbeperkte netwerk toegang tot opslag accounts controleren

### <a name="ac--114--system-must-enforce-access-rights"></a>AC-1,14-systeem moet toegangs rechten afdwingen.

- \[preview\]: vereisten implementeren voor het controleren van Windows-Vm's in ' toewijzing van gebruikers rechten '

### <a name="ac--115--prevent-unauthorized-access-to-security-relevant-information-or-functions"></a>AC-1,15-onbevoegde toegang tot beveiligings relevante informatie of functies voor komen.

- \[preview\]: controle resultaten van configuraties van Windows-Vm's weer geven in beveiligings opties-systeem instellingen

### <a name="ac-1-21---separation-of-duties-must-be-enforced-through-appropriate-assignment-of-role"></a>AC-1-21-schei ding van taken moet worden afgedwongen via de juiste toewijzing van rol.

- [Preview\]: op rollen gebaseerde Access Control (RBAC) moet worden gebruikt voor Kubernetes-Services

### <a name="ac-140--ensure-that-systems-are-not-connecting-trusted-network-and-untrusted-networks-at-the-same-time"></a>AC-1,40-zorg ervoor dat systemen niet tegelijkertijd een vertrouwd netwerk en niet-vertrouwde netwerken verbinden.

- \[preview\]: vereisten implementeren voor het controleren van Windows-Vm's in beveiligings opties-netwerk toegang

### <a name="ac-142--ac--143---remote-access-for-non-employees-must-be-restricted-to-allow-access-only-to-specifically-approved-information-systems"></a>AC-1,42 & AC-1,43: externe toegang voor niet-werk nemers moet worden beperkt om alleen toegang te verlenen aan specifiek goedgekeurde informatie systemen

- \[preview\]: controle resultaten weer geven van Linux-Vm's die externe verbindingen toestaan van accounts zonder wacht woorden

### <a name="ac-150--log-security-related-events-for-all-information-system-components"></a>AC-1,50: Registreer beveiligings gebeurtenissen voor alle informatie systeem onderdelen.

- Diagnostische logboeken in Logic Apps moeten worden ingeschakeld

### <a name="ac-154--ensure-multi-factor-authentication-mfa-is-enabled-for-all-cloud-console-users"></a>AC-1,54-zorg ervoor dat multi-factor Authentication (MFA) is ingeschakeld voor alle Cloud console-gebruikers.

- MFA moet zijn ingeschakeld voor accounts met schrijf machtigingen voor uw abonnement
- Multi-Factor Authentication (MFA) moet zijn ingeschakeld voor alle abonnements accounts met schrijf bevoegdheden om te voor komen dat accounts of bronnen worden geschonden.

## <a name="auditing--logging"></a>& Logboek registratie controleren

### <a name="al-21--successful-and-unsuccessful-events-must-be-logged"></a>AL-2,1-geslaagde en mislukte gebeurtenissen moeten worden geregistreerd.

- Diagnostische logboeken in Search Services moeten worden ingeschakeld

### <a name="al--216---network-devicesinstances-must-log-any-event-classified-as-a-critical-security-event-by-that-network-deviceinstance-elbs-web-application-firewalls-etc"></a>AL-2,16-netwerk apparaten/instanties moeten alle gebeurtenissen registreren die zijn geclassificeerd als kritieke beveiligings gebeurtenis door dat netwerk apparaat/exemplaar (ELBs, firewalls voor webtoepassingen, enzovoort).

- \[preview\]: controle resultaten van configuraties van Windows-Vm's weer geven in beveiligings opties-accounts

### <a name="al-217--serversinstances-must-log-any-event-classified-as-a-critical-security-event-by-that-serverinstance"></a>AL-2,17-servers/instanties moeten alle gebeurtenissen registreren die zijn geclassificeerd als kritieke beveiligings gebeurtenis door die Server/exemplaar

- \[preview\]: controle resultaten van configuraties van Windows-Vm's weer geven in beveiligings opties-accounts

### <a name="al-219---domain-events-must-log-any-event-classified-as-a-critical-or-high-security-event-by-the-domain-management-software"></a>AL-2,19-domein gebeurtenissen moeten gebeurtenissen die zijn geclassificeerd als een kritieke of hoge beveiligings gebeurtenis door de domein beheer software registreren

- \[preview\]: controle resultaten van configuraties van Windows-Vm's weer geven in beveiligings opties-accounts
- \[preview\]: vereisten implementeren voor het controleren van Windows-Vm's in beveiligings opties-micro soft-netwerkclient

### <a name="al-220--domain-events-must-log-any-event-classified-as-a-critical-security-event-by-domain-security-controls"></a>AL-2,20-domein gebeurtenissen moeten gebeurtenissen die zijn geclassificeerd als een kritieke beveiligings gebeurtenis, registreren op basis van beveiligings controles van het domein

- \[preview\]: controle resultaten van configuraties van Windows-Vm's weer geven in beveiligings opties-accounts

### <a name="al-221--domain-events-must-log-any-access-or-changes-to-the-domain-log"></a>AL-2,21-domein gebeurtenissen moeten de toegang of wijzigingen in het domein logboek registreren

- \[preview\]: controle resultaten van Windows-Vm's weer geven in beveiligings opties-herstel console

## <a name="cryptographic-controls"></a>Cryptografische besturings elementen

### <a name="cc-42--applications-and-systems-must-use-current-cryptographic-solutions-for-protecting-data"></a>CC-4,2-toepassingen en systemen moeten huidige cryptografische oplossingen gebruiken voor het beveiligen van gegevens.

- Transparent Data Encryption voor SQL-data bases moet zijn ingeschakeld
- Transparante gegevens versleuteling moet zijn ingeschakeld om gegevens op rest te beveiligen en voldoen aan de nalevings vereisten

### <a name="cc-45--digital-certificates-must-be-signed-by-an-approved-certificate-authority"></a>CC-4,5-digitale certificaten moeten worden ondertekend door een goedgekeurde certificerings instantie.

- \[preview\]: controle resultaten weer geven van Windows-Vm's die certificaten bevatten die binnen het opgegeven aantal dagen verlopen

### <a name="cc-46--digital-certificates-must-be-uniquely-assigned-to-a-user-or-device"></a>CC-4,6-digitale certificaten moeten uniek worden toegewezen aan een gebruiker of apparaat.

- \[preview\]: vereisten implementeren voor het controleren van Windows-Vm's die certificaten bevatten die binnen het opgegeven aantal dagen verlopen

### <a name="cc-47--cryptographic-material-must-be-stored-to-enable-decryption-of-the-records-for-the-length-of-time-the-records-are-retained"></a>CC-4,7-cryptografisch materiaal moet worden opgeslagen om het ontsleutelen van de records in te scha kelen voor de periode waarin de records worden bewaard.

- Schijf versleuteling moet worden toegepast op virtuele machines
- Vm's zonder ingeschakelde schijf versleuteling worden bewaakt door Azure Security Center als aanbevelingen

### <a name="cc-48--secret-and-private-keys-must-be-stored-securely"></a>CC-4,8-geheime en persoonlijke sleutels moeten veilig worden opgeslagen.

- Transparent Data Encryption voor SQL-data bases moet zijn ingeschakeld
- Transparante gegevens versleuteling moet zijn ingeschakeld om gegevens op rest te beveiligen en voldoen aan de nalevings vereisten

## <a name="change--config-management"></a>& Configuratie beheer wijzigen

### <a name="cm-52--only-authorized-users-may-implement-approved-changes-on-the-system"></a>CM-5,2: alleen geautoriseerde gebruikers kunnen goedgekeurde wijzigingen op het systeem implementeren.

- Systeem updates moeten worden geïnstalleerd op uw computers
- Ontbrekende updates van het beveiligings systeem op uw servers worden bewaakt door Azure Security Center als aanbevelingen

### <a name="cm-512--maintain-an-up-to-date-complete-accurate-and-readily-available-baseline-configuration-of-the-information-system"></a>CM-5,12: Houd een up-to-date, volledige, accurate en gemakkelijk beschik bare basislijn configuratie van het informatie systeem bij.

- Systeem updates moeten worden geïnstalleerd op uw computers
- Ontbrekende updates van het beveiligings systeem op uw servers worden bewaakt door Azure Security Center als aanbevelingen

### <a name="cm-513--employ-automated-tools-to-maintain-a-baseline-configuration-of-the-information-system"></a>CM-5,13-gebruik automatische hulpprogram ma's voor het onderhouden van een basislijn configuratie van het informatie systeem.

- Systeem updates moeten worden geïnstalleerd op uw computers
- Ontbrekende updates van het beveiligings systeem op uw servers worden bewaakt door Azure Security Center als aanbevelingen

### <a name="cm-514--identify-and-disable-unnecessary-andor-non-secure-functions-ports-protocols-and-services"></a>CM-5,14-overbodige en/of niet-veilige functies, poorten, protocollen en services identificeren en uitschakelen.

- Netwerk interfaces moeten door sturen via IP uitschakelen
- \[preview\]: door sturen via IP op uw virtuele machine moet worden uitgeschakeld

### <a name="cm-519--monitor-changes-to-the-security-configuration-settings"></a>CM-5,19-Bewaak wijzigingen in de beveiligings configuratie-instellingen.

- Diagnostische instellingen voor netwerk beveiligings groepen implementeren

### <a name="cm-522--ensure-that-only-authorized-software-and-updates-are-installed-on-company-systems"></a>CM-5,22-zorg ervoor dat alleen geautoriseerde software en updates worden geïnstalleerd op bedrijfs systemen.

- Systeem updates moeten worden geïnstalleerd op uw computers
- Ontbrekende updates van het beveiligings systeem op uw servers worden bewaakt door Azure Security Center als aanbevelingen

## <a name="identity--authentication"></a>Verificatie van identiteits &

### <a name="ia-71--user-accounts-must-be-uniquely-assigned-to-individuals-for-access-to-information-that-is-not-classified-as-public-account-ids-must-be-constructed-using-a-standardized-logical-format"></a>IA-7,1: gebruikers accounts moeten uniek worden toegewezen aan individuen voor toegang tot informatie die niet openbaar is. Account-Id's moeten worden samengesteld met behulp van een gestandaardiseerde logische indeling.

- Externe accounts met eigenaars machtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met eigenaars machtigingen moeten worden verwijderd van uw abonnement om onbewaakte toegang te voor komen.

## <a name="network-security"></a>Netwerkbeveiliging

### <a name="ns-92--access-to-network-device-management-functionality-is-restricted-to-authorized-users"></a>NS-9,2: toegang tot de beheer functionaliteit voor netwerk apparaten is beperkt tot gemachtigde gebruikers.

- \[preview\]: vereisten implementeren voor het controleren van Windows-Vm's in beveiligings opties-netwerk toegang

### <a name="ns-93--all-network-devices-must-be-configured-using-their-most-secure-configurations"></a>NS-9,3: alle netwerk apparaten moeten worden geconfigureerd met behulp van de veiligste configuraties.

- \[preview\]: vereisten implementeren voor het controleren van Windows-Vm's in beveiligings opties-netwerk toegang

### <a name="ns-95--all-network-connections-to-a-system-through-a-firewall-must-be-approved-and-audited-on-a-regular-basis"></a>NS-9,5: alle netwerk verbindingen met een systeem via een firewall moeten regel matig worden goedgekeurd en gecontroleerd.

- \[preview\]: controle resultaten van Windows-Vm's weer geven in Windows Firewall eigenschappen

### <a name="ns-97--appropriate-controls-must-be-present-at-any-boundary-between-a-trusted-network-and-any-untrusted-or-public-network"></a>NS-9,7: de juiste besturings elementen moeten aanwezig zijn op een wille keurige grens tussen een vertrouwd netwerk en een niet-vertrouwd of openbaar netwerk.

- \[preview\]: vereisten implementeren voor het controleren van Windows-Vm's in Windows Firewall eigenschappen

## <a name="security-planning"></a>Beveiligings planning

### <a name="sp-113--threats-must-be-identified-that-could-negatively-impact-the-confidentiality-integrity-or-availability-of-company-information-and-content-along-with-the-likelihood-of-their-occurrence"></a>SP-11,3-bedreigingen moeten worden geïdentificeerd die een negatieve invloed kunnen hebben op de vertrouwelijkheid, integriteit of Beschik baarheid van Bedrijfs gegevens en-inhoud, samen met de kans op een voorval.

- De geavanceerde beveiligings typen voor bedreigingen moeten worden ingesteld op ' all ' in de instellingen voor geavanceerde gegevens beveiliging van SQL Managed instance

### <a name="security-continuity"></a>Beveiligings continuïteit

## <a name="sc-125--data-in-long-term-storage-must-be-accessible-throughout-the-retention-period-and-protected-against-media-degradation-and-technology-changes"></a>SC-12,5: gegevens in lange termijn opslag moeten toegankelijk zijn tijdens de Bewaar periode en worden beschermd tegen media-degradatie en technologie wijzigingen.

- SQL-servers moeten worden geconfigureerd met controle dagen van meer dan 90 dagen.
- Controleer de configuratie van SQL-servers die zijn geconfigureerd met een Bewaar periode van minder dan 90 dagen.

## <a name="system-integrity"></a>Systeem integriteit

### <a name="si-143--only-authorized-personnel-may-monitor-network-and-user-activities"></a>SI-14,3-alleen geautoriseerd personeel kan netwerk-en gebruikers activiteiten bewaken.

- Beveiligings problemen voor uw SQL-data bases moeten worden hersteld
- Controle resultaten van de evaluatie van beveiligings problemen en aanbevelingen voor het oplossen van problemen met de data base bewaken.

### <a name="si-144--internet-facing-systems-must-have-intrusion-detection"></a>SI-14,4-Internet gerichte systemen moeten detectie van inbraak hebben.

- Detectie van bedreigingen op SQL-servers implementeren

### <a name="si-1413--standardized-centrally-managed-anti-malware-software-should-be-implemented-across-the-company"></a>SI-14,13-gestandaardiseerde, centraal beheerde anti-malware-software moet worden geïmplementeerd in het hele bedrijf.

- Standaard micro soft IaaSAntimalware-extensie voor Windows Server implementeren

### <a name="si-1414--anti-malware-software-must-scan-computers-and-media-weekly-at-a-minimum"></a>SI-14,14-anti-malware-software moet computers en media wekelijks op een minimum scannen.

- Standaard micro soft IaaSAntimalware-extensie voor Windows Server implementeren

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

### <a name="vm-154--ensure-that-applications-are-scanned-for-vulnerabilities-on-a-monthly-basis"></a>VM-15,4-zorg ervoor dat toepassingen maandelijks worden gescand op beveiligings problemen.

- Beveiligings problemen in de beveiligings configuratie van de schaal sets van virtuele machines moeten worden hersteld
- Controleer de beveiligings problemen voor het besturings systeem op uw schaal sets voor virtuele machines om ze te beschermen tegen aanvallen.

### <a name="vm-155--ensure-that-vulnerabilities-are-identified-paired-to-threats-and-evaluated-for-risk"></a>VM-15,5-zorg ervoor dat beveiligings problemen worden geïdentificeerd, gekoppeld aan bedreigingen en worden beoordeeld op risico.

- Beveiligings problemen in de beveiligings configuratie van de schaal sets van virtuele machines moeten worden hersteld
- Controleer de beveiligings problemen voor het besturings systeem op uw schaal sets voor virtuele machines om ze te beschermen tegen aanvallen.

### <a name="vm-156--ensure-that-identified-vulnerabilities-have-been-remediated-within-a-mutually-agreed-upon-timeline"></a>VM-15,6-Zorg ervoor dat geïdentificeerde beveiligings problemen zijn opgelost binnen een onderling overeengekomen tijd lijn.

- Beveiligings problemen in de beveiligings configuratie van de schaal sets van virtuele machines moeten worden hersteld
- Controleer de beveiligings problemen voor het besturings systeem op uw schaal sets voor virtuele machines om ze te beschermen tegen aanvallen.

### <a name="vm-157--access-to-and-use-of-vulnerability-management-systems-must-be-restricted-to-authorized-personnel"></a>VM-15,7: toegang tot en gebruik van systemen voor beveiligings beheer moet worden beperkt tot geautoriseerd personeel.

- Beveiligings problemen in de beveiligings configuratie van de schaal sets van virtuele machines moeten worden hersteld
- Controleer de beveiligings problemen voor het besturings systeem op uw schaal sets voor virtuele machines om ze te beschermen tegen aanvallen.

> [!NOTE]
> De beschik baarheid van specifieke Azure Policy definities kan verschillen in Azure Government en andere nationale Clouds. 

## <a name="next-steps"></a>Volgende stappen

U hebt de beheer toewijzing van het voor beeld van media-blauw drukken gecontroleerd. Ga vervolgens naar de volgende artikelen voor meer informatie over het overzicht en hoe u dit voor beeld implementeert:

> [!div class="next step action"]
> [Blauw druk voor media-overzicht](./control-mapping.md)
> [Medium blauw druk-stappen implementeren](./deploy.md)

Aanvullende artikelen over blauwdrukken en het gebruik hiervan:

- Meer informatie over de [levenscyclus van een blauwdruk](../../concepts/lifecycle.md).
- Meer informatie over hoe u [statische en dynamische parameters](../../concepts/parameters.md) gebruikt.
- Meer informatie over hoe u de [blauwdrukvolgorde](../../concepts/sequencing-order.md) aanpast.
- Meer informatie over hoe u gebruikmaakt van [resourcevergrendeling in blauwdrukken](../../concepts/resource-locking.md).
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../../how-to/update-existing-assignments.md).
