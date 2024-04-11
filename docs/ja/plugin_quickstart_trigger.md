# プラグイン(トリガー)

> v3.2.0より、「プラグイン(トリガー)」は非推奨になりました。  
今後は、[プラグイン(ボタン)](/ja/plugin_quickstart_button)、もしくは[プラグイン(イベント))](/ja/plugin_quickstart_event)での実装を行ってください。  


Exmentの画面上で特定の操作を行った場合に実行され、値の更新などの処理を行うことができます。  
もしくは、一覧画面もしくはフォーム画面にボタンを追加し、クリック時に処理を行うことができます。  


## トリガーの種類

| 名前 | 種類 | 説明 |
| ---- | ---- | ---- |
| saving | 保存直前 | データの保存直前に、処理が起動します。 |
| saved | 保存後 | データの保存後に、処理が起動します。 |
| grid_menubutton | 一覧画面のメニューボタン | データ一覧画面の上部にボタンを追加し、クリック時にイベントを発生させます。 |
| form_menubutton_show | データ詳細画面のメニューボタン | データ詳細画面の上部にボタンを追加し、クリック時にイベントを発生させます。 |
| form_menubutton_create | データ新規作成画面のメニューボタン | データの新規作成画面の上部にボタンを追加し、クリック時にイベントを発生させます。 |
| form_menubutton_edit | データ更新画面のメニューボタン | データの更新画面の上部にボタンを追加し、クリック時にイベントを発生させます。 |
| workflow_action_executing | ワークフロー実行直前 | ワークフロー実行の直前に、処理が起動します。 |
| workflow_action_executed | ワークフロー実行後 | ワークフロー実行後に、処理が起動します。 |
| notify_executing | 通知実行直前 | 通知実行の直前に、処理が起動します。 |
| notify_executed | 通知実行後 | 通知実行後に、処理が起動します。 |

## 作成方法

### config.json作成
- 以下のconfig.jsonファイルを作成します。  

~~~ json
{
    "plugin_name": "PluginDemoTrigger",
    "uuid": "fa7de170-992a-11e8-b568-0800200c9a66",
    "plugin_view_name": "Plugin Trigger",
    "description": "プラグインをアップロードするテストです。",
    "author": "(Your Name)",
    "version": "1.0.0",
    "plugin_type": "trigger",
    "event_triggers": "loaded,loading",

}
~~~

- plugin_nameは、半角英数で記入してください。
- uuidは、32文字列+ハイフンの、合計36文字の文字列です。プラグインを一意にするために使用します。  
以下のURLなどから、作成を行ってください。  
https://www.famkruithof.net/uuid/uuidgen
- plugin_typeは、triggerと記入してください。  
- event_triggersを最初から指定する場合、「トリガーの種類」の名称を、カンマ区切りで入力してください。
- target_tablesを最初から指定する場合、カスタムテーブルのテーブル名を、カンマ区切りで入力してください。


### PHPファイル作成
- 以下のようなPHPファイルを作成します。名前は「Plugin.php」としてください。

~~~ php
<?php
namespace App\Plugins\PluginDemoTrigger;

use Exceedone\Exment\Services\Plugin\PluginTriggerBase;
class Plugin extends PluginTriggerBase
{
    /**
     * Plugin Trigger
     */
    public function execute()
    {
        admin_toastr('Plugin calling');
        return true;
    }
}
~~~
- namespaceは、**App\Plugins\\(プラグイン名のパスカルケース)**としてください。[詳細はこちら](/ja/plugin_quickstart#プラグイン名のnamespace)

- プラグイン管理画面で登録した、トリガーの条件に合致した場合に、プラグインが呼び出され、Plugin.php内のexecute関数が実行されます。  

- Pluginクラスは、クラスPluginTriggerBaseを継承しています。  
PluginTriggerBaseは、呼び出し元のカスタムテーブル$custom_table、テーブル値$custom_valueなどのプロパティを所有しており、  
execute関数が呼び出された時点で、そのプロパティに値が代入されます。  
プロパティの詳細については、[プラグインリファレンス](plugin_reference.md)をご参照ください。  

### zipに圧縮
上記2ファイルを最小構成として、zipに圧縮します。  
zipファイル名は、「(plugin_name).zip」にしてください。  
- PluginDemoTrigger.zip
    - config.json
    - Plugin.php
    - (その他、必要なPHPファイル、画像ファイルなど)


### サンプルプラグイン
準備中...
