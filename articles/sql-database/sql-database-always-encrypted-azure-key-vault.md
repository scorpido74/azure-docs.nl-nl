---
title: Altijd versleuteld - Azure Key Vault
description: In dit artikel ziet u hoe u gevoelige gegevens in een SQL-database beveiligen met gegevensversleuteling met behulp van de wizard Altijd versleuteld in SQL Server Management Studio.
keywords: gegevensversleuteling, versleutelingssleutel, cloudversleuteling
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 006c780aeb3db813c8fdfb5da0b5c13fc4dcfebc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067422"
---
# <a name="always-encrypted-protect-sensitive-data-and-store-encryption-keys-in-azure-key-vault"></a>Altijd versleuteld: bescherm gevoelige gegevens en sla versleutelingssleutels op in Azure Key Vault

In dit artikel ziet u hoe u gevoelige gegevens in een SQL-database beveiligen met gegevensversleuteling met behulp van de [wizard Always Encrypted](https://msdn.microsoft.com/library/mt459280.aspx) in [SQL Server Management Studio (SSMS).](https://msdn.microsoft.com/library/hh213248.aspx) Het bevat ook instructies die u laten zien hoe u elke versleutelingssleutel in Azure Key Vault opslaan.

Always Encrypted is een nieuwe gegevensversleutelingstechnologie in Azure SQL Database en SQL Server die gevoelige gegevens helpt beschermen tijdens rust op de server, tijdens de verplaatsing tussen client en server en terwijl de gegevens in gebruik zijn. Always Encrypted zorgt ervoor dat gevoelige gegevens nooit als plaintext in het databasesysteem worden weergegeven. Nadat u gegevensversleuteling hebt geconfigureerd, hebben alleen clienttoepassingen of app-servers die toegang hebben tot de sleutels toegang tot plaintext-gegevens. Zie [Always Encrypted (Database Engine)](https://msdn.microsoft.com/library/mt163865.aspx)voor gedetailleerde informatie.

Nadat u de database hebt geconfigureerd om Always Encrypted te gebruiken, maakt u een clienttoepassing in C# met Visual Studio om met de versleutelde gegevens te werken.

Volg de stappen in dit artikel en ontdek hoe u Always Encrypted instelt voor een Azure SQL-database. In dit artikel leert u hoe u de volgende taken uitvoeren:

- Gebruik de wizard Altijd versleuteld in SSMS om [altijd versleutelde sleutels te](https://msdn.microsoft.com/library/mt163865.aspx#Anchor_3)maken.
  - Een [kolomhoofdsleutel (CMK) maken.](https://msdn.microsoft.com/library/mt146393.aspx)
  - Een [kolomversleutelingssleutel (CEK) maken.](https://msdn.microsoft.com/library/mt146372.aspx)
- Maak een databasetabel en versleutel kolommen.
- Maak een toepassing die gegevens uit de versleutelde kolommen invoegt, selecteert en weergeeft.

## <a name="prerequisites"></a>Vereisten

Voor deze zelfstudie heb je het:

- Een Azure-account en -abonnement. Als je er geen hebt, meld je dan aan voor een [gratis proefperiode.](https://azure.microsoft.com/pricing/free-trial/)
- [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) versie 13.0.700.242 of hoger.
- [.NET Framework 4.6](https://msdn.microsoft.com/library/w0x726c2.aspx) of hoger (op de clientcomputer).
- [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
- [Azure PowerShell](/powershell/azure/overview) of [Azure CLI](/cli/azure/install-azure-cli)

## <a name="enable-your-client-application-to-access-the-sql-database-service"></a>Uw clienttoepassing toegang inschakelen tot de SQL Database-service

U moet uw clienttoepassing inschakelen om toegang te krijgen tot de SQL Database-service door een Azure Active Directory-toepassing (AAD) in te stellen en de *toepassings-id* en *-sleutel* te kopiëren die u nodig hebt om uw toepassing te verifiëren.

Als u de *toepassings-id* en *-sleutel wilt opvragen,* voert u de stappen uit bij [het maken van een Azure Active Directory-toepassing en serviceprincipal die toegang hebben tot bronnen](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="create-a-key-vault-to-store-your-keys"></a>Een sleutelkluis maken om uw sleutels op te slaan

Nu uw client-app is geconfigureerd en u uw toepassings-id hebt, is het tijd om een sleutelkluis te maken en het toegangsbeleid te configureren, zodat u en uw toepassing toegang hebben tot de geheimen van de kluis (de altijd versleutelde sleutels). De machtigingen *voor maken*, *ophalen*, *lijst,* *ondertekenen,* *verifiëren,* *wrapKey*en *uitpakkenSleutel* zijn vereist voor het maken van een nieuwe kolomhoofdsleutel en voor het instellen van versleuteling met SQL Server Management Studio.

U snel een sleutelkluis maken door het volgende script uit te voeren. Zie [Wat is Azure Key Vault voor](../key-vault/key-vault-overview.md)een gedetailleerde uitleg van deze opdrachten en meer informatie over het maken en configureren van een sleutelkluis.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

> [!IMPORTANT]
> De PowerShell Azure Resource Manager (RM)-module wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkelingen zijn voor de Az.Sql-module. De AzureRM-module blijft bugfixes ontvangen tot ten minste december 2020.  De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn nagenoeg identiek. Zie De nieuwe Azure [PowerShell Az-module introduceren](/powershell/azure/new-azureps-module-az)voor meer informatie over de compatibiliteit ervan.

```powershell
$subscriptionName = '<subscriptionName>'
$userPrincipalName = '<username@domain.com>'
$applicationId = '<applicationId from AAD application>'
$resourceGroupName = '<resourceGroupName>' # use the same resource group name when creating your SQL Database below
$location = '<datacenterLocation>'
$vaultName = '<vaultName>'

Connect-AzAccount
$subscriptionId = (Get-AzSubscription -SubscriptionName $subscriptionName).Id
Set-AzContext -SubscriptionId $subscriptionId

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzKeyVault -VaultName $vaultName -ResourceGroupName $resourceGroupName -Location $location

Set-AzKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $resourceGroupName -PermissionsToKeys create,get,wrapKey,unwrapKey,sign,verify,list -UserPrincipalName $userPrincipalName
Set-AzKeyVaultAccessPolicy  -VaultName $vaultName  -ResourceGroupName $resourceGroupName -ServicePrincipalName $applicationId -PermissionsToKeys get,wrapKey,unwrapKey,sign,verify,list
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
$subscriptionName = '<subscriptionName>'
$userPrincipalName = '<username@domain.com>'
$applicationId = '<applicationId from AAD application>'
$resourceGroupName = '<resourceGroupName>' # use the same resource group name when creating your SQL Database below
$location = '<datacenterLocation>'
$vaultName = '<vaultName>'

az login
az account set --subscription $subscriptionName

az group create --location $location --name $resourceGroupName

az keyvault create --name $vaultName --resource-group $resourceGroupName --location $location

az keyvault set-policy --name $vaultName --key-permissions create, get, list, sign, unwrapKey, verify, wrapKey --resource-group $resourceGroupName --upn $userPrincipalName
az keyvault set-policy --name $vaultName --key-permissions get, list, sign, unwrapKey, verify, wrapKey --resource-group $resourceGroupName --spn $applicationId
```

* * *

## <a name="create-a-blank-sql-database"></a>Een lege SQL-database maken

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2.  > Ga naar Een > **SQL-database voor** **bronnendatabases****maken**.
3. Maak een **Lege** database met de naam **Clinic** op een nieuwe of bestaande server. Zie Uw eerste Azure SQL-database voor gedetailleerde aanwijzingen over het maken van een database in de [Azure-portal.](sql-database-single-database-get-started.md)

    ![Een lege database maken](./media/sql-database-always-encrypted-azure-key-vault/create-database.png)

U hebt de verbindingstekenreeks later in de zelfstudie nodig, dus nadat u de database hebt gemaakt, bladert u naar de nieuwe database van de kliniek en kopieert u de verbindingstekenreeks. U de verbindingstekenreeks op elk gewenst moment krijgen, maar het is eenvoudig om deze te kopiëren in de Azure-portal.

1. Ga naar **SQL-databases** > **Clinic** > **Show databaseverbindingstekenreeksen**.
2. Kopieer de verbindingstekenreeks voor **ADO.NET**.

    ![De verbindingsreeks kopiëren](./media/sql-database-always-encrypted-azure-key-vault/connection-strings.png)

## <a name="connect-to-the-database-with-ssms"></a>Verbinding maken met de database via SSMS

Open SSMS en maak verbinding met de server met de Clinic-database.

1. Open SQL Server Management Studio. (Ga naar **Database** > **Engine verbinden** om het venster Verbinding maken met server **te** openen als deze niet is geopend.)

2. Voer uw servernaam en referenties in. De servernaam is te vinden op het SQL-databaseblad en in de verbindingstekenreeks die u eerder hebt gekopieerd. Typ de volledige servernaam, inclusief *database.windows.net*.

    ![De verbindingsreeks kopiëren](./media/sql-database-always-encrypted-azure-key-vault/ssms-connect.png)

Als het venster **Nieuwe firewallregel** wordt geopend, meldt u zich aan bij Azure en laat U ssms een nieuwe firewallregel voor u maken.

## <a name="create-a-table"></a>Een tabel maken

In deze sectie maakt u een tabel om patiëntgegevens vast te houden. Het is niet in eerste instantie versleuteld - u configureert encryptie in de volgende sectie.

1. **Databases uitvouwen**.
2. Klik met de rechtermuisknop op de **database van de kliniek** en klik op Nieuwe **query**.
3. Plak de volgende Transact-SQL (T-SQL) in het nieuwe queryvenster en voer deze **uit.**

```sql
CREATE TABLE [dbo].[Patients](
         [PatientId] [int] IDENTITY(1,1),
         [SSN] [char](11) NOT NULL,
         [FirstName] [nvarchar](50) NULL,
         [LastName] [nvarchar](50) NULL,
         [MiddleName] [nvarchar](50) NULL,
         [StreetAddress] [nvarchar](50) NULL,
         [City] [nvarchar](50) NULL,
         [ZipCode] [char](5) NULL,
         [State] [char](2) NULL,
         [BirthDate] [date] NOT NULL
         PRIMARY KEY CLUSTERED ([PatientId] ASC) ON [PRIMARY] );
GO
```

## <a name="encrypt-columns-configure-always-encrypted"></a>Kolommen versleutelen (Altijd versleuteld configureren)

SSMS biedt een wizard waarmee u always encrypted eenvoudig configureren door de kolomhoofdsleutel, kolomversleutelingssleutel en versleutelde kolommen voor u in te stellen.

1.  > **Clinic** > **Tables** **Databaseskliniektabellen**uitbreiden .
2. Klik met de rechtermuisknop op de tabel **Patiënten** en selecteer **Kolommen versleutelen** om de wizard Altijd versleuteld te openen:

    ![Kolommen versleutelen](./media/sql-database-always-encrypted-azure-key-vault/encrypt-columns.png)

De wizard Altijd versleuteld bevat de volgende secties: **Kolomselectie,** **Configuratie van de hoofdsleutel,** **validatie**en **samenvatting**.

### <a name="column-selection"></a>Kolomselectie

Klik **op Volgende** op de pagina **Inleiding** om de pagina Kolomselectie te **openen.** Op deze pagina selecteert u welke kolommen u wilt versleutelen, [welk type versleuteling en welke kolomversleutelingssleutel (CEK)](https://msdn.microsoft.com/library/mt459280.aspx#Anchor_2) u wilt gebruiken.

Versleutel **SSN-** en **Geboortedatum-informatie** voor elke patiënt. De Kolom SSN gebruikt deterministische versleuteling, die opzoekingen voor gelijkheid, joins en groeperen ondersteunt. De kolom BirthDate gebruikt gerandomiseerde versleuteling, die bewerkingen niet ondersteunt.

Stel het **versleutelingstype** voor de kolom SSN in op **Deterministisch** en de kolom Geboortedatum op **Gerandomiseerd**. Klik op **Volgende**.

![Kolommen versleutelen](./media/sql-database-always-encrypted-azure-key-vault/column-selection.png)

### <a name="master-key-configuration"></a>Configuratie van hoofdsleutel

Op de pagina **Configuratie van de mastersleutel** stelt u uw CMK in en selecteert u de belangrijkste winkelprovider waar het CMK wordt opgeslagen. Momenteel u een CMK opslaan in de Windows-certificaatopslag, Azure Key Vault of een Hardware Security Module (HSM).

In deze zelfstudie ziet u hoe u uw sleutels opslaan in Azure Key Vault.

1. Selecteer **Azure Key Vault**.
2. Selecteer de gewenste sleutelkluis in de vervolgkeuzelijst.
3. Klik op **Volgende**.

![Hoofdsleutelconfiguratie](./media/sql-database-always-encrypted-azure-key-vault/master-key-configuration.png)

### <a name="validation"></a>Validatie

U de kolommen nu versleutelen of een PowerShell-script opslaan om later uit te voeren. Selecteer voor deze zelfstudie **Doorgaan om nu te voltooien** en klik op **Volgende**.

### <a name="summary"></a>Samenvatting

Controleer of de instellingen allemaal correct zijn en klik op **Voltooien** om de installatie voor Altijd versleuteld te voltooien.

![Samenvatting](./media/sql-database-always-encrypted-azure-key-vault/summary.png)

### <a name="verify-the-wizards-actions"></a>De acties van de wizard verifiëren

Nadat de wizard is voltooid, is uw database ingesteld voor Altijd versleuteld. De wizard heeft de volgende acties uitgevoerd:

- Een kolomhoofdsleutel maken en deze opslaan in Azure Key Vault.
- Een kolomversleutelingssleutel gemaakt en opgeslagen in Azure Key Vault.
- De geselecteerde kolommen geconfigureerd voor versleuteling. De tabel Patiënten bevat momenteel geen gegevens, maar alle bestaande gegevens in de geselecteerde kolommen zijn nu versleuteld.

U het maken van de sleutels in SSMS verifiëren door de altijd**versleutelde sleutels van** **Clinic** > **Security** > uit te breiden.

## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>Een clienttoepassing maken die werkt met de versleutelde gegevens

Nu Always Encrypted is ingesteld, u een toepassing bouwen die *inserts* uitvoert en *selecteert* op de versleutelde kolommen.  

> [!IMPORTANT]
> Uw toepassing moet [SqlParameter-objecten](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx) gebruiken bij het doorgeven van plaintext-gegevens aan de server met always versleutelde kolommen. Het doorgeven van letterlijke waarden zonder sqlparameter-objecten te gebruiken, resulteert in een uitzondering.

1. Open Visual Studio en maak een nieuwe C# **Console-applicatie** (Visual Studio 2015 en eerder) of **Console App (.NET Framework)** (Visual Studio 2017 en hoger). Controleer of uw project is ingesteld op **.NET Framework 4.6** of hoger.
2. Geef het project **AlwaysEncryptedConsoleAKVApp** een naam en klik op **OK**.
3. Installeer de volgende NuGet-pakketten door naar **Tools** > **NuGet Package Manager** > Package**Manager Console**te gaan.

Voer deze twee regels code uit in de Package Manager Console:

   ```powershell
   Install-Package Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```

## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>De verbindingstekenreeks wijzigen om Always Encrypted in te schakelen

In deze sectie wordt uitgelegd hoe u Always Encrypted inschakelt in uw databaseverbindingstekenreeks.

Als u Always Encrypted wilt inschakelen, moet u het trefwoord **Kolomversleutelingsinstelling** toevoegen aan uw verbindingstekenreeks en deze instellen op **Ingeschakeld**.

U dit rechtstreeks in de verbindingstekenreeks instellen of u deze instellen met [SqlConnectionStringBuilder.](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.aspx) De voorbeeldtoepassing in de volgende sectie laat zien hoe **sqlConnectionstringbuilder**wordt gebruikt.

### <a name="enable-always-encrypted-in-the-connection-string"></a>Altijd versleuteld inschakelen in de verbindingstekenreeks

Voeg het volgende trefwoord toe aan uw verbindingstekenreeks.

   `Column Encryption Setting=Enabled`

### <a name="enable-always-encrypted-with-sqlconnectionstringbuilder"></a>Altijd versleuteld inschakelen met SqlConnectionStringBuilder

In de volgende code ziet u hoe u Always Encrypted inschakelt door [SqlConnectionStringBuilder.ColumnEncryptionSetting](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting.aspx) in te stellen [op Ingeschakeld](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectioncolumnencryptionsetting.aspx).

```csharp
// Instantiate a SqlConnectionStringBuilder.
SqlConnectionStringBuilder connStringBuilder = new SqlConnectionStringBuilder("replace with your connection string");

// Enable Always Encrypted.
connStringBuilder.ColumnEncryptionSetting = SqlConnectionColumnEncryptionSetting.Enabled;
```

## <a name="register-the-azure-key-vault-provider"></a>De Azure Key Vault-provider registreren
In de volgende code ziet u hoe u de Azure Key Vault-provider registreert bij het stuurprogramma ADO.NET.

```csharp
private static ClientCredential _clientCredential;

static void InitializeAzureKeyVaultProvider() {
    _clientCredential = new ClientCredential(applicationId, clientKey);

    SqlColumnEncryptionAzureKeyVaultProvider azureKeyVaultProvider = new SqlColumnEncryptionAzureKeyVaultProvider(GetToken);

    Dictionary<string, SqlColumnEncryptionKeyStoreProvider> providers = new Dictionary<string, SqlColumnEncryptionKeyStoreProvider>();

    providers.Add(SqlColumnEncryptionAzureKeyVaultProvider.ProviderName, azureKeyVaultProvider);
    SqlConnection.RegisterColumnEncryptionKeyStoreProviders(providers);
}
```

## <a name="always-encrypted-sample-console-application"></a>Always Encrypted sample console applicatie Always Encrypted sample console application Always Encrypted sample console application Always Encrypt

In dit voorbeeld wordt uitgelegd hoe:

- Wijzig uw verbindingstekenreeks om Always Encrypted in te schakelen.
- Registreer Azure Key Vault als de belangrijkste winkelprovider van de toepassing.  
- Gegevens invoegen in de versleutelde kolommen.
- Selecteer een record door te filteren op een specifieke waarde in een versleutelde kolom.

Vervang de inhoud van *Program.cs* door de volgende code. Vervang de verbindingstekenreeks voor de globale verbindingstekenreeksvariabele in de regel die rechtstreeks voorafgaat aan de hoofdmethode door uw geldige verbindingstekenreeks van de Azure-portal. Dit is de enige wijziging die u moet aanbrengen in deze code.

Voer de app uit om Always Encrypted in actie te zien.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Data;
using System.Data.SqlClient;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider;

namespace AlwaysEncryptedConsoleAKVApp {
    class Program {
        // Update this line with your Clinic database connection string from the Azure portal.
        static string connectionString = @"<connection string from the portal>";
        static string applicationId = @"<application ID from your AAD application>";
        static string clientKey = "<key from your AAD application>";

        static void Main(string[] args) {
            InitializeAzureKeyVaultProvider();

            Console.WriteLine("Signed in as: " + _clientCredential.ClientId);

            Console.WriteLine("Original connection string copied from the Azure portal:");
            Console.WriteLine(connectionString);

            // Create a SqlConnectionStringBuilder.
            SqlConnectionStringBuilder connStringBuilder =
                new SqlConnectionStringBuilder(connectionString);

            // Enable Always Encrypted for the connection.
            // This is the only change specific to Always Encrypted
            connStringBuilder.ColumnEncryptionSetting =
                SqlConnectionColumnEncryptionSetting.Enabled;

            Console.WriteLine(Environment.NewLine + "Updated connection string with Always Encrypted enabled:");
            Console.WriteLine(connStringBuilder.ConnectionString);

            // Update the connection string with a password supplied at runtime.
            Console.WriteLine(Environment.NewLine + "Enter server password:");
            connStringBuilder.Password = Console.ReadLine();

            // Assign the updated connection string to our global variable.
            connectionString = connStringBuilder.ConnectionString;

            // Delete all records to restart this demo app.
            ResetPatientsTable();

            // Add sample data to the Patients table.
            Console.Write(Environment.NewLine + "Adding sample patient data to the database...");

            InsertPatient(new Patient() {
                SSN = "999-99-0001",
                FirstName = "Orlando",
                LastName = "Gee",
                BirthDate = DateTime.Parse("01/04/1964")
            });
            InsertPatient(new Patient() {
                SSN = "999-99-0002",
                FirstName = "Keith",
                LastName = "Harris",
                BirthDate = DateTime.Parse("06/20/1977")
            });
            InsertPatient(new Patient() {
                SSN = "999-99-0003",
                FirstName = "Donna",
                LastName = "Carreras",
                BirthDate = DateTime.Parse("02/09/1973")
            });
            InsertPatient(new Patient() {
                SSN = "999-99-0004",
                FirstName = "Janet",
                LastName = "Gates",
                BirthDate = DateTime.Parse("08/31/1985")
            });
            InsertPatient(new Patient() {
                SSN = "999-99-0005",
                FirstName = "Lucy",
                LastName = "Harrington",
                BirthDate = DateTime.Parse("05/06/1993")
            });

            // Fetch and display all patients.
            Console.WriteLine(Environment.NewLine + "All the records currently in the Patients table:");

            foreach (Patient patient in SelectAllPatients()) {
                Console.WriteLine(patient.FirstName + " " + patient.LastName + "\tSSN: " + patient.SSN + "\tBirthdate: " + patient.BirthDate);
            }

            // Get patients by SSN.
            Console.WriteLine(Environment.NewLine + "Now lets locate records by searching the encrypted SSN column.");

            string ssn;

            // This very simple validation only checks that the user entered 11 characters.
            // In production be sure to check all user input and use the best validation for your specific application.
            do {
                Console.WriteLine("Please enter a valid SSN (ex. 999-99-0003):");
                ssn = Console.ReadLine();
            } while (ssn.Length != 11);

            // The example allows duplicate SSN entries so we will return all records
            // that match the provided value and store the results in selectedPatients.
            Patient selectedPatient = SelectPatientBySSN(ssn);

            // Check if any records were returned and display our query results.
            if (selectedPatient != null) {
                Console.WriteLine("Patient found with SSN = " + ssn);
                Console.WriteLine(selectedPatient.FirstName + " " + selectedPatient.LastName + "\tSSN: "
                    + selectedPatient.SSN + "\tBirthdate: " + selectedPatient.BirthDate);
            }
            else {
                Console.WriteLine("No patients found with SSN = " + ssn);
            }

            Console.WriteLine("Press Enter to exit...");
            Console.ReadLine();
        }

        private static ClientCredential _clientCredential;

        static void InitializeAzureKeyVaultProvider() {
            _clientCredential = new ClientCredential(applicationId, clientKey);

            SqlColumnEncryptionAzureKeyVaultProvider azureKeyVaultProvider =
              new SqlColumnEncryptionAzureKeyVaultProvider(GetToken);

            Dictionary<string, SqlColumnEncryptionKeyStoreProvider> providers =
              new Dictionary<string, SqlColumnEncryptionKeyStoreProvider>();

            providers.Add(SqlColumnEncryptionAzureKeyVaultProvider.ProviderName, azureKeyVaultProvider);
            SqlConnection.RegisterColumnEncryptionKeyStoreProviders(providers);
        }

        public async static Task<string> GetToken(string authority, string resource, string scope) {
            var authContext = new AuthenticationContext(authority);
            AuthenticationResult result = await authContext.AcquireTokenAsync(resource, _clientCredential);

            if (result == null)
                throw new InvalidOperationException("Failed to obtain the access token");
            return result.AccessToken;
        }

        static int InsertPatient(Patient newPatient) {
            int returnValue = 0;

            string sqlCmdText = @"INSERT INTO [dbo].[Patients] ([SSN], [FirstName], [LastName], [BirthDate])
     VALUES (@SSN, @FirstName, @LastName, @BirthDate);";

            SqlCommand sqlCmd = new SqlCommand(sqlCmdText);

            SqlParameter paramSSN = new SqlParameter(@"@SSN", newPatient.SSN);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            SqlParameter paramFirstName = new SqlParameter(@"@FirstName", newPatient.FirstName);
            paramFirstName.DbType = DbType.String;
            paramFirstName.Direction = ParameterDirection.Input;

            SqlParameter paramLastName = new SqlParameter(@"@LastName", newPatient.LastName);
            paramLastName.DbType = DbType.String;
            paramLastName.Direction = ParameterDirection.Input;

            SqlParameter paramBirthDate = new SqlParameter(@"@BirthDate", newPatient.BirthDate);
            paramBirthDate.SqlDbType = SqlDbType.Date;
            paramBirthDate.Direction = ParameterDirection.Input;

            sqlCmd.Parameters.Add(paramSSN);
            sqlCmd.Parameters.Add(paramFirstName);
            sqlCmd.Parameters.Add(paramLastName);
            sqlCmd.Parameters.Add(paramBirthDate);

            using (sqlCmd.Connection = new SqlConnection(connectionString)) {
                try {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();
                }
                catch (Exception ex) {
                    returnValue = 1;
                    Console.WriteLine("The following error was encountered: ");
                    Console.WriteLine(ex.Message);
                    Console.WriteLine(Environment.NewLine + "Press Enter key to exit");
                    Console.ReadLine();
                    Environment.Exit(0);
                }
            }
            return returnValue;
        }


        static List<Patient> SelectAllPatients() {
            List<Patient> patients = new List<Patient>();

            SqlCommand sqlCmd = new SqlCommand(
              "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients]",
                new SqlConnection(connectionString));

            using (sqlCmd.Connection = new SqlConnection(connectionString))

            using (sqlCmd.Connection = new SqlConnection(connectionString)) {
                try {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows) {
                        while (reader.Read()) {
                            patients.Add(new Patient() {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            });
                        }
                    }
                }
                catch (Exception ex) {
                    throw;
                }
            }

            return patients;
        }

        static Patient SelectPatientBySSN(string ssn) {
            Patient patient = new Patient();

            SqlCommand sqlCmd = new SqlCommand(
                "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients] WHERE [SSN]=@SSN",
                new SqlConnection(connectionString));

            SqlParameter paramSSN = new SqlParameter(@"@SSN", ssn);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            sqlCmd.Parameters.Add(paramSSN);

            using (sqlCmd.Connection = new SqlConnection(connectionString)) {
                try {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows) {
                        while (reader.Read()) {
                            patient = new Patient() {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            };
                        }
                    }
                    else {
                        patient = null;
                    }
                }
                catch (Exception ex) {
                    throw;
                }
            }
            return patient;
        }

        // This method simply deletes all records in the Patients table to reset our demo.
        static int ResetPatientsTable() {
            int returnValue = 0;

            SqlCommand sqlCmd = new SqlCommand("DELETE FROM Patients");
            using (sqlCmd.Connection = new SqlConnection(connectionString)) {
                try {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();

                }
                catch (Exception ex) {
                    returnValue = 1;
                }
            }
            return returnValue;
        }
    }

    class Patient {
        public string SSN { get; set; }
        public string FirstName { get; set; }
        public string LastName { get; set; }
        public DateTime BirthDate { get; set; }
    }
}
```

## <a name="verify-that-the-data-is-encrypted"></a>Controleren of de gegevens zijn versleuteld

U snel controleren of de werkelijke gegevens op de server worden versleuteld door de gegevens van patiënten met SSMS op te vragen (met behulp van uw huidige verbinding waarbij **kolomversleutelingsinstelling** nog niet is ingeschakeld).

Voer de volgende query uit in de database van de kliniek.

```sql
SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
```

U zien dat de versleutelde kolommen geen plaintext-gegevens bevatten.

   ![Nieuwe consoletoepassing](./media/sql-database-always-encrypted-azure-key-vault/ssms-encrypted.png)

Als u SSMS wilt gebruiken om toegang te krijgen tot de plaintext-gegevens, moet u er eerst voor zorgen dat de gebruiker de juiste machtigingen heeft voor de Azure Key Vault: *download,* *uitpakSleutel*en *verifieer*. Zie [Hoofdsleutels voor kolomsleutels maken en opslaan (altijd versleuteld)](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-and-store-column-master-keys-always-encrypted)voor gedetailleerde informatie.

Voeg vervolgens de parameter *Kolomversleuteling=ingeschakeld* toe tijdens uw verbinding.

1. Klik in SSMS met de rechtermuisknop op de server in **Object Explorer** en kies **Verbreken**.
2. Klik **op Databaseengine** > **verbinden** om het venster Verbinding maken met server **te** openen en klik op **Opties**.
3. Klik **op Extra verbindingsparameters** en typ **KolomversleutelingSinstelling=ingeschakeld**.

    ![Nieuwe consoletoepassing](./media/sql-database-always-encrypted-azure-key-vault/ssms-connection-parameter.png)

4. Voer de volgende query uit in de database van de kliniek.

   ```sql
   SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
   ```

     U nu de plaintext-gegevens in de versleutelde kolommen zien.
     ![Nieuwe consoletoepassing](./media/sql-database-always-encrypted-azure-key-vault/ssms-plaintext.png)

## <a name="next-steps"></a>Volgende stappen

Nadat u een database hebt gemaakt die Always Encrypted gebruikt, u het volgende doen:

- [Draai en ruim je sleutels op.](https://msdn.microsoft.com/library/mt607048.aspx)
- [Migreer gegevens die al zijn versleuteld met Always Encrypted](https://msdn.microsoft.com/library/mt621539.aspx).

## <a name="related-information"></a>Gerelateerde informatie

- [Altijd versleuteld (clientontwikkeling)](https://msdn.microsoft.com/library/mt147923.aspx)
- [Transparante gegevensversleuteling](https://msdn.microsoft.com/library/bb934049.aspx)
- [SQL Server-versleuteling](https://msdn.microsoft.com/library/bb510663.aspx)
- [Wizard Altijd versleuteld](https://msdn.microsoft.com/library/mt459280.aspx)
- [Altijd versleutelde blog](https://blogs.msdn.com/b/sqlsecurity/archive/tags/always-encrypted/)
