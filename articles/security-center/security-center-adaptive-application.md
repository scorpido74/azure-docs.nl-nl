---
title: Adaptieve toepassingsbesturingselementen in Azure Security Center
description: Met dit document u het adaptieve toepassingsbesturingselement gebruiken in Azure Security Center om toepassingen die in Azure-machines worden uitgevoerd, op de witte lijst te plaatsen.
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
Adaptive application control is een intelligente, geautomatiseerde, end-to-end oplossing van Azure Security Center die u helpt te bepalen welke toepassingen kunnen worden uitgevoerd op uw Azure- en niet-Azure-machines (Windows en Linux). Dit helpt onder andere om uw machines te verharden tegen malware. Security Center maakt gebruik van machine learning om de toepassingen die op uw machines worden uitgevoerd te analyseren en maakt een lijst met toegestane gegevens van deze intelligentie. Deze mogelijkheid vereenvoudigt het proces van het configureren en onderhouden van toepassingsbeleid, zodat u:

- Blokkeren of waarschuwen voor pogingen om kwaadaardige toepassingen uit te voeren, inclusief toepassingen die anders zouden kunnen worden gemist door antimalwareoplossingen.
- Voldoen aan het beveiligingsbeleid van uw organisatie waarin alleen het gebruik van gelicentieerde software is toegestaan.
- Vermijden dat in uw omgeving ongewenste software wordt gebruikt.
- Vermijden dat oude en niet-ondersteunde apps worden uitgevoerd.
- Voorkomen dat bepaalde softwareprogramma's worden uitgevoerd die in uw organisatie niet zijn toegestaan.
- De IT-afdeling in staat stellen de toegang tot gevoelige gegevens te beheren via het gebruik van apps.

> [!NOTE]
> Voor niet-Azure- en Linux-machines worden adaptieve toepassingsbesturingselementen alleen in de auditmodus ondersteund.

## <a name="how-to-enable-adaptive-application-controls"></a>Hoe worden adaptieve toepassingsbesturingselementen ingeschakeld?

Met adaptieve toepassingsbesturingselementen u een reeks toepassingen definiëren die op geconfigureerde groepen machines mogen worden uitgevoerd. Deze functie is beschikbaar voor zowel Azure- als niet-Azure Windows (alle versies, klassieke of Azure Resource Manager) en Linux-machines. Gebruik de volgende stappen om lijsten met toepassingsprogramma's te configureren:

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

1. Selecteer het **tabblad Aanbevolen** voor een lijst met groepen met aanbevelingen voor toepassingsbesturingselementen:

   ![Aanbevolen](./media/security-center-adaptive-application/security-center-adaptive-application-fig3.png)

   De lijst bevat:

   - **Groepsnaam**: De naam van het abonnement en de groep
   - **VM's en computers**: Het aantal virtuele machines in de groep
   - **Staat**: de staat van de aanbevelingen
   - **Ernst**: het ernstsniveau van de aanbevelingen

