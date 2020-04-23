---
title: Besturingselementen voor voorbeeld van DoD Impact Level 4-blauwdruk
description: Beheer het toewijzen van het blauwdrukvoorbeeld van DoD Impact Level 4. Elk besturingselement wordt toegewezen aan een of meer Azure-beleidsregels die helpen bij de beoordeling.
ms.date: 03/06/2020
ms.topic: sample
ms.openlocfilehash: a11a8637618bb42831ccf1a7001e4da40529ba74
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024826"
---
# <a name="control-mapping-of-the-dod-impact-level-4-blueprint-sample"></a>Beheer van het blueprintvoorbeeld van DoD Impact Level 4

In het volgende artikel wordt beschreven hoe het blueprintsample van het Azure BlueprintS Department of Defense Impact Level 4 (DoD IL4) wordt toegewezen aan de besturingselementen voor DoD Impact Level 4. Zie [DoD Cloud Computing Security Requirements Guide (SRG) voor](https://dl.dod.cyber.mil/wp-content/uploads/cloud/pdf/Cloud_Computing_SRG_v1r3.pdf)meer informatie over de besturingselementen. Het Defense Information Systems Agency (DISA) is een agentschap van het Amerikaanse Ministerie van Defensie (DoD) dat verantwoordelijk is voor het ontwikkelen en onderhouden van de DoD Cloud Computing Security Requirements Guide (SRG). De SRG definieert de basisbeveiligingsvereisten voor cloudserviceproviders (CSP's) die DoD-informatie, -systemen en -toepassingen hosten en voor het gebruik van cloudservices door DoD.  

De volgende toewijzingen zijn naar de **Besturingselementen voor DoD Impact Level 4.** Gebruik de navigatie aan de rechterkant om direct naar een specifieke besturingstoewijzing te springen. Veel van de toegewezen besturingselementen worden geïmplementeerd met een [Azure Policy-initiatief.](../../../policy/overview.md) Als u het volledige initiatief wilt bekijken, opent u **Beleid** in de Azure-portal en selecteert u de pagina **Definities.** Zoek en selecteer ** \[vervolgens\]** het ingebouwde beleidsinitiatief Voorbeeld: DoD Impact Level 4.

> [!IMPORTANT]
> Elk besturingselement hieronder is gekoppeld aan een of meer [Azure Policy-definities.](../../../policy/overview.md) Met dit beleid u [beoordelen of de](../../../policy/how-to/get-compliance-data.md) controle is nageleefd; Er is echter vaak geen 1:1 of volledige overeenkomst tussen een besturingselement en een of meer beleidsregels. **Compliant** in Azure Policy verwijst daarom alleen naar het beleid zelf. Dit zorgt er niet voor dat u volledig voldoet aan alle vereisten van een controle. Bovendien bevat de nalevingsstandaard besturingselementen die op dit moment niet worden behandeld door azure-beleidsdefinities. Naleving in Azure Policy is daarom slechts een gedeeltelijke weergave van uw algemene nalevingsstatus. De koppelingen tussen besturingselementen en Azure Policy-definities voor dit voorbeeld van nalevingsblauwdrukken kunnen in de loop van de tijd veranderen.
> Zie de [GitHub Commit History](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/dod-impact-level-4/control-mapping.md)om de wijzigingsgeschiedenis te bekijken.

## <a name="ac-2-account-management"></a>AC-2-accountbeheer

Met deze blauwdruk u accounts controleren die mogelijk niet voldoen aan de vereisten voor accountbeheer van uw organisatie. In deze blauwdruk worden [Azure Policy-definities](../../../policy/overview.md) toegekend die externe accounts controleren met lees-, schrijf- en eigenaarmachtigingen voor een abonnement en afgeschafte accounts. Door de accounts te controleren die door dit beleid worden gecontroleerd, u passende maatregelen nemen om ervoor te zorgen dat aan de vereisten voor accountbeheer wordt voldaan.

- Afgeschafte accounts moeten uit uw abonnement worden verwijderd
- Afgeschafte accounts met eigenaarmachtigingen moeten uit uw abonnement worden verwijderd
- Externe accounts met eigenaarmachtigingen moeten uit uw abonnement worden verwijderd
- Externe accounts met leesmachtigingen moeten uit uw abonnement worden verwijderd
- Externe accounts met schrijfmachtigingen moeten uit uw abonnement worden verwijderd

## <a name="ac-2-7-account-management--role-based-schemes"></a>AC-2 (7) Accountbeheer | Op rollen gebaseerde regelingen

Azure implementeert [op rollen gebaseerd toegangscontrolebeheer](../../../../role-based-access-control/overview.md) (RBAC) om u te helpen beheren wie toegang heeft tot bronnen in Azure. Met behulp van de Azure-portal u controleren wie toegang heeft tot Azure-bronnen en hun machtigingen. In deze blauwdruk worden ook [Azure Policy-definities](../../../policy/overview.md) toebedeeld om het gebruik van Azure Active Directory-verificatie voor SQL Servers en Service Fabric te controleren. Met Azure Active Directory-verificatie u vereenvoudigd machtigingsbeheer en gecentraliseerd identiteitsbeheer van databasegebruikers en andere Microsoft-services gebruiken. Bovendien wordt in deze blauwdruk een Azure-beleidsdefinitie toegegeven om het gebruik van aangepaste RBAC-regels te controleren. Als u begrijpt waar aangepaste RBAC-regels worden geïmplementeerd, u de noodzaak en de juiste implementatie controleren, omdat aangepaste RBAC-regels foutgevoelig zijn.

- Een Azure Active Directory-beheerder moet zijn ingericht voor SQL-servers
- Controle gebruik van aangepaste RBAC-regels
- Servicesfabricclusters mogen alleen Azure Active Directory gebruiken voor clientverificatie

## <a name="ac-2-12-account-management--account-monitoring--atypical-usage"></a>AC-2 (12) Accountbeheer | Accountmonitoring / Atypisch gebruik

Just-in-time (JIT) toegang tot virtuele machines (JIT) vergrendelt binnenkomend verkeer naar virtuele Azure-machines, waardoor de blootstelling aan aanvallen wordt verminderd en u gemakkelijk verbinding maken met VM's wanneer dat nodig is. Alle JIT-verzoeken om toegang te krijgen tot virtuele machines worden geregistreerd in het activiteitenlogboek, zodat u controleren op atypisch gebruik. In deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) opgenomen waarmee u virtuele machines controleren die just-in-time-toegang kunnen ondersteunen, maar nog niet zijn geconfigureerd.

- Just-In-Time-netwerktoegangsbeheer moet worden toegepast op virtuele machines

## <a name="ac-4-information-flow-enforcement"></a>AC-4 Information Flow Enforcement

Met het delen van resources (Cross origin resource sharing) kunnen app-servicesbronnen worden aangevraagd bij een extern domein. Microsoft raadt u aan alleen vereiste domeinen toe te staan om te communiceren met uw API-, functie- en webtoepassingen. In deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) toegegeven waarmee u cors-bronnen toegang tot beperkingen controleren in Azure Security Center. Als u CORS-implementaties begrijpt, u controleren of de controle van de informatiestroom is geïmplementeerd.

