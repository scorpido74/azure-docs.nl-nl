---
title: Beheerde identiteiten gebruiken voor resources in Azure Cloud Shell
description: Code verifiëren met MSI in Azure Cloud Shell
services: azure
author: maertendMSFT
ms.author: damaerte
tags: azure-resource-manager
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 04/14/2018
ms.openlocfilehash: a5d49a16324a5a97f4a0507f9abf47ea602ea072
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72328721"
---
# <a name="use-managed-identities-for-azure-resources-in-azure-cloud-shell"></a>Beheerde identiteiten gebruiken voor Azure-resources in Azure Cloud Shell

Azure Cloud Shell ondersteunt autorisatie met beheerde identiteiten voor Azure-resources. Gebruik dit om toegangstokens op te halen om veilig te communiceren met Azure-services.

## <a name="about-managed-identities-for-azure-resources"></a>Informatie over beheerde identiteiten voor Azure-resources
Een veelvoorkomende uitdaging bij het bouwen van cloudtoepassingen is het veilig beheren van de referenties die in uw code moeten staan om te authenticeren voor cloudservices. In Cloud Shell moet u mogelijk ophalen uit Key Vault verifiëren voor een referentie die een script mogelijk nodig heeft.

Beheerde identiteiten voor Azure-resources maken het oplossen van dit probleem eenvoudiger door Azure-services een automatisch beheerde identiteit te geven in Azure Active Directory (Azure AD). U kunt deze identiteit gebruiken voor verificatie bij alle services die ondersteuning bieden voor Azure AD-verificatie, inclusief Key Vault, zonder dat u referenties in uw code hoeft te hebben.

## <a name="acquire-access-token-in-cloud-shell"></a>Toegangstoken aanschaffen in Cloud Shell

Voer de volgende opdrachten uit om uw MSI-toegangstoken in te stellen als een omgevingsvariabele. `access_token`
```
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
```

## <a name="handling-token-expiration"></a>Afhandeling van tokenvervaldatum

Het lokale MSI-subsysteem caches tokens. Daarom u het zo vaak noemen als u wilt, en een oproep aan Azure AD resulteert alleen als:
- een cachemiss er t.a.m. optreedt door dat er geen token in de cache is
- het token is verlopen

Als u het token in de cache in uw code indecache, moet u bereid zijn scenario's te verwerken waarin de bron aangeeft dat het token is verlopen.

Ga naar de [MSI-pagina over het krullen van MSI-toegangstokens](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token#error-handling)om tokenfouten te verwerken.

## <a name="next-steps"></a>Volgende stappen
[Meer informatie over MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)  
[Toegangstokens verkrijgen van MSI VM's](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token)
