---
title: Controle op bestandsintegriteit in Azure Security Center | Microsoft Documenten
description: Meer informatie over het configureren van Fim (File Integrity Monitoring) in Azure Security Center met deze walkthrough.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/13/2019
ms.author: memildin
ms.openlocfilehash: 4d65ca8d97e1cca81886259d4f15cc880e45be9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77604286"
---
# <a name="file-integrity-monitoring-in-azure-security-center"></a>Bestandsintegriteit bewaken in Azure Security Center
Meer informatie over het configureren van Fim (File Integrity Monitoring) in Azure Security Center met deze walkthrough.

## <a name="what-is-fim-in-security-center"></a>Wat is FIM in Security Center?
File Integrity Monitoring (FIM), ook wel bekend als change monitoring, onderzoekt bestanden en registers van het besturingssysteem, applicatiesoftware en anderen op wijzigingen die kunnen duiden op een aanval. Er wordt een vergelijkingsmethode gebruikt om te bepalen of de huidige status van het bestand verschilt van de laatste scan van het bestand. U deze vergelijking gebruiken om te bepalen of er geldige of verdachte wijzigingen zijn aangebracht in uw bestanden.

Security Center's File Integrity Monitoring valideert de integriteit van Windows-bestanden, Windows-register en Linux-bestanden. U selecteert de bestanden die u wilt controleren door FIM in te schakelen. Security Center controleert bestanden met FIM ingeschakeld voor activiteiten zoals:

- Aanmaak en verwijdering van bestanden en register
- Bestandswijzigingen (wijzigingen in bestandsgrootte, toegangscontrolelijsten en hash van de inhoud)
- Registerwijzigingen (wijzigingen in grootte, toegangscontrolelijsten, type en inhoud)

Security Center raadt entiteiten aan om te controleren, waarop u FIM eenvoudig inschakelen. U ook uw eigen FIM-beleid of entiteiten definiëren om te controleren. Deze walkthrough laat je zien hoe.

> [!NOTE]
> De FIM-functie (File Integrity Monitoring) werkt voor Windows- en Linux-computers en VM's en is beschikbaar op de standaardlaag van beveiligingscentrum. Bekijk de pagina [Prijzen](security-center-pricing.md) voor meer informatie over de tariefopties van Security Center. FIM uploadt gegevens naar de werkruimte Log Analytics. Er zijn gegevenskosten van toepassing op basis van de hoeveelheid gegevens die u uploadt. Zie [Log Analytics-prijzen](https://azure.microsoft.com/pricing/details/log-analytics/) voor meer informatie.

