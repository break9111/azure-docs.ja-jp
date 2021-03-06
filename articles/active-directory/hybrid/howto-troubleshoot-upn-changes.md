---
title: Azure のユーザー プリンシパル名 (UPN) の変更の計画とトラブルシューティング
description: UPN の変更に関する既知の問題と軽減策について説明します
services: active-directory
ms.service: active-directory
ms.subservice: hybrid
ms.topic: how-to
ms.date: 03/13/2020
ms.author: baselden
author: barbaraselden
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 885d30305ba2b186052e17b9b455b2248bca541b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85608519"
---
# <a name="plan-and-troubleshoot-user-principal-name-changes-in-azure-active-directory"></a>Azure Active Directory でのユーザー プリンシパル名の変更の計画とトラブルシューティング

ユーザー プリンシパル名 (UPN) は、ユーザー アカウントのインターネット通信標準である属性です。 UPN は、UPN プレフィックス (ユーザー アカウント名) と UPN サフィックス (DNS ドメイン名) とから成ります。 プレフィックスとサフィックスは、"@" 記号を使用して結合されます。 たとえば、「 someone@example.com 」のように入力します。 UPN は、ディレクトリ フォレスト内のすべてのセキュリティ プリンシパル オブジェクトの中で一意であることが必要です。 

**この記事では、ユーザー識別子として UPN を使用していることを前提としています。UPN の変更の計画と、UPN の変更によって発生する可能性がある問題からの復旧について説明します。**

> [!NOTE]
> UPN またはメール アドレスは値が変更される可能性があるため、開発者には変更不可の識別子としてユーザーの objectID を使用することをお勧めします。


## <a name="learn-about-upns-and-upn-changes"></a>UPN と UPN の変更について理解する
サインイン ページでは、必要な値が実際には UPN であるときに、ユーザーにメール アドレスの入力を求めることがよくあります。 そのため、プライマリ メール アドレスが変更されるたびに、ユーザーの UPN を変更する必要があります。

ユーザーのプライマリ メール アドレスは、さまざまな理由で変更される可能性があります。

* 会社のブランド変更

* 従業員の異なる部署への異動 

* 合併と買収

* 従業員の名前の変更

### <a name="types-of-upn-changes"></a>UPN の変更の種類

プレフィックス、サフィックス、またはその両方を変更することによって、UPN を変更できます。

* **プレフィックスの変更**。

   *  たとえば、ユーザーの名前が変わった場合は、アカウント名を変更することがあります。  
‎BSimon@contoso.com から BJohnson@contoso.com

   * また、プレフィックスに関する会社の標準が変更されることもあります。  
‎Bsimon@contoso.com から Britta.Simon@contoso.com

* **サフィックスの変更**。 <br>

    たとえば、ユーザーの部署が変わった場合は、ドメインを変更することがあります。 

   * Britta.Simon@contosolabs.com に対して Britta.Simon@contoso.com を行います <br>
     または<br>
    * Britta.Simon@labs.contoso.com に対して Britta.Simon@corp.contoso.com を行います 

プライマリ メール アドレスが更新されるたびに、ユーザーの UPN を変更することをお勧めします。

Active Directory から Azure AD への初期同期中に、ユーザーのメール アドレスが UPN と同じであることを確認します。

### <a name="upns-in-active-directory"></a>Active Directory での UPN

Active Directory では、既定の UPN サフィックスは、ユーザー アカウントを作成したドメインの DNS 名です。 ほとんどの場合、これはインターネット上で企業ドメインとして登録されたドメイン名です。 contoso.com ドメインにユーザー アカウントを作成すると、既定の UPN は次のようになります

username@contoso.com

 ただし、Active Directory ドメインと信頼関係を使用して、[複数の UPN サフィックスを追加する](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)こともできます。 

たとえば、labs.contoso.com を追加し、ユーザーの UPN とメール アドレスにそれを反映させることができます。 この場合、次のようになります

username@labs.contoso.com.

>[!IMPORTANT]
> [Active Directory でサフィックスを変更](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)した場合は、一致するカスタム ドメイン名を [Azure AD に追加して検証する](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)必要があります。 

![検証済みドメインのスクリーンショット](./media/howto-troubleshoot-upn-changes/custom-domains.png)

### <a name="upns-in-azure-active-directory"></a>Azure Active Directory での UPN

ユーザーは、userPrincipalName 属性の値を使用して Azure AD にサインインします。 

