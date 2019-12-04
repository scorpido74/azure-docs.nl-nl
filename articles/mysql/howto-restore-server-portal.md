---
title: Backup en Restore-Azure Portal-Azure Database for MySQL
description: In dit artikel wordt beschreven hoe u een server in Azure Database for MySQL kunt herstellen met behulp van de Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: f8005bccf3a726a6022efdecb2eb5738669f3bc2
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74762621"
---
# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-mysql-using-the-azure-portal"></a>Een back-up maken en herstellen van een server in Azure Database for MySQL met behulp van de Azure Portal

## <a name="backup-happens-automatically"></a>Er wordt automatisch een back-up gemaakt
Er wordt regel matig een back-up van Azure Database for MySQL servers gemaakt om herstel functies in te scha kelen. Met deze functie kunt u de server en alle bijbehorende data bases naar een eerder tijdstip herstellen op een nieuwe server.

## <a name="prerequisites"></a>Vereisten
U hebt het volgende nodig om deze hand leiding te volt ooien:
- Een [Azure database for mysql-server en-data base](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-backup-configuration"></a>Back-upconfiguratie instellen

U maakt de keuze tussen het configureren van uw server voor lokaal redundante back-ups of geografisch redundante back-ups bij het maken van een server, in het venster **prijs categorie** .

> [!NOTE]
> Nadat een server is gemaakt, is het soort redundantie die het heeft, geografisch redundant versus lokaal redundant, niet overgeschakeld.
>

Wanneer u een server maakt via de Azure Portal, selecteert u in het venster **prijs categorie** de optie **lokaal redundante** of **geografisch redundante** back-ups voor uw server. Dit venster is ook de plek waar u de **retentie periode voor back-ups** selecteert-hoe lang (in aantal dagen) u de back-ups van de server wilt opslaan.

   ![Prijs categorie: Kies back-upredundantie](./media/howto-restore-server-portal/pricing-tier.png)

Zie de [Snelstartgids van Azure database for mysql server](quickstart-create-mysql-server-database-using-azure-portal.md)voor meer informatie over het instellen van deze waarden tijdens het maken.

De Bewaar periode voor back-ups kan worden gewijzigd op een server met behulp van de volgende stappen:
1. Meld u aan bij de [Azure Portal](https://portal.azure.com/).
2. Selecteer uw Azure Database for MySQL-server. Met deze actie wordt de pagina **overzicht** geopend.
3. Selecteer **prijs categorie** in het menu onder **instellingen**. Met de schuif regelaar kunt u de **Bewaar periode voor back-ups** wijzigen van 7 tot 35 dagen.
In de onderstaande scherm afbeelding is meer dan 34 dagen verg root.
![Bewaar periode voor back-ups verhoogd](./media/howto-restore-server-portal/3-increase-backup-days.png)

4. Klik op **OK** om de wijziging te bevestigen.

De Bewaar periode voor back-ups bepaalt hoe ver terug in de tijd een herstel naar een bepaald tijdstip kan worden opgehaald, omdat het is gebaseerd op back-ups die beschikbaar zijn. Herstel naar een bepaald tijdstip wordt verder beschreven in de volgende sectie. 

## <a name="point-in-time-restore"></a>Herstel naar een bepaald tijdstip
Met Azure Database for MySQL kunt u de server weer herstellen naar een bepaald tijdstip en naar een nieuwe kopie van de server. U kunt deze nieuwe server gebruiken om uw gegevens te herstellen of uw client toepassingen te laten verwijzen naar deze nieuwe server.

Als een tabel bijvoorbeeld per ongeluk is verwijderd om 12 uur 's middags, kunt u de tijd vóór de middag herstellen en de ontbrekende tabel en gegevens ophalen van de nieuwe kopie van de server. Herstel naar een bepaald tijdstip bevindt zich op server niveau en niet op database niveau.

Met de volgende stappen wordt de voorbeeld server hersteld naar een bepaald tijdstip:
1. Selecteer uw Azure Database for MySQL server in het Azure Portal. 

2. Selecteer in de werk balk van de pagina **overzicht** van de server **herstellen**.

   ![Azure Database for MySQL-overzicht-knop herstellen](./media/howto-restore-server-portal/2-server.png)

3. Vul het formulier Restore in met de vereiste gegevens:

   ![Azure Database for MySQL-herstel informatie](./media/howto-restore-server-portal/3-restore.png)
   - **Herstel punt**: Selecteer het tijdstip waarop u wilt herstellen.
   - **Doel server**: Geef een naam op voor de nieuwe server.
   - **Locatie**: u kunt de regio niet selecteren. Het is standaard hetzelfde als de bron server.
   - **Prijs categorie**: u kunt deze para meters niet wijzigen wanneer u een herstel bewerking op een bepaald tijdstip uitvoert. Deze is hetzelfde als de bronserver. 

4. Klik op **OK** om de server te herstellen om naar een bepaald tijdstip te herstellen. 

5. Zodra de herstel bewerking is voltooid, gaat u naar de nieuwe server die is gemaakt om te controleren of de gegevens naar verwachting zijn teruggezet.

De nieuwe server die door een herstel punt is gemaakt, heeft dezelfde aanmeldings naam en hetzelfde wacht woord voor de server beheerder als de bestaande server op het tijdstip van de tijd. U kunt het wacht woord wijzigen op de pagina **overzicht** van de nieuwe server.

De nieuwe server die tijdens een herstel bewerking is gemaakt, heeft geen firewall regels of VNet-service-eind punten die bestonden op de oorspronkelijke server. Deze regels moeten afzonderlijk worden ingesteld voor deze nieuwe server.

## <a name="geo-restore"></a>Geo-herstel
Als u uw server voor geografisch redundante back-ups hebt geconfigureerd, kan een nieuwe server worden gemaakt op basis van de back-up van die bestaande server. Deze nieuwe server kan worden gemaakt in elke regio die Azure Database for MySQL beschikbaar is.  

1. Selecteer de knop **Een resource maken** (+) in de linkerbovenhoek van de portal. Selecteer **Databases** > **Azure Database voor MySQL**.

   ![De optie Azure Database for MySQL](./media/howto-restore-server-portal/2_navigate-to-mysql.png)

2. Kies in de vervolg keuzelijst **bron selecteren** de optie **back-up**. Met deze actie wordt een lijst met servers geladen waarvoor geo redundante back-ups zijn ingeschakeld. Selecteer een van deze back-ups om de bron van de nieuwe server te zijn.
   ![bron selecteren: back-up en lijst met geo redundante back-ups](./media/howto-restore-server-portal/2-georestore.png)

   > [!NOTE]
   > Wanneer een server voor het eerst wordt gemaakt, is deze mogelijk niet onmiddellijk beschikbaar voor geo Restore. Het kan enkele uren duren voordat de benodigde meta gegevens zijn gevuld.
   >

3. Vul de rest van het formulier in met uw voor keuren. U kunt een wille keurige **locatie**selecteren. Nadat u de locatie hebt geselecteerd, kunt u **prijs categorie**selecteren. Standaard worden de para meters weer gegeven voor de bestaande server waarvan u wilt herstellen. U kunt op **OK** klikken zonder dat u wijzigingen hoeft aan te brengen. Of u kunt de **generatie** van de berekening wijzigen (indien beschikbaar in de regio die u hebt gekozen), het aantal **vCores**, de **Bewaar periode voor back-ups**en de optie voor de **redundantie van back-ups**. Het wijzigen van de **prijs categorie** (Basic, algemeen of Optimized memory) of **opslag** grootte tijdens het terugzetten wordt niet ondersteund.

De nieuwe server die is gemaakt met geo Restore heeft dezelfde aanmeldings naam en hetzelfde wacht woord voor de server beheerder als voor de bestaande server op het moment dat de herstel bewerking werd gestart. Het wacht woord kan worden gewijzigd op de pagina **overzicht** van de nieuwe server.

De nieuwe server die tijdens een herstel bewerking is gemaakt, heeft geen firewall regels of VNet-service-eind punten die bestonden op de oorspronkelijke server. Deze regels moeten afzonderlijk worden ingesteld voor deze nieuwe server.



## <a name="next-steps"></a>Volgende stappen
- Meer informatie over de [back-ups](concepts-backup.md) van de service
- Meer informatie over [replica's](concepts-read-replicas.md)
- Meer informatie over opties voor [bedrijfs continuïteit](concepts-business-continuity.md)
