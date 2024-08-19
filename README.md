# **Learn OS in 3days**

## **OSについて**

### **そもそもOSとは？**

OS（オペレーティングシステム）は、ハードウェアを制御し、OS以外のソフトウェアに対して基本的な機能を提供するソフトウェアである。代表的なものとして、Linux、Windows、macOSなどがある。ソフトウェアを使う以上は逃れることのできないほどOSというのはとても身近な存在だが、何をもってOSとするかという厳密な定義は存在しない。そもそもOSがなくてもソフトウェアを動かすことはできる。

しかし、当たり前だがソフトウェアはハードウェアの上で動作するため、その場合はハードウェアを直接制御するための実装が必要になる。またそのソフトウェアだけではなく、その他のソフトウェアも動作するとなると、複数のソフトウェア間でハードウェアという共通のリソースを共有するための仕組みが必要になる。これらの機能をソフトウェアを作るたびに実装するとなると非常に手間がかかる。そこで、それらの機能をまとめて提供するのがOSである。

しかし、どの機能を提供するかはOSによって異なるため、何をもってOSと呼ぶのかという厳密な定義は存在しないわけだが、現代のOSであれば基本的な機能は大体共通しているため、それらを学ぶことでOSについての理解を深めることができる。

### **OSの役割**

OSの役割には大きく２つあり、ハードウェアリソースの管理と、ソフトウェアの実行の制御を行う。現代のコンピュータには、CPU、メモリ、ストレージ、その他の入出力装置などがあり、それらのハードウェアリソースを管理するために、メモリ管理、ファイル管理、入出力制御を行う。また、ソフトウェアの実行を制御するために、プロセス管理も行う。

- メモリ管理
- ファイル管理
- 入出力制御
- プロセス管理

## **コンピュータのハードウェア**

### **ハードウェア構成**

マザーボードはコンピュータの中心的な基盤となる回路基盤で、CPU、メモリ、ストレージ、入出力装置などの主要なコンポーネントを接続し、相互に通信させる役割を担っている。CPUとメインメモリはマザーボードを介してメモリバスと呼ばれる高速な信号線で接続される。また、SSDやGPUなどの高速なデバイスもマザーボードを介してPCI express（PCIe）と呼ばれる高速なバスでCPUと接続される。

HDDやキーボード、USBなどの入出力装置はそれぞれを担当するデバイスコントローラに接続される。イーサネットやWi-Fiでパケットのやり取りに使用されるNICやWNICは入力装置でもあり出力装置でもあると言える。

デバイスコントローラは入出力装置を制御するための制御レジスタを持っており、プロセッサは制御レジスタに命令を書き込んだり、制御レジスタの値を読み出すことで入出力装置を制御する。通常これらのデバイスコントローラは、チップセットと呼ばれるLSIに集約されていて、チップセットはマザーボードを介してCPUと、PCIeベースの通信プロトコルを使用したDMI（Direct Media Interface）と呼ばれる高速なバスで接続される。

### **ISA**

