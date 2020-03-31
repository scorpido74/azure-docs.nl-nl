---
title: Back-up en herstel - Azure portal - Azure Database voor PostgreSQL - Single Server
description: In dit artikel wordt beschreven hoe u een server in Azure Database voor PostgreSQL - Single Server herstellen met behulp van de Azure-portal.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/25/2019
ms.openlocfilehash: fb13e4f062976e39c3cec607001e6982db228873
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74765627"
---
# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Een server in Azure Database voor PostgreSQL - Single Server back-uppen en herstellen met de Azure-portal

## <a name="backup-happens-automatically"></a>Back-up gebeurt automatisch
Er wordt periodiek een back-up gemaakt van Azure Database voor PostgreSQL-servers om herstelfuncties in te schakelen. Met deze functie u de server en al zijn databases herstellen naar een eerder point-in-time, op een nieuwe server.

## <a name="set-backup-configuration"></a>Back-upconfiguratie instellen

U maakt de keuze tussen het configureren van uw server voor lokaal redundante back-ups of geografisch redundante back-ups bij het maken van de server, in het venster **Prijslaag.**

> [!NOTE]
> Nadat een server is gemaakt, kan het soort redundantie dat het heeft, geografisch redundant vs lokaal redundant, niet worden geschakeld.
>

Tijdens het maken van een server via de Azure-portal selecteert u in het venster **Prijslaag** **lokaal redundante** of **geografisch redundante** back-ups voor uw server. In dit venster selecteert u ook de **bewaarperiode voor back-ups** - hoe lang (in aantal dagen) u de serverback-ups wilt opslaan.

   ![Prijscategorie - Kies back-upredundantie](./media/howto-restore-server-portal/pricing-tier.png)

Zie de Snelstart van de [Azure Database voor PostgreSQL-server voor](quickstart-create-server-database-portal.md)meer informatie over het instellen van deze waarden tijdens het maken.

De back-upbewaarperiode van een server kan worden gewijzigd via de volgende stappen:
1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Selecteer uw Azure Database for PostgreSQL-server. Met deze actie wordt de pagina **Overzicht** geopend.
3. Selecteer **Prijsniveau** in het menu onder **INSTELLINGEN**. Met de schuifregelaar u de **reservebewaarperiode** wijzigen in uw voorkeur tussen 7 en 35 dagen.
In de screenshot hieronder is verhoogd tot 34 dagen.
![Bewaarperiode voor back-ups verlengd](./media/howto-restore-server-portal/3-increase-backup-days.png)

4. Klik op **OK** om de wijziging te bevestigen.

De back-upbewaarperiode bepaalt hoe ver terug in de tijd een point-in-time herstel kan worden opgehaald, omdat deze is gebaseerd op beschikbare back-ups. Point-in-time restore wordt verder beschreven in de volgende sectie. 

## <a name="point-in-time-restore"></a>Terugzetten naar eerder tijdstip
Met Azure Database voor PostgreSQL u de server terugzetten naar een point-in-time en naar een nieuwe kopie van de server. U deze nieuwe server gebruiken om uw gegevens te herstellen of uw clienttoepassingen naar deze nieuwe server laten wijzen.

Als een tabel bijvoorbeeld vandaag om 12.00 uur per ongeluk is gedropt, u de tijd net voor het middaguur herstellen en de ontbrekende tabel en gegevens ophalen uit die nieuwe kopie van de server. Point-in-time restore is op serverniveau, niet op databaseniveau.

De volgende stappen herstellen de voorbeeldserver naar een point-in-time:
1. Selecteer in de Azure-portal uw Azure-database voor PostgreSQL-server. 

2. Selecteer Op de werkbalk van de **pagina Overzicht** van de server de optie **Herstellen**.

   ![Azure Database voor PostgreSQL - Overzicht - Knop Herstellen](./media/howto-restore-server-portal/2-server.png)

