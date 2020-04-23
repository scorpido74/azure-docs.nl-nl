---
title: Adaptieve toepassingsbesturingselementen in Azure Security Center
description: Dit document helpt u bij het gebruik van adaptief toepassings beheer in Azure Security Center voor het white list van toepassingen die worden uitgevoerd op Azure-machines.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9268b8dd-a327-4e36-918e-0c0b711e99d2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/23/2019
ms.author: memildin
ms.openlocfilehash: 1dc94c5ec08cc27fb1819ccc16fd766c62aad796
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77604668"
---
# <a name="adaptive-application-controls"></a>Adaptieve toepassingsbesturingselementen
Lees hoe u toepassingsbeheer configureert in Azure Security Center met behulp van dit stapsgewijze overzicht.

## <a name="what-are-adaptive-application-controls-in-security-center"></a>Wat zijn adaptieve toepassingsbesturingselementen in Security Center?
Adaptief toepassings beheer is een intelligente, geautomatiseerde en end-to-end oplossing van Azure Security Center waarmee u kunt bepalen welke toepassingen kunnen worden uitgevoerd op uw Azure-en niet-Azure-machines (Windows en Linux). Met andere voor delen kunt u uw computers onder andere beschermen tegen malware. Security Center gebruikt machine learning voor het analyseren van de toepassingen die op uw computers worden uitgevoerd en maakt een lijst met toegestane gegevens van deze intelligentie. Deze mogelijkheid vereenvoudigt het proces van het configureren en onderhouden van beleids regels voor het toestaan van toepassingen, zodat u het volgende kunt doen:

- Blok keer of Meld u aan bij pogingen om schadelijke toepassingen uit te voeren, met inbegrip van degenen die anders kunnen worden gemist door antimalware-oplossingen.
- Voldoen aan het beveiligingsbeleid van uw organisatie waarin alleen het gebruik van gelicentieerde software is toegestaan.
- Vermijden dat in uw omgeving ongewenste software wordt gebruikt.
- Vermijden dat oude en niet-ondersteunde apps worden uitgevoerd.
- Voorkomen dat bepaalde softwareprogramma's worden uitgevoerd die in uw organisatie niet zijn toegestaan.
- De IT-afdeling in staat stellen de toegang tot gevoelige gegevens te beheren via het gebruik van apps.

> [!NOTE]
> Voor niet-Azure-en Linux-computers worden adaptieve toepassings besturings elementen alleen in de controle modus ondersteund.

## <a name="how-to-enable-adaptive-application-controls"></a>Hoe worden adaptieve toepassingsbesturingselementen ingeschakeld?

Met besturings elementen voor adaptieve toepassingen kunt u een set toepassingen definiëren die op geconfigureerde machine groepen mogen worden uitgevoerd. Deze functie is beschikbaar voor zowel Azure-als niet-Azure-Windows (alle versies, klassieke of Azure Resource Manager) en Linux-machines. Voer de volgende stappen uit om de lijsten met toegestane apps van uw toepassing te configureren:

1. Open het dashboard van **Security Center**.

