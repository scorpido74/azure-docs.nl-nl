---
title: SSH-toegang beheren voor domein accounts in azure HDInsight
description: Stappen voor het beheren van SSH-toegang voor Azure AD-accounts in HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 5529989384df75b592afa8f5e4960eb9817fb2d7
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472516"
---
# <a name="manage-ssh-access-for-domain-accounts-in-azure-hdinsight"></a>SSH-toegang beheren voor domein accounts in azure HDInsight

Op beveiligde clusters mogen standaard alle domein gebruikers in [Azure AD DS](../../active-directory-domain-services/overview.md) [SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) in de hoofd knooppunten en Edge-knoop punten. Deze gebruikers maken geen deel uit van de sudo-groep en krijgen geen toegang tot de hoofdmap. De SSH-gebruiker die tijdens het maken van het cluster is gemaakt, heeft toegang tot het hoofd niveau.

## <a name="manage-access"></a>Toegang beheren

Als u SSH-toegang tot specifieke gebruikers of groepen wilt wijzigen, moet u `/etc/ssh/sshd_config` op elk knoop punt bijwerken.

1. Gebruik de [SSH-opdracht](../hdinsight-hadoop-linux-use-ssh-unix.md) om verbinding te maken met uw cluster. Bewerk de onderstaande opdracht door CLUSTERNAME te vervangen door de naam van uw cluster en voer vervolgens de volgende opdracht in:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Open het bestand `ssh_confi`g.

    ```bash
    sudo nano /etc/ssh/sshd_config
    ```

1. Wijzig het `sshd_config`-bestand naar wens. Als u gebruikers beperkt tot bepaalde groepen, kunnen de lokale accounts geen SSH-verbinding met dat knoop punt hebben. Hier volgt een voor beeld van een syntaxis:

    ```bash
    AllowUsers useralias1 useralias2

    AllowGroups groupname1 groupname2
    ```

    Sla de wijzigingen vervolgens op: **CTRL + X**, **Y**, **Enter**.

1. Start sshd opnieuw.

    ```bash
    sudo systemctl restart sshd
    ```

1. Herhaal de stappen voor elk knoop punt.

## <a name="ssh-authentication-log"></a>SSH-verificatie logboek

Het SSH-verificatie logboek wordt geschreven in `/var/log/auth.log`. Als er mislukte aanmeldingen via SSH worden weer geven voor lokale of domein accounts, moet u het logboek door lopen om de fouten op te sporen. Vaak is het probleem mogelijk gerelateerd aan specifieke gebruikers accounts en is het meestal een goede gewoonte om andere gebruikers accounts of SSH te proberen met behulp van de standaard SSH-gebruiker (lokaal account) en vervolgens een kinit te proberen.

## <a name="ssh-debug-log"></a>Fout opsporing SSH-logboek

Als u uitgebreide logboek registratie wilt inschakelen, moet u `sshd` opnieuw opstarten met de optie `-d`. Net als `/usr/sbin/sshd -d` kunt u ook `sshd` uitvoeren op een aangepaste poort (zoals 2222), zodat u de hoofd-SSH-daemon niet hoeft te stoppen. U kunt ook `-v` optie met de SSH-client gebruiken om meer logboeken te ontvangen (weer gave aan client zijde van de fouten).

## <a name="next-steps"></a>Volgende stappen

* [HDInsight-clusters beheren met Enterprise Security Package](./apache-domain-joined-manage.md)
* [Verbinding maken met HDInsight (Apache Hadoop) met behulp van SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).
