---
title: 'Zelfstudie: Een draaiboek uitvoeren in Azure Sentinel'
description: 'Zelfstudie: In dit artikel wordt beschreven hoe u een draaiboek uitvoert in Azure Sentinel.'
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: e4afc5c8-ffad-4169-8b73-98d00155fa5a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2019
ms.author: yelevin
ms.openlocfilehash: f6adcb978dbe540d3bdd352089d4dde407d0fb4c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77585081"
---
# <a name="tutorial-set-up-automated-threat-responses-in-azure-sentinel"></a>Zelfstudie: Geautomatiseerde bedreigingen instellen in Azure Sentinel



Met deze zelfstudie u beveiligingsplaybooks in Azure Sentinel gebruiken om geautomatiseerde bedreigingsreacties in te stellen op beveiligingsgerelateerde problemen die door Azure Sentinel zijn gedetecteerd.


> [!div class="checklist"]
> * Playbooks begrijpen
> * Een draaiboek maken
> * Een draaiboek uitvoeren
> * Dreigingsreacties automatiseren


## <a name="what-is-a-security-playbook-in-azure-sentinel"></a>Wat is een beveiligingsplaybook in Azure Sentinel?

Een beveiligingsplaybook is een verzameling procedures die kunnen worden uitgevoerd vanuit Azure Sentinel in reactie op een waarschuwing. Een beveiligingsplaybook kan helpen bij het automatiseren en orkestreren van uw reactie en kan handmatig worden uitgevoerd of automatisch worden uitgevoerd wanneer specifieke waarschuwingen worden geactiveerd. Beveiligingsplaybooks in Azure Sentinel zijn gebaseerd op [Azure Logic Apps,](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps)wat betekent dat u alle kracht, aanpasbare en ingebouwde sjablonen van Logic Apps krijgt. Elk playbook wordt gemaakt voor het specifieke abonnement dat u kiest, maar als je naar de Playbooks-pagina kijkt, zie je alle playbooks voor geselecteerde abonnementen.

> [!NOTE]
> Playbooks maken gebruik van Azure Logic Apps, daarom zijn er kosten in rekening gebracht. Ga naar de pagina met prijzen voor [Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/) voor meer informatie.

Als u zich bijvoorbeeld zorgen maakt over kwaadwillenden die toegang hebben tot uw netwerkbronnen, u een waarschuwing instellen waarin wordt gezocht naar schadelijke IP-adressen die toegang hebben tot uw netwerk. Vervolgens u een draaiboek maken dat het volgende doet:
1. Wanneer de waarschuwing wordt geactiveerd, opent u een ticket in ServiceNow of een ander IT-ticketingsysteem.
2. Stuur een bericht naar uw beveiligingskanaal in Microsoft Teams of Slack om ervoor te zorgen dat uw beveiligingsanalisten op de hoogte zijn van het incident.
3. Stuur alle informatie in de waarschuwing naar uw senior netwerkbeheerder en beveiligingsbeheerder. Het e-mailbericht bevat ook twee knoppen voor gebruikersopties **Blokkeren** of **Negeren.**
4. Het draaiboek wordt nog steeds uitgevoerd nadat een reactie is ontvangen van de beheerders.
5. Als de beheerders **Blokkeren**kiezen, wordt het IP-adres geblokkeerd in de firewall en wordt de gebruiker uitgeschakeld in Azure AD.
6. Als de beheerders **Negeren**kiezen, wordt de waarschuwing gesloten in Azure Sentinel en wordt het incident gesloten in ServiceNow.

Beveiligingsplaybooks kunnen handmatig of automatisch worden uitgevoerd. Als u ze handmatig uitvoert, betekent dit dat wanneer u een waarschuwing ontvangt, u ervoor kiezen om een playbook on-demand uit te voeren als reactie op de geselecteerde waarschuwing. Als u ze automatisch uitvoert, betekent dit automatisch dat u tijdens het ontwerpen van de correlatieregel een of meer playbooks automatisch uitvoert wanneer de waarschuwing wordt geactiveerd.


## <a name="create-a-security-playbook"></a>Een beveiligingsplaybook maken

Volg de volgende stappen om een nieuw beveiligingsplaybook te maken in Azure Sentinel:

1. Open het **Azure Sentinel-dashboard.**
2. Selecteer Onder **Beheer**de optie **Playbooks**.

   ![Logische apps](./media/tutorial-respond-threats-playbook/playbookimg.png)

