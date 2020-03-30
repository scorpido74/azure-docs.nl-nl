---
title: Veelgestelde vragen over implementatieproblemen voor Microsoft Azure Cloud Services| Microsoft Documenten
description: In dit artikel worden de veelgestelde vragen over implementatie voor Microsoft Azure Cloud Services weergegeven.
services: cloud-services
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 63a219078927e9001a8eb4085c722e7ec8d2fac9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75980633"
---
# <a name="deployment-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Implementatieproblemen voor Azure Cloud Services: veelgestelde vragen (veelgestelde vragen)

Dit artikel bevat veelgestelde vragen over implementatieproblemen voor [Microsoft Azure Cloud Services.](https://azure.microsoft.com/services/cloud-services) U ook de [pagina VM-grootte van Cloud Services](cloud-services-sizes-specs.md) raadplegen voor informatie over de grootte.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-does-deploying-a-cloud-service-to-the-staging-slot-sometimes-fail-with-a-resource-allocation-error-if-there-is-already-an-existing-deployment-in-the-production-slot"></a>Waarom mislukt het implementeren van een cloudservice naar de faseringssleuf soms met een fout in de toewijzing van resources als er al een bestaande implementatie in de productiesleuf is?
Als een cloudservice een implementatie heeft in een van beide sleufs, wordt de hele cloudservice vastgemaakt aan een specifiek cluster. Dit betekent dat als er al een implementatie bestaat in de productiesleuf, een nieuwe faseringsimplementatie alleen kan worden toegewezen in hetzelfde cluster als de productiesleuf.

Toewijzingsfouten treden op wanneer het cluster waarin uw cloudservice zich bevindt, niet voldoende fysieke rekenbronnen heeft om aan uw implementatieaanvraag te voldoen.

Zie Fout bij de [toewijzing van cloudservice: Oplossingen voor](cloud-services-allocation-failures.md#solutions)hulp bij het voorkomen van dergelijke toewijzingsfouten.

## <a name="why-does-scaling-up-or-scaling-out-a-cloud-service-deployment-sometimes-result-in-allocation-failure"></a>Waarom leidt het opschalen of uitschalen van een implementatie van een cloudservice soms tot toewijzingsfouten?
Wanneer een cloudservice wordt geïmplementeerd, wordt deze meestal vastgemaakt aan een specifiek cluster. Dit betekent dat het opschalen/uitschalen van een bestaande cloudservice nieuwe exemplaren in hetzelfde cluster moet toewijzen. Als de capaciteit van het cluster bijna is of de gewenste VM-grootte/-type niet beschikbaar is, kan de aanvraag mislukken.

Zie Fout bij de [toewijzing van cloudservice: Oplossingen voor](cloud-services-allocation-failures.md#solutions)hulp bij het voorkomen van dergelijke toewijzingsfouten.

## <a name="why-does-deploying-a-cloud-service-into-an-affinity-group-sometimes-result-in-allocation-failure"></a>Waarom leidt het implementeren van een cloudservice in een affiniteitsgroep soms tot toewijzingsfouten?
Een nieuwe implementatie naar een lege cloudservice kan door de fabric in een cluster in dat gebied worden toegewezen, tenzij de cloudservice is vastgemaakt aan een affiniteitsgroep. Implementaties naar dezelfde affiniteitsgroep worden geprobeerd op hetzelfde cluster. Als de capaciteit van het cluster bijna is, kan de aanvraag mislukken.

Zie Fout bij de [toewijzing van cloudservice: Oplossingen voor](cloud-services-allocation-failures.md#solutions)hulp bij het voorkomen van dergelijke toewijzingsfouten.

## <a name="why-does-changing-vm-size-or-adding-a-new-vm-to-an-existing-cloud-service-sometimes-result-in-allocation-failure"></a>Waarom leidt het wijzigen van vm-grootte of het toevoegen van een nieuwe vm aan een bestaande cloudservice soms tot toewijzingsfouten?
De clusters in een datacenter kunnen verschillende configuraties van machinetypen hebben (bijvoorbeeld Een serie, Av2-serie, D-serie, Dv2-serie, G-serie, H-serie, enz.). Maar niet alle clusters zouden noodzakelijkerwijs alle soorten VM's hebben. Als u bijvoorbeeld een VM met De D-reeksen probeert toe te voegen aan een cloudservice die al is geïmplementeerd in een cluster met alleen a-reeksen, wordt er een toewijzingsfout ervaren. Dit gebeurt ook als u vm-SKU-formaten probeert te wijzigen (bijvoorbeeld overschakelen van een A-serie naar een D-serie).

Zie Fout bij de [toewijzing van cloudservice: Oplossingen voor](cloud-services-allocation-failures.md#solutions)hulp bij het voorkomen van dergelijke toewijzingsfouten.

Zie [Microsoft Azure: Producten die beschikbaar zijn per regio](https://azure.microsoft.com/regions/services)voor controle op de beschikbare formaten in uw regio.

## <a name="why-does-deploying-a-cloud-service-sometime-fail-due-to-limitsquotasconstraints-on-my-subscription-or-service"></a>Waarom mislukt het implementeren van een cloudservice soms vanwege beperkingen/quota/beperkingen voor mijn abonnement of service?
De implementatie van een cloudservice kan mislukken als de resources die moeten worden toegewezen, het standaard- of maximumquotum overschrijden dat is toegestaan voor uw service op regio-/datacenterniveau. Zie [Limieten voor Cloud Services](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cloud-services-limits)voor meer informatie .

U ook het huidige gebruik/quotum voor uw abonnement bijhouden \<op de portal: Azure portal => Abonnementen => juiste abonnement> => Gebruik + quotum.

Informatie over resourcegebruik/verbruik kan ook worden opgehaald via de Azure Billing API's. Zie [Azure Resource Usage API (Preview)](../cost-management-billing/manage/usage-rate-card-overview.md#azure-resource-usage-api-preview).

## <a name="how-can-i-change-the-size-of-a-deployed-cloud-service-vm-without-redeploying-it"></a>Hoe kan ik de grootte van een geïmplementeerde cloudservice-VM wijzigen zonder deze opnieuw te implementeren?
U de VM-grootte van een geïmplementeerde cloudservice niet wijzigen zonder deze opnieuw te implementeren. De VM-grootte is ingebouwd in het CSDEF, dat alleen kan worden bijgewerkt met een redeploy.

Zie [Een cloudservice bijwerken](cloud-services-update-azure-service.md)voor meer informatie.

## <a name="why-am-i-not-able-to-deploy-cloud-services-through-service-management-apis-or-powershell-when-using-azure-resource-manager-storage-account"></a>Waarom kan ik cloudservices niet implementeren via Service Management API's of PowerShell wanneer ik azure resource beheeropslagaccount gebruik? 

Aangezien de Cloud Service een Classic-bron is die niet rechtstreeks compatibel is met het Azure Resource Manager-model, u deze niet koppelen aan de Azure Resource Manager Storage-accounts. Hier zijn enkele opties: 

- Implementeren via REST API.

    Wanneer u implementeert via Service Management REST API, u de beperking omzeilen door een SAS-URL op te geven voor de blob-opslag, die werkt met zowel Classic- als Azure Resource Manager Storage-account. Lees [hier](/previous-versions/azure/reference/ee460813(v=azure.100))meer over de eigenschap 'PackageUrl'.

- Implementeren via [Azure portal](https://portal.azure.com).

    Dit werkt vanuit de [Azure-portal](https://portal.azure.com) terwijl het gesprek via een proxy/shim gaat waarmee communicatie tussen Azure Resource Manager en Classic-bronnen mogelijk is. 

## <a name="why-does-azure-portal-require-me-to-provide-a-storage-account-for-deployment"></a>Waarom moet ik van Azure portal een opslagaccount voor implementatie verstrekken?

In de klassieke portal werd het pakket rechtstreeks geüpload naar de beheer-API-laag en vervolgens zou de API-laag het pakket tijdelijk op een intern opslagaccount plaatsen.  Dit proces veroorzaakt problemen met prestaties en schaalbaarheid omdat de API-laag niet is ontworpen als een service voor het uploaden van bestanden.  In de Azure-portal (Resource Manager deployment model) hebben we de tussentijdse stap van het eerst uploaden naar de API-laag omzeild, wat resulteert in snellere en betrouwbaardere implementaties.

Wat de kosten betreft, is het erg klein en u hetzelfde opslagaccount opnieuw gebruiken voor alle implementaties. U de [opslagkostencalculator](https://azure.microsoft.com/pricing/calculator/#storage1) gebruiken om de kosten te bepalen voor het uploaden van het servicepakket (CSPKG), de CSPKG te downloaden en vervolgens de CSPKG te verwijderen.