オンプレミスの Active Directory と組み合わせて Azure AD を使用すると、ユーザー アカウントは Azure AD Connect サービスを使用して同期されます。 既定の場合、Azure AD Connect ウィザードではオンプレミスの Active Directory の userPrincipalName 属性が Azure AD の UPN として使用されます。 カスタム インストールでは、これを別の属性に変更できます。

1 人のユーザーまたは組織全体のユーザー プリンシパル名 (UPN) を更新するときのプロセスを定義しておくことが重要です。 

このドキュメントの既知の問題と回避策を参照してください。

ユーザー アカウントを Active Directory から Azure AD に同期するときは、Active Directory の UPN が Azure AD 内の検証済みドメインにマップされていることを確認します。

![検証済みドメインのスクリーンショット](./media/howto-troubleshoot-upn-changes/verified-domains.png)

userPrincipalName 属性の値が Azure AD の検証済みドメインに対応しない場合は、同期プロセスによってサフィックスが既定値 .onmicrosoft.com に置き換えられます。


### <a name="roll-out-bulk-upn-changes"></a>UPN の一括変更をロールアウトする

UPN の一括変更については、[パイロットのベスト プラクティス](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans)に従ってください。 また、すぐに解決できない問題が見つかった場合に UPN を元に戻すため、テスト済みのロールバック計画も用意します。 パイロットを実行したら、さまざまな組織の役割とアプリやデバイスの特定のセットが含まれる少数のユーザーのセットを対象にすることができます。

この最初のユーザーのサブセットを使用すると、ユーザーが変更の一部として期待する内容を把握できます。 この情報をユーザーのコミュニケーションに含めます。

通常の運用の一部として、個々のユーザーの UPN を変更する手順を定義します。 既知の問題と回避策に関するドキュメントを含むテスト済みの手順を用意することをお勧めします。

以下のセクションでは、UPN を変更するときに発生する可能性のある既知の問題と回避策について詳しく説明します。

## <a name="apps-known-issues-and-workarounds"></a>アプリの既知の問題と回避策

