## 簡単なアプリケーションをデプロイ (Go編)

とても簡単なGoのWebアプリケーションを作成 & デプロイしましょう。ここでは、パッケージマネージャの`Godep`がインストールされていることを前提としています。

### プロジェクトの作成

``` console
$ mkdir -p $GOPATH/src/github.com/pcf-ws/hello-cfgo
$ cd $GOPATH/src/github.com/pcf-ws/hello-cfgo
$ dep init
```
hello-cfgo のディレクトリに任意のエディタで`main.go`のファイルを作り以下のように編集をします。

``` go
package main

import (
	"fmt"
	"net/http"
)

func main() {

	http.HandleFunc("/hello", func(w http.ResponseWriter, r *http.Request) {
		fmt.Fprintf(w, "Hello World")
	})
	http.ListenAndServe(":8080", nil)
}
```

ソースコードを修正したら、まずはローカルでアプリケーションを実行してみましょう。

``` console
$ go run main.go
```

[http://localhost:8080](http://localhost:8080)にアクセスしてください。
```console
$ curl localhost:8080/hello
Hello World%
```

Hello World!が表示されれば成功です。

**ここまで完了したら進捗シートにチェックをしてください。**

### アプリケーションをPivotal Cloud FoundryにPush

ビルドしたアプリケーションをPivotal Cloud FoundryにPushしましょう。
まずプロジェクトのディレクトリに下記の`manifest.yml`ファイルを作成します。
`<STUDENT_ID>`は配布されたIDに置換してください。
```yaml
---
applications:
- name: hello-cfgo-<STUDENT_ID>
  path: .
  env:
    GOPACKAGENAME: hello-cfgo
```

デプロイします。`cf`コマンドを使えばとても簡単です。
``` console
$ cf push
Using manifest file /Users/tkaburagi/pivotal/go/src/github.com/hello-go/manifest.yml

Creating app hello-cfgo in org pivot-tkaburagi / space playground as tkaburagi@pivotal.io...

OK

Creating route hello-cfgo.apps.pcfone.io...
OK

Binding hello-cfgo.apps.pcfone.io to hello-cfgo...
OK

Uploading hello-cfgo...
Uploading app files from: /Users/tkaburagi/pivotal/go/src/github.com/hello-go
Uploading 1.2K, 5 files
Done uploading
OK

Starting app hello-cfgo in org pivot-tkaburagi / space playground as tkaburagi@pivotal.io...
Downloading microgateway_decorator...
Downloading nodejs_buildpack...
Downloading python_buildpack...
Downloading dotnet_core_buildpack...
Downloading go_buildpack...
Downloaded dotnet_core_buildpack
Downloading php_buildpack...
Downloaded microgateway_decorator
Downloading binary_buildpack...
Downloaded nodejs_buildpack
Downloading hwc_buildpack...
Downloaded php_buildpack
Downloaded hwc_buildpack
Downloading meta_buildpack...
Downloaded go_buildpack
Downloading staticfile_buildpack...
Downloaded python_buildpack
Downloading java_buildpack_offline...
Downloaded binary_buildpack
Downloading ruby_buildpack...
Downloaded ruby_buildpack
Downloaded staticfile_buildpack
Downloaded java_buildpack_offline
Downloaded meta_buildpack

Downloading app package...
Downloaded app package (1K)
[meta-buildpack] /tmp/buildpacks/0411a134f53bd1c4449940e4c628e7ec/bin/detect not found
[meta-buildpack] Compiling with Go
-----> Go Buildpack version 1.8.25
-----> Installing godep 80
       Copy [/tmp/buildpacks/7425d3063cf77382a1d058d91ffc7264/dependencies/045a7a9a68cf48fc0d272457ea825594/godep-v80-linux-x64.tgz]
-----> Installing glide 0.13.1
       Copy [/tmp/buildpacks/7425d3063cf77382a1d058d91ffc7264/dependencies/14bfbddbd6a28177d3f14ef6ea5e6bfa/glide-v0.13.1-linux-x64.t
gz]
-----> Installing dep 0.4.1
       Copy [/tmp/buildpacks/7425d3063cf77382a1d058d91ffc7264/dependencies/6b7d3f5fbda1b24fc7c40dae0a80a1bf/dep-v0.4.1-linux-x64.tgz]
-----> Installing go 1.8.7
       Copy [/tmp/buildpacks/7425d3063cf77382a1d058d91ffc7264/dependencies/2d78093980ccee4a4ac3c2ea22025813/go1.8.7.linux-amd64-9bdda
6e7.tar.gz]

-----> Fetching any unsaved dependencies (dep ensure)
       **WARNING** Installing package '.' (default)
-----> Running: go install -tags cloudfoundry -buildmode pie .
Exit status 0
Uploading droplet, build artifacts cache...
Uploading build artifacts cache...
Uploading droplet...
Uploaded build artifacts cache (213B)
Uploaded droplet (2.1M)
Uploading complete
Cell 5a3640b8-f655-4922-b77b-0f0803f23ae1 stopping instance 34adedff-9056-49f6-aa35-c34a2eee23ea
Cell 5a3640b8-f655-4922-b77b-0f0803f23ae1 destroying container for instance 34adedff-9056-49f6-aa35-c34a2eee23ea
Cell 5a3640b8-f655-4922-b77b-0f0803f23ae1 successfully destroyed container for instance 34adedff-9056-49f6-aa35-c34a2eee23ea

1 of 1 instances running

App started


OK

App hello-cfgo was started using this command `./bin/hello-go`

Showing health and status for app hello-cfgo in org pivot-tkaburagi / space playground as tkaburagi@pivotal.io...

OK

requested state: started
instances: 1/1
usage: 1G x 1 instances
urls: hello-cfgo.apps.pcfone.io
last uploaded: Mon Sep 3 07:31:28 UTC 2018
stack: cflinuxfs2
buildpack: Go (no decorators apply)

     state     since                    cpu    memory       disk         details
#0   running   2018-09-03 04:32:00 PM   0.0%   3.8M of 1G   6.8M of 1G
```

これでデプロイに成功しました。
`cf apps`でデプロイされているアプリケーションの一覧を取得できます。

``` console
$ cf apps
Getting apps in org pivot-tkaburagi / space playground as tkaburagi@pivotal.io...
OK

name          requested state   instances   memory   disk   urls
hello-cfgo    started           1/1         1G       1G     hello-cfgo.apps.pcfone.io
```

`urls`の列に出力されている値がアプリケーションのURLです。この場合は[hello-cfgo.apps.pcfone.io/hello](hello-cfgo.apps.pcfone.io/hello)です。

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/79860de1-4846-c922-8583-787e77a185d2.png)

