---
title: Beheerde identiteiten in azure HDInsight
description: Hierin wordt een overzicht gegeven van de implementatie van beheerde identiteiten in azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/20/2019
ms.openlocfilehash: e7be8fbf5f6c2c59e93d48729785dd34bae5955e
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327376"
---
# <a name="managed-identities-in-azure-hdinsight"></a>Beheerde identiteiten in azure HDInsight

Een beheerde identiteit is een identiteit die is geregistreerd in Azure Active Directory (Azure AD) waarvan de referenties door Azure worden beheerd. Met beheerde identiteiten hoeft u geen service-principals in azure AD te registreren of referenties zoals certificaten te behouden.

Beheerde identiteiten kunnen worden gebruikt in azure HDInsight om uw clusters toegang te geven tot Azure AD Domain Services, toegang te krijgen tot Azure Key Vault of toegang te krijgen tot bestanden in Azure Data Lake Storage Gen2.

Er zijn twee soorten beheerde identiteiten: aan de gebruiker toegewezen en het systeem toegewezen. Azure HDInsight maakt gebruik van door de gebruiker toegewezen beheerde identiteiten. Een door de gebruiker toegewezen beheerde identiteit wordt gemaakt als een zelfstandige Azure-resource, die u vervolgens kunt toewijzen aan een of meer Azure-service-exemplaren. Daarentegen wordt een door het systeem toegewezen beheerde identiteit gemaakt in azure AD en vervolgens automatisch direct ingeschakeld op een bepaald exemplaar van de Azure-service. De levens duur van de door het systeem toegewezen beheerde identiteit wordt vervolgens gekoppeld aan de levens duur van het service-exemplaar waarop het is ingeschakeld.

## <a name="hdinsight-managed-identity-implementation"></a>Implementatie van door HDInsight beheerde identiteit

In azure HDInsight worden beheerde identiteiten ingericht op elk knoop punt van het cluster. Deze identiteits onderdelen kunnen echter alleen worden gebruikt door de HDInsight-service. Er is momenteel geen ondersteunde methode voor het genereren van toegangs tokens met behulp van de beheerde identiteiten die zijn geïnstalleerd op HDInsight-cluster knooppunten. Voor sommige Azure-Services worden beheerde identiteiten geïmplementeerd met een eind punt dat u kunt gebruiken voor het verkrijgen van toegangs tokens voor interactie met andere Azure-Services.

## <a name="create-a-managed-identity"></a>Een beheerde identiteit maken

Beheerde identiteiten kunnen worden gemaakt met een van de volgende methoden:

* [Azure Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* [Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)
* [Azure CLI](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

De resterende stappen voor het configureren van de beheerde identiteit zijn afhankelijk van het scenario waarin het wordt gebruikt.

## <a name="managed-identity-scenarios-in-azure-hdinsight"></a>Scenario's voor beheerde identiteiten in azure HDInsight

Beheerde identiteiten worden in meerdere scenario's gebruikt in azure HDInsight. Raadpleeg de gerelateerde documenten voor gedetailleerde installatie-en configuratie-instructies:

* [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md#create-a-user-assigned-managed-identity)
* [Enterprise Security Package](domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-and-authorize-a-managed-identity)
* [Kafka Bring Your Own Key (BYOK)](kafka/apache-kafka-byok.md#get-started-with-byok)

## <a name="next-steps"></a>Volgende stappen

* [Wat zijn beheerde identiteiten voor Azure-resources?](../active-directory/managed-identities-azure-resources/overview.md)
