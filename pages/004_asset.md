# 作業中
# アセット

## アセットの定義

Houdiniアセットノードはアセット名とHDAファイルのパスが格納されます。アセット名とHDAパスはアセット定義のリファレンスとして動作します。
定義はノードやMayaシーン内に保存されていないため、HDAファイルはいつMayaシーンがひらかれてもアクセスできる状態にする必要があります。 

HDAパスを明示して指定する代わりに、Houdiniのパス検出機能を使用してhdaを検索することができます。
これは、Houdini Engineが、HOUDINI_PATH や HOUDINI_OTLSCAN_PATHのような環境変数に準じてHDAファイルも探すことができることを意味しています。
これによって、アセット名がパス検出システムで見つけられるかぎり、HDAパスを任意にすることができます。

## アセットオプション

![004_Maya_AssetOptions](https://github.com/misakineko/he-maya-doc/blob/main/pages/images/004_Maya_AssetOptions.png?raw=true)

### Show Cook Messages

アセット内のすべてのノードのクックメッセージを表示します。

### Reset Simulation

アセット内のすべてのDOPネットワークのシミュレーションキャッシュをリセットします。

### Reload Asset

parmアトリビュートや出力ノードを再構築せずにアセットの定義をリロードします。これは、入力と出力が変わっていないことがわかっているとき、通常のリロードよりも高速です。
また、アセットよりも下流で行われた変更をすべて保存します。

### Sync When Input Connects
Mayaジオメトリがアセットのインプットに接続されたとき同期します。これは、普段Mayaジオメトリに接続することがアセットのアウトプットになんらかの変更がなされるため、有用です。

### Auto Sync Outputs
これは実験用のオプションです。

必要な時に自動で同時に動作します。
しかし、予期せずにシーンが修正され、Mayaが不安定になったりクラッシュしたりする可能性があります。

### Sync Asset
すべてのMayaのShapeノードを削除し、その時点でアセット出力を表す必要のあるすべてのMayaのShapeノードを再作成します。

アセットのパラメータのアトリビュートを削除し、再作成します。詳細は「Sync Attributes」を参照してください

### Split Geos By Group

通常、すべての同じタイプのプリミティブは一緒に出力され、Mayaでは１つのノードとして出力されます。
このオプションはそれら同じタイプのプリミティブををHoudiniグループによって分割することを可能にします。
そのため、異なるグループに属するプリミティブはMaya上で別々のノードとして出力されます。
多といえば、半分のグリッドを１つ目のグループに、もう半分を別のグループにした場合、デフォルトではすべてのグリッドが一つのメッシュノードとして出力されます。
しかし、このオプションを有効にすると、半分のメッシュが1ノードとなり、もう半分はまたちがう1メッシュノードとなります。

### Use Instancer Node
ジオメトリインスタンスを出力した時、デフォルトの動作は効率的にMayaのパーティクルインスタンサーを使用して、ジオメトリをインスタンス化するのもです。
しかし、Mayaはの親子関係によるノードのインスタンス化もサポートしています。
このオプションが無効にされた場合、ジオメトリインスタンスは親として出力されます。 
膨大な数のインスタンスを出力する場合、パーティクルインスタンサーを使った方が効率的です。

### Output Hidden Objects
アセットによって非表示になっているオブジェクトを出力します。

### Output Templated Geometries
アセットによってテンプレート表示になっているジオメトリを出力します。

### Output Geometry Groups
ジオメトリのグループによるコンポーネントセットを出力します。

### Output Custom Attributes
アセット内のカスタムアトリビュートによる動的なアトリビュートを出力します。

### Preserve Mesh Hard Edges
アトリビュートを追加して入力にハードエッジフラグを盾、出力結果に対応させます。maya2018なら遅くありません。

### Preserve Mesh Locked Normals
アトリビュートを追加して入力に法線をロックするフラグをたて、出力結果に対応させます。maya2018なら遅くありません。

### Ungroup On Bake
アセットをベイキングするとき、最上位レベルのグループノードを削除します。

### Update Parms for Eval Mode
When evaluating in DG mode parm animation is always evaluated as expected during playback.
When the Evaluation Mode is serial or parallel, because of the way our attributes are structured,
we only know that some parms has been dirtied, not which specific ones. 
We are forced to update all the parms if any animation is detected.

Updating all the parms can impact playback speed if there are a lot of parms, 
and the asset cook itself is fast. Turning off this option lets you disable the parm animation update for serial and parallel mode when you don't need it.
Note that animated input geometry will still update in serial or parallel mode if this option is off.

### Connect Geo For Asset Inputs
Normally, when you use the Attribute Editor to connect another asset as an input to an asset, the first output of the asset is directly connected. 
If you select a mesh that is the direct output of an asset, the corresponding output from the asset is directly connected.
The original output geo from the asset still exists, but it's a dead end,and not part of the history chain.
This option allows you to maintain an editable copy of the geometry that can be manipulated after downstream assets have been added.

### Bake Output Textures
If the asset has materials with procedural textures, the Maya plugin will render the texture, bake it out to a file and use that file in a file texture. 
This can significantly impact the plugin's performance, especially if the texture needs to be recomputed often. 
Turning off Bake Output Textures will disable the rebaking of the texture files. 
The file texture nodes will persist with the correct file names during the maya session, 
however if the scene is reopened with the texture baking still turned off, you will need to turn it on again and sync the asset to see the texture in Maya again.
We recommend that in Maya's Display Preferences you set the MaterialLoadingMode to Immediate in order to be able to see the changes to the output textures updating.

### Preserve Houdini Scale
Each unit in Houdini is 1 meter, while in Maya each internal unit is interpreted as 1 centimeter.
If this option is enabled, geometry coming from Maya into Houdini will be scaled by a factor of .01, and geometry going from Houdini 
to Maya will be scaled by a factor of 100. This preserves the scale of the asset between both applications. 
If this is disabled, assets from Maya will come into Houdini 100x larger, and assets from Houdini will come into Maya 100x smaller than their intended scale,
which was the behavior prior to 18.0, and is still the default for backward compatibility. 
If this option is set the corresponding options on upstream houdiniAsset, houdiniInputGeometry, houdiniInputTransform and houdiniInputCurve nodes will also 
be set to control the geometry going from Maya to Houdini.
