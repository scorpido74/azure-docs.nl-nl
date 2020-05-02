---
title: Voorbeeld besturings elementen voor Azure Security Bench Mark-blauw druk
description: De toewijzing van het voor beeld van de Azure Security Bench Mark-blauw druk aan Azure Policy.
ms.date: 04/16/2020
ms.topic: sample
ms.openlocfilehash: f80b72f06532adef28bf5e5afd1eb94c2e34ee2d
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691309"
---
# <a name="control-mapping-of-the-azure-security-benchmark-blueprint-sample"></a>De toewijzing van het voor beeld van Azure Security Bench Mark-blauw druk

In het volgende artikel wordt uitgelegd hoe het voor beeld van Azure blauw drukken Azure Security Bench Mark wordt toegewezen aan de besturings elementen voor Azure Security-benchmarks. Zie [Azure Security Bench Mark](../../../../security/benchmarks/overview.md)(Engelstalig) voor meer informatie over de besturings elementen.

De volgende toewijzingen zijn de **Azure Security Bench Mark** -besturings elementen. Gebruik de navigatie aan de rechter kant om rechtstreeks naar een specifieke besturings element koppeling te gaan. De toegewezen besturings elementen worden geïmplementeerd met een [Azure Policy](../../../policy/overview.md) -initiatief. Als u het complete initiatief wilt bekijken, opent u **beleid** in het Azure Portal en selecteert u de pagina **definities** . Zoek en selecteer vervolgens de ** \[Preview:\]aanbevelingen voor Azure Security-benchmarks controleren en specifieke ondersteuning** voor het ingebouwde beleid voor VM-extensies implementeren.

> [!IMPORTANT]
> Elk besturings element hieronder is gekoppeld aan een of meer [Azure Policy](../../../policy/overview.md) definities. Met deze beleids regels kunt u de naleving van het besturings element [beoordelen](../../../policy/how-to/get-compliance-data.md) . Er is echter vaak geen 1:1-of volledige overeenkomst tussen een besturings element en een of meer beleids regels. Als zodanig is de **naleving** in azure Policy alleen bedoeld voor het beleid zelf. Dit garandeert niet dat u volledig compatibel bent met alle vereisten van een besturings element. Daarnaast kan de nalevings standaard besturings elementen bevatten die op dit moment niet worden behandeld door Azure Policy definities. Daarom is naleving in Azure Policy slechts een gedeeltelijke weer gave van uw algemene nalevings status. De koppelingen tussen de besturings elementen en Azure Policy definities voor dit voor beeld van deze naleving blauw druk kunnen na verloop van tijd veranderen. Als u de wijzigings geschiedenis wilt weer geven, raadpleegt u de [github commit-geschiedenis](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/azure-security-benchmark/control-mapping.md).

## <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1 Beveilig bronnen met behulp van netwerk beveiligings groepen of Azure Firewall op uw Virtual Network

- Subnetten moeten worden gekoppeld aan een netwerk beveiligings groep
- Aanbevelingen voor adaptieve netwerk beveiliging moeten worden toegepast op Internet gerichte virtuele machines
- Virtuele machines moeten zijn verbonden met een goedgekeurd virtueel netwerk
- Internet gerichte virtuele machines moeten worden beveiligd met netwerk beveiligings groepen
- Service Bus moet een service-eind punt van een virtueel netwerk gebruiken
- App Service moet een service-eind punt van een virtueel netwerk gebruiken
- SQL Server moet een service-eind punt van een virtueel netwerk gebruiken
- Event hub moet een service-eind punt voor een virtueel netwerk gebruiken
- Cosmos DB moet een service-eind punt van een virtueel netwerk gebruiken
- Key Vault moet een service-eind punt van een virtueel netwerk gebruiken
- Onbeperkte netwerk toegang tot opslag accounts controleren
- Voor opslag accounts moet een service-eind punt van een virtueel netwerk worden gebruikt
- Container Registry moet een service-eind punt van een virtueel netwerk gebruiken
- Virtuele netwerken moeten de opgegeven virtuele netwerk gateway gebruiken
- Geautoriseerde IP-bereiken moeten worden gedefinieerd op Kubernetes Services
- \[Voor\]beeld: door sturen via IP op uw virtuele machine moet worden uitgeschakeld
- Internet gerichte virtuele machines moeten worden beveiligd met netwerk beveiligings groepen
- Just-In-Time-netwerktoegangsbeheer moet worden toegepast op virtuele machines
- Beheer poorten moeten worden gesloten op uw virtuele machines

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2 bewaking en logboek registratie van de configuratie en het verkeer van Vnets, subnetten en Nic's

