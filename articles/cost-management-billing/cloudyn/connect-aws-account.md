---
title: Een Amazon Web Services-account verbinden met Cloudyn in Azure
description: Lees hoe u een Amazon Web Services-account kunt verbinden om kosten en gebruiksgegevens te bekijken in Cloudyn-rapporten.
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: benshy
ms.custom: seodec18
ROBOTS: NOINDEX
ms.openlocfilehash: 38e5e253c32a2f85e18c80bdefa7d3b640da2e50
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79464439"
---
# <a name="connect-an-amazon-web-services-account"></a>Een Amazon Web Services-account verbinden

U hebt twee opties om uw Amazon Web Services-account (AWS) te verbinden met Cloudyn. U kunt verbinding maken met een IAM-rol of met een alleen-lezen IAM-gebruikersaccount. De IAM-rol wordt aanbevolen omdat u hiermee toegang met gedefinieerde machtigingen kunt delegeren aan vertrouwde entiteiten. Voor de IAM-rol hoeft u geen sleutels voor langetermijntoegang te delen. Nadat u een AWS-account hebt verbonden met Cloudyn, zijn de kosten en gebruiksgegevens beschikbaar in Cloudyn-rapporten. In dit document worden beide opties besproken.

Zie [Identities (Users, Groups, and Roles)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html) (Identiteiten (gebruikers, groepen en rollen)) voor meer informatie over IAM-identiteiten van AWS.

Daarnaast schakelt u gedetailleerde factureringsrapporten van AWS in en slaat u de gegevens op in S3-bucket (Simple Storage Service) van AWS. Gedetailleerde factureringsrapporten bevatten factureringskosten met code-en resourcegegevens per uur. Door de rapporten op te slaan, kan Cloudyn ze ophalen uit uw bucket en de gegevens opnemen in de rapporten.

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]
## <a name="aws-role-based-access"></a>Toegang op basis van AWS-rol

De volgende secties begeleiden u bij het maken van een alleen-lezen IAM-rol om toegang te bieden tot Cloudyn.

### <a name="get-your-cloudyn-account-external-id"></a>Externe id van uw Cloudyn-account ophalen

De eerste stap is het ophalen van de unieke wachtwoordzin voor de verbinding uit de Cloudyn-Portal. Deze zin wordt in AWS gebruikt als de **External ID**.

