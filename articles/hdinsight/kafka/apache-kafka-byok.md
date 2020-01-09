---
title: Uw eigen sleutel voor Apache Kafka in azure HDInsight nemen
description: In dit artikel wordt beschreven hoe u uw eigen sleutel gebruikt van Azure Key Vault om gegevens te versleutelen die zijn opgeslagen in Apache Kafka in azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: b4a6ef4a8559276ea1f74e133055a613ddcbcab4
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/26/2019
ms.locfileid: "75495160"
---
# <a name="bring-your-own-key-for-apache-kafka-on-azure-hdinsight"></a>Uw eigen sleutel voor Apache Kafka in azure HDInsight nemen

Azure HDInsight bevat Bring Your Own Key-ondersteuning (BYOK) voor Apache Kafka. Met deze mogelijkheid kunt u de sleutels die worden gebruikt voor het versleutelen van gegevens in rust krijgen en beheren.

Alle beheerde schijven in HDInsight worden beveiligd met Azure Storage-service versleuteling (SSE). Standaard worden de gegevens op deze schijven versleuteld met door micro soft beheerde sleutels. Als u BYOK inschakelt, geeft u de versleutelings sleutel voor HDInsight op om deze te gebruiken en te beheren met Azure Key Vault.

BYOK-versleuteling is een proces dat uit één stap wordt verwerkt tijdens het maken van het cluster zonder extra kosten. Het enige wat u hoeft te doen, is HDInsight registreren als een beheerde identiteit met Azure Key Vault en de versleutelings sleutel toevoegen wanneer u het cluster maakt.

