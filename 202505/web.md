<h1>Apache</h1>  


<h3>httpd</h3>  

サーバプログラム（デーモン）  
起動すると設定ファイルにあたる、  
httpd.conf（rhel10のデフォルトは/etc/httpd/conf/httpd.confだった）を読み込み、  
記載されているディレクティブに従って動作する。  
各ディレクティブはどのコンテキストで使用できるかが定められている。  
ディレクティブ  
設定ファイルの中でデーモンに動作の指示を与える。  

MPM  
マルチプロセッシングモジュール  
apacheでクライアントのリクエストを並列処理する仕組み。  

prework（デフォルト）  
1つのリクエストに対して1つのプロセスがサービスを行う。  
worker  
1つのリクエストに対してプロセス内の1つのスレッドがサービスを行う。  

fastCGI  
CGIの改良版。  
外部アプリケーションをWEBサーバに接続するプログラムであったCGIだが、リクエストごとにプロセス生成していた。  
→一度動いているプログラムをしばらく待機させて、都度プロセス生成させる手間を省くことで、処理の高速化と負荷の軽減につなげる。  