- CORS mag niet toestaan dat elke bron toegang krijgt tot uw webtoepassing

## <a name="ac-5-separation-of-duties"></a>AC-5 Scheiding van rechten

Als u slechts één Azure-abonnementseigenaar hebt, is er geen beheerredundantie mogelijk. Omgekeerd kan het hebben van te veel Azure-abonnementseigenaren het potentieel voor een inbreuk vergroten via een gecompromitteerd eigenaaraccount. Met deze blauwdruk u een passend aantal Azure-abonnementseigenaren behouden door [Azure-beleidsdefinities](../../../policy/overview.md) toe te wijsen die het aantal eigenaren voor Azure-abonnementen controleren. In deze blauwdruk worden ook Azure Policy-definities toekent waarmee u het lidmaatschap van de groep Administrators op virtuele Windows-machines beheren. Als u de eigenaar van een abonnement beheert en beheerdersmachtigingen voor virtuele machines beheren, u de juiste scheiding van taken implementeren.

- Er moeten maximaal 3 eigenaren worden aangewezen voor uw abonnement
- Windows VM's controleren waarin de groep Administrators een van de opgegeven leden bevat
- Windows VM's controleren waarin de groep Administrators niet alle opgegeven leden bevat
- Vereisten implementeren om Windows VM's te controleren waarin de groep Administrators een van de opgegeven leden bevat
- Vereisten implementeren om Windows VM's te controleren waarin de groep Administrators niet alle opgegeven leden bevat
- Er moeten meer dan één eigenaar aan uw abonnement zijn toegewezen

## <a name="ac-6-7-least-privilege--review-of-user-privileges"></a>AC-6 (7) Minste voorrecht | Beoordeling van gebruikersbevoegdheden

Azure implementeert [op rollen gebaseerd toegangscontrolebeheer](../../../../role-based-access-control/overview.md) (RBAC) om u te helpen beheren wie toegang heeft tot bronnen in Azure. Met behulp van de Azure-portal u controleren wie toegang heeft tot Azure-bronnen en hun machtigingen. In deze blauwdruk worden [Azure Policy-definities](../../../policy/overview.md) toegestoond voor het controleren van accounts die prioriteit moeten krijgen voor controle. Als u deze accountindicatoren bekijkt, u ervoor zorgen dat de minste bevoegdheden worden geïmplementeerd.

- Er moeten maximaal 3 eigenaren worden aangewezen voor uw abonnement
- Windows VM's controleren waarin de groep Administrators een van de opgegeven leden bevat
- Windows VM's controleren waarin de groep Administrators niet alle opgegeven leden bevat
- Vereisten implementeren om Windows VM's te controleren waarin de groep Administrators een van de opgegeven leden bevat
- Vereisten implementeren om Windows VM's te controleren waarin de groep Administrators niet alle opgegeven leden bevat
- Er moeten meer dan één eigenaar aan uw abonnement zijn toegewezen

## <a name="ac-17-1-remote-access--automated-monitoring--control"></a>AC-17 (1) Toegang op afstand | Geautomatiseerde controle / controle

Met deze blauwdruk u externe toegang controleren en beheren door [Azure Policy-definities](../../../policy/overview.md) toe te stellen om te controleren of het op afstand opsporen van de azure-app-servicetoepassing is uitgeschakeld en beleidsdefinities die linux-virtuele machines controleren die externe verbindingen van accounts zonder wachtwoorden toestaan. In deze blauwdruk wordt ook een Azure-beleidsdefinitie opgenomen waarmee u onbeperkte toegang tot opslagaccounts controleren. Het bewaken van deze indicatoren kan u helpen ervoor te zorgen dat methoden voor externe toegang voldoen aan uw beveiligingsbeleid.

- \[Preview:\]Linux VM's controleren die externe verbindingen van accounts zonder wachtwoorden mogelijk maken
- \[Preview:\]Implementatievereisten voor het controleren van Linux VM's die externe verbindingen van accounts zonder wachtwoorden mogelijk maken
- Onbeperkte netwerktoegang tot opslagaccounts controleren
- Externe foutopsporing moet worden uitgeschakeld voor API-app
- Externe foutopsporing moet worden uitgeschakeld voor functie-app
- Externe foutopsporing moet worden uitgeschakeld voor webtoepassing

## <a name="ac-23-data-mining"></a>AC-23 Data Mining

Deze blauwdruk bevat beleidsdefinities die u helpen ervoor te zorgen dat meldingen over gegevensbeveiliging correct zijn ingeschakeld. Bovendien zorgt deze blauwdruk ervoor dat auditing en geavanceerde gegevensbeveiliging zijn geconfigureerd op SQL Servers.

- Geavanceerde gegevensbeveiliging moet worden ingeschakeld op uw SQL-servers
- Geavanceerde gegevensbeveiliging moet worden ingeschakeld op uw SQL-beheerde exemplaren
- Geavanceerde typen bedreigingsbeveiliging moeten worden ingesteld op 'Alles' in sql-server geavanceerde gegevensbeveiligingsinstellingen
- Geavanceerde typen bedreigingsbeveiliging moeten worden ingesteld op 'Alles' in SQL-beheerde instantie Advanced Data Security-instellingen
- Controle moet worden ingeschakeld op geavanceerde instellingen voor gegevensbeveiliging op SQL Server
- E-mailmeldingen aan beheerders en eigenaren van abonnementen moeten worden ingeschakeld in geavanceerde gegevensbeveiligingsinstellingen van SQL-server
- E-mailmeldingen aan beheerders en eigenaren van abonnementen moeten worden ingeschakeld in sql-beheerde instellingen voor geavanceerde gegevensbeveiliging
- Geavanceerde instellingen voor gegevensbeveiliging voor SQL-server moeten een e-mailadres bevatten om beveiligingswaarschuwingen te ontvangen
- Geavanceerde instellingen voor gegevensbeveiliging voor SQL-beheerde instantie moeten een e-mailadres bevatten om beveiligingswaarschuwingen te ontvangen