FIM gebruikt de Azure Change Tracking-oplossing om wijzigingen in uw omgeving bij te houden en te identificeren. Wanneer File Integrity Monitoring is ingeschakeld, beschikt u over een **change tracking-bron** van het type **Solution**. Zie Details van het [verzamelen van gegevens wijzigen](https://docs.microsoft.com/azure/automation/automation-change-tracking#change-tracking-data-collection-details) voor Azure Change.

> [!NOTE]
> Als u de bron **Voor het bijhouden van wijzigingen** verwijdert, schakelt u ook de functie Bestandsintegriteitscontrole in Security Center uit.

## <a name="which-files-should-i-monitor"></a>Welke bestanden moet ik controleren?
U moet nadenken over de bestanden die essentieel zijn voor uw systeem en toepassingen bij het kiezen van welke bestanden te controleren. Overweeg om bestanden te kiezen waarvan u niet verwacht dat ze zonder planning worden gewijzigd. Het kiezen van bestanden die vaak worden gewijzigd door toepassingen of besturingssysteem (zoals logbestanden en tekstbestanden) maken veel ruis waardoor het moeilijk is om een aanval te identificeren.

Security Center raadt aan welke bestanden u standaard moet controleren volgens bekende aanvalspatronen die bestands- en registerwijzigingen bevatten.

## <a name="using-file-integrity-monitoring"></a>Controle op bestandsintegriteit gebruiken
1. Open het dashboard van **Security Center**.
2. Selecteer **Bestandsintegriteitscontrole**in het linkerdeelvenster onder **Geavanceerde cloudverdediging**.
![Dashboard van Security Center][1]

**File Integrity Monitoring** wordt geopend.
  ![Dashboard van Security Center][2]

Voor elke werkruimte wordt de volgende informatie verstrekt:

- Totaal aantal wijzigingen dat zich in de afgelopen week heeft voorgedaan (u ziet mogelijk een streepje "-" als FIM niet is ingeschakeld op de werkruimte)
- Totaal aantal computers en VM's dat rapporteert aan de werkruimte
- Geografische locatie van de werkruimte
- Azure-abonnement waaronder de werkruimte

De volgende knoppen kunnen ook worden weergegeven voor een werkruimte:

- ![Pictogram Inschakelen][3] Geeft aan dat FIM niet is ingeschakeld voor de werkruimte. Als u de werkruimte selecteert, u FIM inschakelen op alle machines onder de werkruimte.
- ![Pictogram Upgradeplan][4] Geeft aan dat de werkruimte of het abonnement niet wordt uitgevoerd onder de standaardlaag van het beveiligingscentrum. Als u de FIM-functie wilt gebruiken, moet uw abonnement standaard worden uitgevoerd.  Als u de werkruimte selecteert, u upgraden naar Standaard. Zie [Upgrade naar standaardlaag](security-center-pricing.md)voor verbeterde beveiliging voor meer informatie over de standaardlaag van de standaardklasse standard .
- Een lege (er is geen knop) betekent dat FIM al is ingeschakeld op de werkruimte.

Onder **Bestandsintegriteitscontrole**u een werkruimte selecteren om FIM voor die werkruimte in te schakelen, het dashboard voor bestandsintegriteitscontrole voor die werkruimte weergeven of de werkruimte [upgraden](security-center-pricing.md) naar Standaard.

## <a name="enable-fim"></a>FIM inschakelen
Fim inschakelen op een werkruimte:

1. Selecteer **onder Bestandsintegriteitscontrole**een werkruimte met de knop **Inschakelen.**
2. **Met de bewaking** van bestandsintegriteit wordt geopend en wordt het aantal Windows- en Linux-machines onder de werkruimte weergegeven.

   ![Controle op bestandsintegriteit inschakelen][5]

   De aanbevolen instellingen voor Windows en Linux worden ook vermeld.  Vouw **Windows-bestanden,** **Register-** en **Linux-bestanden** uit om de volledige lijst met aanbevolen items te bekijken.

3. Schakel de aanbevolen entiteiten waar u FIM niet op wilt toepassen uit.
4. Selecteer **Bestandsintegriteitscontrole toepassen** om FIM in te schakelen.

> [!NOTE]
> U de instellingen op elk gewenst moment wijzigen. Zie Bewaakte entiteiten bewerken hieronder voor meer informatie.
>
>

## <a name="view-the-fim-dashboard"></a>Het FIM-dashboard weergeven
Het dashboard **voor het bewaken van de bestandsintegriteit** wordt weergegeven voor werkruimten waar FIM is ingeschakeld. Het FIM-dashboard wordt geopend nadat u FIM ineenhebt ineenschakelt in een werkruimte of wanneer u een werkruimte selecteert in het venster **Bestandsintegriteitscontrole** waarin al FIM is ingeschakeld.

![Dashboard Bestandsintegriteitscontrole][6]

In het FIM-dashboard voor een werkruimte worden de volgende details weergegeven:

- Totaal aantal machines dat is aangesloten op de werkruimte
- Totaal aantal wijzigingen dat tijdens de geselecteerde periode is opgetreden
- Een uitsplitsing van het type wijziging (bestanden, register)
- Een uitsplitsing van de wijzigingscategorie (gewijzigd, toegevoegd, verwijderd)

Als u Filter boven aan het dashboard selecteert, u de periode toepassen waarvoor u wijzigingen wilt zien.

![Tijdsperiodefilter][7]

Op het tabblad **Computers** (hierboven weergegeven) worden alle machines weergegeven die naar deze werkruimte rapporteren. Voor elke machine wordt in het dashboard het als belangrijkste vermeld:

- Totaal aantal wijzigingen dat tijdens de geselecteerde periode is opgetreden
- Een uitsplitsing van de totale wijzigingen als bestandswijzigingen of registerwijzigingen

**Log Search** wordt geopend wanneer u een machinenaam invoert in het zoekveld of een machine selecteert die wordt weergegeven onder het tabblad Computers. Log Search geeft alle wijzigingen weer die tijdens de geselecteerde periode voor de machine zijn aangebracht. U een wijziging uitbreiden voor meer informatie.

![Zoeken in logboeken][8]

Op het tabblad **Wijzigingen** (zie hieronder) worden alle wijzigingen voor de werkruimte gedurende de geselecteerde periode weergegeven. Voor elke entiteit die is gewijzigd, wordt in het dashboard de sa.o.a.

- Computer waarop de wijziging heeft plaatsgevonden
- Type wijziging (register of bestand)
- Categorie van wijziging (gewijzigd, toegevoegd, verwijderd)
- Datum en tijd van wijziging

![Wijzigingen voor de werkruimte][9]

**Details wijzigen** wordt geopend wanneer u een wijziging invoert in het zoekveld of een entiteit selecteert die wordt vermeld onder het tabblad **Wijzigingen.**

![Details wijzigen][10]

## <a name="edit-monitored-entities"></a>Bewaakte entiteiten bewerken

1. Ga terug naar het **dashboard voor bestandsintegriteitscontrole** en selecteer **Instellingen**.

   ![Instellingen][11]

   **Werkruimteconfiguratie** wordt geopend met drie tabbladen: **Windows Registry,** **Windows Files**en Linux **Files**. Elk tabblad bevat de entiteiten die u in die categorie bewerken. Voor elke vermelde entiteit identificeert Security Center of FIM is ingeschakeld (true) of niet is ingeschakeld (false).  Met het bewerken van de entiteit u FIM in- of uitschakelen.

   ![Werkruimteconfiguratie][12]

2. Selecteer een identiteitsbescherming. In dit voorbeeld hebben we een item geselecteerd onder Windows Registry. **Bewerken voor het bijhouden van wijzigingen** wordt geopend.

   ![Tracking bewerken of wijzigen][13]

Onder **Bewerken voor bijhouden van wijzigingen** u:

- Controle op de integriteit van het bestand (True) inschakelen of uitschakelen (False)
- De naam van de entiteit opgeven of wijzigen
- De waarde of het pad opgeven of wijzigen
- De entiteit verwijderen, de wijziging verwijderen of de wijziging opslaan

## <a name="add-a-new-entity-to-monitor"></a>Een nieuwe entiteit toevoegen om te controleren
1. Ga terug naar het **dashboard voor het bewaken van de integriteit van bestand** en selecteer **Instellingen** bovenaan. **Werkruimteconfiguratie** wordt geopend.
2. Selecteer **onder Werkruimteconfiguratie**het tabblad voor het type entiteit dat u wilt toevoegen: Windows Registry, Windows Files of Linux-bestanden. In dit voorbeeld hebben we **Linux-bestanden**geselecteerd.

   ![Een nieuw item toevoegen om te controleren][14]

3. Selecteer **Toevoegen**. **Add for Change Tracking** opens.

   ![Opgevraagde gegevens invoeren][15]

4. Typ op de pagina **Toevoegen** de gevraagde informatie en selecteer **Opslaan**.

## <a name="disable-monitored-entities"></a>Bewaakte entiteiten uitschakelen
1. Ga terug naar het dashboard **Voor het bewaken van bestandsintegriteit.**
2. Selecteer een werkruimte waar FIM momenteel is ingeschakeld. Een werkruimte is ingeschakeld voor FIM als deze de knop Inschakelen of Upgradeplan mist.

   ![Een werkruimte selecteren waar FIM is ingeschakeld][16]

3. Selecteer Onder Bestandsintegriteitscontrole de optie **Instellingen**.

   ![Instellingen selecteren][17]

4. Selecteer **onder Werkruimteconfiguratie**een groep waarin **Ingeschakeld** is ingesteld op true.

   ![Werkruimteconfiguratie][18]

5. Stel **onder Venster Bewerken voor bijhouden wijzigen** instellen **Ingeschakeld** voor Onwaar.

   ![Ingeschakeld instellen op false][19]

6. Selecteer **Opslaan**.

## <a name="folder-and-path-monitoring-using-wildcards"></a>Map- en padbewaking met jokertekens

Gebruik wildcards om tracking in mappen te vereenvoudigen. De volgende regels zijn van toepassing wanneer u mapbewaking configureert met jokertekens:
-   Wildcards zijn vereist voor het bijhouden van meerdere bestanden.
-   Jokertekens kunnen alleen worden gebruikt in het laatste segment van een pad, zoals C:\folder\bestand of /etc/*.conf
-   Als een omgevingsvariabele een pad bevat dat niet geldig is, wordt de validatie uitgevoerd, maar mislukt het pad wanneer de voorraad wordt uitgevoerd.
-   Vermijd bij het instellen van het\*pad algemene paden zoals c: .* waardoor er te veel mappen worden doorsneden.

## <a name="disable-fim"></a>FIM uitschakelen
U FIM uitschakelen. FIM gebruikt de Azure Change Tracking-oplossing om wijzigingen in uw omgeving bij te houden en te identificeren. Door FIM uit te schakelen, verwijdert u de oplossing Voor het bijhouden van wijzigingen uit geselecteerde werkruimte.

1. Als u FIM wilt uitschakelen, gaat u terug naar het dashboard **Voor bestandsintegriteitscontrole.**
2. Selecteer een werkruimte.
3. Selecteer **Onder Bestandsintegriteitscontrole**de optie **Uitschakelen**.

   ![FIM uitschakelen][20]

4. Selecteer **Verwijderen** om uit te schakelen.

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u geleerd om File Integrity Monitoring (FIM) te gebruiken in Security Center. Zie de volgende pagina's voor meer informatie over Security Center:

* [Beveiligingsbeleid instellen:](tutorial-security-policy.md) meer informatie over het configureren van beveiligingsbeleid voor uw Azure-abonnementen en brongroepen.
* [Beveiligingsaanbevelingen beheren:](security-center-recommendations.md) lees hoe aanbevelingen u helpen uw Azure-bronnen te beschermen.
* [Monitoring van de beveiligingsstatus](security-center-monitoring.md)--Lees hoe u de status van uw Azure-bronnen controleren.
* [Beveiligingswaarschuwingen beheren en beantwoorden](security-center-managing-and-responding-alerts.md)--Meer informatie over het beheren en reageren op beveiligingswaarschuwingen.
* [Monitoring partneroplossingen](security-center-partner-solutions.md) - Ontdek hoe u de gezondheidsstatus van uw partneroplossingen controleren.
* [Azure Security Blog](https://blogs.msdn.com/b/azuresecurity/) (Azure-beveiligingsblog): hier vindt u het laatste nieuws over Azure-beveiliging en andere informatie.

<!--Image references-->
[1]: ./media/security-center-file-integrity-monitoring/security-center-dashboard.png
[2]: ./media/security-center-file-integrity-monitoring/file-integrity-monitoring.png
[3]: ./media/security-center-file-integrity-monitoring/enable.png
[4]: ./media/security-center-file-integrity-monitoring/upgrade-plan.png
[5]: ./media/security-center-file-integrity-monitoring/enable-fim.png
[6]: ./media/security-center-file-integrity-monitoring/fim-dashboard.png
[7]: ./media/security-center-file-integrity-monitoring/filter.png
[8]: ./media/security-center-file-integrity-monitoring/log-search.png
[9]: ./media/security-center-file-integrity-monitoring/changes-tab.png
[10]: ./media/security-center-file-integrity-monitoring/change-details.png
[11]: ./media/security-center-file-integrity-monitoring/fim-dashboard-settings.png
[12]: ./media/security-center-file-integrity-monitoring/workspace-config.png
[13]: ./media/security-center-file-integrity-monitoring/edit.png
[14]: ./media/security-center-file-integrity-monitoring/add.png
[15]: ./media/security-center-file-integrity-monitoring/add-item.png
[16]: ./media/security-center-file-integrity-monitoring/fim-dashboard-disable.png
[17]: ./media/security-center-file-integrity-monitoring/fim-dashboard-settings-disabled.png
[18]: ./media/security-center-file-integrity-monitoring/workspace-config-disable.png
[19]: ./media/security-center-file-integrity-monitoring/edit-disable.png
[20]: ./media/security-center-file-integrity-monitoring/disable-fim.png
