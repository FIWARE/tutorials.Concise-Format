# Concise NGSI-LD[<img src="https://img.shields.io/badge/NGSI-LD-d6604d.svg" width="90"  align="left" />](https://www.etsi.org/deliver/etsi_gs/CIM/001_099/009/01.07.01_60/gs_cim009v010701p.pdf)[<img src="https://fiware.github.io/tutorials.Concise-Format/img/fiware.png" align="left" width="162">](https://www.fiware.org/)<br/>

[![FIWARE Core Context Management](https://nexus.lab.fiware.org/repository/raw/public/badges/chapters/core.svg)](https://github.com/FIWARE/catalogue/blob/master/core/README.md)
[![License: MIT](https://img.shields.io/github/license/fiware/tutorials.Concise-Format.svg)](https://opensource.org/licenses/MIT)
[![Support badge](https://img.shields.io/badge/tag-fiware-orange.svg?logo=stackoverflow)](https://stackoverflow.com/questions/tagged/fiware)
<br/> [![JSON LD](https://img.shields.io/badge/JSON--LD-1.1-f06f38.svg)](https://w3c.github.io/json-ld-syntax/)
[![Documentation](https://img.shields.io/readthedocs/fiware-tutorials.svg)](https://fiware-tutorials.rtfd.io)

このチュートリアルでは、簡潔な NGSI-LD 形式 (Concise NGSI-LD format) を紹介し、その使用法を示し、簡潔な NGSI-LD
ペイロードと正規化された NGSI-LD ペイロードの違いについて説明します。

チュートリアルでは全体で [cUrl](https://ec.haxx.se/) コマンドを使用しますが、 
[Postman documentation](https://fiware.github.io/tutorials.Concise-Format/ngsi-ld.html)
ドキュメントとしても利用できます。

[![Run in Postman](https://run.pstmn.io/button.svg)](https://app.getpostman.com/run-collection/d24facc3c430bb5d5aaf)
[![Open in Gitpod](https://gitpod.io/button/open-in-gitpod.svg)](https://gitpod.io/#https://github.com/FIWARE/tutorials.Concise-Format/tree/NGSI-LD)

## コンテンツ

<details>
<summary><strong>詳細</strong></summary>

-   [簡潔な NGSI-LD ペイロード](#concise-ngsi-ld-payloads)
    -   [NGSI-LD ペイロード](#ngsi-ld-payloads)
        -   [正規化された NGSI-LD](#normalized-ngsi-ld)
        -   [簡略化された NGSI-LD](#simplified-ngsi-ld)
        -   [簡潔な NGSI-LD](#concise-ngsi-ld)
-   [アーキテクチャ](#architecture)
-   [前提条件](#prerequisites)
    -   [Docker と DockerCompose](#docker-and-docker-compose)
    -   [Cygwin for Windows](#cygwin-for-windows)
-   [起動](#start-up)
-   [簡潔な NGSI-LD 操作](#concise-ngsi-ld-operations)
    -   [作成操作](#create-operations)
        -   [新しいデータ・エンティティを作成](#create-a-new-data-entity)
        -   [新しい属性を作成](#create-new-attributes)
        -   [新しいデータ・エンティティまたは属性のバッチ作成](#batch-create-new-data-entities-or-attributes)
        -   [新しいデータ・エンティティのバッチ作成/上書き](#batch-createoverwrite-new-data-entities)
    -   [読み取り操作](#read-operations)
        -   [フィルタリング](#filtering)
        -   [データ・エンティティの読み取り (簡潔)](#read-a-data-entity-concise)
        -   [データ・エンティティから属性の読み取り](#read-an-attribute-from-a-data-entity)
        -   [データ・エンティティの読み取り (簡潔)](#read-a-data-entity-concise-1)
        -   [データ・エンティティから複数の属性値の読み取り](#read-multiple-attributes-values-from-a-data-entity)
        -   [すべてのデータ・エンティティを一覧表示 (簡潔)](#list-all-data-entities-concise)
        -   [すべてのデータ・エンティティを一覧表示 (フィルタリング)](#list-all-data-entities-filtered)
        -   [ID でデータ・エンティティをフィルタリング](#filter-data-entities-by-id)
        -   [GeoJSON としてデータを返す](#returning-data-as-geojson)
    -   [更新操作](#update-operations)
        -   [属性値の値を上書き](#overwrite-the-value-of-an-attribute-value)
        -   [データ・エンティティの複数の属性を上書き](#overwrite-multiple-attributes-of-a-data-entity)
        -   [複数のデータ・エンティティの属性をバッチ更新](#batch-update-attributes-of-multiple-data-entities)
        -   [エンティティ・データのバッチ置換](#batch-replace-entity-data)
    -   [簡潔なサブスクリプションの設定](#setting-up-concise-subscriptions)
        -   [簡潔な通知](#concise-notification)
        -   [簡潔な GeoJSON 通知](#concise-geojson-notification)

</details>

<a name="concise-ngsi-ld-payloads"></a>

# 簡潔な NGSI-LD ペイロード (Concise NGSI-LD Payloads)

> "To speak much is one thing; to speak to the point another!"
>
> — Sophocles, Oedipus at Colonus

NGSI-LD API は、複数の形式でコンテキスト・データを生成するための柔軟なメカニズムです。これは、"正規化" (normalized)
と "キー・バリュー" (key-values) の両方のペア形式が作成された最初の入門チュートリアルで示されました。デフォルトの
詳細なデータ形式は、いわゆる "正規化された" NGSI-LD であり、すべてのプロパティは `"type": "Property"` とによって
すべてのリレーションシップは `"type": "Relationship"` によって定義されます。これらのキーワード (`type`, `Property`
と `Relationship`) は、厳密に定義された JSON-LD 用語であり、すべてのリクエストで提供されるコア @context にあります。

<a name="ngsi-ld-payloads"></a>

## NGSI-LD ペイロード

<a name="normalized-ngsi-ld"></a>

### 正規化された NGSI-LD (Normalized NGSI-LD)

@context と JSON-LD キーワードの定義により、マシンにはペイロード形式を完全に理解するためのすべてのツールが提供される
ため、完全な "正規化" 形式はデータ交換に最適です。レスポンスは、各エンティティの完全な現在の状態を返します。ペイロード
には、Properties-of-Properties, Properties-of-Relationships や `observedAt` や `unitCode` などの他の標準メタデータ用語
などのサブ属性がすべて含まれます。さらに、正規化されたペイロードは非常に規則的で解析可能であり、そのような操作が必要な
場合は、関連する `value` 要素に簡単に減らすことができます。ただし、正規化された形式では、マシンが表現されたデータを
完全に理解できるように、ペイロード全体で `"type": "Property"`などの共通の定義属性を繰り返し提供する必要があります。

#### `options=normalized` 使用して正規化された NGSI-LD

```json
{
    "@context": [
        "https://fiware.github.io/tutorials.Step-by-Step/example.jsonld",
        "https://uri.etsi.org/ngsi-ld/v1/ngsi-ld-core-context-v1.6.jsonld"
    ],
    "id": "urn:nsgi-ld:Beatle:John_Lennon",
    "type": "Beatle",
    "age": { "type": "Property", "value": 40, "unitCode": "ANN" },
    "name": { "type": "Property", "value": "John Lennon" },
    "born": { "type": "Property", "value": "1940-10-09" },
    "spouse": {
        "type": "Relationship",
        "object": "urn:nsgi-ld:Person:Cynthia_Lennon"
    },
    "location": {
        "type": "GeoProperty",
        "value": {
            "type": "Point",
            "coordinates": [-73.975, 40.775556]
        }
    }
}
```

[**JSON-LD Playground**](https://tinyurl.com/4nw9z83m) をオープンします。

<a name="simplified-ngsi-ld"></a>

### 簡略化された NGSI-LD (Simplified NGSI-LD)

"キー・バリュー" (key-values) のペア形式は、正規化された形式の使用とは対照的です。これは、属性の第1レベルの値のみに
純粋に集中する単純化されたバージョンです。ペイロードは通常のままですが、はるかに短く、要領を得ており、すべての情報
がリクエストによって返されるわけではありません。たとえば、`unitCode` や `observedAt` などの第2レベルの属性は
ペイロードに返されません。

#### `options=keyValues` を使用して簡略化されたNGSI-LD

```json
{
    "@context": [
        "https://fiware.github.io/tutorials.Step-by-Step/example.jsonld",
        "https://uri.etsi.org/ngsi-ld/v1/ngsi-ld-core-context-v1.6.jsonld"
    ],
    "id": "urn:nsgi-ld:Beatle:John_Lennon",
    "name": "John Lennon",
    "born": "1940-10-09",
    "spouse": "urn:nsgi-ld:Person:Cynthia_Lennon",
    "age": 40,
    "location": {
        "type": "Point",
        "coordinates": [-73.975, 40.775556]
    }
}
```

[**JSON-LD Playground**](https://tinyurl.com/2p93h8p6) をオープンします。

この key-values ペイロードは、公式 [JSON-LD サイト](https://json-ld.org/)のフロントページに表示される単純な
JSON-LD ペイロードと一致します。

正規化された NGSI-LD 形式と key-values の NGSI-LD 形式はどちらも有効な JSON-LD ですが、キー値形式は不可逆で
あるため、最近まで、NGSI-LD Context Broker へのすべての更新は正規化された形式を使用して行う必要があります。

<a name="concise-ngsi-ld"></a>

### 簡潔な NGSI-LD (Concise NGSI-LD)

API を使いやすくし、開発者の負担を軽減するために、NGSI-LD は、ペイロード内のすべてのコンテキスト・データを提供する
中間の "簡潔な" (concise) 形式を受け入れるようになり、`"type": "Property"` を繰り返し追加する冗長性を排除しています。
簡潔な表現は、正規化された表現の簡潔でロスレスな形式であり、冗長な "type" メンバが省略され、次のルールが適用されます。

-   それ以上のサブ属性のないすべての **Property** は、プロパティ値のみで表されます
-   さらにサブ属性を含むすべての **Property** は、value key-value のペアで表されます
-   それ以上のサブ属性のないすべての **GeoProperty** は、GeoProperty の GeoJSON 表現のみで表されます
-   さらにサブ属性を含むすべての **GeoProperty** は、value key-value のペアで表されます
-   すべての **LanguageProperty** は、`languageMap` key-value のペアによって定義されます
-   すべての **Relationship** は、`object` key-value のペアによって定義されます

#### Concise NGSI-LD using `options=concise`

```json
{
    "@context": [
        "https://fiware.github.io/tutorials.Step-by-Step/example.jsonld",
        "https://uri.etsi.org/ngsi-ld/v1/ngsi-ld-core-context-v1.6.jsonld"
    ],
    "id": "urn:nsgi-ld:Beatle:John_Lennon",
    "name": "John Lennon",
    "born": "1940-10-09",
    "spouse": {
        "object": "urn:nsgi-ld:Person:Cynthia_Lennon"
    },
    "age": { "value": 40, "unitCode": "ANN" },
    "location": {
        "type": "Point",
        "coordinates": [-73.975, 40.775556]
    }
}
```

[**JSON-LD Playground**](https://tinyurl.com/32shtpp6) をオープンします。

上記のペイロードから、(正規化のような) 簡潔な形式も、`unitCode` などの Properties-of-Properties が含まれているため
ロスレスであることがわかります (たとえば、年齢属性の単位は、UN/CEFACT コード `ANN` に続く年数であることは明らかです)。
また、**Properties** と **Relationships** を明確に区別します (**Relationships**には常にオブジェクトがあるため)。

要約すると、すべての NGSI-LD 形式は、構造化され、明確に定義されたペイロードを提供しますが、"正規化された" (normalized)
形式は冗長でロスレスであり、"キー・バリュー" (key-values) は短くて損失があり、3番目の形式の "簡潔" (concise) は、2つの
間のギャップを埋めるために設計された2次の中間ロスレス形式です。

#### デバイス・モニタ

このチュートリアルの目的のために、一連のダミーの農業用 IoT デバイスが作成され、Context Broker に接続されます。使用
されているアーキテクチャとプロトコルの詳細は、
[IoT センサ・チュートリアル](https://github.com/FIWARE/tutorials.IoT-Sensors/tree/NGSI-LD)にあります。各デバイスの
状態は、 UltraLight デバイス・モニタの Web ページは次の場所にあります: `http://localhost:3000/device/monitor`

![FIWARE Monitor](https://fiware.github.io/tutorials.Concise-Format/img/farm-devices.png)

![](https://fiware.github.io/tutorials.Concise-Format/img/history-graphs.png)

<a name="architecture"></a>

# アーキテクチャ

このアプリケーションは、[以前のチュートリアル](https://github.com/FIWARE/tutorials.IoT-Agent/)で作成したコンポーネント
とダミーの IoT デバイスに基づいて構築されています。[Orion Context Broker](https://fiware-orion.readthedocs.io/en/latest/)
と [IoT Agent for Ultralight 2.0](https://fiware-iotagent-ul.readthedocs.io/en/latest/) の2つの FIWARE コンポーネントを
使用します。

したがって、アーキテクチャ全体は次の要素で構成されます:

-   **FIWARE Generic Enablers**:

    -   [Orion Context Broker](https://fiware-orion.readthedocs.io/en/latest/) は、
        [NGSI-LD](https://forge.etsi.org/swagger/ui/?url=https://forge.etsi.org/rep/NGSI-LD/NGSI-LD/raw/master/spec/updated/generated/full_api.json)
        を使用してリクエストを受信します
    -   FIWARE [IoT Agent for UltraLight 2.0](https://fiware-iotagent-ul.readthedocs.io/en/latest/) は、
        [NGSI-LD](https://forge.etsi.org/swagger/ui/?url=https://forge.etsi.org/rep/NGSI-LD/NGSI-LD/raw/master/spec/updated/generated/full_api.json)
        を使用してサウスバウンド・リクエストを受信し、それらをデバイスの
        [UltraLight 2.0](https://fiware-iotagent-ul.readthedocs.io/en/latest/usermanual/index.html#user-programmers-manual)
       コマンドに変換します

-   [MongoDB](https://www.mongodb.com/) データベース:

    -   **Orion Context Broker** が、データ・エンティティ、サブスクリプション、レジストレーションなどの
        コンテキスト・データの情報を保持するために使用します
    -   **IoT Agent** が、デバイスの URLs やキーなどのデバイス情報を保持するために使用します

-   HTTP **Web-Server** は、システム内のコンテキストエンティティを定義する静的な `@context`ファイルを提供します

-   **チュートリアル・アプリケーション**は、以下のことを行います:
    -   HTTP を介して実行される
        [UltraLight 2.0](https://fiware-iotagent-ul.readthedocs.io/en/latest/usermanual/index.html#user-programmers-manual)
        プロトコルを使用して、ダミーの[農業 IoT デバイス](https://github.com/FIWARE/tutorials.IoT-Sensors/tree/NGSI-LD)
        のセットとして機能します

要素間のすべての相互作用は HTTP リクエストによって開始されるため、エンティティをコンテナ化して、公開されたポートから
実行できます。

全体的なアーキテクチャは以下のとおりです:

![](https://fiware.github.io/tutorials.Concise-Format/img/architecture.png)

必要な構成情報は、関連する `docker-compose.yml` ファイルの services セクションにあります。
これは前のチュートリアルで説明されています。

<a name="prerequisites"></a>

# 前提条件

<a name="docker-and-docker-compose"></a>

## Docker と DockerCompose

物事を単純にするために、両方のコンポーネントが [Docker](https://www.docker.com) を使用して実行されます。**Docker**
は、さまざまコンポーネントをそれぞれの環境に分離することを可能にするコンテナ・テクノロジです。

-   Docker を Windows にインストールするには
    、[こちら](https://docs.docker.com/docker-for-windows/)の手順に従ってくださ
    い
-   Docker を Mac にインストールするには
    、[こちら](https://docs.docker.com/docker-for-mac/)の手順に従ってください
-   Docker を Linux にインストールするには
    、[こちら](https://docs.docker.com/install/)の手順に従ってください

**Docker Compose** は、マルチコンテナ Docker アプリケーションを定義して実行するためのツールです。
[YAML file](https://raw.githubusercontent.com/FIWARE/tutorials.Concise-Format/NGSI-LD/docker-compose.yml)
ファイルは、アプリケーションのために必要なサービスを構成するために使用します。つまり、すべてのコンテナ・サービスは
1 つのコマンドで呼び出すことができます。Docker Compose は、デフォルトで Docker for Windows と Docker for Mac
の一部としてインストールされますが、Linux ユーザは[ここ](https://docs.docker.com/compose/install/)
に記載されている手順に従う必要があります。

次のコマンドを使用して、現在の **Docker** バージョンと **Docker Compose** バージョンを確認できます:

```console
docker-compose -v
docker version
```

Docker version 20.10 以降および Docker Compose 1.29 以降を使用していることを確認し、必要に応じてアップグレード
してください。

<a name="cygwin-for-windows"></a>

## Cygwin for Windows

シンプルな bash スクリプトを使用してサービスを開始します。Windows ユーザは [cygwin](http://www.cygwin.com/)
をダウンロードして、Windows 上の Linux ディストリビューションと同様のコマンドライン機能を提供する必要があります。

<a name="start-up"></a>

# 起動

開始する前に、必要な Docker イメージをローカルで取得またはビルドしたことを確認する必要があります。
以下のコマンドを実行して、リポジトリのクローンを作成し、必要なイメージを作成してください:

```console
git clone https://github.com/FIWARE/tutorials.Concise-Format.git
cd tutorials.Concise-Format
git checkout NGSI-LD

./services create
```

その後、リポジトリ内で提供される [services](https://github.com/FIWARE/tutorials.Concise-Format/blob/NGSI-LD/services)
Bash script を実行することにより、コマンドラインからすべてのサービスを初期化できます:

```console
./services [orion|scorpio|stellio]
```

> :information_source: **注:** クリーンアップしてやり直す場合は、次のコマンドを実行してください
>
> ```console
> ./services stop
> ```

---

<a name="concise-ngsi-ld-operations"></a>

# 簡潔な NGSI-LD 操作 (Concise NGSI-LD Operations)

正規化された NGSI-LD ペイロードを使用する Context Broker 操作は、簡潔なペイロードを使用してトリガーすることもできます。

<a name="create-operations"></a>

## 作成操作

作成操作は、HTTP POST にマップします。

-   `/ngsi-ld/v1/entities` エンドポイントは、新しいエンティティを作成するために使用されます
-   `/ngsi-ld/v1/entities/<entity-id>/attrs` エンドポイントは、新しい属性を追加するために使用されます

新しく作成されたエンティティには、`id` 属性と `type` 属性、および有効な `@context` 定義が必要です。 他のすべての属性は
オプションであり、モデル化されるシステムによって異なります。ただし、追加の属性が存在する場合は、簡潔な **Property** を
`value` 内にカプセル化する必要があります。**Relationship** を追加する場合は、`object` 内にカプセル化する必要があります。

レスポンスは、操作が成功した場合は **201 - Created**、操作が失敗した場合は **409 - Conflict** になります。

<a name="create-a-new-data-entity"></a>

### 新しいデータ・エンティティを作成

この例では、新しい **TemperatureSensor** エンティティをコンテキストに追加します。

#### :one: リクエスト:

```console
curl -iX POST 'http://localhost:1026/ngsi-ld/v1/entities/' \
-H 'Content-Type: application/json' \
-H 'Link: <http://context/ngsi-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"' \
--data-raw '{
      "id": "urn:ngsi-ld:TemperatureSensor:001",
      "type": "TemperatureSensor",
      "category": "sensor",
      "temperature": {
            "value": 25,
            "unitCode": "CEL"
      }
}'
```

`/ngsi-ld/v1/entities` エンドポイントに POST リクエストを行うことで、新しいエンティティを追加できます。`category`
サブ属性がないため、`value` 要素を必要としないことに注意してください 。

いつものように、エンティティがコンテキストにすでに存在する場合、リクエストは失敗します。

#### :two: リクエスト:

GET リクエストを行うことで、コンテキスト内で新しい **TemperatureSensor** が見つかるかどうかを確認できます。これにより、
完全に正規化された形式が返されます:

```console
curl -L -X GET 'http://localhost:1026/ngsi-ld/v1/entities/urn:ngsi-ld:TemperatureSensor:001' \
-H 'Link: <http://context/ngsi-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"'
```

<a name="create-new-attributes"></a>

### 新しい属性を作成

この例では、新しい `batteryLevel` プロパティと `controlledAsset` リレーションシップを `id=urn:ngsi-ld:TemperatureSensor:001`
の id をもつ既存の **TemperatureSensor** エンティティに追加します。

#### :three: リクエスト:

```console
curl -iX POST 'http://localhost:1026/ngsi-ld/v1/entities/urn:ngsi-ld:TemperatureSensor:001/attrs' \
-H 'Content-Type: application/json' \
-H 'Link: <http://context/ngsi-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"' \
--data-raw '{
       "batteryLevel": {
            "value": 0.8,
            "unitCode": "C62"
      },
      "controlledAsset": {
            "object": "urn:ngsi-ld:Building:barn002"
      }
}'
```

`/ngsi-ld/v1/entities/<entity>/attrs` エンドポイントに POST リクエストを行うことで、新しい属性を追加できます。

ペイロードは、示されているように属性名と値を保持する JSON オブジェクトで構成されている必要があります。

追加のサブ属性を持つすべての **Property** 属性には、`value` が関連付けられている必要があります。すべての **Relationship**
属性には、別のエンティティの URN を保持する `object` が関連付けられている必要があります。`unitCode` などの明確に定義された
一般的なメタデータ要素は文字列として提供できます。他のすべてのメタデータは、独自の `type` 属性と `value` 属性を持つ JSON
オブジェクトとして渡す必要があります。

同じ `id` を使用する後続のリクエストは、コンテキスト内の属性の値を更新します。

#### :four: リクエスト:

GET リクエストを行うことで、コンテキスト内で新しい **TemperatureSensor** が見つかるかどうかを確認できます。

```console
curl -L -X GET 'http://localhost:1026/ngsi-ld/v1/entities/urn:ngsi-ld:TemperatureSensor:001' \
-H 'Link: <http://context/ngsi-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"'
```

ご覧のとおり、エンティティに2つの追加属性 (`batteryLevel` と `ControlledAsset`) が追加されています。これらの属性は、
**Device** モデルの一部として `@context` で定義されているため、短い名前を使用して読み取ることができます。

<a name="batch-create-new-data-entities-or-attributes"></a>

### 新しいデータ・エンティティまたは属性のバッチ作成

この例では、便利なバッチ処理エンドポイントを使用して、3つの新しい **TemperatureSensor** エンティティをコンテキストに
追加します。バッチ作成は `/ngsi-ld/v1/entityOperations/create` エンドポイントを使用します。

#### :five: リクエスト:

```console
curl -iX POST 'http://localhost:1026/ngsi-ld/v1/entityOperations/create' \
-H 'Content-Type: application/json' \
-H 'Link: <http://context/ngsi-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"' \
-H 'Accept: application/ld+json' \
--data-raw '[
    {
      "id": "urn:ngsi-ld:TemperatureSensor:002",
      "type": "TemperatureSensor",
      "category": ["sensor"],
      "temperature": {
            "value": 20,
            "unitCode": "CEL"
      }
    },
    {
      "id": "urn:ngsi-ld:TemperatureSensor:003",
      "type": "TemperatureSensor",
      "category":  ["sensor" , "actuator"],
      "temperature": {
            "value": 2,
            "unitCode": "CEL"
      }
    },
     {
      "id": "urn:ngsi-ld:TemperatureSensor:004",
      "type": "TemperatureSensor",
      "category": {
            "type": "Property",
            "value": "sensor"
      },
      "temperature": {
            "type": "Property",
            "value": 100,
            "unitCode": "CEL"
      }
    }
]'
```

`"type": "Property"` をオプションで追加して簡潔なペイロードを作成でき、フォーマットは引き続き認識されることがわかります。
これは、正規化されたペイロードが自動的に有効で簡潔なペイロードであることを意味します。JSON-LD には既存の制約があるため、
NGSI-LD を使用してアレイを追加する場合は注意が必要です。事実上、1つのエントリ `"category": ["sensor"]` の配列と、単純な
文字列値 `"category": "sensor"` の間に違いはありません。 さらに、アレイ内の順序は維持されません。

> **注意:** NGSI-LD では、順序付けられた配列値をJSONリテラルとしてエンコードできます。
> `"category" : {"@type": "@json", "@value":[1,2,3]}`.

属性のいずれかがコンテキストにすでに存在する場合、リクエストは失敗します。レスポンスは、成功したアクションと失敗の理由
(発生した場合) を強調表示します。

```jsonld
{
    "@context": "http://context/ngsi-context.jsonld",
    "success": [
        "urn:ngsi-ld:TemperatureSensor:002",
        "urn:ngsi-ld:TemperatureSensor:003",
        "urn:ngsi-ld:TemperatureSensor:004"
    ],
    "errors": []
}
```

<a name="batch-createoverwrite-new-data-entities"></a>

### 新しいデータ・エンティティのバッチ作成/上書き

この例では、便利なバッチ処理エンドポイントを使用して、コンテキスト内の2つの **TemperatureSensor** エンティティを追加
または修正します。

-   エンティティがすでに存在する場合、リクエストはそのエンティティの属性を更新します
-   エンティティが存在しない場合は、新しいエンティティが作成されます

#### :six: リクエスト:

```console
curl -iX POST 'http://localhost:1026/ngsi-ld/v1/entityOperations/upsert' \
-H 'Content-Type: application/json' \
-H 'Link: <http://context/ngsi-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"' \
-H 'Accept: application/ld+json' \
--data-raw '[
    {
      "id": "urn:ngsi-ld:TemperatureSensor:002",
      "type": "TemperatureSensor",
      "category": "sensor",
      "temperature": {
            "value": 21,
            "unitCode": "CEL"
      }
    },
    {
      "id": "urn:ngsi-ld:TemperatureSensor:003",
      "type": "TemperatureSensor",
      "category":  "sensor",
      "temperature": {
            "type": "Property",
            "value": 27,
            "unitCode": "CEL"
      }
    }
]'
```

作成/上書きのバッチ処理では、`/ngsi-ld/v1/entityOperations/upsert` エンドポイントが使用されます。

同じデータ (つまり、同じエンティティと `actionType=append`) を含む後続のリクエストも成功し、コンテキストの状態は
変更されません。ただし、`modifiedAt` メタデータは修正されます。

<a name="read-operations"></a>

## 読み取り操作

-   `/ngsi-ld/v1/entities` エンドポイントはエンティティの一覧表示に使用されます
-   `/ngsi-ld/v1/entities/<entity>` エンドポイントは、単一のエンティティの詳細を取得するために使用されます

読み取り操作の場合、`Link` ヘッダに `@context` を指定する必要があります。

<a name="filtering"></a>

### フィルタリング

-   `options` パラメータ (`attrs` パラメータと組み合わせて) を使用して、返されたフィールドをフィルタリングできます
-   `q` パラメータを使用して、返されたエンティティをフィルタリングできます

<a name="read-a-data-entity-concise"></a>

### データ・エンティティの読み取り (簡潔)

この例では、既知の `id` の既存の **TemperatureSensor** エンティティの状態を読み取り、簡潔な形式で返します。

#### :seven: リクエスト:

```console
curl -G -iX GET 'http://localhost:1026/ngsi-ld/v1/entities/urn:ngsi-ld:TemperatureSensor:001' \
-H 'Link: <http://context/ngsi-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"' \
-d 'options=concise,sysAttrs'
```

#### レスポンス:

TemperatureSensor `urn:ngsi-ld:TemperatureSensor:001` は _concise_ NGSI-LD として返されます。`options=sysAttrs`
であるため、追加のメタデータが返されます。デフォルトでは、`@context` がペイロード本体に返されます (ただし、
`Accept:application/json` が設定されている場合、コンテンツ・ネゴシエーションのために移動される可能性があります)。
完全なレスポンスを以下に示します:

```jsonld
{
    "@context": "http://context/ngsi-context.jsonld",
    "id": "urn:ngsi-ld:TemperatureSensor:001",
    "type": "TemperatureSensor",
    "createdAt": "2020-08-27T14:33:06Z",
    "modifiedAt": "2020-08-27T14:33:10Z",
    "category": {
        "createdAt": "2020-08-27T14:33:06Z",
        "modifiedAt": "2020-08-27T14:33:06Z",
        "value": "sensor"
    },
    "temperature": {
        "createdAt": "2020-08-27T14:33:06Z",
        "modifiedAt": "2020-08-27T14:33:06Z",
        "value": 25,
        "unitCode": "CEL"
    },
    "batteryLevel": {
        "value": 0.8,
        "createdAt": "2020-08-27T14:33:10Z",
        "modifiedAt": "2020-08-27T14:33:10Z",
        "unitCode": "C62"
    },
    "controlledAsset": {
        "object": "urn:ngsi-ld:Building:barn002",
        "createdAt": "2020-08-27T14:33:10Z",
        "modifiedAt": "2020-08-27T14:33:10Z"
    }
}
```

`/ngsi-ld/v1/entities/<entity>` エンドポイントに対して GET リクエストを行うことで、個々のコンテキスト・データ・
エンティティを取得できます。

<a name="read-an-attribute-from-a-data-entity"></a>

### データ・エンティティから属性の読み取り

この例では、既知の `id` を持つ既存の **TemperatureSensor** エンティティから単一の属性 (`temperature`)
の値を読み取ります。

#### :eight: リクエスト:

```console
curl -G -iX GET 'http://localhost:1026/ngsi-ld/v1/entities/urn:ngsi-ld:TemperatureSensor:001' \
-H 'Link: <http://context/ngsi-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"' \
-d 'attrs=temperature'
```

#### レスポンス:

センサ `urn:ngsi-ld:TemperatureSensor:001` は25°Cで読み取ります。レスポンスを以下に示します:

```jsonld
{
    "@context": "http://context/ngsi-context.jsonld",
    "id": "urn:ngsi-ld:TemperatureSensor:001",
    "type": "TemperatureSensor",
    "temperature": {
        "value": 25,
        "unitCode": "CEL"
    }
}
```

`options=concise` が使用されたため、これは `unitCode` などのメタデータを含むレスポンスですが `"type": "Property"`
は含まれませんコンテキスト・データは、`/ngsi-ld/v1/entities/<entity-id>` に GET リクエストを行い、コンマ区切りの
リストを使用して `attrs` を選択して取得できます。

<a name="read-a-data-entity-concise-1"></a>

### データ・エンティティの読み取り (簡潔)

この例では、既知の `id` を持つ既存の **TemperatureSensor** エンティティのコンテキストから簡潔な NGSI-LD
形式を読み取ります。

#### :nine: リクエスト:

```console
curl -G -iX GET 'http://localhost:1026/ngsi-ld/v1/entities/urn:ngsi-ld:TemperatureSensor:001' \
-H 'Link: <http://context/json-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"' \
-H 'Accept: application/json' \
-d 'options=concise'
```

#### レスポンス:

センサ `urn:ngsi-ld:TemperatureSensor:001` は25°Cで読み取ります。レスポンスを以下に示します:

```json
{
    "id": "urn:ngsi-ld:TemperatureSensor:001",
    "type": "TemperatureSensor",
    "category": "sensor",
    "temperature": {
        "value": 25,
        "unitCode": "CEL"
    },
    "batteryLevel": {
        "value": 0.8,
        "unitCode": "C62"
    },
    "controlledAsset": {
        "object": "urn:ngsi-ld:Building:barn002"
    }
}
```

レスポンスには、`urn:ngsi-ld:TemperatureSensor:001` のすべての属性を含むエンティティからのコンテキスト・データの
フィルタリングされていないリストが含まれています。`Accept: application/json` が設定されているため、ペイロード本体
には `@context` 属性が含まれていません。

`options=concise` パラメータを `attrs` パラメータと組み合わせて、キーとバリューのペアの限定されたセットを取得します。

<a name="read-multiple-attributes-values-from-a-data-entity"></a>

### データ・エンティティから複数の属性値の読み取り

この例では、既知の `id` を持つ既存の **TemperatureSensor** エンティティのコンテキストから2つの属性 (`category`
と `temperature`) の値を読み取ります。

#### :one::zero: リクエスト:

```console
curl -G -iX GET 'http://localhost:1026/ngsi-ld/v1/entities/urn:ngsi-ld:TemperatureSensor:001' \
-H 'Link: <http://context/json-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"' \
-H 'Accept: application/json' \
-d 'options=concise' \
-d 'attrs=category,temperature'
```

#### レスポンス:

センサ `urn:ngsi-ld:TemperatureSensor:001` は25°Cで読み取っています。レスポンスを以下に示します:

```json
{
    "id": "urn:ngsi-ld:TemperatureSensor:001",
    "type": "TemperatureSensor",
    "category": "sensor",
    "temperature": {
        "value": 25,
        "unitCode": "CEL"
    }
}
```

`options=concise` パラメータと `attrs` パラメータを組み合わせて、値のリストを返します。

<a name="list-all-data-entities-concise"></a>

### すべてのデータ・エンティティを一覧表示 (簡潔)

この例では、すべての **TemperatureSensor** エンティティの完全なコンテキストを一覧表示します。

#### :one::one: リクエスト:

```console
curl -G -iX GET 'http://localhost:1026/ngsi-ld/v1/entities/' \
-H 'Link: <http://context/ngsi-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"' \
-d 'type=TemperatureSensor' \
-d 'options=concise'
```

#### レスポンス:

起動時にコンテキストが空でしたが、作成操作によって4つの **TemperatureSensor** エンティティが追加されたため、完全な
コンテキストに4つのセンサが含まれるようになります。

```jsonld
[
    {
        "@context": "http://context/ngsi-context.jsonld",
        "id": "urn:ngsi-ld:TemperatureSensor:004",
        "type": "TemperatureSensor",
        "category":  "sensor",
        "temperature": {
            "value": 100,
            "unitCode": "CEL"
        }
    },
    {
        "@context": "http://context/ngsi-context.jsonld",
        "id": "urn:ngsi-ld:TemperatureSensor:002",
        "type": "TemperatureSensor",
        "category": "sensor",
        "temperature": {
            "value": 21,
            "unitCode": "CEL"
        }
    },
    {
        "@context": "http://context/ngsi-context.jsonld",
        "id": "urn:ngsi-ld:TemperatureSensor:003",
        "type": "TemperatureSensor",
        "category": "sensor",
        "temperature": {
            "type": "Property",
            "value": 27,
            "unitCode": "CEL"
        }
    },
    {
        "@context": "http://context/ngsi-context.jsonld",
        "id": "urn:ngsi-ld:TemperatureSensor:001",
        "type": "TemperatureSensor",
        "batteryLevel": {
            "value": 0.8,
            "unitCode": "C62"
        },
        "category": "sensor",
        "controlledAsset": {
            "object": "urn:ngsi-ld:Building:barn002"
        },
        "temperature": {
            "value": 25,
            "unitCode": "CEL"
        }
    }
]
```

<a name="list-all-data-entities-filtered"></a>

### すべてのデータ・エンティティを一覧表示 (フィルタリング)

この例では、すべての **TemperatureSensor** エンティティの `temperature` 属性を簡潔な形式で一覧表示します。

#### :one::two: リクエスト:

```console
curl -G -iX GET 'http://localhost:1026/ngsi-ld/v1/entities/' \
-H 'Link: <http://context/json-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"' \
-H 'Accept: application/json' \
-d 'type=TemperatureSensor' \
-d 'options=concise' \
-d 'attrs=temperature'
```

#### レスポンス:

完全なコンテキストには4つのセンサが含まれ、ランダムな順序で返されます:

```json
[
    {
        "id": "urn:ngsi-ld:TemperatureSensor:004",
        "type": "TemperatureSensor",
        "temperature": {
            "value": 100,
            "unitCode": "CEL"
        }
    },
    {
        "id": "urn:ngsi-ld:TemperatureSensor:002",
        "type": "TemperatureSensor",
        "temperature": {
            "value": 21,
            "unitCode": "CEL"
        }
    },
    {
        "id": "urn:ngsi-ld:TemperatureSensor:003",
        "type": "TemperatureSensor",
        "temperature": {
            "value": 27,
            "unitCode": "CEL"
        }
    },
    {
        "id": "urn:ngsi-ld:TemperatureSensor:001",
        "type": "TemperatureSensor",
        "temperature": {
            "value": 25,
            "unitCode": "CEL"
        }
    }
]
```

指定されたエンティティ・タイプのコンテキスト・データは、`/ngsi-ld/v1/entity/` エンドポイントに GET リクエストを行い、
`type` パラメータを指定して、これを `options=keyValues` パラメータおよび キー値を取得するための `attrs` パラメータ
と組み合わせることで取得できます。

<a name="filter-data-entities-by-id"></a>

### ID でデータ・エンティティをフィルタリング

この例では、`id` によって選択された2つの **TemperatureSensor** エンティティから選択されたデータを一覧表示します。
すべての `id` は一意である必要があるため、このリクエストには `type` は必要ありません。`id` でフィルタリングするには、
カンマ区切りのリストにエントリを追加します。

#### :one::three: リクエスト:

```console
curl -G -iX GET 'http://localhost:1026/ngsi-ld/v1/entities/'' \
-H 'Link: <http://context/json-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"' \
-H 'Accept: 'application/json' \
-d 'id=urn:ngsi-ld:TemperatureSensor:001,urn:ngsi-ld:TemperatureSensor:002' \
-d 'attrs=temperature' \
-d 'options=concise'
```

#### レスポンス:

レスポンスは、選択されたエンティティから選択された属性の詳細を示します。

```json
[
    {
        "id": "urn:ngsi-ld:TemperatureSensor:002",
        "type": "TemperatureSensor",
        "temperature": {
            "value": 21,
            "unitCode": "CEL"
        }
    },
    {
        "id": "urn:ngsi-ld:TemperatureSensor:001",
        "type": "TemperatureSensor",
        "temperature": {
            "value": 25,
            "unitCode": "CEL"
        }
    }
]
```

<a name="returning-data-as-geojson"></a>

### GeoJSON としてデータを返す

簡潔な形式は、`Accept` ヘッダを `application/geo+json` に設定し、`options=concise` パラメータを設定することで
リクエストできる GeoJSON 形式でも使用できます。

#### :one::four: リクエスト:

```console
curl -G -iX GET 'http://localhost:1026//ngsi-ld/v1/entities/' \
-H 'Link: <http://context/json-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"' \
-H 'Accept: application/geo+json' \
-H 'NGSILD-Tenant: openiot' \
-d 'id=urn:ngsi-ld:Animal:pig010,urn:ngsi-ld:Animal:pig006' \
-d 'options=concise'
```

#### レスポンス:

レスポンスの詳細は、選択されたエンティティから選択された属性が GeoJSON 機能コレクションとして返されます。
`properties` セクションは、簡潔な形式でデータを保持します。

```json
{
    "type": "FeatureCollection",
    "features": [
        {
            "id": "urn:ngsi-ld:Animal:pig010",
            "type": "Feature",
            "properties": {
                "type": "Animal",
                "heartRate": {
                    "value": 71,
                    "providedBy": {
                        "object": "urn:ngsi-ld:Device:pig010"
                    },
                    "observedAt": "2022-03-01T15:49:57.039Z",
                    "unitCode": "5K"
                },
                "phenologicalCondition": "femaleAdult",
                "reproductiveCondition": "active",
                "name": "Carnation",
                "legalID": "M-sow010-Carnation",
                "sex": "female",
                "species": "pig",
                "location": {
                    "value": {
                        "type": "Point",
                        "coordinates": [13.346, 52.52]
                    },
                    "providedBy": {
                        "object": "urn:ngsi-ld:Device:pig010"
                    },
                    "observedAt": "2022-03-01T15:49:57.039Z"
                }
            },
            "@context": "http://context/json-context.jsonld",
            "geometry": {
                "type": "Point",
                "coordinates": [13.346, 52.52]
            }
        },
        {
            "id": "urn:ngsi-ld:Animal:pig006",
            "type": "Feature",
            "properties": {
                "type": "Animal",
                "heartRate": {
                    "value": 62,
                    "providedBy": {
                        "object": "urn:ngsi-ld:Device:pig006"
                    },
                    "observedAt": "2022-03-01T15:49:57.287Z",
                    "unitCode": "5K"
                },
                "phenologicalCondition": "femaleAdult",
                "reproductiveCondition": "inCalf",
                "name": "Peach",
                "legalID": "M-sow006-Peach",
                "sex": "female",
                "species": "pig",
                "location": {
                    "value": {
                        "type": "Point",
                        "coordinates": [13.347, 52.522]
                    },
                    "providedBy": {
                        "object": "urn:ngsi-ld:Device:pig006"
                    },
                    "observedAt": "2022-03-01T15:49:57.287Z"
                }
            },
            "@context": "http://context/json-context.jsonld",
            "geometry": {
                "type": "Point",
                "coordinates": [13.347, 52.522]
            }
        }
    ]
}
```

<a name="update-operations"></a>

## 更新操作

上書き操作はHTTPPATCHにマップされます:

-   `/ngsi-ld/v1/entities/<entity-id>/attrs/<attribute>` エンドポイントは属性を更新するために使用されます
-   `/ngsi-ld/v1/entities/<entity-id>/attrs` エンドポイントは、複数の属性を更新するために使用されます

<a name="overwrite-the-value-of-an-attribute-value"></a>

### 属性値の値を上書き

この例では、エンティティの `category` 属性の値を `id=urn:ngsi-ld:TemperatureSensor:001` で更新します。

#### :one::five: リクエスト:

```console
curl -iX PATCH 'http://localhost:1026/ngsi-ld/v1/entities/urn:ngsi-ld:TemperatureSensor:001/attrs/category' \
-H 'Content-Type: application/json' \
-H 'Link: <http://context/ngsi-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"' \
--data-raw '{
    "value": ["sensor", "actuator"]
}'
```

既存の属性値は、`/ngsi-ld/v1/entities/<entity-id>/attrs/<attribute>` エンドポイントに対して PATCH
リクエストを行うことで変更できます。適切な `@context` は `Link` ヘッダとして提供する必要があります。
正規化されたペイロードと簡潔なペイロードの唯一の違いは、`type` 属性が欠落していることです。

<a name="overwrite-multiple-attributes-of-a-data-entity"></a>

### データ・エンティティの複数の属性を上書き

この例では、エンティティの `category` 属性と `ControlledAsset` 属性の両方の値を `id=urn:ngsi-ld:TemperatureSensor:001`
で同時に更新します。

#### :one::six: リクエスト:

```console
curl -iX PATCH 'http://localhost:1026/ngsi-ld/v1/entities/urn:ngsi-ld:TemperatureSensor:001/attrs' \
-H 'Content-Type: application/json' \
-H 'Link: <http://context/ngsi-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"' \
--data-raw '{
      "category": {
            "value": [
                  "sensor",
                  "actuator"
            ]
      },
      "controlledAsset": {
            "object": "urn:ngsi-ld:Building:barn001"
      }
}'
```

<a name="batch-update-attributes-of-multiple-data-entities"></a>

### 複数のデータ・エンティティの属性をバッチ更新

この例では、便利なバッチ処理エンドポイントを使用して、既存のセンサを更新します。

#### :one::seven: リクエスト:

```console
curl -iX POST 'http://localhost:1026/ngsi-ld/v1/entityOperations/upsert?options=update' \
-H 'Content-Type: application/json' \
-H 'Link: <http://context/ngsi-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"' \
--data-raw '[
  {
    "id": "urn:ngsi-ld:TemperatureSensor:003",
    "type": "TemperatureSensor",
    "category": [
        "actuator",
        "sensor"
    ]
  },
  {
    "id": "urn:ngsi-ld:TemperatureSensor:004",
    "type": "TemperatureSensor",
    "category":  [
        "actuator",
        "sensor"
    ]
  }
]'
```

バッチ処理では、`/ngsi-ld/v1/entityOperations/upsert` エンドポイントを使用します。ペイロード本体は、更新する
エンティティと属性の配列を保持します。`options=update` パラメータは、既存の属性がすでに存在し、ペイロードに
含まれていない場合、それらを削除しないことを示します。

別の方法は、`/ngsi-ld/v1/entityOperations/update` エンドポイントを使用することです。`upsert` とは異なり、
`update` 操作はサイレントに新しいエンティティを作成しません - エンティティがまだ存在しない場合は失敗します。

<a name="batch-replace-entity-data"></a>

### エンティティ・データのバッチ置換

この例では、便利なバッチ処理エンドポイントを使用して、既存のセンサのエンティティ・データを置き換えます。

#### :one::eight: リクエスト:

```console
curl -iX POST 'http://localhost:1026/ngsi-ld/v1/entityOperations/update?options=replace' \
-H 'Content-Type: application/json' \
-H 'Link: <http://context/ngsi-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"' \
--data-raw '[
  {
    "id": "urn:ngsi-ld:TemperatureSensor:003",
    "type": "TemperatureSensor",
    "category":[
        "actuator",
        "sensor"
      ]
  },
  {
    "id": "urn:ngsi-ld:TemperatureSensor:004",
    "type": "TemperatureSensor",
    "temperature": {
      "value": 16,
      "unitCode": "CEL"
      "observedAt": "2022-03-01T15:00:00.000Z"
    }
  }
]'
```

バッチ処理では、`/ngsi-ld/v1/entityOperations/update` エンドポイントを使用し、ペイロードに、`options=replace`
パラメータを指定します。これは、既存のエンティティを上書きすることを意味します。新しいエンティティも作成する場合は、
`/ngsi-ld/v1/entityOperations/upsert` を使用することもできます。

<a name="setting-up-concise-subscriptions"></a>

## 簡潔なサブスクリプションの設定

<a name="concise-notification"></a>

### 簡潔な通知

サブスクリプションから通知を生成するときにも、簡潔な形式を使用できます。 図のように、`notification` 要素内に
`"format": "concise"`を設定するだけです:

#### :one::nine: リクエスト:

```console
curl -X POST 'http://{{orion}}/ngsi-ld/v1/subscriptions/' \
-H 'Content-Type: application/ld+json' \
-H 'NGSILD-Tenant: openiot' \
--data-raw '{
  "description": "Notify me of low feedstock on Farm:001",
  "type": "Subscription",
  "entities": [{"id": "urn:ngsi-ld:Animal:pig003", "type": "Animal"}],
  "notification": {
    "format": "concise",
    "endpoint": {
      "uri": "http://tutorial:3000/subscription/low-stock-farm001-ngsild",
      "accept": "application/geo+json"
    }
  },
   "@context": "http://context/ngsi-context.jsonld"
}'
```

次に、デバイス・モニタ `http://localhost:3000/app/farm/urn:ngsi-ld:Building:farm001` に移動し、納屋から干し草を
取り除きます。 最終的に、次のようにリクエストが `subscription/low-stock-farm001` に送信されます:

#### `http://localhost:3000/app/monitor`

#### サブスクリプション・ペイロード:

```json
{
    "id": "urn:ngsi-ld:Notification:6220b4e464f3729a8527f8a0",
    "type": "Notification",
    "subscriptionId": "urn:ngsi-ld:Subscription:6220b4a964f3729a8527f88c",
    "@context": "http://context/ngsi-context.jsonld",
    "notifiedAt": "2022-03-03T12:30:28.237Z",
    "data": [
        {
            "id": "urn:ngsi-ld:Animal:pig003",
            "type": "Animal",
            "heartRate": {
                "value": 67,
                "unitCode": "5K",
                "observedAt": "2022-03-03T12:30:27.000Z",
                "providedBy": {
                    "object": "urn:ngsi-ld:Device:pig003"
                }
            },
            "phenologicalCondition": "maleAdult",
            "reproductiveCondition": "active",
            "name": "Flamingo",
            "legalID": "M-boar003-Flamingo",
            "sex": "male",
            "species": "pig",
            "location": {
                "value": {
                    "type": "Point",
                    "coordinates": [13.357, 52.513]
                },
                "observedAt": "2022-03-03T12:30:27.000Z",
                "providedBy": {
                    "object": "urn:ngsi-ld:Device:pig003"
                }
            }
        }
    ]
}
```

<a name="concise-geojson-notification"></a>

### 簡潔な GeoJSON 通知

#### :two::zero: リクエスト:

`"accept": "application/geo+json"` 属性が設定されている場合は、GeoJSON 通知を送信することもできます。
これを `"format": "concise"` と組み合わせると、プロパティが簡潔な形式の `FeatureCollection` になります。

```console
curl -X POST 'http://{{orion}}/ngsi-ld/v1/subscriptions/' \
-H 'Content-Type: application/ld+json' \
-H 'NGSILD-Tenant: openiot' \
--data-raw '{
  "description": "Notify me of low feedstock on Farm:001",
  "type": "Subscription",
  "entities": [{"id": "urn:ngsi-ld:Animal:pig003", "type": "Animal"}],
  "notification": {
    "format": "concise",
    "endpoint": {
      "uri": "http://tutorial:3000/subscription/low-stock-farm001-ngsild",
      "accept": "application/geo+json"
    }
  },
   "@context": "http://context/ngsi-context.jsonld"
}'
```

#### Subscription Payload:

簡潔な GeoJSON 通知の結果を以下に示します。

```json
{
    "id": "urn:ngsi-ld:Notification:6220b50264f3729a8527f8ab",
    "type": "Notification",
    "subscriptionId": "urn:ngsi-ld:Subscription:6220b47764f3729a8527f886",
    "notifiedAt": "2022-03-03T12:30:58.294Z",
    "data": {
        "type": "FeatureCollection",
        "features": [
            {
                "id": "urn:ngsi-ld:Animal:pig003",
                "type": "Feature",
                "properties": {
                    "type": "Animal",
                    "heartRate": {
                        "value": 63,
                        "unitCode": "5K",
                        "observedAt": "2022-03-03T12:30:58.000Z",
                        "providedBy": {
                            "object": "urn:ngsi-ld:Device:pig003"
                        }
                    },
                    "phenologicalCondition": "maleAdult",
                    "reproductiveCondition": "active",
                    "name": "Flamingo",
                    "legalID": "M-boar003-Flamingo",
                    "sex": "male",
                    "species": "pig",
                    "location": {
                        "value": {
                            "type": "Point",
                            "coordinates": [13.357, 52.513]
                        },
                        "observedAt": "2022-03-03T12:30:58.000Z",
                        "providedBy": {
                            "object": "urn:ngsi-ld:Device:pig003"
                        }
                    }
                },
                "@context": "https://uri.etsi.org/ngsi-ld/v1/ngsi-ld-core-context-v1.6.jsonld",
                "geometry": {
                    "value": {
                        "type": "Point",
                        "coordinates": [13.357, 52.513]
                    },
                    "observedAt": "2022-03-03T12:30:58.000Z",
                    "providedBy": {
                        "object": "urn:ngsi-ld:Device:pig003"
                    }
                }
            }
        ]
    }
}
```

# 次のステップ

高度な機能を追加することで、アプリケーションに複雑さを加える方法を知りたいですか？ このシリーズの
[他のチュートリアル](https://www.letsfiware.jp/ngsi-ld-tutorials)を読むことで見つけることができます

---

## License

[MIT](LICENSE) © 2022-2023 FIWARE Foundation e.V.
