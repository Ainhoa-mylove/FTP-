from socket import  *
from threading import Thread
import sys
from time import sleep
import os

#定义全局变量
PORT = 8888
ADDR = '127.0.0.1'
a = (ADDR, PORT)
FTP=r'E:\Programing\我的学习\多进程\FTP文件服务器\文件库\\'

class FTP_Server():
    def __init__(self,c,FTP_path):
        self.c=c
        self.path=FTP_path

    def do_list(self):
        files=os.listdir(self.path)
        if not files:
            self.c.send('该文件类别为空！'.encode())
            return
        else:
            self.c.send(b'OK')
            sleep(0.1)
        fs=''
        for file in files:
            if file[0]=='.' and os.path.isfile(self.path+file):
                fs+=file+'\n'
        self.c.send(fs.encode())

    def do_get(self,filename):
        try:
            fd=open(self.path+filename,'rb')
        except Exception:
            self.c.send('文件不存在'.encode())
            return
        else:
            self.c.send(b'OK')
            sleep(0.2)
        #发送文件内容
        while True:
            data=fd.read(1024)
            if not data:
                sleep(0.1)
                self.c.send(b'##')
            self.c.send(data)

    def do_put(self,filename):
        if os.path.exists(self.path+filename):
            self.c.send('该文件已存在')
            return
        self.c.send(b'OK')
        fd=open(self.path+filename,'wb')
        while True:
            data=self.c.recv(1024)
            if data==b'##':
                break
            fd.write(data)
        fd.close()



#客户端请求处理函数
def handle(c):
    cls=c.recv(1024).decode()
    FTP_path=FTP+cls
    ftp = FTP_Server(c,FTP_path)
    while True:
        data=c.recv(1024).decode()
        #如果客户端断开返回data为空
        if not data or data[0]=='Q':
            return
        elif data[0]=='L':
            ftp.do_list()
        elif data[0]=='G':
            filename=data.split(' ')[-1]
            ftp.do_get(filename)
        elif data[0] == 'P':
            filename = data.split(' ')[-1]
            ftp.do_get(filename)


def main():
    s = socket()
    s.setsockopt(SOL_SOCKET, SO_REUSEADDR, 1)
    s.bind(a)
    s.listen(3)
    print('Listen the port 8888...')
    while True:
        try:
            c,addr=s.accept()
        except KeyboardInterrupt:
            print('退出服务程序')
            return
        except Exception as e:
            print(e)
            continue
        print('链接的客户端：',addr)
        #创建线程处理请求
        client=Thread(target=handle,args=(c,))
        client.setDaemon(True)
        client.start()
if __name__ == '__main__':
    main()