- Network Watcher moet zijn ingeschakeld

## <a name="13-protect-critical-web-applications"></a>1,3 essentiële webtoepassingen beveiligen

- Zorg ervoor dat de WEB-app client certificaten (binnenkomende client certificaten) heeft ingesteld op on
- CORS mag niet alle bronnen toestaan om toegang te krijgen tot uw webtoepassingen
- CORS mag niet elke resource toestaan om toegang te krijgen tot uw functie-apps
- CORS mag niet alle bronnen toestaan om toegang te krijgen tot uw API-app
- Externe fout opsporing moet worden uitgeschakeld voor webtoepassingen
- Fout opsporing op afstand moet worden uitgeschakeld voor functie-apps
- Fout opsporing op afstand moet worden uitgeschakeld voor API Apps

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4 communicatie met bekende schadelijke IP-adressen weigeren

- DDoS Protection standaard moet zijn ingeschakeld
- Just-In-Time-netwerktoegangsbeheer moet worden toegepast op virtuele machines
- Aanbevelingen voor adaptieve netwerk beveiliging moeten worden toegepast op Internet gerichte virtuele machines

## <a name="15-record-network-packets-and-flow-logs"></a>1,5 netwerk pakketten en stroom logboeken vastleggen

- Network Watcher moet zijn ingeschakeld

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11 gebruik automatische hulpprogram ma's voor het bewaken van netwerk bron configuraties en het detecteren van wijzigingen

- Vereisten implementeren voor het controleren van Windows-Vm's in beveiligings opties-netwerk toegang
- Controle resultaten van configuraties van Windows-Vm's weer geven in beveiligings opties-micro soft-netwerkclient
- Vereisten voor het controleren van Windows-Vm's configureren in beveiligings opties-netwerk beveiliging
- Controle resultaten van configuraties van Windows-Vm's weer geven in beveiligings opties-netwerk beveiliging
- Vereisten voor het controleren van Windows-Vm's configureren in beveiligings opties-micro soft-netwerk server
- Controle resultaten van configuraties van Windows-Vm's weer geven in beveiligings opties-micro soft-netwerk server
- Vereisten implementeren voor het controleren van Windows-Vm's in Beheersjablonen-netwerk
- Controle resultaten weer geven van configuraties van Windows-Vm's in Beheersjablonen-netwerk

## <a name="22-configure-central-security-log-management"></a>2,2 centraal beveiligings logboek beheer configureren

- De Log Analytics-agent moet worden geïnstalleerd op virtuele machines
- De Log Analytics-agent moet worden geïnstalleerd op Virtual Machine Scale Sets
- Vereisten implementeren voor het controleren van Windows-Vm's waarop de Log Analytics-agent niet is aangesloten zoals verwacht
- Controle resultaten weer geven van Windows-Vm's waarop de Log Analytics-agent niet is aangesloten zoals verwacht
- Azure Monitor logboek profiel moet Logboeken verzamelen voor de categorieën schrijven, verwijderen, en actie
- Azure Monitor moet activiteiten logboeken van alle regio's verzamelen
- Het automatisch inrichten van de Log Analytics monitoring agent moet zijn ingeschakeld voor uw abonnement

## <a name="23-enable-audit-logging-for-azure-resources"></a>2,3 controle logboek registratie inschakelen voor Azure-resources

- Diagnostische logboeken in Azure Data Lake Store moeten worden ingeschakeld
- Diagnostische logboeken in Logic Apps moeten worden ingeschakeld
- Diagnostische logboeken in IoT Hub moeten worden ingeschakeld
- Diagnostische logboeken in batch-accounts moeten worden ingeschakeld
- Diagnostische logboeken in Virtual Machine Scale Sets moeten worden ingeschakeld
- Diagnostische logboeken in Event hub moeten worden ingeschakeld
- Diagnostische logboeken in Search Services moeten worden ingeschakeld
- Diagnostische logboeken in App Services moeten worden ingeschakeld
- Diagnostische logboeken in Data Lake Analytics moeten worden ingeschakeld
- Diagnostische logboeken in Key Vault moeten worden ingeschakeld
- Diagnostische logboeken in Service Bus moeten worden ingeschakeld
- Diagnostische logboeken in Azure Stream Analytics moeten worden ingeschakeld
- Controle op SQL Server moet zijn ingeschakeld
- Diagnostische instelling voor controleren

