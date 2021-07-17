# kubernetes-flask-app
Deploy flask app using kubernetes


# remote ssh multipass instance with password

```
sudo nano /etc/ssh/sshd_config
=> edit PasswordAuthentication yes

sudo service sshd reload
```


# lỗi docker expose trên windows nhưng k access đc
trong app.py, lúc đầu tác giả để ip là 127.0.0.1 => fai set thành 0.0.0.0 mới đc
https://stackoverflow.com/a/52648244