3. Vul in het formulier Restore de vereiste gegevens in:

   ![Azure Database voor PostgreSQL - Informatie herstellen](./media/howto-restore-server-portal/3-restore.png)
   - **Herstelpunt:** selecteer het in-time-punt waarmee u wilt herstellen.
   - **Doelserver:** geef een naam op voor de nieuwe server.
   - **Locatie**: U het gebied niet selecteren. Standaard is het hetzelfde als de bronserver.
   - **Prijscategorie:** u deze parameters niet wijzigen wanneer u een point-in-time-herstel doet. Deze is hetzelfde als de bronserver. 

4. Klik op **OK** om de server te herstellen om te herstellen naar een point-in-time. 

5. Zoek de nieuwe server die is gemaakt om te controleren of de gegevens zijn hersteld zoals verwacht.

De nieuwe server die is gemaakt door point-in-time restore heeft dezelfde naam en wachtwoord voor de serverbeheerder die geldig was voor de bestaande server op het gekozen punt-in-de-tijd. U het wachtwoord wijzigen op de **pagina Overzicht** van de nieuwe server.

De nieuwe server die tijdens een herstel is gemaakt, heeft niet de firewallregels of VNet-serviceeindpunten die op de oorspronkelijke server bestonden. Deze regels moeten afzonderlijk worden ingesteld voor deze nieuwe server.


## <a name="geo-restore"></a>Geo-herstel

Als u uw server hebt geconfigureerd voor geografisch redundante back-ups, kan een nieuwe server worden gemaakt met de back-up van die bestaande server. Deze nieuwe server kan worden gemaakt in elke regio waar Azure Database voor PostgreSQL beschikbaar is.  

1. Selecteer de knop **Een resource maken** (+) in de linkerbovenhoek van de portal. Selecteer **Azure** > **Database databases voor PostgreSQL**.

   ![De optie Azure Database for PostgreSQL](./media/howto-restore-server-portal/1-navigate-to-postgres.png)

2. Kies **back-up**in de **vervolgkeuzelijst Bron selecteren** in het formulier. Met deze actie wordt een lijst geladen met servers waarop georedundante back-ups zijn ingeschakeld. Selecteer een van deze back-ups als bron van uw nieuwe server.
   ![Bron selecteren: back-up en lijst met georedundante back-ups](./media/howto-restore-server-portal/2-georestore.png)

   > [!NOTE]
   > Wanneer een server voor het eerst wordt gemaakt, is deze mogelijk niet onmiddellijk beschikbaar voor geoherstel. Het kan enkele uren duren voordat de benodigde metagegevens zijn ingevuld.
   >

3. Vul de rest van het formulier in met uw voorkeuren. U elke **locatie**selecteren. Nadat u de locatie hebt geselecteerd, u **Prijsniveau**selecteren. Standaard worden de parameters voor de bestaande server waarvan u herstelt weergegeven. U op **OK** klikken zonder wijzigingen aan te brengen om deze instellingen over te nemen. U **compute generation wijzigen** (indien beschikbaar in de regio die u hebt gekozen), het aantal **vCores,** **back-upbewaarperiode**en **de optie Back-upredundantie**. Het wijzigen **van de prijslaag** (basis, algemeen doel of geheugengeoptimaliseerd) of **de grootte van de opslag** tijdens het herstellen wordt niet ondersteund.


De nieuwe server die door georestore is gemaakt, heeft dezelfde inlognaam en wachtwoord voor de serverbeheerder die geldig was voor de bestaande server op het moment dat het herstel werd gestart. Het wachtwoord kan worden gewijzigd vanaf de **pagina Overzicht** van de nieuwe server.

De nieuwe server die tijdens een herstel is gemaakt, heeft niet de firewallregels of VNet-serviceeindpunten die op de oorspronkelijke server bestonden. Deze regels moeten afzonderlijk worden ingesteld voor deze nieuwe server.


## <a name="next-steps"></a>Volgende stappen
- Meer informatie over de [back-ups](concepts-backup.md)van de service .
- Meer informatie over [opties voor bedrijfscontinuïteit.](concepts-business-continuity.md)