## <a name="24-collect-security-logs-from-operating-systems"></a>2,4 beveiligings logboeken verzamelen van besturings systemen

- Het automatisch inrichten van de Log Analytics monitoring agent moet zijn ingeschakeld voor uw abonnement
- De Log Analytics-agent moet worden geïnstalleerd op virtuele machines
- De Log Analytics-agent moet worden geïnstalleerd op Virtual Machine Scale Sets
- Vereisten implementeren voor het controleren van Windows-Vm's waarop de Log Analytics-agent niet is aangesloten zoals verwacht
- Controle resultaten weer geven van Windows-Vm's waarop de Log Analytics-agent niet is aangesloten zoals verwacht

## <a name="27-enable-alerts-for-anomalous-activity"></a>2,7 waarschuwingen voor afwijkende activiteit inschakelen

- Security Center prijs categorie moet worden geselecteerd
- Geavanceerde gegevens beveiliging moet zijn ingeschakeld op uw SQL-servers
- Geavanceerde gegevens beveiliging moet zijn ingeschakeld voor uw door SQL beheerde instanties
- De geavanceerde beveiligings typen voor bedreigingen moeten worden ingesteld op ' all ' in de instellingen voor geavanceerde gegevens beveiliging van SQL Server
- De geavanceerde beveiligings typen voor bedreigingen moeten worden ingesteld op ' all ' in de instellingen voor geavanceerde gegevens beveiliging van SQL Managed instance

## <a name="28-centralize-anti-malware-logging"></a>2,8 Centraliseer logboek registratie van schadelijke software

- Micro soft antimalware voor Azure moet zo worden geconfigureerd dat beveiligings handtekeningen automatisch worden bijgewerkt
- Ontbrekende Endpoint Protection in Azure Security Center controleren
- Endpoint Protection-oplossing moet worden geïnstalleerd op virtuele-machine schaal sets

## <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1 een inventaris van beheerders accounts onderhouden

- Er moeten Maxi maal drie eigen aren worden opgegeven voor uw abonnement
- Er moet meer dan één eigenaar aan uw abonnement zijn toegewezen
- Externe accounts met eigenaars machtigingen moeten worden verwijderd uit uw abonnement
- Afgeschafte accounts met eigenaars machtigingen moeten worden verwijderd uit uw abonnement

## <a name="33-use-dedicated-administrative-accounts"></a>3,3 speciale beheerders accounts gebruiken

- Vereisten implementeren voor het controleren van virtuele Windows-machines waarin de beheerders groep niet alleen de opgegeven leden bevat
- Controle resultaten weer geven van Windows-Vm's waarbij de beheerders groep niet alleen de opgegeven leden bevat
- Vereisten implementeren voor het controleren van Windows-Vm's waarbij de groep Administrators niet alle opgegeven leden bevat
- Controle resultaten weer geven van Windows-Vm's waarbij de groep Administrators niet alle opgegeven leden bevat
- Vereisten implementeren voor het controleren van virtuele Windows-machines waarin de groep Administrators een van de opgegeven leden bevat
- Controle resultaten weer geven van Windows-Vm's waarin de groep Administrators een van de opgegeven leden bevat
- Er moeten Maxi maal drie eigen aren worden opgegeven voor uw abonnement
- Er moet meer dan één eigenaar aan uw abonnement zijn toegewezen

## <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5 multi-factor Authentication gebruiken voor alle op Azure Active Directory gebaseerde toegang

- MFA moet zijn ingeschakeld voor accounts met eigenaars machtigingen voor uw abonnement
- MFA moet zijn ingeschakeld voor accounts met schrijf machtigingen voor uw abonnement
- MFA moet zijn ingeschakeld voor accounts met lees machtigingen voor uw abonnement

## <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7 logboek en waarschuwing voor verdachte activiteiten van beheerders accounts

- Security Center prijs categorie moet worden geselecteerd

## <a name="39-use-azure-active-directory"></a>3,9 gebruik Azure Active Directory