## <a name="au-3-2-content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>AU-3 (2) Inhoud van de controledossiers | Gecentraliseerd beheer van de inhoud van geplande auditrecord

Loggegevens die door Azure Monitor worden verzameld, worden opgeslagen in een Log Analytics-werkruimte, zodat gecentraliseerde configuratie en beheer mogelijk zijn. Met deze blauwdruk u ervoor zorgen dat gebeurtenissen worden geregistreerd door [Azure Policy-definities](../../../policy/overview.md) toe te wijsen die de implementatie van de loganalytics-agent op virtuele Azure-machines controleren en afdwingen.

- \[Preview:\]Audit Log Analytics Agent Deployment - VM Image (OS) niet vermeld
- \[Preview:\]Implementatie van auditloganalyseagent in VMSS - VM Image (OS) niet vermeld
- \[Preview\]: Audit Log Analytics Workspace for VM - Report Mismatch
- \[Preview\]: Log Analytics Agent voor Linux VM-schaalsets (VMSS) implementeren
- \[Preview:\]Log Analytics Agent implementeren voor Linux VM's
- \[Voorbeeld:\]Logboekanalyseagent voor Windows VM-schaalsets (VMSS) implementeren
- \[Voorbeeld:\]Logboekanalyseagent implementeren voor Windows VM's

## <a name="au-5-response-to-audit-processing-failures"></a>AU-5-reactie op fouten in controleverwerking

In deze blauwdruk worden [Azure Policy-definities](../../../policy/overview.md) toekent die de configuraties voor het registreren van audit en gebeurtenissen bewaken. Het bewaken van deze configuraties kan een indicator zijn van een fout van het auditsysteem of een verkeerde configuratie en u helpen corrigerende maatregelen te nemen.

- Diagnostische instelling voor controleren
- Controle moet worden ingeschakeld op geavanceerde instellingen voor gegevensbeveiliging op SQL Server
- Geavanceerde gegevensbeveiliging moet worden ingeschakeld op uw beheerde instanties
- Geavanceerde gegevensbeveiliging moet worden ingeschakeld op uw SQL-servers

## <a name="au-6-4-audit-review-analysis-and-reporting--central-review-and-analysis"></a>AU-6 (4) Auditreview, analyse en rapportage | Centrale evaluatie en analyse

Loggegevens die door Azure Monitor worden verzameld, worden opgeslagen in een log analytics-werkruimte, zodat gecentraliseerde rapportage en analyse mogelijk is. Met deze blauwdruk u ervoor zorgen dat gebeurtenissen worden geregistreerd door [Azure Policy-definities](../../../policy/overview.md) toe te wijsen die de implementatie van de loganalytics-agent op virtuele Azure-machines controleren en afdwingen.

- \[Preview:\]Audit Log Analytics Agent Deployment - VM Image (OS) niet vermeld
- \[Preview:\]Implementatie van auditloganalyseagent in VMSS - VM Image (OS) niet vermeld
- \[Preview\]: Audit Log Analytics Workspace for VM - Report Mismatch
- \[Preview\]: Log Analytics Agent voor Linux VM-schaalsets (VMSS) implementeren
- \[Preview:\]Log Analytics Agent implementeren voor Linux VM's
- \[Voorbeeld:\]Logboekanalyseagent voor Windows VM-schaalsets (VMSS) implementeren
- \[Voorbeeld:\]Logboekanalyseagent implementeren voor Windows VM's

## <a name="au-6-5-audit-review-analysis-and-reporting--integration--scanning-and-monitoring-capabilities"></a>AU-6 (5) Auditreview, analyse en rapportage | Integratie / scan- en bewakingsmogelijkheden

Deze blauwdruk biedt beleidsdefinities die records controleren met analyse van kwetsbaarheidsbeoordeling op virtuele machines, virtuele machineschaalsets, SQL-beheerde exemplaren en SQL-servers.
Deze beleidsdefinities controleren ook de configuratie van diagnostische logboeken om inzicht te geven in bewerkingen die worden uitgevoerd binnen Azure-resources. Deze inzichten bieden realtime informatie over de beveiligingsstatus van uw geïmplementeerde resources en kunnen u helpen bij het prioriteren van herstelacties.
Voor gedetailleerde het scannen en monitoren van kwetsbaarheden raden we u aan ook Azure Sentinel en Azure Security Center te gebruiken.

- \[Preview\]: Kwetsbaarheidsbeoordeling moet worden ingeschakeld op virtuele machines
- Kwetsbaarheidsbeoordeling moet worden ingeschakeld op uw SQL-servers
- Diagnostische instelling voor controleren
- Kwetsbaarheidsbeoordeling moet worden ingeschakeld op uw SQL-beheerde exemplaren
- Kwetsbaarheidsbeoordeling moet worden ingeschakeld op uw SQL-servers
- Kwetsbaarheden in beveiligingsconfiguratie op uw machines moeten worden verholpen
- Kwetsbaarheden in uw SQL-databases moeten worden verholpen
- Kwetsbaarheden moeten worden verholpen door een oplossing voor kwetsbaarheidsbeoordeling
- Kwetsbaarheden in beveiligingsconfiguratie op uw virtuele machineschaalsets moeten worden verholpen
- \[Preview:\]Audit Log Analytics Agent Deployment - VM Image (OS) niet vermeld
- \[Preview:\]Implementatie van auditloganalyseagent in VMSS - VM Image (OS) niet vermeld

## <a name="au-12-audit-generation"></a>AU-12 Audit Generatie

Deze blauwdruk bevat beleidsdefinities die de implementatie van de log-analyse-agent op virtuele Azure-machines controleren en afdwingen en de configuratie van controle-instellingen voor andere Azure-brontypen.
Deze beleidsdefinities controleren ook de configuratie van diagnostische logboeken om inzicht te geven in bewerkingen die worden uitgevoerd binnen Azure-resources. Daarnaast zijn auditing en Advanced Data Security geconfigureerd op SQL-servers.

