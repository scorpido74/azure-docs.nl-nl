---
title: Overzicht van beveiligde blauw druk voor de Australische overheid
description: Overzicht van het beveiligde blauw druk-voor beeld van de Australische overheid Met dit blauw druk-voor beeld kunnen klanten specifieke ISM-beveiligde controles beoordelen.
ms.date: 03/10/2020
ms.topic: sample
ms.openlocfilehash: e2f64943e9db207feb3f59a905cad994553a6cd6
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82595029"
---
# <a name="control-mapping-of-the-australian-government-ism-protected-blueprint-sample"></a>Controle van de toewijzing van het beveiligde blauw druk-voor beeld van de Australische overheid

Het volgende artikel bevat informatie over de manier waarop de Azure-blauw drukken met de ISM-proef voor het beveiligde blauw druk is gekoppeld aan de ISM beveiligde controles. Zie [beveiligde beveiliging](https://www.cyber.gov.au/ism)voor meer informatie over de besturings elementen.

De volgende toewijzingen zijn de beveiligde besturings elementen in **ISM** . Gebruik de navigatie aan de rechter kant om rechtstreeks naar een specifieke besturings element koppeling te gaan. Veel van de toegewezen besturings elementen worden geïmplementeerd met een [Azure Policy](../../../policy/overview.md) -initiatief. Als u het complete initiatief wilt bekijken, opent u **beleid** in het Azure Portal en selecteert u de pagina **definities** . Zoek en selecteer vervolgens de ** \[preview\]: controleren van de beveiligde controles van de Australische overheid en implementeer specifieke VM-extensies ter ondersteuning** van de ingebouwde beleids initiatieven voor controle vereisten.

> [!IMPORTANT]
> Elk besturings element hieronder is gekoppeld aan een of meer [Azure Policy](../../../policy/overview.md) definities. Met deze beleids regels kunt u de naleving van het besturings element [beoordelen](../../../policy/how-to/get-compliance-data.md) . Er is echter vaak geen 1:1-of volledige overeenkomst tussen een besturings element en een of meer beleids regels. Als zodanig is de **naleving** in azure Policy alleen bedoeld voor het beleid zelf. Dit garandeert niet dat u volledig compatibel bent met alle vereisten van een besturings element. Daarnaast bevat de nalevings standaard besturings elementen die niet worden behandeld door Azure Policy definities op dit moment. Daarom is naleving in Azure Policy slechts een gedeeltelijke weer gave van uw algemene nalevings status. De koppelingen tussen de besturings elementen en Azure Policy definities voor dit voor beeld van deze naleving blauw druk kunnen na verloop van tijd veranderen.
> Als u de wijzigings geschiedenis wilt weer geven, raadpleegt u de [github commit-geschiedenis](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/ism-protected/control-mapping.md).


## <a name="location-constraints"></a>Locatie beperkingen

Met deze blauw druk kunt u de locatie voor de implementatie van alle resources en resource groepen beperken tot ' Australië-centraal ', ' Australië Central2 ', ' Australië-oost ' en ' Australië-zuidoost ' door de volgende Azure Policy definities toe te wijzen:

- Toegestane locaties (is vastgelegd in ' Australië-centraal ', ' Australia Central2 ', ' Australië-oost ' en ' Australië-zuidoost ')
- Toegestane locaties voor resource groepen (is vastgelegd in ' Australië-centraal ', ' Australia Central2 ', ' Australië-oost ' en ' Australië-zuidoost ')

## <a name="guidelines-for-personnel-security---access-to-systems-and-their-resources"></a>Richt lijnen voor personeels beveiliging-toegang tot systemen en hun resources

### <a name="0414-personnel-granted-access-to-a-system-and-its-resources-are-uniquely-identifiable"></a>0414 mede werkers die toegang hebben gekregen tot een systeem en de bijbehorende resources, zijn uniek identificeerbaar

- MFA moet zijn ingeschakeld voor accounts met eigenaars machtigingen voor uw abonnement
- MFA moet zijn ingeschakeld voor accounts met schrijf machtigingen voor uw abonnement
- MFA moet zijn ingeschakeld voor accounts met lees machtigingen voor uw abonnement

### <a name="1503-standard-access-to-systems-applications-and-data-repositories-is-limited-to-that-required-for-personnel-to-undertake-their-duties"></a>1503 de standaard toegang tot systemen, toepassingen en gegevensopslag plaatsen is beperkt tot de vereisten die nodig zijn voor personeel om hun taken uit te voeren

- Er moeten Maxi maal drie eigen aren worden opgegeven voor uw abonnement
- Er moet meer dan één eigenaar aan uw abonnement zijn toegewezen
- Controle resultaten weer geven van Windows-Vm's waarin de groep Administrators een van de opgegeven leden bevat
- Vereisten implementeren voor het controleren van virtuele Windows-machines waarin de groep Administrators een van de opgegeven leden bevat

### <a name="1507-privileged-access-to-systems-applications-and-data-repositories-is-validated-when-first-requested-and-revalidated-on-an-annual-or-more-frequent-basis"></a>1507 bevoorrechte toegang tot systemen, toepassingen en gegevens opslagplaatsen worden gevalideerd wanneer het voor het eerst wordt aangevraagd en opnieuw gevalideerd op basis van een jaar of vaker.

- Controle resultaten weer geven van Windows-Vm's waarin de groep Administrators een van de opgegeven leden bevat
- Vereisten implementeren voor het controleren van virtuele Windows-machines waarin de groep Administrators een van de opgegeven leden bevat

### <a name="1508-privileged-access-to-systems-applications-and-data-repositories-is-limited-to-that-required-for-personnel-to-undertake-their-duties"></a>1508 privileged Access to Systems, Applications and data opslagplaatss is beperkt tot de vereisten die nodig zijn voor personeel om hun taken uit te voeren

- Er moeten Maxi maal drie eigen aren worden opgegeven voor uw abonnement
- Er moet meer dan één eigenaar aan uw abonnement zijn toegewezen
- Controle resultaten weer geven van Windows-Vm's waarin de groep Administrators een van de opgegeven leden bevat
- Vereisten implementeren voor het controleren van virtuele Windows-machines waarin de groep Administrators een van de opgegeven leden bevat
- Just-In-Time-netwerktoegangsbeheer moet worden toegepast op virtuele machines

### <a name="0415-the-use-of-shared-user-accounts-is-strictly-controlled-and-personnel-using-such-accounts-are-uniquely-identifiable"></a>0415 het gebruik van gedeelde gebruikers accounts wordt strikt gecontroleerd en mede werkers die dergelijke accounts gebruiken, zijn uniek identificeerbaar

- Controle resultaten weer geven van Windows-Vm's waarin de groep Administrators een van de opgegeven leden bevat
- Vereisten implementeren voor het controleren van virtuele Windows-machines waarin de groep Administrators een van de opgegeven leden bevat

### <a name="0445-privileged-users-are-assigned-a-dedicated-privileged-account-to-be-used-solely-for-tasks-requiring-privileged-access"></a>aan 0445 privileged-gebruikers is een speciaal privileged account toegewezen dat alleen wordt gebruikt voor taken die privileged-toegang vereisen

- Controle resultaten weer geven van Windows-Vm's waarin de groep Administrators een van de opgegeven leden bevat
- Vereisten implementeren voor het controleren van virtuele Windows-machines waarin de groep Administrators een van de opgegeven leden bevat

### <a name="0430-access-to-systems-applications-and-data-repositories-is-removed-or-suspended-on-the-same-day-personnel-no-longer-have-a-legitimate-requirement-for-access"></a>0430 toegang tot systemen, toepassingen en gegevens opslagplaatsen wordt verwijderd of geschorst op dezelfde werkdag werk nemers hebben niet langer een rechtmatige vereiste voor toegang

- Afgeschafte accounts moeten worden verwijderd uit uw abonnement
- Afgeschafte accounts met eigenaars machtigingen moeten worden verwijderd uit uw abonnement

### <a name="0441-when-personnel-are-granted-temporary-access-to-a-system-effective-security-controls-are-put-in-place-to-restrict-their-access-to-only-information-required-for-them-to-undertake-their-duties"></a>0441 wanneer personeel tijdelijke toegang tot een systeem krijgen, worden de juiste beveiligings maatregelen in stand gezet om hun toegang te beperken tot alleen de informatie die zij nodig hebben om hun taken uit te voeren

- Externe accounts met eigenaars machtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met schrijf machtigingen moeten worden verwijderd uit uw abonnement
- Afgeschafte accounts moeten worden verwijderd uit uw abonnement
- Afgeschafte accounts met eigenaars machtigingen moeten worden verwijderd uit uw abonnement

## <a name="guidelines-for-system-hardening---operating-system-hardening"></a>Richt lijnen voor systeem beveiliging-beveiliging van het besturings systeem

### <a name="1407-the-latest-version-n-or-n-1-version-of-an-operating-system-is-used-for-standard-operating-environments-soes"></a>1407 de meest recente versie (N) of N-1 versie van een besturings systeem wordt gebruikt voor standaard besturings omgevingen (SOEs)

- Er moeten systeemupdates op uw computers zijn geïnstalleerd
- Systeem updates op virtuele-machine schaal sets moeten worden geïnstalleerd

### <a name="0380-unneeded-operating-system-accounts-software-components-services-and-functionality-are-removed-or-disabled"></a>0380 overbodige besturingssysteem accounts, software, onderdelen, services en functionaliteit worden verwijderd of uitgeschakeld

- Afgeschafte accounts moeten worden verwijderd uit uw abonnement
- Afgeschafte accounts met eigenaars machtigingen moeten worden verwijderd uit uw abonnement

### <a name="1490-an-application-whitelisting-solution-is-implemented-on-all-servers-to-restrict-the-execution-of-executables-software-libraries-scripts-and-installers-to-an-approved-set"></a>1490 er is een white list-oplossing voor toepassingen geïmplementeerd op alle servers om de uitvoering van uitvoer bare bestanden, software bibliotheken, scripts en installatie Programma's te beperken tot een goedgekeurde set

- Adaptieve toepassings besturings elementen moeten worden ingeschakeld op virtuele machines

### <a name="1417-antivirus-software-is-implemented-on-workstations-and-servers-and-configured-with-signature-based-detection-enabled-and-set-to-a-high-level-heuristic-based-detection-enabled-and-set-to-a-high-level-detection-signatures-checked-for-currency-and-updated-on-at-least-a-daily-basis-automatic-and-regular-scanning-configured-for-all-fixed-disks-and-removable-media"></a>1417 antivirus software wordt geïmplementeerd op werk stations en servers en is geconfigureerd met: detectie op basis van een hand tekening is ingeschakeld en is ingesteld op een hoog niveau, gedetecteerde detectie handtekeningen en worden ingesteld op een hoge mate, automatische en regel matige scans die zijn geconfigureerd voor alle vaste schijven en verwissel bare media

- Micro soft IaaSAntimalwaree extension moet worden geïmplementeerd op Windows-servers
- Endpoint Protection-oplossing moet worden geïnstalleerd op virtuele-machine schaal sets
- Ontbrekende Endpoint Protection in Azure Security Center controleren

## <a name="guidelines-for-system-hardening---authentication-hardening"></a>Richt lijnen voor het beveiligen van de beveiliging van het systeem

### <a name="1546-users-are-authenticated-before-they-are-granted-access-to-a-system-and-its-resources"></a>1546 gebruikers worden geverifieerd voordat ze toegang krijgen tot een systeem en de bijbehorende bronnen

- Onbeperkte netwerk toegang tot opslag accounts controleren
- Service Fabric-clusters mogen alleen gebruikmaken van Azure Active Directory voor client verificatie
- \[Voor\]beeld: controle resultaten weer geven van Linux-vm's die externe verbindingen toestaan van accounts zonder wacht woorden
- \[Voor\]beeld: vereisten implementeren voor het controleren van virtuele Linux-machines die externe verbindingen toestaan van accounts zonder wacht woorden
- \[Voor\]beeld: controle resultaten weer geven van Linux-vm's met accounts zonder wacht woorden
- \[Voor\]beeld: vereisten implementeren voor het controleren van virtuele Linux-machines met accounts zonder wacht woorden

### <a name="0974-multi-factor-authentication-is-used-to-authenticate-standard-users"></a>0974 multi-factor Authentication wordt gebruikt voor het verifiëren van standaard gebruikers

- MFA moet zijn ingeschakeld voor accounts met lees machtigingen voor uw abonnement

### <a name="1173-multi-factor-authentication-is-used-to-authenticate-all-privileged-users-and-any-other-positions-of-trust"></a>1173 multi-factor Authentication wordt gebruikt voor het verifiëren van alle bevoegde gebruikers en andere vertrouwens posities

- MFA moet zijn ingeschakeld voor accounts met eigenaars machtigingen voor uw abonnement
- MFA moet zijn ingeschakeld voor accounts met schrijf machtigingen voor uw abonnement

### <a name="0421-passphrases-used-for-single-factor-authentication-are-a-minimum-of-14-characters-with-complexity-ideally-as-4-random-words"></a>0421 wachtwoordzinen die worden gebruikt voor verificatie met één factor zijn mini maal 14 tekens met complexiteit, in het ideale geval 4 wille keurige woorden

- \[Voor\]beeld: controle resultaten van Windows-vm's weer geven in beveiligings instellingen-account beleid
- \[Voor\]beeld: vereisten implementeren voor het controleren van Windows-Vm's in beveiligings instellingen-account beleid

## <a name="guidelines-for-system-management---system-administration"></a>Richt lijnen voor systeem beheer-systeem beheer

### <a name="1384-multi-factor-authentication-is-used-to-authenticate-users-each-time-they-perform-privileged-actions"></a>1384 multi-factor Authentication wordt gebruikt voor het verifiëren van gebruikers telkens wanneer ze geprivilegieerde acties uitvoeren

- MFA moet zijn ingeschakeld voor accounts met eigenaars machtigingen voor uw abonnement
- MFA moet zijn ingeschakeld voor accounts met schrijf machtigingen voor uw abonnement
- MFA moet zijn ingeschakeld voor accounts met lees machtigingen voor uw abonnement

### <a name="1386-management-traffic-is-only-allowed-to-originate-from-network-zones-that-are-used-to-administer-systems-and-applications"></a>1386 beheer verkeer mag alleen afkomstig zijn van netwerk zones die worden gebruikt voor het beheer van systemen en toepassingen

- Just-In-Time-netwerktoegangsbeheer moet worden toegepast op virtuele machines
- Fout opsporing op afstand moet worden uitgeschakeld voor API Apps
- Fout opsporing op afstand moet worden uitgeschakeld voor functie-apps
- Externe fout opsporing moet worden uitgeschakeld voor webtoepassingen

## <a name="guidelines-for-system-management---system-patching"></a>Richt lijnen voor systeem beheer-systeem patches

### <a name="1144-security-vulnerabilities-in-applications-and-drivers-assessed-as-extreme-risk-are-patched-updated-or-mitigated-within-48-hours-of-the-security-vulnerabilities-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1144 beveiligings problemen in toepassingen en stuur Programma's die worden beoordeeld als uitzonderlijk risico, worden met patches opgelost, bijgewerkt of verholpen binnen 48 uur van de beveiligings problemen die worden geïdentificeerd door leveranciers, onafhankelijke derden, systeem beheerders of gebruikers

- Beveiligings problemen voor uw SQL-data bases moeten worden hersteld
- De evaluatie van beveiligings problemen moet worden ingeschakeld op uw SQL-servers
- De evaluatie van beveiligings problemen moet worden ingeschakeld voor uw door SQL beheerde instanties
- \[Voor\]beeld: evaluatie van beveiligings problemen moet zijn ingeschakeld op virtual machines
- Beveiligings problemen in de beveiligings configuratie van de schaal sets van virtuele machines moeten worden hersteld
- Beveiligings problemen moeten worden opgelost met een oplossing voor de evaluatie van de beveiligings lekken
- Beveiligings problemen in de beveiligings configuratie op uw computers moeten worden hersteld
- Beveiligings problemen in container beveiligings configuraties moeten worden hersteld
- De instellingen voor evaluatie van beveiligings problemen voor SQL Server moeten een e-mail adres bevatten voor het ontvangen van scan rapporten

### <a name="0940-security-vulnerabilities-in-applications-and-drivers-assessed-as-high-risk-are-patched-updated-or-mitigated-within-two-weeks-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>0940 beveiligings problemen in toepassingen en stuur Programma's die als hoog risico zijn beoordeeld, worden opgelost, bijgewerkt of verholpen binnen twee weken van het beveiligings probleem dat door leveranciers, onafhankelijke derden, systeem beheerders of gebruikers wordt geïdentificeerd

- Beveiligings problemen voor uw SQL-data bases moeten worden hersteld
- De evaluatie van beveiligings problemen moet worden ingeschakeld op uw SQL-servers
- De evaluatie van beveiligings problemen moet worden ingeschakeld voor uw door SQL beheerde instanties
- \[Voor\]beeld: evaluatie van beveiligings problemen moet zijn ingeschakeld op virtual machines
- Beveiligings problemen in de beveiligings configuratie van de schaal sets van virtuele machines moeten worden hersteld
- Beveiligings problemen moeten worden opgelost met een oplossing voor de evaluatie van de beveiligings lekken
- Beveiligings problemen in de beveiligings configuratie op uw computers moeten worden hersteld
- Beveiligings problemen in container beveiligings configuraties moeten worden hersteld
- De instellingen voor evaluatie van beveiligings problemen voor SQL Server moeten een e-mail adres bevatten voor het ontvangen van scan rapporten

### <a name="1472-security-vulnerabilities-in-applications-and-drivers-assessed-as-moderate-or-low-risk-are-patched-updated-or-mitigated-within-one-month-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1472 beveiligings problemen in toepassingen en stuur Programma's die worden beoordeeld als gemiddeld of laag risico, worden gepatched, bijgewerkt of verholpen binnen één maand van het beveiligings probleem dat door leveranciers, onafhankelijke derden, systeem beheerders of gebruikers wordt geïdentificeerd

- Beveiligings problemen voor uw SQL-data bases moeten worden hersteld
- De evaluatie van beveiligings problemen moet worden ingeschakeld op uw SQL-servers
- De evaluatie van beveiligings problemen moet worden ingeschakeld voor uw door SQL beheerde instanties
- \[Voor\]beeld: evaluatie van beveiligings problemen moet zijn ingeschakeld op virtual machines
- Beveiligings problemen in de beveiligings configuratie van de schaal sets van virtuele machines moeten worden hersteld
- Beveiligings problemen moeten worden opgelost met een oplossing voor de evaluatie van de beveiligings lekken
- Beveiligings problemen in de beveiligings configuratie op uw computers moeten worden hersteld
- Beveiligings problemen in container beveiligings configuraties moeten worden hersteld
- De instellingen voor evaluatie van beveiligings problemen voor SQL Server moeten een e-mail adres bevatten voor het ontvangen van scan rapporten

### <a name="1494-security-vulnerabilities-in-operating-systems-and-firmware-assessed-as-extreme-risk-are-patched-updated-or-mitigated-within-48-hours-of-the-security-vulnerabilities-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1494 beveiligings problemen in besturings systemen en firmware die worden beoordeeld als extreem risico, worden gepatched, bijgewerkt of verholpen binnen 48 uur van de beveiligings problemen die worden geïdentificeerd door leveranciers, onafhankelijke derden, systeem beheerders of gebruikers

- Beveiligings problemen voor uw SQL-data bases moeten worden hersteld
- De evaluatie van beveiligings problemen moet worden ingeschakeld op uw SQL-servers
- De evaluatie van beveiligings problemen moet worden ingeschakeld voor uw door SQL beheerde instanties
- \[Voor\]beeld: evaluatie van beveiligings problemen moet zijn ingeschakeld op virtual machines
- Beveiligings problemen in de beveiligings configuratie van de schaal sets van virtuele machines moeten worden hersteld
- Beveiligings problemen moeten worden opgelost met een oplossing voor de evaluatie van de beveiligings lekken
- Beveiligings problemen in de beveiligings configuratie op uw computers moeten worden hersteld
- Beveiligings problemen in container beveiligings configuraties moeten worden hersteld
- De instellingen voor evaluatie van beveiligings problemen voor SQL Server moeten een e-mail adres bevatten voor het ontvangen van scan rapporten

### <a name="1495-security-vulnerabilities-in-operating-systems-and-firmware-assessed-as-high-risk-are-patched-updated-or-mitigated-within-two-weeks-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1495 beveiligings problemen in besturings systemen en firmware die als hoog risico zijn beoordeeld, worden opgelost, bijgewerkt of verholpen binnen twee weken van het beveiligings probleem dat door leveranciers, onafhankelijke derden, systeem beheerders of gebruikers wordt geïdentificeerd

- Beveiligings problemen voor uw SQL-data bases moeten worden hersteld
- De evaluatie van beveiligings problemen moet worden ingeschakeld op uw SQL-servers
- De evaluatie van beveiligings problemen moet worden ingeschakeld voor uw door SQL beheerde instanties
- \[Voor\]beeld: evaluatie van beveiligings problemen moet zijn ingeschakeld op virtual machines
- Beveiligings problemen in de beveiligings configuratie van de schaal sets van virtuele machines moeten worden hersteld
- Beveiligings problemen moeten worden opgelost met een oplossing voor de evaluatie van de beveiligings lekken
- Beveiligings problemen in de beveiligings configuratie op uw computers moeten worden hersteld
- Beveiligings problemen in container beveiligings configuraties moeten worden hersteld
- De instellingen voor evaluatie van beveiligings problemen voor SQL Server moeten een e-mail adres bevatten voor het ontvangen van scan rapporten

### <a name="1496-security-vulnerabilities-in-operating-systems-and-firmware-assessed-as-moderate-or-low-risk-are-patched-updated-or-mitigated-within-one-month-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1496 beveiligings problemen in besturings systemen en firmware die worden beoordeeld als gemiddeld of laag risico, worden gepatched, bijgewerkt of verholpen binnen één maand van het beveiligings probleem dat door leveranciers, onafhankelijke derden, systeem beheerders of gebruikers wordt geïdentificeerd

- Beveiligings problemen voor uw SQL-data bases moeten worden hersteld
- De evaluatie van beveiligings problemen moet worden ingeschakeld op uw SQL-servers
- De evaluatie van beveiligings problemen moet worden ingeschakeld voor uw door SQL beheerde instanties
- \[Voor\]beeld: evaluatie van beveiligings problemen moet zijn ingeschakeld op virtual machines
- Beveiligings problemen in de beveiligings configuratie van de schaal sets van virtuele machines moeten worden hersteld
- Beveiligings problemen moeten worden opgelost met een oplossing voor de evaluatie van de beveiligings lekken
- Beveiligings problemen in de beveiligings configuratie op uw computers moeten worden hersteld
- Beveiligings problemen in container beveiligings configuraties moeten worden hersteld
- De instellingen voor evaluatie van beveiligings problemen voor SQL Server moeten een e-mail adres bevatten voor het ontvangen van scan rapporten

## <a name="guidelines-for-system-management---data-backup-and-restoration"></a>Richt lijnen voor systeem beheer-gegevens back-up en herstel

### <a name="1511-backups-of-important-information-software-and-configuration-settings-are-performed-at-least-daily"></a>1511 back-ups van belang rijke informatie, software-en configuratie-instellingen worden ten minste dagelijks uitgevoerd

- Virtuele machines controleren zonder nood herstel zijn geconfigureerd

## <a name="guidelines-for-system-monitoring---event-logging-and-auditing"></a>Richt lijnen voor systeem bewaking-logboek registratie en controle

### <a name="1405-a-centralised-logging-facility-is-implemented-and-systems-are-configured-to-save-event-logs-to-the-centralised-logging-facility-as-soon-as-possible-after-each-event-occurs"></a>1405 een gecentraliseerde logboek registratie faciliteit wordt geïmplementeerd en systemen zijn zo geconfigureerd dat gebeurtenis logboeken zo snel mogelijk worden opgeslagen in de gecentraliseerde logboek registratie functie, nadat elke gebeurtenis is opgetreden

- Azure-abonnementen moeten een logboek profiel voor het activiteiten logboek hebben

### <a name="0582-the-following-events-are-logged-for-operating-systems-access-to-important-data-and-processes-application-crashes-and-any-error-messages-attempts-to-use-special-privileges-changes-to-accounts-changes-to-security-policy-changes-to-system-configurations-domain-name-system-dns-and-hypertext-transfer-protocol-http-requests-failed-attempts-to-access-data-and-system-resources-service-failures-and-restarts-system-startup-and-shutdown-transfer-of-data-to-external-media-user-or-group-management-use-of-special-privileges"></a>0582 de volgende gebeurtenissen worden vastgelegd voor besturings systemen: toegang tot belang rijke gegevens en processen, vastlopen van toepassingen en eventuele fout berichten, pogingen tot het gebruiken van speciale bevoegdheden, wijzigingen in accounts, wijzigingen in het beveiligings beleid, wijzigingen in de systeem configuraties, Domain Name System (DNS) en Hypertext Transfer Protocol (HTTP) aanvragen, mislukte pogingen om toegang te krijgen tot gegevens-en systeem bronnen, service fouten , overdracht van gegevens naar externe media, gebruikers-of groeps beheer, gebruik van speciale bevoegdheden

- \[Voor\]beeld: audit log Analytics agent implementatie-VM-installatie kopie (OS) niet vermeld
- \[Voor\]beeld: audit log Analytics agent implementatie in VMSS-VM-installatie kopie (OS) niet vermeld
- \[Voor\]beeld: controle log Analytics werk ruimte voor VM-niet-overeenkomend rapport
- Diagnostische instelling voor controleren

### <a name="1537-the-following-events-are-logged-for-databases-access-to-particularly-important-information-addition-of-new-users-especially-privileged-users-any-query-containing-comments-any-query-containing-multiple-embedded-queries-any-query-or-database-alerts-or-failures-attempts-to-elevate-privileges-attempted-access-that-is-successful-or-unsuccessful-changes-to-the-database-structure-changes-to-user-roles-or-database-permissions-database-administrator-actions-database-logons-and-logoffs-modifications-to-data-use-of-executable-commands"></a>1537 de volgende gebeurtenissen worden vastgelegd voor data bases: toegang tot bijzonder belang rijke informatie, toevoeging van nieuwe gebruikers, met name bevoegde gebruikers, alle query's met opmerkingen, alle query's met meerdere Inge sloten query's, eventuele query's of database waarschuwingen of fouten, pogingen tot verhoging van bevoegdheden, poging tot het uitvoeren van een geslaagde of mislukte wijzigingen in de structuur van de data base, wijzigingen in gebruikers rollen , database aanmeldingen en afmeldingen, wijzigingen aan gegevens, gebruik van uitvoer bare opdrachten

- Geavanceerde gegevens beveiliging moet zijn ingeschakeld op uw SQL-servers
- Diagnostische instelling voor controleren
- Geavanceerde gegevens beveiliging moet zijn ingeschakeld voor uw door SQL beheerde instanties

## <a name="guidelines-for-system-monitoring---vulnerability-management"></a>Richt lijnen voor systeem bewaking-beveiligings beheer

### <a name="0911-vulnerability-assessments-and-penetration-tests-are-conducted-by-suitably-skilled-personnel-before-a-system-is-deployed-after-a-significant-change-to-a-system-and-at-least-annually-or-as-specified-by-the-system-owner"></a>0911-beveiligings lekken en indringings tests worden uitgevoerd door voldoende bekwaam personeel voordat een systeem wordt geïmplementeerd, na een belang rijke wijziging in een systeem, en ten minste jaarlijks of zoals opgegeven door de eigenaar van het systeem

- Beveiligings problemen voor uw SQL-data bases moeten worden hersteld
- De evaluatie van beveiligings problemen moet worden ingeschakeld op uw SQL-servers
- De evaluatie van beveiligings problemen moet worden ingeschakeld voor uw door SQL beheerde instanties
- \[Voor\]beeld: evaluatie van beveiligings problemen moet zijn ingeschakeld op virtual machines
- Beveiligings problemen in de beveiligings configuratie van de schaal sets van virtuele machines moeten worden hersteld
- Beveiligings problemen moeten worden opgelost met een oplossing voor de evaluatie van de beveiligings lekken
- Beveiligings problemen in de beveiligings configuratie op uw computers moeten worden hersteld
- Beveiligings problemen in container beveiligings configuraties moeten worden hersteld

## <a name="guidelines-for-database-systems-management---database-servers"></a>Richt lijnen voor database systeem beheer-database servers

### <a name="1425-hard-disks-of-database-servers-are-encrypted-using-full-disk-encryption"></a>1425 harde schijven van database servers zijn versleuteld met behulp van een volledige schijf versleuteling

- Schijf versleuteling moet worden toegepast op virtuele machines
- Transparent Data Encryption voor SQL-data bases moet zijn ingeschakeld

### <a name="1277-information-communicated-between-database-servers-and-web-applications-is-encrypted"></a>1277 gegevens die worden gecommuniceerd tussen database servers en webtoepassingen, worden versleuteld

- Alleen beveiligde verbindingen met uw Redis Cache moeten worden ingeschakeld
- Beveiligde overdracht naar opslagaccounts moet zijn ingeschakeld
- Controle resultaten weer geven van Windows-webservers die geen beveiligde communicatie protocollen gebruiken
- Vereisten implementeren voor het controleren van Windows-webservers die geen beveiligde communicatie protocollen gebruiken

## <a name="guidelines-for-database-systems-management---database-management-system-software"></a>Richt lijnen voor database systeem beheer-software voor database beheer systeem

### <a name="1260-default-database-administrator-accounts-are-disabled-renamed-or-have-their-passphrases-changed"></a>1260 standaard beheerders accounts voor de Data Base zijn uitgeschakeld, de naam van de groep is gewijzigd of de wachtwoord zinnen is veranderd

- Een Azure Active Directory beheerder moet worden ingericht voor SQL-servers

### <a name="1262-database-administrators-have-unique-and-identifiable-accounts"></a>1262 database beheerders hebben unieke en Identificeer bare accounts

- Een Azure Active Directory beheerder moet worden ingericht voor SQL-servers

### <a name="1261-database-administrator-accounts-are-not-shared-across-different-databases"></a>1261-database beheerders accounts worden niet gedeeld in verschillende data bases

- Een Azure Active Directory beheerder moet worden ingericht voor SQL-servers

### <a name="1263-database-administrator-accounts-are-used-exclusively-for-administrative-tasks-with-standard-database-accounts-used-for-general-purpose-interactions-with-database"></a>1263 database beheerders accounts worden uitsluitend gebruikt voor beheer taken, met standaard database accounts die worden gebruikt voor interacties met algemene doel einden met data base

- Een Azure Active Directory beheerder moet worden ingericht voor SQL-servers

### <a name="1264-database-administrator-access-is-restricted-to-defined-roles-rather-than-accounts-with-default-administrative-permissions-or-all-permissions"></a>1264 database beheerders toegang is beperkt tot gedefinieerde rollen in plaats van accounts met standaard beheerders machtigingen of alle machtigingen

- Een Azure Active Directory beheerder moet worden ingericht voor SQL-servers

## <a name="guidelines-for-using-cryptography---cryptographic-fundamentals"></a>Richt lijnen voor het gebruik van crypto grafie-cryptografische basis bewerkingen

### <a name="0459-encryption-software-used-for-data-at-rest-implements-full-disk-encryption-or-partial-encryption-where-access-controls-will-only-allow-writing-to-the-encrypted-partition"></a>0459-versleutelings software die wordt gebruikt voor gegevens in rust, implementeert de volledige schijf versleuteling of gedeeltelijke versleuteling waarbij toegangs beheer alleen schrijf bewerkingen naar de versleutelde partitie toestaat

- Schijf versleuteling moet worden toegepast op virtuele machines

## <a name="guidelines-for-using-cryptography---transport-layer-security"></a>Richt lijnen voor het gebruik van crypto grafie-Transport Layer Security

### <a name="1139-only-the-latest-version-of-tls-is-used"></a>1139 alleen de meest recente versie van TLS wordt gebruikt

- De laatste TLS-versie moet worden gebruikt in uw API-app
- De laatste TLS-versie moet worden gebruikt in uw web-app
- De meest recente versie van TLS moet worden gebruikt in uw functie-app
- Vereisten implementeren voor het controleren van Windows-webservers die geen beveiligde communicatie protocollen gebruiken
- Controle resultaten weer geven van Windows-webservers die geen beveiligde communicatie protocollen gebruiken

## <a name="guidelines-for-data-transfers-and-content-filtering---content-filtering"></a>Richt lijnen voor gegevens overdrachten en het filteren van inhoud-inhouds filtering

### <a name="1288-antivirus-scanning-using-multiple-different-scanning-engines-is-performed-on-all-content"></a>1288 virus scan, met behulp van meerdere verschillende scan engines, wordt uitgevoerd voor alle inhoud

- Micro soft IaaSAntimalwaree extension moet worden geïmplementeerd op Windows-servers
- Endpoint Protection-oplossing moet worden geïnstalleerd op virtuele-machine schaal sets
- Ontbrekende Endpoint Protection in Azure Security Center controleren

## <a name="guidelines-for-data-transfers-and-content-filtering---web-application-development"></a>Richt lijnen voor gegevens overdrachten en het filteren van inhoud-webtoepassingen ontwikkelen

### <a name="1552-all-web-application-content-is-offered-exclusively-using-https"></a>1552 alle inhoud van de webtoepassing wordt exclusief aangeboden via HTTPS

- functie-app mag alleen toegankelijk zijn via HTTPS
- De API-app mag alleen toegankelijk zijn via HTTPS
- Webtoepassing mag alleen toegankelijk zijn via HTTPS
- Alleen beveiligde verbindingen met uw Redis Cache moeten worden ingeschakeld

### <a name="1424-web-browser-based-security-controls-are-implemented-for-web-applications-in-order-to-help-protect-both-web-applications-and-their-users"></a>1424 webtoepassingen die zijn gebaseerd op de webbrowser, worden geïmplementeerd op basis van beveiligings besturings elementen, zodat beide webtoepassingen en hun gebruikers kunnen worden beveiligd

- CORS mag niet alle bronnen toestaan om toegang te krijgen tot uw webtoepassingen

## <a name="guidelines-for-network-management---network-design-and-configuration"></a>Richt lijnen voor netwerk beheer-netwerk ontwerp en-configuratie

### <a name="0520-network-access-controls-are-implemented-on-networks-to-prevent-the-connection-of-unauthorised-network-devices"></a>0520 netwerk toegangs beheer wordt geïmplementeerd op netwerken om te voor komen dat er niet-geautoriseerde netwerk apparaten worden verbonden

- Onbeperkte netwerk toegang tot opslag accounts controleren

### <a name="1182-network-access-controls-are-implemented-to-limit-traffic-within-and-between-network-segments-to-only-those-that-are-required-for-business-purposes"></a>1182-netwerk toegangs beheer zijn geïmplementeerd om het verkeer binnen en tussen netwerk segmenten te beperken tot alleen de apparaten die vereist zijn voor zakelijke doel einden

- Internet gerichte virtuele machines moeten worden beveiligd met netwerk beveiligings groepen
- Onbeperkte netwerk toegang tot opslag accounts controleren
- Aanbevelingen voor adaptieve netwerk beveiliging moeten worden toegepast op Internet gerichte virtuele machines

## <a name="guidelines-for-network-management---service-continuity-for-online-services"></a>Richt lijnen voor Network Management-service continuïteit voor onlineservices

### <a name="1431-denial-of-service-attack-prevention-and-mitigation-strategies-are-discussed-with-service-providers-specifically-their-capacity-to-withstand-denial-of-service-attacks-any-costs-likely-to-be-incurred-by-customers-resulting-from-denial-of-service-attacks-thresholds-for-notifying-customers-or-turning-off-their-online-services-during-denial-of-service-attacks-pre-approved-actions-that-can-be-undertaken-during-denial-of-service-attacks-denial-of-service-attack-prevention-arrangements-with-upstream-providers-to-block-malicious-traffic-as-far-upstream-as-possible"></a>1431 denial-of-service-aanvallen en risico beperkings strategieën worden besproken bij service providers, in het bijzonder: hun capaciteit om denial-of-service-aanvallen te voor komen. eventuele kosten die door klanten worden veroorzaakt door een denial-of-service-aanval, drempel waarden voor het melden van klanten of het uitschakelen van hun onlineservices tijdens denial-of-service-aanvallen, vooraf goedgekeurde acties die kunnen worden uitgevoerd tijdens denial-of-service-aanvallen, denial-of-service-aanvals preventie met upstream-providers om schadelijk verkeer zo veel mogelijk te blok keren

- DDoS Protection standaard moet zijn ingeschakeld


> [!NOTE]
> De beschik baarheid van specifieke Azure Policy definities kan verschillen in Azure Government en andere nationale Clouds. 

## <a name="next-steps"></a>Volgende stappen

Aanvullende artikelen over blauwdrukken en het gebruik hiervan:

- Meer informatie over de [levenscyclus van een blauwdruk](../../concepts/lifecycle.md).
- Meer informatie over hoe u [statische en dynamische parameters](../../concepts/parameters.md) gebruikt.
- Meer informatie over hoe u de [blauwdrukvolgorde](../../concepts/sequencing-order.md) aanpast.
- Meer informatie over hoe u gebruikmaakt van [resourcevergrendeling in blauwdrukken](../../concepts/resource-locking.md).
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../../how-to/update-existing-assignments.md).