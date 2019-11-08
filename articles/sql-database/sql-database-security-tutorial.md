---
title: Een enkelvoudige of pooldatabase beveiligen
description: Een zelf studie waarin u leert hoe u een enkele of gegroepeerde Data base in Azure SQL Database kunt beveiligen met betips over technieken en functies.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 09/03/2019
ms.custom: seoapril2019
ms.openlocfilehash: 05ea21f7b3221425e5b052797d79e629fdfe3ba5
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818037"
---
# <a name="tutorial-secure-a-single-or-pooled-database"></a>Zelf studie: een enkele of gegroepeerde Data Base beveiligen

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> - Firewallregels op server- en databaseniveau maken
> - Een Azure Active Directory-beheerder configureren
> - Gebruikerstoegang beheren met SQL-verificatie, Azure AD-verificatie en beveiligde verbindingsreeksen
> - Beveiligingsfuncties inschakelen, zoals Advanced Data Security, controle, gegevensmaskering en versleuteling

Azure SQL Database beveiligt gegevens in een enkelvoudige of pooldatabase door:

- Toegang te beperken met behulp van firewallregels
- Verificatiemechanismen die identiteit vereisen
- Autorisatie via op rollen gebaseerde lidmaatschappen en machtigingen
- Beveiligingsfuncties in te schakelen

> [!NOTE]
> Een Azure SQL-database op een beheerd exemplaar wordt beveiligd met behulp van netwerkbeveiligingsregels en privé-eindpunten, zoals beschreven in [Beheerd exemplaar voor Azure SQL Database](sql-database-managed-instance-index.yml) en [connectiviteitsarchitectuur](sql-database-managed-instance-connectivity-architecture.md).

Zie voor meer informatie de artikelen [Overzicht van Azure SQL Database-beveiliging](/azure/sql-database/sql-database-security-index) en [-mogelijkheden](sql-database-security-overview.md).