- Een Azure Active Directory beheerder moet worden ingericht voor SQL-servers
- Service Fabric-clusters mogen alleen gebruikmaken van Azure Active Directory voor client verificatie
- Zorg ervoor dat registreren met Azure Active Directory is ingeschakeld op API-app
- Zorg ervoor dat registreren bij Azure Active Directory is ingeschakeld voor WEB-app
- Zorg ervoor dat registreren met Azure Active Directory is ingeschakeld op functie-app

## <a name="310-regularly-review-and-reconcile-user-access"></a>3,10 regel matig de gebruikers toegang controleren en afstemmen

- Afgeschafte accounts moeten worden verwijderd uit uw abonnement
- Afgeschafte accounts met eigenaars machtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met lees machtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met schrijf machtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met eigenaars machtigingen moeten worden verwijderd uit uw abonnement

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1 een inventaris van gevoelige informatie onderhouden

- Gevoelige gegevens in uw SQL-data bases moeten worden geclassificeerd

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4 alle gevoelige informatie tijdens de overdracht versleutelen

- Beveiligde overdracht naar opslagaccounts moet zijn ingeschakeld
- De laatste TLS-versie moet worden gebruikt in uw API-app
- De laatste TLS-versie moet worden gebruikt in uw web-app
- De meest recente versie van TLS moet worden gebruikt in uw functie-app
- functie-app mag alleen toegankelijk zijn via HTTPS
- Webtoepassing mag alleen toegankelijk zijn via HTTPS
- De API-app mag alleen toegankelijk zijn via HTTPS
- SSL-verbinding afdwingen moet zijn ingeschakeld voor MySQL-database servers
- SSL-verbinding afdwingen moet zijn ingeschakeld voor PostgreSQL-database servers
- Alleen beveiligde verbindingen met uw Redis Cache moeten worden ingeschakeld

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5 een actief detectie hulpprogramma gebruiken om gevoelige gegevens te identificeren

- Gevoelige gegevens in uw SQL-data bases moeten worden geclassificeerd
- Geavanceerde gegevens beveiliging moet zijn ingeschakeld op uw SQL-servers
- Geavanceerde gegevens beveiliging moet zijn ingeschakeld voor uw door SQL beheerde instanties

## <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6 Azure RBAC gebruiken om de toegang tot bronnen te beheren

- Op rollen gebaseerde Access Control (RBAC) moet worden gebruikt voor Kubernetes-Services
- Gebruik van aangepaste RBAC-regels controleren

## <a name="48-encrypt-sensitive-information-at-rest"></a>4,8 gevoelige gegevens in rust versleutelen

- Transparent Data Encryption voor SQL-data bases moet zijn ingeschakeld
- Schijf versleuteling moet worden toegepast op virtuele machines
- Niet-gekoppelde schijven moeten worden versleuteld
- SQL Server TDE-beveiliging moet worden versleuteld met uw eigen sleutel
- TDE-beveiliging van SQL Managed instance moet worden versleuteld met uw eigen sleutel
- De variabelen van het Automation-account moeten worden versleuteld
- Voor Service Fabric clusters moet de eigenschap ClusterProtectionLevel zijn ingesteld op EncryptAndSign

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9 logboek en waarschuwing over wijzigingen in essentiële Azure-resources

- Azure Monitor moet activiteiten logboeken van alle regio's verzamelen

## <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1 automatische hulpprogram ma's voor het scannen van beveiligings problemen uitvoeren

- De evaluatie van beveiligings problemen moet worden ingeschakeld op uw SQL-servers
- De evaluatie van beveiligings problemen moet worden ingeschakeld voor uw door SQL beheerde instanties
- \[Evaluatie\] van het preview-beveiligings probleem moet zijn ingeschakeld op virtual machines
- De instellingen voor evaluatie van beveiligings problemen voor SQL Server moeten een e-mail adres bevatten voor het ontvangen van scan rapporten

## <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2 oplossing voor geautomatiseerd beheer van patches voor besturings systemen

- Er moeten systeemupdates op uw computers zijn geïnstalleerd
- Systeem updates op virtuele-machine schaal sets moeten worden geïnstalleerd
- Zorg ervoor dat de versie van .NET Framework het meest recent is, als deze wordt gebruikt als onderdeel van de functie-app
- Zorg ervoor dat de versie van .NET Framework het meest recent is, als deze wordt gebruikt als onderdeel van de web-app
- Zorg ervoor dat de versie van .NET Framework het meest recent is, als deze wordt gebruikt als onderdeel van de API-app

