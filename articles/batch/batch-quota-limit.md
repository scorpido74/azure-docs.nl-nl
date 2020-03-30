---
title: Servicequota en -limieten - Azure Batch | Microsoft Documenten
description: Meer informatie over standaardazure batchquota, limieten en beperkingen en hoe u quotaverhogingen aanvragen
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 28998df4-8693-431d-b6ad-974c2f8db5fb
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/13/2019
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: c8a78acfa1f3e7332cee337ae1a82ee365fe356a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248227"
---
# <a name="batch-service-quotas-and-limits"></a>Quota en limieten voor Batch-service

Net als bij andere Azure-services zijn er beperkingen voor bepaalde resources die zijn gekoppeld aan de Batch-service. Veel van deze limieten zijn standaardquota die door Azure worden toegepast op abonnements- of accountniveau. In dit artikel worden deze standaardinstellingen besproken en hoe u quotaverhogingen aanvragen.

Houd rekening met deze quota bij het ontwerpen en opschalen van uw Batch-workloads. Als uw groep bijvoorbeeld het door u opgegeven doelaantal rekenknooppunten niet bereikt, hebt u mogelijk de limiet voor het kernquotum voor uw Batch-account bereikt.

U kunt in één Batch-account meerdere Batch-workloads uitvoeren of uw workloads verdelen over Batch-accounts in hetzelfde abonnement maar in verschillende Azure-regio's.