Alle berichten naar het Kafka-cluster (met inbegrip van replica's die worden onderhouden door Kafka) worden versleuteld met een symmetrische gegevens versleutelings sleutel (DEK). De DEK wordt beschermd met behulp van de sleutel versleutelings sleutel (KEK) van uw sleutel kluis. De processen voor versleuteling en ontsleuteling worden volledig verwerkt door Azure HDInsight.

U kunt de Azure Portal of Azure CLI gebruiken om de sleutels in de sleutel kluis veilig te draaien. Wanneer een sleutel draait, begint het HDInsight Kafka-cluster binnen enkele minuten met de nieuwe sleutel. Schakel de beveiligings functies ' zacht verwijderen ' in om te beschermen tegen Ransomware-scenario's en onbedoeld verwijderen. Sleutel kluizen zonder deze beveiligings functie worden niet ondersteund.

## <a name="get-started-with-byok"></a>Aan de slag met BYOK

Als u een BYOK Kafka-cluster wilt maken, gaat u als volgt te werk:

1. Beheerde identiteiten maken voor Azure-resources
2. Azure Key Vault en sleutels instellen
3. HDInsight Kafka-cluster maken met BYOK ingeschakeld
4. De versleutelings sleutel draaien

## <a name="create-managed-identities-for-azure-resources"></a>Beheerde identiteiten maken voor Azure-resources

Als u zich wilt verifiëren bij Key Vault, maakt u een door de gebruiker toegewezen beheerde identiteit met behulp van de [Azure Portal](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md), [Azure PowerShell](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md), [Azure Resource Manager](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)of [Azure cli](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md). Zie [beheerde identiteiten in azure hdinsight](../hdinsight-managed-identities.md)voor meer informatie over de werking van beheerde identiteiten in azure hdinsight. Azure Active Directory is vereist voor beheerde identiteiten en BYOK naar Kafka, Enterprise Security Package (ESP) is geen vereiste. Zorg ervoor dat u de resource-ID van de beheerde ID opslaat wanneer u deze toevoegt aan het Key Vault toegangs beleid.

![Door de gebruiker toegewezen beheerde identiteit maken in Azure Portal](./media/apache-kafka-byok/user-managed-identity-portal.png)

## <a name="set-up-the-key-vault-and-keys"></a>De Key Vault en sleutels instellen

HDInsight ondersteunt alleen Azure Key Vault. Als u uw eigen sleutel kluis hebt, kunt u uw sleutels importeren in Azure Key Vault. Houd er rekening mee dat de sleutels ' zacht verwijderen ' moeten zijn. De functie ' zacht verwijderen ' is beschikbaar via de REST, .NET/C#, Power shell en Azure cli-interfaces.

1. Als u een nieuwe sleutel kluis wilt maken, volgt u de [Azure Key Vault](../../key-vault/key-vault-overview.md) Snelstartgids. Ga voor meer informatie over het importeren van bestaande sleutels naar [sleutels, geheimen en certificaten](../../key-vault/about-keys-secrets-and-certificates.md).

1. Schakel ' zacht-delete ' in op de sleutel kluis met behulp van de opdracht [AZ-kluis update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) cli.

    ```azurecli
    az keyvault update --name <Key Vault Name> --enable-soft-delete
    ```

1. Sleutels maken.

    a. Als u een nieuwe sleutel wilt maken, selecteert u **genereren/importeren** in het menu **sleutels** onder **instellingen**.

    ![Een nieuwe sleutel in Azure Key Vault genereren](./media/apache-kafka-byok/kafka-create-new-key.png "Een nieuwe sleutel in Azure Key Vault genereren")

    b. Stel **Opties** in voor het **genereren** en geven van de sleutel een naam.

    ![Apache Kafka genereert sleutel naam](./media/apache-kafka-byok/apache-kafka-create-key.png "Sleutel naam genereren")

    c. Selecteer de sleutel die u hebt gemaakt in de lijst met sleutels.

    ![Sleutel lijst voor Apache Kafka-sleutel kluis](./media/apache-kafka-byok/kafka-key-vault-key-list.png)

    d. Wanneer u uw eigen sleutel gebruikt voor Kafka-cluster versleuteling, moet u de sleutel-URI opgeven. Kopieer de **sleutel-id** en sla deze ergens op totdat u klaar bent om uw cluster te maken.

    ![Apache Kafka Get sleutel-id](./media/apache-kafka-byok/kafka-get-key-identifier.png)

1. Beheerde identiteit toevoegen aan het toegangs beleid voor de sleutel kluis.

    a. Maak een nieuw Azure Key Vault toegangs beleid.

    ![Nieuw toegangs beleid voor Azure Key Vault maken](./media/apache-kafka-byok/add-key-vault-access-policy.png)

    b. Kies onder **Principal selecteren**de door de gebruiker toegewezen beheerde identiteit die u hebt gemaakt.

    ![Selecteer de principal voor Azure Key Vault toegangs beleid instellen](./media/apache-kafka-byok/add-key-vault-access-policy-select-principal.png)

    c. Stel de **sleutel machtigingen** in om de sleutel op te **halen**, uit te **pakken**en te **verpakken**.

    ![Sleutel machtigingen instellen voor Azure Key Vault toegang policy1](./media/apache-kafka-byok/add-key-vault-access-policy-keys.png "Sleutel machtigingen instellen voor Azure Key Vault toegang policy1")

    d. Stel **geheime machtigingen** in op **Get**, **set**en **Delete**.

    ![Sleutel machtigingen instellen voor Azure Key Vault toegang policy2](./media/apache-kafka-byok/add-key-vault-access-policy-secrets.png "Sleutel machtigingen instellen voor Azure Key Vault toegang policy2")

    e. Selecteer **Opslaan**.

    ![Azure Key Vault toegangs beleid opslaan](./media/apache-kafka-byok/add-key-vault-access-policy-save.png)

## <a name="create-hdinsight-cluster"></a>HDInsight-cluster maken

U bent nu klaar om een nieuw HDInsight-cluster te maken. BYOK kan alleen worden toegepast op nieuwe clusters tijdens het maken van het cluster. Versleuteling kan niet worden verwijderd uit BYOK-clusters en BYOK kan niet worden toegevoegd aan bestaande clusters.

![Kafka-schijf versleuteling in Azure Portal](./media/apache-kafka-byok/azure-portal-cluster-security-networking-kafka-byok.png)

Geef tijdens het maken van het cluster de volledige sleutel-URL op, met inbegrip van de sleutel versie. Bijvoorbeeld `https://contoso-kv.vault.azure.net/keys/kafkaClusterKey/46ab702136bc4b229f8b10e8c2997fa4`. U moet ook de beheerde identiteit toewijzen aan het cluster en de sleutel-URI opgeven.

## <a name="rotating-the-encryption-key"></a>De versleutelings sleutel draaien

Er zijn mogelijk scenario's waarin u mogelijk de versleutelings sleutels wilt wijzigen die door het Kafka-cluster worden gebruikt nadat het is gemaakt. Dit kan eenvoudig zijn via de portal. Voor deze bewerking moet het cluster toegang hebben tot zowel de huidige sleutel als de beoogde nieuwe sleutel, anders kan de bewerking voor het draaien van de sleutel niet worden uitgevoerd.

Als u de sleutel wilt draaien, moet u de volledige URL van de nieuwe sleutel hebben (zie stap 3 van [de Key Vault en sleutels instellen](#set-up-the-key-vault-and-keys)). Als u dat hebt gedaan, gaat u naar de sectie cluster eigenschappen Kafka in de portal en klikt u op **sleutel wijzigen** onder URL voor de **schijf versleutelings sleutel**. Voer in de nieuwe sleutel-URL in en verzend de sleutel om deze te draaien.

![Kafka draai schijf versleutelings sleutel](./media/apache-kafka-byok/apache-kafka-change-key.png)

## <a name="faq-for-byok-to-apache-kafka"></a>Veelgestelde vragen over BYOK naar Apache Kafka

**Hoe opent het Kafka-cluster mijn sleutel kluis?**

Koppel een beheerde identiteit aan het HDInsight Kafka-cluster tijdens het maken van het cluster. Deze beheerde identiteit kan worden gemaakt vóór of tijdens het maken van het cluster. U moet ook de beheerde identiteits toegang verlenen aan de sleutel kluis waar de sleutel wordt opgeslagen.

**Is deze functie beschikbaar voor alle Kafka-clusters in HDInsight?**

BYOK-versleuteling is alleen mogelijk voor Kafka 1,1-en hoger-clusters.

**Kan ik verschillende sleutels voor verschillende onderwerpen/partities hebben?**

Nee, alle beheerde schijven in het cluster worden versleuteld met dezelfde sleutel.

**Wat gebeurt er als het cluster geen toegang meer heeft tot de sleutel kluis of de sleutel?**

Als het cluster de toegang tot de sleutel verliest, worden waarschuwingen weer gegeven in de Apache Ambari-Portal. In deze status mislukt de **wijzigings sleutel** bewerking. Zodra de toegang tot de sleutel is hersteld, worden de Ambari-waarschuwingen verwijderd en worden bewerkingen, zoals het draaien van sleutels, kunnen worden uitgevoerd.

![Ambari-waarschuwing voor sleutel toegang Apache Kafka](./media/apache-kafka-byok/kafka-byok-ambari-alert.png)

**Hoe kan ik het cluster herstellen als de sleutels worden verwijderd?**

Omdat alleen de sleutels "voorlopig verwijderen" worden ondersteund en de sleutels worden hersteld in de sleutel kluis, moet het cluster weer toegang krijgen tot de sleutels. Als u een Azure Key Vault sleutel wilt herstellen, raadpleegt u [Undo-AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) of [AZ-sleutel kluis-Key-Recover](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-recover).

**Kan ik producenten/consumenten-toepassingen gebruiken met een BYOK-cluster en een niet-BYOK-cluster tegelijk?**

Ja. Het gebruik van BYOK is transparant voor producenten/consumenten toepassingen. Versleuteling gebeurt op de laag van het besturings systeem. Er hoeven geen wijzigingen te worden aangebracht aan bestaande producenten/consumenten Kafka-toepassingen.

**Zijn besturingssysteem schijven/bron schijven ook versleuteld?**

Nee. BESTURINGSSYSTEEM schijven en bron schijven worden niet versleuteld.

**Als een cluster omhoog wordt geschaald, worden BYOK naadloos door de nieuwe Brokers ondersteund?**

Ja. Het cluster moet toegang hebben tot de sleutel in de sleutel kluis tijdens het omhoog schalen. Dezelfde sleutel wordt gebruikt voor het versleutelen van alle beheerde schijven in het cluster.

**Is BYOK beschikbaar op mijn locatie?**

Kafka BYOK is beschikbaar in alle open bare Clouds.

## <a name="next-steps"></a>Volgende stappen

* Zie [Wat is Azure Key Vault](../../key-vault/key-vault-overview.md)? voor meer informatie over Azure Key Vault.
* Zie aan de slag [met Azure Key Vault](../../key-vault/key-vault-overview.md)om aan de slag te gaan met Azure Key Vault.