## <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3 geautomatiseerde oplossing voor software patch beheer van derden implementeren

- Zorg ervoor dat ' PHP-versie ' het meest recent is, als deze wordt gebruikt als onderdeel van de API-app
- Zorg ervoor dat ' PHP-versie ' het meest recent is, als deze wordt gebruikt als onderdeel van de WEB-app
- Zorg ervoor dat ' PHP-versie ' de meest recente is, als deze wordt gebruikt als onderdeel van de functie-app
- Zorg ervoor dat ' Java-versie ' het meest recent is, als deze wordt gebruikt als onderdeel van de web-app
- Zorg ervoor dat ' Java-versie ' het meest recent is, als deze wordt gebruikt als onderdeel van de functie-app
- Zorg ervoor dat ' Java-versie ' het meest recent is, als deze wordt gebruikt als onderdeel van de API-app
- Controleer of ' python-versie ' het meest recent is, als deze wordt gebruikt als onderdeel van de web-app
- Controleer of ' python-versie ' het meest recent is, als deze wordt gebruikt als onderdeel van de functie-app
- Controleer of ' python-versie ' het meest recent is, als deze wordt gebruikt als onderdeel van de API-app
- Kubernetes Services moet worden bijgewerkt naar een niet-kwets bare Kubernetes-versie

## <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5 een risico classificatie proces gebruiken om prioriteit te geven aan het herstel van ontdekte beveiligings problemen

- Beveiligings problemen moeten worden opgelost met een oplossing voor de evaluatie van de beveiligings lekken
- Beveiligings problemen in de beveiligings configuratie op uw computers moeten worden hersteld
- Beveiligings problemen in de beveiligings configuratie van de schaal sets van virtuele machines moeten worden hersteld
- Beveiligings problemen in container beveiligings configuraties moeten worden hersteld
- Beveiligings problemen voor uw SQL-data bases moeten worden hersteld

## <a name="68-use-only-approved-applications"></a>6,8 alleen goedgekeurde toepassingen gebruiken

- Security Center prijs categorie moet worden geselecteerd
- Adaptieve toepassings besturings elementen moeten worden ingeschakeld op virtuele machines

## <a name="69-use-only-approved-azure-services"></a>6,9 alleen goedgekeurde Azure-Services gebruiken

- Virtuele machines moeten worden gemigreerd naar nieuwe Azure Resource Manager-resources
- Opslag accounts moeten worden gemigreerd naar nieuwe Azure Resource Manager-resources

## <a name="610-implement-approved-application-list"></a>6,10 lijst met goedgekeurde toepassingen implementeren

- Security Center prijs categorie moet worden geselecteerd
- Adaptieve toepassings besturings elementen moeten worden ingeschakeld op virtuele machines

## <a name="73-maintain-secure-azure-resource-configurations"></a>7,3 beveiligde Azure-resource configuraties onderhouden

- \[Voor\]beeld: Pod-beveiligings beleid moet worden gedefinieerd op Kubernetes-Services

## <a name="74-maintain-secure-operating-system-configurations"></a>7,4 veilige besturingssysteem configuraties onderhouden

- Beveiligings problemen in de beveiligings configuratie op uw computers moeten worden hersteld
- Beveiligings problemen in container beveiligings configuraties moeten worden hersteld
- Beveiligings problemen in de beveiligings configuratie van de schaal sets van virtuele machines moeten worden hersteld

## <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9 geautomatiseerde configuratie bewaking voor Azure-Services implementeren

- \[Voor\]beeld: Pod-beveiligings beleid moet worden gedefinieerd op Kubernetes-Services

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10 geautomatiseerde configuratie bewaking voor besturings systemen implementeren

- Beveiligings problemen in de beveiligings configuratie op uw computers moeten worden hersteld
- Beveiligings problemen in container beveiligings configuraties moeten worden hersteld
- Beveiligings problemen in de beveiligings configuratie van de schaal sets van virtuele machines moeten worden hersteld

## <a name="711-manage-azure-secrets-securely"></a>7,11 Azure-geheimen veilig beheren

- Key Vault objecten moeten worden hersteld

