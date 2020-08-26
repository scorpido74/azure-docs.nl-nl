---
title: Besturingselementen van het HIPAA HITRUST-blauwdrukvoorbeeld
description: Besturingselementen toewijzen van HIPAA HITRUST-blauwdrukvoorbeelden. Elke beheeroptie wordt toegewezen aan een of meer Azure-beleidsregels die helpt bij de evaluatie.
ms.date: 08/03/2020
ms.topic: sample
ms.openlocfilehash: 10b771e3cfb18a28bd720332a26e13bb1d1f6022
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/14/2020
ms.locfileid: "88209419"
---
# <a name="control-mapping-of-the-hipaa-hitrust-blueprint-sample"></a>Besturingselementen toewijzen van HIPAA HITRUST-blauwdrukvoorbeelden

In het volgende artikel wordt uitgelegd hoe het blauwdrukvoorbeeld HIPAA HITRUST van Azure Blueprints wordt toegewezen aan de besturingselementen van HIPAA HITRUST. Zie [HIPAA HITRUST](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html) voor meer informatie over de besturingselementen.

De volgende toewijzingen zijn voor de besturingselementen van **HIPAA HITRUST**. Gebruik de navigatie aan de rechterkant om rechtstreeks naar een toewijzing van een specifieke beheeroptie te gaan. Veel van de toegewezen beheeropties worden geïmplementeerd met een [Azure Policy](../../../policy/overview.md)-initiatief. Als u het complete initiatief wilt bekijken, opent u **Beleid** in de Azure-portal en selecteert u de pagina **Definities**. Zoek en selecteer vervolgens de **\[Preview\]: HIPAA HITRUST-besturingselementen controleren**, een ingebouwd beleidsinitiatief.

> [!IMPORTANT]
> Elke beheeroptie hieronder is gekoppeld aan een of meer [Azure Policy](../../../policy/overview.md)-definities. Met deze beleidsregels kunt u de [compliance beoordelen](../../../policy/how-to/get-compliance-data.md) met de beheeroptie. Er is echter vaak geen één-op-één- of volledige overeenkomst tussen een beheeroptie en een of meer beleidsregels. Als zodanig verwijst de term **Conform** in Azure Policy alleen naar de beleidsregels zelf. Dit garandeert niet dat u volledig conform bent met alle vereisten van een beheeroptie. Daarnaast bevat de compliance standaard beheeropties die op dit moment nog niet worden beschreven door Azure Policy-definities. Daarom is compliance in Azure Policy slechts een gedeeltelijke weergave van uw algemene compliancestatus. De koppelingen tussen de beheeropties en Azure Policy definities voor dit blauwdrukvoorbeeld voor compliance kunnen na verloop van tijd veranderen. Als u de wijzigingsgeschiedenis wilt bekijken, raadpleegt u de [GitHub Commit-geschiedenis](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/HIPAA-HITRUST/control-mapping.md).

## <a name="control-against-malicious-code"></a>Bescherming tegen schadelijke code

Met deze blauwdruk kunt u Endpoint Protection beheren, met inbegrip van beveiliging tegen schadelijke code, door [Azure Policy](../../../policy/overview.md)-definities toe te wijzen die controleren op ontbrekende Endpoint Protection op virtuele machines in Azure Security Center en de Microsoft-antimalware-oplossing afdwingen op virtuele Windows-machines.

- Microsoft Antimalware voor Azure moet zijn geconfigureerd om automatisch beveiligingsdefinities bij te werken
- Ontbrekende Endpoint Protection bewaken in Azure Security Center
- De Endpoint Protection-oplossing moet worden geïnstalleerd op virtuele-machineschaalsets
- Besturingselementen voor adaptieve toepassingen om veilige toepassingen in de toegestane lijst op te nemen, moeten worden ingeschakeld op uw computers


## <a name="management-of-removable-media"></a>Beheer van verwisselbare media

De organisatie registreert media (met inbegrip van laptops) voordat ze worden gebruikt, plaatst redelijke beperkingen voor de manier waarop dergelijke media worden gebruikt en biedt een passend niveau van fysieke en logische beveiliging (inclusief versleuteling) voor media die gedekte informatie bevatten voordat ze juist worden vernietigd of opgeschoond. Dit gebeurt op basis van het niveau van de gegevensclassificatie.

