---
title: Storage-accounts configureren voor Cloudyn in Azure | Microsoft Docs
description: In dit artikel wordt beschreven hoe u Azure storage-accounts en AWS opslag buckets voor Cloudyn configureren.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/20/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: benshy
ms.custom: secdec18
ms.openlocfilehash: 1ac4442aa5a7e5e4367a03d33169412d37b3f1ea
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229933"
---
# <a name="configure-storage-accounts-for-cloudyn"></a>Storage-accounts voor Cloudyn configureren

<!--- intent: As a Cloudyn user, I want to configure Cloudyn to use my cloud service provider storage account to store my reports. -->

U kunt de Cloudyn-rapporten opslaan in de Cloudyn-portal, Azure storage of buckets voor AWS-opslag. Uw rapporten opslaan in de Cloudyn-portal is gratis. Echter, uw rapporten opslaan in uw cloudserviceprovider opslag is optioneel en worden extra kosten in rekening gebracht. Dit artikel helpt u bij het configureren van Azure storage-accounts en Amazon Web Services (AWS) opslag buckets voor het opslaan van uw rapporten.

## <a name="prerequisites"></a>Vereisten

U moet een Azure storage-account of een bucket van Amazon opslag hebben.

Als u geen Azure storage-account hebt, moet u er een maken. Zie [een opslag account maken](../storage/common/storage-quickstart-create-account.md)voor meer informatie over het maken van een Azure-opslag account.

