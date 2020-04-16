---
title: Beheerde identiteiten in Azure HDInsight
description: Biedt een overzicht van de implementatie van beheerde identiteiten in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/15/2020
ms.openlocfilehash: 1081865a2e138af38ba171197719f08dedf6ffdb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408943"
---
# <a name="managed-identities-in-azure-hdinsight"></a>Beheerde identiteiten in Azure HDInsight

Een beheerde identiteit is een identiteit die is geregistreerd in Azure Active Directory (Azure AD) waarvan de referenties worden beheerd door Azure. Met beheerde identiteiten hoeft u serviceprincipals niet te registreren in Azure AD. Of inloggegevens zoals certificaten onderhouden.

Beheerde identiteiten worden in Azure HDInsight gebruikt om toegang te krijgen tot Azure AD-domeinservices of waar nodig toegang te krijgen tot bestanden in Azure Data Lake Storage Gen2.

Er zijn twee soorten beheerde identiteiten: door de gebruiker toegewezen en systeemtoegewezen. Azure HDInsight ondersteunt alleen door de gebruiker toegewezen beheerde identiteiten. HDInsight ondersteunt geen door het systeem toegewezen beheerde identiteiten. Een door de gebruiker toegewezen beheerde identiteit wordt gemaakt als een zelfstandige Azure-bron, die u vervolgens toewijzen aan een of meer Azure-service-exemplaren. Een beheerde identiteit met systeemtoegewezen wordt daarentegen gemaakt in Azure AD en vervolgens automatisch ingeschakeld op een bepaalde Azure-serviceinstantie. De levensduur van de door het systeem toegewezen beheerde identiteit is vervolgens gekoppeld aan de levensduur van de serviceinstantie waarop deze is ingeschakeld.

## <a name="hdinsight-managed-identity-implementation"></a>HDInsight managed identity implementation HDInsight managed identity implementation HDInsight managed identity implementation HDInsight

In Azure HDInsight worden beheerde identiteiten op elk knooppunt van het cluster ingericht. Deze identiteitscomponenten zijn echter alleen bruikbaar voor de HDInsight-service. Er is momenteel geen ondersteunde methode om toegangstokens te genereren met behulp van de beheerde identiteiten die zijn geïnstalleerd op HDInsight-clusterknooppunten. Voor sommige Azure-services worden beheerde identiteiten geïmplementeerd met een eindpunt dat u gebruiken om toegangstokens te verkrijgen. Gebruik de tokens voor interactie met andere Azure-services.

## <a name="create-a-managed-identity"></a>Een beheerde identiteit maken

Beheerde identiteiten kunnen worden gemaakt met een van de volgende methoden:

* [Azure Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* [Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)
* [Azure-CLI](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

De resterende stappen voor het configureren van de beheerde identiteit zijn afhankelijk van het scenario waarin deze wordt gebruikt.

## <a name="managed-identity-scenarios-in-azure-hdinsight"></a>Scenario's voor beheerde identiteit in Azure HDInsight

Beheerde identiteiten worden in Azure HDInsight in meerdere scenario's gebruikt. Zie de bijbehorende documenten voor gedetailleerde installatie- en configuratie-instructies:

* [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md#create-a-user-assigned-managed-identity)
* [Enterprise Security Package](domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-and-authorize-a-managed-identity)
* [Schijfversleuteling met behulp van door klant beheerde sleutel](disk-encryption.md)

## <a name="faq"></a>Veelgestelde vragen

### <a name="what-happens-if-i-delete-the-managed-identity-after-the-cluster-creation"></a>Wat gebeurt er als ik de beheerde identiteit na het maken van het cluster verwijder?

Uw cluster loopt problemen tegen wanneer de beheerde identiteit nodig is. Er is momenteel geen manier om een beheerde identiteit bij te werken of te wijzigen nadat het cluster is gemaakt. Dus onze aanbeveling is om ervoor te zorgen dat de beheerde identiteit niet wordt verwijderd tijdens de runtime van het cluster. U het cluster opnieuw maken en een nieuwe beheerde identiteit toewijzen.

## <a name="next-steps"></a>Volgende stappen

* [Wat zijn beheerde identiteiten voor Azure-resources?](../active-directory/managed-identities-azure-resources/overview.md)