- Transparent Data Encryption in SQL-databases moet zijn ingeschakeld
- Schijfversleuteling moet worden toegepast op virtuele machines
- Niet-gekoppelde schijven moeten worden versleuteld
- Versleuteling van Data Lake Store-accounts vereisen
- TDE-beveiliging van de SQL-server moet worden versleuteld met uw eigen sleutel
- TDE-beveiliging van SQL Managed Instance moet worden versleuteld met uw eigen sleutel

## <a name="control-of-operational-software"></a>Beheer van operationele software 

De organisatie identificeert niet-geautoriseerde software op het informatiesysteem, waaronder servers, werkstations en laptops, maakt gebruik van een beleid waarbij toestaan de regel en weigeren de uitzondering is om de uitvoering van bekende ongeoorloofde software op het informatiesysteem te verbieden, en controleert de lijst van niet-geautoriseerde software en werkt deze minimaal jaarlijks bij.

- Beveiligingsproblemen in de beveiligingsconfiguratie op uw computers moeten worden hersteld
- Beveiligingsproblemen in beveiligingsconfiguraties voor containers moeten worden verholpen
- Beveiligingsproblemen in de beveiligingsconfiguratie van virtuele-machineschaalsets moeten worden hersteld
- Besturingselementen voor adaptieve toepassingen om veilige toepassingen in de toegestane lijst op te nemen, moeten worden ingeschakeld op uw computers

## <a name="change-control-procedures"></a>Controleprocedures wijzigen

De integriteit van alle installatiekopieën van virtuele machines wordt continu gewaarborgd door een waarschuwing in te schakelen en registreren voor wijzigingen in installatiekopieën van virtuele machines. Daarnaast worden de resultaten van een wijziging of verplaatsing en de bijbehorende validatie van de integriteit van de installatiekopieën ter beschikking gesteld aan de bedrijfseigenaar en/of klant(en) via elektronische methoden (zoals portals of waarschuwingen).

- \[Preview\] Auditresultaten van configuraties voor Windows-VM's weergeven in ‘Systeemcontrolebeleid - Uitvoerige tracering’
- \[Preview\]: Controleresultaten van configuraties voor Windows-VM's weergeven in ‘Systeemcontrolebeleid - Uitvoerige tracering’

## <a name="control-of-technical-vulnerabilities"></a>Beheer van technische beveiligingsproblemen 

Er bestaat een beveiligde configuratiestandaard voor alle systeem- en netwerkonderdelen.

- De evaluatie van beveiligingsproblemen moet worden ingeschakeld op uw SQL-servers
- De evaluatie van beveiligingsproblemen moet worden ingeschakeld voor uw beheerde SQL-exemplaren
- \[Preview\] Evaluatie van beveiligingsproblemen moet zijn ingeschakeld op virtuele machines
- Beveiligingsproblemen moeten worden opgelost met een oplossing voor evaluatie van beveiligingsproblemen
- Beveiligingsproblemen in de beveiligingsconfiguratie op uw computers moeten worden hersteld
- Beveiligingsproblemen in de beveiligingsconfiguratie van virtuele-machineschaalsets moeten worden hersteld
- Beveiligingsproblemen in beveiligingsconfiguraties voor containers moeten worden verholpen
- Beveiligingsproblemen in uw SQL-databases moeten worden opgelost
- \[Preview\]: Beveiligingsbeleid voor pods moet worden gedefinieerd voor Kubernetes Services

## <a name="segregation-in-networks"></a>Scheiding in netwerken

De beveiligingsgateways van de organisatie (zoals firewalls) dwingen beveiligingsbeleid af en zijn geconfigureerd voor het filteren van verkeer tussen domeinen, het blokkeren van onbevoegde toegang en worden gebruikt voor het onderhouden van de scheiding tussen interne bekabelde, interne draadloze en externe netwerksegmenten (bijvoorbeeld het internet), waaronder DMZ's. Bovendien wordt beleid voor toegangsbeheer afgedwongen voor elk domein.