- \[Preview:\]Audit Log Analytics Agent Deployment - VM Image (OS) niet vermeld
- \[Preview:\]Implementatie van auditloganalyseagent in VMSS - VM Image (OS) niet vermeld
- \[Preview\]: Audit Log Analytics Workspace for VM - Report Mismatch
- \[Preview\]: Log Analytics Agent voor Linux VM-schaalsets (VMSS) implementeren
- \[Preview:\]Log Analytics Agent implementeren voor Linux VM's
- \[Voorbeeld:\]Logboekanalyseagent voor Windows VM-schaalsets (VMSS) implementeren
- \[Voorbeeld:\]Logboekanalyseagent implementeren voor Windows VM's
- Diagnostische instelling voor controleren
- Controle moet worden ingeschakeld op geavanceerde instellingen voor gegevensbeveiliging op SQL Server
- Geavanceerde gegevensbeveiliging moet worden ingeschakeld op uw beheerde instanties
- Geavanceerde gegevensbeveiliging moet worden ingeschakeld op uw SQL-servers
- Geavanceerde gegevensbeveiliging implementeren op SQL-servers
- Auditing implementeren op SQL-servers
- Diagnostische instellingen voor netwerkbeveiligingsgroepen implementeren

## <a name="au-12-01-audit-generation--system-wide--time-correlated-audit-trail"></a>AU-12 (01) Auditgeneratie | System-Wide / Time-Correlated Audit Trail

Met deze blauwdruk u ervoor zorgen dat systeemgebeurtenissen worden geregistreerd door [Azure Policy-definities](../../../policy/overview.md) toe te wijsen die de instellingen van het logboek controleren op Azure-bronnen.
Dit ingebouwde beleid vereist dat u een array met resourcetypen opgeeft om te controleren of diagnostische instellingen zijn ingeschakeld of niet.

- Diagnostische instelling voor controleren

## <a name="cm-7-2-least-functionality--prevent-program-execution"></a>CM-7 (2) Minste functionaliteit | Programma-uitvoering voorkomen

Adaptief toepassingsbeheer in Azure Security Center is een intelligente, geautomatiseerde end-to-end oplossing voor het whitelisten van toepassingen die specifieke software op uw virtuele machines kan blokkeren of voorkomen. Toepassingsbeheer kan worden uitgevoerd in een handhavingsmodus die het uitvoeren van niet-goedgekeurde toepassingen verbiedt. In deze blauwdruk wordt een Azure-beleidsdefinitie toegekend waarmee u virtuele machines controleren waar een whitelist van een toepassing wordt aanbevolen, maar nog niet is geconfigureerd.

- Adaptieve toepassingsbesturingselementen moeten worden ingeschakeld op virtuele machines

## <a name="cm-7-5-least-functionality--authorized-software--whitelisting"></a>CM-7 (5) Minste functionaliteit | Geautoriseerde software / whitelisting

Adaptief toepassingsbeheer in Azure Security Center is een intelligente, geautomatiseerde end-to-end oplossing voor het whitelisten van toepassingen die specifieke software op uw virtuele machines kan blokkeren of voorkomen. Toepassingsbesturingselement helpt u bij het maken van goedgekeurde toepassingslijsten voor uw virtuele machines. In deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) toegekend waarmee u virtuele machines controleren waar een whitelist van een toepassing wordt aanbevolen, maar nog niet is geconfigureerd.

- Adaptieve toepassingsbesturingselementen moeten worden ingeschakeld op virtuele machines

## <a name="cm-11-user-installed-software"></a>CM-11 Door de gebruiker geïnstalleerde software

Adaptief toepassingsbeheer in Azure Security Center is een intelligente, geautomatiseerde end-to-end oplossing voor het whitelisten van toepassingen die specifieke software op uw virtuele machines kan blokkeren of voorkomen. Toepassingsbeheer kan u helpen de naleving van het beleid voor softwarebeperking af te dwingen en te controleren. In deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) toegekend waarmee u virtuele machines controleren waar een whitelist van een toepassing wordt aanbevolen, maar nog niet is geconfigureerd.

- Adaptieve toepassingsbesturingselementen moeten worden ingeschakeld op virtuele machines

## <a name="cp-7-alternate-processing-site"></a>CP-7 alternatieve verwerkingssite

Azure Site Recovery repliceert workloads die op virtuele machines worden uitgevoerd van een primaire locatie naar een secundaire locatie. Als er een storing optreedt op de primaire locatie, mislukt de werkbelasting boven de secundaire locatie. In deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) toegegeven die virtuele machines controleert zonder dat herstel zonder ramp is geconfigureerd. Het bewaken van deze indicator kan u helpen ervoor te zorgen dat er noodzakelijke onvoorziene controles zijn.

- Virtuele machines controleren zonder herstel van noodgevallen geconfigureerd

## <a name="cp-9-05--information-system-backup--transfer-to-alternate-storage-site"></a>CP-9 (05) Back-up van het informatiesysteem | Overzetten naar alternatieve opslagsite

In deze blauwdruk worden Azure Policy-definities toegewisseld die de systeemback-upgegevens van de organisatie elektronisch controleren op de alternatieve opslagsite. Overweeg voor fysieke verzending van opslagmetagegevens Azure Data Box.

- Georedundante opslag moet zijn ingeschakeld voor opslagaccounts
- Georedundante back-up moet worden ingeschakeld voor Azure Database voor PostgreSQL
- Georedundante back-up moet zijn ingeschakeld voor Azure Database voor MySQL
- Georedundante back-up moet worden ingeschakeld voor Azure Database voor MariaDB
- Georedundant-back-up op lange termijn moet worden ingeschakeld voor Azure SQL-databases

## <a name="ia-2-1-identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>IA-2 (1) Identificatie en verificatie (organisatorische gebruikers) | Netwerktoegang tot geprivilegieerde accounts

