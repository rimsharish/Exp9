---server.py
import socket, threading, os

def handle_client(c):
    filename=c.recv(1024).decode()
    base=os.path.dirname(os.path.abspath(__file__))
    path=os.path.join(base,filename)
    if os.path.isfile(path):
        with open(path,"r") as f:
            data=f.read()
        c.send(data.encode())
    else:
        c.send("File not found".encode())
    c.close()

s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
s.bind(("0.0.0.0",8080))
s.listen(5)
print("File Server Running...")

while True:
    c,a=s.accept()
    print("Connected:",a)
    threading.Thread(target=handle_client,args=(c,)).start()

    ---client.py

import socket

c=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
ip=input("Enter server IP: ")
c.connect((ip,8080))
filename=input("Enter filename: ")
c.send(filename.encode())
data=c.recv(4096)
print("File Content:")
print(data.decode())
c.close()