> [!TIP]
> De volgende Microsoft Learn module helpt u gratis te leren hoe u [uw Azure SQL database kunt beveiligen](https://docs.microsoft.com/learn/modules/secure-your-azure-sql-database/).

## <a name="prerequisites"></a>Vereisten

Zorg dat u over het volgende beschikt als u de zelfstudie wilt uitvoeren:

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)
- Een Azure SQL-server en -database
  - Maak deze met [Azure-portal](sql-database-single-database-get-started.md), [CLI](sql-database-cli-samples.md) of [PowerShell](sql-database-powershell-samples.md)

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Voor alle stappen in deze zelfstudie moet u zich aanmelden bij de [Azure-portal](https://portal.azure.com/)

## <a name="create-firewall-rules"></a>Firewall-regels maken

SQL-databases worden in Azure beveiligd door een firewall. Standaard worden alle verbindingen met de server en de data base geweigerd. Raadpleeg [firewallregels op Azure SQL Database-serverniveau en -databaseniveau](sql-database-firewall-configure.md) voor meer informatie.

Stel **Toegang tot Azure services toestaan** in op **UIT** voor de veiligste configuratie. Vervolgens maakt u een [gereserveerd IP-adres (klassieke implementatie)](../virtual-network/virtual-networks-reserved-public-ip.md) voor de resource die verbinding moet maken, zoals een Azure-VM of cloudservice, en laat u alleen dat IP-adres toe door de firewall. Als u het [resourcebeheer](/azure/virtual-network/virtual-network-ip-addresses-overview-arm)-implementatiemodel gebruikt, is er voor elke resource een toegewezen openbaar IP-adres nodig.

> [!NOTE]
> SQL Database communiceert via poort 1433. Als u verbinding probeert te maken vanuit een bedrijfsnetwerk, wordt uitgaand verkeer via poort 1433 mogelijk niet toegestaan door de firewall van uw netwerk. In dat geval kunt u geen verbinding maken met de Azure SQL Database-server, tenzij de beheerder poort 1433 openstelt.

### <a name="set-up-sql-database-server-firewall-rules"></a>Firewallregels voor SQL Database-server instellen

IP-firewallregels op serverniveau zijn van toepassing op alle databases binnen dezelfde SQL Database-server.

Stel als volgt een serverfirewallregel in:

1. Selecteer in de Azure-portal **SQL-databases** in het menu links en selecteer uw database op de pagina **SQL-databases**.

    ![serverfirewallregel](./media/sql-database-security-tutorial/server-name.png)

    > [!NOTE]
    > Kopieer de volledig gekwalificeerde naam van uw server (zoals *uwserver.database.windows.net*) voor gebruik verderop in de zelfstudie.

1. Selecteer **Serverfirewall instellen** op de pagina **Overzicht**. De pagina **Firewallinstellingen** voor de databaseserver wordt geopend.

   1. Selecteer **IP van client toevoegen** op de werkbalk om uw huidige IP-adres aan een nieuwe firewallregel toe te voegen. De regel kan poort 1433 openen voor één IP-adres of voor een bereik met IP-adressen. Selecteer **Opslaan**.

      ![serverfirewallregel instellen](./media/sql-database-security-tutorial/server-firewall-rule2.png)

   1. Selecteer **OK** en sluit de pagina **Firewallinstellingen**.

U kunt nu verbinding maken met elke database op de server met het opgegeven IP-adres of IP-adresbereik.

### <a name="setup-database-firewall-rules"></a>Database-firewallregels instellen

Firewallregels op databaseniveau zijn alleen van toepassing op afzonderlijke databases. Deze regels worden tijdens een failover van de server bewaard in de data base. Firewallregels op databaseniveau kunnen alleen worden geconfigureerd met behulp van T-SQL-instructies (Transact-SQL) en alleen nadat u een firewallregel op serverniveau hebt geconfigureerd.

Firewallregel op databaseniveau instellen:

1. Maak verbinding met de database, bijvoorbeeld met behulp van [SQL Server Management Studio](./sql-database-connect-query-ssms.md).

1. Klik in **Objectverkenner** met de rechtermuisknop op de database en selecteer **Nieuwe query**.

1. Voeg in het queryvenster de volgende instructie toe, waarbij u het IP-adres wijzigt in uw openbare IP-adres:

    ```sql
    EXECUTE sp_set_database_firewall_rule N'Example DB Rule','0.0.0.4','0.0.0.4';
    ```

1. Selecteer **Uitvoeren** op de werkbalk om de firewallregel te maken.

> [!NOTE]
> U kunt ook een firewallregel op serverniveau maken in SSMS met behulp van de opdracht [sp_set_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database?view=azuresqldb-current), maar u moet zijn verbonden met de *hoofd*database.

## <a name="create-an-azure-ad-admin"></a>Een Azure-beheerder maken

Zorg ervoor dat u het juiste beheerde Azure Active Directory-domein gebruikt. Selecteer het AD-domein in de rechterbovenhoek van de Azure-portal. Met dit proces wordt bevestigd dat hetzelfde abonnement wordt gebruikt voor zowel Azure AD als de SQL Server waar uw Azure SQL-database of datawarehouse wordt gehost.

   ![ad-kiezen](./media/sql-database-security-tutorial/8choose-ad.png)

De Azure AD-beheerder instellen:

1. In Azure Portal selecteert u op de pagina **SQL server** **Active Directory beheerder**. Selecteer vervolgens **beheerder instellen**.

    ![active directory selecteren](./media/sql-database-security-tutorial/admin-settings.png)  

    > [!IMPORTANT]
    > U moet een 'Bedrijfsbeheerder' of 'Globale beheerder' zijn om deze taak uit te voeren.

1. Zoek en selecteer op de pagina **Beheerder toevoegen** de gewenste AD-gebruiker of groep en kies **Selecteren**. Alle leden en groepen in uw Active Directory worden weergegeven. Namen die grijs worden weergegeven, worden niet ondersteund als beheerders voor Azure AD. Zie [Functies en beperkingen van Azure AD](sql-database-aad-authentication.md#azure-ad-features-and-limitations).

    ![beheerder selecteren](./media/sql-database-security-tutorial/admin-select.png)

    > [!IMPORTANT]
    > Op rollen gebaseerd toegangsbeheer (RBAC) is alleen van toepassing op de portal en wordt niet doorgegeven aan SQL Server.

1. Selecteer bovenaan de pagina **Active Directory-beheerder** de optie **Opslaan**.

    Het proces voor het wijzigen van de beheerder kan enkele minuten duren. De nieuwe beheerder wordt weergegeven in het vak **Active Directory-beheerder**.

> [!NOTE]
> Als u een Azure AD-beheerder instelt, mag de naam van de nieuwe beheerder (gebruiker of groep) niet voorkomen als SQL Server-verificatiegebruiker in de *hoofd*database. Als deze aanwezig is, mislukt de installatie en worden de wijzigingen teruggedraaid, en wordt aangegeven dat die beheerdersnaam al bestaat. Omdat de SQL Server-verificatiegebruiker geen deel uitmaakt van Azure AD, kan er geen verbinding met de gebruiker worden gemaakt met behulp van Azure AD-verificatie.

Zie voor informatie over het configureren van Azure AD:

- [Uw on-premises identiteiten integreren met Azure AD](../active-directory/hybrid/whatis-hybrid-identity.md)
- [Uw eigen domeinnaam toevoegen aan Azure AD](../active-directory/active-directory-domains-add-azure-portal.md)
- [Microsoft Azure ondersteunt nu federatie met Windows Server AD](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)
- [Uw Azure AD-directory beheren](../active-directory/fundamentals/active-directory-administer.md)
- [Azure AD beheren met behulp van PowerShell](/powershell/azure/overview?view=azureadps-2.0)
- [Voor hybride identiteit benodigde poorten en protocollen](../active-directory/hybrid/reference-connect-ports.md)

## <a name="manage-database-access"></a>Databasetoegang beheren

Beheer databasetoegang door gebruikers toe te voegen aan de database, of door gebruikerstoegang met beveiligde verbindingsreeksen toe te staan. Verbindingsreeksen zijn nuttig voor externe toepassingen. Zie voor meer informatie [Toegangsbeheer voor Azure SQL](sql-database-control-access.md) en [AD-verificatie](sql-database-aad-authentication.md).

Kies het databaseverificatietype om gebruikers toe te voegen:

- **SQL-verificatie**, waarbij voor aanmelding gebruikersnamen en wachtwoorden worden gebruikt die alleen geldig zijn in de context van een bepaalde database binnen de server

- **Azure AD-verificatie**, waarbij door Azure AD beheerde identiteiten worden gebruikt

### <a name="sql-authentication"></a>SQL-verificatie

Toevoegen van een gebruiker met SQL-verificatie:

1. Maak verbinding met de database, bijvoorbeeld met behulp van [SQL Server Management Studio](./sql-database-connect-query-ssms.md).

1. Klik in **Objectverkenner** met de rechtermuisknop op de database en kies **Nieuwe query**.

1. Voer in het queryvenster de volgende opdracht in:

    ```sql
    CREATE USER ApplicationUser WITH PASSWORD = 'YourStrongPassword1';
    ```

1. Selecteer **Uitvoeren** op de werkbalk om de gebruiker te maken.

1. De standaardinstelling is dat de gebruiker verbinding kan maken met de database, maar geen machtigingen heeft om gegevens te lezen of te schrijven. Als u deze machtigingen wilt toekennen, voert u de volgende opdrachten uit in een nieuw queryvenster:

    ```sql
    ALTER ROLE db_datareader ADD MEMBER ApplicationUser;
    ALTER ROLE db_datawriter ADD MEMBER ApplicationUser;
    ```

> [!NOTE]
> Maak niet-beheerdersaccounts op databaseniveau, tenzij ze beheerderstaken moet uitvoeren, zoals het maken van nieuwe gebruikers.

### <a name="azure-ad-authentication"></a>Azure Active Directory-verificatie

Voor verificatie met Azure Active Directory moeten databasegebruikers ingesloten worden gemaakt. Een ingesloten databasegebruiker wordt toegewezen aan een identiteit in de Azure AD-directory die is gekoppeld aan de database, en heeft geen aanmelding in de *hoofd*database. De Azure AD-identiteit kan een individuele gebruiker of een groep zijn. Zie voor meer informatie [Contained Database Users - Making Your Database Portable](https://msdn.microsoft.com/library/ff929188.aspx) en raadpleeg de [Azure AD-zelfstudie](./sql-database-aad-authentication-configure.md) over verificatie met behulp van Azure AD.

> [!NOTE]
> Databasegebruikers (behalve beheerders) kunnen niet in de Azure-portal worden gemaakt. Azure RBAC-rollen worden niet doorgegeven aan SQL-servers, databases of datawarehouses. Ze worden alleen gebruikt voor het beheren van Azure-resources, en zijn niet van toepassing op databasemachtigingen.
>
> Zo verleent de rol *Inzender voor SQL Server* geen toegang voor het maken van verbinding met een database of datawarehouse. Deze machtiging moet binnen de database worden verleend met behulp van T-SQL-instructies.

> [!IMPORTANT]
> Speciale tekens zoals dubbele punt `:` of en-teken `&` worden niet ondersteund in gebruikersnamen in de T-SQL-instructies `CREATE LOGIN` en `CREATE USER`.

Toevoegen van een gebruiker met Azure AD-verificatie:

1. Maak verbinding met de Azure SQL-server met behulp van uw Azure AD-account, waarbij u ten minste over de machtiging *ALTER ANY USER* moet beschikken.

1. Klik in **Objectverkenner** met de rechtermuisknop op de database en selecteer **Nieuwe query**.

1. Voer in het queryvenster de volgende opdracht in en wijzig `<Azure_AD_principal_name>` in de principal-naam van de Azure AD-gebruiker of de weergavenaam van de Azure AD-groep:

   ```sql
   CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
   ```

> [!NOTE]
> Azure AD-gebruikers worden in de metagegevens van de database gemarkeerd met het type `E (EXTERNAL_USER)` en het type `X (EXTERNAL_GROUPS)` voor groepen. Zie [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql) voor meer informatie.

### <a name="secure-connection-strings"></a>Beveiligde verbindingsreeksen

Om een beveiligde, gecodeerde verbinding tot stand te brengen tussen de clienttoepassing en SQL-database, moet er een verbindingsreeks worden geconfigureerd die:

- Een versleutelde verbinding aanvraagt
- Het servercertificaat niet vertrouwt

Er wordt verbinding gemaakt via TLS (Transport Layer Security), waardoor het risico op man-in-the-middle-aanvallen afneemt. Verbindingsreeksen zijn per database beschikbaar en zijn vooraf geconfigureerd om clientstuurprogramma’s zoals ADO.NET, JDBC, ODBC en PHP te ondersteunen. Voor informatie over TLS en connectiviteit, zie [TLS-overwegingen](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity).

Ga als volgt te werk om een beveiligde verbindingsreeks te kopiëren:

1. Selecteer in de Azure-portal **SQL-databases** in het menu links en selecteer uw database op de pagina **SQL-databases**.

1. Selecteer **Databaseverbindingsreeksen tonen** op de pagina **Overzicht**.

1. Selecteer een stuurprogrammatabblad en kopieer de volledige verbindingsreeks.

    ![ADO.NET-verbindingsreeks](./media/sql-database-security-tutorial/connection.png)

## <a name="enable-security-features"></a>Beveiligingsfuncties in te schakelen

Azure SQL Database biedt beveiligingsfuncties die toegankelijk zijn met behulp van de Azure-portal. Deze functies zijn beschikbaar voor zowel de database als de server, behalve gegevensmaskering, wat alleen beschikbaar is op de database. Zie voor meer informatie deze artikelen over [Advanced Data Security](sql-database-advanced-data-security.md), [controle](sql-database-auditing.md), [dynamische gegevensmaskering](sql-database-dynamic-data-masking-get-started.md) en [transparante gegevensversleuteling](transparent-data-encryption-azure-sql.md).

### <a name="advanced-data-security"></a>Advanced Data Security

De functie Advanced Data Security detecteert bedreigingen terwijl ze zich voordoen en biedt beveiligingswaarschuwingen over afwijkende activiteiten. Gebruikers kunnen de controlefunctie gebruiken om deze verdachte gebeurtenissen te onderzoeken en te bepalen of die het gevolg zijn van pogingen om toegang te krijgen tot, of misbruik te maken van, gegevens in de database. Gebruikers krijgen ook een beveiligingsoverzicht met een evaluatie van beveiligingsproblemen en het hulpprogramma voor gegevensdetectie en -classificatie.

> [!NOTE]
> Een voorbeeld van een bedreiging is SQL-injectie, een proces waarbij aanvallers schadelijke SQL in de invoer van de toepassing injecteren. Een toepassing kan dan onbewust de schadelijke SQL uitvoeren en aanvallers toegang geven om in te breken in de database of gegevens erin te wijzigen.

U schakelt Advanced Data Security als volgt in:

1. Selecteer in de Azure-portal **SQL-databases** in het menu links en selecteer uw database op de pagina **SQL-databases**.

1. Selecteer op de pagina **Overzicht** de koppeling **Servernaam**. De databaseserverpagina wordt geopend.

1. Ga op de pagina **SQL-server** naar de sectie **Beveiliging** en selecteer **Advanced Data Security**.

   1. Selecteer **AAN** onder **Advanced Data Security** om de functie in te schakelen. Kies een opslagaccount om de evaluaties van beveiligingsproblemen in op te slaan. Selecteer vervolgens **Opslaan**.

      ![Navigatievenster](./media/sql-database-security-tutorial/threat-settings.png)

      U kunt ook e-mailberichten configureren voor het ontvangen van beveiligingswaarschuwingen, opslaggegevens en detectie van typen bedreigingen.

1. Ga terug naar de pagina**SQL-databases** van uw database en selecteer **Advanced Data Security** in de sectie **Beveiliging**. Hier vindt u verschillende beveiligingsindicatoren die beschikbaar zijn voor de database.

    ![Bedreigingsstatus](./media/sql-database-security-tutorial/threat-status.png)

Als er afwijkende activiteiten worden gedetecteerd, ontvangt u een e-mailbericht met informatie over de gebeurtenis. Dit bevat de aard van de activiteit, database, server, tijd van de gebeurtenis, mogelijke oorzaken en aanbevolen acties om de potentiële dreiging te onderzoeken en tegen te gaan. Als een dergelijke e-mail wordt ontvangen, selecteert u de koppeling **Azure SQL-auditlogboek** om de Azure-portal te starten en relevante controlerecords weer te geven voor het tijdstip van de gebeurtenis.

   ![E-mail met waarschuwing voor detectie van bedreiging](./media/sql-database-security-tutorial/threat-email.png)

### <a name="auditing"></a>Controleren

De controle functie houdt database gebeurtenissen bij en schrijft gebeurtenissen naar een audit logboek in azure Storage, Azure Monitor-Logboeken of naar een Event Hub. Controles helpen u zorgen voor naleving van wet- en regelgeving, de activiteit in uw database te begrijpen en inzicht te krijgen in afwijkingen en discrepanties die kunnen wijzen op mogelijke schendingen van de beveiliging.

Controle inschakelen:

1. Selecteer in de Azure-portal **SQL-databases** in het menu links en selecteer uw database op de pagina **SQL-databases**.

1. Selecteer **Controle** in de sectie **Beveiliging**.

1. Stel bij de **Controle**-instellingen de volgende waarden in:

   1. Stel **Controle** in op **AAN**.

   1. Selecteer bij **Bestemming voor auditlogboek** een van de volgende mogelijkheden:

       - **Storage**, een Azure-opslagaccount waar gebeurtenislogboeken worden opgeslagen en als *.xel*-bestanden kunnen worden gedownload

          > [!TIP]
          > Gebruik hetzelfde opslagaccount voor alle gecontroleerde databases om optimaal gebruik te maken van de sjablonen voor auditrapporten.

       - **Log Analytics**, waarin gebeurtenissen automatisch worden opgeslagen voor het uitvoeren query’s of verdere analyse

           > [!NOTE]
           > Een **log Analytics-werk ruimte** is vereist voor de ondersteuning van geavanceerde functies, zoals analyses, aangepaste waarschuwings regels en Excel of het exporteren van Power bi. Zonder een werkruimte is alleen de query-editor beschikbaar.

       - **Event Hub**, waarmee gebeurtenissen kunnen worden gerouteerd voor gebruik in andere toepassingen

   1. Selecteer **Opslaan**.

      ![Controle-instellingen](./media/sql-database-security-tutorial/audit-settings.png)

1. Nu kunt u **Auditlogboeken weergeven** om gegevens van databasegebeurtenissen weer te geven.

    ![Controlerecords](./media/sql-database-security-tutorial/audit-records.png)

> [!IMPORTANT]
> Zie [SQL Database controleren](sql-database-auditing.md) over het verder aanpassen van controlegebeurtenissen met behulp van PowerShell of REST API.

### <a name="dynamic-data-masking"></a>Dynamische gegevensmaskering

De functie voor het maskeren van gegevens verbergt automatisch gevoelige gegevens in uw database.

Gegevensmaskering inschakelen:

1. Selecteer in de Azure-portal **SQL-databases** in het menu links en selecteer uw database op de pagina **SQL-databases**.

1. Selecteer **Dynamische gegevensmaskering** in de sectie **Beveiliging**.

1. Selecteer **Masker toevoegen** in de instellingen bij **Dynamische gegevensmaskering** om een maskeringsregel toe te voegen. Azure vult automatisch beschikbare databaseschema's, tabellen en kolommen in waaruit u kunt kiezen.

    ![Maskerinstellingen](./media/sql-database-security-tutorial/mask-settings.png)

1. Selecteer **Opslaan**. De geselecteerde gegevens wordt nu voor privacy gemaskeerd.

    ![Maskervoorbeeld](./media/sql-database-security-tutorial/mask-query.png)

### <a name="transparent-data-encryption"></a>Transparent Data Encryption

De versleutelingsfunctie versleutelt automatisch uw data-at-rest en vereist geen wijzigingen in toepassingen die toegang hebben tot de versleutelde database. Voor nieuwe databases is versleuteling standaard ingeschakeld. U kunt gegevens ook versleutelen met behulp van SSMS en de functie [Always Encrypted](sql-database-always-encrypted.md).

Versleuteling inschakelen of controleren:

1. Selecteer in de Azure-portal **SQL-databases** in het menu links en selecteer uw database op de pagina **SQL-databases**.

1. Selecteer **Transparante gegevensversleuteling** in de sectie **Beveiliging**.

1. Zet **Gegevensversleuteling** indien nodig **AAN**. Selecteer **Opslaan**.

    ![Transparante gegevensversleuteling](./media/sql-database-security-tutorial/encryption-settings.png)

> [!NOTE]
> Als u de versleutelingsstatus wilt bekijken, maakt u verbinding met de database met behulp van [SSMS](./sql-database-connect-query-ssms.md) en vraagt u de `encryption_state`-kolom van de weergave [sys.dm_database_encrytion_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) op. De status `3` geeft aan dat de database is versleuteld.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd de beveiliging van uw database met een paar eenvoudige stappen te verbeteren. U hebt geleerd hoe u:

> [!div class="checklist"]
> - Firewallregels op server- en databaseniveau maken
> - Een Azure Active Directory-beheerder configureren
> - Gebruikerstoegang beheren met SQL-verificatie, Azure AD-verificatie en beveiligde verbindingsreeksen
> - Beveiligingsfuncties inschakelen, zoals Advanced Data Security, controle, gegevensmaskering en versleuteling

Ga door naar de volgende zelfstudie om te leren hoe u geo-replicatie kunt implementeren.

> [!div class="nextstepaction"]
>[Een geografisch gedistribueerde database implementeren](sql-database-implement-geo-distributed-database.md)
