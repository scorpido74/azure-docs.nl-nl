---
title: Striim snel aan de slag
description: Ga snel aan de slag met Striim en Azure SQL Data Warehouse.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 10/12/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: e963d62df8d2c416726852ee3d46daf1cd22936e
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349946"
---
# <a name="striim-azure-sql-dw-marketplace-offering-install-guide"></a>Striim Azure SQL DW Marketplace-installatiehandleiding

Deze quickstart gaat ervan uit dat u al een reeds bestaande instantie van SQL Data Warehouse hebt.

Zoeken naar Striim in de Azure Marketplace en selecteer de optie Striim voor gegevensintegratie naar SQL Data Warehouse (Gefaseerd) 

![Striim installeren][install]

De Striim-VM configureren met opgegeven eigenschappen, waarbij het striim-clusternaam,-wachtwoord en beheerderswachtwoord worden vermeld

![Striim configureren][configure]

Eenmaal geïmplementeerd, klikt \<u op VM-naam>-masternode in de Azure-portal, klikt u op Verbinding maken en kopieert u de aanmelding met het lokale VM-account 

![Striim verbinden met SQL Data Warehouse][connect]

Download de sqljdbc42.jar van <https://www.microsoft.com/en-us/download/details.aspx?id=54671> uw lokale machine. 

Open een opdrachtregelvenster en wijzig mappen naar de plaats waar u de JDBC-pot hebt gedownload. SCP het jar-bestand naar uw Striim VM, het ophalen van het adres en wachtwoord van de Azure-portal

![Jar-bestand naar uw vm kopiëren][copy-jar]

Open een ander opdrachtregelvenster of gebruik een ssh-hulpprogramma om in het Striim-cluster te ssh

![SSH in het cluster][ssh]

Voer de volgende opdrachten uit om het JDBC-jarbestand naar de libmap van Striim te verplaatsen en de server te starten en te stoppen.

   1. sudo su
   2. cd /tmp
   3. mv sqljdbc42.jar /opt/striim/lib
   4. systemctl stop striim-node
   5. systemctl stop striim-dbms
   6. systemctl start striim-dbms
   7. systemctl start striim-node

![Het Striim-cluster starten][start-striim]

Open nu uw favoriete browser \<en navigeer naar DNS-naam>:9080

![Navigeren naar het inlogscherm][navigate]

Meld u aan met de gebruikersnaam en het wachtwoord dat u in de Azure-portal hebt ingesteld en selecteer de gewenste wizard om aan de slag te gaan of ga naar de pagina Apps om de gebruikersinterface slepen en neerzetten te gebruiken

![Inloggen met serverreferenties][login]



[install]: ./media/striim-quickstart/install.png
[configure]: ./media/striim-quickstart/configure.png
[connect]:./media/striim-quickstart/connect.png
[copy-jar]:./media/striim-quickstart/copy-jar.png
[ssh]:./media/striim-quickstart/ssh.png
[start-striim]:./media/striim-quickstart/start-striim.png
[navigate]:./media/striim-quickstart/navigate.png
[login]:./media/striim-quickstart/login.png
