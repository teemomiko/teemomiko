# ansible 

+ Ansible 配置文件 

```yaml
[defaults]
hostfile       = /usr/local/etc/ansible/hosts
library        = /usr/local/share/ansible
remote_tmp     = $HOME/.ansible/tmp
pattern        = *
forks          = 128
poll_interval  = 15
sudo_user      = root
transport      = smart
remote_port    = 22
host_key_checking = False
sudo_exe = sudo
timeout = 10
ansible_managed = Ansible managed: {file} modified on %Y-%m-%d %H:%M:%S by {uid} on {host}
action_plugins     = /usr/local/share/ansible_plugins/action_plugins
callback_plugins   = /usr/local/share/ansible_plugins/callback_plugins
connection_plugins = /usr/local/share/ansible_plugins/connection_plugins
lookup_plugins     = /usr/local/share/ansible_plugins/lookup_plugins
vars_plugins       = /usr/local/share/ansible_plugins/vars_plugins
filter_plugins     = /usr/local/share/ansible_plugins/filter_plugins

```





# 2⃣️、使用Ansible 管理windows主机



- 2.1   安装环境🤔   

	1. Alma Linux  for ansible server
	
	2. Win10 for windows test


+ 2.2 安装 

  - a.安装ansible 

    ```bash
    yum install ansible
    ```

    - b.   安装Python 环境

      ⚠️ 千万不要使用yum install  python3.x 去进行安装，否则后续会在安装pywinrm时报错。

      ```bash
      cd /tmp
      wget https://www.python.org/ftp/python/3.8.1/Python-3.8.1.tgz
      tar -zxvf Python-3.8.1.tgz  -C /usr/local/
      ./configure
      make -j 8 && make install
      python3 --version. # check for isntalled 
      ```

    - c.安装winrm

      winrm [windows remote manager ]用于Windows 远程进行管理。

      Ansible 借用winrm 进行远程管理windows机器 。

      ```bash
      pip3 install pywinrm
      # if install error. u should update pip verison
      pip3 install update 
      # or check by error 
       dnf install redhat-rpm-config gcc libffi-devel python3-devel     openssl-devel cargo pkg-config
      ```

    - d.  Windows 主机上配置

      ```powershell
      #配置检查
       $ansibleconfigurl = "https://raw.githubusercontent.com/ansible/ansible/devel/examples/scripts/ConfigureRemotingForAnsible.ps1"
      $ansibleconfig = "$env:temp\ConfigureRemotingForAnsible.ps1"
      (New-Object -TypeName System.Net.WebClient).DownloadFile($ansibleconfigurl, $ansibleconfig)
      powershell.exe -ExecutionPolicy ByPass -File $ansibleconfig
      # 设置ps 允许执行
      Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Force 
      #winrm 配置
        winrm set winrm/config/service/auth '@{Basic="true"}'
      winrm set winrm/config/service '@{AllowUnencrypted="true"}' 
      
      #开启监听端口
       netstat -an | Select-String -Pattern '5986'
      
      ```

  + e.ansible 配置文件配置

    在 /etc/ansible/下新建windows 配置文件，示例如下：

    ```ini
    [windows]
    10.x.x.x
    
    [windows:vars]
    ansible_user="administrator"
    ansible_password="password"
    ansible_port=5986
    ansible_connection="winrm"
    ansible_winrm_server_cert_validation=ignore
    ansible_winrm_transport=basic
    ```

    执行ping测试：

    ```bash
    ansible -i windows  all -m win_ping
    
    ```

    ```json
    10.3.1.68 | SUCCESS => {
        "changed": false,
        "ping": "pong"
    }
    ```

    