## <a name="712-manage-identities-securely-and-automatically"></a>7,12 identiteiten veilig en automatisch beheren

- De beheerde identiteit moet worden gebruikt in uw functie-app
- De beheerde identiteit moet worden gebruikt in uw web-app
- Beheerde identiteit moet worden gebruikt in uw API-app

## <a name="81-use-centrally-managed-anti-malware-software"></a>8,1 centraal beheerde anti-malware-software gebruiken

- Ontbrekende Endpoint Protection in Azure Security Center controleren
- Endpoint Protection-oplossing moet worden geïnstalleerd op virtuele-machine schaal sets

## <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2 vooraf gescande bestanden die moeten worden geüpload naar niet-reken resources van Azure

- Security Center prijs categorie moet worden geselecteerd

## <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3 controleren of anti-malware-software en hand tekeningen zijn bijgewerkt

- Micro soft antimalware voor Azure moet zo worden geconfigureerd dat beveiligings handtekeningen automatisch worden bijgewerkt

## <a name="91-ensure-regular-automated-back-ups"></a>9,1 zorgen voor regel matige automatische back-ups

- Het maken van een geo-redundante back-up op lange termijn moet zijn ingeschakeld voor Azure SQL-data bases
- De geo-redundante back-up moet zijn ingeschakeld voor Azure Database for MySQL
- De geo-redundante back-up moet zijn ingeschakeld voor Azure Database for PostgreSQL
- De geo-redundante back-up moet zijn ingeschakeld voor Azure Database for MariaDB
- Azure Backup moet zijn ingeschakeld voor Virtual Machines

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2 volledige back-ups van het systeem uitvoeren en een back-up maken van door de klant beheerde sleutels

- Het maken van een geo-redundante back-up op lange termijn moet zijn ingeschakeld voor Azure SQL-data bases
- De geo-redundante back-up moet zijn ingeschakeld voor Azure Database for MySQL
- De geo-redundante back-up moet zijn ingeschakeld voor Azure Database for PostgreSQL
- De geo-redundante back-up moet zijn ingeschakeld voor Azure Database for MariaDB
- Azure Backup moet zijn ingeschakeld voor Virtual Machines

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4 zorgen voor de bescherming van back-ups en door de klant beheerde sleutels

- Key Vault objecten moeten worden hersteld

## <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2 een beoordelings procedure voor incidenten en prioriteits procedures maken

- Security Center prijs categorie moet worden geselecteerd

## <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4 de contact gegevens van het beveiligings incident opgeven en waarschuwings meldingen configureren voor beveiligings incidenten

- Voor uw abonnement moet een e-mail adres voor een beveiligings contact worden gegeven
- Voor uw abonnement moet een telefoon nummer voor de contact persoon voor beveiliging worden gegeven
- Geavanceerde instellingen voor gegevens beveiliging voor SQL Server moeten een e-mail adres bevatten voor het ontvangen van beveiligings waarschuwingen
- Geavanceerde instellingen voor gegevens beveiliging voor het beheerde exemplaar van SQL moeten een e-mail adres bevatten voor het ontvangen van beveiligings waarschuwingen
- E-mail meldingen voor beheerders en abonnements eigenaren moeten zijn ingeschakeld in de beveiligings instellingen van SQL Server Advanced Data
- E-mail meldingen voor beheerders en abonnements eigenaren moeten zijn ingeschakeld in de beveiligings instellingen voor geavanceerde gegevens van SQL Managed instance

## <a name="next-steps"></a>Volgende stappen

Nu u de toewijzing van het besturings element van de blauw druk van Azure Security hebt gecontroleerd, gaat u naar Azure Policy in het Azure Portal om het initiatief toe te wijzen:

> [!div class="nextstepaction"]
> [Azure Policy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions)

Aanvullende artikelen over blauwdrukken en het gebruik hiervan:

- Meer informatie over de [levenscyclus van een blauwdruk](../../concepts/lifecycle.md).
- Meer informatie over hoe u [statische en dynamische parameters](../../concepts/parameters.md) gebruikt.
- Meer informatie over hoe u de [blauwdrukvolgorde](../../concepts/sequencing-order.md) aanpast.
- Meer informatie over hoe u gebruikmaakt van [resourcevergrendeling in blauwdrukken](../../concepts/resource-locking.md).
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../../how-to/update-existing-assignments.md).