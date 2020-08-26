---
title: Azure Synapse Analytics-uitvoer van Azure Stream Analytics
description: In dit artikel wordt Azure Synapse Analytics beschreven als uitvoer voor Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 5d281bf33542354e2904805deed2f1deaf2725a2
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2020
ms.locfileid: "88875736"
---
# <a name="azure-synapse-analytics-output-from-azure-stream-analytics-preview"></a>Azure Synapse Analytics-uitvoer van Azure Stream Analytics (preview-versie)

[Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics) (voorheen SQL Data Warehouse) is een oneindige analyse service waarmee bedrijfs gegevensopslag en Big data-analyses worden gecombineerd. 

Azure Stream Analytics-taken kunnen worden uitgevoerd naar een SQL-groeps tabel in azure Synapse Analytics en kunnen doorvoer snelheden tot 200 MB per seconde verwerken. Dit biedt ondersteuning voor de meest veeleisende, realtime analyse-en hot-path gegevens verwerking voor werk belastingen, zoals rapportage en dash boarding.  

De SQL-groeps tabel moet bestaan voordat u deze als uitvoer kunt toevoegen aan uw Stream Analytics-taak. Het schema van de tabel moet overeenkomen met de velden en hun typen in de uitvoer van uw taak. 

Als u Azure Synapse als uitvoer wilt gebruiken, moet u ervoor zorgen dat u het opslag account hebt geconfigureerd. Navigeer naar de instellingen van het opslag account om het opslag account te configureren. Alleen de typen opslag accounts die ondersteuning bieden voor tabellen zijn toegestaan: v2 en algemeen gebruik v1. Selecteer alleen standaard laag. De Premium-laag wordt niet ondersteund.

## <a name="output-configuration"></a>Uitvoer configuratie

De volgende tabel bevat de namen van de eigenschappen en de bijbehorende beschrijvingen voor het maken van de Azure Synapse Analytics-uitvoer.

|Naam van eigenschap|Beschrijving|
|-|-|
|Uitvoeralias |Een beschrijvende naam die wordt gebruikt in query's om de uitvoer van de query naar deze data base te sturen. |
|Database |De naam van de SQL-groep waarnaar u uw uitvoer wilt verzenden. |
|Servernaam |Naam van de Azure Synapse-server.  |
|Gebruikersnaam |De gebruikers naam die schrijf toegang tot de data base heeft. Stream Analytics ondersteunt alleen SQL-verificatie. |
|Wachtwoord |Het wacht woord om verbinding te maken met de data base. |
|Tabel  | De tabel naam waarin de uitvoer wordt geschreven. De tabel naam is hoofdletter gevoelig. Het schema van deze tabel moet exact overeenkomen met het aantal velden en de bijbehorende typen die uw taak uitvoer genereert.|

## <a name="next-steps"></a>Volgende stappen

* [Snelstart: Een Stream Analytics-taak maken via Azure Portal](stream-analytics-quick-create-portal.md)
* [Quickstart: een Azure Stream Analytics-taak maken via de Azure CLI](quick-create-azure-cli.md)
* [Quickstart: Een Azure Stream Analytics-taak maken via een ARM-sjabloon](quick-create-azure-resource-manager.md)
* [Quickstart: Een Stream Analytics-taak maken met behulp van Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Quickstart: Een Azure Stream Analytics-taak maken met behulp van Visual Studio](stream-analytics-quick-create-vs.md)
* [Snelstartgids: een Azure Stream Analytics-taak maken in Visual Studio code](quick-create-vs-code.md)