- Subnetten moeten worden gekoppeld aan een netwerkbeveiligingsgroep
- Virtuele machines moeten zijn verbonden met een goedgekeurd virtueel netwerk
- Virtuele machines moeten worden gekoppeld aan een netwerkbeveiligingsgroep
- Service Bus moet gebruikmaken van een service-eindpunt voor een virtueel netwerk
- App Service moet gebruikmaken van een service-eindpunt voor een virtueel netwerk
- SQL Server moet gebruikmaken van een service-eindpunt voor een virtueel netwerk
- Event Hub moet gebruikmaken van een service-eindpunt voor een virtueel netwerk
- Cosmos DB moet gebruikmaken van een service-eindpunt voor een virtueel netwerk
- Key Vault moet gebruikmaken van een virtuele-netwerkservice-eindpunt
- Gatewaysubnetten moeten niet worden geconfigureerd met een netwerkbeveiligingsgroep
- Opslagaccounts moeten gebruikmaken van een  service-eindpunt voor een virtueel netwerk
- \[Preview\]: Container Registry moet gebruikmaken van een virtuele-netwerkservice-eindpunt
- Aanbevelingen voor adaptieve netwerkbeveiliging moeten worden toegepast op intern gerichte virtuele machines

## <a name="network-connection-control"></a>Beheer van de netwerkverbinding

Netwerkverkeer wordt beheerd in overeenstemming met het toegangsbeleid van de organisatie via firewall en andere netwerkbeperkingen voor elk netwerktoegangspunt, of de beheerde interface van de externe telecommunicatieservice.

- Beveiligde overdracht naar opslagaccounts moet zijn ingeschakeld
- De nieuwste TLS-versie moet worden gebruikt in uw API-app
- De nieuwste TLS-versie moet worden gebruikt in uw web-app
- De nieuwste TLS-versie moet worden gebruikt in uw functie-app
- Functie-app mag alleen toegankelijk zijn via HTTPS
- Webtoepassing mag alleen toegankelijk zijn via HTTPS
- API-app mag alleen toegankelijk zijn via HTTPS
- SSL-verbinding afdwingen moet worden ingeschakeld voor MySQL-databaseservers
- SSL-verbinding afdwingen moet worden ingeschakeld voor PostgreSQL-databaseservers
- Alleen beveiligde verbindingen met uw Redis Cache moeten zijn ingeschakeld
- Subnetten moeten worden gekoppeld aan een netwerkbeveiligingsgroep
- De regels voor NSG's ten aanzien van webtoepassingen op IaaS moeten strenger worden
- De regels voor netwerkbeveiligingsgroepen voor virtuele machines die zijn gericht op internet moeten strenger worden
- Virtuele machines moeten zijn verbonden met een goedgekeurd virtueel netwerk
- Virtuele machines moeten worden gekoppeld aan een netwerkbeveiligingsgroep

## <a name="network-controls"></a>Netwerkbesturingselementen

De organisatie gebruikt beveiligde en versleutelde communicatiekanalen bij het migreren van fysieke servers, toepassingen of gegevens naar gevirtualiseerde servers.

- Just-In-Time-netwerktoegangsbeheer moet worden toegepast op virtuele machines
- Aanbevelingen voor adaptieve netwerkbeveiliging moeten worden toegepast op intern gerichte virtuele machines
- Service Bus moet gebruikmaken van een service-eindpunt voor een virtueel netwerk
- App Service moet gebruikmaken van een service-eindpunt voor een virtueel netwerk
- SQL Server moet gebruikmaken van een service-eindpunt voor een virtueel netwerk
- Event Hub moet gebruikmaken van een service-eindpunt voor een virtueel netwerk
- Cosmos DB moet gebruikmaken van een service-eindpunt voor een virtueel netwerk
- Key Vault moet gebruikmaken van een virtuele-netwerkservice-eindpunt
- Netwerktoegang tot opslagaccounts moet zijn beperkt
- Opslagaccounts moeten gebruikmaken van een  service-eindpunt voor een virtueel netwerk
- \[Preview\]: Container Registry moet gebruikmaken van een virtuele-netwerkservice-eindpunt

## <a name="security-of-network-services"></a>Beveiliging van netwerkservices

Overeengekomen services die door een netwerkserviceprovider/-beheerder worden geboden, worden formeel beheerd en bewaakt om ervoor te zorgen dat ze veilig worden geboden.

