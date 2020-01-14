---
title: Gegevens versleuteling voor Azure Database for PostgreSQL één server met behulp van portal
description: Meer informatie over het instellen en beheren van gegevens versleuteling voor uw Azure Database for PostgreSQL één server met behulp van Azure Portal
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/10/2020
ms.openlocfilehash: e579866b1df6d32dc2b90a19ac001c381da625a6
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/13/2020
ms.locfileid: "75922800"
---
# <a name="data-encryption-for-azure-database-for-postgresql-single-server-using-portal"></a>Gegevens versleuteling voor Azure Database for PostgreSQL één server met behulp van portal

In dit artikel vindt u informatie over het instellen en beheren van de Azure Portal voor het instellen van gegevens versleuteling voor uw Azure Database for PostgreSQL één server.

## <a name="prerequisites-for-cli"></a>Vereisten voor CLI

* U moet een Azure-abonnement hebben en een beheerder van dat abonnement zijn.
* Maak een Azure Key Vault en een sleutel om te gebruiken voor door de klant beheerde sleutel.
* De sleutel kluis moet de volgende eigenschap hebben om te kunnen worden gebruikt als een door de klant beheerde sleutel
    * [Voorlopig verwijderen](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)

        ```azurecli-interactive
        az resource update --id $(az keyvault show --name \ <key_valut_name> -test -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
        ```
    
    * [Beveiligd leegmaken](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection)

        ```azurecli-interactive
        az keyvault update --name <key_valut_name> --resource-group <resource_group_name>  --enable-purge-protection true
        ```

* De sleutel moet de volgende kenmerken hebben om te kunnen worden gebruikt voor door de klant beheerde sleutel.
    * Geen verval datum
    * Niet uitgeschakeld
    * Kan de Get-, terugloop-, sleutel bewerking uitpakken

## <a name="setting-the-right-permissions-for-key-operations"></a>De juiste machtigingen instellen voor belang rijke bewerkingen

1. Selecteer op de Azure Key Vault het **toegangs beleid** en voeg het **toegangs beleid toe** 

   ![Overzicht van toegangs beleid](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. Onder de **sleutel machtigingen** selecteert u **Get**, **wrap**, **dewrap** en de **Principal**. Dit is de naam van de postgresql-server. Als uw server-Principal niet kan worden gevonden in de lijst met bestaande principals, moet u deze registreren door de gegevens versleuteling voor de eerste keer in te stellen. Dit kan mislukken.  

   ![Overzicht van toegangs beleid](media/concepts-data-access-and-security-data-encryption/access-policy-warp-unwrap.png)

3. **Sla** de instellingen op.

## <a name="setting-data-encryption-for-azure-database-for-postgresql-single-server"></a>Gegevens versleuteling instellen voor Azure Database for PostgreSQL één server

1. Selecteer de **gegevens versleuteling** in het **Azure database for PostgreSQL**om de door de klant beheerde sleutel in te stellen.

   ![Gegevens versleuteling instellen](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. U kunt een **Key Vault** en **sleutel** paar selecteren of een sleutel- **id**door geven.

   ![Key Vault instellen](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. **Sla** de instellingen op.

4. Om ervoor te zorgen dat alle bestanden (inclusief **tijdelijke bestanden**) volledig zijn versleuteld, moet de server opnieuw worden **opgestart** .

## <a name="restoring-or-creating-replica-of-the-server-which-has-data-encryption-enabled"></a>Replica van de server herstellen of maken, waarvoor gegevens versleuteling is ingeschakeld

Zodra een Azure Database for PostgreSQL één server is versleuteld met de door de klant beheerde sleutel die is opgeslagen in de Key Vault, wordt een nieuw gemaakt exemplaar van de server, ongeacht de lokale of geo-herstel bewerking of een replica (lokale/cross-Region). Voor een versleutelde PostgreSQL-server kunt u de onderstaande stappen uitvoeren om een versleutelde herstelde server te maken.

1. Selecteer op uw server **overzicht**en selecteer vervolgens **herstellen**.

   ![Initiëren/herstellen](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   Of voor een server waarvoor replicatie is ingeschakeld, selecteert u in de kop **instellingen** de optie **replicatie**

   ![Initiëren-replica](media/concepts-data-access-and-security-data-encryption/postgresql-replica.png)

2. Zodra de herstel bewerking is voltooid, wordt de nieuwe server gemaakt en worden de gegevens versleuteld met de sleutel van de primaire server. De functies en opties op de server zijn echter uitgeschakeld en de server is gemarkeerd met een niet- **toegankelijke** status. Dit is om te voor komen dat gegevens worden gemanipuleerd omdat de identiteit van de nieuwe server nog steeds geen toegangs machtigingen heeft gekregen voor de Key Vault.

   ![Server markeren als niet toegankelijk](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)


3. Als u de niet-toegankelijke status wilt herstellen, moet u de sleutel op de herstelde server opnieuw valideren. Klik op de Blade **gegevens versleuteling** en vervolgens op de knop **sleutel opnieuw valideren** .

   > [!NOTE]
   > De eerste poging om opnieuw te valideren, mislukt omdat de service-principal van de nieuwe server toegang moet krijgen tot de sleutel kluis. Als u de Service-Principal wilt genereren, klikt u op de **sleutel opnieuw valideren**, waardoor er een fout optreedt, maar wordt de Service-Principal gegenereerd. Daarna raadpleegt u de stappen [in sectie 2](https://docs.microsoft.com/azure/postgresql/howto-data-encryption-portal#setting-the-right-permissions-for-key-operations) hierboven.

   ![server opnieuw valideren](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   U moet toegang tot de nieuwe server verlenen aan de Key Vault. 

4. Nadat u de Service-Principal hebt geregistreerd, moet u de sleutel opnieuw valideren en wordt de normale functionaliteit van de server hervat.

   ![De normale server is hersteld](media/concepts-data-access-and-security-data-encryption/restore-successful.png)

## <a name="next-steps"></a>Volgende stappen

 Zie [Wat is Azure Data Encryption](concepts-data-encryption-postgresql.md)? voor meer informatie over gegevens versleuteling.