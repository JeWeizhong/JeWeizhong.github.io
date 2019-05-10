---
title: python3 标准库系列(一)---email模块
categories: 
  - python3
tags:
  - 编程语言
  - python3
  
date: 2018-11-02
mathjax: true

---

自己写的一个发邮件的类，自己用的时候，省的自己再到处翻教程

```Python

#-*-coding:utf-8-*-

import smtplib
import sys
import re
import os

from email.mime.multipart import MIMEMultipart
from email.mime.text import MIMEText
from email.mime.image import MIMEImage
from email import encoders
from email.header import Header
from email.utils import parseaddr, formataddr

'''
发送邮件：
文本，网页，附件，图片
'''

class Sendemail():


    def __init__(self,mailToList,mailHost,mailUser,mailPass,sub):
        self.mailToList = mailToList  # 邮件接收方的邮件地址，传入的必须是list类型
        self.mailHost = mailHost    # 邮件传送协议服务器: smtp.qiye.163.com
        self.mailUser = mailUser  # 邮件发送方的邮箱账号: xxxx@xxxx.org
        self.mailPass = mailPass  # 邮件发送方的邮箱密码: ****
        self.sub = sub # 邮件名
        #self.content = content # 内容
        #self.subtype = subtype # 发送类型 plain(文字)

    def _formatAddr(self,s):
        '''
            处理发件人的名字，防止有带中文名的发件人
        '''
        name, addr = parseaddr(s)
        return formataddr((Header(name, 'utf-8').encode(), addr))

    def sendEmail(self,content,subtype):
        msg = MIMEMultipart('mixed') #　默认就是　mixed类型
        me = re.split('@',self.mailUser)[0]+"<"+self.mailUser+">"
        msg['Subject'] = self.sub
        msg['From'] = self._formatAddr(me)
        msg['To'] = ','.join(self.mailToList)  # 多个收件人以“,”分隔
        text = MIMEText(content, _subtype=subtype, _charset='utf-8')
        msg.attach(text)
        
        try:
            server = smtplib.SMTP()
            server.connect(self.mailHost)
            server.login(self.mailUser, self.mailPass)
            server.sendmail(me, self.mailToList, msg.as_string())
            server.quit()
            print("发送成功")
            return True
        except Exception as e:
            print (e)
            return False


    def sendFile(self,file):
        '''
        发送附件'''
        msg = MIMEMultipart('mixed') #　默认就是　mixed类型
        me = re.split('@',self.mailUser)[0]+"<"+self.mailUser+">"
        msg['Subject'] = self.sub
        msg['From'] = self._formatAddr(me)
        msg['To'] = ','.join(self.mailToList)
        
        sendfile=open(file,'rb').read()
        text_att = MIMEText(sendfile, 'base64', 'utf-8') 
        text_att["Content-Type"] = 'application/octet-stream'  
        # 设置附件名字，如果是windows下的中文文件名需要以gbk编码，这里默认是再Linux上的中文名字的文件
        text_att.add_header('Content-Disposition', 'attachment', filename=("utf-8", "",os.path.basename(file)))
        #return text_att
        
        msg.attach(text_att)
        try:
            server = smtplib.SMTP()
            server.connect(self.mailHost)
            server.login(self.mailUser, self.mailPass)
            server.sendmail(me, self.mailToList, msg.as_string())
            server.quit()
            print("发送成功")
            return True
        except Exception as e:
            print (e)
            return False
        

def main():
    
    mailToList = ["xxx@xx.com","xxx@xxx.com"]  # 邮件接收方的邮件地址，传入的必须是list类型
    mailHost = "smtp.qiye.163.com"    # 邮件传送协议服务器: smtp.qiye.163.com
    mailUser = "xxx@xxx.com"  # 邮件发送方的邮箱账号: xxxx@xxxx.com
    mailPass = "xxxx"  # 邮件发送方的邮箱密码: ****
    sub = "测试邮件" # 邮件名
    s = Sendemail(mailToList,mailHost,mailUser,mailPass,sub)
    #content  内容/文件路径
    #subtype  发送类型 plain(文字) html(网页) 正文图片(目前只能实现插入一张图) 附件
    content = "test"
    subtype = "plain"
    # 如果发送的是附件用这个函数，暂时不支持邮件正文中发图片，因为比较麻烦，就是把图片插入网页格式中
    s.sendFile("filepath")

if __name__ == '__main__':
    main()
```
