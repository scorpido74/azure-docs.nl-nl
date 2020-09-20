---
title: Uw Azure VMware-oplossings resource inschakelen
description: Meer informatie over het indienen van een ondersteunings aanvraag om uw Azure VMware-oplossings resource in te scha kelen. U kunt ook meer knoop punten aanvragen in uw bestaande privécloud van Azure VMware-oplossingen.
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 72a7aca97067ce4e9ed0e901e4016c82b3549eb1
ms.sourcegitcommit: 0fd1f3fe7817ad44d878d580ec167e1508051795
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/19/2020
ms.locfileid: "90817895"
---
# <a name="how-to-enable-azure-vmware-solution-resource"></a>Azure VMware Solution resource inschakelen
Meer informatie over het indienen van een ondersteunings aanvraag om uw Azure VMware-oplossings resource in te scha kelen. U kunt ook meer knoop punten aanvragen in uw bestaande privécloud van Azure VMware-oplossingen.

## <a name="eligibility-criteria"></a>Criteria om in aanmerking te komen

* U hebt een [Azure Enterprise Agreement (EA)](https://docs.microsoft.com/azure/cost-management-billing/manage/ea-portal-agreements) met micro soft nodig.
* U hebt een Azure-account in een Azure-abonnement nodig.


## <a name="enable-azure-vmware-solution-resource"></a>Resource voor Azure VMware-oplossing inschakelen
Voordat u uw Azure VMware-oplossings resource maakt, moet u een ondersteunings ticket indienen om uw knoop punten toe te wijzen. Zodra het ondersteunings team uw aanvraag heeft ontvangen, duurt het Maxi maal vijf werk dagen om uw aanvraag te bevestigen en uw knoop punten toe te wijzen. Als u een bestaande privécloud van Azure VMware-oplossing hebt en u meer knoop punten wilt toewijzen, gaat u door hetzelfde proces.


1. Maak in uw Azure Portal onder **Help en ondersteuning**een **[nieuwe ondersteunings aanvraag](https://rc.portal.azure.com/#create/Microsoft.Support)** en geef de volgende informatie op voor het ticket:
   - **Type probleem:** Documentatie
   - **Abonnement:** Uw abonnement selecteren
   - **Service:** Alle services > Azure VMware-oplossing
   - **Resource:** Algemene vraag 
   - **Samen vatting:** Capaciteit nodig
   - **Probleem type:** Problemen met capaciteits beheer
   - **Subtype van probleem:** Klant aanvraag voor extra quotum/capaciteit van host

1. Geef in de **Beschrijving** van het ondersteunings ticket op het tabblad **Details** het volgende op:

   - HAALBAARHEIDs-of productie 
   - Naam regio
   - Aantal knooppunten
   - Eventuele andere gegevens

   >[!NOTE]
   >De Azure VMware-oplossing raadt ten minste drie knoop punten aan om uw privécloud te maken en voor redundantie-N + 1-knoop punten. 

1. Selecteer **beoordeling + maken** om de aanvraag in te dienen.

   Het duurt Maxi maal vijf werk dagen voor een ondersteunings medewerker om uw aanvraag te bevestigen.

   >[!IMPORTANT] 
   >Als u al een bestaande Azure VMware-oplossing hebt en u extra knoop punten aanvraagt, moeten we vijf werk dagen hebben om de knoop punten toe te wijzen. 

1. Voordat u uw knoop punten kunt inrichten, moet u ervoor zorgen dat u de resource provider **micro soft. AVS** registreert in de Azure Portal.  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>
   ```

   Zie [Azure-resourceproviders en -typen](../azure-resource-manager/management/resource-providers-and-types.md) voor meer manieren om de Azure Synapse-resourceprovider te registeren.