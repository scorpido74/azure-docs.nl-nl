---
title: Altijd versleuteld - Windows-certificaatarchief
description: In dit artikel ziet u hoe u gevoelige gegevens in een SQL-database met databaseversleuteling beveiligen met behulp van de wizard Always Encrypted in SQL Server Management Studio (SSMS). Het toont u ook hoe u uw versleutelingssleutels opslaat in het Windows-certificaatarchief.
keywords: versleuteling van gegevens, sql-versleuteling, databaseversleuteling, gevoelige gegevens, Always Encrypted
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviwer: ''
ms.date: 03/08/2019
ms.openlocfilehash: 82c3c3274a8a9d66019ce906ee7be47cedac7470
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822044"
---
# <a name="always-encrypted-protect-sensitive-data-and-store-encryption-keys-in-the-windows-certificate-store"></a>Altijd versleuteld: gevoelige gegevens beveiligen en versleutelingssleutels opslaan in het Windows-certificaatarchief

In dit artikel ziet u hoe u gevoelige gegevens in een SQL-database met databaseversleuteling beveiligen met behulp van de [wizard Always Encrypted](https://msdn.microsoft.com/library/mt459280.aspx) in [SQL Server Management Studio (SSMS).](https://msdn.microsoft.com/library/hh213248.aspx) Het toont u ook hoe u uw versleutelingssleutels opslaat in het Windows-certificaatarchief.

Always Encrypted is een nieuwe gegevensversleutelingstechnologie in Azure SQL Database en SQL Server die gevoelige gegevens helpt beschermen in rust op de server, tijdens de verplaatsing tussen client en server en terwijl de gegevens in gebruik zijn, zodat gevoelige gegevens nooit worden weergegeven als plaintext in het databasesysteem. Nadat u gegevens hebt versleuteld, hebben alleen clienttoepassingen of app-servers die toegang hebben tot de sleutels toegang tot plaintext-gegevens. Zie [Always Encrypted (Database Engine)](https://msdn.microsoft.com/library/mt163865.aspx)voor gedetailleerde informatie.

Nadat u de database hebt geconfigureerd om Always Encrypted te gebruiken, maakt u een clienttoepassing in C# met Visual Studio om met de versleutelde gegevens te werken.

Volg de stappen in dit artikel voor meer informatie over het instellen van Always Encrypted voor een Azure SQL-database. In dit artikel leert u hoe u de volgende taken uitvoeren:

* Gebruik de wizard Altijd versleuteld in SSMS om [altijd versleutelde sleutels te maken.](https://msdn.microsoft.com/library/mt163865.aspx#Anchor_3)
  * Een [kolomhoofdsleutel (CMK) maken.](https://msdn.microsoft.com/library/mt146393.aspx)
  * Een [kolomversleutelingssleutel (CEK)](https://msdn.microsoft.com/library/mt146372.aspx)maken.
* Maak een databasetabel en versleutel kolommen.
* Maak een toepassing die gegevens uit de versleutelde kolommen invoegt, selecteert en weergeeft.

## <a name="prerequisites"></a>Vereisten

Voor deze zelfstudie heb je het:

* Een Azure-account en -abonnement. Als je er geen hebt, meld je dan aan voor een [gratis proefperiode.](https://azure.microsoft.com/pricing/free-trial/)
* [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) versie 13.0.700.242 of hoger.
* [.NET Framework 4.6](https://msdn.microsoft.com/library/w0x726c2.aspx) of hoger (op de clientcomputer).
* [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).

## <a name="create-a-blank-sql-database"></a>Een lege SQL-database maken

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Klik **op Een resourcegegevens** > +**SQL-database voor****opslag** > maken .
3. Maak een **Lege** database met de naam **Clinic** op een nieuwe of bestaande server. Zie Uw eerste Azure SQL-database voor gedetailleerde instructies over het maken van een database in de [Azure-portal.](sql-database-single-database-get-started.md)

    ![Een lege database maken](./media/sql-database-always-encrypted/create-database.png)

U hebt de verbindingstekenreeks later in de zelfstudie nodig. Nadat de database is gemaakt, gaat u naar de nieuwe database van de kliniek en kopieert u de verbindingstekenreeks. U de verbindingstekenreeks op elk gewenst moment krijgen, maar het is eenvoudig om deze te kopiëren wanneer u zich in de Azure-portal bevindt.

1. Klik op **SQL-databases** > **Clinic** > **Show databaseverbindingstekenreeksen**.
2. Kopieer de verbindingstekenreeks voor **ADO.NET**.

    ![De verbindingsreeks kopiëren](./media/sql-database-always-encrypted/connection-strings.png)

## <a name="connect-to-the-database-with-ssms"></a>Verbinding maken met de database via SSMS

Open SSMS en maak verbinding met de server met de Clinic-database.

1. Open SQL Server Management Studio. (Klik op **Databaseengine** > **verbinden** om het venster **Verbinding maken met server te** openen als deze niet is geopend).
2. Voer uw servernaam en referenties in. De servernaam is te vinden op het SQL-databaseblad en in de verbindingstekenreeks die u eerder hebt gekopieerd. Typ de volledige servernaam inclusief *database.windows.net*.

    ![De verbindingsreeks kopiëren](./media/sql-database-always-encrypted/ssms-connect.png)

Als het venster **Nieuwe firewallregel** wordt geopend, meldt u zich aan bij Azure en laat U ssms een nieuwe firewallregel voor u maken.

## <a name="create-a-table"></a>Een tabel maken

In deze sectie maakt u een tabel om patiëntgegevens vast te houden. Dit zal een normale tabel in eerste instantie - u zal encryptie configureren in de volgende sectie.

1. **Databases uitvouwen**.
2. Klik met de rechtermuisknop op de **database van de kliniek** en klik op Nieuwe **query**.
3. Plak de volgende Transact-SQL (T-SQL) in het nieuwe queryvenster en voer deze **uit.**

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

## <a name="encrypt-columns-configure-always-encrypted"></a>Kolommen versleutelen (Altijd versleuteld configureren)

SSMS biedt een wizard om Always Encrypted eenvoudig te configureren door de CMK-, CEK- en versleutelde kolommen voor u in te stellen.

1.  > **Clinic** > **Tables** **Databaseskliniektabellen**uitbreiden .
2. Klik met de rechtermuisknop op de tabel **Patiënten** en selecteer **Kolommen versleutelen** om de wizard Altijd versleuteld te openen:

    ![Kolommen versleutelen](./media/sql-database-always-encrypted/encrypt-columns.png)

De wizard Altijd versleuteld bevat de volgende secties: **Kolomselectie,** **Hoofdsleutelconfiguratie** (CMK), **Validatie**en **Samenvatting**.

### <a name="column-selection"></a>Kolomselectie

Klik **op Volgende** op de pagina **Inleiding** om de pagina Kolomselectie te **openen.** Op deze pagina selecteert u welke kolommen u wilt versleutelen, [welk type versleuteling en welke kolomversleutelingssleutel (CEK)](https://msdn.microsoft.com/library/mt459280.aspx#Anchor_2) u wilt gebruiken.

Versleutel **SSN-** en **Geboortedatum-informatie** voor elke patiënt. De **Kolom SSN** gebruikt deterministische versleuteling, die opzoekingen voor gelijkheid, joins en groeperen ondersteunt. De kolom **BirthDate** gebruikt gerandomiseerde versleuteling, die bewerkingen niet ondersteunt.

Stel het **versleutelingstype** voor de kolom **SSN** in op **Deterministisch** en de kolom **Geboortedatum** op **Gerandomiseerd**. Klik op **Volgende**.

![Kolommen versleutelen](./media/sql-database-always-encrypted/column-selection.png)

### <a name="master-key-configuration"></a>Configuratie van hoofdsleutel

Op de pagina **Configuratie van de mastersleutel** stelt u uw CMK in en selecteert u de belangrijkste winkelprovider waar het CMK wordt opgeslagen. Momenteel u een CMK opslaan in de Windows-certificaatopslag, Azure Key Vault of een Hardware Security Module (HSM). In deze zelfstudie ziet u hoe u uw sleutels opslaat in het Windows-certificaatarchief.

Controleer of **het Windows-certificaatarchief** is geselecteerd en klik op **Volgende**.

![Hoofdsleutelconfiguratie](./media/sql-database-always-encrypted/master-key-configuration.png)

### <a name="validation"></a>Validatie

U de kolommen nu versleutelen of een PowerShell-script opslaan om later uit te voeren. Selecteer voor deze zelfstudie **Doorgaan om nu te voltooien** en klik op **Volgende**.

### <a name="summary"></a>Samenvatting

Controleer of de instellingen allemaal correct zijn en klik op **Voltooien** om de installatie voor Altijd versleuteld te voltooien.

![Samenvatting](./media/sql-database-always-encrypted/summary.png)

### <a name="verify-the-wizards-actions"></a>De acties van de wizard verifiëren

Nadat de wizard is voltooid, is uw database ingesteld voor Altijd versleuteld. De wizard heeft de volgende acties uitgevoerd:

* Een CMK gemaakt.
* Een CEK gemaakt.
* De geselecteerde kolommen geconfigureerd voor versleuteling. Uw **patiëntentabel** bevat momenteel geen gegevens, maar bestaande gegevens in de geselecteerde kolommen zijn nu versleuteld.

U het maken van de sleutels in SSMS verifiëren door naar **Clinic** > **Security** > **Always Encrypted Keys**te gaan. U nu de nieuwe sleutels zien die de wizard voor u heeft gegenereerd.

## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>Een clienttoepassing maken die werkt met de versleutelde gegevens

Nu Always Encrypted is ingesteld, u een toepassing bouwen die *inserts* uitvoert en *selecteert* op de versleutelde kolommen. Als u de voorbeeldtoepassing wilt uitvoeren, moet u deze uitvoeren op dezelfde computer waar u de wizard Altijd versleuteld hebt uitgevoerd. Als u de toepassing op een andere computer wilt uitvoeren, moet u uw Always Encrypted-certificaten implementeren op de computer waarop de client-app wordt uitgevoerd.  

> [!IMPORTANT]
> Uw toepassing moet [SqlParameter-objecten](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx) gebruiken bij het doorgeven van plaintext-gegevens aan de server met always versleutelde kolommen. Het doorgeven van letterlijke waarden zonder sqlparameter-objecten te gebruiken, resulteert in een uitzondering.

1. Open Visual Studio en maak een nieuwe C#-consoletoepassing. Controleer of uw project is ingesteld op **.NET Framework 4.6** of hoger.
2. Geef het project **AlwaysEncryptedConsoleApp** een naam en klik op **OK**.

![Nieuwe consoletoepassing](./media/sql-database-always-encrypted/console-app.png)

## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>De verbindingstekenreeks wijzigen om Always Encrypted in te schakelen

In deze sectie wordt uitgelegd hoe u Always Encrypted inschakelt in uw databaseverbindingstekenreeks. U wijzigt de console-app die u zojuist hebt gemaakt in de volgende sectie, 'Altijd versleutelde voorbeeldconsoletoepassing'.

Als u Always Encrypted wilt inschakelen, moet u het trefwoord **Kolomversleutelingsinstelling** toevoegen aan uw verbindingstekenreeks en deze instellen op **Ingeschakeld**.

U dit rechtstreeks in de verbindingstekenreeks instellen of u deze instellen met behulp van een [SqlConnectionStringBuilder.](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.aspx) De voorbeeldtoepassing in de volgende sectie laat zien hoe **sqlConnectionstringbuilder**wordt gebruikt.

> [!NOTE]
> Dit is de enige wijziging die nodig is in een clienttoepassing die specifiek is voor Always Encrypted. Als u een bestaande toepassing hebt die de verbindingstekenreeks extern opslaat (dat wil zeggen in een config-bestand), u mogelijk Altijd versleuteld inschakelen zonder code te wijzigen.

### <a name="enable-always-encrypted-in-the-connection-string"></a>Altijd versleuteld inschakelen in de verbindingstekenreeks

Voeg het volgende trefwoord toe aan uw verbindingstekenreeks:

    Column Encryption Setting=Enabled

### <a name="enable-always-encrypted-with-a-sqlconnectionstringbuilder"></a>Always Encrypted inschakelen met een SqlConnectionStringBuilder

In de volgende code ziet u hoe u Always Encrypted inschakelt door de [SqlConnectionStringBuilder.ColumnEncryptionSetting](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting.aspx) in te stellen op [Ingeschakeld](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectioncolumnencryptionsetting.aspx).

    // Instantiate a SqlConnectionStringBuilder.
    SqlConnectionStringBuilder connStringBuilder =
       new SqlConnectionStringBuilder("replace with your connection string");

    // Enable Always Encrypted.
    connStringBuilder.ColumnEncryptionSetting =
       SqlConnectionColumnEncryptionSetting.Enabled;

## <a name="always-encrypted-sample-console-application"></a>Always Encrypted sample console applicatie Always Encrypted sample console application Always Encrypted sample console application Always Encrypt

In dit voorbeeld wordt uitgelegd hoe:

* Wijzig uw verbindingstekenreeks om Always Encrypted in te schakelen.
* Gegevens invoegen in de versleutelde kolommen.
* Selecteer een record door te filteren op een specifieke waarde in een versleutelde kolom.

Vervang de inhoud van **Program.cs** door de volgende code. Vervang de verbindingstekenreeks voor de globale verbindingstekenreeksvariabele in de lijn direct boven de hoofdmethode door uw geldige verbindingstekenreeks van de Azure-portal. Dit is de enige wijziging die u moet aanbrengen in deze code.

Voer de app uit om Always Encrypted in actie te zien.

```cs
using System;
using System.Collections.Generic;
using System.Data;
using System.Data.SqlClient;
using System.Globalization;

namespace AlwaysEncryptedConsoleApp
{
    class Program
    {
        // Update this line with your Clinic database connection string from the Azure portal.
        static string connectionString = @"Data Source = SPE-T640-01.sys-sqlsvr.local; Initial Catalog = Clinic; Integrated Security = true";

        static void Main(string[] args)
        {
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

            CultureInfo culture = CultureInfo.CreateSpecificCulture("en-US");
            InsertPatient(new Patient()
            {
                SSN = "999-99-0001",
                FirstName = "Orlando",
                LastName = "Gee",
                BirthDate = DateTime.Parse("01/04/1964", culture)
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0002",
                FirstName = "Keith",
                LastName = "Harris",
                BirthDate = DateTime.Parse("06/20/1977", culture)
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0003",
                FirstName = "Donna",
                LastName = "Carreras",
                BirthDate = DateTime.Parse("02/09/1973", culture)
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0004",
                FirstName = "Janet",
                LastName = "Gates",
                BirthDate = DateTime.Parse("08/31/1985", culture)
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0005",
                FirstName = "Lucy",
                LastName = "Harrington",
                BirthDate = DateTime.Parse("05/06/1993", culture)
            });


            // Fetch and display all patients.
            Console.WriteLine(Environment.NewLine + "All the records currently in the Patients table:");

            foreach (Patient patient in SelectAllPatients())
            {
                Console.WriteLine(patient.FirstName + " " + patient.LastName + "\tSSN: " + patient.SSN + "\tBirthdate: " + patient.BirthDate);
            }

            // Get patients by SSN.
            Console.WriteLine(Environment.NewLine + "Now let's locate records by searching the encrypted SSN column.");

            string ssn;

            // This very simple validation only checks that the user entered 11 characters.
            // In production be sure to check all user input and use the best validation for your specific application.
            do
            {
                Console.WriteLine("Please enter a valid SSN (ex. 123-45-6789):");
                ssn = Console.ReadLine();
            } while (ssn.Length != 11);

            // The example allows duplicate SSN entries so we will return all records
            // that match the provided value and store the results in selectedPatients.
            Patient selectedPatient = SelectPatientBySSN(ssn);

            // Check if any records were returned and display our query results.
            if (selectedPatient != null)
            {
                Console.WriteLine("Patient found with SSN = " + ssn);
                Console.WriteLine(selectedPatient.FirstName + " " + selectedPatient.LastName + "\tSSN: "
                    + selectedPatient.SSN + "\tBirthdate: " + selectedPatient.BirthDate);
            }
            else
            {
                Console.WriteLine("No patients found with SSN = " + ssn);
            }

            Console.WriteLine("Press Enter to exit...");
            Console.ReadLine();
        }


        static int InsertPatient(Patient newPatient)
        {
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

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();
                }
                catch (Exception ex)
                {
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


        static List<Patient> SelectAllPatients()
        {
            List<Patient> patients = new List<Patient>();


            SqlCommand sqlCmd = new SqlCommand(
              "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients]",
                new SqlConnection(connectionString));


            using (sqlCmd.Connection = new SqlConnection(connectionString))

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patients.Add(new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            });
                        }
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }

            return patients;
        }


        static Patient SelectPatientBySSN(string ssn)
        {
            Patient patient = new Patient();

            SqlCommand sqlCmd = new SqlCommand(
                "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients] WHERE [SSN]=@SSN",
                new SqlConnection(connectionString));

            SqlParameter paramSSN = new SqlParameter(@"@SSN", ssn);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            sqlCmd.Parameters.Add(paramSSN);


            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patient = new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            };
                        }
                    }
                    else
                    {
                        patient = null;
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }
            return patient;
        }


        // This method simply deletes all records in the Patients table to reset our demo.
        static int ResetPatientsTable()
        {
            int returnValue = 0;

            SqlCommand sqlCmd = new SqlCommand("DELETE FROM Patients");
            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();

                }
                catch (Exception ex)
                {
                    returnValue = 1;
                }
            }
            return returnValue;
        }
    }

    class Patient
    {
        public string SSN { get; set; }
        public string FirstName { get; set; }
        public string LastName { get; set; }
        public DateTime BirthDate { get; set; }
    }
}
```

## <a name="verify-that-the-data-is-encrypted"></a>Controleren of de gegevens zijn versleuteld

U snel controleren of de werkelijke gegevens op de server worden versleuteld door de **gegevens van de patiënt** met SSMS op te vragen. (Gebruik uw huidige verbinding waarbij de instelling voor kolomversleuteling nog niet is ingeschakeld.)

Voer de volgende query uit in de database van de kliniek.

    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

U zien dat de versleutelde kolommen geen plaintext-gegevens bevatten.

   ![Nieuwe consoletoepassing](./media/sql-database-always-encrypted/ssms-encrypted.png)

Als u SSMS wilt gebruiken om toegang te krijgen tot de plaintext-gegevens, u de parameter **Kolomversleuteling=ingeschakeld** aan de verbinding toevoegen.

1. Klik in SSMS met de rechtermuisknop op de server in **Object Explorer**en klik vervolgens op **Verbreken**.
2. Klik **op Databaseengine** > **verbinden** om het venster Verbinding maken met server **te** openen en klik vervolgens op **Opties**.
3. Klik **op Extra verbindingsparameters** en typ **KolomversleutelingSinstelling=ingeschakeld**.

    ![Nieuwe consoletoepassing](./media/sql-database-always-encrypted/ssms-connection-parameter.png)
4. Voer de volgende query uit in de database van de **kliniek.**

        SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

     U nu de plaintext-gegevens in de versleutelde kolommen zien.

    ![Nieuwe consoletoepassing](./media/sql-database-always-encrypted/ssms-plaintext.png)

> [!NOTE]
> Als u verbinding maakt met SSMS (of een client) vanaf een andere computer, heeft deze geen toegang tot de versleutelingssleutels en kan deze de gegevens niet decoderen.

## <a name="next-steps"></a>Volgende stappen

Nadat u een database hebt gemaakt die Always Encrypted gebruikt, u het volgende doen:

* Voer dit voorbeeld uit vanaf een andere computer. Het heeft geen toegang tot de encryptiesleutels, dus het heeft geen toegang tot de plaintext-gegevens en wordt niet met succes uitgevoerd.
* [Draai en ruim je sleutels op.](https://msdn.microsoft.com/library/mt607048.aspx)
* [Migreer gegevens die al zijn versleuteld met Always Encrypted](https://msdn.microsoft.com/library/mt621539.aspx).
* Altijd versleutelde certificaten implementeren [voor andere clientmachines](https://msdn.microsoft.com/library/mt723359.aspx#Anchor_1) (zie de sectie Certificaten beschikbaar maken voor toepassingen en gebruikers).

## <a name="related-information"></a>Gerelateerde informatie

* [Altijd versleuteld (clientontwikkeling)](https://msdn.microsoft.com/library/mt147923.aspx)
* [Transparante gegevensversleuteling](https://msdn.microsoft.com/library/bb934049.aspx)
* [SQL Server-versleuteling](https://msdn.microsoft.com/library/bb510663.aspx)
* [Wizard Altijd versleuteld](https://msdn.microsoft.com/library/mt459280.aspx)
* [Altijd versleutelde blog](https://blogs.msdn.com/b/sqlsecurity/archive/tags/always-encrypted/)