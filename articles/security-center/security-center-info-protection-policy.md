---
title: SQL Information Protection aanpassen-Azure Security Center
description: Meer informatie over het aanpassen van het beleid voor gegevens beveiliging in Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2ebf2bc7-232a-45c4-a06a-b3d32aaf2500
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/11/2020
ms.author: memildin
ms.openlocfilehash: abcdc903e1509c266b9ea6666c296a59183e83c5
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/11/2020
ms.locfileid: "84711082"
---
# <a name="customize-the-sql-information-protection-policy-in-azure-security-center-preview"></a>Het SQL Information Protection-beleid in Azure Security Center aanpassen (preview-versie)
 
In Azure Security Center kunt u een SQL Information Protection-beleid definiëren en aanpassen voor uw hele Azure-Tenant.

Information Protection is een geavanceerde beveiligings mogelijkheid voor het detecteren, classificeren, labelen en rapporteren van gevoelige gegevens in uw Azure-gegevens bronnen. Het detecteren en classificeren van uw meest gevoelige gegevens (bedrijfs-, financiële, gezondheids zorg, persoonlijke gegevens, enz.) kan een draai functie in uw organisatie voor gegevens beveiliging van stature afspelen. Dit kan dienen als infrastructuur om:
- Helpt voldoen aan de normen voor gegevens beveiliging en naleving van regelgeving
- Beveiligings scenario's zoals controle (controle) en waarschuwingen over afwijkende toegang tot gevoelige gegevens
- Het beheren van de toegang tot en het beveiligen van de beveiliging van gegevens archieven met uiterst gevoelige gegevens
 
[Sql Information Protection](../azure-sql/database/data-discovery-and-classification-overview.md) implementeert dit paradigma voor uw SQL-gegevens archieven, die momenteel worden ondersteund voor Azure SQL database. SQL Information Protection detecteert en classificeert mogelijk gevoelige gegevens automatisch, biedt een label mechanisme voor het permanent coderen van gevoelige gegevens met classificatie kenmerken en biedt een gedetailleerd dash board met de classificatie status van de data base. Daarnaast berekent de IT de gevoeligheid van de resultatenset van SQL-query's, zodat query's die gevoelige gegevens ophalen, expliciet kunnen worden gecontroleerd en de gegevens kunnen worden beveiligd. Zie [Azure SQL database voor gegevens detectie en-classificatie](../azure-sql/database/data-discovery-and-classification-overview.md)voor meer informatie over SQL Information Protection.
 
Het classificatie mechanisme is gebaseerd op twee primaire constructs **die samen de classificatie-en** **informatie typen**voor het classificeren vormen.
- **Labels** : de belangrijkste classificatie kenmerken, die worden gebruikt voor het definiëren van het gevoeligheids niveau van de gegevens die zijn opgeslagen in de kolom. 
- **Informatie typen** : biedt een extra granulatie voor het type gegevens dat in de kolom wordt opgeslagen.
 
Information Protection wordt geleverd met een ingebouwde set van labels en informatie typen die standaard worden gebruikt. Als u deze labels en typen wilt aanpassen, kunt u het Information Protection-beleid aanpassen in Security Center.
 
## <a name="customize-the-information-protection-policy"></a>Het informatiebeschermingsbeleid aanpassen
Als u het Information Protection-beleid voor uw Azure-Tenant wilt aanpassen, moet u [beheerders bevoegdheden hebben voor de hoofd beheer groep van de Tenant](security-center-management-groups.md). 
 
1. Ga in het hoofd menu van Security Center naar **gegevens & opslag** onder **resource Security-hygiëne** en klik op de knop **SQL Information Protection** .

   ![Information Protection-beleid configureren](./media/security-center-info-protection-policy/security-policy.png) 
 
2. Op de pagina **SQL-Information Protection** kunt u uw huidige set met labels bekijken. Dit zijn de belangrijkste classificatie kenmerken die worden gebruikt voor het categoriseren van het gevoeligheids niveau van uw gegevens. Hier kunt u de **labels voor gegevens beveiliging** en **informatie typen** voor de Tenant configureren. 
 
### <a name="customizing-labels"></a>Labels aanpassen
 
