---
title: Verificatieproblemen in Azure HDInsight
description: Verificatieproblemen in Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 11/08/2019
ms.openlocfilehash: 26eec9cdd327ceb51e72deb1d6f40d585ce368fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75896126"
---
# <a name="authentication-issues-in-azure-hdinsight"></a>Verificatieproblemen in Azure HDInsight

In dit artikel worden stappen voor het oplossen van problemen en mogelijke oplossingen voor problemen beschreven bij interactie met Azure HDInsight-clusters.

Op beveiligde clusters die worden ondersteund door Azure Data Lake (Gen1 of Gen2), probeert HDI Gateway eerst een OAuth-token te verkrijgen van Azure Active Directory (Azure AD) wanneer domeingebruikers zich aanmelden bij de clusterservices (zoals aanmelden bij de Apache Ambari-portal), wanneer ze eerst een OAuth-token van Azure Active Directory (Azure AD) verkrijgen en ontvang vervolgens een Kerberos-ticket van Azure AD DS. Verificatie kan in een van deze fasen mislukken. Dit artikel is gericht op het debuggen van een aantal van deze kwesties.

Wanneer de verificatie mislukt, wordt u gevraagd om referenties. Als u dit dialoogvenster annuleert, wordt het foutbericht afgedrukt. Hier volgen enkele van de veelvoorkomende foutmeldingen:

## <a name="invalid_grant-or-unauthorized_client-50126"></a>invalid_grant of unauthorized_client, 50126

### <a name="issue"></a>Probleem

Aanmelden mislukt voor federatieve gebruikers met foutcode 50126 (aanmelden slaagt voor cloudgebruikers). Foutbericht is vergelijkbaar met:

```
Reason: Bad Request, Detailed Response: {"error":"invalid_grant","error_description":"AADSTS70002: Error validating credentials. AADSTS50126: Invalid username or password\r\nTrace ID: 09cc9b95-4354-46b7-91f1-efd92665ae00\r\n Correlation ID: 4209bedf-f195-4486-b486-95a15b70fbe4\r\nTimestamp: 2019-01-28 17:49:58Z","error_codes":[70002,50126], "timestamp":"2019-01-28 17:49:58Z","trace_id":"09cc9b95-4354-46b7-91f1-efd92665ae00","correlation_id":"4209bedf-f195-4486-b486-95a15b70fbe4"}
```

### <a name="cause"></a>Oorzaak

Azure AD-foutcode 50126 betekent dat het `AllowCloudPasswordValidation` beleid niet is ingesteld door de tenant.

### <a name="resolution"></a>Oplossing

De bedrijfsbeheerder van de Azure AD-tenant moet Azure AD in staat stellen wachtwoordhashes te gebruiken voor aDFS-gebruikers die worden ondersteund.  Pas `AllowCloudPasswordValidationPolicy` de zoals weergegeven in het artikel [Gebruik Enterprise Security Package in HDInsight](../domain-joined/apache-domain-joined-architecture.md).

---

## <a name="invalid_grant-or-unauthorized_client-50034"></a>invalid_grant of unauthorized_client, 50034

### <a name="issue"></a>Probleem

Aanmelden mislukt met foutcode 50034. Foutbericht is vergelijkbaar met:

```
{"error":"invalid_grant","error_description":"AADSTS50034: The user account Microsoft.AzureAD.Telemetry.Diagnostics.PII does not exist in the 0c349e3f-1ac3-4610-8599-9db831cbaf62 directory. To sign into this application, the account must be added to the directory.\r\nTrace ID: bbb819b2-4c6f-4745-854d-0b72006d6800\r\nCorrelation ID: b009c737-ee52-43b2-83fd-706061a72b41\r\nTimestamp: 2019-04-29 15:52:16Z", "error_codes":[50034],"timestamp":"2019-04-29 15:52:16Z","trace_id":"bbb819b2-4c6f-4745-854d-0b72006d6800", "correlation_id":"b009c737-ee52-43b2-83fd-706061a72b41"}
```

### <a name="cause"></a>Oorzaak

Gebruikersnaam is onjuist (bestaat niet). De gebruiker gebruikt niet dezelfde gebruikersnaam die wordt gebruikt in de Azure-portal.

### <a name="resolution"></a>Oplossing

Gebruik dezelfde gebruikersnaam die in die portal werkt.

---

## <a name="invalid_grant-or-unauthorized_client-50053"></a>invalid_grant of unauthorized_client, 50053

### <a name="issue"></a>Probleem

Gebruikersaccount is vergrendeld, foutcode 50053. Foutbericht is vergelijkbaar met:

