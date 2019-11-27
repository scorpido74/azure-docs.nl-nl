---
title: Azure Red Hat open Shift cluster beheerdersrol | Microsoft Docs
description: Toewijzing en gebruik van de rol Azure Red Hat open Shift Cluster Administrator
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: 0cb875122c63be18f7c39cdfea7986d705ed434e
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74539278"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Azure Red Hat open Shift-klant beheerdersrol

U bent de Cluster beheerder van een Azure Red Hat open Shift-cluster. Uw account heeft verhoogde machtigingen en toegang tot alle door de gebruiker gemaakte projecten.

Als uw account is gekoppeld aan de autorisatie rol klant-beheerder-cluster, kan hiermee automatisch een project worden beheerd.

> [!Note] 
> De rol klant-beheerder-cluster cluster is niet hetzelfde als de cluster functie cluster beheerder.


U kunt bijvoorbeeld acties uitvoeren die zijn gekoppeld aan een set termen (`create`) om te worden uitgevoerd op een set resource namen (`templates`). Voer de volgende opdracht uit om de details van deze rollen en hun sets met werk woorden en resources weer te geven:

`$ oc get clusterroles customer-admin-cluster -o yaml`

De namen van woorden zijn niet noodzakelijkerwijs alle toewijzingen rechtstreeks aan `oc` opdrachten. Ze zijn in het algemeen gelijk aan de typen CLI-bewerkingen die u kunt uitvoeren. 

Een voor beeld: met de `list` werk woord betekent dat u een lijst met alle objecten van een resource naam (`oc get`) kunt weer geven. Het `get` werk woord betekent dat u de details van een specifiek object kunt weer geven als u de naam van de groep kent (`oc describe`).

## <a name="configure-the-customer-administrator-role"></a>De rol klant beheerder configureren

U kunt de cluster functie klant-beheerder-cluster alleen configureren tijdens het maken van een cluster door de vlag `--customer-admin-group-id`op te geven. Zie [Azure Active Directory-integratie voor Azure Red Hat open Shift voor](howto-aad-app-configuration.md)meer informatie over het configureren van Azure Active Directory en de groep Administrators.

## <a name="next-steps"></a>Volgende stappen

Configureer de rol klant-beheerder-cluster cluster:
> [!div class="nextstepaction"]
> [Azure Active Directory integratie voor Azure Red Hat open Shift](howto-aad-app-configuration.md)
