---
title: SQL-informatiebeveiliging aanpassen - Azure Security Center
description: Meer informatie over het aanpassen van het beleid voor informatiebescherming in Azure Security Center.
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
ms.date: 04/29/2019
ms.author: memildin
ms.openlocfilehash: 9c776a32b4a35c72fc40a16afb87db9896a763cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75611063"
---
# <a name="customize-the-sql-information-protection-policy-in-azure-security-center-preview"></a>Het SQL-informatiebeveiligingsbeleid aanpassen in Azure Security Center (Voorbeeld)
 
U een SQL-informatiebeveiligingsbeleid voor uw gehele Azure-tenant definiëren en aanpassen in Azure Security Center.

Informatiebeveiliging is een geavanceerde beveiligingsmogelijkheid voor het ontdekken, classificeren, labelen en rapporteren van gevoelige gegevens in uw Azure-gegevensbronnen. Het ontdekken en classificeren van uw meest gevoelige gegevens (zakelijk, financieel, gezondheidszorg, persoonsgegevens, enz.) kan een centrale rol spelen in uw organisatieinformatiebescherming. Dit kan dienen als infrastructuur om:
- Helpen voldoen aan de privacynormen en nalevingsvereisten voor regelgeving
- Beveiligingsscenario's zoals monitoring (auditing) en waarschuwing en waarschuwing over afwijkende toegang tot gevoelige gegevens
- Controle van de toegang tot en verharding van de beveiliging van gegevensopslag met zeer gevoelige gegevens
 
[SQL Information Protection](../sql-database/sql-database-data-discovery-and-classification.md) implementeert dit paradigma voor uw SQL-gegevensopslag, die momenteel wordt ondersteund voor Azure SQL Database. SQL Information Protection detecteert en classificeert automatisch potentieel gevoelige gegevens, biedt een etiketteringsmechanisme voor het voortdurend taggen van de gevoelige gegevens met classificatiekenmerken en biedt een gedetailleerd dashboard met de classificatiestatus van de database. Bovendien berekent het de gevoeligheid van SQL-query's met resultaatset, zodat query's die gevoelige gegevens extraheren expliciet kunnen worden gecontroleerd en de gegevens kunnen worden beschermd. Zie [Azure SQL Database Data Discovery and Classification](../sql-database/sql-database-data-discovery-and-classification.md)voor meer informatie over SQL Information Protection.
 
Het classificatiemechanisme is gebaseerd op twee primaire constructies die deel uitmaken van de classificatietaxonomie - **Labels** en **informatietypen**.
- **Labels** : de belangrijkste classificatiekenmerken die worden gebruikt om het gevoeligheidsniveau te definiëren van de gegevens die in de kolom zijn opgeslagen. 
- **Informatietypen** : biedt extra granulariteit in het type gegevens dat in de kolom is opgeslagen.
 
Informatiebescherming wordt geleverd met een ingebouwde set labels en informatietypen, die standaard worden gebruikt. Als u deze labels en typen wilt aanpassen, u het beleid voor informatiebeveiliging aanpassen in Security Center.
 
## <a name="customize-the-information-protection-policy"></a>Het informatiebeschermingsbeleid aanpassen
Als u het beleid voor informatiebeveiliging voor uw Azure-tenant wilt aanpassen, moet u beheerdersbevoegdheden hebben [voor de hoofdbeheergroep van de tenant.](security-center-management-groups.md) 
 
1. Ga in het hoofdmenu van het Beveiligingscentrum onder **RESOURCE SECURITY HYGIENE** naar Gegevens & **opslag** en klik op de knop SQL **Information Protection.**

   ![Beleid voor gegevensbeveiliging configureren](./media/security-center-info-protection-policy/security-policy.png) 
 
2. Op de pagina **SQL Information Protection** u uw huidige set labels bekijken. Dit zijn de belangrijkste classificatiekenmerken die worden gebruikt om het gevoeligheidsniveau van uw gegevens te categoriseren. Vanaf hier u de **labels voor informatiebeveiliging** en **informatietypen** voor de tenant configureren. 
 
### <a name="customizing-labels"></a>Labels aanpassen
 