Als u een AWS geen bucket simple storage-service (S3), moet u er een maken. Zie [een Bucket maken](https://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html)voor meer informatie over het maken van een S3-Bucket.

## <a name="configure-your-azure-storage-account"></a>Uw Azure storage-account configureren

Configureert u is Azure storage voor gebruik door Cloudyn vrij eenvoudig. Verzamelen van gegevens over de storage-account en kopieer deze in de Cloudyn-portal.

1. Meld u aan bij Azure Portal op https://portal.azure.com.
2. Klik op **alle services**, selecteer **opslag accounts**, blader naar het opslag account dat u wilt gebruiken en selecteer vervolgens het account.
3. Klik op de pagina opslag account onder **instellingen**op **toegangs toetsen**.
4. Kopieer de **naam van uw opslag account** en de **verbindings reeks** onder Key1.  
   ![Kopieer de naam van het opslag account en de connection string](./media/storage-accounts/azure-storage-access-keys.png)  
5. Open de Cloudyn-portal vanuit Azure Portal of ga naar https://azure.cloudyn.com en meld u aan.
6. Klik op het symbool tandwiel en selecteer vervolgens **rapport opslag beheer**.
7. Klik op **nieuwe toevoegen +** en zorg ervoor dat Microsoft Azure is geselecteerd. Plak de naam van uw Azure Storage-account in het gebied **naam** . Plak uw **Connection String** in het bijbehorende gebied. Voer een container naam in en klik vervolgens op **Opslaan**.  
   ![plak de naam van het Azure Storage-account en de connection string in het vak een nieuw rapport toevoegen](./media/storage-accounts/azure-cloudyn-storage.png)

   Uw nieuwe rapport van Azure storage-vermelding wordt weergegeven in de lijst met storage-account.  
    ![Nieuwe Azure-rapport opslag vermelding in lijst](./media/storage-accounts/azure-storage-entry.png)


U kunt nu rapporten opslaan naar Azure storage. Klik in een rapport op **acties** en selecteer vervolgens **rapport plannen**. Het rapport een naam en voeg uw eigen URL of de automatisch gemaakte URL gebruiken. Selecteer **opslaan naar opslag** en selecteer vervolgens het opslag account. Voer een voorvoegsel dat wordt toegevoegd aan de naam van het rapport. Selecteer CSV of JSON-indeling en sla het rapport.

## <a name="configure-an-aws-storage-bucket"></a>Een bucket van AWS-opslag configureren

De Cloudyn maakt gebruik van bestaande AWS-referenties: gebruiker of rol, de rapporten opslaan naar de bucket. Als u de toegang wilt testen, probeert Cloudyn een klein tekst bestand op te slaan in de Bucket met de bestands naam _Check-Bucket-permission. txt_.

U opgeven de Cloudyn-rol of de gebruiker met de machtiging PutObject aan de bucket. Vervolgens gebruikt u een bestaande bucket of een nieuwe maken om op te slaan van rapporten. Ten slotte bepalen hoe u de opslagklasse beheren, levenscyclus regels instellen of verwijder onnodige bestanden.

###  <a name="assign-permissions-to-your-aws-user-or-role"></a>Machtigingen toewijzen aan uw AWS-gebruiker of rol

Wanneer u een nieuw beleid maakt, kunt u de exacte machtigingen die nodig zijn voor een rapport opslaan in een S3-bucket opgeven.

1. Meld u aan bij de AWS-console en selecteer **Services**.
2. Selecteer **iam** in de lijst met Services.
3. Selecteer **beleid** aan de linkerkant van de console en klik vervolgens op **beleid maken**.
4. Klik op het tabblad **JSON** .
5. Het volgende beleid kunt u een rapport opslaan in een S3-bucket. Kopieer en plak het volgende voor beeld van het beleid op het tabblad **JSON** . Vervang &lt;bucket&gt; door de Bucket naam.

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

6. Klik op **beleid controleren**.  
    ![AWS JSON-beleid met voorbeeld informatie](./media/storage-accounts/aws-policy.png)  
7. Typ een naam voor het beleid op de pagina van het beleid controleren. Bijvoorbeeld _CloudynSaveReport2S3_.
8. Klik op **beleid maken**.

### <a name="attach-the-policy-to-a-cloudyn-role-or-user-in-your-account"></a>Het beleid te koppelen aan een Cloudyn-functie of een gebruiker in uw account

Als u wilt koppelen het nieuwe beleid, de AWS-console openen en bewerken van de Cloudyn-rol of de gebruiker.

1. Meld u aan bij de AWS-console en selecteer **Services**en selecteer vervolgens **iam** in de lijst met Services.
2. Selecteer **rollen** of **gebruikers** aan de linkerkant van de console.

**Voor rollen:**

  1. Klik op de naam van uw Cloudyn-rol.
  2. Klik op het tabblad **machtigingen** op **beleid koppelen**.
  3. Zoek het beleid dat u hebt gemaakt, selecteer het en klik vervolgens op **beleid koppelen**.
    ![voorbeeld beleid dat aan uw Cloudyn-rol is gekoppeld](./media/storage-accounts/aws-attach-policy-role.png)

**Voor gebruikers:**

1. Selecteer de Cloudyn-gebruiker.
2. Klik op het tabblad **machtigingen** op **machtigingen toevoegen**.
3. Selecteer **bestaande beleids regels rechtstreeks koppelen**in de sectie **machtiging verlenen** .
4. Zoek het beleid dat u hebt gemaakt en selecteer dit en klik vervolgens op **volgende: controleren**.
5. Klik op de pagina machtigingen toevoegen aan rol naam op **machtigingen toevoegen**.  
    ![voorbeeld beleid dat is gekoppeld aan uw Cloudyn-gebruikers](./media/storage-accounts/aws-attach-policy-user.png)


### <a name="optional-set-permission-with-bucket-policy"></a>Optioneel: Machtiging met de bucket beleid instellen

U kunt ook de machtiging voor het maken van rapporten op uw S3-bucket op basis van beleid bucket instellen. In de klassieke S3-weergave:

1. Maak of Selecteer een bestaande bucket.
2. Selecteer het tabblad **machtigingen** en klik vervolgens op **Bucket beleid**.
3. Kopieer en plak het volgende voorbeeld van beleid. Vervang &lt;Bucket\_naam&gt; en &lt;Cloudyn\_principe&gt; met de ARN van uw Bucket. Vervang de informatie van de rol of de gebruiker die door Cloudyn worden gebruikt.

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

4. Klik in de editor voor het beleid voor Bucket op **Opslaan**.

### <a name="add-aws-report-storage-to-cloudyn"></a>Archiefopslag van AWS-rapport toevoegen aan Cloudyn

1. Open de Cloudyn-portal vanuit Azure Portal of ga naar https://azure.cloudyn.com en meld u aan.
2. Klik op het symbool tandwiel en selecteer vervolgens **rapport opslag beheer**.
3. Klik op **nieuwe toevoegen +** en zorg ervoor dat AWS is geselecteerd.
4. Selecteer een bucket-account en de opslag. De naam van de bucket van AWS-opslag wordt automatisch ingevuld.  
    ![Van de voorbeeldinformatie in het toevoegen van een nieuw rapport opslag vak](./media/storage-accounts/aws-cloudyn-storage.png)  
5. Klik op **Opslaan** en vervolgens op **OK**.

    Uw nieuwe AWS rapport opslag vermelding wordt weergegeven in de lijst met storage-account.  
    ![Nieuwe AWS rapport opslag vermelding weergegeven in de lijst met storage-account](./media/storage-accounts/aws-storage-entry.png)


U kunt nu rapporten opslaan naar Azure storage. Klik in een rapport op **acties** en selecteer vervolgens **rapport plannen**. Het rapport een naam en voeg uw eigen URL of de automatisch gemaakte URL gebruiken. Selecteer **opslaan naar opslag** en selecteer vervolgens het opslag account. Voer een voorvoegsel dat wordt toegevoegd aan de naam van het rapport. Selecteer CSV of JSON-indeling en sla het rapport.

## <a name="next-steps"></a>Volgende stappen

- Bekijk [inzicht](understanding-cost-reports.md) in de Cloudyn-rapporten voor meer informatie over de basis structuur en functies van Cloudyn-rapporten.