CPUの機種によって解釈できる機械語は異なる。CPUが解釈できる機械語命令や、使用できるレジスタなど、プログラムから見たときのCPUの仕様をISA（命令セットアーキテクチャ）と呼ぶ。詳細は[ARM? RISC-V? x86? 用語がごっちゃになっている人のためのCPUアーキテクチャの全体図](https://qiita.com/rihib/items/6b70361b48c3840d09b0)を参照。

### **マルチコア**

CPUが機械語命令を順次実行していく流れのことをハードウェアスレッドという。コンピュータの性能を向上させるには大きく、クロック周波数を高くする方法と複数のプロセッサを用いて複数のハードウェアスレッドを並列に動作させる方法がある。複数のプロセッサを持つCPUのことをマルチコアCPUと呼ぶ。前者の方法はクロック周波数が3GHzを超えたあたりから頭打ちになりつつあり、現在は後者の方法が中心となっている。

### **ブートストラップ**

電源を入れてからOSが起動するまでの一連の動作をブートストラップ（ブート）と呼ぶ。通常のプログラムはカーネルによってメモリ上に載せられるが、OSもソフトウェアである以上、実行するにはメモリ上にプログラムを載せる必要がある。一般的にOSはストレージ上に通常のファイルとして格納されているため、これをOSが起動していない状態で読み込む必要がある。

そこでROMやフラッシュメモリなどの不揮発性メモリ上にブートストラップローダと呼ばれるプログラムを用意しておき、電源投入時にこれが動作することで、ストレージ内の特定の場所に置かれたブートローダと呼ばれるプログラムをメモリにロードして起動する。ブートローダはストレージ内に置かれているカーネルを読み込み、メモリ上に載せ、最後にカーネルに制御を移す。

具体的には下記の流れでブートが行われる。

1. 電源を入れるとパワーオンリセットと呼ばれる回路によりプロセッサがリセットされる。これにより、プロセッサはあらかじめ定められた状態に設定され、特定の物理アドレスから実行を開始する。そこにはBIOSの実行開始アドレスにジャンプする機械語命令が入っている
2. BIOSは最初にハードウェアの簡単な診断を行うPOST（power on self test）と呼ばれる処理を実行し、搭載している物理メモリの容量の確認や、起動に必要な周辺機器の検出と初期化などを行う
3. ブートデバイス（OSがインストールされているストレージ）の先頭にあるMBR（master boot record）と呼ばれる領域を読み込む。MBRにはブートストラップコードと呼ばれる小さい機械語プログラムとストレージのパーティション情報が含まれている。次にブートストラップコードに制御を移す
4. ブートストラップコードはパーティション情報をチェックし、ブート可能とマークされているパーティションの先頭から、セカンダリブートローダと呼ばれる小さなプログラムをロードし、実行する
5. セカンダリブートローダは、OSを使わずに直接、ストレージにアクセスし、カーネルを読み込む
6. 読み込んだカーネルの実行開始アドレスにジャンプし、カーネルの起動処理が始まる

### **プロセッサの動作モード**

一般的にプロセッサはカーネル（特権）モードとユーザーモードと呼ばれる２つの動作モードを持つ。カーネルモードはカーネルを実行するためのモードであり、プロセッサの全ての動作を際限なく行えるのに対し、ユーザーモードはユーザープログラムを実行するためのモードであり、特権命令（システムに影響を与える可能性がある機械語命令）の実行やハードウェアへの直接アクセスが禁止される。ユーザーモードでこれらを実行しようとすると特権違反例外が発生し、カーネルに制御が移る。

### **割り込み**

プロセッサは、システムで何らかの急いで処理すべき事象が発生したときに、実行中のプログラムを一時中断し、別のプログラムを実行する、割り込みと呼ばれる機能を持っている。このときに実行するプログラムを割り込みハンドラと呼ぶ。割り込みには、外部割り込みと内部割り込みがある。

外部割り込み（ハードウェア割り込み）は、プロセッサ外部のデバイスで何らかの事象が発生したことをプロセッサに通知するための割り込みである。プロセッサは外部からの割り込みを受け付けるためのIRQ（割り込み信号線）を持つ。単に割り込みといった場合は、通常外部割り込みを指す。

内部割り込みは、プログラムの実行に伴ってプロセッサ内部で発生する割り込みである。内部割り込みには（狭義の）ソフトウェア割り込みとトラップ（例外、フォールト）がある（内部割り込み自体をソフトウェア割り込みと呼ぶこともある）。

ソフトウェア割り込みは特別な機械語命令を実行することで発生し、プロセッサのモードをユーザーモードからカーネルモードに変更するために使用される。トラップはプログラムの実行中になんらかの例外的な事象が発生することで引き起こされる。例えばゼロ除算例外、未定義命令違反、特権違反例外、セグメンテーションフォールト（アクセスが許可されていないアドレスを参照）、ページフォールト（物理メモリが割り当てられていない仮想ページを参照しようとしたとき）などがある。

コンピュータは指定した周期でプロセッサに割り込みをかけるタイマと呼ばれるデバイスを備えている。この割り込みをタイマ割り込みという。OSには定期的に実行する様々な処理があり、タイマ割り込みはそのために使用される。タイマ割り込みは内部割り込みの一種である。

## **メモリ管理**

### **物理アドレス**

メインメモリには１バイトごとに１つの物理アドレスが割り当てられる。またプロセッサがアクセスできる物理アドレスの範囲を物理アドレス空間と呼ぶ。

### **仮想メモリ**

仮想メモリは、プロセスが使用するアドレスと実際の物理アドレスを分離することで、メモリを仮想化する技術である。仮想記憶では、プロセスごとに専用の独立したアドレス空間を割り当てる。プロセスから見えるアドレスは物理アドレスではなく、仮想アドレスである。プロセスがメモリにアクセスするときは、仮想アドレスを物理アドレスに変換した上で物理メモリにアクセスする。

仮想メモリを用いることで、他のプロセスに割り当てられたメモリに影響されずに各プロセスに対して連続したアドレス空間を提供でき、また各プロセスのアドレス空間を分離することで互いに隔離し、システムの安定性を高めることができる。

### **ページング方式**

仮想アドレスと物理アドレスとの対応関係（マッピング）をバイト単位で指定できるようにすると、アドレス変換のための情報量が膨大になってしまうため、ある程度の大きさのメモリブロックを単位としてマッピングを行う。現在は固定長のメモリブロックを単位とするページング方式が主流である。

ページング方式では、仮想アドレス空間と物理アドレス空間を固定長（ページサイズ）のブロックに分割する。ページサイズは4KBが一般的である。仮想アドレス空間のブロックを仮想ページ（ページ）、物理アドレス空間のブロックを物理ページ（ページフレーム）と呼ぶ。ページにはページ番号がついており、仮想アドレスや物理アドレスは先頭部分にそのアドレスが属するページ番号を持ち、ページ内オフセットがそれに続く構成になっている。

仮想アドレスから物理アドレスへの変換は、ページを物理ページにマッピングすることで行われる。プロセスに割り当てるメモリは物理アドレス空間上では連続している必要はないため、物理メモリの外部断片化が発生しない。

カーネルは空いている物理ページを連結リストやビットマップなどで管理する。カーネルは新しくプロセスを実行する場合などにプロセスに割り当てるページが必要になると、空いている物理ページを割り当てる。また、プロセスが終了した場合など、ページが不要になれば対応する物理ページを回収する。

### **ページテーブル**

仮想アドレスから物理アドレスへの変換にはページテーブルと呼ばれるデータ構造を使用する。ページテーブルは仮想ページ番号でインデックスする配列であり、物理メモリ上に配置される。ページテーブルの要素をページテーブルエントリと呼び、１つのページテーブルエントリが１つの仮想ページに対応し、マッピングされている物理ページの番号や各種のフラグを格納している。

仮想アドレスを物理アドレスに変換する作業は、MMU（memory management unit）と呼ばれるハードウェアがページテーブルを参照することで動的に行われる（動的アドレス変換）。現代の一般的なプロセッサはMMUを内蔵している。

MMUには、使用するページテーブルの物理アドレスを保持するレジスタ（ページテーブルベースレジスタ）があり、異なるページテーブルを指すように変更することで、プロセッサが使用する仮想アドレス空間を切り替えることができる。

OSはプロセスごとにページテーブルを用意し、コンテキストスイッチによって実行するプロセスを切り替えるとき、ページテーブルベースレジスタの値を、次に実行するプロセスのページテーブルの先頭アドレスに設定し直す。

具体的には下記の流れでアドレス変換が行われる。

1. 仮想アドレスから仮想ページ番号とページ内オフセットを求める
2. ページテーブルベースレジスタからページテーブルを参照し、仮想ページ番号をもとに、ページテーブルエントリを取得する
3. ページテーブルエントリから物理ページ番号を取得する
4. 物理ページ番号とページ内オフセット（１で取得したもの）を組み合わせて物理アドレスを求める（物理ページ番号 * ページサイズ + ページ内オフセット）

### **多段ページテーブル**

仮想アドレス空間が広くなると、その分、必要なページテーブルのサイズも大きくなる。例えば仮想アドレス空間が256TB、ページサイズが4KB、ページテーブルエントリが8Bの場合、ページテーブルとして、256 / 4 * 8 = 512GB必要になる。しかし、一般的にプロセスは仮想アドレス空間のごく一部しか使用しないため、ほとんどのページテーブルエントリは無駄となる。

そこで考え出されたのが、ページテーブルを階層化した多段ページテーブルである。多段ページテーブルでは、最下位のページテーブルは１段しかない場合のページテーブルと同様のページテーブルエントリを保持するが、最下位以外のページテーブルでは、各エントリは１つ下のページテーブルの物理アドレスを保持する。

多段ページテーブルでは、実際に使用する仮想アドレスの範囲に対応するページテーブルのみを用意すれば良いため、物理メモリを節約することができる。

### **仮想アドレス空間**

仮想アドレス空間の構成はOSによって異なるが、低位領域をユーザープロセス用とし、高位領域をカーネル用とするのが一般的である。前者をユーザー空間、後者をカーネル空間と呼ぶ。

## **プロセス管理**

### **プロセスとは？**

OSでは実行中のプログラムのことをプロセスと呼ぶ。OSはプロセス単位で保護、リソース割り当て、権限管理を行う。

ユーザーは信頼度の低いプログラムを動かす可能性があり、そのような場合でもシステムを安定動作させるために、OSはそれぞれのプロセスが他のプロセスやOSそのものに影響を与えないように隔離し、保護する。これにより、バグがあるプログラムを動かしてもたいていの場合はそのプロセスだけがクラッシュ（異常終了）するだけですむ。

また、プロセスが動作するには機械語を実行するためのプロセッサと、プログラムやデータを配置するためのメモリが必要であり、OSは各プロセスに対して適切にこれらのリソースを割り当てる。

また、OSにはさまざまなオブジェクト（ファイル、デバイス、ソケット、共有メモリなど）が存在するが、OSはそれぞれのオブジェクトに対して操作を行う権限をプロセスごとに管理し、プロセスが許可されていない操作を行おうとすると、OSはプロセスの実行を停止させたり、ユーザーに許可を求めたりする。

### **PCB**

OSはプロセスを管理するためにそれぞれのプロセスに対してカーネル内にPCB（プロセス制御ブロック）と呼ばれるデータ構造を割り当てる。プロセッサごとに現在実行中のプロセスのPCBへのポインタを持つ。PCBには一般的に下記の内容が含まれる。

- PID（プロセスID）
- プロセスを実行しているユーザに関する情報
- プロセスが使用しているメモリ領域に関する情報
- プロセスが使用しているリソース（オープンしているファイルなど）に関する情報
- プロセスが保持する権限に関する情報
- アカウンティング情報（使用したCPU時間やメモリ使用量、入出力の回数などに関する統計情報）

### **プログラムのロード**

プログラムを実行するには、ストレージ上に実行ファイルとして格納されている機械語プログラムを、プロセッサのアドレス空間上にロードする必要がある。またスタックやヒープ領域などの実行中に必要なメモリ領域を確保し、初期化する必要もある。これらを行うプログラムはローダと呼ばれ、カーネルに組み込まれている。ロードにあたって、OSはプロセスにアドレス空間上の適当なメモリ領域を割り当てる。一般的なOSではプロセスのメモリ空間は下記の領域から構成される。

- テキスト領域：プログラムを構成する機械語命令を格納する。実行ファイルのテキストセクションに対応する
- データ領域：初期値が与えられているデータを格納する。実行ファイルのデータセクションに対応する
- BSS領域：初期値が与えられていないデータのための領域。実行ファイルのBSSセクションに対応する
- ヒープ領域：C言語のmalloc関数やC++言語のnew演算子のようにプログラムの実行中に動的に割り当てるメモリのための領域。通常アドレスの大きくなる方向に拡大する
- スタック領域：機械語プログラム実行中に使用されるスタックのための領域。通常アドレスの小さくなる方向に拡大する

実行中に必要なデータを一時的に保持するために、スタック領域を使用する。一般的なプロセッサはスタックトップ（最後にpushしたデータ）のアドレスを持つスタックポインタ（SP）というレジスタを持っている。スタックはレジスタに格納できない関数の引数やリターンアドレス、ローカル変数などを保持する。プロセッサがカーネル内のコードを実行しているときは、ユーザープロセスのスタック（ユーザースタック）とは別のスタック（カーネルスタック）を使用する。カーネルスタックはカーネル領域に存在する。

下記の流れでプログラムはロードされる。

1. 実行ファイルのヘッダ部から各セグメントの大きさを読み取る
2. 必要なメモリ領域を確保する
3. 実行ファイルからテキストセクションとデータセクションの内容をメモリ上に読み込む
4. BSS領域を初期化する（一般的には0クリアする）
5. スタック領域を初期化する（一般的には0クリアする）
6. プログラムが動的リンクされる場合、動的リンクに備えた準備を行う（メモリ上に共有ライブラリを配置するなど）

### **実行ファイルの構造**

実行ファイルはヘッダと複数のセクションから構成される。ヘッダはマジックナンバ（ファイル形式を識別するための値）から始まり、実行ファイルのメタデータ（各セクションのサイズ、プログラムの実行開始アドレス、サポートするOSのバージョン、プロセッサの情報など）を格納している。セクションには下記がある。

- テキスト：プログラムの機械語命令の部分を格納する
- データ：プログラムが使用するデータのうち、初期値が与えられているものを格納する
- BSS：初期値が与えられていないデータのための領域。実行ファイルにはBSSセクションのサイズ情報のみが格納される
- 共有ライブラリ情報：動的リンクする共有ライブラリに関する情報を格納する
- リロケーションテーブル：プログラムを任意のアドレスに配置するための情報を格納する
- シンボルテーブル：関数やグローバル変数などのシンボルとアドレスの対応表を格納する。動的リンクでは、動的ライブラリからプログラムのアドレスを参照するために用いる。デバッガはこの情報を使用することでシンボル名を使用してデバッグできるようにする
- デバッグ情報：デバッグに必要な情報（ソースコードの行番号とアドレスの対応表など）が格納される。デバッガはこの情報も参照する。プログラムの開発段階でのみ使用される
- その他：フォーマットによっては実行ファイルのアイコンの画像などを格納できるものもある

### **Cソースコードが実行ファイルになるまで**

1. プリプロセス：マクロ展開、ファイルのインクルード、条件付きコンパイル
2. コンパイル：コンパイラは、プリプロセスされたソースコードを受け取り、アセンブリ言語に変換
   1. 字句解析：トークンに分割
   2. 構文解析：構文木に変換
   3. 中間表現生成：ClangのようなLLVMを基盤とするコンパイラはLLVM IRを生成
   4. 最適化：LLVM IRはLLVMのバックエンドで最適化される。ループ最適化、デッドコード除去、インライン展開など
   5. アセンブリコード生成：最適化された中間表現をターゲットアーキテクチャに応じたアセンブリコードに変換
3. アセンブル：アセンブラはマシンコード（オブジェクトファイル）に変換
4. リンク：リンカは全てのオブジェクトファイルと静的ライブラリをリンクし、未解決の外部シンボルを解決して１つの実行可能ファイルを生成する

### **システムコール**

ユーザープログラムから自由にプロセッサの設定を変更したり、ハードウェアを直接制御したりできてしまうと、システムの安定性やセキュリティ上重大な問題となるため、カーネルは実行ファイルからロードした機械語プログラムを実行している間、プロセッサをユーザーモードに設定して、ユーザープログラムが問題がある動作を行おうとすると特権違反例外が発生し、カーネルはそのプロセスを強制終了するなどの処置をとる。しかし、ユーザープログラムもファイルの読み書きやメモリの割り当てなど、リソースを操作したい場合が存在する。そのため、それらの操作をC言語などから呼び出し可能なAPIとして提供する。このAPIをシステムコールと呼ぶ。ユーザープログラムからシステムコールを呼び出すと、プロセッサのモードをユーザーモードからカーネルモードに切り替え、システムコールの処理を実行した後、カーネルモードからユーザーモードに切り替えてからユーザープログラムに戻る。この時、プロセッサがカーネル内のコードを実行している間はカーネルスタックが使用される。

システムコールの実行は下記のように行われる。

1. アプリケーションプログラムがシステムコールのラッパー関数を呼び出す
2. ラッパー関数はシステムコール番号とシステムコールへの引数をカーネルに渡すために、レジスタやスタックなどに書き込む
3. ラッパー関数はシステムコールを実行するための特別な機械語命令を実行する。この命令はソフトウェア割り込みを引き起こす
   1. プロセッサをカーネルモードに遷移させる
   2. スタックをカーネルスタックに切り替える
   3. ユーザープロセスの現在のコンテキストをレジスタやカーネルスタックなどに退避させる
   4. カーネル内にあるシステムコールを処理するためのルーチンに制御を移す
4. ２で渡されたシステムコール番号を読み取り、そのシステムコールを処理する関数を呼び出す
5. 処理が終了したらシステムコールの返り値をレジスタなどに書き込み、ユーザーモードに戻るための機械語命令を実行する
6. プロセッサはユーザーモードに遷移し、ユーザープロセスのコンテキストを復元する
7. ラッパー関数はシステムコールの返り値を返す

### **マルチタスク**

現代のOSは複数のプログラムを同時に実行できるマルチタスク（マルチプロセス）をサポートしている。マルチタスクには協調的マルチタスクとプリエンティブマルチタスクの２つがある。協調的マルチタスクではそれぞれのプログラムは自発的に他のプログラムにプロセッサを譲る（これをyieldという）操作を行うまで実行を継続する。協調的マルチタスクは単純なので、プリエンプティブマルチタスクに比べて実装が容易であるが、各プログラムは長時間プロセッサを占有しないように定期的にyieldする必要がある。一方、プリエンプティブマルチタスクでは、プログラムが明示的にyieldしなくても、実行するプログラムがタイムスライス（プリエンプションされずに連続して実行できる時間）を使い切るとカーネルが強制的に切り替える。この強制的な切り替えのことをプリエンプション（横取り）と呼ぶ。プリエンプションされたプログラムはいずれ中断した処理の続きを実行する機会が与えられる。

実行するプロセスやスレッドを切り替えることをコンテキストスイッチと呼ぶ。どのプロセスにプロセッサを割り当てて実行するのかを決める処理のことをスケジューリングといい、スケジューラと呼ばれるプログラムがこれを行う。