```
{"error":"unauthorized_client","error_description":"AADSTS50053: You've tried to sign in too many times with an incorrect user ID or password.\r\nTrace ID: 844ac5d8-8160-4dee-90ce-6d8c9443d400\r\nCorrelation ID: 23fe8867-0e8f-4e56-8764-0cdc7c61c325\r\nTimestamp: 2019-06-06 09:47:23Z","error_codes":[50053],"timestamp":"2019-06-06 09:47:23Z","trace_id":"844ac5d8-8160-4dee-90ce-6d8c9443d400","correlation_id":"23fe8867-0e8f-4e56-8764-0cdc7c61c325"}
```

### <a name="cause"></a>Oorzaak

Te veel aanmeldingspogingen met een onjuist wachtwoord.

### <a name="resolution"></a>Oplossing

Wacht ongeveer 30 minuten, stop alle toepassingen die mogelijk proberen te verifiëren.

---

## <a name="invalid_grant-or-unauthorized_client-50053"></a>invalid_grant of unauthorized_client, 50053

### <a name="issue"></a>Probleem

Wachtwoord verlopen, foutcode 50053. Foutbericht is vergelijkbaar met:

```
{"error":"user_password_expired","error_description":"AADSTS50055: Password is expired.\r\nTrace ID: 241a7a47-e59f-42d8-9263-fbb7c1d51e00\r\nCorrelation ID: c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a\r\nTimestamp: 2019-06-06 17:29:37Z","error_codes":[50055],"timestamp":"2019-06-06 17:29:37Z","trace_id":"241a7a47-e59f-42d8-9263-fbb7c1d51e00","correlation_id":"c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a","suberror":"user_password_expired","password_change_url":"https://portal.microsoftonline.com/ChangePassword.aspx"}
```

### <a name="cause"></a>Oorzaak

Het wachtwoord is verlopen.

### <a name="resolution"></a>Oplossing

Wijzig het wachtwoord in de Azure-portal (op uw on-premises systeem) en wacht vervolgens 30 minuten tot de synchronisatie wordt ingehaald.

---

## <a name="interaction_required"></a>interaction_required

### <a name="issue"></a>Probleem

Ontvang een `interaction_required`foutbericht .

### <a name="cause"></a>Oorzaak

Het beleid voor voorwaardelijke toegang of MFA wordt toegepast op de gebruiker. Omdat interactieve verificatie nog niet wordt ondersteund, moet de gebruiker of het cluster worden uitgesloten van MFA/voorwaardelijke toegang. Als u ervoor kiest het cluster vrij te stellen (op `ServiceEndpoints` IP-adres gebaseerd vrijstellingsbeleid), controleert u of het AD is ingeschakeld voor dat vnet.

### <a name="resolution"></a>Oplossing

Gebruik het beleid voor voorwaardelijke toegang en verlos de HDInisght-clusters van MFA zoals weergegeven in [Een HDInsight-cluster configureren met Enterprise Security Package met Azure Active Directory Domain Services](./apache-domain-joined-configure-using-azure-adds.md).

---

## <a name="sign-in-denied"></a>Aanmelden geweigerd

### <a name="issue"></a>Probleem

Aanmelden wordt geweigerd.

### <a name="cause"></a>Oorzaak

Om naar deze fase, uw OAuth authenticatie is geen probleem, maar Kerberos authenticatie is. Als dit cluster wordt ondersteund door ADLS, is OAuth-aanmelding geslaagd voordat Kerberos auth wordt geprobeerd. Op WASB-clusters wordt OAuth-aanmelding niet geprobeerd. Er kunnen veel redenen zijn voor Kerberos-fouten- zoals wachtwoordhashes niet synchroon lopen, gebruikersaccount is vergrendeld in Azure AD DS, enzovoort. Wachtwoord hashes synchroniseren alleen wanneer de gebruiker wachtwoord wijzigt. Wanneer u het Azure AD DS-exemplaar maakt, worden wachtwoorden gesynchroniseerd die na de creatie zijn gewijzigd. Het zal niet met terugwerkende kracht wachtwoorden synchroniseren die zijn ingesteld vóór de aanvang.

### <a name="resolution"></a>Oplossing

Als u denkt dat wachtwoorden mogelijk niet synchroon lopen, probeert u het wachtwoord te wijzigen en wacht u een paar minuten om te synchroniseren.

Probeer ssh in een U moet proberen te verifiëren (kinit) met behulp van dezelfde gebruikersreferenties, van een machine die is aangesloten op het domein. SSH in het hoofd / rand knooppunt met een lokale gebruiker en vervolgens lopen kinit.

---

## <a name="kinit-fails"></a>kinit faalt

### <a name="issue"></a>Probleem

Kinit faalt.

### <a name="cause"></a>Oorzaak

