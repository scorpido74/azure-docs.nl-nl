---
title: Back-up maken en herstellen - Azure portal - Azure Database voor MySQL
description: In dit artikel wordt beschreven hoe u een server in Azure Database voor MySQL herstellen met behulp van de Azure-portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/27/2020
ms.openlocfilehash: 619dc05d709f41941d16764bf32b49a0d2a11958
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372992"
---
# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-mysql-using-the-azure-portal"></a>Een server in Azure Database voor MySQL back-upmaken en herstellen met de Azure-portal

## <a name="backup-happens-automatically"></a>Back-up gebeurt automatisch
Er wordt periodiek een back-up gemaakt van Azure Database voor MySQL-servers om herstelfuncties in te schakelen. Met deze functie u de server en al zijn databases herstellen naar een eerder point-in-time, op een nieuwe server.

## <a name="prerequisites"></a>Vereisten
Om deze handleiding te voltooien, moet u het:
- Een [Azure-database voor MySQL-server en -database](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-backup-configuration"></a>Back-upconfiguratie instellen

U maakt de keuze tussen het configureren van uw server voor lokaal redundante back-ups of geografisch redundante back-ups bij het maken van de server, in het venster **Prijslaag.**

> [!NOTE]
> Nadat een server is gemaakt, kan het soort redundantie dat het heeft, geografisch redundant vs lokaal redundant, niet worden geschakeld.
>

Tijdens het maken van een server via de Azure-portal selecteert u in het venster **Prijslaag** **lokaal redundante** of **geografisch redundante** back-ups voor uw server. In dit venster selecteert u ook de **bewaarperiode voor back-ups** - hoe lang (in aantal dagen) u de serverback-ups wilt opslaan.

   ![Prijscategorie - Kies back-upredundantie](./media/howto-restore-server-portal/pricing-tier.png)

Zie de Snelstart van de [Azure Database voor MySQL-server voor](quickstart-create-mysql-server-database-using-azure-portal.md)meer informatie over het instellen van deze waarden tijdens het maken.

De bewaartermijn voor back-ups kan op een server worden gewijzigd via de volgende stappen:
1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Selecteer uw Azure Database voor MySQL-server. Met deze actie wordt de pagina **Overzicht** geopend.
3. Selecteer **Prijsniveau** in het menu onder **INSTELLINGEN**. Met de schuifregelaar u de **reservebewaarperiode** wijzigen in uw voorkeur tussen 7 en 35 dagen.
In de screenshot hieronder is verhoogd tot 34 dagen.
![Bewaarperiode voor back-ups verlengd](./media/howto-restore-server-portal/3-increase-backup-days.png)

4. Klik op **OK** om de wijziging te bevestigen.

De back-upbewaarperiode bepaalt hoe ver terug in de tijd een point-in-time herstel kan worden opgehaald, omdat deze is gebaseerd op beschikbare back-ups. Point-in-time restore wordt verder beschreven in de volgende sectie. 

## <a name="point-in-time-restore"></a>Terugzetten naar eerder tijdstip
Met Azure Database for MySQL u de server terugzetten naar een point-in-time en naar een nieuwe kopie van de server. U deze nieuwe server gebruiken om uw gegevens te herstellen of uw clienttoepassingen naar deze nieuwe server laten wijzen.

Als een tabel bijvoorbeeld vandaag om 12.00 uur per ongeluk is gedropt, u de tijd net voor het middaguur herstellen en de ontbrekende tabel en gegevens ophalen uit die nieuwe kopie van de server. Point-in-time restore is op serverniveau, niet op databaseniveau.

De volgende stappen herstellen de voorbeeldserver naar een point-in-time:
1. Selecteer in de Azure-portal uw Azure-database voor MySQL-server. 

2. Selecteer Op de werkbalk van de **pagina Overzicht** van de server de optie **Herstellen**.

   ![Azure Database voor MySQL - Overzicht - Knop Herstellen](./media/howto-restore-server-portal/2-server.png)

3. Vul in het formulier Restore de vereiste gegevens in:

   ![Azure Database voor MySQL - Informatie herstellen](./media/howto-restore-server-portal/3-restore.png)
   - **Herstelpunt:** selecteer het in-time-punt waarmee u wilt herstellen.
   - **Doelserver:** geef een naam op voor de nieuwe server.
   - **Locatie**: U het gebied niet selecteren. Standaard is het hetzelfde als de bronserver.
   - **Prijscategorie:** u deze parameters niet wijzigen wanneer u een point-in-time-herstel doet. Deze is hetzelfde als de bronserver. 

4. Klik op **OK** om de server te herstellen om te herstellen naar een point-in-time. 

5. Zoek de nieuwe server die is gemaakt om te controleren of de gegevens zijn hersteld zoals verwacht.

De nieuwe server die is gemaakt door point-in-time restore heeft dezelfde naam en wachtwoord voor de serverbeheerder die geldig was voor de bestaande server op het gekozen punt-in-de-tijd. U het wachtwoord wijzigen op de **pagina Overzicht** van de nieuwe server.

De nieuwe server die tijdens een herstel is gemaakt, heeft niet de VNet-serviceeindpunten die op de oorspronkelijke server bestonden. Deze regels moeten afzonderlijk worden ingesteld voor deze nieuwe server. Firewallregels van de oorspronkelijke server worden hersteld.

## <a name="geo-restore"></a>Geo-herstel
Als u uw server hebt geconfigureerd voor geografisch redundante back-ups, kan een nieuwe server worden gemaakt met de back-up van die bestaande server. Deze nieuwe server kan worden gemaakt in elke regio waar Azure Database voor MySQL beschikbaar is.  

1. Selecteer de knop **Een resource maken** (+) in de linkerbovenhoek van de portal. Selecteer **Azure** > **Database databases voor MySQL**.

   ![De optie 'Azure Database for MySQL'.](./media/howto-restore-server-portal/2_navigate-to-mysql.png)

2. Kies **back-up**in de **vervolgkeuzelijst Bron selecteren** in het formulier. Met deze actie wordt een lijst geladen met servers waarop georedundante back-ups zijn ingeschakeld. Selecteer een van deze back-ups als bron van uw nieuwe server.
   ![Bron selecteren: back-up en lijst met georedundante back-ups](./media/howto-restore-server-portal/2-georestore.png)

   > [!NOTE]
   > Wanneer een server voor het eerst wordt gemaakt, is deze mogelijk niet onmiddellijk beschikbaar voor geoherstel. Het kan enkele uren duren voordat de benodigde metagegevens zijn ingevuld.
   >

3. Vul de rest van het formulier in met uw voorkeuren. U elke **locatie**selecteren. Nadat u de locatie hebt geselecteerd, u **Prijsniveau**selecteren. Standaard worden de parameters voor de bestaande server waarvan u herstelt weergegeven. U op **OK** klikken zonder wijzigingen aan te brengen om deze instellingen over te nemen. U **compute generation wijzigen** (indien beschikbaar in de regio die u hebt gekozen), het aantal **vCores,** **back-upbewaarperiode**en **de optie Back-upredundantie**. Het wijzigen **van de prijslaag** (basis, algemeen doel of geheugengeoptimaliseerd) of **de grootte van de opslag** tijdens het herstellen wordt niet ondersteund.

De nieuwe server die door georestore is gemaakt, heeft dezelfde inlognaam en wachtwoord voor de serverbeheerder die geldig was voor de bestaande server op het moment dat het herstel werd gestart. Het wachtwoord kan worden gewijzigd vanaf de **pagina Overzicht** van de nieuwe server.

De nieuwe server die tijdens een herstel is gemaakt, heeft niet de VNet-serviceeindpunten die op de oorspronkelijke server bestonden. Deze regels moeten afzonderlijk worden ingesteld voor deze nieuwe server. Firewallregels van de oorspronkelijke server worden hersteld.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over de [back-ups](concepts-backup.md) van de service
- Meer informatie over [replica's](concepts-read-replicas.md)
- Meer informatie over opties voor [bedrijfscontinuïteit](concepts-business-continuity.md)
