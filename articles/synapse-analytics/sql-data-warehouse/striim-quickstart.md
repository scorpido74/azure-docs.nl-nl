---
title: Quick start voor realtimeplatform
description: Ga snel aan de slag met Realtimeplatform en Azure SQL Data Warehouse.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 10/12/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 945bcd03bc3bf13517836e7a5624bd5142782183
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85208549"
---
# <a name="striim-azure-sql-dw-marketplace-offering-install-guide"></a>Installatie handleiding voor Azure SQL DW Marketplace-realtimeplatform

In deze Quick Start wordt ervan uitgegaan dat u al een reeds bestaand exemplaar van SQL Data Warehouse hebt.

Zoek naar Realtimeplatform in azure Marketplace en selecteer de optie Realtimeplatform voor gegevens integratie met SQL Data Warehouse (klaargezet) 

![Realtimeplatform installeren][install]

Configureer de Realtimeplatform-VM met de opgegeven eigenschappen en noteer de Realtimeplatform-cluster naam, het wacht woord en het beheerders wachtwoord

![Realtimeplatform configureren][configure]

Zodra de implementatie is geïmplementeerd, klikt \<VM Name> u op masternode in de Azure Portal, klikt u op verbinding maken en kopieert u de aanmelding met een lokaal VM-account 

![Realtimeplatform verbinden met SQL Data Warehouse][connect]

Down load het sqljdbc42. jar van <https://www.microsoft.com/en-us/download/details.aspx?id=54671> op uw lokale computer. 

Open een opdracht regel venster en wijzig de mappen in de locatie waar u het JDBC-jar hebt gedownload. SCP het jar-bestand naar uw Realtimeplatform-VM en haal het adres en het wacht woord op uit de Azure Portal

![Jar-bestand kopiëren naar uw VM][copy-jar]

Open een ander opdracht regel venster of gebruik een ssh-hulp programma voor ssh in het Realtimeplatform-cluster

![SSH in het cluster][ssh]

Voer de volgende opdrachten uit om het JDBC jar-bestand naar de map lib van Realtimeplatform te verplaatsen en de server te starten en te stoppen.

   1. sudo su
   2. cd-map/tmp
   3. MV sqljdbc42. jar/opt/striim/lib
   4. systemctl stoppen realtimeplatform-knoop punt
   5. systemctl stoppen realtimeplatform-DBMS
   6. systemctl start realtimeplatform-DBMS
   7. systemctl start realtimeplatform-node

![Het Realtimeplatform-cluster starten][start-striim]

Open nu uw favoriete browser en ga naar \<DNS Name> : 9080

![Ga naar het aanmeldings scherm][navigate]

Meld u aan met de gebruikers naam en het wacht woord dat u in de Azure Portal hebt ingesteld en selecteer de wizard die u wilt gebruiken om aan de slag te gaan of ga naar de pagina apps om de gebruikers interface te slepen en neer te zetten

![Aanmelden met Server referenties][login]



[install]: ./media/striim-quickstart/install.png
[configure]: ./media/striim-quickstart/configure.png
[connect]:./media/striim-quickstart/connect.png
[copy-jar]:./media/striim-quickstart/copy-jar.png
[ssh]:./media/striim-quickstart/ssh.png
[start-striim]:./media/striim-quickstart/start-striim.png
[navigate]:./media/striim-quickstart/navigate.png
[login]:./media/striim-quickstart/login.png
