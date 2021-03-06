---
title: クイック スタート:Azure Active Directory (Azure AD) テナントのアプリケーションのプロパティを構成する
description: このクイック スタートでは、Azure portal を使用して、Azure Active Directory (Azure AD) テナントに登録されているアプリケーションを構成します。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 10/29/2019
ms.author: kenwith
ms.openlocfilehash: beb5c7262a5475f5c1535e120fcebe4c70838c7e
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/12/2020
ms.locfileid: "88135489"
---
# <a name="quickstart-configure-properties-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>クイック スタート:Azure Active Directory (Azure AD) テナントのアプリケーションのプロパティを構成する

前のクイックスタートでは、Azure Active Directory (Azure AD) テナントにアプリケーションを追加しました。 アプリケーションを追加すると、Azure AD テナントにアプリの ID プロバイダーであることを認識させることになります。 次に、アプリのプロパティをいくつか構成します。
 
## <a name="prerequisites"></a>前提条件

Azure AD テナントのアプリケーションのプロパティを構成するには、次のものが必要です。

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 次のいずれかのロール: グローバル管理者、クラウド アプリケーション管理者、アプリケーション管理者、またはサービス プリンシパルの所有者。
- 省略可能:[アプリの表示](view-applications-portal.md)の完了。
- 省略可能:[アプリの追加](add-application-portal.md)の完了。

>[!IMPORTANT]
>このクイックスタートの手順をテストする場合は、非運用環境を使用してください。

## <a name="configure-app-properties"></a>アプリのプロパティを構成する

Azure AD テナントへのアプリケーションの追加が完了すると、概要ページが表示されます。 既に追加されているアプリケーションを構成する場合は、最初のクイックスタートを参照してください。 テナントに追加されているアプリケーションを表示する手順が説明されています。 

アプリケーションのプロパティを編集するには:

1. Azure AD portal で、 **[エンタープライズ アプリケーション]** を選択します。 次に、構成するアプリケーションを探して選択します。
2. **[管理]** セクションで、 **[プロパティ]** を選択して編集用の **[プロパティ]** ペインを開きます。

    ![編集可能なアプリのプロパティが表示された [プロパティ] 画面のスクリーンショット。](media/add-application-portal/edit-properties.png)

3. ここで、構成で使用できるオプションについて理解しておきましょう。
    - **[ユーザーのサインインが有効になっていますか?]** は、アプリケーションに割り当てられているユーザーがサインインできるかどうかを決定します。
    - **[ユーザーの割り当てが必要ですか?]** は、アプリケーションに割り当てられていないユーザーがサインインできるかどうかを決定します。
    - **[ユーザーに表示しますか?]** は、アプリに割り当てられているユーザーの[アクセス パネル](https://myapps.microsoft.com)と Office 365 アプリ ランチャーにアプリを表示するかどうかを決定します (Office 365 または Microsoft 365 Web サイトの左上隅にあるワッフル メニューを参照してください)。
    
    > [!TIP]
    > ユーザーの割り当ては、ナビゲーションの **[ユーザーとグループ]** セクションで行われます。

    3 つのオプションは互いに独立して切り替えることができ、結果の動作が常に明らかであるとは限りません。 次の表を参考にしてください。
    
    | ユーザーのサインインが有効になっていますか? | ユーザーの割り当てが必要ですか? | ユーザーに表示しますか? | アプリに割り当てられているまたは割り当てられていないユーザーの動作 |
    |---|---|---|---|
    | はい | はい | はい | 割り当てられているユーザーはアプリを表示し、サインインできます。<br>割り当てられていないユーザーはアプリを表示できず、サインインすることもできません。 |
    | はい | はい | いいえ  | 割り当てられているユーザーはアプリを表示できませんが、サインインすることは可能です。<br>割り当てられていないユーザーはアプリを表示できず、サインインすることもできません。 |
    | はい | いいえ  | はい | 割り当てられているユーザーはアプリを表示し、サインインできます。<br>割り当てられていないユーザーはアプリを表示できませんが、サインインすることは可能です。 |
    | はい | いいえ  | いいえ  | 割り当てられているユーザーはアプリを表示できませんが、サインインすることは可能です。<br>割り当てられていないユーザーはアプリを表示できませんが、サインインすることは可能です。 |
    | いいえ  | はい | はい | 割り当てられているユーザーはアプリを表示できず、サインインすることもできません。<br>割り当てられていないユーザーはアプリを表示できず、サインインすることもできません。 |
    | いいえ  | はい | いいえ  | 割り当てられているユーザーはアプリを表示できず、サインインすることもできません。<br>割り当てられていないユーザーはアプリを表示できず、サインインすることもできません。 |
    | いいえ  | いいえ  | はい | 割り当てられているユーザーはアプリを表示できず、サインインすることもできません。<br>割り当てられていないユーザーはアプリを表示できず、サインインすることもできません。 |
    | いいえ  | いいえ  | いいえ  | 割り当てられているユーザーはアプリを表示できず、サインインすることもできません。<br>割り当てられていないユーザーはアプリを表示できず、サインインすることもできません。 |

4. 完了したら、 **[保存]** をクリックします。

## <a name="use-a-custom-logo"></a>カスタム ロゴを使用する

カスタム ロゴを使用するには:

1. 215 x 215 ピクセルのロゴを作成し、.png 形式で保存します。
2. Azure AD portal で、 **[エンタープライズ アプリケーション]** を選択します。 次に、構成するアプリケーションを探して選択します。
3. **[管理]** セクションで、 **[プロパティ]** を選択して編集用の **[プロパティ]** ペインを開きます。 
4. アイコンを選択してロゴをアップロードします。
5. 完了したら、 **[保存]** をクリックします。

    ![ロゴの変更方法を示す [プロパティ] 画面のスクリーンショット。](media/add-application-portal/change-logo.png)

   > [!NOTE]
   > この **[プロパティ]** ペインに表示されるサムネイルは、すぐには更新されません。 **[プロパティ]** ペインを閉じて再度開くと、更新されたアイコンが表示されます。


> [!TIP]
> Graph API を使用してアプリの管理を自動化できます。[Microsoft Graph API によるアプリ管理の自動化](https://docs.microsoft.com/graph/application-saml-sso-configure-api)に関するページを参照してください。


## <a name="clean-up-resources"></a>リソースをクリーンアップする

クイックスタート シリーズを続行しない場合は、アプリを削除してテスト テナントをクリーンアップすることを検討してください。 アプリの削除については、このシリーズの最後のクイックスタートである[アプリの削除](delete-application-portal.md)に関する記事で説明されています。

## <a name="next-steps"></a>次のステップ

次の記事に進み、アプリのシングル サインオンをセットアップする方法を学習してください。
> [!div class="nextstepaction"]
> [シングル サインオンを設定する](add-application-portal-setup-sso.md)
