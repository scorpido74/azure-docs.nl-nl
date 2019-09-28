---
title: TDE-Azure Key Vault Integration of Bring Your Own Key (BYOK)-Azure SQL Database | Microsoft Docs
description: Bring Your Own Key-ondersteuning (BYOK) voor Transparent Data Encryption (TDE) met Azure Key Vault voor SQL Database en Data Warehouse. TDE met BYOK overview, voor delen, hoe het werkt, overwegingen en aanbevelingen.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
ms.date: 07/18/2019
ms.openlocfilehash: 095ecc360e5639a5d47dff4bc4675fc237cf81da
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348917"
---
# <a name="azure-sql-transparent-data-encryption-with-customer-managed-keys-in-azure-key-vault-bring-your-own-key-support"></a>Azure SQL Transparent Data Encryption met door de klant beheerde sleutels in Azure Key Vault: Ondersteuning voor Bring Your Own Key

[Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) met Azure Key Vault-integratie kunt u de database versleutelings sleutel (dek) versleutelen met een door de klant beheerde asymmetrische sleutel met de naam TDe Protector. Dit wordt ook in het algemeen aangeduid als Bring Your Own Key-ondersteuning (BYOK) voor Transparent Data Encryption.  In het BYOK-scenario wordt de TDE-Protector opgeslagen in een door de klant eigendom en beheerd [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault), het op de cloud gebaseerde externe sleutel beheer systeem van Azure. De TDE-Protector kan worden [gegenereerd](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates) door de sleutel kluis of worden [overgebracht](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys) naar de sleutel kluis van een on-premises HSM-apparaat. De TDE-DEK, die is opgeslagen op de opstart pagina van een Data Base, wordt versleuteld en ontsleuteld door de TDE-Protector die is opgeslagen in Azure Key Vault, die er nooit in wordt gelaten.  Aan SQL Database moeten machtigingen worden verleend om de DEK te ontsleutelen en te versleutelen. Als de machtigingen van de logische SQL-Server aan de sleutel kluis worden ingetrokken, is een Data Base niet toegankelijk, worden de verbindingen geweigerd en worden alle gegevens versleuteld. Voor Azure SQL Database wordt de TDE-Protector ingesteld op het niveau van de logische SQL-Server en overgenomen door alle data bases die aan die server zijn gekoppeld. Voor [Azure SQL Managed instance](https://docs.microsoft.com/azure/sql-database/sql-database-howto-managed-instance)wordt de TDe-Protector ingesteld op het niveau van de instantie en wordt deze overgenomen door alle versleutelde data bases op die instantie. De term *Server* verwijst naar de server en het exemplaar in dit document, tenzij anders aangegeven.

> [!NOTE]
> Transparent Data Encryption met Azure Key Vault-integratie (Bring Your Own Key) voor Azure SQL Database beheerde instantie is in preview.


Met TDE met Azure Key Vault-integratie kunnen gebruikers belang rijke beheer taken beheren, zoals het draaien van sleutels, sleutel kluis machtigingen, sleutel back-ups en het inschakelen van controle/rapportage op alle TDE-beveiligingen met behulp van Azure Key Vault functionaliteit. Key Vault biedt centraal sleutel beheer, maakt gebruik van nauw bewakende Hsm's (Hardware Security modules) en stelt schei ding van taken mogelijk te maken tussen het beheer van sleutels en gegevens om te voldoen aan de naleving van beveiligings beleid.  

TDE met Azure Key Vault-integratie biedt de volgende voor delen:

- Verbeterde transparantie en nauw keurigheid van besturings elementen met de mogelijkheid om zelf de TDE-Protector te beheren
- De mogelijkheid om machtigingen op elk gewenst moment in te trekken om de data base niet toegankelijk te maken
- Centraal beheer van TDE-beveiligingen (samen met andere sleutels en geheimen die worden gebruikt in andere Azure-Services) door ze te hosten in Key Vault
- Schei ding van de verantwoordelijkheden voor sleutel-en gegevens beheer binnen de organisatie om de schei ding van taken te ondersteunen
- Meer vertrouwen van uw eigen clients, omdat Key Vault is ontworpen, zodat micro soft geen versleutelings sleutels kan zien of extra heren.
- Ondersteuning voor het draaien van sleutels

> [!IMPORTANT]
> Voor degenen die gebruikmaken van service-beheerde TDE die willen beginnen met het gebruik van Key Vault, blijft TDE ingeschakeld tijdens het overschakelen naar een TDE-Protector in Key Vault. Er is geen downtime of hercodering van de database bestanden. Als u overschakelt van een door een service beheerde sleutel naar een Key Vault sleutel, moet de database versleutelings sleutel (DEK) opnieuw worden versleuteld, wat een snelle en online bewerking is.

## <a name="how-does-tde-with-azure-key-vault-integration-support-work"></a>Hoe werkt TDE met Azure Key Vault Integration-ondersteunings werk

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> De Power shell-Azure Resource Manager module wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkeling is voor de module AZ. SQL. Zie [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)voor deze cmdlets. De argumenten voor de opdrachten in de module AZ en in de AzureRm-modules zijn aanzienlijk identiek.

![Verificatie van de server naar de Key Vault](./media/transparent-data-encryption-byok-azure-sql/tde-byok-server-authentication-flow.PNG)

Wanneer TDE voor het eerst is geconfigureerd voor het gebruik van een TDE-Protector van Key Vault, verzendt de server de DEK van elke TDE-Data Base naar Key Vault voor een aanvraag voor de terugloop sleutel. Key Vault retourneert de versleutelde database versleuteling sleutel, die wordt opgeslagen in de gebruikers database.  

> [!IMPORTANT]
> Het is belang rijk te weten dat **zodra een TDe-Protector is opgeslagen in azure Key Vault, de Azure Key Vault nooit wordt verlaten**. De server kan alleen aanvragen voor de sleutel bewerking verzenden naar het TDE-sleutel materiaal in Key Vault en **nooit toegang hebben tot de TDe-Protector of deze in de cache opslaat**. De Key Vault-beheerder heeft het recht om Key Vault machtigingen van de server op elk moment in te trekken, in welk geval alle verbindingen met de Data Base worden geweigerd.

## <a name="guidelines-for-configuring-tde-with-azure-key-vault"></a>Richt lijnen voor het configureren van TDE met Azure Key Vault

### <a name="general-guidelines"></a>Algemene richtlijnen

- Zorg ervoor dat Azure Key Vault en het Azure SQL Database/beheerde exemplaar zich in dezelfde Tenant bevinden.  De cross-Tenant sleutel kluis en server interacties **worden niet ondersteund**.
- Als u een Tenant wilt verplaatsen, moet u TDE met Azure opnieuw configureren, meer informatie over het [verplaatsen van resources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).
- Bij het configureren van TDE met Azure Key Vault is het belang rijk dat u de belasting op de sleutel kluis beoordeelt door herhaalde of onverpakte bewerkingen. Omdat alle data bases die zijn gekoppeld aan een SQL Database Server bijvoorbeeld dezelfde TDE-Protector gebruiken, wordt een failover van die server geactiveerd als veel belang rijke bewerkingen voor de kluis als er data bases op de server zijn. Op basis van onze ervaring en gedocumenteerde [sleutel kluis-service limieten](https://docs.microsoft.com/azure/key-vault/key-vault-service-limits)wordt u aangeraden maxi maal 500 standaard/Algemeen of 200 Premium/bedrijfskritiek data bases te koppelen aan één Azure Key Vault in één abonnement om te zorgen voor consistent hoge Beschik baarheid bij toegang tot de TDE-Protector in de kluis.
- Aanbevolen: Bewaar een kopie van de TDE-Protector on-premises.  Hiervoor is een HSM-apparaat vereist voor het lokaal maken van een TDE-Protector en een belang rijk borg systeem voor het opslaan van een lokale kopie van de TDE-Protector.  Meer informatie [over het overdragen van een sleutel van een lokale HSM naar Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys).


### <a name="guidelines-for-configuring-azure-key-vault"></a>Richt lijnen voor het configureren van Azure Key Vault

- Een sleutel kluis maken met [zacht verwijderen](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) en de beveiliging opschonen ingeschakeld om te beschermen tegen gegevens verlies in het geval van een onbedoelde sleutel – of sleutel kluis-verwijderen.  U moet [Power shell gebruiken om de eigenschap "zacht-delete" in te scha kelen](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell) voor de sleutel kluis (deze optie is nog niet beschikbaar via de Azure-Portal, maar vereist voor Azure SQL):  
  - Voorlopig verwijderde resources worden bewaard gedurende een bepaalde periode, 90 dagen, tenzij ze worden hersteld of verwijderd.
  - De **herstel** - en opschoon acties hebben hun eigen machtigingen die zijn gekoppeld aan een sleutel kluis toegangs beleid.
- Stel een resource vergrendeling in voor de sleutel kluis om te bepalen wie deze kritieke resource kan verwijderen en om onbedoelde of ongeoorloofde verwijdering te voor komen.  [Meer informatie over resource vergrendelingen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources)

- Verleen de SQL Database Server toegang tot de sleutel kluis met behulp van de identiteit van de Azure Active Directory (Azure AD).  Wanneer u de portal-gebruikers interface gebruikt, wordt de Azure AD-identiteit automatisch gemaakt en worden de toegangs machtigingen voor de sleutel kluis verleend aan de server.  Als u Power shell gebruikt om TDE te configureren met BYOK, moet de Azure AD-identiteit worden gemaakt en voltooid. Zie [Configure TDe with BYOK](transparent-data-encryption-byok-azure-sql-configure.md) en [Configure TDe with BYOK for Managed instance](https://aka.ms/sqlmibyoktdepowershell) voor gedetailleerde stapsgewijze instructies voor het gebruik van Power shell.

   > [!NOTE]
   > Als de Azure AD-identiteit **per ongeluk wordt verwijderd of de machtigingen van de server worden ingetrokken** met het toegangs beleid van de sleutel kluis of per ongeluk door de server naar een andere Tenant te verplaatsen, verliest de server de toegang tot de sleutel kluis en TDe versleutelde data bases is niet toegankelijk en de aanmeldingen worden geweigerd totdat de Azure AD-identiteit en-machtigingen van de logische server zijn hersteld.  

- Wanneer u firewalls en virtuele netwerken met Azure Key Vault gebruikt, moet u vertrouwde micro soft-Services toestaan deze firewall over te slaan. Kies Ja.

   > [!NOTE]
   > Als TDE versleutelde SQL-data bases geen toegang meer hebben tot de sleutel kluis, omdat de firewall niet kan worden omzeild, zijn de data bases niet toegankelijk en worden aanmeldingen geweigerd totdat de firewall bypass-machtigingen zijn hersteld.

- Controle en rapportage inschakelen voor alle versleutelings sleutels: Key Vault biedt logboeken die eenvoudig kunnen worden geïnjecteerd in andere hulpprogram ma's voor SIEM (Security Information and Event Management). [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault) van operations management suite (OMS) is een voor beeld van een service die al is geïntegreerd.
- Om te zorgen voor een hoge Beschik baarheid van versleutelde data bases, configureert u elke SQL Database-Server met twee Azure-sleutel kluizen die zich in verschillende regio's bevinden.


### <a name="guidelines-for-configuring-the-tde-protector-asymmetric-key"></a>Richt lijnen voor het configureren van de TDE-Protector (asymmetrische sleutel)

- Maak uw versleutelings sleutel lokaal op een lokaal HSM-apparaat. Zorg ervoor dat dit een asymmetrische, RSA 2048-of RSA HSM 2048-sleutel is, zodat deze storable is in Azure Key Vault.
- Borg de sleutel in een belang rijk borg systeem.  
- Importeer het bestand met de versleutelings sleutel (. pfx,. byok of. back-up) naar Azure Key Vault.

   > [!NOTE]
   > Voor test doeleinden is het mogelijk om een sleutel met Azure Key Vault te maken, maar deze sleutel kan niet worden geborgd, omdat de persoonlijke sleutel de sleutel kluis nooit kan verlaten.  Altijd een back-up maken en borg sleutels die worden gebruikt voor het versleutelen van productie gegevens, als het verlies van de sleutel (per ongeluk verwijderen in sleutel kluis, verval datum enz.) resulteert in permanent gegevens verlies.

- Als u een sleutel met een verval datum gebruikt: Implementeer een systeem voor een verloop waarschuwing om de sleutel te roteren voordat deze verloopt: **zodra de sleutel is verlopen, verliest de versleutelde data base toegang tot hun TDe-Protector en is deze niet toegankelijk** en worden alle aanmeldingen geweigerd totdat de sleutel is gedraaid naar een nieuwe sleutel en geselecteerd als de nieuwe sleutel en de standaard TDE-Protector voor de logische SQL-Server.
- Zorg ervoor dat de sleutel is ingeschakeld en dat u beschikt over de juiste machtigingen om de *Get*-, *Terugloop*-en uitstel *sleutel* bewerkingen uit te voeren.
- Maak een back-up van de Azure Key Vault sleutel voordat u de sleutel in Azure Key Vault voor de eerste keer gebruikt. Meer informatie over de [back-up-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey) -opdracht.
- Maak een nieuwe back-up wanneer er wijzigingen worden aangebracht aan de sleutel (bijvoorbeeld Acl's toevoegen, labels toevoegen, sleutel kenmerken toevoegen).
- **Bewaar vorige versies** van de sleutel in de sleutel kluis bij het draaien van sleutels, zodat oudere database back-ups kunnen worden hersteld. Wanneer de TDE-Protector voor een Data Base wordt gewijzigd, worden de oude back-ups van de Data Base **niet bijgewerkt** voor gebruik van de meest recente TDe-Protector.  Voor elke back-up is de TDE-Protector vereist die op het moment van terugzetten is gemaakt. U kunt de volgende stappen uitvoeren om de [transparent Data Encryption beveiliging te draaien met behulp van Power shell](transparent-data-encryption-byok-azure-sql-key-rotation.md).
- Bewaar alle eerder gebruikte sleutels in Azure Key Vault nadat u de sleutels hebt gewijzigd in een service die wordt beheerd.  Dit zorgt ervoor dat database back-ups kunnen worden hersteld met de TDE-beveiligingen die zijn opgeslagen in Azure Key Vault.  TDE-beveiligingen die zijn gemaakt met Azure Key Vault moeten worden behouden totdat alle opgeslagen back-ups zijn gemaakt met door service beheerde sleutels.  
- Herstel bare back-ups van deze sleutels maken met [Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey).
- Als u een mogelijk gemanipuleerde sleutel tijdens een beveiligings incident wilt verwijderen zonder het risico van gegevens verlies, volgt u de stappen in [een mogelijk beschadigde sleutel verwijderen](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md).

### <a name="guidelines-for-monitoring-the-tde-with-azure-key-vault-configuration"></a>Richt lijnen voor het bewaken van de TDE met Azure Key Vault configuratie

Als de logische SQL-Server de toegang tot de door de klant beheerde TDE-Protector in Azure Key Vault kwijtraakt, worden alle verbindingen door de data base geweigerd en worden ze niet meer weer gegeven in de Azure Portal.  De meest voorkomende oorzaken hiervoor zijn:
- Sleutel kluis per ongeluk of achter een firewall verwijderd
- Sleutel kluis sleutel per ongeluk verwijderd, uitgeschakeld of verlopen
- De logische SQL Server exemplaar-AppId per ongeluk verwijderd
- Belangrijkste specifieke machtigingen voor de AppId van het logische SQL Server exemplaar zijn ingetrokken

 > [!NOTE]
 > De data base wordt zelf hersteld en wordt automatisch online weer geven als de toegang tot de door de klant beheerde TDE-Protector binnen 48 uur opnieuw wordt geactiveerd.  Als de data base niet toegankelijk is vanwege een onregelmatige netwerk storing, is er geen actie vereist en worden de data bases automatisch weer online gezet.
  
- Zie [problemen oplossen met TDe](https://docs.microsoft.com/sql/relational-databases/security/encryption/troubleshoot-tde) voor meer informatie over het oplossen van bestaande configuraties

- Als u de status van de Data Base wilt bewaken en waarschuwingen wilt inschakelen voor het verlies van toegang tot TDE-Protector, configureert u de volgende Azure-functies:
    - [Azure resource Health](https://docs.microsoft.com/azure/service-health/resource-health-overview). Een ontoegankelijke data base die de toegang tot de TDE-Protector heeft verloren, wordt weer gegeven als ' niet beschikbaar ' nadat de eerste verbinding met de data base is geweigerd.
    - [Activiteiten logboek](https://docs.microsoft.com/azure/service-health/alerts-activity-log-service-notifications) wanneer toegang tot de TDe-Protector in de door de klant beheerde sleutel kluis mislukt, worden de vermeldingen toegevoegd aan het activiteiten logboek.  Als u waarschuwingen voor deze gebeurtenissen maakt, kunt u zo snel mogelijk toegang herstellen.
    - [Actie groepen](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) kunnen worden gedefinieerd om u meldingen en waarschuwingen te sturen op basis van uw voor keuren, bijvoorbeeld E-mail/SMS/push/Voice, Logic app, webhook, ITSM of Automation Runbook.
    

## <a name="high-availability-geo-replication-and-backup--restore"></a>Hoge Beschik baarheid, geo-replicatie en back-up/herstellen

### <a name="high-availability-and-disaster-recovery"></a>Hoge beschikbaarheid en herstel na noodgevallen

Het configureren van hoge Beschik baarheid met Azure Key Vault is afhankelijk van de configuratie van uw data base en SQL Database-Server, en hier zijn de aanbevolen configuraties voor twee verschillende gevallen.  De eerste draagtas is een zelfstandige Data Base of SQL Database Server zonder geconfigureerde geo-redundantie.  Het tweede geval is een Data Base of SQL Database Server die is geconfigureerd met failover-groepen of geo-redundantie, waarbij moet worden gegarandeerd dat elke geo-redundante kopie een lokale Azure Key Vault in de failovergroep heeft om ervoor te zorgen dat geo-failovers werken.

Als u in het eerste geval hoge Beschik baarheid nodig hebt van een Data Base en SQL Database Server zonder geconfigureerde geo-redundantie, wordt u ten zeerste aangeraden de server te configureren voor het gebruik van twee verschillende sleutel kluizen in twee verschillende regio's met hetzelfde sleutel materiaal. Dit kan worden bereikt door een TDE-Protector te maken met behulp van de primaire Key Vault die zich in dezelfde regio bevindt als de SQL Database-Server en de sleutel te klonen in een sleutel kluis in een andere Azure-regio, zodat de server toegang heeft tot een tweede sleutel kluis als de primaire de sleutel kluis ervaart een storing terwijl de data base actief is. Gebruik de cmdlet backup-AzKeyVaultKey om de sleutel in een versleutelde indeling op te halen uit de primaire sleutel kluis en gebruik vervolgens de cmdlet Restore-AzKeyVaultKey en geef een sleutel kluis op in de tweede regio.

![HA met één server en geen geo-Dr](./media/transparent-data-encryption-byok-azure-sql/SingleServer_HA_Config.PNG)

## <a name="how-to-configure-geo-dr-with-azure-key-vault"></a>Geo-DR configureren met Azure Key Vault

Om hoge Beschik baarheid van TDE-beveiligingen voor versleutelde data bases te behouden, is het vereist dat er redundante Azure-sleutel kluizen worden geconfigureerd op basis van de bestaande of gewenste SQL Database failover-groepen of actieve geo-replicatie-exemplaren.  Voor elke geo-gerepliceerde server is een afzonderlijke sleutel kluis vereist. deze moet zich op dezelfde locatie bevinden als de server in dezelfde Azure-regio. Als een primaire data base ontoegankelijk wordt als gevolg van een storing in één regio en er een failover wordt geactiveerd, kan de secundaire data base gebruikmaken van de secundaire sleutel kluis.

Voor geo-gerepliceerde Azure SQL-data bases is de volgende Azure Key Vault configuratie vereist:

- Eén primaire data base met een sleutel kluis in de regio en een secundaire data base met een sleutel kluis in de regio.
- Ten minste één secundair is vereist, Maxi maal vier secundairen worden ondersteund.
- De secundaire zones van de secundaire zones (koppelen) worden niet ondersteund.

In de volgende sectie vindt u meer informatie over de installatie-en configuratie stappen.

### <a name="azure-key-vault-configuration-steps"></a>Configuratie stappen voor Azure Key Vault

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) installeren
- Maak twee Azure-sleutel kluizen in twee verschillende regio's met behulp van [Power shell om de eigenschap "zacht-delete" in te scha kelen](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell) voor de sleutel kluizen (deze optie is nog niet beschikbaar vanuit de Azure-Portal, maar vereist voor SQL).
- Beide Azure-sleutel kluizen moeten zich bevinden in de twee regio's die beschikbaar zijn in dezelfde Azure geo om back-up en herstel van sleutels te kunnen uitvoeren.  Als u wilt dat de twee sleutel kluizen in verschillende geografische gebieden zich bevinden om te voldoen aan de vereisten van SQL geo-DR, volgt u het [BYOK-proces](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys) waarmee sleutels kunnen worden geïmporteerd uit een on-premises HSM.
- Een nieuwe sleutel maken in de eerste sleutel kluis:  
  - RSA/RSA-HSM 2048-sleutel
  - Geen verval datums
  - Sleutel is ingeschakeld en heeft machtigingen voor het uitvoeren van Get-, terugloop-en uitpakken van sleutel bewerkingen
- Maak een back-up van de primaire sleutel en zet de sleutel terug naar de tweede sleutel kluis.  Zie [BackupAzureKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey) en [Restore-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey).

### <a name="azure-sql-database-configuration-steps"></a>Configuratie stappen voor Azure SQL Database

De volgende configuratie stappen variëren of u begint met een nieuwe SQL-implementatie of dat u werkt met een al bestaande SQL geo-DR-implementatie.  We geven eerst een overzicht van de configuratie stappen voor een nieuwe implementatie en vervolgens wordt uitgelegd hoe u TDE-beveiligingen die zijn opgeslagen in Azure Key Vault toewijst aan een bestaande implementatie waarvoor al een geo-DR-verbinding is gemaakt.

**Stappen voor een nieuwe implementatie**:

- Maak de twee SQL Database-servers in dezelfde twee regio's als de eerder gemaakte sleutel kluizen.
- Selecteer het deel venster SQL Database Server TDE en voor elke SQL Database Server:  
  - Selecteer de Azure in dezelfde regio
  - Selecteer de sleutel die moet worden gebruikt als TDE-Protector: elke server gebruikt de lokale kopie van de TDE-Protector.
  - Als u dit doet in de portal, wordt een [AppID](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview) gemaakt voor de SQL database-server, die wordt gebruikt om de SQL database-server machtigingen toe te wijzen voor toegang tot de sleutel kluis. Verwijder deze identiteit niet. De toegang kan worden ingetrokken door de machtigingen in Azure Key Vault te verwijderen in plaats van de SQL Database-Server, die wordt gebruikt om de SQL Database Server machtigingen toe te wijzen voor toegang tot de sleutel kluis.
- Maak de primaire data base.
- Volg de [richt lijnen voor actieve geo-replicatie](sql-database-geo-replication-overview.md) om het scenario te volt ooien. met deze stap wordt de secundaire data base gemaakt.

![Failover-groepen en geo-Dr](./media/transparent-data-encryption-byok-azure-sql/Geo_DR_Config.PNG)

> [!NOTE]
> Het is belang rijk om ervoor te zorgen dat dezelfde TDE-beveiligingen aanwezig zijn in beide sleutel kluizen voordat u doorgaat met het instellen van de geo-koppeling tussen de data bases.

**Stappen voor een bestaande SQL-data base met geo-Dr-implementatie**:

Omdat de SQL Database servers al bestaan en de primaire en secundaire data base al zijn toegewezen, moeten de stappen voor het configureren van Azure Key Vault worden uitgevoerd in de volgende volg orde:

- Begin met de SQL Database-Server die als host fungeert voor de secundaire Data Base:
  - De sleutel kluis toewijzen die zich in dezelfde regio bevindt
  - De TDE-Protector toewijzen
- Ga nu naar de SQL Database-Server die als host fungeert voor de primaire Data Base:
  - Selecteer dezelfde TDE-Protector als voor de secundaire data base

![Failover-groepen en geo-Dr](./media/transparent-data-encryption-byok-azure-sql/geo_DR_ex_config.PNG)

> [!NOTE]
> Wanneer u de sleutel kluis aan de server toewijst, is het belang rijk om te beginnen met de secundaire server.  In de tweede stap wijst u de sleutel kluis toe aan de primaire server en werkt u de TDE-Protector bij, dan blijft de geo-DR-koppeling werken, omdat de TDE-Protector die wordt gebruikt door de gerepliceerde data base, beschikbaar is voor beide servers.

Voordat u TDE inschakelt met door de klant beheerde sleutels in Azure Key Vault voor een SQL Database geo-DR-scenario, is het belang rijk om twee Azure-sleutel kluizen te maken en te onderhouden met identieke inhoud in dezelfde regio's die wordt gebruikt voor SQL Database geo-replicatie.  "Identieke inhoud" houdt in dat beide sleutel kluizen kopieën van dezelfde TDE-Protector (s) moeten bevatten, zodat beide servers toegang hebben tot de TDE-beveiligingen die door alle data bases worden gebruikt.  Het is in de toekomst nood zakelijk dat beide sleutel kluizen synchroon blijven, wat betekent dat ze dezelfde kopieën van TDE-beveiligingen moeten bevatten na het draaien van sleutels, het behouden van oude versies van sleutels die worden gebruikt voor logboek bestanden of back-ups, TDE-beveiligingen moeten dezelfde sleutel eigenschappen en de sleutel behouden. kluizen moeten dezelfde toegangs machtigingen voor SQL behouden.  

Volg de stappen in het [overzicht van actieve geo-replicatie](sql-database-geo-replication-overview.md) om een failover te testen en activeren, die regel matig moet worden uitgevoerd om de toegangs machtigingen voor SQL te bevestigen voor de sleutel kluizen.

### <a name="backup-and-restore"></a>Back-up en herstel

Wanneer een Data Base is versleuteld met TDE met behulp van een sleutel uit Key Vault, worden gegenereerde back-ups ook versleuteld met dezelfde TDE-Protector.

Als u een back-up wilt herstellen die is versleuteld met een TDE-Protector van Key Vault, moet u ervoor zorgen dat het sleutel materiaal zich nog in de oorspronkelijke kluis in de oorspronkelijke sleutel naam bevindt. Wanneer de TDE-Protector voor een Data Base wordt gewijzigd, worden de oude back-ups van de Data Base **niet** bijgewerkt voor gebruik van de meest recente TDe-Protector. Daarom is het raadzaam dat u alle oude versies van de TDE-Protector in Key Vault behoudt, zodat database back-ups kunnen worden hersteld.

Als een sleutel die nodig is voor het herstellen van een back-up, niet meer in de oorspronkelijke sleutel kluis is, wordt het volgende fout bericht weer gegeven: "Doel server `<Servername>` heeft geen toegang tot alle Azure-uri's die zijn \<gemaakt tussen tijds tempel \<#1 > en tijds tempel #2 >. Voer de bewerking opnieuw uit nadat alle Azure-Uri's zijn teruggezet.

Als u dit wilt verhelpen, voert u de cmdlet [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) uit om de lijst met sleutels te retour neren van Key Vault die zijn toegevoegd aan de server (tenzij ze door een gebruiker zijn verwijderd). Om ervoor te zorgen dat alle back-ups kunnen worden hersteld, moet u ervoor zorgen dat de doel server voor de back-up toegang heeft tot al deze sleutels.

```powershell
Get-AzSqlServerKeyVaultKey `
  -ServerName <LogicalServerName> `
  -ResourceGroup <SQLDatabaseResourceGroupName>
```

Zie [een Azure SQL database herstellen](sql-database-recovery-using-backups.md)voor meer informatie over herstel van back-ups voor SQL database. Zie [een Azure SQL Data Warehouse herstellen](../sql-data-warehouse/backup-and-restore.md)voor meer informatie over herstel van back-ups voor SQL Data Warehouse.

Aanvullende overweging voor back-ups van logboek bestanden: Back-ups van logboek bestanden blijven versleuteld met de oorspronkelijke TDE Encryption, zelfs als de TDE-Protector is geroteerd en de data base nu een nieuwe TDE-Protector gebruikt.  Op het moment van terugzetten zijn beide sleutels nodig om de data base te herstellen.  Als het logboek bestand gebruikmaakt van een TDE-Protector die is opgeslagen in Azure Key Vault, is deze sleutel vereist tijdens de herstel tijd, zelfs als de data base is gewijzigd voor het gebruik van door de service beheerde TDE.