[サービスとしてのソフトウェア (SaaS)](https://azure.microsoft.com/overview/what-is-saas/) アプリケーションおよび基幹業務 (LoB) アプリケーションでは、多くの場合、UPN を利用して、ユーザーの検索や、ロールなどのユーザー プロファイル情報の格納が行われます。 ユーザーが初めてアプリにサインインするときにユーザー プロファイルを作成するために [Just-In-Time プロビジョニング](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning)を使用するアプリケーションは、UPN の変更によって影響を受ける可能性があります。

**既知の問題**<br>
ユーザーの UPN を変更すると、Azure AD ユーザーと、アプリケーションで作成されたユーザー プロファイルの間の関係が壊れる可能性があります。 アプリケーションで [Just-In-Time プロビジョニング](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning)を使用している場合は、新しいユーザー プロファイルが作成される可能性があります。 その場合、アプリケーション管理者は手動で変更を行って、この関係を修正する必要があります。

**回避策**<br>
[Azure AD の自動化されたユーザー プロビジョニング](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)を使用すると、サポートされているクラウド アプリケーションでのユーザー ID を自動的に作成、管理、削除できます。 アプリケーションで自動化されたユーザー プロビジョニングを構成すると、アプリケーションの UPN が自動的に更新されます。 プログレッシブ ロールアウトの一部としてアプリケーションをテストし、UPN の変更によって影響を受けないことを検証します。
開発者は、[アプリケーションに SCIM サポートを追加](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups)して、Azure Active Directory からの自動ユーザー プロビジョニングを有効にすることを検討してください。 

## <a name="managed-devices-known-issues-and-workarounds"></a>マネージド デバイスに関する既知の問題と回避策

[Azure AD にデバイスを設定する](https://docs.microsoft.com/azure/active-directory/devices/overview)ことにより、クラウドとオンプレミス リソースの間でシングル サインオン (SSO) を使用して、ユーザーの生産性を最大化できます。

### <a name="azure-ad-joined-devices"></a>Azure AD 参加済みデバイス

[Azure AD 参加](https://docs.microsoft.com/azure/active-directory/devices/concept-azure-ad-join)デバイスは Azure AD に直接参加し、ユーザーは組織の ID を使用してデバイスにサインインできます。

**既知の問題** <br>
認証を Azure AD に依存するアプリケーションで、シングル サインオンに関する問題が発生する可能性があります。

**解決策** <br>
この項で説明した問題は、2020 年 5 月の Windows 10 更新プログラム (2004) で修正されました。

**回避策** <br>
UPN の変更が Azure AD に同期されるのに十分な時間を確保します。 新しい UPN が Azure AD ポータルに反映されたことを確認した後、[その他のユーザー] タイルを選択して新しい UPN でサインインするようにユーザーに依頼します。 [PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0) を使用して確認することもできます。 新しい UPN でサインインした後も、古い UPN への参照により、[職場または学校へのアクセス] という Windows の設定が表示される場合があります。

![検証済みドメインのスクリーンショット](./media/howto-troubleshoot-upn-changes/other-user.png)


### <a name="hybrid-azure-ad-joined-devices"></a>ハイブリッド Azure AD 参加済みデバイス

[ハイブリッド Azure AD 参加済み](https://docs.microsoft.com/azure/active-directory/devices/concept-azure-ad-join-hybrid)デバイスは、Active Directory と Azure AD に参加しています。 環境にオンプレミスの Active Directory フットプリントがあり、Azure AD によって提供される機能も利用したい場合は、Hybrid Azure AD 参加済みデバイスを実装できます。

**既知の問題** 

Windows 10 の Hybrid Azure AD 参加済みデバイスでは、予期しない再起動とアクセスの問題が発生する可能性があります。

新しい UPN が Azure AD に同期される前にユーザーが Windows にサインインした場合、または既存の Windows セッションを使い続けた場合、ハイブリッド参加済みデバイスを使用してリソースにアクセスするように条件付きアクセスが設定されていると、認証に Azure AD を使用するアプリケーションでシングル サインオンに関する問題が発生する可能性があります。 

さらに、次のメッセージが表示され、1 分後に再起動が強制されます。 

"PC は 1 分で自動的に再起動されます。 Windows で問題が発生したため、再起動する必要があります。 今すぐこのメッセージを閉じて、作業中のデータを保存してください。"

**解決策** <br>
この項で説明した問題は、2020 年 5 月の Windows 10 更新プログラム (2004) で修正されました。

**回避策** 

デバイスの Azure AD への参加を解除し、再起動する必要があります。 再起動後、デバイスは自動的に Azure AD に再び参加します。ユーザーは、[その他のユーザー] タイルを選択し、新しい UPN を使用してサインインする必要があります。 デバイスの Azure AD への参加を解除するには、コマンド プロンプトで次のコマンドを実行します。

**dsregcmd /leave**

Windows Hello for Business が使用されている場合、ユーザーは[再登録する](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-whfb-provision)必要があります。 Windows 7 および8.1 のデバイスは、UPN 変更後のこの問題による影響を受けません。


## <a name="microsoft-authenticator-known-issues-and-workarounds"></a>Microsoft Authenticator に関する既知の問題と回避策

組織では、組織のアプリケーションやデータへのサインインとアクセスに、[Microsoft Authenticator アプリ](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-overview)の使用が要求されている場合があります。 アプリにユーザー名が表示される場合でも、ユーザーが登録プロセスを完了するまで、アカウントは検証方法として機能するように設定されません。

[Microsoft Authenticator アプリ](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-overview)には、次の 4 つの主要な機能があります。

* プッシュ通知または確認コードを使用した多要素認証

* [ブローカー認証](https://docs.microsoft.com/azure/active-directory/develop/brokered-auth)を使用するアプリケーションにシングル サインオンを提供するための、iOS および Android デバイスでの認証ブローカーとしての機能

* Intune App Protection やデバイスの登録と管理などの他の機能に対する要件である、Azure AD へのデバイスの登録 (Workplace Join とも呼ばれます)

* MFA とデバイスの登録が必要な電話でのサインイン。

### <a name="multi-factor-authentication-with-android-devices"></a>Android デバイスでの Multi-Factor Authentication

Microsoft Authenticator アプリには、帯域外検証オプションが用意されています。 [Multi-Factor Authentication (MFA)](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) では、サインイン時にユーザーに自動音声通話または SMS 送信を行うのではなく、ユーザーのスマートフォンまたはタブレットの Microsoft Authenticator アプリにプッシュ通知が行われます。 ユーザーはアプリで [許可] をタップ (または、PIN または生体認証情報を入力して [認証] をタップ) するだけで、サインインを完了することができます。

**既知の問題** 

ユーザーの UPN を変更すると、ユーザー アカウントに古い UPN が引き続き表示され、通知が受信されない可能性があります。 [検証コード](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-faq)は引き続き機能します。

**回避策**

通知を受け取った場合は、通知を無視し、Authenticator アプリを開いて [通知の確認] オプションをタップし、MFA プロンプトを承認するよう、ユーザーに指示しします。 その後、アカウントに表示される UPN が更新されます。 更新された UPN が新しいアカウントとして表示される場合があることに注意してください。これは、Authenticator の他の機能が使用されているためです。 詳細については、この記事の他の既知の問題を参照してください。

### <a name="brokered-authentication"></a>ブローカー認証

Microsoft Authenticator のように、Android および iOS のブローカーには次の機能があります。

* シングル サインオン (SSO) - ユーザーがアプリケーションごとにサインインする必要がなくなります。

* デバイスの識別 - ブローカーでは、ワークプレースに参加したときにデバイスに作成されたデバイス証明書にアクセスします。

* アプリケーション ID の検証 - アプリケーションはブローカーを呼び出してリダイレクト URL を渡し、それがブローカーによって検証されます。

さらに、アプリケーションでは、[条件付きアクセス](https://docs.microsoft.com/azure/active-directory/conditional-access/)などのより高度な機能に参加し、[Microsoft Intune のシナリオ](https://docs.microsoft.com/azure/active-directory/develop/msal-net-use-brokers-with-xamarin-apps)をサポートすることができます。

**既知の問題**<br>
アプリケーションによって渡される login_hint とブローカーに格納されている UPN が一致しないために、ブローカーで支援されたサインインを使用する新しいアプリケーションでは、ユーザーにより対話的な認証プロンプトが提供されます。

**回避策** <br> ユーザーは、Microsoft Authenticator からアカウントを手動で削除し、ブローカーで支援されたアプリケーションから新しいサインインを開始する必要があります。 初期認証後に、アカウントが自動的に追加されます。

### <a name="device-registration"></a>デバイス登録

Microsoft Authenticator アプリでは、Azure AD へのデバイスの登録が行われます。 デバイス登録によりデバイスで Azure AD に対する認証を行うことができるようになり、デバイス登録は次のシナリオに対する要件です。

* Intune App Protection

* Intune デバイスの登録

* 電話によるサインイン

**既知の問題**<br>
UPN を変更すると、新しい UPN を使用する新しいアカウントが、Microsoft Authenticator アプリの一覧に表示されますが、古い UPN を使用するアカウントもまだ一覧に表示されます。 また、古い UPN は、アプリ設定の [デバイスの登録] セクションに表示されます。 [デバイスの登録] または依存するシナリオの通常の機能に変更はありません。

**回避策** <br> Microsoft Authenticator アプリで古い UPN に対するすべての参照を削除するには、Microsoft Authenticator から古いアカウントと新しいアカウントの両方を手動で削除し、MFA に再登録して、デバイスを再度参加させるよう、ユーザーに指示します。

### <a name="phone-sign-in"></a>電話によるサインイン

電話によるサインインを使うと、ユーザーはパスワードを使用せずに Azure AD にサインインできます。 電話によるサインインを有効にするには、ユーザーは Authenticator アプリを使用して MFA に登録した後、Authenticator で電話によるサインインを直接有効にする必要があります。 構成の一部として、デバイスは Azure AD に登録されます。

**既知の問題** <br>
ユーザーは通知を受け取らないため、電話によるサインインを使用できません。 ユーザーが [通知の確認] をタップすると、エラーが表示されます。

**回避策**<br>
ユーザーは、電話によるサインインが有効になっているアカウントでドロップダウン メニューを選択し、[電話によるサインインを無効にする] を選択する必要があります。 必要に応じて、電話によるサインインを再度有効にすることができます。

## <a name="security-key-fido2-known-issues-and-workarounds"></a>セキュリティ キー (FIDO2) に関する既知の問題と回避策

**既知の問題** <br>
複数のユーザーが同じキーに登録されている場合、サインイン画面のアカウント選択ページに古い UPN が表示されます。 セキュリティ キーを使用したサインインは、UPN の変更による影響を受けません。  

**回避策**<br>
古い UPN への参照を削除するには、ユーザーは[セキュリティ キーをリセットして再登録する](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key#known-issues)必要があります。

## <a name="onedrive-known-issues-and-workarounds"></a>OneDrive に関する既知の問題と回避策

OneDrive のユーザーは、UPN の変更後に問題が発生することがわかっています。 詳細については、「[UPN の変更が OneDrive の URL と OneDrive の機能に与える影響](https://docs.microsoft.com/onedrive/upn-changes)」を参照してください。

## <a name="next-steps"></a>次のステップ

次のリソースを参照してください。
* [Azure AD Connect:設計概念](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-design-concepts)

* [Azure AD の UserPrincipalName の設定](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-userprincipalname)

* [Microsoft ID プラットフォームの ID トークン](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
