---
title: Firewall regels beheren-grootschalige (Citus)-Azure Database for PostgreSQL
description: Maak en beheer firewall regels voor Azure Database for PostgreSQL-grootschalige (Citus) met behulp van de Azure Portal
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: b7de5755a9a1e49b994e7efa7fc4bca58cc6cfd9
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82584036"
---
# <a name="manage-firewall-rules-for-azure-database-for-postgresql---hyperscale-citus"></a>Firewall regels voor Azure Database for PostgreSQL-grootschalige beheren (Citus)
Firewall regels op server niveau kunnen worden gebruikt voor het beheren van de toegang tot een grootschalige (Citus)-coördinator knooppunt vanaf een opgegeven IP-adres of bereik van IP-adressen.

## <a name="prerequisites"></a>Vereisten
Als u deze hand leiding wilt door lopen, hebt u het volgende nodig:
- Een server groep [maakt een Citus-Server groep (Azure database for PostgreSQL – grootschalige)](quickstart-create-hyperscale-portal.md).

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Een serverfirewallregel maken in Azure Portal

> [!NOTE]
> Deze instellingen zijn ook toegankelijk tijdens het maken van een Citus-Server groep (Azure Database for PostgreSQL-grootschalige). Klik op het tabblad **netwerken** op **openbaar eind punt**.
> ![Azure Portal-tabblad netwerk](./media/howto-hyperscale-manage-firewall-using-portal/0-create-public-access.png)

1. Klik op de pagina PostgreSQL-Server groep, onder de kop beveiliging, op **netwerken** om de firewall regels te openen.

   ![Azure Portal-klikken op netwerken](./media/howto-hyperscale-manage-firewall-using-portal/1-connection-security.png)

2. Klik op **IP van client toevoegen**op de werk balk (optie hieronder) of in de koppeling (optie B). Een van de manieren om automatisch een firewall regel te maken met het open bare IP-adres van uw computer, zoals wordt waargenomen door het Azure-systeem.

   ![Azure Portal-Klik op IP van client toevoegen](./media/howto-hyperscale-manage-firewall-using-portal/2-add-my-ip.png)

U kunt ook op **+ toevoegen 0.0.0.0-255.255.255.255** (rechts van optie B) niet alleen uw IP-adres gebruiken, maar het hele internet heeft toegang tot de poort 5432 van het coördinator knooppunt. In deze situatie moeten clients zich nog steeds aanmelden met de juiste gebruikers naam en wacht woord om het cluster te gebruiken. Desondanks raden we u aan om wereld wijde toegang alleen in te stellen voor korte Peri Oden en alleen voor niet-productie databases.

3. Controleer uw IP-adres voordat u de configuratie opslaat. In sommige gevallen wijkt het IP-adres van Azure Portal af van het IP-adres dat wordt gebruikt voor toegang tot het internet en Azure-servers. Daarom moet u het eerste IP-adres en het laatste IP-adres wijzigen om de regel te laten werken zoals verwacht.
   Gebruik een zoek machine of een ander online hulp programma om uw eigen IP-adres te controleren. Zoek bijvoorbeeld naar ' wat is mijn IP '.

   ![Bing zoeken naar wat is mijn IP-adres](./media/howto-hyperscale-manage-firewall-using-portal/3-what-is-my-ip.png)

4. Voeg extra adresbereiken toe. In de firewall regels kunt u één IP-adres of een bereik van adressen opgeven. Als u de regel wilt beperken tot één IP-adres, typt u hetzelfde adres in het veld voor eerste IP en laatste IP. Als u de firewall opent, kunnen beheerders, gebruikers en toepassingen toegang krijgen tot het coördinator knooppunt op poort 5432.

5. Klik op **Opslaan** op de werk balk om deze firewall regel op server niveau op te slaan. Wacht tot de bevestiging is geslaagd voor de update van de firewall regels.

## <a name="connecting-from-azure"></a>Verbinding maken vanuit Azure

Er is een eenvoudige manier om toegang tot grootschalige-data bases toe te kennen aan toepassingen die worden gehost op Azure (zoals een Azure Web Apps-toepassing, of die worden uitgevoerd in een Azure-VM). Stel de optie **Azure-Services en-resources toegang tot deze server groep toestaan** in op **Ja** in de portal in het deel venster **netwerken** en druk op **Opslaan**.

> [!IMPORTANT]
> Met deze optie configureert u de firewall zo dat alle verbindingen vanuit Azure zijn toegestaan, inclusief verbindingen vanuit de abonnementen van andere klanten. Wanneer u deze optie selecteert, zorg dan dat uw aanmeldings- en gebruikersmachtigingen de toegang beperken tot alleen geautoriseerde gebruikers.

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Bestaande firewallregels op serverniveau beheren via Azure Portal
Herhaal de stappen voor het beheren van de firewall regels.
* Als u de huidige computer wilt toevoegen, klikt u op de knop aan + **client-IP toevoegen**. Klik op **Opslaan** om de wijzigingen op te slaan.
* As u extra IP-adressen wilt toevoegen, typt u de Regelnaam, het Eerste IP-adres en het Laatste IP-adres in. Klik op **Opslaan** om de wijzigingen op te slaan.
* Als u een bestaande regel wilt wijzigen, klikt u op een willekeurig veld in de regel om dit aan te passen. Klik op **Opslaan** om de wijzigingen op te slaan.
* Als u een bestaande regel wilt verwijderen, klikt u op het beletsel teken [...] en klikt u op **verwijderen** om de regel te verwijderen. Klik op **Opslaan** om de wijzigingen op te slaan.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het [concept van firewall regels](concepts-hyperscale-firewall-rules.md), met inbegrip van het oplossen van verbindings problemen.