- \[Preview\]: De gegevensverzamelingsagent voor het netwerkverkeer moet worden geïnstalleerd op virtuele Windows-machines
- \[Preview\]: De gegevensverzamelingsagent voor het netwerkverkeer moet zijn geïnstalleerd op virtuele machines van Linux en Network Watcher moet zijn ingeschakeld

## <a name="information-exchange-policies-and-procedures"></a>Beleid en procedures voor informatieoverdracht

De organisatie beperkt het gebruik van door de organisatie beheerde, draagbare opslagmedia door geautoriseerde personen op externe informatiesystemen.

- Controleren of clientcertificaten (inkomende clientcertificaten) zijn ingesteld op Aan voor de web-app
- Gebruik van CORS mag er niet toe leiden dat elke resource toegang heeft tot uw webtoepassingen
- Gebruik van CORS mag er niet toe leiden dat elke resource toegang heeft tot uw Function-apps
- Gebruik van CORS mag er niet toe leiden dat elke resource toegang tot uw API-app heeft
- Externe foutopsporing moet worden uitgeschakeld voor webtoepassingen
- Externe foutopsporing moet worden uitgeschakeld voor Function-apps
- Externe foutopsporing moet worden uitgeschakeld voor API-apps

## <a name="on-line-transactions"></a>Online transacties

De organisatie vereist het gebruik van versleuteling tussen, en het gebruik van elektronische handtekeningen door elk van de partijen bij de transactie. De organisatie garandeert dat de opslag van de transactiegegevens zich niet in een openbaar toegankelijke omgeving bevindt (bijvoorbeeld op een opslagplatform op het intranet van de organisatie), niet wordt bewaard en niet via een opslagmedium rechtstreeks toegankelijk is vanaf het internet. Wanneer een vertrouwde instantie wordt gebruikt (bijvoorbeeld voor het verlenen en onderhouden van digitale handtekeningen en/of digitale certificaten), is de beveiliging geïntegreerd en ingesloten in het volledige end-to-end-proces voor het beheren van certificaten en handtekeningen.

- Beveiligde overdracht naar opslagaccounts moet zijn ingeschakeld
- De nieuwste TLS-versie moet worden gebruikt in uw API-app
- De nieuwste TLS-versie moet worden gebruikt in uw web-app
- De nieuwste TLS-versie moet worden gebruikt in uw functie-app
- Functie-app mag alleen toegankelijk zijn via HTTPS
- Webtoepassing mag alleen toegankelijk zijn via HTTPS
- API-app mag alleen toegankelijk zijn via HTTPS
- SSL-verbinding afdwingen moet worden ingeschakeld voor MySQL-databaseservers
- SSL-verbinding afdwingen moet worden ingeschakeld voor PostgreSQL-databaseservers
- Alleen beveiligde verbindingen met uw Redis Cache moeten zijn ingeschakeld
- \[Preview\]: Vereisten implementeren om Windows VM's te controleren die niet de opgegeven certificaten in Vertrouwde basiscertificeringsinstanties bevatten
- \[Preview\]: Controleresultaten weergeven van Windows-VM's die niet de opgegeven certificaten in Vertrouwde basiscertificeringsinstanties bevatten

## <a name="user-password-management"></a>Beheer van gebruikerswachtwoorden

Wachtwoorden worden versleuteld tijdens de overdracht en opslag op alle systeemonderdelen.

- \[Preview\] VM's met onveilige instellingen voor wachtwoordbeveiliging controleren

## <a name="user-authentication-for-external-connections"></a>Gebruikersverificatie gebruiken voor externe verbindingen

Krachtige verificatiemethoden zoals meervoudige verificatie, RADIUS of Kerberos (voor bevoegde toegang) en CHAP (voor versleuteling van referenties voor inbelmethoden) worden geïmplementeerd voor alle externe verbindingen met het netwerk van de organisatie.

- MFA moet zijn ingeschakeld voor accounts met eigenaarsmachtigingen voor uw abonnement
- MFA moet zijn ingeschakeld voor accounts met schrijfmachtigingen voor uw abonnement
- MFA moet zijn ingeschakeld voor accounts met leesmachtigingen voor uw abonnement
- Just-In-Time-netwerktoegangsbeheer moet worden toegepast op virtuele machines

