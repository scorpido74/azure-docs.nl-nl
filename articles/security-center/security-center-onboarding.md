---
title: Enhanced Security-Azure Security Center van de Standard-laag
description: " Meer informatie over het voorbereiden van Azure Security Center Standard voor verbeterde beveiliging. "
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
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/28/2020
ms.locfileid: "77912049"
---
# <a name="onboarding-to-azure-security-center-standard-for-enhanced-security"></a>Onboarding van Azure Security Center Standard voor verbeterde beveiliging
Voer een upgrade uit naar Security Center Standard om te profiteren van verbeterde beveiligings beheer en bedreigings beveiliging voor uw hybride Cloud werkbelastingen. U kunt standaard gratis proberen. Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/security-center/) voor Security Center voor meer informatie.

Security Center Standard-laag bevat:

- **Hybride beveiliging** : krijg een uniforme weer gave van beveiliging in al uw on-premises en Cloud werkbelastingen. Pas het beveiligings beleid toe en evalueer voortdurend de beveiliging van uw hybride Cloud werkbelastingen om te zorgen voor naleving van de beveiligings standaarden. Verzamel, zoek en analyseer beveiligings gegevens uit verschillende bronnen, inclusief firewalls en andere partner oplossingen.
- **Beveiligings waarschuwingen** : Gebruik geavanceerde analyses en de Microsoft intelligent Security Graph om een rand te krijgen over het ontwikkelen van Cyber aanvallen. Maak gebruik van ingebouwde gedrags analyses en machine learning om aanvallen te identificeren en gebruik te maken van een Zero-Day-aanval. Bewaak netwerken, machines en Cloud Services voor inkomende aanvallen en activiteit na inbreuk. Stroom lijn onderzoek met interactieve hulpprogram ma's en contextuele bedreigings informatie.
- **Toegangs-en toepassings besturings elementen** : blok keer malware en andere ongewenste toepassingen door White List aanbevelingen toe te passen die zijn afgestemd op uw specifieke werk belastingen en die worden aangedreven door machine learning. Verminder de kwets baarheid van het netwerk met Just-in-time, gecontroleerde toegang tot beheer poorten op Azure-Vm's, waardoor de bloot stelling aan brute kracht en andere netwerk aanvallen drastisch wordt verkleind.

## <a name="detecting-unprotected-resources"></a>Niet-beveiligde bronnen detecteren
Security Center detecteert automatisch alle Azure-abonnementen of werkruimten die niet zijn ingeschakeld voor Security Center Standard. Hiertoe behoren Azure-abonnementen die Security Center Free gebruiken en werkruimten waarvoor de Security-oplossing niet is ingeschakeld.

U kunt een volledig Azure-abonnement upgraden naar de Standard-laag, die wordt overgenomen door alle ondersteunde resources binnen het abonnement. Het Toep assen van de Standard-laag op een werk ruimte is van toepassing op alle resources die aan de werk ruimte rapporteren.

> [!NOTE]
> U kunt uw kosten beheren en de hoeveelheid gegevens die voor een oplossing wordt verzameld beperken door deze te beperken tot een bepaalde set agents. Met [oplossings doelen](../operations-management-suite/operations-management-suite-solution-targeting.md) kunt u een bereik Toep assen op de oplossing en een subset van computers in de werk ruimte richten. Als u oplossings doelen gebruikt, bevat Security Center de werk ruimte als geen oplossing.
>
>

## <a name="upgrade-an-azure-subscription-or-workspace"></a>Een Azure-abonnement of-werk ruimte bijwerken
Een abonnement of werk ruimte bijwerken naar Standard:
1. Selecteer in het hoofdmenu van Security Center de optie **Aan de slag**.
  ![Aan de slag](./media/security-center-onboarding/get-started.png)
2. Onder **Upgrade** vermeldt Security Center abonnementen en werkruimten die in aanmerking komen voor onboarding. 
   - U kunt op het uitvouwbare **Uw proefversie toepassen** klikken om een lijst met alle abonnementen en werkruimten te bekijken met de status voor een proefversie.
   -    U kunt abonnementen en werkruimten upgraden die niet in aanmerking komen voor een proefversie.
   -    U kunt in aanmerking komende werkruimten en abonnementen selecteren om uw proefversie te beginnen.