Met deze blauwdruk u de toegang tot bevoegdheden beperken en beheren door [Azure Policy-definities](../../../policy/overview.md) toe te wijs voor het controleren van accounts met eigenaar- en/of schrijfmachtigingen waarbij multi-factorverificatie niet is ingeschakeld. Meervoudige verificatie helpt accounts veilig te houden, zelfs als één stuk verificatiegegevens wordt gecompromitteerd. Door accounts te controleren zonder dat multi-factor authenticatie is ingeschakeld, u accounts identificeren die waarschijnlijk meer kans hebben op gecompromitteerd.

- MFA moet zijn ingeschakeld voor accounts met eigenaarmachtigingen voor uw abonnement
- MFA moet zijn ingeschakeld voor accounts met schrijfmachtigingen voor uw abonnement

## <a name="ia-2-2-identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>IA-2 (2) Identificatie en verificatie (organisatorische gebruikers) | Netwerktoegang tot niet-bevoorrechte accounts

Met deze blauwdruk u de toegang beperken en beheren door een [Azure Policy-definitie](../../../policy/overview.md) toe te stellen voor het controleren van accounts met leesmachtigingen die geen multi-factor authenticatie hebben ingeschakeld. Meervoudige verificatie helpt accounts veilig te houden, zelfs als één stuk verificatiegegevens wordt gecompromitteerd. Door accounts te controleren zonder dat multi-factor authenticatie is ingeschakeld, u accounts identificeren die waarschijnlijk meer kans hebben op gecompromitteerd.

- MFA moet zijn ingeschakeld voor accounts met leesmachtigingen voor uw abonnement

## <a name="ia-5-authenticator-management"></a>IA-5 Authenticator Management

In deze blauwdruk worden [Azure Policy-definities](../../../policy/overview.md) toegestaan die virtuele Linux-machines controleren die externe verbindingen van accounts zonder wachtwoorden toestaan en/of onjuiste machtigingen hebben ingesteld op het passwd-bestand. In deze blauwdruk worden ook beleidsdefinities toegevoegd die de configuratie van het wachtwoordversleutelingstype voor virtuele Windows-apparaten controleren. Door deze indicatoren te controleren, u ervoor zorgen dat systeemauthenticators voldoen aan het identificatie- en verificatiebeleid van uw organisatie.

- \[Preview:\]Linux VM's controleren die niet zijn ingesteld op 0644
- \[Preview\]: Linux VM's controleren die accounts hebben zonder wachtwoorden
- \[Voorbeeld:\]Windows VM's controleren die geen wachtwoorden opslaan met omkeerbare versleuteling
- \[Voorbeeld:\]Vereisten implementeren voor het controleren van Linux VM's die niet zijn ingesteld op 0644
- \[Preview:\]Vereisten implementeren om Linux VM's met accounts zonder wachtwoorden te controleren
- \[Voorbeeld:\]Vereisten implementeren om Windows VM's te controleren die geen wachtwoorden opslaan met omkeerbare versleuteling

## <a name="ia-5-1-authenticator-management--password-based-authentication"></a>IA-5(1) Authenticator Beheer | Verificatie op basis van wachtwoord

Met deze blauwdruk u sterke wachtwoorden afdwingen door [Azure Policy-definities](../../../policy/overview.md) toe te kennen die virtuele Windows-machines controleren die geen minimale sterkte en andere wachtwoordvereisten afdwingen. Bewustzijn van virtuele machines in strijd met het wachtwoord sterkte beleid helpt u corrigerende maatregelen te nemen om ervoor te zorgen wachtwoorden voor alle virtuele machine gebruikersaccounts voldoen aan het wachtwoord beleid van uw organisatie.

- \[Voorbeeld:\]Windows VM's controleren waarmee de vorige 24 wachtwoorden opnieuw kunnen worden gebruikt
- \[Voorbeeld:\]Windows VM's controleren die geen maximale wachtwoordleeftijd van 70 dagen hebben
- \[Preview:\]Windows VM's controleren die geen minimumwachtwoordleeftijd van 1 dag hebben
- \[Voorbeeld:\]Windows VM's controleren die de instelling voor wachtwoordcomplexiteit niet hebben ingeschakeld
- \[Voorbeeld:\]Windows VM's controleren die de minimale wachtwoordlengte niet beperken tot 14 tekens
- \[Voorbeeld:\]Windows VM's controleren die geen wachtwoorden opslaan met omkeerbare versleuteling
- \[Voorbeeld:\]Vereisten implementeren om Windows VM's te controleren waarmee de vorige 24 wachtwoorden opnieuw kunnen worden gebruikt
- \[Voorbeeld:\]Vereisten implementeren om Windows VM's te controleren die geen maximale wachtwoordleeftijd van 70 dagen hebben
- \[Voorbeeld:\]Vereisten implementeren om Windows VM's te controleren die geen minimumwachtwoordleeftijd van 1 dag hebben
- \[Voorbeeld:\]Vereisten implementeren om Windows VM's te controleren die niet zijn ingeschakeld voor de instelling voor de complexiteit van wachtwoorden
- \[Voorbeeld:\]Vereisten implementeren om Windows VM's te controleren die de minimale wachtwoordlengte niet beperken tot 14 tekens
- \[Voorbeeld:\]Vereisten implementeren om Windows VM's te controleren die geen wachtwoorden opslaan met omkeerbare versleuteling

## <a name="ir-6-2-incident-reporting--vulnerabilities-related-to-incidents"></a>IR-6 (2) Melding van incidenten | Kwetsbaarheden in verband met incidenten

Deze blauwdruk biedt beleidsdefinities die records controleren met analyse van kwetsbaarheidsbeoordeling op virtuele machines, virtuele machineschaalsets en SQL-servers. Deze inzichten bieden realtime informatie over de beveiligingsstatus van uw geïmplementeerde resources en kunnen u helpen bij het prioriteren van herstelacties.

- Kwetsbaarheden in beveiligingsconfiguratie op uw virtuele machineschaalsets moeten worden verholpen
- Kwetsbaarheden moeten worden verholpen door een oplossing voor kwetsbaarheidsbeoordeling
- Kwetsbaarheden in beveiligingsconfiguratie op uw machines moeten worden verholpen
- Kwetsbaarheden in containerbeveiligingsconfiguraties moeten worden verholpen
- Kwetsbaarheden in uw SQL-databases moeten worden verholpen

## <a name="ra-5-vulnerability-scanning"></a>RA-5 Kwetsbaarheid scannen

