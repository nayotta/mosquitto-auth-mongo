## 安装mosquitto带auth plugin(mongo)

docker pull coldfire84/mosquitto-auth

## mosquitto配置文件

mosquitto.conf

allow_anonymous false
auth_plugin /usr/local/lib/auth-plug.so
auth_opt_backends mongo
auth_opt_mongo_uri mongodb://{uri}:27017

## 启动docker

docker run -d -p 1883:1883 -v {mosquitto.conf目录}:/mosquitto/config/ coldfire84/mosquitto-auth

## 配置mongo

docker pull mogo
docker run -d -p 27017:27017 mongo

创建默认db和collection
use mqGate
db.createCollection('users')


创建用户及acl:
用户密码使用https://github.com/jpmens/mosquitto-auth-plug/tree/master/contrib/golang 计算, mosquitto的docker里也有命令行工具, np -p {密码} 计算密码hash

db.users.insert({
	'username':'test',
	'password':'PBKDF2$sha256$901$WXkNjx3foZoK+lxK$dIP5wiPklfyjBzVC4l+I5NyEM/0gBBM7',
	'superuser':false,
	'topics':
		{'test/#':'rw'}
	})
				
topics是用户acl, test/# 表示对test/后面的多级路径均有权限, r表示读取, w表示写入, topics可以多个权限控制, 比如{{'test/#':'r', 'test/cmd/#','rw'}}

## 客户端测试

手机可使用MQTTool登录

## 日志

docker 日志可以看 AUTHORIZED=1 或者 CACHEDAUTH: 0 表示acl成功