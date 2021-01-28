#ひとまず途中

# Nodes

Houdiniのアセット自体がHoudiniアセットノードとしてカプセル化されています。
ジオメトリの入力は自身のラッパーノードがあり、エンジン側でmayaジオメトリからHoudiniジオメトリへコンバートし、対応するnullを作成します。
多様なmayaのシェイプは HoudiniInputMergeノードを介して入力に接続されます。

houdiniEngine plugin for Mayaで使われるプラグインノードは以下の通りです。

- houdiniAsset
- houdiniInputGeometry
- houdiniInputTransform
- houdiniInputCurve
- houdiniInputMerge

houdiniAssetノードは、mayaのジオメトリシェイプ出力を同期するために必要な情報と、
一度作成した自身のノードへ接続するためのmayaのジオメトリデータの、両方を出力します。

## houdiniAsset

houdiniAsset ha3種類のアトリビュートを持っています。

- static definition and option attributes
- parameters
- outputs

アトリビュートにかかわるすべてのパラメータは、混成したhoudiniAttrParmパラメータのもとで親になります。
詳しい情報は「パラメータ」のページを参照してください。
アセットオプションやアトリビュートの定義についての情報は「アセット」ページを参照してください

ほとんどのパラメータが直接アトリビュートとして露出している間は、Attribute Editorのジオメトリ入力は特別なものになります。
string型入力フィールドは、入力されたジオメトリシェイプの最小限のユニークなDAGのパスリストを定める領域になります。
このリストは直接編集でき、または Set to Selection buttonを選択中のリストで設定することも可能です。 
どのような方法で入力を指定したとしても、入力との実際の関係は上流のネットワーク入力によって表現され、計算されます。
AEフィールドは入力と視覚的フィードバックのためにあり、それはノードに乗ったアトリビュートに対応していません。

これにて出力アトリビュートを得ました。
すべての出力されたアトリビュートは混成したアトリビュート"output"のもとで親になります。
これは以下のどちらのアトリビュートも含みます。
- 同期時に作成する出力ノードとアトリビュートのための描画情報のアトリビュート
- Houdiniアセットから出力ジオメトリを接続するための実データのアトリビュート

理想的には、ユーザーはこれらのコネクションを管理する必要はなく、アセットを同期することで常に最新の状態を保つことファできます。
しかしながら、autoSyncOutputsがコネクションを無くすときもあります。
いくつかの接続されていないな出力のなかには、Mayaでのアセットの動作をデバッグする際に便利な情報を帝京するものもあります。 

| Attribute | Usage | Description |
|:---|:---|:---|
|outputAssetTransform |Connect |For an object asset, its world transform TRS|
|outputAssetInstancers(Multi) | |for each of the asset's output instancers|
|...outputInstancerTransform |Connect |instancers transform TRS|
|...outputHoudiniNameAttribute |Sync |attribute containing name|
|...outputHoudiniInstanceAttribute | |attribute for instance matching|
|...outputInstancedObjectName |Sync |object to instance|
|...outputInstancerData |Connect |named per-instance data to drive the particle instancer|
|outputObjects(Multi) | |all the output geometries from the asset|
|...outputObjectTransform |Connect |object transform TRS|
|...outputObjectMetaData |Connect |houdini object id, for connecting to other assets|
|...outputObjectFluidFromAsset |Connect |connect to fluids playFromCache to indicate data is available|
|...outputIsInstanced |Sync |is the output instanced?|
|...outputVisiblility |Sync |is the output visible?|
|...outputObjectName |Sync |name for output transform and shape|
|...outputGeos(Multi) | |geos for this object|
|......outputGeoName |Sync |name for output xform and shape|
|......outputGeoIsTemplated |Sync |is this geo templated?|
|......outputGeoIsDisplayGeo |Sync |is this geo a display object?|
|......outputParts(Multi) | |parts of geos to be output as separate shapes, e.g. for split geos by group|
|.........outputPartName |Sync |name for output shape|
|.........outputPartHasMesh |Sync |is this a mesh? i.e. does the output have primitives and vertices|
|.........outputPartHasParticles |Sync |is this part a particle? i.e. does the output have only points|
|.........outputPartHasInstancer |Sync |is this part an instancer? i.e. is the output a packed prim|
|.........outputPartMesh | ||
|............outputPartMeshCurrentColorSet |Sync |current colour set, passed through from maya|
|............outputPartMeshCurrentUV |Sync |current UV set, passed through from maya|
|............outputPartMeshData |Connect |mesh geometry Data|
|.........outputPartParticle | ||
|............outputPartParticleCurrentTime |Connect |pass though from input time connection|
|............outputPartParticlePositions |Connect |particle positions only|
|............outputPartParticleArrayData |Connect |all per-particle attributes|
|.........outputPartCurves(Multi) |Connect |curve data for each curve in this part|
|.........outputPartCurvesIsBezier |Sync |Bezier or Nurbs|
|.........outputPartVolume | |output fluid|
|............outputPartVolumeName |Sync |name of the fluid grid being output|
|............outputPartVolumeGrid |Connect |fluid grid data (connects as cache data)|
|............outputPartVolumeRes |Connect |grid resolution, use autoresize if this is time dependent|
|............outputPartVolumeTransform |Connect |fluid transform (TRS)|
|.........outputPartInstancer(Multi) | |output particle instancer|
|............outputPartInstancerArrayData |Connect |all the named per-instance attributes|
|............outputPartInstancerParts |Sync |ids of objects to instance|
|............outputPartInstancerTransform |Connect |instancer transform (TRS)|
|.........outputPartMaterialIds | |mapping for groups to material nodes|
|.........outputPartExtraAttributes(Multi) | |includes attributes with information about how to reconstruct|
|maya mesh material assignment and other mesh properties|
|as well as purely user-defined attributes from the asset|
|............outputPartExtraAttributeName |Sync |attribute name|
|............outputPartExtraAttributeOwner |Sync |detail, point or primitive|
|............outputPartExtraAttributeDataType |Sync |data type|
|...........outputPartExtraAttributeTuple |Sync |attribute tuple size|
|............outputPartExtraAttributeData |Connect |generic attr for attribute data|
|.........outputPartGroups |Multi ||
|............outputPartGroupName |Sync |group name|
|............outputPartGroupType |Sync |component type|
|............outputPartGroupMembers |Sync |component id array|
|outputMaterials(Multi) | |new materials exported from the asset|
|...outputMaterialPath |Sync |material to connect|
|...outputMaterialName |Sync |material to create|
|...outputMaterialNodeId |Sync |match material to node|
|...outputMaterialAmbientColor |Connect |ambient colour|
|...outputMaterialDiffuseColor |Connect |diffuse color|
|...outputMaterialSpecularColor |Connect |specular color|
|...outputMaterialAlphaColor |Connect |alpha|
|...outputMaterialTexturePath |Connect |file texture path|

