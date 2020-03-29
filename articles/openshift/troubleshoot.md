---
title: Azure Red Hat OpenShift oplossen
description: Veelvoorkomende problemen met Azure Red Hat OpenShift oplossen en oplossen
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: troubleshooting
ms.date: 05/08/2019
ms.openlocfilehash: ee032cdf4a3f72b2cd2e7da0658effe75b6fb1fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76274932"
---
# <a name="troubleshooting-for-azure-red-hat-openshift"></a>Problemen oplossen voor Azure Red Hat OpenShift

In dit artikel worden enkele veelvoorkomende problemen beschreven die zijn opgetreden bij het maken of beheren van Microsoft Azure Red Hat OpenShift-clusters.

## <a name="retrying-the-creation-of-a-failed-cluster"></a>Het maken van een mislukt cluster opnieuw proberen

Als het maken van een Azure `az` Red Hat OpenShift-cluster met de opdracht CLI mislukt, blijft het opnieuw proberen van de maken mislukken.
Hiermee `az openshift delete` u het mislukte cluster verwijderen en vervolgens een geheel nieuw cluster maken.

## <a name="hidden-azure-red-hat-openshift-cluster-resource-group"></a>Verborgen Azure Red Hat OpenShift-clusterbrongroep

Momenteel is `Microsoft.ContainerService/openShiftManagedClusters` de bron die automatisch wordt gemaakt`az openshift create` door de Azure CLI (opdracht) verborgen in de Azure-portal. Schakel in de **groep Resource** de optie Verborgen **typen weergeven** om de resourcegroep weer te geven.

![Schermafbeelding van het selectievakje Verborgen tekst in de portal](./media/aro-portal-hidden-type.png)

## <a name="creating-a-cluster-results-in-error-that-no-registered-resource-provider-found"></a>Het maken van een cluster resulteert in een fout die geen geregistreerde resourceprovider heeft gevonden

Als het maken van een `No registered resource provider found for location '<location>' and API version '2019-04-30' for type 'openShiftManagedClusters'. The supported api-versions are '2018-09-30-preview`cluster resulteert in een fout die , dan bent u onderdeel van de preview en nu nodig hebt om [Azure virtuele machine gereserveerde exemplaren](https://aka.ms/openshift/buy) te kopen om het algemeen beschikbare product te gebruiken. Een reservering vermindert uw uitgaven door vooraf te betalen voor volledig beheerde Azure-services. Raadpleeg [*Wat zijn Azure-reserveringen*](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations) voor meer informatie over reserveringen en hoe ze u geld besparen.

## <a name="next-steps"></a>Volgende stappen

- Probeer het [Red Hat OpenShift Help Center](https://help.openshift.com/) voor meer informatie over OpenShift-probleemoplossing.

- Vind antwoorden [op veelgestelde vragen over Azure Red Hat OpenShift](openshift-faq.md).