1. Ga naar het deelvenster aan de linkerkant en selecteer **Adaptieve toepassingsbesturingselementen** onder **Geavanceerde cloudbeveiliging**.

    [![Defensie](./media/security-center-adaptive-application/security-center-adaptive-application-fig1-new.png)](./media/security-center-adaptive-application/security-center-adaptive-application-fig1-new.png#lightbox)

De pagina **Besturingselementen voor adaptieve toepassingen** wordt geopend.

![besturingselementen](./media/security-center-adaptive-application/security-center-adaptive-application-fig2.png)

De sectie **Groepen virtuele machines** bevat drie tabbladen:

* **Geconfigureerd**: een lijst van groepen met de VM's waarvoor toepassingsbeheer is geconfigureerd.
* **Aanbevolen**: een lijst met groepen waarvoor toepassingsbeheer wordt aanbevolen. Security Center maakt gebruik van machine learning om virtuele machines te identifiveren die baat zouden hebben bij het gebruik van toepassingsbeheer. Hierbij wordt gekeken of de virtuele machines consequent dezelfde toepassingen uitvoeren.
* **Geen aanbeveling**: een lijst van groepen met VM's waarvoor geen aanbevelingen zijn gedaan voor het gebruik van toepassingsbeheer. Hierbij kan het bijvoorbeeld gaan om virtuele machines waarop toepassingen steeds wisselen en geen stabiele status hebben.

> [!NOTE]
> Security Center gebruikt een eigen clustering-algoritme voor groepen met VM's om ervoor te zorgen dat vergelijkbare VM’s het optimale aanbevolen toepassingsbeheerbeleid krijgen.
>
>

### <a name="configure-a-new-application-control-policy"></a>Een nieuw toepassingsbeheerbeleid configureren

1. Selecteer het tabblad **Aanbevolen** voor een lijst met groepen met aanbevelingen voor toepassings beheer:

   ![Aanbevolen](./media/security-center-adaptive-application/security-center-adaptive-application-fig3.png)

   De lijst bevat:

   - **Groeps naam**: de naam van het abonnement en de groep
   - **Vm's en computers**: het aantal virtuele machines in de groep
   - **Status**: de status van de aanbevelingen
   - **Ernst**: het Ernst niveau van de aanbevelingen

2. Klik op een groep om de optie **regels voor toepassings beheer maken** te openen.

   [![Regels voor toepassingsbeheer](./media/security-center-adaptive-application/security-center-adaptive-application-fig4.png)](./media/security-center-adaptive-application/security-center-adaptive-application-fig4.png#lightbox)

3. Bekijk in de **Select vm's**de lijst met aanbevolen vm's en schakel alle selectie vakjes uit waarop u geen white list-beleid wilt Toep assen. Vervolgens ziet u twee lijsten:

   - **Aanbevolen toepassingen**: een lijst met toepassingen die veelvuldig voor komen op de virtuele machines in deze groep en die worden aanbevolen om uit te voeren.
   - **Meer toepassingen**: een lijst met toepassingen die minder frequent zijn op de virtuele machines in deze groep of die Exploitables worden genoemd (Zie meer hieronder) en aanbevolen voor controle.

4. Bekijk de toepassingen in elk van de lijsten en schakel uit wat u niet wilt toepassen. Elke lijst bevat:

   - **Naam**: de certificaat gegevens of het volledige pad van een toepassing
   - **BESTANDSTYPEN**: het bestandstype van de toepassing. Dit kan EXE, script, MSI of een permutatie van deze typen zijn.
   - **Exploitable**: een waarschuwings pictogram geeft aan of een specifieke toepassing door een aanvaller kan worden gebruikt om een lijst met toegestane toepassingen te omzeilen. U wordt aanbevolen om deze toepassingen te controleren voordat ze worden goedgekeurd.
   - **GEBRUIKERS**: gebruikers die worden aanbevolen om een toepassing uit te mogen voeren

5. Selecteer **Maken** nadat u uw selecties hebt gemaakt. <br>
   Nadat u maken hebt geselecteerd, worden met Azure Security Center automatisch de juiste regels gemaakt boven op de ingebouwde oplossing lijst met toegestane toepassingen beschikbaar op Windows-servers (AppLocker).

> [!NOTE]
> - Security Center heeft minimaal twee weken aan gegevens nodig om een basislijn te maken en de unieke aanbevelingen per groep virtuele machines te vullen. Voor nieuwe klanten van de Security Center-standaardcategorie worden groepen virtuele machines in het begin weergegeven op het tabblad *Geen aanbeveling*.
> - Adaptieve toepassingsbesturingselementen in Security Center bieden geen ondersteuning voor virtuele machines waarvoor al een AppLocker-beleid is ingeschakeld door een groepsbeleidsobject (GPO) of een lokaal beveiligingsbeleid.
> -  Als een beveiligings best practice, probeert Security Center altijd een uitgevers regel te maken voor toepassingen die zijn geselecteerd om te worden toegestaan. alleen als een toepassing geen informatie over de uitgever heeft (ook wel niet ondertekend), wordt er een padregel gemaakt voor het volledige pad van de specifieke toepassing.
>   

### <a name="editing-and-monitoring-a-group-configured-with-application-control"></a>Een groep die met toepassingsbeheer is geconfigureerd, bewerken en bewaken

1. Als u een groep wilt bewerken en controleren die is geconfigureerd met een lijst beleid voor het toestaan van toepassingen, keert u terug naar de pagina **adaptieve toepassings besturings elementen** en selecteert u **geconfigureerd** onder **groepen vm's**:

   ![Groepen](./media/security-center-adaptive-application/security-center-adaptive-application-fig5.png)

   De lijst bevat:

   - **Groeps naam**: de naam van het abonnement en de groep
   - **Vm's en computers**: het aantal virtuele machines in de groep
   - **Modus**: de controle modus meldt pogingen om toepassingen uit te voeren die niet voor komen in de lijst met toegestane apps. Met afdwingen kunnen toepassingen alleen worden uitgevoerd als ze zich in de acceptatie lijst bevinden
   - **Waarschuwingen**: eventuele huidige schendingen

2. Klik op een groep om wijzigingen aan te brengen op de pagina **toepassings beheer beleid bewerken** .

   ![Beveiliging](./media/security-center-adaptive-application/security-center-adaptive-application-fig6.png)

3. Onder **Beveiligingsmodus** kunt u een keuze maken uit de volgende opties:

   - **Controle**: in deze modus worden de regels niet afgedwongen door de oplossing voor toepassingsbeheer en wordt alleen de activiteit op de beveiligde virtuele machines gecontroleerd. Dit wordt aanbevolen voor scenario's waarin u eerst het algehele gedrag wilt observeren voordat de uitvoering van een app op de doel-VM wordt geblokkeerd.
   - **Afdwingen**: in deze modus worden de regels wel afgedwongen door de oplossing voor toepassingsbeheer. Ook worden toepassingen die niet mogen worden uitgevoerd, geblokkeerd.

   > [!NOTE]
   > -  De beveiligings modus **afdwingen** is uitgeschakeld tot verdere kennisgeving.
   > - Zoals eerder vermeld, wordt een nieuw beleid voor toepassingsbeheer altijd standaard geconfigureerd in de modus *Controle*. 
   >

4. Onder **beleids uitbreiding**voegt u een toepassingspad toe dat u wilt toestaan. Nadat u deze paden hebt toegevoegd, wordt door Security Center het beleid voor lijst met toegestane toepassingen op de virtuele machines in de geselecteerde groep VM'S bijgewerkt en worden de juiste regels voor deze toepassingen gemaakt, naast de regels die al aanwezig zijn.

5. Bekijk de huidige schendingen die worden vermeld in de sectie **recente waarschuwingen** . Klik op elke regel die u wilt omleiden naar de pagina **waarschuwingen** binnen Azure Security Center en Bekijk alle waarschuwingen die door Azure Security Center zijn gedetecteerd op de gekoppelde vm's.
   - **Waarschuwingen**: eventuele schendingen die zijn vastgelegd.
   - **Nee. van Vm's**: het aantal virtuele machines met dit waarschuwings type.

6. Onder **Publisher white list regels**, **Path white list Rules**en **hash white list-regels** kunt u zien welke toepassings white list-regels momenteel zijn geconfigureerd op de virtuele machines binnen een groep, volgens het type regel verzameling. Voor elke regel kunt u het volgende zien:

   - **Regel**: de specifieke para meters op basis waarvan een toepassing door AppLocker wordt gecontroleerd om te bepalen of een toepassing mag worden uitgevoerd.
   - **Bestands type**: de bestands typen die worden gedekt door een specifieke regel. Dit kan een van de volgende zijn: EXE, script, MSI of een permutatie van die bestands typen.
   - **Gebruikers**: naam of aantal gebruikers die een toepassing mogen uitvoeren die wordt gedekt door een toepassing white list regel.

   ![Regels voor opname in de whitelist](./media/security-center-adaptive-application/security-center-adaptive-application-fig9.png)

7. Klik op de drie punten aan het einde van elke regel als u de specifieke regel wilt verwijderen of de toegestane gebruikers wilt bewerken.

8. Nadat u wijzigingen hebt aangebracht in een beleid voor **adaptieve toepassings besturings elementen** , klikt u op **Opslaan**.

### <a name="not-recommended-list"></a>De lijst Niet aanbevolen

Security Center beveelt alleen white list-beleid van toepassingen aan voor virtuele machines waarop een stabiele set toepassingen wordt uitgevoerd. Opname in de whitelist wordt niet aanbevolen als de toepassingen op de bijbehorende virtuele machines voortdurend wisselen.

![Aanbeveling](./media/security-center-adaptive-application/security-center-adaptive-application-fig11.png)

De lijst bevat:
- **Groeps naam**: de naam van het abonnement en de groep
- **Vm's en computers**: het aantal virtuele machines in de groep

Met Azure Security Center kunt u ook een white list-beleid voor toepassingen definiëren op niet-aanbevolen groepen Vm's. Volg dezelfde principes als eerder beschreven, voor het configureren van een toepassing white list-beleid voor die groepen.

## <a name="move-a-vm-from-one-group-to-another"></a>Een virtuele machine verplaatsen van de ene groep naar een andere

 Wanneer u een virtuele machine verplaatst van de ene groep naar een andere, wordt het beleid voor toepassings beheer toegepast op wijzigingen in de instellingen van de groep waarnaar u het hebt verplaatst. U kunt een virtuele machine ook van een geconfigureerde groep naar een niet-geconfigureerde groep verplaatsen, wat resulteert in het verwijderen van een toepassings beheer beleid dat eerder is toegepast op de virtuele machine.

 1. Klik op het tabblad **geconfigureerd** van de pagina **adaptieve toepassings besturings elementen** op de groep waarnaar de VM momenteel moet worden verplaatst.
1. Klik op **geconfigureerde vm's en computers**.
1. Klik op de drie puntjes in de regel van de virtuele machine die u wilt verplaatsen en klik vervolgens op **verplaatsen**. Het venster **computer naar andere groep verplaatsen** wordt geopend.

    ![Beveiliging](./media/security-center-adaptive-application/adaptive-application-move-group.png)

 1. Selecteer de groep waarnaar u de virtuele machine wilt verplaatsen, klik op **computer verplaatsen**en klik op **Opslaan**.

    ![Beveiliging](./media/security-center-adaptive-application/adaptive-application-move-group2.png)

 > [!NOTE]
> Zorg ervoor dat u op **Opslaan** klikt nadat u op **computer verplaatsen**hebt geklikt. Als u niet op **Opslaan**klikt, wordt de computer niet verplaatst.

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u gebruik kunt maken van adaptieve toepassings beheer in Azure Security Center voor het white list van toepassingen die worden uitgevoerd in Azure-en niet-Azure-Vm's. Zie de volgende onderwerpen voor meer informatie over Azure Security Center:

* [Beveiligings waarschuwingen beheren en erop reageren in azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Informatie over het beheren van waarschuwingen en het reageren op beveiligingsincidenten in Security Center.
* [Beveiligings status bewaken in azure Security Center](security-center-monitoring.md). Meer informatie over het controleren van de status van uw Azure-resources.
* Meer [informatie over beveiligings waarschuwingen in azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Meer informatie over de verschillende typen beveiligingswaarschuwingen.
* [Azure Security Center probleemoplossings handleiding](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Informatie over het oplossen van veelvoorkomende problemen met Security Center.
* [Azure-beveiligings blog](https://blogs.msdn.com/b/azuresecurity/). Raadpleeg de blogberichten over beveiliging en naleving in Azure.
