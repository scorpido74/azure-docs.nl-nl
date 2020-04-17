---
title: Gegevensversleuteling - Azure-portal - voor Azure Database voor PostgreSQL - Enkele server
description: Meer informatie over het instellen en beheren van gegevensversleuteling voor uw Azure Database voor PostgreSQL Single server met behulp van de Azure-portal.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 07e103c3e1f56e8a46ea24e750d83e719abab3d5
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457974"
---
# <a name="data-encryption-for-azure-database-for-postgresql-single-server-by-using-the-azure-portal"></a>Gegevensversleuteling voor Azure Database voor PostgreSQL Single-server met behulp van de Azure-portal

Meer informatie over het gebruik van de Azure-portal voor het instellen en beheren van gegevensversleuteling voor uw Azure Database voor PostgreSQL Single-server.

## <a name="prerequisites-for-azure-cli"></a>Voorwaarden voor Azure CLI

* U moet een Azure-abonnement hebben en een beheerder zijn bij dat abonnement.
* Maak in Azure Key Vault een sleutelkluis en sleutelsleutel die u gebruiken voor een door de klant beheerde sleutel.
* De sleutelkluis moet de volgende eigenschappen hebben om te gebruiken als een door de klant beheerde sleutel:
  * [Zachte verwijderen](../key-vault/general/overview-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -test -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Gezuiverd beschermd](../key-vault/general/overview-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* De sleutel moet de volgende kenmerken hebben om te gebruiken als een door de klant beheerde sleutel:
  * Geen vervaldatum
  * Niet uitgeschakeld
  * In staat om belangrijke bewerkingen te krijgen, verpakken en uitpakken

## <a name="set-the-right-permissions-for-key-operations"></a>De juiste machtigingen instellen voor belangrijke bewerkingen

1. Selecteer **access-beleid** > In Key Vault selecteer**je Toegangsbeleid toevoegen**.

   ![Schermafbeelding van Key Vault, met toegangsbeleid en Toegangsbeleid toevoegen gemarkeerd](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. Selecteer **Belangrijke machtigingen**en selecteer **Get**, **Wrap**, **Uitpakken**en de **principal,** de naam van de PostgreSQL-server. Als uw serverprincipal niet kan worden gevonden in de lijst met bestaande principals, moet u deze registreren. U wordt gevraagd om uw serverprincipal te registreren wanneer u voor de eerste keer gegevensversleuteling probeert in te stellen en dit mislukt.  

   ![Overzicht van toegangsbeleid](media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png)

3. Selecteer **Opslaan**.

## <a name="set-data-encryption-for-azure-database-for-postgresql-single-server"></a>Gegevensversleuteling instellen voor Azure Database voor PostgreSQL Single-server

1. Selecteer **gegevensversleuteling in** Azure Database voor PostgreSQL om de door de klant beheerde sleutel in te stellen.

   ![Schermafbeelding van Azure Database voor PostgreSQL, met gemarkeerde gegevensversleuteling](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. U een sleutelkluis en sleutelpaar selecteren of een sleutel-id invoeren.

   ![Schermafbeelding van Azure Database voor PostgreSQL, met gemarkeerde opties voor gegevensversleuteling](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. Selecteer **Opslaan**.

4. Start de server opnieuw op om ervoor te zorgen dat alle bestanden (inclusief tijdelijke bestanden) volledig zijn versleuteld.

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>Gegevensversleuteling gebruiken voor herstel- of replicaservers

Nadat Azure Database voor PostgreSQL Single-server is versleuteld met de beheerde sleutel van een klant die is opgeslagen in Key Vault, wordt ook elke nieuw gemaakte kopie van de server versleuteld. U deze nieuwe kopie maken via een lokale of geoherstelbewerking of via een bewerking voor replica's (lokaal/regio). Voor een versleutelde PostgreSQL-server u dus de volgende stappen gebruiken om een versleutelde herstelde server te maken.

1. Selecteer **Overzicht** > **herstellen**op uw server .

   ![Schermafbeelding van Azure Database voor PostgreSQL, met overzicht en herstel gemarkeerd](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   Of selecteer **Replicatie**voor een server **Settings** met replicatiedie is ingeschakeld.

   ![Schermafbeelding van Azure Database voor PostgreSQL, met replicatie gemarkeerd](media/concepts-data-access-and-security-data-encryption/postgresql-replica.png)

2. Nadat de herstelbewerking is voltooid, wordt de nieuwe server die is gemaakt, versleuteld met de sleutel van de primaire server. De functies en opties op de server zijn echter uitgeschakeld en de server is niet toegankelijk. Dit voorkomt gegevensmanipulatie, omdat de identiteit van de nieuwe server nog geen toestemming heeft gekregen om toegang te krijgen tot de sleutelkluis.

   ![Schermafbeelding van Azure Database voor PostgreSQL, met ontoegankelijke status gemarkeerd](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. Als u de server toegankelijk wilt maken, moet u de sleutel op de herstelserver opnieuw valideren. Selecteer de sleutel voor **gegevensversleuteling** > **opnieuw valideren**.

   > [!NOTE]
   > De eerste poging om opnieuw te valideren mislukt, omdat de serviceprincipal van de nieuwe server toegang moet krijgen tot de sleutelkluis. Als u de serviceprincipal wilt genereren, selecteert u **De sleutel opnieuw valideren,** waardoor een fout wordt weergegeven, maar de serviceprincipal wordt gegenereerd. Daarna, verwijzen naar [deze stappen](#set-the-right-permissions-for-key-operations) eerder in dit artikel.

   ![Schermafbeelding van Azure Database voor PostgreSQL, waarbij de validatiestap is gemarkeerd](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   U moet de sleutelkluis toegang geven tot de nieuwe server.

4. Nadat u de serviceprincipal hebt geregistreerd, wordt de sleutel opnieuw gevalideerd en wordt de normale functionaliteit van de server hervat.

   ![Schermafbeelding van Azure Database voor PostgreSQL, met herstelde functionaliteit](media/concepts-data-access-and-security-data-encryption/restore-successful.png)

## <a name="next-steps"></a>Volgende stappen

 Zie Azure Database voor [PostgreSQL Single server-gegevensversleuteling met door de klant beheerde sleutel voor](concepts-data-encryption-postgresql.md)meer informatie over gegevensversleuteling.
