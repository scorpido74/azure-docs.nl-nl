---
title: Azure Red Hat open Shift cluster beheerdersrol | Microsoft Docs
description: Toewijzing en gebruik van de rol Azure Red Hat open Shift Cluster Administrator
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: 1c676b2671b73084a2b4ae8908acb83c23a59b7b
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936933"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Azure Red Hat open Shift-klant beheerdersrol

U bent de Cluster beheerder van een Azure Red Hat open Shift-cluster. Uw account heeft verhoogde machtigingen en toegang tot alle door de gebruiker gemaakte projecten.

Als uw account de rol OSA-Customer-admin Authorization heeft, kan een project automatisch worden beheerd.

> [!Note] 
> Het OSA-klant-admin-cluster is niet hetzelfde als de cluster rol cluster beheerder.


U kunt bijvoorbeeld acties uitvoeren die zijn gekoppeld aan een set termen (`create`) om te kunnen worden uitgevoerd op een set resource namen (`templates`). Voer de volgende opdracht uit om de details van deze rollen en hun sets met werk woorden en resources weer te geven:

`$ oc describe clusterrole/osa-customer-admin`

De namen van woorden zijn niet noodzakelijkerwijs alle toewijzingen rechtstreeks aan `oc`-opdrachten. Ze zijn in het algemeen gelijk aan de typen CLI-bewerkingen die u kunt uitvoeren. 

Als u bijvoorbeeld een `list`-werk woord instelt, kunt u een lijst met alle objecten van een resource naam weer geven (`oc get`). De `get` betekent dat u de details van een specifiek object kunt weer geven als u de naam weet (`oc describe`).

## <a name="configure-the-customer-administrator-role"></a>De rol klant beheerder configureren

U kunt de beheerdersrol van de klant alleen configureren tijdens het maken van het cluster door de vlag `--customer-admin-group-id` op te geven. Zie [Azure Active Directory-integratie voor Azure Red Hat open Shift voor](howto-aad-app-configuration.md)meer informatie over het configureren van Azure Active Directory en de groep Administrators.

## <a name="next-steps"></a>Volgende stappen

De rol OSA-klant-admin configureren:
> [!div class="nextstepaction"]
> [Azure Active Directory integratie voor Azure Red Hat open Shift](howto-aad-app-configuration.md)
