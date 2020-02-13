---
title: Opslagaccounts configureren voor Cloudyn in Azure | Microsoft Docs
description: In dit artikel wordt beschreven hoe u Azure-opslagaccounts en AWS-opslagbuckets configureert voor Cloudyn.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/24/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: benshy
ms.custom: secdec18
ms.openlocfilehash: ec6c68d209f867ab3547e855f3cf754bc27d53c5
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77117626"
---
# <a name="configure-storage-accounts-for-cloudyn"></a>Opslagaccounts configureren voor Cloudyn

<!--- intent: As a Cloudyn user, I want to configure Cloudyn to use my cloud service provider storage account to store my reports. -->

U kunt Cloudyn-rapporten opslaan in de Cloudyn-portal, Azure-opslag of AWS-opslagbuckets. Het opslaan van uw rapporten in de Cloudyn-portal is gratis. Het opslaan van uw rapporten in de opslag van uw cloudserviceprovider is optioneel en brengt extra kosten met zich mee. Dit artikel helpt u bij het configureren van Azure-opslagaccounts en AWS-opslagbuckets (Amazon Web Services) voor het opslaan van uw rapporten.

## <a name="prerequisites"></a>Vereisten

U moet een Azure-opslagaccount of een Amazon-opslagbucket hebben.

Als u geen Azure-opslagaccount hebt, moet u er een maken. Zie [Een opslagaccount maken](../../storage/common/storage-account-create.md) voor more informatie over het maken van een Azure-opslagaccount.

Als u geen S3-bucket (simple storage service) van AWS hebt, moet u er een maken. Zie [Create a bucket](https://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html) voor meer informatie over het maken van een S3-bucket.

## <a name="configure-your-azure-storage-account"></a>Uw Azure-opslagaccount configureren

Het configureren van Azure-opslag voor gebruik door Cloudyn is eenvoudig. Verzamel details over het opslagaccount en kopieer deze naar de Cloudyn-portal.

1. Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).
2. Klik op **Alle services**, selecteer **Opslagaccounts**, blader naar het opslagaccount dat u wilt gebruiken en selecteer vervolgens het account.
3. Klik op de pagina van uw opslagaccount onder **Instellingen** op **Toegangssleutels**.
4. Kopieer de **naam van uw opslagaccount** en de **verbindingsreeks** onder key1.  
   ![Naam van opslagaccount en verbindingsreeks kopiëren](./media/storage-accounts/azure-storage-access-keys.png)  
