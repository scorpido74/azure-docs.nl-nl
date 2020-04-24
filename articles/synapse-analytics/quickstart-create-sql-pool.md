---
title: 'Quick Start: een Synapse SQL-groep maken'
description: Maak een nieuwe Synapse SQL-groep voor een Azure Synapse Analytics Synapse-werk ruimte door de stappen in deze hand leiding te volgen.
services: synapse-analytics
author: malvenko
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: josels
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: a50f203b8057dd4ea51eff1cf09fc97fb598cb3e
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82096432"
---
# <a name="quickstart-create-a-synapse-sql-pool"></a>Quick Start: een Synapse SQL-groep maken

Azure Synapse Analytics biedt diverse analyse-engines waarmee u uw gegevens kunt opnemen, transformeren, model leren, analyseren en verwerken. Een SQL-groep biedt op T-SQL gebaseerde reken-en opslag mogelijkheden. Nadat u een SQL-groep hebt gemaakt in uw Synapse-werk ruimte, kunnen gegevens worden geladen, gemodelleerd, verwerkt en bediend om inzichten te verkrijgen.

In deze Quick Start leert u hoe u een SQL-groep maakt in een Synapse-werk ruimte met behulp van de Azure Portal.

Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account aan voordat u begint](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Vereisten

[Synapse-werk ruimte](quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure Portal](https://portal.azure.com/)

## <a name="create-new-sql-pool"></a>Nieuwe SQL-groep maken

1. Klik in de werk ruimte Synapse waar u de SQL-groep wilt maken op de opdracht **nieuwe SQL-groep** in de bovenste balk.
![Overzicht van de Synapse-werk ruimte met een rood vak rond de opdracht om een nieuwe SQL-groep te maken.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-01.png)
2. Voer de volgende gegevens in op het tabblad **basis beginselen** :

    | Instelling | Voorgestelde waarde | Beschrijving |
    | :------ | :-------------- | :---------- |
    | **Naam van SQL-groep** | Een geldige naam | De naam van de SQL-groep. |
    | **Prestatie niveau** | DW100c | Stel in op de kleinste grootte om de kosten voor deze Quick Start te verlagen |
    ||||
  
    ![SQL-groep maken stroom-tabblad basis.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-02.png)
    > [!IMPORTANT]
    > Houd er rekening mee dat er specifieke beperkingen gelden voor de namen die SQL-groepen kunnen gebruiken. Namen mogen geen speciale tekens bevatten, mag niet langer zijn dan 15 tekens, mogen geen gereserveerde woorden bevatten en moeten uniek zijn in de werk ruimte.

3. Klik op **volgende: aanvullende instellingen**.
4. Selecteer **geen** om de SQL-groep zonder gegevens in te richten. Behoud de standaard sortering geselecteerd.
![SQL-groep maken stroom-tabblad aanvullende instellingen.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-03.png)

5. Klik op **Controleren + maken**.
6. Zorg ervoor dat de Details correct zijn, op basis van wat er eerder is ingevoerd en klik op **maken**.
![Het tabblad Instellingen voor het maken van een SQL-groep.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-04.png)

7. Op dit moment wordt de stroom voor het inrichten van de resource gestart.
 ![SQL-pool Create flow-resource-inrichting.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-06.png)

8. Nadat het inrichten is voltooid, wordt in de werk ruimte weer gegeven met een nieuwe vermelding voor de zojuist gemaakte SQL-groep.
 ![SQL-pool Create flow-resource-inrichting.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-07.png)

## <a name="clean-up-resources"></a>Resources opschonen

Volg de onderstaande stappen om de SQL-groep uit de werk ruimte te verwijderen.
> [!WARNING]
> Als u een SQL-groep verwijdert, worden zowel de analyse-engine als de gegevens die zijn opgeslagen in de data base van de verwijderde SQL-groep verwijderd uit de werk ruimte. Het is niet meer mogelijk om verbinding te maken met de SQL-groep en alle query's, pijp lijnen en notitie blokken die worden gelezen of geschreven naar deze SQL-groep, werken niet meer.

Als u de SQL-groep wilt verwijderen, voert u de volgende stappen uit:

1. Navigeer naar de Blade SQL-groepen op de Blade van de werk ruimte
1. Selecteer de SQL-groep die u wilt verwijderen (in dit geval **contosoedw**)
1. Selecteer deze en druk op **Delete**.
1. Bevestig het verwijderen en druk op de knop **verwijderen** .
 ![Overzicht van SQL-groep-markering verwijderen bevestigen.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-11.png)
1. Wanneer het proces is voltooid, wordt de SQL-groep niet meer weer gegeven in de werkruimte resources.

Nadat de SQL-groep is gemaakt, is deze beschikbaar in de werk ruimte voor het laden van gegevens, het verwerken van stromen, het lezen van het Lake, enzovoort.

## <a name="next-steps"></a>Volgende stappen

- Zie [Quick Start: een Apache Spark groep maken in Synapse Studio met](quickstart-apache-spark-notebook.md)behulp van web tools.
- Zie [Quick Start: een Apache Spark groep maken met behulp van de Azure Portal](quickstart-create-apache-spark-pool.md).
