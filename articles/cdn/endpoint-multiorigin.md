---
title: Meerdere begin punten Azure CDN
description: Aan de slag met Azure CDN eind punt meerdere oorsprongen.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.topic: how-to
ms.date: 5/8/2020
ms.author: allensu
ms.openlocfilehash: 628a284335b7285a2ee989511b130fb7d4d3cde6
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597875"
---
# <a name="azure-cdn-endpoint-multi-origin"></a>Meerdere begin punten Azure CDN

Met de ondersteuning van meerdere oorsprong kunt u wereld wijde redundantie instellen en uitval tijd elimineren door meerdere oorsprongen in een Azure CDN eind punt te kiezen. De redundantie die door meerdere oorsprong wordt verschaft, verspreidt het risico door de status van elke oorsprong te zoeken en zo nodig bij te werken. Als u meerdere Origin wilt instellen, stelt u een of meer oorspronkelijke groepen in. Elke oorsprongs groep is een verzameling van een of meer oorsprong die vergelijk bare workloads kan hebben.

> [!NOTE]
> Deze functie is momenteel alleen beschikbaar via Azure CDN van micro soft. 

## <a name="create-the-origin-group"></a>De oorspronkelijke groep maken

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)

2. Selecteer uw Azure CDN profiel en selecteer vervolgens het eind punt dat moet worden geconfigureerd voor meerdere oorsprong.

3. Selecteer **oorsprong** onder **instellingen** in de eindpunt configuratie:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-1.png" alt-text="Azure CDN meerdere oorsprong" border="true":::

4. Als u meerdere Origins wilt inschakelen, moet u ten minste één oorsprongs groep hebben. Selecteer een **oorsprongs groep maken**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-2.png" alt-text="Azure CDN meerdere oorsprong" border="true":::

5. Typ of selecteer de volgende informatie in de configuratie van de groep voor het toevoegen van de **oorsprong** :

   | Instelling           | Waarde                                                                 |
   |-------------------|-----------------------------------------------------------------------|
   | Naam van de oorspronkelijke groep | Voer een naam in voor de oorspronkelijke groep.                                   |
   | Pad testen        | Het pad in de oorsprong dat wordt gebruikt om de status te bepalen. |
   | Test interval    | Selecteer een test interval van 1, 2 of 4 minuten.                        |
   | Test protocol    | Selecteer **http** of **https**.                                         |
   | Test methode      | Selecteer **Head** of **Get**.                                           |
    
   :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-3.png" alt-text="Azure CDN meerdere oorsprong" border="true":::

6. Selecteer **Toevoegen**.

7. Als u de standaard groep voor de oorsprong wilt kiezen, selecteert u **bron groep configureren**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-4.png" alt-text="Azure CDN meerdere oorsprong" border="true":::

8. Selecteer uw oorspronkelijke groep in de vervolg keuzelijst en selecteer **OK**.

## <a name="add-multiple-origins"></a>Meerdere oorsprongen toevoegen

1. Selecteer **+ oorsprong maken**in de oorspronkelijke instellingen voor het eind punt:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-5.png" alt-text="Azure CDN meerdere oorsprong" border="true":::

2. Voer de volgende informatie in of Selecteer deze in de configuratie **oorsprong toevoegen** :

   | Instelling           | Waarde                                                                 |
   |-------------------|-----------------------------------------------------------------------|
   | Type oorsprong | Selecteer **opslag**, **Cloud service**, **Web-app**of **aangepaste oorsprong**.                                   |
   | Hostnaam van oorsprong        | Selecteer of typ de hostnaam van de oorsprong.  De vervolg keuzelijst bevat alle beschik bare oorsprongen van het type dat u in de vorige instelling hebt opgegeven. Als u **aangepaste oorsprong** als uw oorsprongs type hebt geselecteerd, voert u het domein van de bron server van uw klant in. |
   | Host-header van oorsprong    | Voer de host-header in die u Azure CDN wilt verzenden met elke aanvraag, of wijzig de standaard waarde.                        |
   | HTTP-poort   | Voer de HTTP-poort in.                                         |
   | HTTPS-poort     | Voer de HTTPS-poort in.                                           |
   | Prioriteit    | Voer een getal tussen 1 en 5 in.       |
   | Gewicht      | Voer een getal in tussen 1 en 1000.   |

    > [!NOTE]
    > Wanneer een oorsprong in een oorspronkelijke groep wordt gemaakt, moet er een prioriteit en gewicht worden verleend. Als een oorspronkelijke groep slechts één oorsprong heeft, wordt de standaard prioriteit en het basis gewicht ingesteld op 1. Het verkeer wordt doorgestuurd naar de oorsprong van de hoogste prioriteit als de oorsprong in orde is. Als wordt vastgesteld dat een oorsprong een slechte status heeft, worden de verbindingen in de volg orde van prioriteit doorverkocht naar een andere oorsprong. Als twee oorsprongen dezelfde prioriteit hebben, wordt verkeer gedistribueerd als per gewicht dat is opgegeven voor de oorsprong 

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-6.png" alt-text="Azure CDN meerdere oorsprong" border="true":::

3. Selecteer **Toevoegen**.

4. Selecteer **oorsprong configureren** om het bronpad in te stellen voor alle oorsprongen:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-7.png" alt-text="Azure CDN meerdere oorsprong" border="true":::

5. Selecteer **OK**.

## <a name="configure-origins-and-origin-group-settings"></a>Instellingen voor oorsprong en oorspronkelijke groep configureren

Zodra u verschillende oorsprongen en een oorspronkelijke groep hebt, kunt u de oorsprong toevoegen aan of verwijderen uit verschillende groepen. Oorsprongen binnen dezelfde groep moeten soort gelijke workloads hebben. Verkeer wordt naar deze oorsprongen gedistribueerd op basis van hun gezonde status, prioriteit en gewichts waarde. 

1. Selecteer in de oorspronkelijke instellingen van het Azure CDN-eind punt de optie **oorspronkelijke groep configureren**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-8.png" alt-text="Azure CDN meerdere oorsprong" border="true":::

2. Selecteer in de vervolg keuzelijst de oorspronkelijke groep die u wilt configureren en selecteer **OK**.

3. In de **groep update Origin**selecteert u **+ oorsprong toevoegen**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-9.png" alt-text="Azure CDN meerdere oorsprong" border="true":::

4. Selecteer de oorsprong die u wilt toevoegen aan de groep in de vervolg keuzelijst en selecteer **OK**.

5. Controleer of de oorsprong is toegevoegd aan de groep en selecteer vervolgens **Opslaan**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-10.png" alt-text="Azure CDN meerdere oorsprong" border="true":::

## <a name="remove-origin-from-origin-group"></a>Oorsprong uit de oorspronkelijke groep verwijderen

1. Selecteer in de oorspronkelijke instellingen van het Azure CDN-eind punt de optie **oorspronkelijke groep configureren**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-8.png" alt-text="Azure CDN meerdere oorsprong" border="true":::

2. Selecteer in de vervolg keuzelijst de oorspronkelijke groep die u wilt configureren en selecteer **OK**.

3. Als u een oorsprong uit de oorspronkelijke groep wilt verwijderen, selecteert u het prullenbak pictogram naast de oorsprong en selecteert u **Opslaan**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-11.png" alt-text="Azure CDN meerdere oorsprong" border="true":::

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u Azure CDN-eind punt in meerdere oorsprong ingeschakeld.

Zie voor meer informatie over Azure CDN en de andere Azure-Services die in dit artikel worden genoemd:

* [Azure CDN](./cdn-overview.md)
* [Azure CDN product functie vergelijken](./cdn-features.md)