Met deze blauwdruk u kwetsbaarheden in het informatiesysteem beheren door [Azure Policy-definities](../../../policy/overview.md) toe te stellen die kwetsbaarheden in het besturingssysteem, SQL-kwetsbaarheden en kwetsbaarheden in virtuele machines in Azure Security Center bewaken. Azure Security Center biedt rapportagemogelijkheden waarmee u realtime inzicht hebt in de beveiligingsstatus van geïmplementeerde Azure-resources. Deze blauwdruk wijst ook beleidsdefinities toe die Advanced Data Security op SQL-servers controleren en afdwingen. Geavanceerde gegevensbeveiliging omvatte kwetsbaarheidsbeoordeling en geavanceerde mogelijkheden voor bedreigingsbescherming om u te helpen inzicht te krijgen in kwetsbaarheden in uw geïmplementeerde resources.

- Geavanceerde gegevensbeveiliging moet worden ingeschakeld op uw beheerde instanties
- Geavanceerde gegevensbeveiliging moet worden ingeschakeld op uw SQL-servers
- Geavanceerde gegevensbeveiliging implementeren op SQL-servers
- Kwetsbaarheden in beveiligingsconfiguratie op uw virtuele machineschaalsets moeten worden verholpen
- Kwetsbaarheden in beveiligingsconfiguratie op uw virtuele machines moeten worden verholpen
- Kwetsbaarheden in uw SQL-databases moeten worden verholpen
- Kwetsbaarheden moeten worden verholpen door een oplossing voor kwetsbaarheidsbeoordeling

## <a name="sc-5-denial-of-service-protection"></a>SC-5 Denial of Service Protection

De ddos-standaardlaag (Distributed Denial of Service) van Azure biedt extra functies en mitigatiemogelijkheden voor de basisservicelaag. Deze extra functies omvatten Azure Monitor-integratie en de mogelijkheid om rapporten over de beperking na de aanval te bekijken. In deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) toegesteld die wordt gecontroleerd als de DDoS-standaardlaag is ingeschakeld. Als u inzicht hebt in het verschil tussen de servicelagen, u de beste oplossing selecteren om denial of service-beveiligingen voor uw Azure-omgeving aan te pakken.

- DDoS Protection Standard moet ingeschakeld zijn

## <a name="sc-7-boundary-protection"></a>SC-7 grensbeveiliging

Met deze blauwdruk u de systeemgrens beheren en beheren door een [Azure-beleidsdefinitie](../../../policy/overview.md) toe te stellen die controleert op aanbevelingen voor netwerkbeveiligingsgroepen in Azure Security Center. Azure Security Center analyseert verkeerspatronen van internet tegenover virtuele machines en biedt aanbevelingen voor de regels voor netwerkbeveiliging om het potentiële aanvalsoppervlak te verminderen.
Bovendien wijst deze blauwdruk ook beleidsdefinities toe die onbeveiligde eindpunten, toepassingen en opslagaccounts controleren. Eindpunten en toepassingen die niet worden beschermd door een firewall en opslagaccounts met onbeperkte toegang kunnen onbedoelde toegang geven tot informatie in het informatiesysteem.

- Netwerkbeveiligingsgroepregels voor internet waarmee virtuele machines worden geconfronteerd, moeten worden verhard
- Toegang via het eindpunt van internet moet worden beperkt
- De NSGs-regels voor webapplicaties op IaaS moeten worden verhard
- Onbeperkte netwerktoegang tot opslagaccounts controleren

## <a name="sc-7-3-boundary-protection--access-points"></a>SC-7 (3) Grensbeveiliging | Toegangspunten

Just-in-time (JIT) toegang tot virtuele machines (JIT) vergrendelt binnenkomend verkeer naar virtuele Azure-machines, waardoor de blootstelling aan aanvallen wordt verminderd en u gemakkelijk verbinding maken met VM's wanneer dat nodig is. Met jit-toegang tot virtuele machines u het aantal externe verbindingen met uw resources in Azure beperken. In deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) opgenomen waarmee u virtuele machines controleren die just-in-time-toegang kunnen ondersteunen, maar nog niet zijn geconfigureerd.

- Just-In-Time-netwerktoegangsbeheer moet worden toegepast op virtuele machines

## <a name="sc-7-4-boundary-protection--external-telecommunications-services"></a>SC-7 (4) Grensbeveiliging | Externe telecommunicatiediensten

Just-in-time (JIT) toegang tot virtuele machines (JIT) vergrendelt binnenkomend verkeer naar virtuele Azure-machines, waardoor de blootstelling aan aanvallen wordt verminderd en u gemakkelijk verbinding maken met VM's wanneer dat nodig is. Met jit-toegang tot virtuele machines u uitzonderingen op uw verkeersstroombeleid beheren door de toegangsaanvraag- en goedkeuringsprocessen te vergemakkelijken. In deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) opgenomen waarmee u virtuele machines controleren die just-in-time-toegang kunnen ondersteunen, maar nog niet zijn geconfigureerd.

- Just-In-Time-netwerktoegangsbeheer moet worden toegepast op virtuele machines

## <a name="sc-8-1-transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>SC-8 (1) Vertrouwelijkheid en integriteit van de transmissie | Cryptografische of alternatieve fysieke bescherming

Met deze blauwdruk u de vertrouwelijke en integriteit van verzonden informatie beschermen door [Azure Policy-definities](../../../policy/overview.md) toe te wijsen waarmee u het cryptografische mechanisme controleren dat is geïmplementeerd voor communicatieprotocollen. Als u ervoor zorgt dat de communicatie goed is versleuteld, u voldoen aan de vereisten van uw organisatie of informatie beschermen tegen ongeautoriseerde openbaarmaking en wijziging.

- API-app mag alleen toegankelijk zijn via HTTPS
- Windows-webservers controleren die geen beveiligde communicatieprotocollen gebruiken
- Vereisten implementeren om Windows-webservers te controleren die geen beveiligde communicatieprotocollen gebruiken
- Functie-app mag alleen toegankelijk zijn via HTTPS
- Alleen beveiligde verbindingen met uw Redis-cache moeten worden ingeschakeld
- Beveiligde overdracht naar opslagaccounts moet zijn ingeschakeld
- Webtoepassing mag alleen toegankelijk zijn via HTTPS

## <a name="sc-28-1-protection-of-information-at-rest--cryptographic-protection"></a>SC-28 (1) Bescherming van informatie in rust | Cryptografische bescherming

