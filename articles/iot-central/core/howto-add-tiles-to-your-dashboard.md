---
title: Azure IoT Central ダッシュボードにタイルを追加する | Microsoft Docs
description: 開発者として、既定の Azure IoT Central アプリケーション ダッシュボードにタイルを構成する方法を学習します。
author: Haley-Rowland
ms.author: harowl
ms.date: 05/27/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 892bdcc08bd19b92c8b3d32d2954583f80005e87
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2020
ms.locfileid: "84022886"
---
# <a name="configure-the-application-dashboard"></a>アプリケーション ダッシュボードの構成

**ダッシュボード**は、IoT Central アプリケーションに接続したときに最初に表示されるページです。 業界にフォーカスしたいずれかの[アプリケーション テンプレート](./concepts-app-templates.md)からアプリケーションを作成した場合、アプリケーションには事前定義されたダッシュボードがあり、それを基に作業を開始できます。 カスタム [アプリケーション テンプレート](./concepts-app-templates.md)からアプリケーションを作成した場合、使用を開始するためのヒントがダッシュボードに表示されます。

> [!TIP]
> ユーザーは、既定のアプリケーション ダッシュボードの他に、[複数のダッシュボードを作成](howto-create-personal-dashboards.md)できます。 これらのダッシュボードは、そのユーザー専用にすることも、アプリケーションのすべてのユーザー間で共有することもできます。  

## <a name="add-tiles"></a>タイルを追加する

次のスクリーンショットは、**カスタム アプリケーション** テンプレートから作成されたアプリケーションのダッシュボードを示しています。 現在のダッシュボードをカスタマイズする場合は **[編集]** を、カスタム個人用ダッシュボードまたは共有ダッシュボードを追加する場合は **[新規]** を選択します。

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/dashboard-sample-contoso.png" alt-text="カスタム アプリケーション テンプレートに基づくアプリケーションのダッシュボード":::

**[編集]** または **[新規]** を選択すると、ダッシュボードは "*編集*" モードになります。 **[ダッシュボードの編集]** パネルのツールを使用して、ダッシュボードへのタイルの追加、ダッシュボード上でのタイルのカスタマイズと削除を実行できます。 たとえば、**テレメトリ** タイルを追加して、1 台以上のデバイスによって報告された現在の温度を表示するには、次のようにします。

1. **[ダッシュボードの編集]** パネルで、 **[デバイス グループ]** を選択します。
1. **[デバイス]** ドロップダウンから、タイルに表示する 1 台以上のデバイスを選択します。 これで、デバイスから入手できるテレメトリ、プロパティ、およびコマンドが表示されます。
1. [テレメトリ] セクションで **[温度]** を選択した後、 **[タイルの追加]** を選択します。 これで、ダッシュボードにタイルが表示され、視覚化を変更したり、タイルのサイズを変更したり、タイルを構成したりすることができます。

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/device-details.png" alt-text="温度テレメトリ タイルをダッシュボードに追加する":::

ダッシュボードでのタイルの追加とカスタマイズが完了したら、 **[保存]** を選択します。

## <a name="customize-tiles"></a>タイルをカスタマイズする