5. Open de Cloudyn-portal vanuit de Azure-portal of ga naar [https://azure.cloudyn.com](https://azure.cloudyn.com) en meld u aan.
6. Klik op het tandwielsymbool en selecteer vervolgens **Reports Storage Management** (Rapportopslagbeheer).
7. Klik op **Add New +** (Nieuwe toevoegen) en zorg ervoor dat Microsoft Azure is geselecteerd. Plak de naam van uw Azure-opslagaccount in het gebied **Name** (Naam). Plak uw **verbindingsreeks** in het corresponderende gebied. Voer een containernaam in en klik op **Save** (Opslaan).  
   ![De naam van het Azure-opslagaccount en de verbindingsreeks in het vak Add a new report storage (Nieuwe rapportopslag toevoegen) plakken](./media/storage-accounts/azure-cloudyn-storage.png)

   Uw nieuwe Azure-rapportopslagvermelding wordt weergegeven in de lijst met opslagaccounts.  
    ![Nieuwe Azure-rapportopslagvermelding in lijst](./media/storage-accounts/azure-storage-entry.png)


U kunt nu rapporten opslaan in Azure-opslag. Klik vanuit een willekeurig rapport op **Acties** en selecteer vervolgens **Rapport plannen**. Geef een naam voor het rapport op en voeg uw eigen URL toe of gebruik de automatisch gemaakte URL. Selecteer **Opslaan in opslag** en selecteer vervolgens het opslagaccount. Voer een voorvoegsel in dat wordt toegevoegd aan de bestandsnaam van het rapport. Selecteer CSV of JSON als bestandsindeling en sla het rapport op.

## <a name="configure-an-aws-storage-bucket"></a>Een AWS-opslagbucket configureren

Cloudyn maakt gebruik van bestaande AWS-referenties, Gebruiker of Rol, om de rapporten op te slaan in de bucket. Om de toegang te testen, probeert Cloudyn een klein tekstbestand met de naam _check-bucket-permission.txt_ op te slaan in de bucket.

Geef de Cloudyn-rol of -gebruiker op met de machtiging PutObject voor uw bucket. Gebruik vervolgens een bestaande bucket of maak een nieuwe bucket om rapporten in op te slaan. Bepaal ten slotte hoe u de opslagklasse beheert, levenscyclusregels instelt of overbodige bestanden verwijdert.

###  <a name="assign-permissions-to-your-aws-user-or-role"></a>Machtigingen toewijzen aan uw AWS-gebruiker of -rol

Wanneer u een nieuw beleid maakt, geeft u de exacte machtigingen op die nodig zijn om een rapport op te slaan in een S3-bucket.

1. Meld u aan bij de AWS-console en selecteer **Services**.
2. Selecteer **IAM** in de lijst met services.
3. Selecteer **Policies** (Beleidsregels) links in de console en klik vervolgens op **Create Policy** (Beleid maken).
4. Klik op het tabblad **JSON**.
5. Met het volgende beleid kunt u een rapport opslaan in een S3-bucket. Kopieer en plak het volgende beleidsvoorbeeld op het tabblad **JSON**. Vervang &lt;bucketname&gt; door de naam van uw bucket.

   ```json
   {
    "Version": "2012-10-17",
    "Statement": [
      {
        "Sid":  "CloudynSaveReport2S3",
        "Effect":      "Allow",
        "Action": [
          "s3:PutObject"
        ],
        "Resource": [
          "arn:aws:s3:::<bucketname>/*"
        ]
      }
    ]
   }
   ```

6. Klik op **Review policy** (Beleid beoordelen).  
    ![JSON-beleid in AWS met voorbeeldinformatie](./media/storage-accounts/aws-policy.png)  
7. Typ een naam voor uw beleid op de pagina Beleid beoordelen. Bijvoorbeeld: _CloudynSaveReport2S3_.
8. Klik op **Create policy** (Beleid maken).

### <a name="attach-the-policy-to-a-cloudyn-role-or-user-in-your-account"></a>Het beleid koppelen aan een Cloudyn-rol of -gebruiker in uw account

U kunt het nieuwe beleid koppelen door de AWS-console te openen en de Cloudyn-rol of -gebruiker te bewerken.

1. Meld u aan bij de AWS-console, selecteer **Services** en selecteer vervolgens **IAM** in de lijst met services.
2. Selecteer **Roles** (Rollen) of **Users** (Gebruikers) links in de console.

**Voor rollen:**

  1. Klik op de naam van uw Cloudyn-rol.
  2. Klik op het tabblad **Permissions** (Machtigingen) op **Attach Policy** (Beleid koppelen).
  3. Zoek het beleid dat u hebt gemaakt, selecteer het en klik vervolgens op **Attach Policy** (Beleid koppelen).
    ![Voorbeeld van beleid dat aan uw Cloudyn-rol is gekoppeld](./media/storage-accounts/aws-attach-policy-role.png)

**Voor gebruikers:**

1. Selecteer de Cloudyn-gebruiker.
2. Klik op het tabblad **Permissions** (Machtigingen) op **Add permissions** (Machtigingen toevoegen).
3. Selecteer **Attach existing policies directly** in de sectie **Grant Permission** (Toestemming geven).
4. Zoek het beleid dat u hebt gemaakt, selecteer het en klik vervolgens op **Next: Review**.
5. Klik op de pagina Add permissions to role name (Machtigingen toevoegen aan rolnaam) op **Add permissions** (Machtigingen toevoegen).  
    ![Voorbeeld van beleid dat aan uw Cloudyn-gebruiker is gekoppeld](./media/storage-accounts/aws-attach-policy-user.png)


### <a name="optional-set-permission-with-bucket-policy"></a>Optioneel: Machtiging instellen met bucketbeleid

U kunt ook machtigingen voor het maken van rapporten in de S3-bucket instellen met behulp van een bucketbeleid. In de klassieke S3-weergave:

1. Maak of selecteer een bestaande bucket.
2. Selecteer het tabblad **Permissions** (Machtigingen) en klik vervolgens op **Bucket policy** (Bucketbeleid).
3. Kopieer en plak het volgende voorbeeldscript. Vervang &lt;bucket\_name&gt; en &lt;Cloudyn\_principle&gt; door de ARN van uw bucket. Vervang de ARN van de rol of gebruiker die door Cloudyn wordt gebruikt.

   ```
   {
   "Id": "Policy1485775646248",
   "Version": "2012-10-17",
   "Statement": [
    {
      "Sid": "SaveReport2S3",
      "Action": [
        "s3:PutObject"
      ],
      "Effect": "Allow",
      "Resource": "<bucket_name>/*",
      "Principal": {
        "AWS": [
          "<Cloudyn_principle>"
        ]
      }
    }
   ]
   }
   ```

4. Klik in de editor voor bucketbeleid op **Save** (Opslaan).

### <a name="add-aws-report-storage-to-cloudyn"></a>AWS-rapportopslag toevoegen aan Cloudyn

1. Open de Cloudyn-portal vanuit de Azure-portal of ga naar [https://azure.cloudyn.com](https://azure.cloudyn.com) en meld u aan.
2. Klik op het tandwielsymbool en selecteer vervolgens **Reports Storage Management** (Rapportopslagbeheer).
3. Klik op **Add New +** (Nieuwe toevoegen) en zorg ervoor dat AWS is geselecteerd.
4. Selecteer een account en een opslagbucket. De naam van de AWS-opslagbucket wordt automatisch ingevuld.  
    ![Voorbeeld van informatie in het vak Add a new report storage](./media/storage-accounts/aws-cloudyn-storage.png)  
5. Klik op **Save** (Opslaan) en klik vervolgens op **Ok**.

    Uw nieuwe AWS-rapportopslagvermelding wordt weergegeven in de lijst met opslagaccounts.  
    ![Nieuwe AWS-rapportopslagvermelding in de lijst met opslagaccounts](./media/storage-accounts/aws-storage-entry.png)


U kunt nu rapporten opslaan in Azure-opslag. Klik in een willekeurig rapport op **Acties** en selecteer vervolgens **Rapport plannen**. Geef een naam voor het rapport op en voeg uw eigen URL toe of gebruik de automatisch gemaakte URL. Selecteer **Opslaan in opslag** en selecteer vervolgens het opslagaccount. Voer een voorvoegsel in dat wordt toegevoegd aan de bestandsnaam van het rapport. Selecteer CSV of JSON als bestandsindeling en sla het rapport op.

## <a name="next-steps"></a>Volgende stappen

- Lees [Inzicht in Cloudyn-rapporten](understanding-cost-reports.md) voor meer informatie over de basisstructuur en functies van Cloudyn-rapporten.