Met deze blauwdruk u uw beleid afdwingen voor het gebruik van cryptograafbesturingselementen om informatie in rust te beschermen door [Azure Policy-definities](../../../policy/overview.md) toe te wijsen die specifieke cryptograafbesturingselementen afdwingen en het gebruik van zwakke cryptografische instellingen te controleren. Als u begrijpt waar uw Azure-resources mogelijk niet-optimale cryptografische configuraties hebben, u corrigerende maatregelen nemen om ervoor te zorgen dat resources zijn geconfigureerd in overeenstemming met uw informatiebeveiligingsbeleid. In het bijzonder vereisen de beleidsdefinities die door deze blauwdruk zijn toegewezen versleuteling voor opslagaccounts van gegevensbestanden. transparante gegevensversleuteling in SQL-databases vereisen; en controleer ontbrekende versleuteling op SQL-databases, virtuele machineschijven en automatiseringsaccountvariabelen.

- Geavanceerde gegevensbeveiliging moet worden ingeschakeld op uw beheerde instanties
- Geavanceerde gegevensbeveiliging moet worden ingeschakeld op uw SQL-servers
- Geavanceerde gegevensbeveiliging implementeren op SQL-servers
- SQL DB-transparante gegevensversleuteling implementeren
- Schijfversleuteling moet worden toegepast op virtuele machines
- Versleuteling vereisen voor Data Lake Store-accounts
- Transparante gegevensversleuteling in SQL-databases moet worden ingeschakeld

## <a name="si-2-flaw-remediation"></a>SI-2 Foutherstel

Met deze blauwdruk u fouten in het informatiesysteem beheren door [Azure Policy-definities](../../../policy/overview.md) toe te stellen die ontbrekende systeemupdates, kwetsbaarheden in het besturingssysteem, SQL-kwetsbaarheden en kwetsbaarheden in virtuele machines in Azure Security Center controleren. Azure Security Center biedt rapportagemogelijkheden waarmee u realtime inzicht hebt in de beveiligingsstatus van geïmplementeerde Azure-resources. Deze blauwdruk wijst ook een beleidsdefinitie toe die ervoor zorgt dat het besturingssysteem wordt gepatcht voor virtuele machineschaalsets.

- Automatische patchen van os-afbeeldingen vereisen op virtuele machineschaalsets
- Systeemupdates op virtuele machineschaalsets moeten worden geïnstalleerd
- Systeemupdates moeten worden geïnstalleerd op uw virtuele machines
- Kwetsbaarheden in beveiligingsconfiguratie op uw virtuele machineschaalsets moeten worden verholpen
- Kwetsbaarheden in beveiligingsconfiguratie op uw virtuele machines moeten worden verholpen
- Kwetsbaarheden in uw SQL-databases moeten worden verholpen
- Kwetsbaarheden moeten worden verholpen door een oplossing voor kwetsbaarheidsbeoordeling

## <a name="si-02-06-flaw-remediation--removal-of-previous-versions-of-software--firmware"></a>SI-02 (06) Foutherstel | Verwijdering van eerdere versies van software / firmware

Met deze blauwdruk worden beleidsdefinities toegevoegd die u helpen ervoor te zorgen dat toepassingen de nieuwste versie van het .NET Framework, HTTP, Java, PHP, Python en TLS gebruiken. Deze blauwdruk wijst ook een beleidsdefinitie toe die ervoor zorgt dat Kubernetes Services wordt geüpgraded naar de niet-kwetsbare versie.

- Ervoor zorgen dat de versie '.Net Framework' de nieuwste versie is, indien gebruikt als onderdeel van de API-app
- Ervoor zorgen dat de versie '.Net Framework' de nieuwste versie is, indien gebruikt als onderdeel van de functie-app
- Ervoor zorgen dat de versie '.Net Framework' de nieuwste versie is, indien gebruikt als onderdeel van de web-app
- Zorg ervoor dat 'HTTP-versie' de nieuwste is, indien gebruikt om de Api-app uit te voeren
- Zorg ervoor dat 'HTTP-versie' de laatste is, indien gebruikt om de functie-app uit te voeren
- Ervoor zorgen dat 'HTTP-versie' de laatste is, indien gebruikt om de web-app uit te voeren
- Zorg ervoor dat 'Java-versie' de nieuwste is, indien gebruikt als onderdeel van de Api-app
- Zorg ervoor dat 'Java-versie' de nieuwste is, indien gebruikt als onderdeel van de functie-app
- Zorg ervoor dat 'Java-versie' de nieuwste is, indien gebruikt als onderdeel van de web-app
- Zorg ervoor dat 'PHP-versie' de nieuwste is, indien gebruikt als onderdeel van de Api-app
- Zorg ervoor dat 'PHP-versie' de nieuwste is, indien gebruikt als onderdeel van de functie-app
- Zorg ervoor dat 'PHP-versie' de nieuwste is, indien gebruikt als onderdeel van de WEB-app
- Zorg ervoor dat 'Python-versie' de nieuwste is, indien gebruikt als onderdeel van de Api-app
- Ervoor zorgen dat 'Python-versie' de nieuwste is, indien gebruikt als onderdeel van de functie-app
- Zorg ervoor dat 'Python-versie' de nieuwste is, indien gebruikt als onderdeel van de web-app
- Nieuwste TLS-versie moet worden gebruikt in uw API-app
- Nieuwste TLS-versie moet worden gebruikt in uw functie-app
- Nieuwste TLS-versie moet worden gebruikt in uw web-app
- \[Preview\]: Kubernetes Services moet worden geüpgraded naar een niet-kwetsbare Kubernetes-versie

## <a name="si-3-malicious-code-protection"></a>SI-3 kwaadaardige codebeveiliging

Met deze blauwdruk u de beveiliging van eindpunten beheren, inclusief schadelijke codebeveiliging, door [Azure Policy-definities](../../../policy/overview.md) toe te wijzen die controleren op ontbrekende endpointbeveiliging op virtuele machines in Azure Security Center en de Microsoft-antimalwareoplossing op virtuele Windows-machines af te dwingen.

- Standaard Microsoft IaaSAntimalware-extensie implementeren voor Windows Server
- Endpoint-beveiligingsoplossing moet worden geïnstalleerd op virtuele machineschaalsets
- Ontbrekende endpointbeveiliging controleren in Azure Security Center

