---
title: "Azure IoT Hub での device-to-cloud 通信に関するガイダンス | Microsoft Docs"
description: "Azure IoT Hub 開発者ガイド - device-to-cloud メッセージ、報告されるプロパティ、またはファイルのアップロードを使用するタイミングに関するガイダンスです。"
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: 979136db-c92d-4288-870c-f305e8777bdd
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2016
ms.author: elioda
translationtype: Human Translation
ms.sourcegitcommit: 53f14e6fe115ed5f96d25b9ec5ab04abe23712d5
ms.openlocfilehash: 6d5608cd86e3f042a7d63bd4e43e75a06141dfab


---
# <a name="device-to-cloud-communications-guidance"></a>device-to-cloud 通信に関するガイダンス
デバイス アプリからバック エンドに情報を送信する場合、IoT Hub では、次の 3 つのオプションを公開します。

* [device-to-cloud (D2C) メッセージ][lnk-d2c]: 時系列のテレメトリとアラート用。
* [報告されるプロパティ][lnk-twins]: 使用できる機能、状態、実行時間の長いワークフローの状態などのデバイスの状態情報のレポート用 (例: 構成とソフトウェアの更新)。
* [ファイルのアップロード][lnk-fileupload]: 断続的に接続されるデバイスによってアップロードされたり、帯域幅を節約するために圧縮されているメディア ファイルと大容量のテレメトリ バッチ用。

さまざまな device-to-cloud 通信オプションの詳細な比較を次に示します。

|  | D2C メッセージ | 報告されるプロパティ | ファイルのアップロード |
| ---- | ------- | ---------- | ---- |
| シナリオ | 時系列のテレメトリとアラート (例: 5 分間隔で送信される 256 KB のセンサー データ バッチ)。 | 使用できる機能と状態 (例: デバイスの現在の接続モード (携帯電話または wifi))。 構成やソフトウェア更新などの実行時間の長いワークフローと同期します。 | メディア ファイル。 大規模な (通常は圧縮された) テレメトリ バッチ。 |
| 格納と取得 | IoT Hub によって、最大 7 日間、一時的に格納されます。 シーケンシャルな読み取りのみ可能です。 | IoT Hub によってデバイス ツインに格納されます。 [IoT Hub クエリ言語][lnk-query]を使用して取得できます。 | ユーザー指定の Azure Storage アカウントに格納されます。 |
| サイズ | 最大 256 KB のメッセージ。 | 報告されるプロパティの最大サイズは 8 KB。 | Azure Blob Storage によってサポートされるファイルの最大サイズ。 |
| 頻度 | 高。 詳細については、[IoT Hub の制限][lnk-quotas]に関するページを参照してください。 | 中。 詳細については、[IoT Hub の制限][lnk-quotas]に関するページを参照してください。 | 低。 詳細については、[IoT Hub の制限][lnk-quotas]に関するページを参照してください。 |
| プロトコル | すべてのプロトコルで使用できます。 | 現時点では、MQTT を使用する場合のみ使用できます。 | どのプロトコルでも使用できますが、デバイス上に HTTP が必要です。 |

> [!NOTE]
> アプリケーションは、情報を時系列のテレメトリまたはアラートとして送信することと、デバイス ツインで使用できるようにすることの両方を要求できます。 このような場合は、デバイス アプリが D2C メッセージの送信とプロパティ変更の報告の両方を実行するか、バックエンドがメッセージの受信時に情報をデバイス ツインのタグに格納できます。 D2C メッセージのほうがデバイス ツインの更新よりもはるかにスループットが高いため、すべての D2C メッセージに対してデバイス ツインを更新することは避けることをお勧めします。
> 
> 

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-fileupload]: iot-hub-devguide-file-upload.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages



<!--HONumber=Nov16_HO5-->