Pivotal Cloud Foundry上にデプロイされたアプリケーションにもアクセスできました。

**ここまで完了したら進捗シートにチェックをしてください。**

[Pivotal Cloud Foundry Apps Manager](https://apps.sys.pcflab.jp)を見てみましょう。
![image](https://qiita-image-store.s3.amazonaws.com/0/1852/4fd1565a-9bad-63a2-9382-6e7f59363b10.png)

「Space development」をクリックしてください。`development`というスペースにデプロイされているアプリケーションの一覧を確認できます。`hello-cfgo-<STUDENT_ID>`が表示されています。

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/a0684f30-3757-2cfe-0580-c19b3598a499.png)

`hello-cfgo-<STUDENT_ID>`をクリックしてください。

![image](https://qiita-image-store.s3.amazonaws.com/0/1852/58280398-6b1b-1d41-7581-62ba76f24d06.png)

アプリケーションの情報が確認できます。

直近のログは`cf logs <App> --recent`で確認できます。

``` console
$ cf logs hello-cfgo-<STUDENT_ID> --recent
Connected, dumping recent logs for app hello-tmaki in org tmaki / space development as ****@gmail.com...

 2018-09-03T16:31:44.10+0900 [STG/0] OUT -----> Running: go install -tags cloudfoundry -buildmode pie .
 2018-09-03T16:31:54.10+0900 [STG/0] OUT Exit status 0
 2018-09-03T16:31:54.10+0900 [STG/0] OUT Uploading droplet, build artifacts cache...
 2018-09-03T16:31:54.10+0900 [STG/0] OUT Uploading build artifacts cache...
 2018-09-03T16:31:54.10+0900 [STG/0] OUT Uploading droplet...
 2018-09-03T16:31:54.14+0900 [STG/0] OUT Uploaded build artifacts cache (213B)

 2018-09-03T16:31:54.18+0900 [API/4] OUT Creating droplet for app with guid b4e02d29-381c-4dbe-aa4c-28901d307cbd
 2018-09-03T16:31:59.22+0900 [STG/0] OUT Uploaded droplet (2.1M)
 2018-09-03T16:31:59.23+0900 [STG/0] OUT Uploading complete
 2018-09-03T16:31:59.25+0900 [STG/0] OUT Cell 5a3640b8-f655-4922-b77b-0f0803f23ae1 stopping instance 34adedff-9056-49f6-aa35-c34a2e
ee23ea
 2018-09-03T16:31:59.25+0900 [STG/0] OUT Cell 5a3640b8-f655-4922-b77b-0f0803f23ae1 destroying container for instance 34adedff-9056-
49f6-aa35-c34a2eee23ea
 2018-09-03T16:31:59.46+0900 [CELL/0] OUT Cell edb926f2-8057-4f20-bee2-c44ae043e557 creating container for instance f3bce0fb-f9c3-4
96f-6969-16e6
 2018-09-03T16:31:59.79+0900 [STG/0] OUT Cell 5a3640b8-f655-4922-b77b-0f0803f23ae1 successfully destroyed container for instance 34
adedff-9056-49f6-aa35-c34a2eee23ea
 2018-09-03T16:31:59.87+0900 [CELL/0] OUT Cell edb926f2-8057-4f20-bee2-c44ae043e557 successfully created container for instance f3b
ce0fb-f9c3-496f-6969-16e6
 2018-09-03T16:32:00.35+0900 [CELL/0] OUT Starting health monitoring of container
 2018-09-03T16:32:00.72+0900 [CELL/0] OUT Container became healthy
 2018-09-03T16:32:33.55+0900 [RTR/1] OUT hello-cfgo.apps.pcfone.io - [2018-09-03T07:32:33.551+0000] "GET /hello HTTP/1.1" 200 0 11
"-" "curl/7.54.0" "192.168.4.4:1164" "192.168.4.91:61008" x_forwarded_for:"126.112.254.162, 192.168.4.4" x_forwarded_proto:"https" vc
ap_request_id:"b813b3d9-2dda-464c-7c7a-dda167abc99a" response_time:0.003734283 app_id:"b4e02d29-381c-4dbe-aa4c-28901d307cbd" app_inde
x:"0" x_b3_traceid:"ddcd9a99d5df8a47" x_b3_spanid:"ddcd9a99d5df8a47" x_b3_parentspanid:"-"
 2018-09-03T16:32:33.55+0900 [RTR/1] OUT
 2018-09-03T16:38:40.00+0900 [RTR/1] OUT hello-cfgo.apps.pcfone.io - [2018-09-03T07:38:40.002+0000] "GET /env HTTP/1.1" 404 0 19 "-
" "curl/7.54.0" "192.168.4.4:21459" "192.168.4.91:61008" x_forwarded_for:"126.112.254.162, 192.168.4.4" x_forwarded_proto:"https" vca
p_request_id:"c4d346f1-ccdd-4134-7ba2-f5b249a9c4f6" response_time:0.003483628 app_id:"b4e02d29-381c-4dbe-aa4c-28901d307cbd" app_index
:"0" x_b3_traceid:"f2ef20cbd7daf8aa" x_b3_spanid:"f2ef20cbd7daf8aa" x_b3_parentspanid:"-"
 2018-09-03T16:38:40.00+0900 [RTR/1] OUT
```

また`cf logs <App>`で今後流れるログを確認することができます(`tail -f`相当)。

### アプリケーションの削除

`cf delete`でアプリケーションを削除できます。

``` console
$ cf delete hello-<STUDENT_ID>

Really delete the app hello-tmaki?> y
Deleting app hello-tmaki in org tmaki / space development as ****@gmail.com...
OK
```

**ここまで完了したら進捗シートにチェックをしてください。**