1. Open de Cloudyn-portal vanuit Azure-portal of ga naar [https://azure.cloudyn.com](https://azure.cloudyn.com) en meld u aan.
2. Klik op het tandwiel en selecteer vervolgens **Cloud Accounts**.
3. Selecteer in Accounts Management het tabblad **AWS Accounts** en klik op **Add new +** .
4. Kopieer in het dialoogvenster **Add AWS Account** de waarde bij **External ID** en sla de waarde op voor gebruik in de stap voor het maken van een AWS-rol in de volgende sectie. De externe id is uniek voor uw account. In de volgende afbeelding is _Contoso_ gevolgd door een getal het voorbeeld van een externe id. Uw id wijkt hiervan af.  
    ![Externe id in het venster Add AWS Account](./media/connect-aws-account/external-id.png)

### <a name="add-aws-read-only-role-based-access"></a>Alleen-lezen toegang op basis van AWS-rol toevoegen

1. Meld u aan bij de AWS-console op [https://console.aws.amazon.com/iam/home](https://console.aws.amazon.com/iam/home) en selecteer **Roles**.
2. Klik op **Create role** en selecteer vervolgens **Another AWS account**.
3. Plak `432263259397` in het vak **Account ID**. Deze account-id is gekoppeld aan het account van Cloudyn voor gegevensverzameling dat door AWS is toegewezen aan de Cloudyn-service. Gebruik de exacte account-id die wordt weergegeven.
4. Selecteer naast **Options** **Require external ID**. Plak de unieke waarde die u eerder hebt gekopieerd uit het veld **External ID** in Cloudyn. Klik vervolgens op **Volgende: Permissions**.  
    ![plak de externe id vanuit Cloudyn op de pagina Create role](./media/connect-aws-account/create-role01.png)
5. Ga onder **Attach permissions policies** naar **Policy type**, typ `ReadOnlyAccess` in het zoekvak, selecteer **ReadOnlyAccess** en klik vervolgens op **Next: Review**.  
    ![selecteer alleen-lezen toegang in de lijst met beleidsnamen](./media/connect-aws-account/readonlyaccess.png)
6. Controleer op de pagina Review of uw selecties juist zijn en geef een naam op bij **Role name**. Gebruik bijvoorbeeld de naam *Azure-Cost-Mgt*. Geef een waarde op voor **Role description**. Typ bijvoorbeeld _Roltoewijzing voor Cloudyn_ en klik vervolgens op **Create role**.
7. Klik in de lijst **Roles** op de rol die u hebt gemaakt en kopieer de waarde van **Role ARN** van de pagina Summary. Gebruik de waarde voor Role ARN (Amazon Resource Name) later wanneer u uw configuratie registreert in Cloudyn.  
    ![kopieer de Role ARN op de pagina Summary](./media/connect-aws-account/role-arn.png)

### <a name="configure-aws-iam-role-access-in-cloudyn"></a>IAM-rol van AWS configureren in Cloudyn

1. Open de Cloudyn-portal vanuit de Azure-portal of ga naar https://azure.cloudyn.com/ en meld u aan.
2. Klik op het tandwiel en selecteer vervolgens **Cloud Accounts**.
3. Selecteer in Accounts Management het tabblad **AWS Accounts** en klik op **Add new +** .
4. Typ bij **Account Name** een naam voor het account.
5. Selecteer **IAM Role** naast **Access Type**.
6. Plak in het veld **Role ARN** de waarde die u eerder hebt gekopieerd en klik vervolgens op **Save**.  
    ![plak de Role ARN in het venster Add AWS Account](./media/connect-aws-account/add-aws-account-box.png)


Uw AWS-account wordt weergegeven in de lijst met accounts. De eigenaar-id die wordt weergegeven bij **Owner Id** komt overeen met de waarde voor de Role ARN. In de kolom **Account Status** moet een groen vinkje staan dat aangeeft dat Cloudyn toegang heeft tot uw AWS-account. Totdat u gedetailleerde AWS-facturering hebt ingeschakeld, wordt uw consolidatiestatus weergegeven als **Standalone**.

![status van AWS-account op de pagina Accounts Management](./media/connect-aws-account/aws-account-status01.png)

Cloudyn begint met het verzamelen van de gegevens en het invullen van rapporten. Vervolgens [schakelt u gedetailleerde AWS-facturering in](#enable-detailed-aws-billing).


## <a name="aws-user-based-access"></a>Toegang op basis van AWS-gebruiker

De volgende secties begeleiden u bij het maken van een alleen-lezen gebruiker om toegang te bieden tot Cloudyn.

### <a name="add-aws-read-only-user-based-access"></a>Alleen-lezen toegang op basis van AWS-gebruiker toevoegen

1. Meld u aan bij de AWS-console op [https://console.aws.amazon.com/iam/home](https://console.aws.amazon.com/iam/home) en selecteer **Users**.
2. Klik op **Add User**.
3. Typ een gebruikersnaam in het veld **User name**.
4. Selecteer bij **Access type** de optie **Programmatic access** en klik op **Next: Permissions**.  
    ![voer een gebruikersnaam in op de pagina Add user](./media/connect-aws-account/add-user01.png)
5. Selecteer **Attach existing policies directly** voor machtigingen.
6. Ga onder **Attach permissions policies** naar **Policy type**, typ `ReadOnlyAccess` in het zoekvak, selecteer **ReadOnlyAccess** en klik vervolgens op **Next: Review**.  
    ![selecteer ReadOnlyAccess om machtigingen voor de gebruiker in te stellen](./media/connect-aws-account/set-permission-for-user.png)
7. Controleer op de pagina Review of uw selecties juist zijn en klik vervolgens op **Create user**.
8. Op de pagina Complete worden de id voor toegangssleutel en geheime toegangssleutel weergegeven. U hebt deze gegevens nodig voor het configureren van de registratie in Cloudyn.
9. Klik op **Download .csv** en sla het bestand credentials.csv op een veilige locatie op.  
    ![klik op Download .csv om de referenties op te slaan](./media/connect-aws-account/download-csv.png)

### <a name="configure-aws-iam-user-based-access-in-cloudyn"></a>Toegang op basis van AWS IAM-gebruiker configureren in Cloudyn

1. Open de Cloudyn-portal vanuit Azure Portal of ga naar https://azure.cloudyn.com/ en meld u aan.
2. Klik op het tandwiel en selecteer vervolgens **Cloud Accounts**.
3. Selecteer in Accounts Management het tabblad **AWS Accounts** en klik op **Add new +** .
4. Typ een accountnaam bij **Account Name**.
5. Selecteer **IAM User** naast **Access Type**.
6. Plak bij **Access Key** de waarde voor **Access key ID** uit het bestand credentials.csv.
7. Plak bij **Secret Key** de waarde voor **Secret access key** uit het bestand credentials.csv en klik op **Save**.  

Uw AWS-account wordt weergegeven in de lijst met accounts. In de kolom **Account Status** moet nu een groen vinkje staan.

Cloudyn begint met het verzamelen van de gegevens en het invullen van rapporten. Vervolgens [schakelt u gedetailleerde AWS-facturering in](#enable-detailed-aws-billing).

## <a name="enable-detailed-aws-billing"></a>Gedetailleerde AWS-facturering inschakelen

Gebruik de volgende stappen om de ARN van uw AWS-rol te verkrijgen. U gebruikt de Role ARN om leesmachtigingen toe te kennen aan een facturerings-bucket.

1. Meld u aan bij de AWS-console op [https://console.aws.amazon.com](https://console.aws.amazon.com) en selecteer **Services**.
2. Typ *IAM*in het zoekvak bovenaan en selecteer die optie.
3. Selecteer **Roles** in het menu aan de linkerkant.
4. Selecteer in de lijst met rollen de rol die u hebt gemaakt voor toegang tot Cloudyn.
5. Klik op de pagina Roles Summary om de **Role ARN** te kopiëren. Sla de Role ARN op voor latere stappen.

### <a name="create-an-s3-bucket"></a>Een S3-bucket maken

U maakt een S3-bucket om gedetailleerde factureringsgegevens op te slaan.

1. Meld u aan bij de AWS-console op [https://console.aws.amazon.com](https://console.aws.amazon.com) en selecteer **Services**.
2. Typ *S3*in het zoekvak bovenaan en selecteer **S3**.
3. Klik op de pagina Amazon S3 op **Create bucket**.
4. Kies in de wizard Create bucket een naam en regio voor de bucket en klik vervolgens op **Next**.  
    ![voorbeeld van gegevens op de pagina Create bucket](./media/connect-aws-account/create-bucket.png)
5. Laat de standaardwaarden staan op de pagina **Properties** en klik vervolgens op **Next**.
6. Klik op de pagina Review op **Create bucket**. De lijst met buckets wordt weergegeven.
7. Klik op de bucket die u hebt gemaakt en selecteer het tabblad **Permissions** en selecteer vervolgens **Bucket Policy**. De editor voor bucket-beleid wordt geopend.
8. Kopieer het volgende JSON-voorbeeld en plak het in de editor voor bucket-beleid.
   - Vervang `<BillingBucketName>` door de naam van uw S3-bucket.
   - Vervang `<ReadOnlyUserOrRole>` door de rol of gebruikers-ARN die u eerder hebt gekopieerd.

   ```json
   {
      "Version": "2012-10-17",
      "Id": "Policy1426774604000",
      "Statement": [
          {
              "Sid": "Stmt1426774604000",
              "Effect": "Allow",
              "Principal": {
                  "AWS": "arn:aws:iam::386209384616:root"
              },
              "Action": [
                  "s3:GetBucketAcl",
                  "s3:GetBucketPolicy"
              ],
              "Resource": "arn:aws:s3:::<BillingBucketName>"
          },
          {
              "Sid": "Stmt1426774604001",
              "Effect": "Allow",
              "Principal": {
                  "AWS": "arn:aws:iam::386209384616:root"
              },
              "Action": "s3:PutObject",
              "Resource": "arn:aws:s3:::<BillingBucketName>/*"
          },
          {
              "Sid": "Stmt1426774604002",
              "Effect": "Allow",
              "Principal": {
                  "AWS": "<ReadOnlyUserOrRole>"
              },
              "Action": [
                  "s3:List*",
                  "s3:Get*"
              ],
              "Resource": "arn:aws:s3:::<BillingBucketName>/*"
          }
      ]
   }
   ```

9. Klik op **Opslaan**.  
    ![klik op Save in de editor voor bucket-beleid](./media/connect-aws-account/bucket-policy-editor.png)


### <a name="enable-aws-billing-reports"></a>AWS-factureringsrapporten inschakelen

Nadat u de S3-bucket hebt gemaakt en geconfigureerd, gaat u naar [Billing Preferences](https://console.aws.amazon.com/billing/home?#/preference) in de AWS-console.

1. Selecteer **Receive Billing Reports** op de pagina Preferences.
2. Voer onder **Receive Billing Reports**de naam in van de bucket die u hebt gemaakt en klik vervolgens op **Verify**.  
3. Selecteer alle vier de opties voor granulariteit en klik vervolgens op **Save preferences**.  
    ![granulariteit selecteren om rapporten in te schakelen](./media/connect-aws-account/enable-reports.png)

Cloudyn haalt gedetailleerde factureringsgegevens op uit uw S3-bucket en vult rapporten in nadat gedetailleerde facturering is ingeschakeld. Het kan tot 24 uur duren voordat gedetailleerde factureringsgegevens worden weergegeven in de Cloudyn-console. Wanneer gedetailleerde factureringsgegevens beschikbaar zijn, wordt de consolidatiestatus van uw account weergegeven als **Consolidated**. De accountstatus is dan **Completed**.

![consolidatiestatus op het tabblad AWS Accounts](./media/connect-aws-account/consolidated-status.png)

Voor sommige van de optimalisatierapporten zijn mogelijk enkele dagen aan gegevens nodig om een juiste steekproefgrootte te krijgen voor nauwkeurige aanbevelingen.

## <a name="next-steps"></a>Volgende stappen

- Als u meer wilt weten over Cloudyn, gaat u verder met de zelfstudie [Gebruik en kosten bekijken](tutorial-review-usage.md) voor Cloudyn.
