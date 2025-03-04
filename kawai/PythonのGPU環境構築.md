# PythonのGPU環境構築

河合宏紀

　深層学習モデルを扱う場合、GPUを活用することで計算速度を数倍\~数十倍に高速化することできる。学習済みモデルの推論時はCPU実行でも待機可能な時間のスケールで収まる場面もあるが、学習まで行おうと思うと、GPU環境を構築することは必須に近い。ここでは、PythonでGPUを使用した深層学習環境を構築する方法を説明する。

## GPU環境構築に必要な要素

　PythonでGPUを使用する場合に必要となってくる要素は、ハードウェアとしてはグラフィックボード、ソフトウェアとしてはNVIDIAドライバ、CUDA、cuDNNである。また、ここでは深層学習ライブラリとしてPytorchを使用することとする。  
　
　グラフィックボードはものによって計算速度が違うが、最も重要な要素は多くの場合メモリである。メモリが大きいほど大きな画像や大きなモデルを扱うことができ、選択肢が広がる。生物画像においては大きな画像 (1024x1024pixelは機械学習分野では大きな画像の部類である) を扱うことが多く、メモリの大きいものを選んだ方が良い。  

ソフトウェアには互いにバージョンの依存関係があり、使用したいバージョンのPytorchが要求するCUDAのバージョンに合わせてCUDAおよびcuDNN (この二つは基本的にセットである) をインストールする必要があり、さらにCUDAの要求を満たすNVIDIAドライバのバージョンをインストールする必要がある。また、あまりにも古いグラフィックボードだと新しいCUDAのバージョンが使えないことがあるが、そこまで古いボードを使用することは稀であるのでほとんどの場合は気にする必要はなく、ここでは考えない。依存関係は例えば次のような形である。執筆時点 (2024年10月) のPytorchの最新版が要求するCUDAのバージョンは11.8である。さらにCUDA11.8の要求する最低のNVIDIAドライバのバージョンは Linuxでは450.80.02、Windowsでは452.39である。

NVIDIA CUDA Toolkit Release Notes
https://docs.nvidia.com/cuda/cuda-toolkit-release-notes/index.html

したがって、これに合わせてそれぞれをインストールする必要がある。

## ソフトウェアのインストール方法

　まず、グラフィックボードに適合したNVIDIAドライバをインストールする。ドライバは公式サイトから入手することができる。

NVIDIA 公式ドライバーのダウンロード | NVIDIA

https://www.nvidia.com/ja-jp/drivers/

Windowsの場合は公式サイトからインストール用のexeを取得して実行することが一般的である。一方でLinuxの場合はコマンドで実行することが多いが、ここでは説明は省く。NVIDIAドライバが正しくインストールできれば、

`nvidia-smi `

このコマンドによってGPUの状態が表示される。

　次にCUDAとcuDNNをインストールする。CUDAのインストールは若干癖があり、うまくいかないこともあるが、condaを使用すると簡単に仮想環境ごとに任意のCUDAのバージョンをインストールすることができ非常に楽である。また、現在はPytorchのインストール時に自動的に対応するCUDAがインストールされる。ここでは、PytorchのCUDA12.1バージョンを使用することを想定する。インストールのコマンドは変化していくので公式ページ ([Start Locally | PyTorch](https://pytorch.org/get-started/locally/)) をご確認いただきたいが、執筆時点 (2024年10月) 時点におけるcondaを使用してPytorchの最新版をCUDA12.1でlinuxにインストールするコマンドは以下の通りである。

```
conda install pytorch torchvision torchaudio pytorch-cuda=12.1 \-c pytorch \-c nvidia
```



condaにおいては、CUDAおよびcuDNNに対応するものはcudatoolkitであり、cudatoolkitが上記コマンドで一緒にインストールされている。

`conda list `

のコマンドで環境にインストールされているパッケージリストを確認するとcudatookitが確認できるはずである。cudatoolkitを単独でinstallしたい場合は

`conda install conda-forge::cudatoolkit==12.1`

などとすれば良い。

次に、Pytorchが正しくGPUを認識できているか確認しよう。pythonのインタラクティブシェル (ターミナルなどでpythonコマンドを打つと始まるpython実行環境) にて

```import torch  
print(torch.cuda.is_available())  
```

と実行すると、GPUが認識できていれば `True` が、失敗していれば` False`が表示される。

## おわりに

GPUの環境構築は初心者にとって最初のハードルであり、ここさえ超えれば比較的簡単に深層学習ツールが使える状況になってきている。この分野は変化が早く、1年もすればインストール方法が変わっていることもザラである。しかし、上記で記載したような簡単な基礎情報さえわかっていれば、今はchatGPTなどを活用すれば環境を補助してもらうこともできる。また、1度構築してみればこんなものかと思うものであるので、ぜひ取り組んでみていただきたい。
