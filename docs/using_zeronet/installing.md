# 安装ZeroNet

* 下载ZeroBundle： [Windows](https://github.com/HelloZeroNet/ZeroNet-win/archive/dist/ZeroNet-win.zip), [ macOS](https://github.com/HelloZeroNet/ZeroNet-mac/archive/dist/ZeroNet-mac.zip), [Linux 64bit](https://github.com/HelloZeroNet/ZeroBundle/raw/master/dist/ZeroBundle-linux64.tar.gz), [Linux 32bit](https://github.com/HelloZeroNet/ZeroBundle/raw/master/dist/ZeroBundle-linux32.tar.gz)
* 在你喜欢的地方解压。
* 运行 `ZeroNet.exe` (win), `ZeroNet(.app)` (macOS), `ZeroNet.sh` (linux)

#### Debian Linux系统的手动安装

* `sudo apt-get update`
* `sudo apt-get install msgpack-python python-gevent`
* `wget https://github.com/HelloZeroNet/ZeroNet/archive/master.tar.gz`
* `tar xvpfz master.tar.gz`
* `cd ZeroNet-master`
* 运行`python zeronet.py`
* 在浏览器中打开 http://127.0.0.1:43110/

### [Vagrant](https://www.vagrantup.com/)

* `vagrant up`
* 通过 `vagrant ssh`来连接VM
* `cd /vagrant`
* 运行 `python zeronet.py --ui_ip 0.0.0.0`
* 在浏览器中打开 http://127.0.0.1:43110/

### [Docker](https://www.docker.com/)
* `docker run -d -v <local_data_folder>:/root/data -p 15441:15441 -p 127.0.0.1:43110:43110 nofish/zeronet`
* 这个 Docker 镜像包含了Tor 代理，不过默认情况下是不启用的。注意，某些服务器提供商不允许你在他们的服务器上运行Tor服务。假如你要启用Tor，把环境变量`ENABLE_TOR` 设置为 `true` (默认是 `false`)。例如

 `docker run -d -e "ENABLE_TOR=true" -v <local_data_folder>:/root/data -p 15441:15441 -p 127.0.0.1:43110:43110 nofish/zeronet`
* 在浏览器中打开 http://127.0.0.1:43110/

### [Virtualenv](https://virtualenv.readthedocs.org/en/latest/)

* `virtualenv env`
* `source env/bin/activate`
* `pip install msgpack-python gevent`
* `python zeronet.py`
* 在浏览器中打开 http://127.0.0.1:43110/


