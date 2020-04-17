---
title: Azure Security Benchmark blueprint sample besturingselementen
description: Beheer het toewijzingen van het blueprintvoorbeeld azure security benchmark voor Azure Policy.
ms.date: 04/16/2020
ms.topic: sample
ms.openlocfilehash: 0f2d24d7d8c9f7ce2568f11b15e65ed1fcd02afb
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538731"
---
# <a name="control-mapping-of-the-azure-security-benchmark-blueprint-sample"></a>Beheer van het blueprintvoorbeeld van Azure Security Benchmark

In het volgende artikel wordt beschreven hoe het blueprintvoorbeeld van Azure Blueprints Azure Security Benchmark wordt toegewezen aan de Azure Security Benchmark-besturingselementen. Zie [Azure Security Benchmark](https://docs.microsoft.com/azure/security/benchmarks/overview)voor meer informatie over de besturingselementen.

De volgende toewijzingen zijn aan de Azure Security Benchmark-besturingselementen. **Azure Security Benchmark** Gebruik de navigatie aan de rechterkant om direct naar een specifieke besturingstoewijzing te springen. De toegewezen besturingselementen worden geïmplementeerd met een [Azure Policy-initiatief.](../../../policy/overview.md) Als u het volledige initiatief wilt bekijken, opent u **Beleid** in de Azure-portal en selecteert u de pagina **Definities.** Zoek en selecteer vervolgens de ** \[aanbevelingen Preview\]: Audit Azure Security Benchmark en implementeer specifieke ondersteuning van vm-extensies** ingebouwd beleidsinitiatief.

> [!IMPORTANT]
> Elk besturingselement hieronder is gekoppeld aan een of meer [Azure Policy-definities.](../../../policy/overview.md) Met dit beleid u [beoordelen of de](../../../policy/how-to/get-compliance-data.md) controle is nageleefd; Er is echter vaak geen 1:1 of volledige overeenkomst tussen een besturingselement en een of meer beleidsregels. **Compliant** in Azure Policy verwijst daarom alleen naar het beleid zelf. Dit zorgt er niet voor dat u volledig voldoet aan alle vereisten van een controle. Bovendien kan de nalevingsstandaard besturingselementen bevatten die op dit moment niet worden behandeld door azure-beleidsdefinities. Naleving in Azure Policy is daarom slechts een gedeeltelijke weergave van uw algemene nalevingsstatus. De koppelingen tussen besturingselementen en Azure Policy-definities voor dit voorbeeld van nalevingsblauwdrukken kunnen in de loop van de tijd veranderen. Zie de [GitHub Commit History](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/azure-security-benchmark/control-mapping.md)om de wijzigingsgeschiedenis te bekijken.

## <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1 Resources beveiligen met netwerkbeveiligingsgroepen of Azure Firewall in uw virtuele netwerk

- Subnetten moeten worden gekoppeld aan een netwerkbeveiligingsgroep
- Adaptive Network Hardening aanbevelingen moeten worden toegepast op internet geconfronteerd met virtuele machines
- Virtuele machines moeten worden aangesloten op een goedgekeurd virtueel netwerk
- Virtuele apparaten met internetgerichte apparaten moeten worden beschermd met netwerkbeveiligingsgroepen
- ServiceBus moet een eindpunt voor virtuele netwerkservice gebruiken
- App Service moet een eindpunt voor virtuele netwerkservice gebruiken
- SQL Server moet een eindpunt van de virtuele netwerkservice gebruiken
- Event Hub moet een eindpunt voor virtuele netwerkservice gebruiken
- Cosmos DB moet een eindpunt voor virtuele netwerkservice gebruiken
- Key Vault moet een eindpunt voor virtuele netwerkservice gebruiken
- Onbeperkte netwerktoegang tot opslagaccounts controleren
- Opslagaccounts moeten een eindpunt voor virtuele netwerkservice gebruiken
- Containerregistry moet een eindpunt van de virtuele netwerkservice gebruiken
- Virtuele netwerken moeten een opgegeven virtuele netwerkgateway gebruiken
- Geautoriseerde IP-bereiken moeten worden gedefinieerd op Kubernetes Services
- \[Voorbeeld:\]IP Forwarding op uw virtuele machine moet worden uitgeschakeld
- Virtuele apparaten met internetgerichte apparaten moeten worden beschermd met netwerkbeveiligingsgroepen
- Just-In-Time-netwerktoegangsbeheer moet worden toegepast op virtuele machines
- Beheerpoorten moeten worden gesloten op uw virtuele machines

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2 De configuratie en het verkeer van Vnets, Subnetten en NIC's controleren en registreren

- Network Watcher moet ingeschakeld zijn

## <a name="13-protect-critical-web-applications"></a>1.3 Kritieke webapplicaties beschermen

- Ervoor zorgen dat web-app 'Clientcertificates (Inkomende clientcertificaten)' is ingesteld op 'Aan'
- CORS mag niet toestaan dat elke bron toegang krijgt tot uw webtoepassingen
- CORS mag niet toestaan dat elke resource toegang krijgt tot uw functie-apps
- CORS mag niet toestaan dat elke bron toegang krijgt tot uw API-app
- Externe foutopsporing moet worden uitgeschakeld voor webtoepassingen
- Externe foutopsporing moet worden uitgeschakeld voor functie-apps
- Externe foutopsporing moet worden uitgeschakeld voor API-apps

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4 Communicatie met bekende kwaadaardige IP-adressen weigeren

- DDoS Protection Standard moet ingeschakeld zijn
- Just-In-Time-netwerktoegangsbeheer moet worden toegepast op virtuele machines
- Adaptive Network Hardening aanbevelingen moeten worden toegepast op internet geconfronteerd met virtuele machines

## <a name="15-record-network-packets-and-flow-logs"></a>1.5 Netwerkpakketten en stroomlogboeken opnemen

- Network Watcher moet ingeschakeld zijn

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11 Gebruik geautomatiseerde tools om netwerkbronconfiguraties te bewaken en wijzigingen te detecteren

- Vereisten implementeren voor het controleren van Windows VM's-configuraties in 'Beveiligingsopties - netwerktoegang'
- Controleresultaten van Windows VM's weergeven in 'Beveiligingsopties - Microsoft Network Client'
- Vereisten implementeren voor het controleren van Windows VM's-configuraties in 'Beveiligingsopties - netwerkbeveiliging'
- Controleresultaten van Windows VM's weergeven in 'Beveiligingsopties - netwerkbeveiliging'
- Vereisten implementeren voor het controleren van Windows VM's-configuraties in 'Beveiligingsopties - Microsoft Network Server'
- Controleresultaten van Windows VM's weergeven in 'Beveiligingsopties - Microsoft Network Server'
- Vereisten implementeren voor het controleren van Windows VM's-configuraties in 'Beheersjablonen - netwerk'
- Controleresultaten van Windows VM's weergeven in 'Beheersjablonen - netwerk'

## <a name="22-configure-central-security-log-management"></a>2.2 Centrale beveiliginglogboekbeheer configureren

- De Log Analytics-agent moet op virtuele machines worden geïnstalleerd
- De Log Analytics-agent moet worden geïnstalleerd op virtuele machineschaalsets
- Vereisten implementeren om Windows VM's te controleren waarop de Log Analytics-agent niet is verbonden zoals verwacht
- Controleresultaten weergeven van Windows VM's waarop de Log Analytics-agent niet is verbonden zoals verwacht
- Azure Monitor-logboekprofiel moet logboeken verzamelen voor categorieën 'schrijven', 'verwijderen' en 'actie'
- Azure Monitor moet activiteitslogboeken uit alle regio's verzamelen
- Automatische inrichting van de Monitoringagent Log Analytics moet worden ingeschakeld op uw abonnement

## <a name="23-enable-audit-logging-for-azure-resources"></a>2.3 Controlelogboekregistratie inschakelen voor Azure-resources

- Diagnostische logboeken in Azure Data Lake Store moeten zijn ingeschakeld
- Diagnostische logboeken in Logische Apps moeten zijn ingeschakeld
- Diagnostische logboeken in IoT Hub moeten zijn ingeschakeld
- Diagnostische logboeken in Batch-accounts moeten zijn ingeschakeld
- Diagnostische logboeken in virtuele machineschaalsets moeten zijn ingeschakeld
- Diagnostische logboeken in gebeurtenishub moeten zijn ingeschakeld
- Diagnostische logboeken in zoekservices moeten zijn ingeschakeld
- Diagnostische logboeken in App Services moeten zijn ingeschakeld
- Diagnostische logboeken in Data Lake Analytics moeten zijn ingeschakeld
- Diagnostische logboeken in Key Vault moeten zijn ingeschakeld
- Diagnostische logboeken in servicebus moeten zijn ingeschakeld
- Diagnostische logboeken in Azure Stream Analytics moeten zijn ingeschakeld
- Controle op SQL-server moet worden ingeschakeld
- Diagnostische instelling voor controleren

## <a name="24-collect-security-logs-from-operating-systems"></a>2.4 Beveiligingslogboeken verzamelen van besturingssystemen

- Automatische inrichting van de Monitoringagent Log Analytics moet worden ingeschakeld op uw abonnement
- De Log Analytics-agent moet op virtuele machines worden geïnstalleerd
- De Log Analytics-agent moet worden geïnstalleerd op virtuele machineschaalsets
- Vereisten implementeren om Windows VM's te controleren waarop de Log Analytics-agent niet is verbonden zoals verwacht
- Controleresultaten weergeven van Windows VM's waarop de Log Analytics-agent niet is verbonden zoals verwacht

## <a name="27-enable-alerts-for-anomalous-activity"></a>2.7 Waarschuwingen voor afwijkende activiteiten inschakelen

- De standaardprijslaag van het Beveiligingscentrum moet worden geselecteerd
- Geavanceerde gegevensbeveiliging moet worden ingeschakeld op uw SQL-servers
- Geavanceerde gegevensbeveiliging moet worden ingeschakeld op uw SQL-beheerde exemplaren
- Geavanceerde typen bedreigingsbeveiliging moeten worden ingesteld op 'Alles' in sql-server geavanceerde gegevensbeveiligingsinstellingen
- Geavanceerde typen bedreigingsbeveiliging moeten worden ingesteld op 'Alles' in SQL-beheerde instantie Advanced Data Security-instellingen

## <a name="28-centralize-anti-malware-logging"></a>2.8 Centraliseren anti-malware logging

- Microsoft Antimalware voor Azure moet worden geconfigureerd om beveiligingshandtekeningen automatisch bij te werken
- Ontbrekende endpointbeveiliging controleren in Azure Security Center
- Endpoint-beveiligingsoplossing moet worden geïnstalleerd op virtuele machineschaalsets

## <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1 Inventaris er bij houden van de administratieve rekeningen

- Er moeten maximaal 3 eigenaren worden aangewezen voor uw abonnement
- Er moeten meer dan één eigenaar aan uw abonnement zijn toegewezen
- Externe accounts met eigenaarmachtigingen moeten uit uw abonnement worden verwijderd
- Afgeschafte accounts met eigenaarmachtigingen moeten uit uw abonnement worden verwijderd

## <a name="33-use-dedicated-administrative-accounts"></a>3.3 Gebruik maken van speciale administratieve rekeningen

- Vereisten implementeren voor het controleren van Windows VM's waarin de groep Administrators niet alleen de opgegeven leden bevat
- Controleresultaten van Windows VM's weergeven waarin de groep Administrators niet alleen de opgegeven leden bevat
- Vereisten implementeren voor het controleren van Windows VM's waarin de groep Administrators niet alle opgegeven leden bevat
- Controleresultaten van Windows VM's weergeven waarin de groep Administrators niet alle opgegeven leden bevat
- Vereisten implementeren om Windows VM's te controleren waarin de groep Administrators een van de opgegeven leden bevat
- Controleresultaten weergeven van Windows VM's waarin de groep Administrators een van de opgegeven leden bevat
- Er moeten maximaal 3 eigenaren worden aangewezen voor uw abonnement
- Er moeten meer dan één eigenaar aan uw abonnement zijn toegewezen

## <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5 Multi-factor authenticatie gebruiken voor alle Azure Active Directory-gebaseerde toegang

- MFA moet zijn ingeschakeld voor accounts met eigenaarmachtigingen voor uw abonnement
- MFA moet accounts zijn ingeschakeld met schrijfmachtigingen voor uw abonnement
- MFA moet zijn ingeschakeld voor accounts met leesmachtigingen voor uw abonnement

## <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7 Inloggen en waarschuwen voor verdachte activiteiten van administratieve accounts

- De standaardprijslaag van het Beveiligingscentrum moet worden geselecteerd

## <a name="39-use-azure-active-directory"></a>3.9 Azure Active Directory gebruiken

- Een Azure Active Directory-beheerder moet zijn ingericht voor SQL-servers
- Servicesfabricclusters mogen alleen Azure Active Directory gebruiken voor clientverificatie
- Ervoor zorgen dat Registreren met Azure Active Directory is ingeschakeld in de API-app
- Controleren of Registreren met Azure Active Directory is ingeschakeld in WEB App
- Ervoor zorgen dat Registreren met Azure Active Directory is ingeschakeld in functie-app

## <a name="310-regularly-review-and-reconcile-user-access"></a>3.10 Regelmatig gebruikerstoegang controleren en afstemmen

- Afgeschafte accounts moeten uit uw abonnement worden verwijderd
- Afgeschafte accounts met eigenaarmachtigingen moeten uit uw abonnement worden verwijderd
- Externe accounts met leesmachtigingen moeten uit uw abonnement worden verwijderd
- Externe accounts met schrijfmachtigingen moeten uit uw abonnement worden verwijderd
- Externe accounts met eigenaarmachtigingen moeten uit uw abonnement worden verwijderd

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1 Een inventaris van gevoelige informatie bijhouden

- Gevoelige gegevens in uw SQL-databases moeten worden geclassificeerd

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4 Alle gevoelige informatie tijdens het transport versleutelen

- Beveiligde overdracht naar opslagaccounts moet zijn ingeschakeld
- Nieuwste TLS-versie moet worden gebruikt in uw API-app
- Nieuwste TLS-versie moet worden gebruikt in uw web-app
- Nieuwste TLS-versie moet worden gebruikt in uw functie-app
- Functie-app mag alleen toegankelijk zijn via HTTPS
- Webtoepassing mag alleen toegankelijk zijn via HTTPS
- API-app mag alleen toegankelijk zijn via HTTPS
- SSL-verbinding afdwingen moet zijn ingeschakeld voor MySQL-databaseservers
- SSL-verbinding afdwingen moet zijn ingeschakeld voor PostgreSQL-databaseservers
- Alleen beveiligde verbindingen met uw Redis-cache moeten worden ingeschakeld

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5 Gebruik een actief detectieprogramma om gevoelige gegevens te identificeren

- Gevoelige gegevens in uw SQL-databases moeten worden geclassificeerd
- Geavanceerde gegevensbeveiliging moet worden ingeschakeld op uw SQL-servers
- Geavanceerde gegevensbeveiliging moet worden ingeschakeld op uw SQL-beheerde exemplaren

## <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6 Azure RBAC gebruiken om de toegang tot bronnen te beheren

- Role-Based Access Control (RBAC) moet worden gebruikt op Kubernetes Services
- Controle gebruik van aangepaste RBAC-regels

## <a name="48-encrypt-sensitive-information-at-rest"></a>4.8 Versleutel gevoelige informatie in rust

- Transparante gegevensversleuteling in SQL-databases moet worden ingeschakeld
- Schijfversleuteling moet worden toegepast op virtuele machines
- Niet-bevestigde schijven moeten worden versleuteld
- SQL server TDE protector moet worden versleuteld met uw eigen sleutel
- SQL managed instance TDE protector moet worden versleuteld met uw eigen sleutel
- Variabelen van automatiseringsaccount moeten worden versleuteld
- Clusterfabricclusters moeten de eigenschap ClusterProtectionLevel hebben ingesteld op EncryptAndSign

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9 Logboeken en waarschuwingen over wijzigingen in kritieke Azure-resources

- Azure Monitor moet activiteitslogboeken uit alle regio's verzamelen

## <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1 Geautomatiseerde hulpprogramma's voor het scannen van kwetsbaarheden uitvoeren

- Kwetsbaarheidsbeoordeling moet worden ingeschakeld op uw SQL-servers
- Kwetsbaarheidsbeoordeling moet worden ingeschakeld op uw SQL-beheerde exemplaren
- \[Preview\] Kwetsbaarheidsbeoordeling moet worden ingeschakeld op virtuele machines
- Instellingen voor kwetsbaarheidsbeoordeling voor SQL-server moeten een e-mailadres bevatten om scanrapporten te ontvangen

## <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2 Implementatie van een oplossing voor het patchbeheer van geautomatiseerd besturingssysteem

- Er moeten systeemupdates op uw computers zijn geïnstalleerd
- Systeemupdates op virtuele machineschaalsets moeten worden geïnstalleerd
- Ervoor zorgen dat de versie '.NET Framework' de nieuwste versie is, indien gebruikt als onderdeel van de functie-app
- Ervoor zorgen dat de versie '.NET Framework' de nieuwste versie is, indien gebruikt als onderdeel van de web-app
- Ervoor zorgen dat de versie '.NET Framework' de nieuwste versie is, indien gebruikt als onderdeel van de API-app

## <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3 Geautomatiseerde softwarepatchbeheeroplossing van derden implementeren

- Zorg ervoor dat 'PHP-versie' de nieuwste is, indien gebruikt als onderdeel van de Api-app
- Zorg ervoor dat 'PHP-versie' de nieuwste is, indien gebruikt als onderdeel van de WEB-app
- Zorg ervoor dat 'PHP-versie' de nieuwste is, indien gebruikt als onderdeel van de functie-app
- Zorg ervoor dat 'Java-versie' de nieuwste is, indien gebruikt als onderdeel van de web-app
- Zorg ervoor dat 'Java-versie' de nieuwste is, indien gebruikt als onderdeel van de functie-app
- Zorg ervoor dat 'Java-versie' de nieuwste is, indien gebruikt als onderdeel van de Api-app
- Zorg ervoor dat 'Python-versie' de nieuwste is, indien gebruikt als onderdeel van de web-app
- Ervoor zorgen dat 'Python-versie' de nieuwste is, indien gebruikt als onderdeel van de functie-app
- Zorg ervoor dat 'Python-versie' de nieuwste is, indien gebruikt als onderdeel van de Api-app
- Kubernetes Services moet worden geüpgraded naar een niet-kwetsbare Kubernetes-versie

## <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5 Gebruik een risicobeoordelingsproces om prioriteit te geven aan het herstel van ontdekte kwetsbaarheden

- Kwetsbaarheden moeten worden verholpen door een oplossing voor kwetsbaarheidsbeoordeling
- Kwetsbaarheden in beveiligingsconfiguratie op uw machines moeten worden verholpen
- Kwetsbaarheden in beveiligingsconfiguratie op uw virtuele machineschaalsets moeten worden verholpen
- Kwetsbaarheden in containerbeveiligingsconfiguraties moeten worden verholpen
- Kwetsbaarheden in uw SQL-databases moeten worden verholpen

## <a name="68-use-only-approved-applications"></a>6.8 Gebruik alleen goedgekeurde aanvragen

- De standaardprijslaag van het Beveiligingscentrum moet worden geselecteerd
- Adaptieve toepassingsbesturingselementen moeten worden ingeschakeld op virtuele machines

## <a name="69-use-only-approved-azure-services"></a>6.9 Alleen goedgekeurde Azure-services gebruiken

- Virtuele machines moeten worden gemigreerd naar nieuwe Azure Resource Manager-bronnen
- Opslagaccounts moeten worden gemigreerd naar nieuwe Azure Resource Manager-bronnen

## <a name="610-implement-approved-application-list"></a>6.10 Lijst met goedgekeurde aanvragen implementeren

- De standaardprijslaag van het Beveiligingscentrum moet worden geselecteerd
- Adaptieve toepassingsbesturingselementen moeten worden ingeschakeld op virtuele machines

## <a name="73-maintain-secure-azure-resource-configurations"></a>7.3 Veilige Azure-bronconfiguraties behouden

- \[Preview\]: Pod-beveiligingsbeleid moet worden gedefinieerd op Kubernetes Services

## <a name="74-maintain-secure-operating-system-configurations"></a>7.4 Veilige configuratie van het besturingssysteem behouden

- Kwetsbaarheden in beveiligingsconfiguratie op uw machines moeten worden verholpen
- Kwetsbaarheden in containerbeveiligingsconfiguraties moeten worden verholpen
- Kwetsbaarheden in beveiligingsconfiguratie op uw virtuele machineschaalsets moeten worden verholpen

## <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9 Geautomatiseerde configuratiebewaking voor Azure-services implementeren

- \[Preview\]: Pod-beveiligingsbeleid moet worden gedefinieerd op Kubernetes Services

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10 Geautomatiseerde configuratiebewaking voor besturingssystemen implementeren

- Kwetsbaarheden in beveiligingsconfiguratie op uw machines moeten worden verholpen
- Kwetsbaarheden in containerbeveiligingsconfiguraties moeten worden verholpen
- Kwetsbaarheden in beveiligingsconfiguratie op uw virtuele machineschaalsets moeten worden verholpen

## <a name="711-manage-azure-secrets-securely"></a>7.11 Azure-geheimen veilig beheren

- Key Vault-objecten moeten kunnen worden hersteld

## <a name="712-manage-identities-securely-and-automatically"></a>7.12 Identiteiten veilig en automatisch beheren

- Beheerde identiteit moet worden gebruikt in uw functie-app
- Beheerde identiteit moet worden gebruikt in uw web-app
- Beheerde identiteit moet worden gebruikt in uw API-app

## <a name="81-use-centrally-managed-anti-malware-software"></a>8.1 Gebruik centraal beheerde anti-malwaresoftware

- Ontbrekende endpointbeveiliging controleren in Azure Security Center
- Endpoint-beveiligingsoplossing moet worden geïnstalleerd op virtuele machineschaalsets

## <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2 Vooraf scannen van bestanden die moeten worden geüpload naar niet-compute Azure-bronnen

- De standaardprijslaag van het Beveiligingscentrum moet worden geselecteerd

## <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3 Ervoor zorgen dat antivirussoftware en -handtekeningen worden bijgewerkt

- Microsoft Antimalware voor Azure moet worden geconfigureerd om beveiligingshandtekeningen automatisch bij te werken

## <a name="91-ensure-regular-automated-back-ups"></a>9.1 Zorgen voor regelmatige geautomatiseerde back-ups

- Georedundant-back-up op lange termijn moet worden ingeschakeld voor Azure SQL-databases
- Georedundante back-up moet zijn ingeschakeld voor Azure Database voor MySQL
- Georedundante back-up moet worden ingeschakeld voor Azure Database voor PostgreSQL
- Georedundante back-up moet worden ingeschakeld voor Azure Database voor MariaDB
- Azure Backup moet zijn ingeschakeld voor virtuele machines

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2 Complete systeemback-ups uitvoeren en een back-up maken van beheerde sleutels van een klant

- Georedundant-back-up op lange termijn moet worden ingeschakeld voor Azure SQL-databases
- Georedundante back-up moet zijn ingeschakeld voor Azure Database voor MySQL
- Georedundante back-up moet worden ingeschakeld voor Azure Database voor PostgreSQL
- Georedundante back-up moet worden ingeschakeld voor Azure Database voor MariaDB
- Azure Backup moet zijn ingeschakeld voor virtuele machines

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4 Zorgen voor bescherming van back-ups en door de klant beheerde sleutels

- Key Vault-objecten moeten kunnen worden hersteld

## <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2 Een incidentscore- en prioriteringsprocedure maken

- De standaardprijslaag van het Beveiligingscentrum moet worden geselecteerd

## <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4 Contactgegevens voor beveiligingsincidenten verstrekken en waarschuwingsmeldingen configureren voor beveiligingsincidenten

- Voor uw abonnement moet een e-mailadres voor beveiligingscontact worden opgegeven
- Voor uw abonnement moet een telefoonnummer van een beveiligingscontactpersoon worden opgegeven
- Geavanceerde instellingen voor gegevensbeveiliging voor SQL-server moeten een e-mailadres bevatten om beveiligingswaarschuwingen te ontvangen
- Geavanceerde instellingen voor gegevensbeveiliging voor SQL-beheerde instantie moeten een e-mailadres bevatten om beveiligingswaarschuwingen te ontvangen
- E-mailmeldingen aan beheerders en eigenaren van abonnementen moeten worden ingeschakeld in geavanceerde gegevensbeveiligingsinstellingen van SQL-server
- E-mailmeldingen aan beheerders en eigenaren van abonnementen moeten worden ingeschakeld in sql-beheerde instellingen voor geavanceerde gegevensbeveiliging

## <a name="next-steps"></a>Volgende stappen

Nu u de beheertoewijzing van de azure security benchmark-blauwdruk hebt gecontroleerd, gaat u naar Azure Policy in de Azure-portal om het initiatief toe te wijzen:

> [!div class="nextstepaction"]
> [Azure Policy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions)

Aanvullende artikelen over blauwdrukken en het gebruik hiervan:

- Meer informatie over de [levenscyclus van een blauwdruk](../../concepts/lifecycle.md).
- Meer informatie over hoe u [statische en dynamische parameters](../../concepts/parameters.md) gebruikt.
- Meer informatie over hoe u de [blauwdrukvolgorde](../../concepts/sequencing-order.md) aanpast.
- Meer informatie over hoe u gebruikmaakt van [resourcevergrendeling in blauwdrukken](../../concepts/resource-locking.md).
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../../how-to/update-existing-assignments.md).