from socket import *
import sys
from time import sleep
FTP=r'E:\Programing\我的学习\多进程\FTP文件服务器\文件库'
class FTP_Client:
     def __init__(self,sockfd):
         self.sockfd=sockfd

     def do_list(self):
         self.sockfd.send(b'L')#发送请求
         #等待回复
         data=self.sockfd.recv(128).decode()
         #OK表示请求成功
         if data=='OK':
                 #接收文件列表
                 data=self.sockfd.recv(4096)
                 print(data.decode())
         else:
             print(data)

     def do_quit(self):
         self.sockfd.send(b'Q')
         self.sockfd.close()
         sys.exit('谢谢使用')

     def do_get(self,filename):
         #发送请求
         self.sockfd.send(('G '+filename).encode())
         #等待回复
         data=self.sockfd.recv(128).decode()
         if data=='OK':
             fd=open(filename,'wb')
             while True:
                 data=self.sockfd.recv(1024)
                 if data==b'##':
                     break
                 fd.write(data)
             fd.close()
             print('文件下载完毕!!')
         else:
             print(data)

     def do_put(self,filename):
         try:
             f=open(filename,'rb')
         except:
             print('没有该文件')
             return
         filename=filename.split('/')[-1]
         self.sockfd.send(('P'+filename).encode())
         data=self.sockfd.recv(128).decode()
         if data=='OK':
             while True:
                 data=f.read()
                 if not data:
                     sleep(0.1)
                     self.sockfd.send(b'##')
                     break
                 self.sockfd.send(data)
             f.close()
         else:
             print(data)

def request(sockfd):
    ftp=FTP_Client(sockfd)
    while True:
        print('\n==========命令选项===========')
        print('\n========== list  ===========')
        print('\n========== get file==========')
        print('\n========== put file==========')
        print('\n========== quit  ===========')
        cmd=input('输入命令:')
        if cmd.strip()=='list':
            ftp.do_list()
        elif cmd.strip()=='quit':
            ftp.do_quit()
        elif cmd[:3].strip()=='get':
            filename=cmd.split(' ')[-1]
            ftp.do_get(filename)
        elif cmd[:3].strip()=='put':
            filename=cmd.split(' ')[-1]
            ftp.do_put(filename)

def main():
    PORT = 8888
    ADDR = '127.0.0.1'
    a = (ADDR, PORT)
    sockfd=socket(AF_INET,SOCK_STREAM)
    try:
        sockfd.connect(a)
    except Exception as e:
        print(e)
        return
    else:
        print("""
              ********************************
                   Data     File    Image
              ********************************
                 """)
        cls=input('请输入想要的文件种类:')
        if cls not in['data','file','image']:
            print('sorry input Error')
            return
        else:
            sockfd.send(cls.encode())
            request(sockfd)

if __name__ == '__main__':
    main()
