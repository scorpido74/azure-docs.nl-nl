---
title: 'Zelfstudie: E-mailmeldingen van Apache Ambari configureren in Azure HDInsight'
description: In dit artikel wordt beschreven hoe u SendGrid met Apache Ambari gebruiken voor e-mailmeldingen.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: tutorial
ms.date: 03/10/2020
ms.openlocfilehash: 21376eb40fb40abe67f7e03d15aabd7d89ea62f8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80082312"
---
# <a name="tutorial-configure-apache-ambari-email-notifications-in-azure-hdinsight"></a>Zelfstudie: E-mailmeldingen van Apache Ambari configureren in Azure HDInsight

In deze zelfstudie configureert u Apache Ambari-e-mailmeldingen met SendGrid. [Apache Ambari](./hdinsight-hadoop-manage-ambari.md) vereenvoudigt het beheer en de monitoring van een HDInsight-cluster door een gebruiksvriendelijke web-gebruikersinterface en REST API te bieden. Ambari is opgenomen in HDInsight-clusters en wordt gebruikt om het cluster te bewaken en configuratiewijzigingen aan te brengen. [SendGrid](https://sendgrid.com/solutions/) is een gratis cloudgebaseerde e-mailservice die betrouwbare transactionele e-maillevering, schaalbaarheid en realtime analyses biedt, samen met flexibele API's die aangepaste integratie eenvoudig maken. Azure-klanten kunnen iedere maand 25.000 gratis e-mails ontgrendelen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Gebruikersnaam Sendgrid verkrijgen
> * E-mailmeldingen van Apache Ambari configureren

## <a name="prerequisites"></a>Vereisten

* Een SendGrid-e-mailaccount. Zie [Hoe u e-mail verzendt met SendGrid met Azure](https://docs.microsoft.com/azure/sendgrid-dotnet-how-to-send-email) voor instructies.

* Een HDInsight-cluster. Zie [Apache Hadoop-clusters maken met behulp van de Azure-portal](./hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="obtain-sendgrid-username"></a>Gebruikersnaam SendGrid verkrijgen

1. Navigeer vanuit de [Azure-portal](https://portal.azure.com)naar uw SendGrid-bron.

1. Selecteer op de pagina Overzicht de optie **Beheren**om de webpagina SendGrid voor uw account te gaan.

    ![SendGrid-overzicht in azure portal](./media/apache-ambari-email/azure-portal-sendgrid-manage.png)

1. Navigeer in het linkermenu naar de naam van uw account en vervolgens **naar Accountgegevens**.

    ![Navigatie van sendGrid-dashboard](./media/apache-ambari-email/sendgrid-dashboard-navigation.png)

1. Neem op de pagina **Accountgegevens** de **gebruikersnaam op.**

    ![Gegevens van SendGrid-account](./media/apache-ambari-email/sendgrid-account-details.png)

## <a name="configure-ambari-e-mail-notification"></a>Ambari-e-mailmelding configureren

1. Navigeer vanuit een webbrowser `https://CLUSTERNAME.azurehdinsight.net/#/main/alerts`naar `CLUSTERNAME` , waar is de naam van uw cluster.

1. Selecteer **Meldingen beheren**in de vervolgkeuzelijst **Acties** .

1. Selecteer in het venster **Waarschuwingsmeldingen beheren** het **+** pictogram.

    ![Ambari maken waarschuwingsmelding](./media/apache-ambari-email/azure-portal-create-notification.png)

1. Geef in het dialoogvenster **Waarschuwingsmelding maken** de volgende gegevens op:

    |Eigenschap |Beschrijving |
    |---|---|
    |Name|Geef een naam op voor de melding.|
    |Groepen|Configureren zoals gewenst.|
    |Severity|Configureren zoals gewenst.|
    |Beschrijving|Optioneel.|
    |Methode|Laat op **e-mail**.|
    |E-mail naar|E-mail(s) verstrekken om meldingen te ontvangen, gescheiden door een komma.|
    |SMTP-server|`smtp.sendgrid.net`|
    |SMTP-poort|25 of 587 (voor onversleutelde/TLS-verbindingen).|
    |E-mail van|Geef een e-mailadres op. Het adres hoeft niet authentiek te zijn.|
    |Verificatie gebruiken|Schakel dit selectievakje in.|
    |Gebruikersnaam|Geef de gebruikersnaam SendGrid op.|
    |Wachtwoord|Geef het wachtwoord op dat u hebt gebruikt toen u de SendGrid-bron in Azure maakte.|
    |Bevestiging van wachtwoord|Voer het wachtwoord opnieuw in.|
    |TLS starten|Schakel dit selectievakje in|

    ![Ambari maken waarschuwingsmelding](./media/apache-ambari-email/ambari-create-alert-notification.png)

    Selecteer **Opslaan**. Je gaat terug naar het venster **Waarschuwingsmeldingen beheren.**

1. Selecteer In het venster **Waarschuwingsmeldingen beheren** de optie **Sluiten**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u Apache Ambari-e-mailmeldingen configureren met SendGrid. Gebruik het volgende om meer te weten te komen over Apache Ambari:

* [HDInsight-clusters beheren met behulp van de Apache Ambari-webinterface](./hdinsight-hadoop-manage-ambari.md)

* [Een waarschuwingsmelding maken](https://docs.cloudera.com/HDPDocuments/Ambari-latest/managing-and-monitoring-ambari/content/amb_create_an_alert_notification.html)
