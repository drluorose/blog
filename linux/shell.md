#这里是各种Shell的合集
##批量添加用户到同一个组
```
#!/bin/bash
groupadd   usergroup
for username in user1 user2 user3
do
	used -G user group $username
	echo "password" | password --stdin $username
done
```