3. Klik op de pagina **Azure Sentinel - Playbooks** op **Knop Toevoegen.**

   ![Logische app maken](./media/tutorial-respond-threats-playbook/create-playbook.png) 

4. Typ op de **app-pagina Logica maken** de gevraagde informatie om uw nieuwe logische app te maken en klik op **Maken**. 

5. Selecteer in de [**Logic App Designer**](../logic-apps/logic-apps-overview.md)de sjabloon die u wilt gebruiken. Als u een sjabloon selecteert die referenties vereist, moet u deze verstrekken. U ook een nieuw leeg draaiboek maken. Selecteer **De app Lege logica**selecteren . 

   ![Ontwerper van logische app](./media/tutorial-respond-threats-playbook/playbook-template.png)

6. U wordt naar de Logic App Designer gebracht waar u nieuwe bouwen of de sjabloon bewerken. Voor meer informatie over het maken van een draaiboek met [Logic Apps](../logic-apps/logic-apps-create-logic-apps-from-templates.md).

7. Als u een leeg draaiboek maakt, typt u in het veld **Alle connectoren en triggers zoeken** Azure *Sentinel*en selecteert u Wanneer een reactie op een **Azure Sentinel-waarschuwing wordt geactiveerd.** <br>Nadat het is gemaakt, wordt het nieuwe draaiboek weergegeven in de **lijst Playbooks.** Als deze niet wordt weergegeven, klikt u op **Vernieuwen**.

1. Gebruik de functies **Entiteiten ophalen,** waarmee u de relevante entiteiten uit de lijst **Entiteiten** halen, zoals accounts, IP-adressen en hosts. Hiermee u acties uitvoeren op specifieke entiteiten.

7. Nu kunt u bepalen wat er moet gebeuren wanneer de playbook wordt geactiveerd. U een actie, logische voorwaarde, andere procedure of lussen toevoegen.

   ![Ontwerper van logische app](./media/tutorial-respond-threats-playbook/logic-app.png)

## <a name="how-to-run-a-security-playbook"></a>Een beveiligingsplaybook uitvoeren

U een draaiboek op aanvraag uitvoeren.

Een draaiboek op aanvraag uitvoeren:

1. Selecteer **op** de pagina Incidenten een incident en klik op **Alle details weergeven**.

2. Klik op het tabblad **Waarschuwingen** op de waarschuwing waarop u het draaiboek wilt uitvoeren en blader helemaal naar rechts en klik op **PlayBooks weergeven** en selecteer een draaiboek dat u wilt **uitvoeren** in de lijst met beschikbare playbooks op het abonnement. 



## <a name="automate-threat-responses"></a>Dreigingsreacties automatiseren

SIEM/SOC-teams kunnen regelmatig worden overspoeld met beveiligingswaarschuwingen. Het aantal gegenereerde waarschuwingen is zo groot, dat beschikbare beveiligingsbeheerders worden overweldigd. Dit resulteert maar al te vaak in situaties waar veel waarschuwingen niet kunnen worden onderzocht, waardoor de organisatie kwetsbaar is voor aanvallen die onopgemerkt blijven. 

Veel, zo niet de meeste, van deze waarschuwingen voldoen aan terugkerende patronen die kunnen worden aangepakt door specifieke en gedefinieerde herstelacties. Azure Sentinel stelt u al in staat om uw herstel in playbooks te definiëren. Het is ook mogelijk om real-time automatisering in te stellen als onderdeel van uw playbook-definitie, zodat u een gedefinieerde reactie op bepaalde beveiligingswaarschuwingen volledig automatiseren. Met behulp van real-time automatisering kunnen responsteams hun werklast aanzienlijk verminderen door de routinematige reacties op terugkerende typen waarschuwingen volledig te automatiseren, zodat u zich meer concentreren op unieke waarschuwingen, het analyseren van patronen, bedreigingsjacht en meer.

Reacties automatiseren:

1. Selecteer de waarschuwing waarvoor u het antwoord wilt automatiseren.
1. Kies op de pagina **Waarschuwingsregel bewerken** onder **Real-time automatisering**het **geactiveerde draaiboek** dat u wilt uitvoeren wanneer deze waarschuwingsregel is gekoppeld.
1. Selecteer **Opslaan**.

   ![real-time automatisering](./media/tutorial-detect-threats/rt-configuration.png)






## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een draaiboek in Azure Sentinel uitvoeren. Ga verder met hoe [u proactief jagen op bedreigingen](hunting.md) met Azure Sentinel.


