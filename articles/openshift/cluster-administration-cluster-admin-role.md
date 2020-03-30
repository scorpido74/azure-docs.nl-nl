---
title: Azure Red Hat OpenShift-clusterbeheerdersrol | Microsoft Documenten
description: Toewijzing en gebruik van de Azure Red Hat OpenShift-clusterbeheerderrol
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: ae9a421a165d6c8bda688819c5233ae5bb1a8562
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139093"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Rol klantbeheerder azure Red Hat OpenShift

U bent de clusterbeheerder van een Azure Red Hat OpenShift-cluster. Uw account heeft meer machtigingen en toegang tot alle door gebruikers gemaakte projecten.

Wanneer aan uw account de autorisatierol klantbeheer-cluster is gekoppeld, kan het automatisch een project beheren.

> [!Note] 
> De clusterrol klantbeheer-cluster is niet hetzelfde als de clusterrol clusterclusterclusterclustercluster cluster.

U bijvoorbeeld acties uitvoeren die zijn gekoppeld`create`aan een set werkwoorden`templates`( ) om te werken op een set resourcenamen ( ). Voer de volgende opdracht uit om de details van deze rollen en hun sets werkwoorden en bronnen weer te geven:

`$ oc get clusterroles customer-admin-cluster -o yaml`

De werkwoordnamen worden niet per `oc` se rechtstreeks naar opdrachten toegewezen. Ze komen meer in het algemeen overeen met de typen CLI-bewerkingen die u uitvoeren. 

Als u bijvoorbeeld `list` het werkwoord hebt, betekent dit dat u`oc get`een lijst met alle objecten van een resourcenaam weergeven ( ). Het `get` werkwoord betekent dat u de details van een`oc describe`specifiek object weergeven als u de naam ( ).

## <a name="configure-the-customer-administrator-role"></a>De rol klantbeheerder configureren

U de clusterrol klantbeheer-cluster alleen configureren tijdens `--customer-admin-group-id`het maken van het cluster door de vlag op te geven. Dit veld is momenteel niet configureerbaar in de Azure-portal. Zie [Azure Active Directory-integratie voor Azure Red Hat OpenShift voor](howto-aad-app-configuration.md)meer informatie over het configureren van Azure Active Directory en de groep Administrators.

## <a name="confirm-membership-in-the-customer-administrator-role"></a>Lidmaatschap bevestigen in de rol klantbeheerder

Als u uw lidmaatschap in de groep klantbeheerders `oc get nodes` wilt `oc projects`bevestigen, probeert u de opdrachten OpenShift CLI of . `oc get nodes`toont een lijst met knooppunten als u de rol klantbeheer-cluster hebt en een machtigingsfout als u alleen de rol klantbeheer-project hebt. `oc projects`toont alle projecten in het cluster in tegenstelling tot alleen de projecten waarin u werkt.

Als u rollen en machtigingen in uw cluster [`oc policy who-can <verb> <resource>`](https://docs.openshift.com/container-platform/3.11/admin_guide/manage_rbac.html#managing-role-bindings) verder wilt verkennen, u de opdracht gebruiken.

## <a name="next-steps"></a>Volgende stappen

De clusterrol klantbeheer-cluster configureren:
> [!div class="nextstepaction"]
> [Azure Active Directory-integratie voor Azure Red Hat OpenShift](howto-aad-app-configuration.md)
