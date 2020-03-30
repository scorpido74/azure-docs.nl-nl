---
title: Firewallregels beheren - Hyperscale (Citus) - Azure Database voor PostgreSQL
description: Firewallregels maken en beheren voor Azure Database voor PostgreSQL - Hyperscale (Citus) met behulp van de Azure-portal
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: 660c395e6cff81b0abcac07e66385f80a538695f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74977536"
---
# <a name="manage-firewall-rules-for-azure-database-for-postgresql---hyperscale-citus"></a>Firewallregels voor Azure Database voor PostgreSQL beheren - Hyperscale (Citus)
Firewallregels op serverniveau kunnen worden gebruikt om de toegang tot een Citus-coördinator (Hyperscale) te beheren vanaf een opgegeven IP-adres of een reeks IP-adressen.

## <a name="prerequisites"></a>Vereisten
Om deze handleiding door te nemen, hebt u het volgende nodig:
- Een servergroep [Een Azure-database maken voor de servergroep PostgreSQL – Hyperscale (Citus).](quickstart-create-hyperscale-portal.md)

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Een serverfirewallregel maken in Azure Portal

> [!NOTE]
> Deze instellingen zijn ook toegankelijk tijdens het maken van een Azure Database voor PostgreSQL - Hyperscale (Citus) servergroep. Klik onder het tabblad **Netwerken** op **Openbaar eindpunt**.
> ![Azure-portal - tabblad Netwerken](./media/howto-hyperscale-manage-firewall-using-portal/0-create-public-access.png)

1. Klik op de groepspagina van de PostgreSQL-server onder de kop Beveiliging op **Netwerken** om de Firewall-regels te openen.

   ![Azure-portal - klik op Netwerken](./media/howto-hyperscale-manage-firewall-using-portal/1-connection-security.png)

2. Klik **op Client-IP toevoegen**, op de werkbalk (optie A onder) of in de koppeling (optie B). Hoe dan ook maakt automatisch een firewallregel met het openbare IP-adres van uw computer, zoals waargenomen door het Azure-systeem.

   ![Azure-portal - klik op Client-IP toevoegen](./media/howto-hyperscale-manage-firewall-using-portal/2-add-my-ip.png)

Als u afwisselend op **+0.0.0.0 - 255.255.255.2555** (rechts van optie B) klikt, u niet alleen uw IP toevoegen, maar het hele internet om toegang te krijgen tot de poort 5432 van het coördinatorknooppunt. In deze situatie moeten clients nog steeds inloggen met de juiste gebruikersnaam en wachtwoord om het cluster te gebruiken. Desalniettemin raden we aan om wereldwijd slechts korte tijd toegang te verlenen en alleen voor niet-productiedatabases.

3. Controleer uw IP-adres voordat u de configuratie opslaat. In sommige situaties verschilt het IP-adres dat wordt waargenomen door Azure-portal van het IP-adres dat wordt gebruikt bij de toegang tot de internet- en Azure-servers. Daarom moet u mogelijk het IP-begin en het ip-adres starten wijzigen om de regel te laten functioneren zoals verwacht.
   Gebruik een zoekmachine of andere online tool om uw eigen IP-adres te controleren. Zoek bijvoorbeeld naar 'wat is mijn IP'.

   ![Bing zoeken naar Wat is mijn IP](./media/howto-hyperscale-manage-firewall-using-portal/3-what-is-my-ip.png)

4. Voeg extra adresbereiken toe. In de firewallregels u één IP-adres of een reeks adressen opgeven. Als u de regel wilt beperken tot één IP-adres, typt u hetzelfde adres in het veld voor IP-en eind-IP starten. Als u de firewall opent, kunnen beheerders, gebruikers en toepassingen toegang krijgen tot het coördinatorknooppunt op poort 5432.

5. Klik op **Opslaan** op de werkbalk om deze firewallregel op serverniveau op te slaan. Wacht op de bevestiging dat de update van de firewallregels is geslaagd.

## <a name="connecting-from-azure"></a>Verbinding maken vanuit Azure

Er is een eenvoudige manier om Hyperscale-databasetoegang te verlenen tot toepassingen die op Azure worden gehost (zoals een Azure Web Apps-toepassing of toepassingen die worden uitgevoerd in een Azure VM). Stel de optie **Azure-services en -bronnen toestaan om toegang te krijgen tot deze servergroepoptie** op **Ja** in de portal in het **deelvenster Netwerken** en druk op **Opslaan**.

> [!IMPORTANT]
> Met deze optie configureert u de firewall zo dat alle verbindingen vanuit Azure zijn toegestaan, inclusief verbindingen vanuit de abonnementen van andere klanten. Wanneer u deze optie selecteert, zorg dan dat uw aanmeldings- en gebruikersmachtigingen de toegang beperken tot alleen geautoriseerde gebruikers.

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Bestaande firewallregels op serverniveau beheren via Azure Portal
Herhaal de stappen om de firewallregels te beheren.
* Als u de huidige computer wilt toevoegen, klikt u op de knop + **Client-IP toevoegen**. Klik op **Opslaan** om de wijzigingen op te slaan.
* As u extra IP-adressen wilt toevoegen, typt u de Regelnaam, het Eerste IP-adres en het Laatste IP-adres in. Klik op **Opslaan** om de wijzigingen op te slaan.
* Als u een bestaande regel wilt wijzigen, klikt u op een willekeurig veld in de regel om dit aan te passen. Klik op **Opslaan** om de wijzigingen op te slaan.
* Als u een bestaande regel wilt verwijderen, klikt u op de ovaal [...] en klikt u op **Verwijderen** om de regel te verwijderen. Klik op **Opslaan** om de wijzigingen op te slaan.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [concept van firewallregels,](concepts-hyperscale-firewall-rules.md)waaronder het oplossen van verbindingsproblemen.