3.  Klik op **Proefversie beginnen** om uw proefversie van de geselecteerde abonnementen te beginnen.
  ![abonnement selecteren](./media/security-center-onboarding/select-subscription.png)


   > [!NOTE]
   > De gratis mogelijkheden van Security Center worden alleen toegepast op uw Azure-Vm's en VMSS. De gratis mogelijkheden worden niet toegepast op uw niet-Azure-computers. Als u standaard selecteert, worden de standaard mogelijkheden toegepast op alle Azure-Vm's, VM-schaal sets en niet-Azure-computers die rapporteren aan de werk ruimte. U wordt aangeraden standaard te gebruiken om geavanceerde beveiliging te bieden voor uw Azure-en niet-Azure-resources.
   >

## <a name="onboard-non-azure-computers"></a>Niet-Azure-computers onboarden
Security Center kan de beveiligingsstatus van uw niet-Azure-computers controleren, maar u moet deze resources dan eerst onboarden. U kunt niet-Azure-computers toevoegen vanaf de Blade **aan** de slag of op de Blade **Compute** . We gaan beide methoden door lopen.

### <a name="add-new-non-azure-computers-from-getting-started"></a>Nieuwe niet-Azure-computers toevoegen aan **aan** de slag

1. Ga terug naar aan de **slag**.
2. Selecteer het tabblad **Aan de slag**.

   ![Non-Azure](./media/security-center-onboarding/non-azure.png)

3. Klik op **Configureren** onder **Nieuwe niet-Azure-computers toevoegen**. Een lijst met uw Log Analytics-werkruimten wordt weergegeven. De lijst bevat, indien van toepassing, de standaardwerkruimte die is gemaakt door Security Center toen automatisch inrichten werd ingeschakeld. Selecteer deze werkruimte of een andere werkruimte die u wilt gebruiken.

   ![Niet-Azure-computer toevoegen][7]

Als u bestaande werk ruimten hebt, worden deze weer gegeven onder **nieuwe niet-Azure-computers toevoegen**. U kunt computers toevoegen aan een bestaande werk ruimte of een nieuwe werk ruimte maken. Als u een nieuwe werk ruimte wilt maken, selecteert u de koppeling **een nieuwe werk ruimte toevoegen**.

### <a name="add-new-non-azure-computers-from-compute"></a>Nieuwe niet-Azure-computers toevoegen vanuit **Compute**

**Een nieuwe werk ruimte maken en een computer toevoegen**

1. Onder **nieuwe niet-Azure-computers toevoegen**selecteert u **een nieuwe werk ruimte toevoegen**.

   ![Een nieuwe werk ruimte toevoegen][4]

2. Selecteer onder **beveiliging en audit**de **OMS-werk ruimte** om een nieuwe werk ruimte te maken.
   > [!NOTE]
   > OMS-werkruimten worden nu aangeduid als Log Analytics-werkruimten.
3. Voer onder **OMS-werk ruimte**de informatie in voor uw werk ruimte.
4. Selecteer **OK**onder **OMS-werk ruimte**. Nadat u OK hebt geselecteerd, krijgt u een koppeling voor het downloaden van een Windows-of Linux-agent en sleutels voor de werk ruimte-ID die u kunt gebruiken bij het configureren van de agent.
5. Selecteer **OK**onder **beveiliging en audit**.

**Een bestaande werk ruimte selecteren en een computer toevoegen**

U kunt een computer toevoegen door de werk **stroom te volgen**van de voor bereiding, zoals hierboven wordt weer gegeven. U kunt ook een computer toevoegen door de werk stroom te volgen van **Compute**. In dit voor beeld gebruiken we **Compute**.

1. Ga terug naar het hoofd menu en het **overzichts** dashboard van Security Center.

   ![Overzicht][5]

2. Selecteer **compute & apps**.
3. Selecteer **computers toevoegen**onder **Compute & apps**.

   ![Blade Compute][6]

4. Onder **nieuwe niet-Azure-computers toevoegen**selecteert u een werk ruimte om uw computer aan te sluiten en klikt u op **computers toevoegen**.

   ![Computers toevoegen][7]

   De Blade **direct agent** bevat een koppeling voor het downloaden van een Windows-of Linux-agent en de werk ruimte-ID en-sleutels die moeten worden gebruikt in de configuratie van de agent.

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u geleerd hoe u Azure-en niet-Azure-resources kunt vrijgeven om te profiteren van de geavanceerde beveiliging van Security Center. Als u meer wilt doen met uw onboardde bronnen, raadpleegt u

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
