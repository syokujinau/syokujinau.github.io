---
title: CentOS架設Jupyter notebook server
date: 2019-03-03 23:10:54
tags:
- jupyter
- CentOS
thumbnail: https://i.imgur.com/IYjQsLQ.png
---

<!-- title: CentOS架設Jupyter notebook server -->

# Step1: 新增CentOS使用者

```sh
sudo adduser jpynb
sudo su
passwd jpynb
gpasswd -a jpynb wheel #給予使用者 sudo 權限
lid -g wheel
```

之後就能用該組帳號密碼ssh進入/home/jpynb



# Step2: 安裝jupyter

```sh
sudo yum install python-pip
sudo yum install python3-pip

sudo pip install ipython[all]
sudo pip3 install ipython[all]
```

這時輸入以下指令，初步測試是否安裝成功
```shell
jupyter notebook --no-browser
```
<!-- more -->
# Step3: Config與自製憑證

```sh
jupyter notebook --generate-config
```

進入ipython介面設定密碼

```sh
ipython

In[1]: from notebook.auth import passwd
In[2]: passwd()
# 輸入exit()退出ipython
```

這時會輸出一個sha1加密的密碼
`Out[2]: 'sha1:XXXXXXXXXXXXXXXXXXX'`

修改/home/jpynb/.jupyter/jupyter_notebook_config.py
```
vim /home/jpynb/.jupyter/jupyter_notebook_config.py
```
新增下面幾行
```
c.NotebookApp.password = 'sha1:XXXXXXXXXXXXXXXXXXX'
c.NotebookApp.port = 8888
c.NotebookApp.ip = '0.0.0.0' 
c.NotebookApp.open_browser = False
c.NotebookApp.certfile = '/home/jpynb/.jupyter_keys/jcert.pem'
c.NotebookApp.keyfile = '/home/jpynb/.jupyter_keys/jkey.key'
```

> Note: 有些教學文寫c.NotebookApp.ip = '*'來允許任何ip連線，但是會有問題，用'0.0.0.0'可以解決 

新增資料夾存放自製憑證的key
```
mkdir .jupyter_keys
cd .jupyter_keys
openssl req -x509 -nodes -days 365 -newkey rsa:4096 -keyout jkey.key -out jcert.pem
```
之後`cd ..`回到home/jpynb

# Step4: 防火牆設定

```sh
sudo firewall-cmd --zone=public --add-port=8888/tcp --permanent
systemctl restart firewalld.service
```

> Note: iptables跟firewallId不能同時使用，可以參考[CentOS Linux 7 以 firewalld 指令設定防火牆規則教學](https://blog.gtwang.org/linux/centos-7-firewalld-command-setup-tutorial/)來更改設定

# Step5: 使用Screen背景執行

現在輸入`jupyter notebook`就可以執行了，但是把ssh連線關掉它就會跟著shutdown

```sh
screen
```

進入screen之後，執行`jupyter notebook`
然後按`Ctrl + a`再按`d`就可以detach
之後用`screen -ls`查看screen號碼，輸入`screen -r <號碼>`就能重新進入screen

# Step6: 安裝Anaconda

因為python3 kernal一直裝不起來，所以我直接裝anaconda

參考https://linuxize.com/post/how-to-install-anaconda-on-centos-7/

```sh
cd /tmp
curl -O https://repo.anaconda.com/archive/Anaconda3-5.3.1-Linux-x86_64.sh
sha256sum Anaconda3-5.3.1-Linux-x86_64.sh
```

這時會輸出一個sha256的編碼，到這個[網頁](http://docs.anaconda.com/anaconda/install/hashes/lin-3-64/)查看是否有對應

```sh
bash Anaconda3-5.3.1-Linux-x86_64.sh
```

接著就一直按Enter跟yes(也會問你要不要裝VScode，不是必要就不裝)

```sh
source ~/.bashrc
```

> Note: 如果出現conda: command not found，參考這篇[stackoverflow](https://stackoverflow.com/questions/18675907/how-to-run-conda)的解答