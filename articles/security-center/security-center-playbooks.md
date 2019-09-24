---
title: Beveiligingsplaybook in Azure Security Center | Microsoft Docs
description: In dit document wordt uitgelegd hoe u beveiligingsplaybooks gebruikt in Azure Security Center voor het automatiseren van reacties op beveiligingsincidenten.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: a8c45ddf-5c4c-4393-b6e9-46ed1f91bf5f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2018
ms.author: memildin
ms.openlocfilehash: faf297ef4b07b76f98bb7773c636cd8f6228bbc0
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/23/2019
ms.locfileid: "71201741"
---
# <a name="security-playbook-in-azure-security-center-preview"></a>Beveiligingsplaybook in Azure Security Center (Preview)
In dit document wordt uitgelegd hoe u beveiligingsplaybooks gebruikt in Azure Security Center voor het reageren op beveiligingsproblemen.

## <a name="what-is-security-playbook-in-security-center"></a>Wat is een beveiligingsplaybook in Security Center?
Beveiligingsplaybooks bestaan uit een verzameling procedures die in Security Center worden uitgevoerd nadat een bepaalde playbook is geactiveerd door de geselecteerde waarschuwing. Met behulp van beveiligingsplaybooks kunt u uw reactie op een specifieke beveiligingswaarschuwing van Security Center automatiseren en organiseren. Beveiligingsplaybooks in Security Center zijn gebaseerd op [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps), wat betekent dat u de sjablonen kunt gebruiken die worden aangeboden in de categorie Beveiliging van Logic Apps-sjablonen, dat u ze kunt aanpassen op basis van uw behoeften of dat u nieuwe playbooks kunt maken met de [werkstroom van Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-create-a-logic-app), waarbij u Security Center als de trigger gebruikt.

> [!NOTE]
> Playbooks maken gebruik van Azure Logic Apps, en hiervoor worden kosten in rekening gebracht. Ga naar de pagina met prijzen voor [Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/) voor meer informatie.

## <a name="how-to-create-a-security-playbook-from-security-center"></a>Een beveiligingsplaybook maken vanuit Security Center
Volg deze stappen om een nieuwe beveiligingsplaybook te maken vanuit Security Center:

1.  Open het dashboard **Security Center**.
2.  Ga in het linkerdeelvenster naar **Automation & Orchestration** en klik op **Playbooks (Preview)** .

    ![Logische app](./media/security-center-playbooks/security-center-playbooks-fig17.png)

3. Klik op de pagina **Security Center - Playbooks (Preview)** op de knop **Toevoegen**.

    ![Logische app maken](./media/security-center-playbooks/security-center-playbooks-fig2.png)

4. Geef op de pagina **Logische app maken** de benodigde gegevens op voor het maken van de nieuwe logische app en klik op de knop **Maken**. Zodra de app is gemaakt, wordt de nieuwe playbook weergegeven in de lijst. Als dat niet het geval is, klikt u op de knop **Vernieuwen**. Als de playbook in de lijst staat, klikt u erop om de playbook te gaan bewerken.

    ![Logische app maken](./media/security-center-playbooks/security-center-playbooks-fig3.png)

5. U ziet het venster **Ontwerper van logische app**. Klik op **Lege logische app** om een nieuwe playbook te maken. U kunt ook **Beveiliging** selecteren onder de categorieën en een van de sjablonen gebruiken.

    ![Ontwerper van logische app](./media/security-center-playbooks/security-center-playbooks-fig4.png)

6. Typ *Azure Security Center* in het veld **Alle connectors en triggers doorzoeken** en selecteer **Wanneer een reactie op een Azure Security Center-waarschuwing is geactiveerd**.

    ![Trigger](./media/security-center-playbooks/security-center-playbooks-fig12.png)

7. Nu kunt u bepalen wat er moet gebeuren wanneer de playbook wordt geactiveerd. U kunt een actie, een logische voorwaarde, switch case-voorwaarden of lussen toevoegen.

    ![Ontwerper van logische app](./media/security-center-playbooks/security-center-playbooks-fig5.png)

