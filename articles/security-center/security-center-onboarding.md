---
title: Verbeterde beveiliging standaardlaag - Azure Security Center
description: " Meer informatie over het gebruik van Azure Security Center Standard voor verbeterde beveiliging. "
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
ms.date: 02/19/2019
ms.author: memildin
ms.openlocfilehash: be26a9d4c66412518079de303ac0764d979c3e7c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77912049"
---
# <a name="onboarding-to-azure-security-center-standard-for-enhanced-security"></a>Onboarding to Azure Security Center Standard for enhanced security (Onboarding voor Azure Security Center Standard voor meer veiligheid)
Upgrade naar Security Center Standard om te profiteren van verbeterd beveiligingsbeheer en bedreigingsbeveiliging voor uw hybride cloudworkloads. Je Standaard gratis proberen. Zie de [prijspagina](https://azure.microsoft.com/pricing/details/security-center/) van het Beveiligingscentrum voor meer informatie.

Standaardlaag beveiligingscentrum omvat:

- **Hybride beveiliging** : krijg een uniform overzicht van de beveiliging voor al uw on-premises en cloudworkloads. Pas beveiligingsbeleid toe en beoordeel continu de beveiliging van uw hybride cloudworkloads om ervoor te zorgen dat aan beveiligingsstandaarden wordt voldaan. Verzamel, zoek en analyseer beveiligingsgegevens uit verschillende bronnen, waaronder firewalls en andere partneroplossingen.
- **Beveiligingswaarschuwingen** - Gebruik geavanceerde analyses en de Microsoft Intelligent Security Graph om een voorsprong te krijgen op evoluerende cyberaanvallen. Maak gebruik van ingebouwde gedragsanalyses en machine learning om aanvallen en zero-day exploits te identificeren. Monitor netwerken, machines en cloudservices voor inkomende aanvallen en activiteiten na inbreuk. Stroomlijn het onderzoek met interactieve tools en contextuele dreigingsinformatie.
- **Toegangs- en toepassingsbesturingselementen** - Blokkeer malware en andere ongewenste toepassingen door whitelisting-aanbevelingen toe te passen die zijn aangepast aan uw specifieke workloads en worden aangedreven door machine learning. Verminder het netwerkaanvalsoppervlak met just-in-time, gecontroleerde toegang tot beheerpoorten op Azure VM's, waardoor de blootstelling aan brute force en andere netwerkaanvallen drastisch wordt verminderd.

## <a name="detecting-unprotected-resources"></a>Onbeschermde bronnen detecteren
Security Center detecteert automatisch alle Azure-abonnementen of werkruimten die niet zijn ingeschakeld voor Security Center Standard. Hiertoe behoren Azure-abonnementen die Security Center Free gebruiken en werkruimten waarvoor de Security-oplossing niet is ingeschakeld.

U een volledig Azure-abonnement upgraden naar de standaardlaag, die wordt overgenomen door alle ondersteunde bronnen binnen het abonnement. Het toepassen van de standaardlaag op een werkruimte is van toepassing op alle bronnen die naar de werkruimte worden verzonden.

> [!NOTE]
> Misschien wilt u uw kosten beheren en de hoeveelheid gegevens die voor een oplossing wordt verzameld beperken door deze te beperken tot een bepaalde set agents. [Met oplossingstargeting](../operations-management-suite/operations-management-suite-solution-targeting.md) u een bereik toepassen op de oplossing en een subset van computers in de werkruimte targeten. Als u oplossingstargeting gebruikt, geeft Security Center aan dat de werkruimte geen oplossing heeft.
>
>

## <a name="upgrade-an-azure-subscription-or-workspace"></a>Een Azure-abonnement of -werkruimte bijwerken
Een abonnement of werkruimte upgraden naar standaard:
1. Selecteer in het hoofdmenu van Security Center de optie **Aan de slag**.
  ![Aan de slag](./media/security-center-onboarding/get-started.png)
2. Onder **Upgrade** vermeldt Security Center abonnementen en werkruimten die in aanmerking komen voor onboarding. 
   - U kunt op het uitvouwbare **Uw proefversie toepassen** klikken om een lijst met alle abonnementen en werkruimten te bekijken met de status voor een proefversie.
   -    U kunt abonnementen en werkruimten upgraden die niet in aanmerking komen voor een proefversie.
   -    U kunt in aanmerking komende werkruimten en abonnementen selecteren om uw proefversie te beginnen.
3.  Klik op **Proefversie beginnen** om uw proefversie van de geselecteerde abonnementen te beginnen.
  ![Abonnement selecteren](./media/security-center-onboarding/select-subscription.png)


   > [!NOTE]
   > De gratis mogelijkheden van Security Center worden alleen toegepast op uw Azure VM's en VMSS. De gratis mogelijkheden worden niet toegepast op uw niet-Azure-computers. Als u Standaard selecteert, worden de standaardmogelijkheden toegepast op alle Azure VM's, VM-schaalsets en niet-Azure-computers die naar de werkruimte worden verzonden. We raden u aan Standard toe te passen om geavanceerde beveiliging te bieden voor uw Azure- en niet-Azure-bronnen.
   >

## <a name="onboard-non-azure-computers"></a>Niet-Azure-computers aan boord
Security Center kan de beveiligingsstatus van uw niet-Azure-computers controleren, maar u moet deze resources dan eerst onboarden. U niet-Azure-computers toevoegen via het blade Aan de **slag** of vanaf het **Compute-blad.** We zullen beide methoden doorlopen.

### <a name="add-new-non-azure-computers-from-getting-started"></a>Nieuwe niet-Azure-computers toevoegen **vanaf aan de slag**

1. Terug naar **Aan de slag**.
2. Selecteer het tabblad **Aan de slag**.

   ![Niet-Azure](./media/security-center-onboarding/non-azure.png)

3. Klik op **Configureren** onder **Nieuwe niet-Azure-computers toevoegen**. Een lijst met uw Log Analytics-werkruimten wordt weergegeven. De lijst bevat, indien van toepassing, de standaardwerkruimte die is gemaakt door Security Center toen automatisch inrichten werd ingeschakeld. Selecteer deze werkruimte of een andere werkruimte die u wilt gebruiken.

   ![Niet-Azure-computer toevoegen][7]

Als u bestaande werkruimten hebt, worden deze weergegeven onder **Nieuwe niet-Azure-computers toevoegen**. U computers toevoegen aan een bestaande werkruimte of een nieuwe werkruimte maken. Als u een nieuwe werkruimte wilt maken, selecteert u de koppeling **een nieuwe werkruimte toevoegen**.

### <a name="add-new-non-azure-computers-from-compute"></a>Nieuwe niet-Azure-computers toevoegen vanuit **Compute**

**Een nieuwe werkruimte maken en een computer toevoegen**

1. Selecteer **onder Nieuwe niet-Azure-computers toevoegen**de optie Een nieuwe werkruimte **toevoegen**.

   ![Een nieuwe werkruimte toevoegen][4]

2. Selecteer **ONDER Beveiliging en Controle**de optie **OMS Workspace** om een nieuwe werkruimte te maken.
   > [!NOTE]
   > OMS-werkruimten worden nu aangeduid als Log Analytics-werkruimten.
3. Voer **onder OMS Workspace**de gegevens voor uw werkruimte in.
4. **Selecteer**OK onder **OMS Workspace**. Nadat u OK hebt geselecteerd, krijgt u een koppeling voor het downloaden van een Windows- of Linux-agent en sleutels voor uw werkruimte-id die u gebruiken bij het configureren van de agent.
5. Selecteer **OK**onder **Beveiliging en controle**.

**Een bestaande werkruimte selecteren en een computer toevoegen**

U een computer toevoegen door de werkstroom van **Onboarding**te volgen, zoals hierboven wordt weergegeven. U ook een computer toevoegen door de werkstroom van Compute te **volgen.** In dit voorbeeld gebruiken we **Compute**.

1. Ga terug naar het hoofdmenu en **het overzichtsdashboard van** Security Center.

   ![Overzicht][5]

2. Selecteer **&-apps berekenen**.
3. Selecteer **onder &-apps**berekenen de optie **Computers toevoegen**.

   ![Blade Compute][6]

4. Selecteer **onder Nieuwe niet-Azure-computers toevoegen**een werkruimte waarmee u uw computer wilt verbinden en klik op Computers **toevoegen**.

   ![Computers toevoegen][7]

   Het **Direct Agent-blad** biedt een koppeling voor het downloaden van een Windows- of Linux-agent, evenals de werkruimte-id en sleutels die u gebruiken bij het configureren van de agent.

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u geleerd hoe u Azure- en niet-Azure-resources inschepen om te profiteren van de geavanceerde beveiliging van security center. Zie voor meer doen met uw resources aan boord

- [Gegevensverzameling inschakelen](security-center-enable-data-collection.md)
- [Bedreigingsinformatierapport](security-center-threat-report.md)
- [Just-in-time-toegang voor virtuele machines](security-center-just-in-time.md)

<!--Image references-->
[1]: ./media/security-center-onboarding/onboard.png
[2]: ./media/security-center-onboarding/onboard-subscription.png
[3]: ./media/security-center-onboarding/get-started.png
[4]: ./media/security-center-onboarding/create-workspace.png
[5]: ./media/security-center-onboarding/overview.png
[6]: ./media/security-center-onboarding/compute-blade.png
[7]: ./media/security-center-onboarding/add-computer.png
[8]: ./media/security-center-onboarding/onboard-workspace.png
