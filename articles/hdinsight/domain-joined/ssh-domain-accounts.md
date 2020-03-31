---
title: SSH-toegang beheren voor domeinaccounts in Azure HDInsight
description: Stappen om SSH-toegang voor Azure AD-accounts in HDInsight te beheren.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 5529989384df75b592afa8f5e4960eb9817fb2d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472516"
---
# <a name="manage-ssh-access-for-domain-accounts-in-azure-hdinsight"></a>SSH-toegang beheren voor domeinaccounts in Azure HDInsight

Op beveiligde clusters mogen standaard alle domeingebruikers in [Azure AD DS](../../active-directory-domain-services/overview.md) [SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) in de hoofd- en randknooppunten plaatsen. Deze gebruikers maken geen deel uit van de sudoers-groep en krijgen geen root-toegang. De SSH-gebruiker die tijdens het maken van het cluster is gemaakt, heeft roottoegang.

## <a name="manage-access"></a>Toegang beheren

Als u de SSH-toegang tot `/etc/ssh/sshd_config` specifieke gebruikers of groepen wilt wijzigen, werkt u bij op elk van de knooppunten.

1. Gebruik [de ssh-opdracht](../hdinsight-hadoop-linux-use-ssh-unix.md) om verbinding te maken met uw cluster. Bewerk de onderstaande opdracht door CLUSTERNAME te vervangen door de naam van uw cluster en voer de opdracht in:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Open `ssh_confi`het g-bestand.

    ```bash
    sudo nano /etc/ssh/sshd_config
    ```

1. Wijzig `sshd_config` het bestand naar wens. Als u gebruikers beperkt tot bepaalde groepen, kunnen de lokale accounts niet SSH in dat knooppunt. Het volgende is slechts een voorbeeld van syntaxis:

    ```bash
    AllowUsers useralias1 useralias2

    AllowGroups groupname1 groupname2
    ```

    Sla vervolgens wijzigingen op: **Ctrl + X,** **Y**, **Enter**.

1. Herstart sshd.

    ```bash
    sudo systemctl restart sshd
    ```

1. Herhaal bovenstaande stappen voor elk knooppunt.

## <a name="ssh-authentication-log"></a>SSH-verificatielogboek

SSH-verificatielogboek is `/var/log/auth.log`geschreven in . Als u inlogfouten ziet via SSH voor lokale of domeinaccounts, moet u door het logboek gaan om de fouten te debuggen. Vaak is het probleem kan worden gerelateerd aan specifieke gebruikersaccounts en het is meestal een goede gewoonte om andere gebruikersaccounts of SSH proberen met behulp van de standaard SSH-gebruiker (lokaal account) en vervolgens proberen een kinit.

## <a name="ssh-debug-log"></a>SSH-foutopsporingslogboek

Om verbose logging in te `sshd` schakelen, `-d` moet u opnieuw starten met de optie. Net `/usr/sbin/sshd -d` als u `sshd` ook draaien op een aangepaste poort (zoals 2222) zodat u niet hoeft te stoppen met de belangrijkste SSH daemon. U `-v` de optie ook gebruiken met de SSH-client om meer logboeken te krijgen (clientzijde van de fouten).

## <a name="next-steps"></a>Volgende stappen

* [HDInsight-clusters beheren met enterprise security pakket](./apache-domain-joined-manage.md)
* [Maak verbinding met HDInsight (Apache Hadoop) met Behulp van SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).