## <a name="si-3-1-malicious-code-protection--central-management"></a>SI-3 (1) Bescherming tegen kwaadaardige code | Centraal beheer

Met deze blauwdruk u de endpointbeveiliging beheren, inclusief schadelijke codebeveiliging, door [Azure Policy-definities](../../../policy/overview.md) toe te wijzen die controleren op ontbrekende endpoint-beveiliging op virtuele machines in Azure Security Center. Azure Security Center biedt gecentraliseerde beheer- en rapportagemogelijkheden waarmee u realtime inzicht hebt in de beveiligingsstatus van geïmplementeerde Azure-resources.

- Endpoint-beveiligingsoplossing moet worden geïnstalleerd op virtuele machineschaalsets
- Ontbrekende endpointbeveiliging controleren in Azure Security Center

## <a name="si-4-information-system-monitoring"></a>SI-4 Informatiesysteem monitoring

Met deze blauwdruk u uw systeem controleren en afdwingen door logboekregistratie en gegevensbeveiliging in Azure-bronnen te controleren en af te dwingen. Met name het beleid dat de implementatie van de Log Analytics-agent is toegewezen en afdwingt, en verbeterde beveiligingsinstellingen voor SQL-databases, opslagaccounts en netwerkbronnen. Deze mogelijkheden kunnen u helpen afwijkend gedrag en indicatoren van aanvallen te detecteren, zodat u passende maatregelen nemen.

- \[Preview:\]Audit Log Analytics Agent Deployment - VM Image (OS) niet vermeld
- \[Preview:\]Implementatie van auditloganalyseagent in VMSS - VM Image (OS) niet vermeld
- \[Preview\]: Audit Log Analytics Workspace for VM - Report Mismatch
- \[Preview\]: Log Analytics Agent voor Linux VM-schaalsets (VMSS) implementeren
- \[Preview:\]Log Analytics Agent implementeren voor Linux VM's
- \[Voorbeeld:\]Logboekanalyseagent voor Windows VM-schaalsets (VMSS) implementeren
- \[Voorbeeld:\]Logboekanalyseagent implementeren voor Windows VM's
- Geavanceerde gegevensbeveiliging moet worden ingeschakeld op uw beheerde instanties
- Geavanceerde gegevensbeveiliging moet worden ingeschakeld op uw SQL-servers
- Geavanceerde gegevensbeveiliging implementeren op SQL-servers
- Geavanceerde bedreigingsbeveiliging implementeren op opslagaccounts
- Auditing implementeren op SQL-servers
- Netwerkwatcher implementeren wanneer virtuele netwerken worden gemaakt
- Bedreigingsdetectie implementeren op SQL-servers
- Toegestane locaties
- Toegestane locaties voor resourcegroepen

## <a name="si-4-12-information-system-monitoring--automated-alerts"></a>SI-4 (12) Monitoring van het informatiesysteem | Automatische waarschuwingen

Deze blauwdruk bevat beleidsdefinities die u helpen ervoor te zorgen dat meldingen over gegevensbeveiliging correct zijn ingeschakeld. Bovendien zorgt deze blauwdruk ervoor dat de standaardprijslaag is ingeschakeld voor Azure Security Center. Houd er rekening mee dat de standaardprijslaag bedreigingsdetectie voor netwerken en virtuele machines mogelijk maakt, met bedreigingsinformatie, anomaliedetectie en gedragsanalyse in Azure Security Center.

- E-mailmelding aan de eigenaar van een abonnement voor waarschuwingen met hoge ernst moet worden ingeschakeld
- Voor uw abonnement moet een e-mailadres voor beveiligingscontact worden opgegeven 
- E-mailmeldingen aan beheerders en eigenaren van abonnementen moeten worden ingeschakeld in sql-beheerde instellingen voor geavanceerde gegevensbeveiliging 
- E-mailmeldingen aan beheerders en eigenaren van abonnementen moeten worden ingeschakeld in geavanceerde gegevensbeveiligingsinstellingen van SQL-server 
- Voor uw abonnement moet een telefoonnummer van een beveiligingscontactpersoon worden opgegeven
- Geavanceerde instellingen voor gegevensbeveiliging voor SQL-server moeten een e-mailadres bevatten om beveiligingswaarschuwingen te ontvangen
- De standaardprijslaag van het Beveiligingscentrum moet worden geselecteerd

## <a name="si-4-18-information-system-monitoring--analyze-traffic--covert-exfiltration"></a>SI-4 (18) Monitoring van het informatiesysteem | Verkeer analyseren / Geheime exfiltratie

Geavanceerde bedreigingsbeveiliging voor Azure Storage detecteert ongebruikelijke en mogelijk schadelijke pogingen om opslagaccounts te openen of te exploiteren. Beveiligingswaarschuwingen omvatten afwijkende toegangspatronen, afwijkende extracten/uploads en verdachte opslagactiviteiten. Deze indicatoren kunnen u helpen bij het detecteren van geheime exfiltratie van informatie.

- Geavanceerde bedreigingsbeveiliging implementeren op opslagaccounts

> [!NOTE]
> De beschikbaarheid van specifieke Azure Policy-definities kan variëren in Azure Government en andere nationale clouds. 

## <a name="next-steps"></a>Volgende stappen

Nu u de beheerkaart van de blauwdruk van DoD Impact Level 4 hebt bekeken, gaat u naar de volgende artikelen om meer te weten te komen over de blauwdruk en hoe u dit voorbeeld implementeert:

> [!div class="nextstepaction"]
> [DoD Impact Level 4 blueprint - Overzicht](./index.md)
> [DoD Impact Level 4 blauwdruk - Deploy stappen](./deploy.md)

Aanvullende artikelen over blauwdrukken en het gebruik hiervan:

- Meer informatie over de [levenscyclus van een blauwdruk](../../concepts/lifecycle.md).
- Meer informatie over hoe u [statische en dynamische parameters](../../concepts/parameters.md) gebruikt.
- Meer informatie over hoe u de [blauwdrukvolgorde](../../concepts/sequencing-order.md) aanpast.
- Meer informatie over hoe u gebruikmaakt van [resourcevergrendeling in blauwdrukken](../../concepts/resource-locking.md).
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../../how-to/update-existing-assignments.md).