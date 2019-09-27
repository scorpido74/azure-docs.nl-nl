---
title: bestand opnemen
description: bestand opnemen
author: jonels-msft
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: include
ms.date: 09/12/2019
ms.author: jonels
ms.custom: include file
ms.openlocfilehash: fadbcf04f1cd474cf2d23963e88016d240272263
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71279884"
---
Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij [Azure Portal](https://portal.azure.com).

## <a name="create-an-azure-database-for-postgresql---hyperscale-citus"></a>Een Azure Database for PostgreSQL-grootschalige maken (Citus)

Volg deze stappen voor het maken van een Azure Database voor PostgreSQL-server:
1. Klik in de linkerbovenhoek van Azure Portal op **Een resource maken**.
2. Selecteer **Databases** op de pagina **Nieuw** en selecteer **Azure Database voor PostgreSQL** op de pagina **Databases**.
3. Voor de implementatie optie klikt u op de knop **maken** onder **grootschalige (Citus)-Server groep-preview.**
4. Vul het formulier voor de gegevens van de nieuwe server als volgt in:
   - Resource groep: Klik op de koppeling **nieuwe maken** onder het tekstvak voor dit veld. Voer een naam in, zoals **myresourcegroup**.
   - Naam Server groep: Voer een unieke naam in voor de nieuwe server groep die ook wordt gebruikt voor een subdomein van de server.
   - Beheerder gebruikers naam: momenteel moet de waarde **Citus**zijn en kan niet worden gewijzigd.
   - Wacht woord: moet ten minste acht tekens lang zijn en tekens bevatten uit drie van de volgende categorieën: Nederlandse hoofd letters, Nederlandse kleine letters, cijfers (0-9) en niet-alfanumerieke tekens (!, $, #,%, enzovoort).
   - Locatie: gebruik de locatie die zich het dichtst bij uw gebruikers bevindt om hen de snelste toegang tot de gegevens te geven.

   > [!IMPORTANT]
   > Het beheerders wachtwoord voor de server dat u hier opgeeft, is vereist voor aanmelding bij de server en de bijbehorende data bases. Onthoud of noteer deze informatie voor later gebruik.

5. Klik op **Server groep configureren**. Laat de instellingen in die sectie ongewijzigd en klik op **Opslaan**.
6. Klik op **Volgende: Netwerk >** aan de onderkant van het scherm.

7. Klik op het tabblad **netwerken** op het keuze rondje **open bare eind punt** .
   ![Openbaar eind punt geselecteerd](./media/azure-postgresql-hyperscale-create-db/network-public-endpoint.png)
8. Klik op de koppeling **+ huidige client-IP-adres toevoegen**.
   ![Client-IP toegevoegd](./media/azure-postgresql-hyperscale-create-db/network-add-client-ip.png)

   > [!NOTE]
   > De Azure PostgreSQL-server communiceert via poort 5432. Als u verbinding probeert te maken vanuit een bedrijfsnetwerk, wordt uitgaand verkeer via poort 5432 mogelijk niet toegestaan door de firewall van uw netwerk. In dat geval kunt u geen verbinding maken met uw Azure SQL Database-server, tenzij de IT-afdeling poort 5432 openstelt.
   >

9. Klik op **beoordeling + maken** en vervolgens op **maken** om de server in te richten. De inrichting duurt een paar minuten.
10. De pagina wordt omgeleid om de implementatie te bewaken. Wanneer de status van **uw implementatie** wordt gewijzigd, wordt de **implementatie voltooid**en klikt u op het menu-item **uitvoer** aan de linkerkant van de pagina.
11. De pagina uitvoer bevat een coördinator hostnaam met een knop ernaast om de waarde naar het klem bord te kopiëren. Noteer deze informatie voor later gebruik.

## <a name="connect-to-the-database-using-psql"></a>Verbinding maken met de data base met behulp van psql

Wanneer u uw Azure Database for PostgreSQL-server maakt, wordt er een standaard database met de naam **Citus** gemaakt. Als u verbinding wilt maken met uw database server, hebt u een connection string en het beheerders wachtwoord nodig.

1. De connection string ophalen. Klik op de pagina Server groep op het menu-item **verbindings reeksen** . (Het is onder **instellingen**.) Zoek de teken reeks die is gemarkeerd  **C++ (libpq)** . Het heeft de volgende vorm:

   ```
   host=hostname.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require
   ```

   Kopieer de teken reeks. U moet {uw\_wacht woord} vervangen door het beheerders wachtwoord dat u eerder hebt gekozen. Uw Lees bare wacht woord wordt niet opgeslagen in het systeem en kan dus niet worden weer gegeven in de connection string.

2. Open een Terminal venster op de lokale computer.

3. Maak verbinding met uw Azure Database for PostgreSQL-server via het hulp programma [psql](https://www.postgresql.org/docs/current/app-psql.html) . Geef uw connection string in aanhalings tekens en controleer of het uw wacht woord bevat:
   ```bash
   psql "{connection_string}"
   ```

   Met de volgende opdracht maakt u bijvoorbeeld verbinding met het coördinator knooppunt van de Server groep **mydemoserver**:

   ```bash
   psql "host=mydemoserver-c.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require"
   ```
