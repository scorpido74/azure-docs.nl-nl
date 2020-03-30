---
title: Firewallregels beheren - Azure portal - Azure Database voor MariaDB
description: Azure Database voor MariaDB-firewallregels maken en beheren met behulp van de Azure-portal
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 8be8e948595cfb93049c0d6c93f421e4902e771d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530662"
---
# <a name="create-and-manage-azure-database-for-mariadb-firewall-rules-by-using-the-azure-portal"></a>Azure Database voor MariaDB-firewallregels maken en beheren met behulp van de Azure-portal
Firewallregels op serverniveau kunnen worden gebruikt om de toegang tot een Azure-database voor MariaDB-server te beheren vanaf een opgegeven IP-adres of een reeks IP-adressen.

VNet-regels (Virtual Network) kunnen ook worden gebruikt om de toegang tot uw server te beveiligen. Meer informatie over [het maken en beheren van eindpunten en regels voor virtual network-service met behulp van de Azure-portal](howto-manage-vnet-portal.md).

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Een serverfirewallregel maken in Azure Portal

1. Klik op de pagina MariaDB-server onder Kop Instellingen op **Verbindingsbeveiliging** om de pagina Verbindingsbeveiliging voor de Azure-database voor MariaDB te openen.

   ![Azure-portal - klik op Verbindingsbeveiliging](./media/howto-manage-firewall-portal/1-connection-security.png)

2. Klik **op Mijn IP toevoegen** op de werkbalk. Hierdoor wordt automatisch een firewallregel gemaakt met het openbare IP-adres van uw computer, zoals waargenomen door het Azure-systeem.

   ![Azure-portal - klik op Mijn IP toevoegen](./media/howto-manage-firewall-portal/2-add-my-ip.png)

3. Controleer uw IP-adres voordat u de configuratie opslaat. In sommige situaties verschilt het IP-adres dat wordt waargenomen door Azure-portal van het IP-adres dat wordt gebruikt bij de toegang tot de internet- en Azure-servers. Daarom moet u mogelijk het IP-begin en het ip-adres starten wijzigen om de regel te laten functioneren zoals verwacht.

   Gebruik een zoekmachine of andere online tool om uw eigen IP-adres te controleren. Zoek bijvoorbeeld op "wat is mijn IP-adres".

4. Voeg extra adresbereiken toe. In de firewallregels voor de Azure Database voor MariaDB u één IP-adres of een reeks adressen opgeven. Als u de regel wilt beperken tot één IP-adres, typt u hetzelfde adres in de IP- en ip-velden starten. Als u de firewall opent, kunnen beheerders, gebruikers en toepassingen toegang krijgen tot elke database op de MariaDB-server waarop ze geldige referenties hebben.

   ![Azure-portal - firewallregels](./media/howto-manage-firewall-portal/4-specify-addresses.png)

5. Klik op **Opslaan** op de werkbalk om deze firewallregel op serverniveau op te slaan. Wacht op de bevestiging dat de update van de firewallregels is geslaagd.

   ![Azure-portal - klik op Opslaan](./media/howto-manage-firewall-portal/5-save-firewall-rule.png)

## <a name="connecting-from-azure"></a>Verbinding maken vanuit Azure
Azure-verbindingen moeten zijn ingeschakeld om toepassingen van Azure toe te staan verbinding te maken met uw Azure Database voor MariaDB-server. Bijvoorbeeld om een Azure Web Apps-toepassing te hosten, of een toepassing die wordt uitgevoerd in een Azure VM, of om verbinding te maken vanuit een Azure Data Factory-gegevensbeheergateway. De resources hoeven zich niet in dezelfde Virtual Network (VNet) of Resource Group te bevinden voor de firewallregel om deze verbindingen in te schakelen. Wanneer een toepassing vanuit Azure probeert verbinding te maken met uw databaseserver, verifieert de firewall of Azure-verbindingen zijn toegestaan. Er zijn een paar methoden om dit soort verbindingen in te schakelen. Een firewallinstelling waarvan het begin- en eindadres gelijk zijn aan 0.0.0.0 geeft aan dat deze verbindingen zijn toegestaan. U ook de optie **Toegang tot Azure-services toestaan** instellen in **DE** portal in het **beveiligingsdeelvenster Verbinding** en op **Opslaan**klikken. Als de verbindingspoging niet is toegestaan, bereikt de aanvraag de Azure-database voor MariaDB-server niet.

> [!IMPORTANT]
> Met deze optie configureert u de firewall zo dat alle verbindingen vanuit Azure zijn toegestaan, inclusief verbindingen vanuit de abonnementen van andere klanten. Wanneer u deze optie selecteert, zorg dan dat uw aanmeldings- en gebruikersmachtigingen de toegang beperken tot alleen geautoriseerde gebruikers.
> 

## <a name="manage-existing-firewall-rules-in-the-azure-portal"></a>Bestaande firewallregels beheren in de Azure-portal
Herhaal de stappen om de firewallregels te beheren.
* Als u de huidige computer wilt toevoegen, klikt u op **+ Mijn IP toevoegen**. Klik op **Opslaan** om de wijzigingen op te slaan.
* Als u extra IP-adressen wilt toevoegen, typt u de **REGELNAAM**, **IP starten**en **IP beëindigen**. Klik op **Opslaan** om de wijzigingen op te slaan.
* Als u een bestaande regel wilt wijzigen, klikt u op een van de velden in de regel en wijzigt u deze. Klik op **Opslaan** om de wijzigingen op te slaan.
* Als u een bestaande regel wilt verwijderen, klikt u op de ellips [...]en klikt u vervolgens op **Verwijderen**. Klik op **Opslaan** om de wijzigingen op te slaan.

## <a name="next-steps"></a>Volgende stappen
 - Op dezelfde manier u azure [database voor MariaDB-firewallregels maken en beheren met Azure CLI](howto-manage-firewall-cli.md).
 - Verdere veilige toegang tot uw server door [eindpunten en regels voor virtual network-service te maken en te beheren met behulp van de Azure-portal.](howto-manage-vnet-portal.md)