## <a name="how-to-run-a-security-playbook-in-security-center"></a>Een beveiligingsplaybook uitvoeren in Security Center

U kunt een beveiligingsplaybook uitvoeren in Security Center als u wilt organiseren, meer informatie wilt verkrijgen van andere services of wanneer u wilt herstellen. Ga als volgt te werk om de playbook te openen:

1.  Open het dashboard **Security Center**.
2.  Ga naar het linkerdeelvenster en klik onder **Detectie van bedreigingen** op **Beveiligingsincidenten en -waarschuwingen**.

    ![Waarschuwingen](./media/security-center-playbooks/security-center-playbooks-fig6.png)

3.  Klik op de waarschuwing die u wilt onderzoeken.
4.  Klik boven aan de pagina van de waarschuwing op de knop **Playbook uitvoeren**.

    ![Playbook uitvoeren](./media/security-center-playbooks/security-center-playbooks-fig7.png)

5. Selecteer op de pagina Playbooks de playbook die u wilt uitvoeren en klik op de knop **Uitvoeren**. Als u de playbook wilt zien voordat deze wordt uitgevoerd, klikt u erop om de playbook te bekijken in de ontwerpfunctie.

    ![Playbooks](./media/security-center-playbooks/security-center-playbooks-fig13.png)

### <a name="history"></a>Geschiedenis

Nadat u de playbook hebt uitgevoerd, kunt u ook eerdere uitvoeringen bekijken, en stappen met meer informatie over de status van eerdere uitgevoerd playbooks. De geschiedenis bevat per waarschuwing de bijbehorende context, wat betekent dat de playbookgeschiedenis die u op deze pagina ziet, is gecorreleerd aan de waarschuwing die deze playbook heeft geactiveerd.

![Geschiedenis](./media/security-center-playbooks/security-center-playbooks-fig16.png)

Als u meer details wilt zien van de uitvoering van een bepaalde playbook, klikt u op de playbook zelf. U ziet dan de uitvoeringspagina van de logische app met de volledige werkstroom.

![Details](./media/security-center-playbooks/security-center-playbooks-fig14.png)

In deze werkstroom kunt u zien hoelang de uitvoering van elke taak heeft geduurd en u kunt een taak uitvouwen om het resultaat te zien.

### <a name="changing-an-existing-playbook"></a>Een bestaande playbook wijzigen

U kunt een bestaande playbook in Security Center wijzigen om een actie of voorwaarden toe te voegen. Hiervoor hoeft u alleen op het tabblad Playbooks op de naam te klikken van de playbook die u wilt wijzigen om de ontwerpfunctie voor logische apps te starten.

> [!NOTE]
> Meer informatie over het maken van uw eigen playbook met Azure Logic App vindt u in [Uw eerste werkstroom voor logische apps maken om processen tussen cloud-apps en cloudservices te automatiseren](https://docs.microsoft.com/azure/logic-apps/logic-apps-create-a-logic-app).


## <a name="see-also"></a>Zie ook
In dit document hebt u kunnen lezen hoe u playbooks gebruikt in Azure Security Center. Zie de volgende onderwerpen voor meer informatie over het Azure Beveiligingscentrum:

* [Beveiligingswaarschuwingen beheren en erop reageren in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Informatie over het beheren van waarschuwingen en het reageren op beveiligingsincidenten in Security Center.
* [Beveiligingsstatus bewaken in Azure Security Center](security-center-monitoring.md). Meer informatie over het controleren van de status van uw Azure-resources.
* [Beveiligingswaarschuwingen in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Meer informatie over de verschillende typen beveiligingswaarschuwingen.
* [Handleiding voor het oplossen van problemen met Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Informatie over het oplossen van veelvoorkomende problemen met Security Center.
* [Veelgestelde vragen over Azure Security Center](security-center-faq.md). Raadpleeg de veelgestelde vragen over het gebruik van de service.
* [Azure-beveiligingsblog](https://blogs.msdn.com/b/azuresecurity/). Raadpleeg de blogberichten over beveiliging en naleving in Azure.
