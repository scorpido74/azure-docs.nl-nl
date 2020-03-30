---
title: Door de klant beheerde transparante gegevensversleuteling (TDE)
description: Breng BYOK-ondersteuning (Your Own Key) voor Transparante gegevensversleuteling (TDE) met Azure Key Vault voor SQL Database en Azure Synapse. TDE met BYOK overzicht, voordelen, hoe het werkt, overwegingen en aanbevelingen.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019, azure-synapse
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 03/18/2020
ms.openlocfilehash: 462326fb16663a6f25ff4b51ea11791201086fd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528725"
---
# <a name="azure-sql-transparent-data-encryption-with-customer-managed-key"></a>Azure SQL Transparent Data Encryption met door de klant beheerde sleutel

Azure SQL [Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) met door de klant beheerde sleutel maakt Bring Your Own Key (BYOK) scenario voor gegevensbescherming in rust mogelijk en stelt organisaties in staat om scheiding van taken te implementeren in het beheer van sleutels en gegevens. Met door de klant beheerde transparante gegevensversleuteling is de klant verantwoordelijk voor en in een volledige controle over een sleutellevenscyclusbeheer (sleutelcreatie, upload, rotatie, verwijdering), belangrijke gebruiksmachtigingen en controle van bewerkingen op sleutels.

