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



# docker
```
sudo docker build -t sontt/k8s-flask:lastest .
sudo docker run -it --rm -d -p 8080:5000 --name k8s-flask sontt/k8s-flask:lastest
```


# ssh tunnel
trong project này, mình đã dùng multipass instance để run web.
Vì instance này ko có public ip, nên để truy cập đc web từ browser local, fai tạo ssh tunnel
(tham khảo https://www.ssh.com/academy/ssh/tunneling/example)
(tham khảo https://linuxize.com/post/how-to-setup-ssh-tunneling/)

```
ssh -L localhost:8080:172.24.89.176:8080 ubuntu@172.24.89.176
```