Als u van plan bent productieworkloads uit te voeren in Batch, moet u mogelijk een of meer van de quota boven de standaardinstelling verhogen. Als u een quotum wilt verhogen, u kosteloos een online [verzoek voor klantenondersteuning](#increase-a-quota) openen.

## <a name="resource-quotas"></a>Resourcequota

Een quotum is een kredietlimiet, geen capaciteitsgarantie. Als u op grote schaal capaciteitsbehoeften hebt, neemt u contact op met Azure-ondersteuning.

Houd er ook rekening mee dat quota geen gegarandeerde waarden zijn. Quota kunnen variëren op basis van wijzigingen van de Batch-service of een gebruikersverzoek om een quotumwaarde te wijzigen.

[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

### <a name="cores-quotas-in-user-subscription-mode"></a>Kernen quota in gebruikersabonnementsmodus

Als u een Batch-account hebt gemaakt met de toewijzingsmodus voor een groep ingesteld op **gebruikersabonnement,** worden quota anders toegepast. In deze modus worden batch-VM's en andere bronnen rechtstreeks in uw abonnement gemaakt wanneer een groep wordt gemaakt. De quota azure batch-cores zijn niet van toepassing op een account dat in deze modus is gemaakt. In plaats daarvan worden de quota in uw abonnement voor regionale rekenkernen en andere resources toegepast. Meer informatie over deze quota in [Azure-abonnements- en servicelimieten, quota's en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="pool-size-limits"></a>Limieten voor de grootte van de groep

Limieten voor de grootte van de groep worden ingesteld door de batchservice. In tegenstelling tot [resourcequota](#resource-quotas)kunnen deze waarden niet worden gewijzigd. Alleen groepen met internodecommunicatie en aangepaste afbeeldingen hebben beperkingen die verschillen van het standaardquotum.

| **Resource** | **Maximumaantal** |
| --- | --- |
| **Compute-knooppunten in [internodecommunicatie ingeschakeld](batch-mpi.md)**  ||
| Toewijzingsmodus batchservicegroep | 100 |
| Toewijzingsmodus voor batchabonnementen | 80 |
| **Gegevensknooppunten in [groep die zijn gemaakt met een beheerde afbeeldingsbron](batch-custom-images.md)**<sup>1</sup> ||
| Toegewezen knooppunten | 2000 |
| Knooppunten met lage prioriteit | 1000 |

<sup>1</sup> Voor zwembaden die geen internodecommunicatie zijn ingeschakeld.

## <a name="other-limits"></a>Andere limieten

Extra limieten die zijn ingesteld door de Batch-service. In tegenstelling tot [resourcequota](#resource-quotas)kunnen deze waarden niet worden gewijzigd.

| **Resource** | **Maximumaantal** |
| --- | --- |
| [Gelijktijdige taken](batch-parallel-node-tasks.md) per compute-knooppunt | 4 x aantal node-kernen |
| [Toepassingen](batch-application-packages.md) per batchaccount | 20 |
| Toepassingspakketten per toepassing | 40 |
| Toepassingspakketten per groep | 10 |
| Maximale taaklevensduur | 180 dagen<sup>1</sup> |
| [Mounts](virtual-file-mount.md) per compute node | 10 |

<sup>1</sup> De maximale levensduur van een taak, vanaf het moment dat deze aan de taak wordt toegevoegd tot wanneer deze is voltooid, is 180 dagen. Voltooide taken blijven zeven dagen duren; gegevens voor taken die niet binnen de maximale levensduur zijn voltooid, zijn niet toegankelijk.

## <a name="view-batch-quotas"></a>Batchquota weergeven

Uw Batch-accountquota weergeven in de [Azure-portal.][portal]

1. Selecteer **Batch-accounts** in de portal en selecteer vervolgens het Batch-account waarin u geïnteresseerd bent.
1. Selecteer **Quota** in het menu van het batchaccount.
1. De quota weergeven die momenteel worden toegepast op de batchrekening

    ![Batchaccountquota][account_quotas]

## <a name="increase-a-quota"></a>Een quotum verhogen

Volg deze stappen om een quotumverhoging aan te vragen voor uw Batch-account of uw abonnement via de [Azure-portal.][portal] Het type quotumverhoging is afhankelijk van de pooltoewijzingsmodus van uw Batch-account. Als u een quotumverhoging wilt aanvragen, moet u de VM-reeks opnemen waarvoor u het quotum wilt verhogen. Wanneer de quotumverhoging wordt toegepast, wordt deze toegepast op alle reeksen VM's.

### <a name="increase-cores-quota-in-batch"></a>Kernenquotum in batch verhogen 

1. Selecteer de **help + ondersteuningstegel** op het portaaldashboard of het vraagteken (**?**) in de rechterbovenhoek van de portal.
1. Selecteer **Basisbeginselen voor nieuwe** > **ondersteuningsaanvragen**.
1. In **de basis:**
   
    a. **Probleemservice en** > **abonnementslimieten (quota)**
   
    b. Selecteer uw abonnement.
   
    c. **Onderdeel van** > **quotatype**
      
    Selecteer **Volgende**.
    
1. In **Details**:
      
    a. Geef **in Details opgeven**de locatie, het quotumtype en batchaccount op.
    
    ![Verhoging batchquota][quota_increase]

    Quotatypen zijn:

    * **Per batchaccount**  
        Waarden die specifiek zijn voor één batchaccount, inclusief toegewezen en kernen met lage prioriteit, en het aantal taken en groepen.
        
    * **Per regio**  
        Waarden die van toepassing zijn op alle Batch-accounts in een regio en het aantal Batch-accounts per regio per abonnement bevatten.

    Quota met lage prioriteit zijn één waarde voor alle VM-reeksen. Als u beperkte SKU's nodig hebt, moet u kernen met lage prioriteit selecteren en de **VM-families** opnemen die u wilt aanvragen.

    b. Selecteer een **ernst** op basis van uw [bedrijfsimpact.][support_sev]

    Selecteer **Volgende**.

1. In **Contactgegevens**:
   
    a. Selecteer een **methode voor voorkeurscontactpersonen**.
   
    b. Controleer en voer de vereiste contactgegevens in.
   
    Selecteer **Maken** om het ondersteuningsverzoek in te dienen.

Zodra u uw ondersteuningsaanvraag hebt ingediend, neemt Azure-ondersteuning contact met u op. Quotaaanvragen kunnen binnen enkele minuten of maximaal twee werkdagen worden voltooid.

## <a name="related-quotas-for-vm-pools"></a>Gerelateerde quota voor VM-groepen

Batchgroepen in de virtuele machineconfiguratie die zijn geïmplementeerd in een virtueel Azure-netwerk, wijzen automatisch extra Azure-netwerkbronnen toe. De volgende resources zijn nodig voor elke 50 poolknooppunten in een virtueel netwerk:

* Eén [netwerkbeveiligingsgroep](../virtual-network/security-overview.md#network-security-groups)
* Eén [openbaar IP-adres](../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* Eén [load balancer](../load-balancer/load-balancer-overview.md)

Deze resources worden toegewezen in het abonnement dat het virtuele netwerk bevat dat wordt geleverd bij het maken van de batchgroep. De beperkingen die voor deze resources gelden, worden bepaald door de [resourcequota](../azure-resource-manager/management/azure-subscription-service-limits.md) van het abonnement. Als u grote poolimplementaties plant in een virtueel netwerk, controleert u de quota van het abonnement voor deze resources. Vraag indien nodig een verhoging van de Azure-portal aan door **Help + ondersteuning te**selecteren.


## <a name="related-topics"></a>Verwante onderwerpen
* [Een Azure Batch-account in de Azure Portal maken](batch-account-create-portal.md)
* [Overzicht van azure batch-functies](batch-api-basics.md)
* [Azure-abonnements- en servicelimieten, quota en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: https://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.png
[quota_increase]: ./media/batch-quota-limit/quota-increase.png
