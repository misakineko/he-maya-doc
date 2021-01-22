# はじめよう

## プラグインのロード
Mayaプラグインがインストールされたら、houdiniEngineプラグインがMayaのプラグインマネージャを通してロードされるようになります。
プラグインがロードされると、Houdini Engineメニューがメインメニューに出現します。
このメニューでは、ハイライトされたアイテムはHoudiniの最新版にて追加されたものであり、Mayaの最新版で追加されたものではありません。

![test](https://github.com/misakineko/he-maya-doc/blob/main/pages/images/002_Maya_Menu.png?raw=true)

## アセットのインスタンス化
Houdini Digital Asset(HDA)は メインメニューHoudini Engine -> Load Asset…で現れるダイアログからHDAファイルを読み込むことによってインスタンスされます。

![test](https://github.com/misakineko/he-maya-doc/blob/main/pages/images/002_Maya_BrowseHDA.png?raw=true)

HDAファイルを選んだ後に、HDAを含むアセットがインスタンスされます。
もしHDAが1つよりも多くののアセットを含んでいた場合、どのアセットをインスタンスするかを選ぶダイアログが表示されます。

![test](https://github.com/misakineko/he-maya-doc/blob/main/pages/images/002_Maya_ChooseAsset.png?raw=true)

アセットがMayaシーン内にインスタンスされたとき、houdiniAssetノードが作成されます。
アセットに関するすべてが表示され、たとえばアセットのパラメータ、インプットジオメトリ、アウトプットジオメトリなどがノードによって操作されます。
