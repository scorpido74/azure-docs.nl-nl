---
title: 'Zelf studie: Apache Ambari-e-mail meldingen configureren in azure HDInsight'
description: In dit artikel wordt beschreven hoe u SendGrid gebruikt met Apache Ambari voor e-mail meldingen.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: tutorial
ms.date: 03/10/2020
ms.openlocfilehash: 21376eb40fb40abe67f7e03d15aabd7d89ea62f8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80082312"
---
# <a name="tutorial-configure-apache-ambari-email-notifications-in-azure-hdinsight"></a>Zelf studie: Apache Ambari-e-mail meldingen configureren in azure HDInsight

In deze zelf studie configureert u Apache Ambari-e-mail meldingen met behulp van SendGrid. [Apache Ambari](./hdinsight-hadoop-manage-ambari.md) vereenvoudigt het beheer en de controle van een HDInsight-cluster door een gebruiks vriendelijke webgebruikersinterface en rest API te bieden. Ambari is opgenomen in HDInsight-clusters en wordt gebruikt om het cluster te controleren en configuratie wijzigingen aan te brengen. [SendGrid](https://sendgrid.com/solutions/) is een gratis cloud-gebaseerde e-mail service die betrouw bare transactionele e-mail levering, schaal baarheid en real-time analyse biedt, samen met flexibele api's die aangepaste integratie eenvoudig maken. Azure-klanten kunnen iedere maand 25.000 gratis e-mails ontgrendelen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Sendgrid-gebruikers naam ophalen
> * Apache Ambari-e-mail meldingen configureren

## <a name="prerequisites"></a>Vereisten

* Een SendGrid-e-mail account. Zie [E-mail verzenden met behulp van SendGrid met Azure](https://docs.microsoft.com/azure/sendgrid-dotnet-how-to-send-email) voor instructies.

* An HDInsight cluster. Zie [Apache Hadoop clusters maken met behulp van de Azure Portal](./hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="obtain-sendgrid-username"></a>SendGrid-gebruikers naam ophalen

1. Navigeer vanuit het [Azure Portal](https://portal.azure.com)naar uw SendGrid-resource.

1. Selecteer op de pagina overzicht de optie **beheren**om de SendGrid-webpagina voor uw account te bezoeken.

    ![Overzicht van SendGrid in azure Portal](./media/apache-ambari-email/azure-portal-sendgrid-manage.png)

1. Ga in het menu links naar uw account naam en vervolgens op **account gegevens**.

    ![SendGrid-dashboard navigatie](./media/apache-ambari-email/sendgrid-dashboard-navigation.png)

1. Noteer op de pagina **account Details** de **gebruikers naam**.

    ![Details van SendGrid-account](./media/apache-ambari-email/sendgrid-account-details.png)

## <a name="configure-ambari-e-mail-notification"></a>E-mail meldingen voor Ambari configureren

1. Ga in een webbrowser naar `https://CLUSTERNAME.azurehdinsight.net/#/main/alerts`, waarbij `CLUSTERNAME` de naam van het cluster is.

1. Selecteer in de vervolg keuzelijst **acties** de optie **meldingen beheren**.

1. Selecteer het **+** pictogram in het venster **waarschuwings meldingen beheren** .

    ![Ambari-waarschuwings melding maken](./media/apache-ambari-email/azure-portal-create-notification.png)

1. Geef in het dialoog venster **waarschuwings melding maken** de volgende informatie op:

    |Eigenschap |Beschrijving |
    |---|---|
    |Naam|Geef een naam op voor de melding.|
    |Groepen|Configureer naar wens.|
    |Severity|Configureer naar wens.|
    |Beschrijving|Optioneel.|
    |Methode|Bij **e-mail**blijven.|
    |E-mail verzenden naar|Geef een of meer e-mail berichten op om meldingen te ontvangen, gescheiden door een komma.|
    |SMTP-server|`smtp.sendgrid.net`|
    |SMTP-poort|25 of 587 (voor niet-versleutelde/TLS-verbindingen).|
    |E-mail van|Geef een e-mail adres op. Het adres hoeft niet authentiek te zijn.|
    |Authenticatie gebruiken|Schakel dit selectie vakje in.|
    |Gebruikersnaam|Geef de SendGrid-gebruikers naam op.|
    |Wachtwoord|Geef het wacht woord op dat u hebt gebruikt bij het maken van de SendGrid-resource in Azure.|
    |Wachtwoord bevestiging|Voer het wachtwoord opnieuw in.|
    |TLS starten|Schakel dit selectie vakje in|

    ![Ambari-waarschuwings melding maken](./media/apache-ambari-email/ambari-create-alert-notification.png)

    Selecteer **Opslaan**. U keert terug naar het venster **waarschuwings meldingen beheren** .

1. In het venster **waarschuwings meldingen beheren** selecteert u **sluiten**.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u Apache Ambari-e-mail meldingen kunt configureren met behulp van SendGrid. Gebruik de volgende informatie om meer te weten te komen over Apache Ambari:

* [HDInsight-clusters beheren met behulp van de Apache Ambari-webinterface](./hdinsight-hadoop-manage-ambari.md)

* [Een waarschuwings melding maken](https://docs.cloudera.com/HDPDocuments/Ambari-latest/managing-and-monitoring-ambari/content/amb_create_an_alert_notification.html)