1. U een bestaand label bewerken of verwijderen of een nieuw label toevoegen. Als u een bestaand label wilt bewerken, selecteert u dat label en klikt u op **Configureren**, bovenaan of in het contextmenu aan de rechterkant. Als u een nieuw label wilt toevoegen, klikt u op **Label maken** in de bovenste menubalk of onder aan de tabel labels.
2. In het scherm **Gevoeligheidslabel configureren** u de labelnaam en de beschrijving maken of wijzigen. U ook instellen of het label actief of uitgeschakeld is door de in- of **uitschakeling van** het in- of uitschakelen van het label. Ten slotte u informatietypen toevoegen of verwijderen die aan het label zijn gekoppeld. Alle gegevens die worden ontdekt en die overeenkomen met dat informatietype, bevatten automatisch het bijbehorende gevoeligheidslabel in de classificatieaanbevelingen.
3. Klik op **OK**.
 
   ![Gevoeligheidslabel configureren](./media/security-center-info-protection-policy/config-sensitivity-label.png)
 
4. Labels worden weergegeven in volgorde van oplopende gevoeligheid. Als u de rangschikking tussen labels wilt wijzigen, sleept u de labels om ze opnieuw in de tabel te ordenen of gebruikt u de knoppen **Omhoog** en **Omlaag om** de volgorde te wijzigen. 
 
    ![Beleid voor gegevensbeveiliging configureren](./media/security-center-info-protection-policy/move-up.png)
 
5. Klik op **Opslaan** boven aan het scherm als u klaar bent.
 
 
## <a name="adding-and-customizing-information-types"></a>Informatietypen toevoegen en aanpassen
 
1. U informatietypen beheren en aanpassen door op **Informatietypen beheren**te klikken.
2. Als u een nieuw **teksttype Informatie wilt**toevoegen, selecteert u **Het teksttype Informatie maken** in het bovenste menu. U een naam, beschrijving en tekenreeksen van zoekpatroons configureren voor het **teksttype Informatie**. Zoekpatroontekenreeksen kunnen optioneel zoekwoorden met jokertekens gebruiken (met behulp van het teken '%'), dat de geautomatiseerde detectieengine gebruikt om gevoelige gegevens in uw databases te identificeren op basis van de metagegevens van de kolommen.
 
    ![Beleid voor gegevensbeveiliging configureren](./media/security-center-info-protection-policy/info-types.png)
 
3. U de ingebouwde **informatietypen** ook configureren door extra tekenreeksen voor zoekpatroontoe te voegen, een aantal bestaande tekenreeksen uit te schakelen of door de beschrijving te wijzigen. U ingebouwde **informatietypen** niet verwijderen of hun namen bewerken. 
4. **Informatietypen** worden weergegeven in volgorde van oplopende detectierangschikking, wat betekent dat de typen hoger in de lijst eerst proberen overeen te komen. Als u de rangschikking tussen informatietypen wilt wijzigen, sleept u de typen naar de juiste plek in de tabel of gebruikt u de knoppen **Omhoog** en **Omlaag om** de volgorde te wijzigen. 
5. Klik op **OK** als u klaar bent.
6. Nadat u het beheer van uw gegevenstypen hebt voltooid, moet u de relevante typen koppelen aan de relevante labels, door op **Configureren** voor een bepaald label te klikken en informatietypen naar gelang van het geval toe te voegen of te verwijderen.
7. Klik op **Opslaan** in het **hoofdlabelblad om** al uw wijzigingen toe te passen.
 
Nadat uw informatiebeveiligingsbeleid volledig is gedefinieerd en opgeslagen, is dit van toepassing op de classificatie van gegevens op alle Azure SQL-databases in uw tenant.
 
 
## <a name="next-steps"></a>Volgende stappen
 
In dit artikel leert u over het definiëren van een SQL Information Protection-beleid in Azure Security Center. Zie [Azure SQL Database Data Discovery and Classification](../sql-database/sql-database-data-discovery-and-classification.md)voor meer informatie over het gebruik van SQL Information Protection voor het classificeren en beveiligen van gevoelige gegevens in uw SQL-databases. 

Zie de volgende artikelen voor meer informatie over beveiligingsbeleid en gegevensbeveiliging in Azure Security Center:
 
- [Beveiligingsbeleid instellen in Azure Security Center:](tutorial-security-policy.md)meer informatie over het configureren van beveiligingsbeleid voor uw Azure-abonnementen en brongroepen
- [Gegevensbeveiliging azure Security Center:](security-center-data-security.md)ontdek hoe Beveiligingscentrum gegevens beheert en beschermt