## <a name="user-identification-and-authentication"></a>Identificatie en verificatie van gebruikers

Gebruikers die gemachtigde functies (zoals systeembeheer) hebben uitgevoerd, gebruiken afzonderlijke accounts bij het uitvoeren van deze geprivilegieerde functies. Methoden voor meervoudige verificatie worden gebruikt in overeenstemming met het organisatiebeleid (bijvoorbeeld voor externe netwerktoegang).

- MFA moet zijn ingeschakeld voor accounts met eigenaarsmachtigingen voor uw abonnement
- MFA moet zijn ingeschakeld voor accounts met schrijfmachtigingen voor uw abonnement
- MFA moet zijn ingeschakeld voor accounts met leesmachtigingen voor uw abonnement
- Er moeten maximaal drie eigenaren worden aangewezen voor uw abonnement
- Er moet meer dan één eigenaar zijn toegewezen aan uw abonnement
- Vereisten implementeren om Windows-VM's te controleren waarvoor de groep Administrators een van de opgegeven leden bevat
- Controleresultaten weergeven van Windows-VM's waarop de groep Administrators een van de opgegeven leden bevat
- Vereisten implementeren om virtuele Windows-machines te controleren waarop de groep Administrators niet alle opgegeven leden bevat
- Controleresultaten weergeven van virtuele Windows-machines waarop de groep Administrators niet alle opgegeven leden bevat
- Vereisten implementeren om Windows-VM's te controleren waarop de groep Administrators niet alleen de opgegeven leden bevat
- Controleresultaten weergeven van Windows-VM's waarop de groep Administrators niet alleen de opgegeven leden bevat

## <a name="privilege-management"></a>Machtigingsbeheer

Toegang tot beheerfuncties of beheerconsoles voor systemen die als host fungeren voor gevirtualiseerde systemen is beperkt tot personeel op basis van minimale bevoegdheden en wordt ondersteund door middel van technische controles.

- Just-In-Time-netwerktoegangsbeheer moet worden toegepast op virtuele machines
- Beheerpoorten moeten gesloten zijn op uw virtuele machines
- Er moeten maximaal drie eigenaren worden aangewezen voor uw abonnement
- Er moet meer dan één eigenaar zijn toegewezen aan uw abonnement
- Externe accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement
- Afgeschafte accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement
- Het gebruik van aangepaste RBAC-regels controleren
- Op rollen gebaseerd toegangsbeheer (RBAC) moet worden gebruikt voor Kubernetes Services

## <a name="review-of-user-access-rights"></a>Beoordeling van toegangsrechten voor gebruikers

De organisatie houdt een gedocumenteerde lijst bij met gemachtigde gebruikers van informatie-assets.

- Het gebruik van aangepaste RBAC-regels controleren

## <a name="remote-diagnostic-and-configuration-port-protection"></a>Beveiliging van externe diagnose en configuratiepoorten

Poorten, services en soort gelijke toepassingen die zijn geïnstalleerd op een computer of netwerksystemen die niet specifiek vereist zijn voor de bedrijfsfunctionaliteit, worden uitgeschakeld of verwijderd.

- Just-In-Time-netwerktoegangsbeheer moet worden toegepast op virtuele machines
- Beheerpoorten moeten gesloten zijn op uw virtuele machines
- Externe foutopsporing moet worden uitgeschakeld voor webtoepassingen
- Externe foutopsporing moet worden uitgeschakeld voor Function-apps
- Externe foutopsporing moet worden uitgeschakeld voor API-apps
- Besturingselementen voor adaptieve toepassingen om veilige toepassingen in de toegestane lijst op te nemen, moeten worden ingeschakeld op uw computers

## <a name="audit-logging"></a>Auditlogboekregistratie

Logboeken van verzonden en ontvangen berichten worden bewaard, inclusief de datum, de tijd, de oorsprong en de bestemming van het bericht, maar de inhoud ervan wordt niet bewaard. Controle is altijd beschikbaar wanneer het systeem actief is belangrijke gebeurtenissen bijhoudt, zoals geslaagde/mislukte gegevenstoegang, wijzigingen in de configuratie van de systeembeveiliging, gemachtigd gebruik of gebruik door hulpprogramma’s, opgetreden waarschuwingen, activering en deactivering van beveiligingssystemen (bijvoorbeeld A/V en IDS), activering en deactivering van identificatie- en verificatie mechanismen en het maken en verwijderen van objecten op systeemniveau.

