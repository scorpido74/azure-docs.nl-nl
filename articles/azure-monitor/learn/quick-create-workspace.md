---
title: Een Logboekanalysewerkruimte maken in de Azure Portal | Microsoft Documenten
description: Meer informatie over het maken van een Log Analytics-werkruimte om beheeroplossingen en gegevensverzameling vanuit uw cloud- en on-premises omgevingen in de Azure-portal mogelijk te maken.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2019
ms.openlocfilehash: 3c2e9d5634916c3713b7e3380c0496611d8f60a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77656276"
---
# <a name="create-a-log-analytics-workspace-in-the-azure-portal"></a>Een Logboekanalysewerkruimte maken in de Azure-portal
Gebruik het menu **Logboekanalyse-werkruimten** om een Log Analytics-werkruimte te maken met behulp van de Azure-portal. Een Log Analytics-werkruimte is een unieke omgeving voor Azure Monitor-logboekgegevens. Elke werkruimte heeft zijn eigen gegevensopslagplaats en -configuratie en gegevensbronnen en -oplossingen zijn geconfigureerd om hun gegevens in een bepaalde werkruimte op te slaan. U hebt een Log Analytics-werkruimte nodig als u gegevens wilt verzamelen uit de volgende bronnen:

* Azure-bronnen in uw abonnement
* On-premises computers gecontroleerd door System Center Operations Manager
* Apparaatverzamelingen van Configuration Manager 
* Diagnostische gegevens of logboekgegevens uit Azure-opslag

Zie de volgende onderwerpen voor andere bronnen, zoals Azure VM's en Windows- of Linux-VM's in uw omgeving:

*  [Gegevens verzamelen van virtuele Azure-machines](../learn/quick-collect-azurevm.md) 
*  [Gegevens verzamelen van hybride Linux-computer](../learn/quick-collect-linux-computer.md)
*  [Gegevens verzamelen van hybride Windows-computer](quick-collect-windows-computer.md)

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="sign-in-to-azure-portal"></a>Meld u aan bij Azure Portal
Meld u aan bij [https://portal.azure.com](https://portal.azure.com)de Azure-portal op . 

## <a name="create-a-workspace"></a>Een werkruimte maken
1. Klik in de Azure-portal op **Alle services**. Typ in de lijst met resources **Log Analytics**. Als u begint te typen, wordt de lijst gefilterd op basis van uw invoer. Selecteer **Logboekanalysewerkruimten**.

    ![Azure Portal](media/quick-create-workspace/azure-portal-01.png)
  
2. Klik **op Toevoegen**en selecteer vervolgens de opties voor de volgende items:

   * Geef een naam op voor de nieuwe **Log Analytics-werkruimte**, bijvoorbeeld *StandaardLAWerkruimte*. Deze naam moet wereldwijd uniek zijn voor alle Azure Monitor-abonnementen.
   * Selecteer een **abonnement** om te koppelen door een selectie in de vervolgkeuzelijst te maken als de geselecteerde standaardwaarde niet juist is.
   * Kies voor **Resourcegroep**om een bestaande resourcegroep te gebruiken die al is ingesteld of een nieuwe groep maakt.  
   * Selecteer een beschikbare **locatie**.  Zie in welke [regio's Log Analytics beschikbaar is en](https://azure.microsoft.com/regions/services/) zoek naar Azure Monitor in het veld Zoeken naar een **product.**  
   * Als u een werkruimte maakt in een nieuw abonnement dat na 2 april 2018 is gemaakt, gebruikt deze automatisch de prijscategorie *Per GB*. De optie voor het selecteren van een prijscategorie is dan niet beschikbaar.  Als u een werkruimte maakt voor een bestaand abonnement dat vóór 2 april is gemaakt of voor een abonnement dat is gekoppeld aan een bestaande Inschrijving voor de Ondernemingsovereenkomst (EA), selecteert u de prijscategorie van uw voorkeur.  Zie [Prijsgegevens log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/)voor meer informatie over de specifieke lagen.

        ![Logboekanalysebronblad maken](media/quick-create-workspace/create-loganalytics-workspace-02.png)  

3. Nadat u de vereiste gegevens hebt opgegeven in het deelvenster **Log Analytics-werkruimte**, klikt u op **OK**.  

Terwijl de gegevens worden geverifieerd en de werkruimte wordt gemaakt, kunt u de voortgang bijhouden onder **Meldingen** in het menu. 

## <a name="next-steps"></a>Volgende stappen
Nu u een werkruimte beschikbaar hebt, u het verzamelen van bewakingstelemetrie configureren, logboekzoekopdrachten uitvoeren om die gegevens te analyseren en een beheeroplossing toevoegen om aanvullende gegevens en analytische inzichten te bieden. 

* Zie [Azure-servicelogboeken en -statistieken verzamelen voor gebruik in Logboekanalyse](../platform/collect-azure-metrics-logs.md)voor het maken van gegevens uit Azure-bronnen met Azure Diagnostics of Azure-opslag.  
* [Voeg System Center Operations Manager toe als gegevensbron](../platform/om-agents.md) om gegevens te verzamelen van agents die uw beheergroep Operations Manager rapporteren en deze op te slaan in uw Log Analytics-werkruimte. 
* Sluit [Configuratiebeheer](../platform/collect-sccm.md) aan bij het importeren van computers die lid zijn van verzamelingen in de hiërarchie.  
* Bekijk de beschikbare [bewakingsoplossingen](../insights/solutions.md) en hoe u een oplossing uit uw werkruimte toevoegen of verwijderen.
