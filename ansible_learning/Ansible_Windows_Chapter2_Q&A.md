# Ansible_Windows_Chapter2_Q&A

+ 1.Q:出现error：

  - ```json
    [root@alma-ansible ansible]# ansible -i windows  all -m win_ping
    10.2.14.88 | UNREACHABLE! => {
        "changed": false,
        "msg": "basic: HTTPSConnectionPool(host='10.2.14.88', port=5986): Max retries exceeded with url: /wsman (Caused by NewConnectionError('<urllib3.connection.HTTPSConnection object at 0x7f8033a65880>: Failed to establish a new connection: [Errno 111] Connection refused'))",
        "unreachable": true
    }
    
    ```

  A:

  ``````
  1. check windows. firewalld. 
  	 先进行防火墙全部关闭进行测试
  2.Winrm 配置检查
  	  winrm set winrm/config/service/auth '@{Basic="true"}'
      winrm set winrm/config/service '@{AllowUnencrypted="true"}'
  3.Windows Server 开启远程时打开了以下按钮：
     
  ```

==![image-20230512132210255](/Users/teemo/Library/Application Support/typora-user-images/image-20230512132210255.png)