- Diagnostische logboeken in Azure Data Lake Store moeten zijn ingeschakeld
- Diagnostische logboeken in Logic Apps moeten zijn ingeschakeld 
- Diagnostische logboeken in IoT Hub moeten zijn ingeschakeld 
- Diagnostische logboeken in Batch-accounts moeten worden ingeschakeld 
- Diagnostische logboeken in Microsoft Azure Virtual Machine Scale Sets moeten zijn ingeschakeld 
- Diagnostische logboeken in Event Hub moeten zijn ingeschakeld 
- Diagnostische logboeken in zoekservices moeten zijn ingeschakeld 
- Diagnostische logboeken in App Services moeten zijn ingeschakeld 
- Diagnostische logboeken in Data Lake Analytics moeten zijn ingeschakeld 
- Diagnostische logboeken in Key Vault moeten zijn ingeschakeld 
- Diagnostische logboeken in Service Bus moeten zijn ingeschakeld
- Diagnostische logboeken in Azure Stream Analytics moeten zijn ingeschakeld
- Controle op SQL-servers moet zijn ingeschakeld
- Diagnostische instelling voor controleren
- Azure Monitor moet activiteitenlogboeken uit alle regio's verzamelen

## <a name="monitoring-system-use"></a>Systeemgebruik bewaken

Geautomatiseerde systemen die in de organisatieomgeving worden geïmplementeerd, worden gebruikt voor het bewaken van belangrijke gebeurtenissen en afwijkende activiteiten, en het analyseren van systeemlogboeken, waarvan de resultaten regelmatig worden gecontroleerd. Bewaking omvat gemachtigde bewerkingen, geautoriseerde toegang of pogingen tot onbevoegde toegang, waaronder pogingen om toegang te krijgen tot gedeactiveerde accounts, en systeemwaarschuwingen of fouten.

- Azure Monitor moet activiteitenlogboeken uit alle regio's verzamelen
- De Log Analytics-agent moet worden geïnstalleerd op virtuele machines
- De Log Analytics-agent moet worden geïnstalleerd op virtuele-machineschaalsets
- \[Preview\]: Vereisten implementeren om Windows-VM's te controleren waarop de Log Analytics-agent niet is verbonden zoals verwacht
- \[Preview\]: Controleresultaten weergeven van Windows-VM's waarop de Log Analytics-agent niet is verbonden zoals verwacht
- Met het Azure Monitor-logboekprofiel moeten logboeken worden verzameld voor de categorieën schrijven, verwijderen en actie
- Automatisch inrichten van de Log Analytics-agent voor controle moet zijn ingeschakeld voor uw abonnement

## <a name="segregation-of-duties"></a>Scheiding van taken

Scheiding van taken wordt gebruikt om het risico op ongeoorloofde of onbedoelde wijziging van informatie en systemen te beperken. Geen enkele persoon kan gegevenssystemen openen, wijzigen of gebruiken zonder autorisatie of detectie. Toegang voor personen die verantwoordelijk zijn voor beheer en toegangsbeheer is beperkt tot het minimum dat nodig is op basis van de rol en verantwoordelijkheden van elke gebruiker. Deze personen hebben geen toegang tot controlefuncties die betrekking hebben op deze besturingselementen.

- Op rollen gebaseerd toegangsbeheer (RBAC) moet worden gebruikt voor Kubernetes Services
- Het gebruik van aangepaste RBAC-regels controleren
- \[Preview\]: Vereisten implementeren om configuraties voor Windows-VM's te controleren in ‘Toewijzing van gebruikersrechten’
- \[Preview\]: Controleresultaten van configuraties voor Windows-VM's weergeven in ‘Toewijzing van gebruikersrechten’
- \[Preview\]: Vereisten implementeren om configuraties voor Windows-VM's te controleren in ‘Beveiligingsopties - Gebruikersaccountbeheer’
- \[Preview\]: Controleresultaten van configuraties voor Windows-VM's weergeven in ‘Beveiligingsopties - Gebruikersaccountbeheer’
- Aangepaste rollen voor abonnementseigenaren mogen niet bestaan

