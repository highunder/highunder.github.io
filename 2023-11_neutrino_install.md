---
layout: default
title: UbuntuにNEUTRINO Diffusion Muon v2.2をインストール
---

# UbuntuにNEUTRINO Diffusion Muon v2.2をインストール

このメモによって引き起こされた森羅万象についてひぐは一切の責任を負いかねます。**諦めろ**。

UbuntuにNEUTRINOオンライン版をインストールする手順のメモ。[Linuxへのインストール手順](https://studio-neutrino.com/596/) のCUDA周りを補足する感じ。環境は

* Ubuntu 23.04 x86_64
* いい感じのNVIDIA GPU、CPUとかメモリとか

GCCやNEUTRINOは公式の通り脳死で導入できる。nvidia-driverはUbuntuなら既に入っていたりするかもしれない。`dpkg -l | grep nvidia-driver` で入っているかどうか確認できる。入っていなかったら [ubuntuにCUDA、nvidiaドライバをインストールするメモ](https://qiita.com/porizou1/items/74d8264d6381ee2941bd) とかコピペして入れる。

僕はCUDA Toolkitで躓いたんで以下記録。

## CUDA Toolkitのインストール

[Ubuntu20.04にPythonとTensorFlowとCUDAとnvidia-driver(とcuDNN)を入れて機械学習GPU環境構築　libcudart.so.11のエラーなど](https://qiita.com/python-nnn/items/805e7d09d55c96cb655c) の焼き直し。

CUDA Toolkit入れずに `Run.sh` 叩いても `./bin/NEUTRINO: error while loading shared libraries: libcudart.so.11.0: cannot open shared object file: No such file or directory` って怒られる。まずはこのエラーが出ないようにしてく。

公式ではCUDA Toolkit v11.8を推奨している。v12.3とか入れたけど動かなかった。[CUDA Toolkit Archive](https://developer.nvidia.com/cuda-toolkit-archive) から11.8.0を探す。選択肢が出てくるので選択していく。

* Operating System: Linux
* Architecture: x86_64
* Distribution: Ubuntu
* Version: 22.04
* Installer Type: deb (local)

を選ぶとコマンドが表示されるけど、最後cudaをインストールしてもNEUTRINOが動かないので `cuda-toolkit-11-8` をインストールする。(なんでこんな面倒なインストール方法なんか疑問わね)

```
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/x86_64/cuda-ubuntu2204.pin
sudo mv cuda-ubuntu2204.pin /etc/apt/preferences.d/cuda-repository-pin-600
wget https://developer.download.nvidia.com/compute/cuda/11.8.0/local_installers/cuda-repo-ubuntu2204-11-8-local_11.8.0-520.61.05-1_amd64.deb
sudo dpkg -i cuda-repo-ubuntu2204-11-8-local_11.8.0-520.61.05-1_amd64.deb
sudo cp /var/cuda-repo-ubuntu2204-11-8-local/cuda-*-keyring.gpg /usr/share/keyrings/
sudo apt-get update
sudo apt-get -y install cuda-toolkit-11-8
```

これで `libcudart.so.11.0` がないんだが？？？っていうエラーは出なくなる。でも新たに `libcudnn.so.8` とかないよってエラーが出る。なんで ["Could not load dynamic library 'libcudnn.so.8'" when running tensorflow on ubuntu 20.04](https://stackoverflow.com/questions/66977227/could-not-load-dynamic-library-libcudnn-so-8-when-running-tensorflow-on-ubun) の通りにCUDNNを入れていく。

そのためには新たにリポジトリを追加する必要がある。 https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/x86_64/ を見て最新の公開鍵のURLを把握する。
2023年11月時点では https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/x86_64/3bf863cc.pub 。

```
sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/x86_64/3bf863cc.pub
sudo add-apt-repository "deb https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/x86_64/ /"
sudo apt-get update
sudo apt-get install libcudnn8
```

Congratulations!!! これによってあなたはNEUTRINOの使用が可能になります！楽しい音声合成人生を謳歌しましょう！NVIDIAはもう少しインストールわかりやすくしてどうぞ
