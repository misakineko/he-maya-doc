# install

Houdini Engine for MayaはHoudiniインストーラと一緒にバンドルされています。
Houdiniインストーラをスタートさせた際に、 Houdini Engine for Mayaの項目を選択するだけで使用できるようになります。
インストールがおわれば、プラグインをMayaにおいてすぐに利用できるようになります。

インストーラ―は以下のように動きます。
1. Mayaのバージョンでサポートされているすべてのプラグインを展開します。
ここにはプラグインバイナリや様々なスクリプトが含まれています。
実際にそのシステムにインストールされているMayaのバージョンにかかわらず、Mayaのそのバージョンでサポートされているすべてのファイルが引用されます。
抽出されたファイルは、$HFS/engine/mayaディレクトリで参照できます。
2. Mayaによってインストールされた、最新のMayaのプラグインを登録します。この手順はMayaのバージョンをインストーラが検出できるようにするためにのみ行われます。

通常、Mayaがローカルのデフォルトの場所にインストールされている場合、インストーラーはMayaへプラグインを登録するための必要な手順を行います。
しかし、異なるMayaのバーションが後からインストールされた場合や、変更されたソフトウェアの配置仕様においては、プラグインはMayaへ自動登録されないことがあります。
このようなケースでは、Mayaにプラグインを登録するため追加の手順が必要になります。
プラグインはMayaへ登録するために Maya module description fileを使用します。また、プラグインの実行に必要な正しい環境変数を設定します。
よくあるインストールの問題はユーザが手動でMAYA_PLUG_IN_PATH や MAYA_SCRIPT_PATHの環境変数設定を試みることです。しかし、これはプラグインを実行するための正しい環境を導かないことがあります。
Maya module description fileはライブラリのコンフリクトを解決するための追加情報を含んでいます。（この情報にアクセスしないとクラッシュするかもしれません）
プラグインの登録には、Mayaのmodule description fileの仕様が推奨されています。

## 既存のモジュールディレクトリにモジュールディスクリプションファイルをコピーする
モジュールディスクリプションファイルはプラグインファイルの絶対パスを含んでいます。

```
$HFS/engine/maya/maya<version>/houdiniEngine-maya<version>
```

そして、下記のようなMayaが検出する１つのディレクトリへコピーされるよう設計されています。

```
$MAYA_LOCATION/modules
```


例えばWindowsで、プラグインを登録するためにファイルをコピーします

```
C:\Program Files\Side Effects Software\Houdini 17.0.544\engine\maya\maya2018\houdiniEngine-maya2018
```

ここへペーストします

```
C:\Program Files\Autodesk\Maya2018\modules
```

また、Linuxにおいてはこのファイルを

```
/opt/hfs17.5.342/engine/maya/maya2018/houdiniEngine-maya2018
```

ここへコピーします

```
/usr/autodesk/maya2018/modules
```

これは実際にインストーラがMayaにプラグインを登録するとき行うものです。

## MAYA_MODULE_PATH環境変数の設定

MAYA_MODULE_PATH環境変数の設定は、もう少し融通の利くプラグイン登録方法です。モジュールディスクリプションファイルは下記のような相対パスを含みます。

```
$HFS/engine/maya/houdiniEngine-maya<version>
```

そして、MAYA_MODULE_PATH 環境変数と一緒に使用されるよう設計されています。例えば、追従するパスをこの環境変数へ挿入すると…

```
C:\Program Files\Side Effects Software\Houdini 15.0.244\engine\maya
```

またLinuxでは、このように追加できます。

```
/opt/hfs16.5.364/engine/maya
```

Mayaのバージョンのディレクトリノパスはこのパスは含まれないことに注意してください。起動するMayaのバージョンによって正しいモジュールを選択できるように、モジュールディスクリプションファイルが作成されます。