2. Klik op een groep om de optie **Regels voor toepassingsbesturingselementen maken** te openen.

   [![Regels voor toepassingsbeheer](./media/security-center-adaptive-application/security-center-adaptive-application-fig4.png)](./media/security-center-adaptive-application/security-center-adaptive-application-fig4.png#lightbox)

3. Bekijk **in de selecte VM's**de lijst met aanbevolen VM's en schakel het selectievakje uit waarop u geen whitelistingbeleid voor toepassingen wilt toepassen. Vervolgens ziet u twee lijsten:

   - **Aanbevolen toepassingen:** een lijst met toepassingen die vaak voorkomen op de VM's binnen deze groep en die worden aanbevolen om te mogen worden uitgevoerd.
   - **Meer toepassingen**: een lijst met toepassingen die ofwel minder frequent zijn op de VM's binnen deze groep of die bekend staan als Exploitables (zie meer hieronder), en aanbevolen voor beoordeling.

4. Bekijk de toepassingen in elk van de lijsten en schakel uit wat u niet wilt toepassen. Elke lijst bevat:

   - **NAAM**: de certificaatgegevens of het volledige traject van een aanvraag
   - **BESTANDSTYPEN**: het bestandstype van de toepassing. Dit kan EXE, Script, MSI of een permutatie van deze typen zijn.
   - **EXPLOITABLE**: een waarschuwingspictogram geeft aan of een specifieke toepassing door een aanvaller kan worden gebruikt om een lijst met toepassingstoestaan te omzeilen. U wordt aanbevolen om deze toepassingen te controleren voordat ze worden goedgekeurd.
   - **GEBRUIKERS**: gebruikers die worden aanbevolen om een toepassing uit te mogen voeren

5. Selecteer **Maken** nadat u uw selecties hebt gemaakt. <br>
   Nadat u Maken hebt geselecteerd, maakt Azure Security Center automatisch de juiste regels bovenop de ingebouwde lijstoptieoplossing die beschikbaar is op Windows-servers (AppLocker).

> [!NOTE]
> - Security Center heeft minimaal twee weken aan gegevens nodig om een basislijn te maken en de unieke aanbevelingen per groep virtuele machines te vullen. Voor nieuwe klanten van de Security Center-standaardcategorie worden groepen virtuele machines in het begin weergegeven op het tabblad *Geen aanbeveling*.
> - Adaptieve toepassingsbesturingselementen in Security Center bieden geen ondersteuning voor virtuele machines waarvoor al een AppLocker-beleid is ingeschakeld door een groepsbeleidsobject (GPO) of een lokaal beveiligingsbeleid.
> -  Als best practice voor beveiliging probeert Security Center altijd een uitgeversregel te maken voor toepassingen die zijn geselecteerd om te worden toegestaan, en alleen als een toepassing geen uitgeversinformatie heeft (ook bekend als niet ondertekend), wordt een padregel gemaakt voor het volledige pad van de specifieke toepassing.
>   

### <a name="editing-and-monitoring-a-group-configured-with-application-control"></a>Een groep die met toepassingsbeheer is geconfigureerd, bewerken en bewaken

1. Als u een groep wilt bewerken en controleren die is geconfigureerd met een lijstbeleid voor toepassingstoestaan, gaat u terug naar de pagina **Adaptieve toepassingsbesturingselementen** en selecteert u **GECONFIGUREERD** onder **Groepen VM's:**

   ![Groepen](./media/security-center-adaptive-application/security-center-adaptive-application-fig5.png)

   De lijst bevat:

   - **Groepsnaam**: de naam van het abonnement en de groep
   - **VM's en computers**: het aantal virtuele machines in de groep
   - **Modus**: In de controlemodus worden pogingen om toepassingen uit te voeren die niet op de lijst toestaan staan, logboeken. Afdwingen staat niet toe dat toepassingen worden uitgevoerd, tenzij ze op de lijst met toegestane
   - **Waarschuwingen**: eventuele huidige schendingen

2. Klik op een groep om wijzigingen aan te brengen op de **beleidspagina Besturingselementbeleid voor toepassingen bewerken.**

   ![Beveiliging](./media/security-center-adaptive-application/security-center-adaptive-application-fig6.png)

3. Onder **Beveiligingsmodus** kunt u een keuze maken uit de volgende opties:

   - **Controle**: in deze modus worden de regels niet afgedwongen door de oplossing voor toepassingsbeheer en wordt alleen de activiteit op de beveiligde virtuele machines gecontroleerd. Dit wordt aanbevolen voor scenario's waarin u eerst het algehele gedrag wilt observeren voordat de uitvoering van een app op de doel-VM wordt geblokkeerd.
   - **Afdwingen**: in deze modus worden de regels wel afgedwongen door de oplossing voor toepassingsbeheer. Ook worden toepassingen die niet mogen worden uitgevoerd, geblokkeerd.

   > [!NOTE]
   > -  De beveiligingsmodus **afdwingen** is tot nader order uitgeschakeld.
   > - Zoals eerder vermeld, wordt een nieuw beleid voor toepassingsbeheer altijd standaard geconfigureerd in de modus *Controle*. 
   >

4. Voeg **onder Beleidsextensie**een toepassingspad toe dat u wilt toestaan. Nadat u deze paden hebt toegevoegd, werkt Beveiligingscentrum het lijstbeleid voor de VM's binnen de geselecteerde groep VMS bij en maakt het de juiste regels voor deze toepassingen, naast de regels die al zijn ingevoerd.

5. Bekijk de huidige schendingen die worden vermeld in de sectie **Recente waarschuwingen.** Klik op elke regel die u wilt doorsturen naar de pagina **Waarschuwingen** in Azure Security Center en bekijk alle waarschuwingen die zijn gedetecteerd door Azure Security Center op de gekoppelde VM's.
   - **Waarschuwingen:** eventuele overtredingen die zijn geregistreerd.
   - **Nee. van VM's**: het aantal virtuele machines met dit waarschuwingstype.

6. Onder **Publisher whitelisting rules**, Path **whitelisting rules**, and Hash **whitelisting rules** you can see which application whitelisting rules are currently configured on the VM's within a group, according to the rule collection type. Voor elke regel u zien:

   - **Regel:** De specifieke parameters volgens welke een toepassing door AppLocker wordt onderzocht om te bepalen of een toepassing mag worden uitgevoerd.
   - **Bestandstype:** de bestandstypen die onder een specifieke regel vallen. Dit kan een van de volgende: EXE, Script, MSI of een permutatie van deze bestandstypen zijn.
   - **Gebruikers**: Naam of aantal gebruikers die een toepassing mogen uitvoeren die onder een whitelistingregel voor toepassingen valt.

   ![Regels voor opname in de whitelist](./media/security-center-adaptive-application/security-center-adaptive-application-fig9.png)

7. Klik op de drie puntjes aan het einde van elke regel als u de specifieke regel wilt verwijderen of de toegestane gebruikers wilt bewerken.

8. Nadat u wijzigingen hebt aangebracht in een beleid **voor adaptieve toepassingsbesturingselementen,** klikt u op **Opslaan**.

### <a name="not-recommended-list"></a>De lijst Niet aanbevolen

Security Center raadt alleen het whitelistingbeleid voor toepassingen aan voor virtuele machines met een stabiele set toepassingen. Opname in de whitelist wordt niet aanbevolen als de toepassingen op de bijbehorende virtuele machines voortdurend wisselen.

![Aanbeveling](./media/security-center-adaptive-application/security-center-adaptive-application-fig11.png)

De lijst bevat:
- **Groepsnaam**: de naam van het abonnement en de groep
- **VM's en computers**: het aantal virtuele machines in de groep

Azure Security Center stelt u in staat om ook een whitelistingbeleid voor toepassingen te definiëren voor niet-aanbevolen groepen VM's. Volg dezelfde principes als eerder beschreven, om ook een whitelistingbeleid voor toepassingen voor die groepen te configureren.

## <a name="move-a-vm-from-one-group-to-another"></a>Een virtuele machine van de ene groep naar de andere verplaatsen

 Wanneer u een virtuele machine van de ene groep naar de andere verplaatst, wordt het toepassingsbeheerbeleid dat daarop van toepassing is, gewijzigd in de instellingen van de groep waarnaar u deze hebt verplaatst. U een VM ook verplaatsen van een geconfigureerde groep naar een niet-geconfigureerde groep, wat resulteert in het verwijderen van een toepassingsbeheerbeleid dat eerder op de VM is toegepast.

 1. Klik op de pagina **Adaptieve toepassingsbesturingselementen** op het tabblad **GECONFIGUREERD** op de groep waartoe de VM momenteel moet worden verplaatst.
1. Klik **op Geconfigureerde VM's en computers**.
1. Klik op de drie puntjes in de regel van de virtuele machine om te verplaatsen en klik op **Verplaatsen**. De **computer verplaatsen naar het andere groepsvenster** wordt geopend.

    ![Beveiliging](./media/security-center-adaptive-application/adaptive-application-move-group.png)

 1. Selecteer de groep waarnaar u de virtuele machine wilt verplaatsen en klik op **Computer verplaatsen**en klik op **Opslaan**.

    ![Beveiliging](./media/security-center-adaptive-application/adaptive-application-move-group2.png)

 > [!NOTE]
> Klik op **Opslaan** nadat u **op Computer verplaatsen hebt geklikt**. Als u niet op **Opslaan**klikt, wordt de computer niet verplaatst.

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u adaptieve toepassingsbesturingselementen gebruiken in Azure Security Center om toepassingen die in Azure en niet-Azure VM's worden uitgevoerd, op de witte lijst te plaatsen. Zie de volgende onderwerpen voor meer informatie over Azure Security Center:

* [Beveiligingswaarschuwingen beheren en beantwoorden in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Informatie over het beheren van waarschuwingen en het reageren op beveiligingsincidenten in Security Center.
* [Bewaking van de beveiligingsstatus in Azure Security Center](security-center-monitoring.md). Meer informatie over het controleren van de status van uw Azure-resources.
* [Beveiligingswaarschuwingen in Azure Security Center begrijpen.](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) Meer informatie over de verschillende typen beveiligingswaarschuwingen.
* [Handleiding voor probleemoplossing azure security center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Informatie over het oplossen van veelvoorkomende problemen met Security Center.
* [Azure-beveiligingsblog](https://blogs.msdn.com/b/azuresecurity/). Raadpleeg de blogberichten over beveiliging en naleving in Azure.