In dit scenario is de sleutel die wordt gebruikt voor versleuteling van de Database Encryption Key (DEK), genaamd TDE protector, een door de klant beheerde asymmetrische sleutel die is opgeslagen in een azure [key vault (AKV) die eigendom](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)is van de klant en door de klant wordt beheerd, een cloudgebaseerd extern sleutelbeheersysteem. Key Vault is zeer beschikbaar en schaalbare beveiligde opslag voor RSA cryptografische sleutels, optioneel ondersteund door FIPS 140-2 Level 2 gevalideerde hardware beveiligingsmodules (HSM's). Het staat geen directe toegang tot een opgeslagen sleutel, maar biedt diensten van encryptie / decryptie met behulp van de sleutel tot de geautoriseerde entiteiten. De sleutel kan worden gegenereerd door de sleutelkluis, geïmporteerd of [overgebracht naar de sleutelkluis vanaf een on-prem HSM-apparaat.](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys)

Voor Azure SQL Database en Azure Synapse is de TDE-beveiliging ingesteld op het logische serverniveau en wordt deze overgenomen door alle versleutelde databases die aan die server zijn gekoppeld. Voor Azure SQL Managed Instance is de TDE-beveiliging ingesteld op instantieniveau en wordt deze overgenomen door alle versleutelde databases in die instantie. De term *server* verwijst zowel naar SQL Database logische server en beheerde instantie in dit document, tenzij anders vermeld.

> [!IMPORTANT]
> Voor degenen die gebruik maken van service-managed TDE die willen beginnen met het gebruik van door de klant beheerde TDE, gegevens blijven versleuteld tijdens het proces van overschakelen, en er is geen downtime, noch re-encryptie van de databasebestanden. Voor de overstap van een servicebeheersleutel naar een door de klant beheerde sleutel is alleen herversleuteling van de DEK vereist, wat een snelle en online bewerking is.

## <a name="benefits-of-the-customer-managed-tde"></a>Voordelen van de door de klant beheerde TDE

Door de klant beheerde TDE biedt de volgende voordelen voor de klant:

- Volledige en gedetailleerde controle over het gebruik en beheer van de TDE-beschermer;

- Transparantie van het gebruik van de TDE-beschermer;

- Vermogen om scheiding van taken te implementeren in het beheer van sleutels en gegevens binnen de organisatie;

- Key Vault-beheerder kan belangrijke toegangsmachtigingen intrekken om versleutelde database ontoegankelijk te maken;

- Centraal beheer van sleutels in AKV;

- Meer vertrouwen van uw eindklanten, omdat AKV zodanig is ontworpen dat Microsoft geen versleutelingssleutels kan zien of extraheren;

## <a name="how-customer-managed-tde-works"></a>Hoe klantbeheerde TDE werkt

![Installatie en werking van de door de klant beheerde TDE](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-roles.PNG)

Om de server te kunnen gebruiken TDE-beveiliging die is opgeslagen in AKV voor versleuteling van de DEK, moet de sleutelkluisbeheerder de volgende toegangsrechten aan de server geven met behulp van zijn unieke AAD-identiteit:

- **krijgen** - voor het ophalen van het openbare deel en de eigenschappen van de sleutel in de Key Vault

- **wrapKey** - om DEK te kunnen beschermen (versleutelen)

- **uitpakkenKey** - om de protectie (decoderen) DEK te kunnen ontsleutelen

Key vault administrator kan ook [het loggen van key vault audit-gebeurtenissen inschakelen,](https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault)zodat ze later kunnen worden gecontroleerd.

Wanneer de server is geconfigureerd om een TDE-beveiliging van AKV te gebruiken, stuurt de server de DEK van elke TDE-database naar de sleutelkluis voor versleuteling. Key vault retourneert de versleutelde DEK, die vervolgens wordt opgeslagen in de gebruikersdatabase.

Indien nodig stuurt de server beveiligde DEK naar de sleutelkluis voor decryptie.

Auditors kunnen Azure Monitor gebruiken om de logboeken van key vault AuditEvent te controleren als logboekregistratie is ingeschakeld.

[!INCLUDE [sql-database-akv-permission-delay](includes/sql-database-akv-permission-delay.md)]

## <a name="requirements-for-configuring-customer-managed-tde"></a>Vereisten voor het configureren van door de klant beheerde TDE

### <a name="requirements-for-configuring-akv"></a>Vereisten voor het configureren van AKV

- Sleutelkluis en SQL Database/beheerde instantie moeten tot dezelfde Azure Active Directory-tenant behoren. Cross-tenant key vault en server interacties worden niet ondersteund. Om middelen daarna te verplaatsen, zal TDE met AKV opnieuw moeten worden geconfigureerd. Meer informatie over [het verplaatsen van resources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).

- [Soft-delete-functie](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) moet zijn ingeschakeld op de sleutelkluis, om te beschermen tegen gegevensverlies accidentele sleutel (of key vault) verwijdering gebeurt. Uit soft-verwijderde bronnen worden gedurende 90 dagen bewaard, tenzij de klant in de tussentijd wordt hersteld of verwijderd. De *herstel-* en *zuiveringsacties* hebben hun eigen machtigingen die zijn gekoppeld aan een belangrijk toegangsbeleid voor kluizen. Soft-delete functie is standaard uitgeschakeld en kan worden ingeschakeld via [PowerShell](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell#enabling-soft-delete) of [CLI](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-cli#enabling-soft-delete). Het kan niet worden ingeschakeld via Azure portal.  

- Geef de SQL Database-server of beheerde instantie toegang tot de sleutelkluis (get, wrapKey, unwrapKey) met behulp van de Azure Active Directory-identiteit. Wanneer u Azure-portal gebruikt, wordt de Azure AD-identiteit automatisch gemaakt. Wanneer u PowerShell of CLI gebruikt, moet de Azure AD-identiteit expliciet worden gemaakt en moet de voltooiing worden geverifieerd. Zie [TDE configureren met BYOK](transparent-data-encryption-byok-azure-sql-configure.md) en [TDE configureren met BYOK voor beheerde instantie](https://aka.ms/sqlmibyoktdepowershell) voor gedetailleerde stapsgewijze instructies bij het gebruik van PowerShell.

- Wanneer u firewall met AKV gebruikt, moet u de optie *Vertrouwde Microsoft-services toestaan om de firewall te omzeilen*inschakelen.

### <a name="requirements-for-configuring-tde-protector"></a>Vereisten voor het configureren van TDE-beveiliging

- TDE beschermer kan alleen asymmetrisch, RSA 2048 of RSA HSM 2048 sleutel.

- De sleutelactiveringsdatum (indien ingesteld) moet een datum en tijd in het verleden zijn. Vervaldatum (indien ingesteld) moet een toekomstige datum en tijd zijn.

- De sleutel moet zich in de *status Ingeschakeld* hebben.

- Als u bestaande sleutel importeert in de sleutelkluis, moet u deze in de ondersteunde bestandsindelingen (.pfx, .byok of .backup) verstrekken.

## <a name="recommendations-when-configuring-customer-managed-tde"></a>Aanbevelingen bij het configureren van door de klant beheerde TDE

### <a name="recommendations-when-configuring-akv"></a>Aanbevelingen bij het configureren van AKV

- Koppel maximaal 500 General Purpose- of 200 Business Critical-databases in totaal aan een sleutelkluis in één abonnement om een hoge beschikbaarheid te garanderen wanneer de server toegang krijgt tot de TDE-beveiliging in de sleutelkluis. Deze cijfers zijn gebaseerd op de ervaring en gedocumenteerd in de [key vault service limits.](https://docs.microsoft.com/azure/key-vault/key-vault-service-limits) De bedoeling hier is om problemen te voorkomen na server failover, want het zal leiden tot zo veel belangrijke bewerkingen tegen de kluis als er databases in die server.

- Stel een bronvergrendeling in op de sleutelkluis om te bepalen wie deze kritieke bron kan verwijderen en onbedoelde of ongeautoriseerde verwijdering te voorkomen. Meer informatie over [resourcesloten](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources).

- Controle en rapportage van alle versleutelingssleutels inschakelen: Key vault biedt logboeken die eenvoudig te injecteren zijn in andere beveiligingsinformatie en hulpprogramma's voor gebeurtenisbeheer. Operations Management Suite [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault) is een voorbeeld van een service die al is geïntegreerd.

- Koppel elke server aan twee belangrijke kluizen die zich in verschillende regio's bevinden en hetzelfde sleutelmateriaal bevatten, om een hoge beschikbaarheid van versleutelde databases te garanderen. Markeer alleen de sleutel uit de sleutelkluis in dezelfde regio als een TDE-beschermer. Het systeem schakelt automatisch over naar de sleutelkluis in het externe gebied als er een storing is die de sleutelkluis in dezelfde regio beïnvloedt.

### <a name="recommendations-when-configuring-tde-protector"></a>Aanbevelingen bij het configureren van TDE-beveiliging
- Bewaar een kopie van de TDE-beschermer op een veilige plaats of borg het aan de escrow-service.

- Als de sleutel wordt gegenereerd in de sleutelkluis, maakt u een sleutelback-up voordat u de sleutel voor de eerste keer in AKV gebruikt. Back-up kan alleen worden hersteld naar een Azure Key Vault. Meer informatie over de opdracht [Back-up-AzKeyVaultKey.](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey)

- Maak een nieuwe back-up wanneer er wijzigingen worden aangebracht in de sleutel (bijvoorbeeld belangrijke kenmerken, tags, ACL's).

- **Bewaar eerdere versies** van de sleutel in de sleutelkluis bij het roteren van toetsen, zodat oudere databaseback-ups kunnen worden hersteld. Wanneer de TDE-beveiliging is gewijzigd voor een database, worden oude back-ups van de database **niet bijgewerkt** om de nieuwste TDE-protector te gebruiken. Bij hersteltijd heeft elke back-up de TDE-beveiliging nodig waarmee deze tijdens het maken is versleuteld. Sleutelrotaties kunnen worden uitgevoerd volgens de instructies bij [Roteren van de Transparent Data Encryption Protector met PowerShell.](transparent-data-encryption-byok-azure-sql-key-rotation.md)

- Bewaar alle eerder gebruikte toetsen in AKV, zelfs na het overschakelen naar servicebeheerde sleutels. Het zorgt ervoor dat database back-ups kunnen worden hersteld met de TDE-beschermers die zijn opgeslagen in AKV.  TDE-beschermers die met Azure Key Vault zijn gemaakt, moeten worden onderhouden totdat alle resterende opgeslagen back-ups zijn gemaakt met servicebeheersleutels. Maak herstelbare back-upkopieën van deze sleutels met [back-up-AzKeyVaultKey.](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey)

- Als u een mogelijk gecompromitteerde sleutel tijdens een beveiligingsincident wilt verwijderen zonder het risico van gegevensverlies, volgt u de stappen uit de [mogelijk gecompromitteerde sleutel verwijderen.](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)

## <a name="inaccessible-tde-protector"></a>Ontoegankelijke TDE-beschermer

Wanneer transparante gegevensversleuteling is geconfigureerd om een door de klant beheerde sleutel te gebruiken, is continue toegang tot de TDE-beveiliging vereist om de database online te laten blijven. Als de server geen toegang heeft tot de door de klant beheerde TDE-protector in AKV, wordt in maximaal 10 minuten een database alle verbindingen met het bijbehorende foutbericht verwijderd en verandert u de status ervan in *Ontoegankelijk*. De enige actie die is toegestaan in een database in de status Ontoegankelijk, is deze verwijderen.

> [!NOTE]
> Als de database niet toegankelijk is vanwege een onderbroken netwerkstoring, is er geen actie nodig en komen de databases automatisch weer online.

Nadat de toegang tot de sleutel is hersteld, vereist het weer online nemen van de database extra tijd en stappen, die kunnen variëren op basis van de verstreken tijd zonder toegang tot de sleutel en de grootte van de gegevens in de database:

- Als de toegang tot de sleutel binnen 8 uur wordt hersteld, wordt de database binnen het volgende uur automatisch hersteld.

- Als de toegang tot de sleutel na meer dan 8 uur wordt hersteld, is automatisch genezen niet mogelijk en het terugbrengen van de database vereist extra stappen op de portal en kan een aanzienlijke hoeveelheid tijd in beslag nemen, afhankelijk van de grootte van de database. Zodra de database weer online is, gaan eerder geconfigureerde instellingen op serverniveau, zoals [failovergroepconfiguratie,](https://docs.microsoft.com/azure/sql-database/sql-database-auto-failover-group) point-in-time-restore-geschiedenis en tags **verloren.** Daarom wordt aanbevolen een meldingssysteem te implementeren waarmee u de onderliggende belangrijke toegangsproblemen binnen 8 uur identificeren en aanpakken.

### <a name="accidental-tde-protector-access-revocation"></a>Toevallige TDE-beschermer toegang intrekking

Het kan voorkomen dat iemand met voldoende toegangsrechten tot de sleutelkluis per ongeluk de toegang tot de sleutel van de server uitschakelt door:

- intrekking van key vault's *get*, *wrapKey,* *unwrapKey-machtigingen* van de server

- de sleutel verwijderen

- de sleutelkluis verwijderen

- de firewallregels van key vault wijzigen

- de beheerde identiteit van de server verwijderen in Azure Active Directory

Meer informatie over [de algemene oorzaken voor het ontoegankelijk worden van de database](https://docs.microsoft.com/sql/relational-databases/security/encryption/troubleshoot-tde?view=azuresqldb-current#common-errors-causing-databases-to-become-inaccessible).

## <a name="monitoring-of-the-customer-managed-tde"></a>Monitoring van de door de klant beheerde TDE

Als u de status van de database wilt controleren en waarschuwingen voor verlies van TDE-beveiligingtoegang wilt inschakelen, configureert u de volgende Azure-functies:
- [Azure-bronstatus](https://docs.microsoft.com/azure/service-health/resource-health-overview). Een ontoegankelijke database die geen toegang meer heeft tot de TDE-protector wordt weergegeven als 'Niet beschikbaar' nadat de eerste verbinding met de database is geweigerd.
- [Activiteitslogboek](https://docs.microsoft.com/azure/service-health/alerts-activity-log-service-notifications) wanneer de toegang tot de TDE-beveiliging in de door de klant beheerde sleutelkluis mislukt, worden items toegevoegd aan het activiteitenlogboek.  Als u waarschuwingen voor deze gebeurtenissen maakt, u de toegang zo snel mogelijk herstellen.
- [Actiegroepen](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) kunnen worden gedefinieerd om u meldingen en waarschuwingen te sturen op basis van uw voorkeuren, zoals E-mail/SMS/Push/Voice, Logic App, Webhook, ITSM of Automation Runbook.

## <a name="database-backup-and-restore-with-customer-managed-tde"></a>Databaseback-up en herstel met door de klant beheerde TDE

Zodra een database is versleuteld met TDE met behulp van een sleutel van Key Vault, worden nieuw gegenereerde back-ups ook versleuteld met dezelfde TDE-beveiliging. Wanneer de TDE-beveiliging wordt gewijzigd, **worden** oude back-ups van de database niet bijgewerkt om de nieuwste TDE-protector te gebruiken.

Als u een back-up wilt herstellen die is versleuteld met een TDE-beveiliging van Key Vault, moet u ervoor zorgen dat het belangrijkste materiaal beschikbaar is voor de doelserver. Daarom raden we u aan om alle oude versies van de TDE-beveiliging in de sleutelkluis te bewaren, zodat databaseback-ups kunnen worden hersteld.

> [!IMPORTANT]
> Op elk moment kan er niet meer dan een TDE-beveiliging ingesteld voor een server. Het is de sleutel gemarkeerd met "Maak de sleutel de standaard TDE-beveiliging" in het Azure-portalblad. Er kunnen echter meerdere extra sleutels aan een server worden gekoppeld zonder ze te markeren als een TDE-beschermer. Deze sleutels worden niet gebruikt voor het beschermen van DEK, maar kunnen worden gebruikt tijdens het herstellen van een back-up, als back-upbestand is versleuteld met de sleutel met de bijbehorende duimafdruk.

Als de sleutel die nodig is voor het herstellen van een back-up niet meer beschikbaar is `<Servername>` voor de doelserver, wordt het \<volgende foutbericht \<geretourneerd bij het herstellen: "Doelserver heeft geen toegang tot alle AKV-URI's die zijn gemaakt tussen Timestamp #1> en Timestamp #2>. Probeer de bewerking opnieuw uit na het herstellen van alle AKV-URI's."

Voer de [cmdlet Get-AzSqlServerKeyKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) voor de logische server van doelSQL Database of [Get-AzSqlInstanceKeyVaultKey](/powershell/module/az.sql/get-azsqlinstancekeyvaultkey) voor doelbeheerde instantie uit om de lijst met beschikbare sleutels terug te sturen en de ontbrekende sleutels te identificeren. Om ervoor te zorgen dat alle back-ups kunnen worden hersteld, moet u ervoor zorgen dat de doelserver voor het herstel toegang heeft tot alle sleutels die nodig zijn. Deze toetsen hoeven niet te worden gemarkeerd als TDE-beschermer.

Zie Een Azure SQL-database herstellen voor meer informatie over back-upherstel voor [SQL-database.](sql-database-recovery-using-backups.md) Zie Een SQL Pool herstellen voor meer informatie over back-upherstel voor SQL [Pool.](../synapse-analytics/sql-data-warehouse/backup-and-restore.md) Zie [Snelstart: Een database herstellen naar een beheerde instantie voor](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) native back-up/herstel van SQL Server met beheerde instantie

Extra aandacht voor logbestanden: Back-up log bestanden blijven versleuteld met de originele TDE-beschermer, zelfs als het werd gedraaid en de database is nu met behulp van een nieuwe TDE-beschermer.  Op hersteltijd zijn beide sleutels nodig om de database te herstellen.  Als het logboekbestand een TDE-beveiliging gebruikt die is opgeslagen in Azure Key Vault, is deze sleutel nodig tijdens het herstellen, zelfs als de database is gewijzigd om servicebeheerde TDE in de tussentijd te gebruiken.

## <a name="high-availability-with-customer-managed-tde"></a>Hoge beschikbaarheid met door de klant beheerde TDE

Zelfs in gevallen waarin er geen geconfigureerde georedundantie voor de server is, is het ten zeerste aan te raden om de server te configureren om twee verschillende sleutelkluizen te gebruiken in twee verschillende regio's met hetzelfde sleutelmateriaal. Dit kan worden bereikt door een TDE-protector te maken met behulp van de primaire sleutelkluis die zich in dezelfde regio als de server bevindt en de sleutel in een sleutelkluis in een andere Azure-regio kloont, zodat de server toegang heeft tot een tweede sleutelkluis als de primaire sleutelkluis moet worden een storing ervaren terwijl de database actief is.

Gebruik de cmdlet Backup-AzKeyVaultKey Om de sleutel in versleutelde indeling uit de primaire sleutelkluis op te halen en gebruik vervolgens de cmdlet Restore-AzKeyVaultKeyKey Te gebruiken en een sleutelkluis in het tweede gebied op te geven om de sleutel te klonen. U ook azure-portal gebruiken om de sleutel te back-upten en te herstellen. De sleutel in de secundaire sleutel kluis in se andere regio mag niet worden gemarkeerd als TDE beschermer, en het is niet eens toegestaan.

 Als er een storing is die van invloed is op de primaire sleutelkluis, en alleen dan schakelt het systeem automatisch over naar de andere gekoppelde sleutel met dezelfde duimafdruk in de secundaire sleutelkluis, als deze bestaat. Houd er echter rekening mee dat deze switch niet plaatsvindt als TDE-beveiliging niet toegankelijk is vanwege ingetrokken toegangsrechten of omdat sleutel- of sleutelkluis is verwijderd, omdat dit erop kan wijzen dat de klant opzettelijk de toegang van de server wilde beperken om toegang te krijgen tot de sleutel.

![HA met één server](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-ha.png)

## <a name="geo-dr-and-customer-managed-tde"></a>Geo-DR en door de klant beheerde TDE

In zowel [actieve scenario's voor georeplicatie-](https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication) als [failovergroepen](https://docs.microsoft.com/azure/sql-database/sql-database-auto-failover-group) vereist elke betrokken server een afzonderlijke sleutelkluis, die moet worden gekoppeld aan de server in hetzelfde Azure-gebied. De klant is verantwoordelijk voor het consistent houden van het belangrijkste materiaal in de sleutelkluizen, zodat geo-secundaire gesynchroniseerd is en dezelfde sleutel kan overnemen uit de lokale sleutelkluis als primaire ontoegankelijk wordt als gevolg van een storing in de regio en een failover wordt geactiveerd . Maximaal vier secondaries kunnen worden geconfigureerd en chaining (secondaries van secondaries) wordt niet ondersteund.

Om problemen tijdens het instellen of tijdens georeplicatie te voorkomen vanwege onvolledig sleutelmateriaal, is het belangrijk om deze regels te volgen bij het configureren van door de klant beheerde TDE:

- Alle betrokken sleutelkluizen moeten dezelfde eigenschappen en dezelfde toegangsrechten hebben voor de betreffende servers.

- Alle betrokken sleutelkluizen moeten identiek sleutelmateriaal bevatten. Het is niet alleen van toepassing op de huidige TDE-beschermer, maar op de alle vorige TDE-beschermers die kunnen worden gebruikt in de back-upbestanden.

- Zowel de initiële installatie als de rotatie van de TDE-beschermer moeten eerst op de secundaire en vervolgens op de primaire.

![Failovergroepen en geo-dr](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-bcdr.png)

Als u een failover wilt testen, voert u de stappen in [het overzicht actieve georeplicatie](sql-database-geo-replication-overview.md). Het moet worden gedaan op een regelmatige basis om te bevestigen dat de toegangsmachtigingen voor SQL naar beide belangrijke kluizen zijn gehandhaafd.

## <a name="next-steps"></a>Volgende stappen

U ook de volgende PowerShell-voorbeeldscripts controleren voor de algemene bewerkingen met door de klant beheerde TDE:

- [De transparent data encryption protector voor SQL Database roteren met PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md)

- [Een TDE-beveiliging (Transparent Data Encryption) verwijderen voor SQL Database met PowerShell](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-remove-tde-protector)

- [Transparante gegevensversleuteling beheren in een beheerde instantie met uw eigen sleutel met PowerShell](https://docs.microsoft.com/azure/sql-database/scripts/transparent-data-encryption-byok-sql-managed-instance-powershell?toc=%2fpowershell%2fmodule%2ftoc.json)
