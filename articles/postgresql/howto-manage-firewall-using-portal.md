---
title: Firewallregels beheren - Azure portal - Azure Database for PostgreSQL - Single Server
description: Firewallregels voor Azure Database voor PostgreSQL - Single Server maken en beheren met behulp van de Azure-portal
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: aeef22bf96221061a444f40e16e33343fafe511c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74770302"
---
# <a name="create-and-manage-firewall-rules-for-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Firewallregels voor Azure Database voor PostgreSQL - Single Server maken en beheren met behulp van de Azure-portal
Firewallregels op serverniveau kunnen worden gebruikt om de toegang tot een Azure Database voor PostgreSQL Server te beheren vanaf een opgegeven IP-adres of een reeks IP-adressen.

VNet-regels (Virtual Network) kunnen ook worden gebruikt om de toegang tot uw server te beveiligen. Meer informatie over [het maken en beheren van eindpunten en regels voor virtual network-service met behulp van de Azure-portal](howto-manage-vnet-using-portal.md).

## <a name="prerequisites"></a>Vereisten
Om deze handleiding door te nemen, hebt u het volgende nodig:
- Een server [Een Azure-database maken voor PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Een serverfirewallregel maken in Azure Portal
1. Klik op de pagina PostgreSQL-server onder kop Instellingen op **Verbindingsbeveiliging** om de pagina Verbindingsbeveiliging voor de Azure Database voor PostgreSQL te openen.

   ![Azure-portal - klik op Verbindingsbeveiliging](./media/howto-manage-firewall-using-portal/1-connection-security.png)

2. Klik **op Mijn IP toevoegen** op de werkbalk. Hierdoor wordt automatisch een firewallregel gemaakt met het openbare IP-adres van uw computer, zoals waargenomen door het Azure-systeem.

   ![Azure-portal - klik op Mijn IP toevoegen](./media/howto-manage-firewall-using-portal/2-add-my-ip.png)

3. Controleer uw IP-adres voordat u de configuratie opslaat. In sommige situaties verschilt het IP-adres dat wordt waargenomen door Azure-portal van het IP-adres dat wordt gebruikt bij de toegang tot de internet- en Azure-servers. Daarom moet u mogelijk het IP-begin en het ip-adres starten wijzigen om de regel te laten functioneren zoals verwacht.
   Gebruik een zoekmachine of andere online tool om uw eigen IP-adres te controleren. Zoek bijvoorbeeld naar 'wat is mijn IP'.

   ![Bing zoeken naar Wat is mijn IP](./media/howto-manage-firewall-using-portal/3-what-is-my-ip.png)

4. Voeg extra adresbereiken toe. In de firewallregels voor de Azure Database voor PostgreSQL u één IP-adres of een reeks adressen opgeven. Als u de regel wilt beperken tot één IP-adres, typt u hetzelfde adres in het veld voor IP-en eind-IP starten. Als u de firewall opent, kunnen beheerders, gebruikers en toepassingen toegang krijgen tot elke database op de PostgreSQL-server waarop ze geldige referenties hebben.

   ![Azure-portal - firewallregels](./media/howto-manage-firewall-using-portal/4-specify-addresses.png)

5. Klik op **Opslaan** op de werkbalk om deze firewallregel op serverniveau op te slaan. Wacht op de bevestiging dat de update van de firewallregels is geslaagd.

   ![Azure-portal - klik op Opslaan](./media/howto-manage-firewall-using-portal/5-save-firewall-rule.png)

## <a name="connecting-from-azure"></a>Verbinding maken vanuit Azure
Azure-verbindingen moeten zijn ingeschakeld om toepassingen van Azure toe te staan verbinding te maken met uw Azure Database voor PostgreSQL-server. Bijvoorbeeld om een Azure Web Apps-toepassing te hosten, of een toepassing die wordt uitgevoerd in een Azure VM, of om verbinding te maken vanuit een Azure Data Factory-gegevensbeheergateway. De resources hoeven zich niet in dezelfde Virtual Network (VNet) of Resource Group te bevinden voor de firewallregel om deze verbindingen in te schakelen. Wanneer een toepassing vanuit Azure probeert verbinding te maken met uw databaseserver, verifieert de firewall of Azure-verbindingen zijn toegestaan. Er zijn een paar methoden om dit soort verbindingen in te schakelen. Een firewallinstelling waarvan het begin- en eindadres gelijk zijn aan 0.0.0.0 geeft aan dat deze verbindingen zijn toegestaan. U ook de optie **Toegang tot Azure-services toestaan** instellen in **DE** portal in het **beveiligingsdeelvenster Verbinding** en op **Opslaan drukken.** Als de verbindingspoging niet is toegestaan, bereikt de aanvraag de Azure Database voor PostgreSQL-server niet.

> [!IMPORTANT]
> Met deze optie configureert u de firewall zo dat alle verbindingen vanuit Azure zijn toegestaan, inclusief verbindingen vanuit de abonnementen van andere klanten. Wanneer u deze optie selecteert, zorg dan dat uw aanmeldings- en gebruikersmachtigingen de toegang beperken tot alleen geautoriseerde gebruikers.
> 

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Bestaande firewallregels op serverniveau beheren via Azure Portal
Herhaal de stappen om de firewallregels te beheren.
* Als u de huidige computer wilt toevoegen, klikt u op de knop + **Mijn IP toevoegen**. Klik op **Opslaan** om de wijzigingen op te slaan.
* As u extra IP-adressen wilt toevoegen, typt u de Regelnaam, het Eerste IP-adres en het Laatste IP-adres in. Klik op **Opslaan** om de wijzigingen op te slaan.
* Als u een bestaande regel wilt wijzigen, klikt u op een willekeurig veld in de regel om dit aan te passen. Klik op **Opslaan** om de wijzigingen op te slaan.
* Als u een bestaande regel wilt verwijderen, klikt u op de ovaal [...] en klikt u op **Verwijderen** om de regel te verwijderen. Klik op **Opslaan** om de wijzigingen op te slaan.

## <a name="next-steps"></a>Volgende stappen
- Op dezelfde manier u azure [database maken en beheren voor PostgreSQL-firewallregels met Azure CLI](howto-manage-firewall-using-cli.md).
- Verdere veilige toegang tot uw server door [eindpunten en regels voor virtual network-service te maken en te beheren met behulp van de Azure-portal.](howto-manage-vnet-using-portal.md)
- Zie [Verbindingsbibliotheken voor Azure Database voor PostgreSQL](concepts-connection-libraries.md)voor hulp bij het maken van verbinding met een Azure Database voor PostgreSQL.
