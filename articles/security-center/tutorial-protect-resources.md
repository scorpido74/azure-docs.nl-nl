---
title: 'Zelfstudie voor Azure Security Center: uw resources beveiligen met Azure Security Center | Microsoft Docs'
description: Deze zelf studie laat zien hoe u een just-in-time-VM-toegangs beleid en een toepassings beheer beleid configureert.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/03/2018
ms.author: memildin
ms.openlocfilehash: 8cb07f3447e50528a94811f33a2142086f698586
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996341"
---
# <a name="tutorial-protect-your-resources-with-azure-security-center"></a>Zelfstudie: Uw resources beveiligen met Azure Security Center
Security Center beperkt de blootstelling aan bedreigingen met behulp van toegangs- en toepassingsbesturingselementen om schadelijke activiteiten te blokkeren. Met Just-in-time (JIT) virtuele machine (VM) wordt de bloot stelling aan aanvallen beperkt doordat u permanente toegang tot virtuele machines kunt weigeren. U biedt in plaats daarvan beheerde en gecontroleerde toegang tot VM's, alleen wanneer dat nodig is. Besturingselementen voor adaptieve toepassingen helpen u om VM's beter te beschermen tegen malware door te beheren welke toepassingen op uw VM's kunnen worden uitgevoerd. Security Center maakt gebruik van machine learning om de processen te analyseren die op de virtuele machine worden uitgevoerd. Ook helpt het u op basis van deze informatie regels voor opname in de whitelist toe te passen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een just-in-time-VM-toegangs beleid configureren
> * Een toepassingsbeheerbeleid configureren

Als u nog geen Azure-abonnement hebt, maak dan een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten
Om de functies in deze zelfstudie te doorlopen, moet u zich in de Standard-prijscategorie van Security Center bevinden. U kunt Security Center Standard kosteloos proberen. Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/security-center/) voor meer informatie. In de snelstartgids [Onboard your Azure subscription to Security Center Standard](security-center-get-started.md) (Uw Azure-abonnement registreren voor Security Center Standard) wordt u begeleid bij het upgraden naar Standard.

## <a name="manage-vm-access"></a>VM-toegang beheren
JIT-VM-toegang kan worden gebruikt om binnenkomend verkeer naar uw Azure-VM's te blokkeren, zodat u minder kwetsbaar bent voor aanvallen maar tegelijkertijd eenvoudig toegang wordt geboden om verbinding met VM's te kunnen maken wanneer dat nodig is.

Beheerpoorten hoeven niet te allen tijde geopend te zijn. Ze hoeven alleen geopend te zijn wanneer u bent verbonden met de VM, bijvoorbeeld om beheer- of onderhoudstaken uit te voeren. Wanneer just-in-time is ingeschakeld, gebruikt Security Center NSG-regels (netwerk beveiligings groep) waarmee de toegang tot beheer poorten wordt beperkt zodat deze niet kunnen worden benaderd door aanvallers.

1. Selecteer in het hoofd menu van Security Center **just-in-time-VM-toegang** onder **geavanceerde Cloud beveiliging**.

   ![Just-in-time-toegang voor virtuele machines][1]

   **Just-in-time-VM-toegang** biedt informatie over de status van uw vm's:

   - **Geconfigureerd** : vm's die zijn geconfigureerd voor ondersteuning van just-in-time-VM-toegang.
   - **Aanbevolen** : vm's die just-in-time-VM-toegang kunnen ondersteunen, maar die niet zijn geconfigureerd voor.
   - **Geen aanbeveling**: redenen waarom een VM mogelijk niet wordt aanbevolen zijn:

     - Ontbrekende NSG: voor de just-in-time-oplossing moet een NSG aanwezig zijn.
     - Klassieke VM-Security Center just-in-time-VM-toegang ondersteunt momenteel alleen Vm's die via Azure Resource Manager zijn geïmplementeerd.
     - Andere-een VM bevindt zich in deze categorie als de just-in-time-oplossing is uitgeschakeld in het beveiligings beleid van het abonnement of de resource groep, of als er geen openbaar IP-adres voor de VM is en er geen NSG aanwezig is.

2. Selecteer een aanbevolen VM en klik op **JIT inschakelen op 1 VM** om een just-in-time-beleid voor die VM te configureren:

   U kunt de standaard poorten opslaan die worden Security Center aanbevolen of u kunt een nieuwe poort toevoegen en configureren waarop u de just-in-time-oplossing wilt inschakelen. In deze zelfstudie gaan we een poort toevoegen door **Toevoegen** te selecteren.

   ![Poortconfiguratie toevoegen][2]

3. Onder **Poortconfiguratie toevoegen** kunt u het volgende identificeren:

   - De poort
   - Het protocoltype
   - Toegestane bron-IP's: toegestane IP-bereiken om toegang te verkrijgen met een goedgekeurde aanvraag
   - Maximale aanvraagtijd: maximaal tijdvenster dat een specifieke poort geopend kan zijn

4. Selecteer **OK** om op te slaan.

