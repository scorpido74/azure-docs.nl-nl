---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: 22f16a7382cb0fe1f3fe2a6ef5e7c00a6989623c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67175985"
---
## <a name="next-steps"></a>Volgende stappen

Nadat u Azure Key Vault Integration hebt ingeschakeld, u SQL Server-versleuteling inschakelen op uw SQL VM. Eerst moet u een asymmetrische sleutel in uw sleutelkluis maken en een symmetrische sleutel binnen SQL Server op uw VM. Vervolgens u T-SQL-instructies uitvoeren om versleuteling voor uw databases en back-ups mogelijk te maken.

Er zijn verschillende vormen van versleuteling die u gebruiken:

* [Transparent Data Encryption (TDE)](https://msdn.microsoft.com/library/bb934049.aspx)
* [Versleutelde back-ups](https://msdn.microsoft.com/library/dn449489.aspx)
* [Versleuteling van kolomniveau (CLE)](https://msdn.microsoft.com/library/ms173744.aspx)

De volgende Transact-SQL-scripts geven voorbeelden voor elk van deze gebieden.

### <a name="prerequisites-for-examples"></a>Voorwaarden voor voorbeelden

Elk voorbeeld is gebaseerd op de twee vereisten: een asymmetrische sleutel uit uw sleutelkluis genaamd **CONTOSO_KEY** en een referentie die is gemaakt door de AKV-integratiefunctie **Azure_EKM_TDE_cred**. Met de volgende Transact-SQL-opdrachten worden deze vereisten ingesteld voor het uitvoeren van de voorbeelden.

``` sql
USE master;
GO

--create credential
--The <<SECRET>> here requires the <Application ID> (without hyphens) and <Secret> to be passed together without a space between them.
CREATE CREDENTIAL sysadmin_ekm_cred
    WITH IDENTITY = 'keytestvault', --keyvault
    SECRET = '<<SECRET>>'
FOR CRYPTOGRAPHIC PROVIDER AzureKeyVault_EKM_Prov;


--Map the credential to a SQL login that has sysadmin permissions. This allows the SQL login to access the key vault when creating the asymmetric key in the next step.
ALTER LOGIN [SQL_Login]
ADD CREDENTIAL sysadmin_ekm_cred;


CREATE ASYMMETRIC KEY CONTOSO_KEY
FROM PROVIDER [AzureKeyVault_EKM_Prov]
WITH PROVIDER_KEY_NAME = 'KeyName_in_KeyVault',  --The key name here requires the key we created in the key vault
CREATION_DISPOSITION = OPEN_EXISTING;
```

### <a name="transparent-data-encryption-tde"></a>Transparent Data Encryption (TDE)

1. Maak een SQL Server-login die door de Database Engine voor TDE moet worden gebruikt en voeg er vervolgens de referenties aan toe.

   ``` sql
   USE master;
   -- Create a SQL Server login associated with the asymmetric key
   -- for the Database engine to use when it loads a database
   -- encrypted by TDE.
   CREATE LOGIN EKM_Login
   FROM ASYMMETRIC KEY CONTOSO_KEY;
   GO

   -- Alter the TDE Login to add the credential for use by the
   -- Database Engine to access the key vault
   ALTER LOGIN EKM_Login
   ADD CREDENTIAL Azure_EKM_cred;
   GO
   ```

1. Maak de databaseversleutelingssleutel die wordt gebruikt voor TDE.

   ``` sql
   USE ContosoDatabase;
   GO

   CREATE DATABASE ENCRYPTION KEY 
   WITH ALGORITHM = AES_128 
   ENCRYPTION BY SERVER ASYMMETRIC KEY CONTOSO_KEY;
   GO

   -- Alter the database to enable transparent data encryption.
   ALTER DATABASE ContosoDatabase
   SET ENCRYPTION ON;
   GO
   ```

### <a name="encrypted-backups"></a>Versleutelde back-ups

1. Maak een SQL Server-login die door de Database Engine moet worden gebruikt voor het versleutelen van back-ups en de referenties eraan toe te voegen.

   ``` sql
   USE master;
   -- Create a SQL Server login associated with the asymmetric key
   -- for the Database engine to use when it is encrypting the backup.
   CREATE LOGIN EKM_Login
   FROM ASYMMETRIC KEY CONTOSO_KEY;
   GO

   -- Alter the Encrypted Backup Login to add the credential for use by
   -- the Database Engine to access the key vault
   ALTER LOGIN EKM_Login
   ADD CREDENTIAL Azure_EKM_cred ;
   GO
   ```

1. Maak een back-up van de database met versleuteling met de asymmetrische sleutel die is opgeslagen in de sleutelkluis.

   ``` sql
   USE master;
   BACKUP DATABASE [DATABASE_TO_BACKUP]
   TO DISK = N'[PATH TO BACKUP FILE]'
   WITH FORMAT, INIT, SKIP, NOREWIND, NOUNLOAD,
   ENCRYPTION(ALGORITHM = AES_256, SERVER ASYMMETRIC KEY = [CONTOSO_KEY]);
   GO
   ```

### <a name="column-level-encryption-cle"></a>Versleuteling van kolomniveau (CLE)

Dit script creëert een symmetrische sleutel die wordt beschermd door de asymmetrische sleutel in de sleutelkluis en gebruikt vervolgens de symmetrische sleutel om gegevens in de database te versleutelen.

``` sql
CREATE SYMMETRIC KEY DATA_ENCRYPTION_KEY
WITH ALGORITHM=AES_256
ENCRYPTION BY ASYMMETRIC KEY CONTOSO_KEY;

DECLARE @DATA VARBINARY(MAX);

--Open the symmetric key for use in this session
OPEN SYMMETRIC KEY DATA_ENCRYPTION_KEY
DECRYPTION BY ASYMMETRIC KEY CONTOSO_KEY;

--Encrypt syntax
SELECT @DATA = ENCRYPTBYKEY(KEY_GUID('DATA_ENCRYPTION_KEY'), CONVERT(VARBINARY,'Plain text data to encrypt'));

-- Decrypt syntax
SELECT CONVERT(VARCHAR, DECRYPTBYKEY(@DATA));

--Close the symmetric key
CLOSE SYMMETRIC KEY DATA_ENCRYPTION_KEY;
```

## <a name="additional-resources"></a>Aanvullende bronnen

Zie [EKM gebruiken met SQL Server-versleutelingsfuncties](https://msdn.microsoft.com/library/dn198405.aspx#UsesOfEKM)voor meer informatie over het gebruik van deze versleutelingsfuncties.

Houd er rekening mee dat de stappen in dit artikel ervan uitgaan dat SQL Server al op een virtuele Azure-machine wordt uitgevoerd. Zie Een [virtuele SQL Server-machine inAzure inrichten.](../articles/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md) Zie SQL Server op [Azure Virtual Machines-overzicht](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)voor andere richtlijnen voor het uitvoeren van SQL Server op Azure VM's.
