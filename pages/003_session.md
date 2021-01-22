# 作業中
# セッション
Houdini Engineを使用するために、Houdini Engine セッションが作成される必要があります。
セッションの作成によって必要なHoudini ライブラリとプラグインがロードされ、様々な設定や環境変数に従ってHoudini Engineがセットアップされます。

## Within Maya Process

Within Maya ProcessセッションタイプはもはやH17のMayaではサポートされていません。
シンクライアントがHoudiniに追加される前に、

## Socket
SocketセッションはHoudiniEngineにシンクライアントが導入された際に追加されました。
つまり、それらクライアントは、分断されたプロセスにおいて起こる―Mayaプロセスの外で起こる、主なHoudini Enginの処理プロセスを許可しています。
ソケットセッションにおいて、TCPソケットは、以前Mayaの処理とHoudini Engineの処理の間を取り持っていました。
ソケットセッションを使用するため、Houdini Engineサーバーは、既知のマシーンの既知のポートによって、
マニュアルで起動させなければなりません。そのとき、ホストとポートの情報はプラグインに入力されます。

## Named Pipe
Named PipeセッションはHoudiniEngineにシンクライアントが導入された際に追加されました。
つまり、それらクライアントは、文壇されたプロセスにおいて起こる―Mayaプロセスの外で起こる、主なHoudini Enginの処理プロセスを許可しています
。Named Pipeセッションにおいて、TCPソケットは、以前Mayaの処理とHoudini Engineの処理の間を取り持っていました。
これは、不明確なクライアントを同じローカルマシンで使用するときに推奨される方法です。
Named pipeセッションはHoudini Engineサーバの自動起動をサポートしています。プラグインが初期化されたとき、
Houdini Engineサーバーは自動的に起動され、named pipeが自動的に選択されます。
Houdini Engineサーバーはまた既知のnamed pipeによって手動で起動することもできます。ことの時、named pipeの情報はプラグインへ入力されます。

## OptionVars
もし読み込んでいるhoudini pluginがライブラリのコンフリクトによってデフォルトセッティングが可能でないとき、
プラグイン読み込み前にセッションタイプによって操作されるoptionVarsが手動でセットできます。

デフォルトポートないしホストのsocket session：
```
optionVar -iv "houdiniEngineSessionType" 1;
optionVar -iv "houdiniEngineThriftPort" 9090;
optionVar -sv "houdiniEngineThriftServer" "localhost";
```

自動スタートのnamed pipe session：
```
optionVar -iv "houdiniEngineSessionType" 2;
optionVar -sv "houdiniEngineThriftPipe" "hapi";
optionVar -iv "houdiniEngineSessionPipeCustom" 0;
```

## Server
クライアント側でsocketセッションを使用するために、Houdiniインストール時に含まれる実行可能なHARSサーバーを起動する必要があります。
HARSはシンプルなコマンドライン引数のコンソールアプリケーションです。：

```
$ HARS -h
Allowed options:
  -h [ --help ]             Produce this help message
  -s [ --socket-port ] arg  The server port, if using a TCP socket session
  -n [ --named-pipe ] arg   The name of the pipe, if using a named pipe session
  -a [ --auto-close ]       Close the server automatically when all client
                            connections are closed
  -r [ --ready-handle ] arg Event to signal when the server is ready to serve
                            (for automated server startup)
```

HARSはlibHAPIとcore Houdini libraries、それらに依存するものに直接リンクします。
Thrift IPCがクロスプラットフォームであるので、サーバープロセス(HARS)は異なるプラットフォームにて動作するでしょう。
HARSサーバーはまた、自動スタートが明示的でないとき、named pipeセッションのために使用されるでしょう。
ポートまたはパイプはHoudini Engine設定にて明示されるひとつにあわせなければいけません。詳細はHoudini Engineドキュメントの「Session section」を参照してください。
HARSサーバーは今のところシングルクライアント接続のみサポートしているのに注意してください。
クライアントがサーバーにアクセスし、違うクライアントが接続を試みると、その時２つめのクライアントが最初のクライアントの接続が閉じられるまでブロックされるでしょう。