## <a name="harden-vms-against-malware"></a>Virtuele machines beschermen tegen malware
Met Besturingselementen voor adaptieve toepassingen kunt u een set toepassingen definiëren die in de geconfigureerde resourcegroepen mogen worden uitgevoerd. Een van de voordelen hiervan is dat uw VM's tegen malware worden beschermd. Security Center maakt gebruik van machine learning om de processen te analyseren die op de virtuele machine worden uitgevoerd. Ook helpt het u op basis van deze informatie regels voor opname in de whitelist toe te passen.

1. Ga terug naar het hoofdmenu van Security Center. Selecteer onder **GEAVANCEERDE CLOUDBEVEILIGING** de optie **Besturingselementen voor adaptieve toepassingen**.

   ![Besturingselementen voor adaptieve toepassingen][3]

   De sectie **Resourcegroepen** bevat drie tabbladen:

   - **Geconfigureerd**: een lijst met resourcegroepen met VM's waarvoor toepassingsbeheer is geconfigureerd.
   - **Aanbevolen**: een lijst met resourcegroepen waarvoor toepassingsbeheer wordt aanbevolen.
   - **Geen aanbeveling**: een lijst met resourcegroepen met VM's waarvoor geen aanbevelingen zijn gedaan voor het gebruik van toepassingsbeheer. Hierbij kan het bijvoorbeeld gaan om virtuele machines waarop toepassingen steeds wisselen en geen stabiele status hebben.

2. Selecteer het tabblad **Aanbevolen** voor een lijst met resourcegroepen waarvoor toepassingsbeheer wordt aanbevolen.

   ![Aanbevelingen voor toepassingsbeheer][4]

3. Selecteer een resourcegroep om de optie **Regels voor toepassingsbeheer maken** te openen. Bekijk in **VM's selecteren** de lijst met aanbevolen virtuele machines en schakel alle virtuele machines uit waarop u geen toepassingsbeheer wilt toepassen. Bekijk in **Processen selecteren voor de regels voor opname in de whitelist** de lijst met aanbevolen toepassingen en schakel alle toepassingen uit waarop u geen toepassingsbeheer wilt toepassen. De lijst bevat:

   - **NAAM**: het volledige toepassingspad
   - **PROCESSEN**: hoeveel toepassingen zich binnen elk pad bevinden
   - **ALGEMEEN**: 'Ja' geeft aan dat deze processen zijn uitgevoerd op de meeste virtuele machines in deze resourcegroep
   - **EXPLOITEERBAAR**: er wordt een waarschuwingspictogram weergegeven als de toepassingen door een aanvaller kunnen worden gebruikt om opname in de whitelist met toepassingen te omzeilen. U wordt aanbevolen om deze toepassingen te controleren voordat ze worden goedgekeurd.

4. Selecteer **Maken** nadat u uw selecties hebt gemaakt.

## <a name="clean-up-resources"></a>Resources opschonen
Andere snelstartgidsen en zelfstudies in deze verzameling zijn gebaseerd op deze snelstartgids. Als u de volgende snelstartgidsen en zelfstudies ook wilt doornemen, blijf dan de prijscategorie Standard gebruiken en houd automatische inrichting ingeschakeld. Als u niet wilt doorgaan of wilt terugkeren naar de laag gratis:

1. Ga terug naar het hoofdmenu van Security Center en selecteer **Beveiligingsbeleid**.
2. Selecteer het abonnement of het beleid dat u wilt terugzetten op Gratis. **Beveiligingsbeleid** wordt geopend.
3. Selecteer onder **BELEIDSONDERDELEN** de optie **Prijscategorie**.
4. Selecteer **Gratis** om het abonnement te wijzigen van de Standard-laag in de Gratis laag.
5. Selecteer **Opslaan**.

Als u automatisch inrichten wilt uitschakelen:

1. Ga terug naar het hoofdmenu van Security Center en selecteer **Beveiligingsbeleid**.
2. Selecteer het abonnement waarvoor u automatisch inrichten wilt uitschakelen.
3. Ga naar **Beveiligingsbeleid – Gegevensverzameling** en selecteer onder **Onboarding** de optie **Uit** om automatisch inrichten uit te schakelen.
4. Selecteer **Opslaan**.

>[!NOTE]
> Wanneer u automatische inrichting uitschakelt, wordt MMA niet verwijderd van Azure-VM's waarop de agent is ingericht. Door automatische inrichting uit te schakelen, wordt de beveiligingsbewaking voor uw resources beperkt.
>

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u geleerd hoe u blootstelling aan bedreigingen kunt beperken met de volgende handelingen:

> [!div class="checklist"]
> * Een just-in-time-VM-toegangs beleid configureren om alleen beheerde en gecontroleerde toegang tot Vm's te bieden als dat nodig is
> * Een beleid voor besturingselementen voor adaptieve toepassingen configureren om te bepalen welke toepassingen op uw VM's kunnen worden uitgevoerd

Ga naar de volgende zelfstudie voor meer informatie over het reageren op beveiligingsincidenten.

> [!div class="nextstepaction"]
> [Zelfstudie: Reageren op beveiligingsincidenten](tutorial-security-incident.md)

<!--Image references-->
[1]: ./media/tutorial-protect-resources/just-in-time-vm-access.png
[2]: ./media/tutorial-protect-resources/add-port.png
[3]: ./media/tutorial-protect-resources/adaptive-application-control-options.png
[4]: ./media/tutorial-protect-resources/recommended-resource-groups.png
