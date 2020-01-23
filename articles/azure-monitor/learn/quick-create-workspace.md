---
title: Een Log Analytics-werkruimte maken in Azure Portal | Microsoft Docs
description: Informatie over het maken van een Log Analytics-werkruimte om in te schakelen management-oplossingen en -gegevens verzamelen van uw cloud en on-premises omgevingen in Azure portal.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2019
ms.openlocfilehash: bc8162a157cee10deeb8bf3f1e62a53fbdd30d0e
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513436"
---
# <a name="create-a-log-analytics-workspace-in-the-azure-portal"></a>Een Log Analytics-werkruimte maken in Azure portal
Gebruik het menu **log Analytics werk ruimten** om een log Analytics-werk ruimte te maken met behulp van de Azure Portal. Een Log Analytics-werk ruimte is een unieke omgeving voor Azure Monitor logboek gegevens. Elke werk ruimte heeft een eigen gegevens opslagplaats en-configuratie, en gegevens bronnen en-oplossingen zijn geconfigureerd om hun gegevens op te slaan in een bepaalde werk ruimte. U hebt een Log Analytics-werk ruimte nodig als u van plan bent om gegevens te verzamelen uit de volgende bronnen:

* Azure-resources in uw abonnement
* On-premises computers bewaakt door System Center Operations Manager
* Verzamelingen van apparaten uit Configuration Manager 
* Diagnostische gegevens of logboek gegevens uit Azure storage

Zie de volgende onderwerpen voor andere bronnen, zoals Azure-VM's en Windows of Linux-VM's in uw omgeving:

*  [Verzamelen van gegevens van virtuele machines van Azure](../learn/quick-collect-azurevm.md) 
*  [Gegevens verzamelen van hybride Linux-computer](../learn/quick-collect-linux-computer.md)
*  [Gegevens verzamelen van hybride Windows-computer](quick-collect-windows-computer.md)

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="sign-in-to-azure-portal"></a>Aanmelden bij Azure Portal
Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Een werkruimte maken
1. Klik in Azure Portal op **Alle services**. Typ in de lijst met resources **Log Analytics**. Als u begint te typen, wordt de lijst gefilterd op basis van uw invoer. Selecteer **log Analytics-werk ruimten**.

    ![Azure Portal](media/quick-create-workspace/azure-portal-01.png)
  
2. Klik op **toevoegen**en selecteer vervolgens opties voor de volgende items:

   * Geef een naam op voor de nieuwe **Log Analytics-werkruimte**, bijvoorbeeld *StandaardLAWerkruimte*. Deze naam moet globaal uniek zijn in alle Azure Monitor-abonnementen.
   * Selecteer een **abonnement** om te koppelen door een selectie in de vervolgkeuzelijst te maken als de geselecteerde standaardwaarde niet juist is.
   * Voor **resourcegroep**, kiest u een bestaande resource gebruiken groep al setup of maak een nieuwe.  
   * Selecteer een beschikbare **locatie**.  Zie voor meer informatie de [regio's log Analytics beschikbaar in](https://azure.microsoft.com/regions/services/) en zoek naar Azure monitor in het veld **zoeken naar een product** .  
   * Als u een werkruimte maakt in een nieuw abonnement dat na 2 april 2018 is gemaakt, gebruikt deze automatisch de prijscategorie *Per GB*. De optie voor het selecteren van een prijscategorie is dan niet beschikbaar.  Als u een werkruimte voor een bestaand abonnement gemaakt vóór 2 April, of voor een abonnement dat is gekoppeld aan een bestaande Enterprise Agreement (EA)-inschrijving maakt, selecteert u uw gewenste prijscategorie.  Zie voor meer informatie over de afzonderlijke lagen [prijsinformatie voor Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).

        ![Log Analytics resource-Blade maken](media/quick-create-workspace/create-loganalytics-workspace-02.png)  

3. Nadat u de vereiste gegevens hebt opgegeven in het deelvenster **Log Analytics-werkruimte**, klikt u op **OK**.  

Terwijl de gegevens worden geverifieerd en de werkruimte wordt gemaakt, kunt u de voortgang bijhouden onder **Meldingen** in het menu. 

## <a name="next-steps"></a>Volgende stappen
Nu dat u een werkruimte beschikbaar hebt, kunt u verzamelen van telemetrie bewaking configureren, uitvoeren van zoekopdrachten in Logboeken om die gegevens te analyseren en toevoegen van een oplossing om aanvullende gegevens en analytische informatie te bieden. 

* Zie voor het inschakelen van verzamelen van gegevens uit Azure-resources met Azure Diagnostics of Azure storage [Azure verzamelen van Logboeken en metrische gegevens voor gebruik in Log Analytics](../platform/collect-azure-metrics-logs.md).  
* [System Center Operations Manager als een gegevensbron toevoegen](../platform/om-agents.md) voor het verzamelen van gegevens van agents die rapporteren van uw Operations Manager-beheergroep en sla deze op in uw Log Analytics-werkruimte. 
* Verbinding maken met [Configuration Manager](../platform/collect-sccm.md) voor het importeren van computers die lid van verzamelingen in de hiërarchie zijn.  
* Bekijk de beschik bare [bewakings oplossingen](../insights/solutions.md) en hoe u een oplossing kunt toevoegen aan of verwijderen uit uw werk ruimte.
