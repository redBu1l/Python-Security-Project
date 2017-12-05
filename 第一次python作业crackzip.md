#今天学习了一下暴力破解zip压缩文件的密码口令的python脚本方法
#原来用的比较多的是工具，难得有机会亲手实现一把源代码，感觉还
#是很有收获的，主要是利用了python的zipFile库的extract方法输入
#字典中的password，如果成功则返回为真；
#同时还体会了命令行下输入被破解zip文件和字典文件，感觉到了hashc
#at和hydra的味道，主要是利用optparser库；另外还体验到了多线程
#的编程方法
#在写python脚本前还，安装了与kali虚机对接的xshell客户端，而且
#通过生成公钥证书对的方式解决了免密码登录的方式，在客户端生成
#秘钥证书并上传到kali的~/.ssh/authorized_keys文件中，并在kali
#的？etc/sshd/sshd_config中修改对应的允许证书认证的开关和证书
#所在目录，同时可以修改取消掉原有的运行ssh口令方式登录和root
#登录，需要进行servie ssh restart

import zipfile
import optparse
from threading import Thread
def extractFile(zFile,password):
	try:
		zFile.extractall(pwd=password)
		print '[+] Found password ' +password+ '\n'
	except:
		pass

def main():
	parser=optparse.OptionParser("usage%prog "+\
	"-f <zipfile> -d <dictionary>")
	parser.add_option('-f',dest='zname',type='string',\
	help='specify zip file')
	parser.add_option('-d',dest='dname',type='string',\
	help='specify dictionary file')
	(options,args)=parser.parse_args()
	if (options.zname==None)|(options.dname==None):
		print parser.usage
		exit(0)
	else:
		zname=options.zname
		dname=options.dname
	zFile=zipfile.ZipFile(zname)
	passFile=open(dname)
	for line in passFile.readlines():
		password=line.strip('\n')
		t=Thread(target=extractFile,args=(zFile,password))
		t.start()
if __name__=='__main__':
	main()