1. U kunt een bestaand label bewerken of verwijderen of een nieuw label toevoegen. Als u een bestaand label wilt bewerken, selecteert u het label en klikt u op **configureren**, hetzij boven of in het context menu aan de rechter kant. Als u een nieuw label wilt toevoegen, klikt u op **label maken** in de bovenste menu balk of aan de onderkant van de tabel labels.
2. In het scherm **gevoeligheids label configureren** kunt u de naam van het label en de beschrijving maken of wijzigen. U kunt ook instellen of het label actief of uitgeschakeld is door de **ingeschakelde** switch in of uit te scha kelen. Ten slotte kunt u gegevens typen toevoegen of verwijderen die zijn gekoppeld aan het label. Alle gegevens die zijn gedetecteerd die overeenkomen met dat informatie type, bevatten automatisch het bijbehorende gevoeligheids label in de classificatie aanbevelingen.
3. Klik op **OK**.
 
   ![Gevoeligheids label configureren](./media/security-center-info-protection-policy/config-sensitivity-label.png)
 
4. Labels worden weer gegeven in volg orde van oplopende gevoeligheid. Als u de rang schikking tussen labels wilt wijzigen, sleept u de labels om ze in de tabel opnieuw te rangschikken of gebruikt u de knoppen **omhoog** en **omlaag** om de volg orde te wijzigen. 
 
    ![Information Protection-beleid configureren](./media/security-center-info-protection-policy/move-up.png)
 
5. Zorg ervoor dat u boven aan het scherm op **Opslaan** klikt wanneer u klaar bent.
 
 
## <a name="adding-and-customizing-information-types"></a>Informatie typen toevoegen en aanpassen
 
1. U kunt gegevens typen beheren en aanpassen door te klikken op **informatie typen beheren**.
2. Als u een nieuw **gegevens type**wilt toevoegen, selecteert u **gegevens type maken** in het bovenste menu. U kunt een naam, beschrijving en zoek patroon teken reeksen voor het **informatie type**configureren. Zoek patroon teken reeksen kunnen optioneel tref woorden gebruiken met Joker tekens (met het teken%), die de geautomatiseerde detectie-engine gebruikt om gevoelige gegevens in uw data bases te identificeren, op basis van de meta gegevens van de kolommen.
 
    ![Information Protection-beleid configureren](./media/security-center-info-protection-policy/info-types.png)
 
3. U kunt ook de ingebouwde **informatie typen** configureren door extra Zoek patronen toe te voegen, een aantal bestaande teken reeksen uit te scha kelen of door de beschrijving te wijzigen. U kunt geen ingebouwde **gegevens typen** verwijderen of hun namen bewerken. 
4. **Informatie typen** worden weer gegeven in volg orde van oplopende detectie classificatie, wat betekent dat de typen die hoger in de lijst staan, worden geprobeerd eerst te komen. Als u de rang schikking tussen gegevens typen wilt wijzigen, sleept u de typen naar de juiste plaats in de tabel of gebruikt u de knoppen **omhoog** en **omlaag** om de volg orde te wijzigen. 
5. Klik op **OK** wanneer u klaar bent.
6. Nadat u klaar bent met het beheren van uw gegevens typen, moet u de relevante typen koppelen aan de relevante labels door te klikken op **configureren** voor een bepaald label en zo nodig informatie typen toe te voegen of te verwijderen.
7. Zorg ervoor dat u in de Blade **Hoofdlabels** op **Opslaan** klikt om al uw wijzigingen toe te passen.
 
Nadat uw Information Protection-beleid volledig is gedefinieerd en opgeslagen, is dit van toepassing op de classificatie van gegevens op alle Azure SQL-data bases in uw Tenant.

## <a name="manage-sql-information-protection-using-azure-powershell"></a>SQL Information Protection beheren met Azure PowerShell

- [Get-AzSqlInformationProtectionPolicy](https://docs.microsoft.com/powershell/module/az.security/get-azsqlinformationprotectionpolicy): haalt het effectief beveiligings beleid voor TENANT-SQL information op.
- [Set-AzSqlInformationProtectionPolicy](https://docs.microsoft.com/powershell/module/az.security/set-azsqlinformationprotectionpolicy): Hiermee stelt u het effectief beveiligings beleid voor TENANT-SQL-gegevens in.
 
## <a name="next-steps"></a>Volgende stappen
 
In dit artikel hebt u geleerd hoe u een SQL Information Protection-beleid definieert in Azure Security Center. Zie [Azure SQL database gegevens detectie en-classificatie](../azure-sql/database/data-discovery-and-classification-overview.md)voor meer informatie over het gebruik van SQL Information Protection voor het classificeren en beveiligen van gevoelige gegevens in uw SQL-data bases. 

Raadpleeg de volgende artikelen voor meer informatie over beveiligings beleid en gegevens beveiliging in Azure Security Center:
 
- [Beveiligings beleid instellen in azure Security Center](tutorial-security-policy.md): meer informatie over het configureren van beveiligings beleid voor uw Azure-abonnementen en-resource groepen
- [Azure Security Center gegevens beveiliging](security-center-data-security.md): meer informatie over het Security Center beheren en beveiligen van gegevens