## <a name="administrator-and-operator-logs"></a>Beheerders- en operatorlogboeken

De organisatie zorgt ervoor dat de juiste logboekregistratie is ingeschakeld om de beheerdersactiviteiten te controleren. en beoordeelt regelmatig de logboeken van systeembeheerders en operators.

- Er moet een waarschuwing voor activiteitenlogboeken bestaan voor specifieke beheerbewerkingen

## <a name="identification-of-risks-related-to-external-parties"></a>Identificatie van risico's met betrekking tot externe partijen

Externe toegangsverbindingen tussen de organisatie en externe partijen worden versleuteld

- Beveiligde overdracht naar opslagaccounts moet zijn ingeschakeld
- Functie-app mag alleen toegankelijk zijn via HTTPS
- Webtoepassing mag alleen toegankelijk zijn via HTTPS
- API-app mag alleen toegankelijk zijn via HTTPS
- SSL-verbinding afdwingen moet worden ingeschakeld voor MySQL-databaseservers
- SSL-verbinding afdwingen moet worden ingeschakeld voor PostgreSQL-databaseservers
- Alleen beveiligde verbindingen met uw Redis Cache moeten zijn ingeschakeld

## <a name="business-continuity-and-risk-assessment"></a>Bedrijfscontinuïteit en risicobeoordeling

De organisatie identificeert de essentiële bedrijfsprocessen en integreert de vereisten voor gegevensbeveiligingsbeheer voor bedrijfscontinuïteit met andere continuïteitsvereisten voor zaken als activiteiten, personeel, materialen, transport en faciliteiten.

- Virtuele machines controleren waarop geen noodherstel is geconfigureerd
- Key Vault-objecten moeten herstelbaar zijn
- \[Preview\]: Vereisten implementeren om configuraties voor Windows-VM's te controleren in ‘Beveiligingsopties - Herstelconsole’
- \[Preview\] Auditresultaten van configuraties voor Windows-VM's weergeven in ‘Beveiligingsopties - Herstelconsole’

## <a name="back-up"></a>Een back-up maken

Deze blauwdruk wijst Azure-beleidsdefinities toe waarmee de systeemback-upinformatie van de organisatie op de alternatieve opslagsite elektronisch kan worden gecontroleerd. Overweeg het gebruik van Azure Data Box voor fysieke verzending van opslagmetagegevens.

- Geografisch redundante back-up op de lange termijn moet zijn ingeschakeld voor Azure SQL Databases
- Geografisch redundante back-up moet zijn ingeschakeld voor Azure Database for MySQL
- Geografisch redundante back-up moet zijn ingeschakeld voor Azure Database for PostgreSQL
- Geografisch redundante back-up moet zijn ingeschakeld voor Azure Database for MariaDB
- Azure Backup moet zijn ingeschakeld voor virtuele machines

> [!NOTE]
> De beschikbaarheid van specifieke Azure Policy-definities kan verschillen in Azure Government en andere nationale clouds. 

## <a name="next-steps"></a>Volgende stappen

U hebt de toewijzing van besturingselementen van het HIPAA HITRUST-blauwdrukvoorbeeld gelezen. Lees nu de volgende artikelen voor meer informatie over het overzicht en het implementeren van dit voorbeeld:

> [!div class="next step action"]
> [HIPAA HITRUST-blauwdruk - Overzicht](./control-mapping.md)
> [HIPAA HITRUST-blauwdruk - Implementatiestappen](./deploy.md)

Aanvullende artikelen over blauwdrukken en het gebruik hiervan:

- Meer informatie over de [levenscyclus van een blauwdruk](../../concepts/lifecycle.md).
- Meer informatie over hoe u [statische en dynamische parameters](../../concepts/parameters.md) gebruikt.
- Meer informatie over hoe u de [blauwdrukvolgorde](../../concepts/sequencing-order.md) aanpast.
- Meer informatie over hoe u gebruikmaakt van [resourcevergrendeling in blauwdrukken](../../concepts/resource-locking.md).
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../../how-to/update-existing-assignments.md).