Varieert.

### <a name="resolution"></a>Oplossing

Om te slagen, moet je je `sAMAccountName` weten (dit is de naam van het korte account zonder het rijk). `sAMAccountName`is meestal het account voorvoegsel (zoals bob in `bob@contoso.com`). Voor sommige gebruikers kan het anders zijn. Je moet de mogelijkheid om te bladeren `sAMAccountName`/ zoeken in de directory om je te leren .

Manieren om `sAMAccountName`te vinden:

* Als u zich bij Ambari aanmelden met de lokale Ambari-beheerder, bekijkt u de lijst met gebruikers.

* Als u een [windows-machine voor een domein hebt aangesloten,](../../active-directory-domain-services/manage-domain.md)u de standaard hulpprogramma's van Windows AD gebruiken om te bladeren. Hiervoor is een werkend account in het domein vereist.

* Vanaf het hoofdknooppunt u SAMBA-opdrachten gebruiken om te zoeken. Dit vereist een geldige Kerberos sessie (succesvolle kinit). net advertenties zoeken "(userPrincipalName=bob*)"

    De zoekresultaten/ bladerresultaten moeten `sAMAccountName` u het kenmerk laten zien. Ook zou je kunnen kijken `pwdLastSet`naar `badPasswordTime` `userPrincipalName` andere attributen zoals , , etc. om te zien of die eigenschappen overeenkomen met wat je verwacht.

---

## <a name="kinit-fails-with-preauthentication-failure"></a>kinit mislukt met Preauthentication-fout

### <a name="issue"></a>Probleem

Kinit faalt `Preauthentication` met falen.

### <a name="cause"></a>Oorzaak

Onjuiste gebruikersnaam of wachtwoord.

### <a name="resolution"></a>Oplossing

Controleer uw gebruikersnaam en wachtwoord. Controleer ook voor andere eigenschappen hierboven beschreven. Als u het foutopsporing `export KRB5_TRACE=/tmp/krb.log` van verbose wilt inschakelen, voert u vanaf de sessie uit voordat u kinit probeert.

---

## <a name="job--hdfs-command-fails-due-to-tokennotfoundexception"></a>Opdracht Taak / HDFS mislukt vanwege TokenNotFoundException

### <a name="issue"></a>Probleem

Taak / HDFS-opdracht `TokenNotFoundException`mislukt als gevolg van .

### <a name="cause"></a>Oorzaak

Het vereiste OAuth-toegangstoken is niet gevonden om de taak/opdracht te laten slagen. Het ADLS/ ABFS-stuurprogramma probeert het OAuth-toegangstoken op te halen uit de referentieservice voordat u opslagaanvragen invoert. Dit token wordt geregistreerd wanneer u zich aanmeldt bij de Ambari-portal met dezelfde gebruiker.

### <a name="resolution"></a>Oplossing

Zorg ervoor dat u eenmaal met succes bent ingelogd op de Ambari-portal via de gebruikersnaam waarvan de identiteit wordt gebruikt om de taak uit te voeren.

---

## <a name="error-fetching-access-token"></a>Fout bij het ophalen van toegangstoken

### <a name="issue"></a>Probleem

Gebruiker ontvangt `Error fetching access token`foutbericht .

### <a name="cause"></a>Oorzaak

Deze fout treedt met tussenpozen op wanneer gebruikers toegang proberen te krijgen tot de ADLS Gen2 met ACL's en het Kerberos-token is verlopen.

### <a name="resolution"></a>Oplossing

* Maak voor Azure Data Lake Storage Gen1 de cache van de browser schoon en meld u opnieuw aan bij Ambari.

* Voor Azure Data Lake Storage `/usr/lib/hdinsight-common/scripts/RegisterKerbWithOauth.sh <upn>` Gen2, uitvoeren voor de gebruiker die de gebruiker probeert in te loggen als

---

## <a name="next-steps"></a>Volgende stappen

Als je je probleem niet hebt gezien of niet in staat bent om je probleem op te lossen, ga je naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure Community Support.](https://azure.microsoft.com/support/community/)

* Maak [@AzureSupport](https://twitter.com/azuresupport) verbinding met - het officiële Microsoft Azure-account voor het verbeteren van de klantervaring. De Azure-community verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, u een ondersteuningsaanvraag indienen via de [Azure-portal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecteer **Ondersteuning** op de menubalk of open de **Help + ondersteuningshub.** Voor meer gedetailleerde informatie, bekijk [Hoe maak je een Azure-ondersteuningsaanvraag](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Toegang tot abonnementsbeheer en factureringsondersteuning is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geboden via een van de [Azure Support-abonnementen](https://azure.microsoft.com/support/plans/).