ダッシュボード上のタイルをカスタマイズするには、ダッシュボードを編集モードにする必要があります。 使用できるカスタマイズ オプションは、[タイルの種類](#tile-types)によって異なります。

* タイルのルーラー アイコンを使用して、視覚化を変更できます。 視覚化には、折れ線グラフ、最後の既知の値、およびヒート マップが含まれます。

* 四角形アイコンを使用して、タイルのサイズを変更できます。

* 歯車アイコンを使用して、視覚化を構成できます。 たとえば、折れ線グラフの視覚化の場合、凡例と軸を表示することを選択し、プロットする時間の範囲を選択できます。

## <a name="tile-types"></a>タイルの種類

次の表では、ダッシュボードに追加できるさまざまな種類のタイルについて説明します。

| タイル             | 説明 |
| ---------------- | ----------- |
| Markdown         | マークダウン タイルは、マークダウンを使用してフォーマットされた見出しと説明テキストが表示されるクリック可能なタイルです。 URL には、アプリケーション内の別のページへの相対リンク、または外部サイトへの絶対リンクを指定できます。|
| Image            | 画像タイルは、カスタム画像を表示するタイルで、クリック可能にすることもできます。 URL には、アプリケーション内の別のページへの相対リンク、または外部サイトへの絶対リンクを指定できます。|
| Label            | ラベル タイルは、ダッシュボードにカスタム テキストを表示するためのタイルです。 テキストのサイズは選択できます。 ラベル タイルを使用すると、ダッシュボードに説明、連絡先の詳細、ヘルプなどの関連情報を追加できます。|
| Count            | カウント タイルには、デバイス グループ内のデバイスの数が表示されます。|
| マップ              | マップ タイルには、1 台以上のデバイスの場所が地図上に表示されます。 デバイスの場所の履歴を最大 100 地点表示することもできます。 たとえば、デバイスが過去 1 週間に存在していた場所のサンプル ルートを表示できます。|
| KPI              |  KPI タイルには、1 台以上のデバイスの一定期間にわたるテレメトリ値の集計値が表示されます。 たとえば、1 台以上のデバイスについて過去 1 時間の最高温度と最高気圧を表示するために使用できます。|
| 折れ線グラフ       | 折れ線グラフ タイルには、1 台以上のデバイスの一定期間にわたるテレメトリの集計値がプロットされます。 たとえば、1 台以上のデバイスについて過去 1 時間の平均温度と平均気圧をプロットした折れ線グラフを表示できます。|
| 横棒グラフ        | 横棒グラフ タイルには、1 台以上のデバイスの一定期間にわたるテレメトリの集計値がプロットされます。 たとえば、1 台以上のデバイスの過去 1 時間の平均温度と平均気圧を示す横棒グラフを表示できます。|
| 円グラフ        | 円グラフ タイルには、1 台以上のデバイスの一定期間にわたるテレメトリの集計値が表示されます。|
| [ヒート マップ]         | ヒート マップ タイルには、1 台以上のデバイスに関する情報が、色で表示されます。|
| 最後の既知の値 | 最後の既知の値タイルには、1 台以上のデバイスの最新のテレメトリ値が表示されます。 たとえば、このタイルを使用して、1 台以上のデバイスについて、最新の温度、気圧、および湿度の値を表示できます。 |
| イベント履歴    | イベント履歴タイルは、一定期間内に特定のデバイスで発生したイベントを表示するタイルです。 たとえば、1 台以上のデバイスの過去 1 時間のすべてのバルブ開閉イベントを表示するために使用できます。|
| プロパティ         |  プロパティ タイルには、1 台以上のデバイスのプロパティとクラウド プロパティの現在の値が表示されます。 たとえば、このタイルを使用して、デバイスの製造元やファームウェアのバージョンなどのデバイスのプロパティを表示できます。 |

現時点では、複数のデバイスをサポートするタイルに最大 10 台のデバイスを追加できます。

### <a name="customizing-visualizations"></a>視覚化のカスタマイズ

集計値を表示するタイルについては、 **[グラフの構成]** パネルでテレメトリの種類の横にある歯車アイコンを選択して集計を選択します。 [平均]、[合計]、[最大]、[最小]、および [カウント] から選択できます。

折れ線グラフ、横棒グラフ、および円グラフでは、異なるテレメトリ値の色をカスタマイズできます。 カスタマイズするテレメトリの横にあるパレット アイコンを選択します。

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/color-customization.png" alt-text="テレメトリ値の色を変更する":::

文字列プロパティやテレメトリ値を表示するタイルでは、テキストの表示方法を選択できます。 たとえば、デバイスで文字列プロパティに URL が格納されている場合は、クリック可能リンクとして表示できます。 URL が画像を参照している場合は、最後に認識された値またはプロパティ タイルで画像を表示できます。 文字列の表示方法を変更するには、タイルの構成で、テレメトリの種類またはプロパティの横にある歯車アイコンを選択します。

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/string-customization.png" alt-text="タイルに表示する文字列を変更する方法":::

## <a name="next-steps"></a>次のステップ

これで、Azure IoT Central の既定のアプリケーション ダッシュボードを構成する方法を習得したので、[個人用ダッシュボードを作成する方法を学習](howto-create-personal-dashboards.md)できます。
