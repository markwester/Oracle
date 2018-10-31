对于以下的对象名称con_res_view，new_user，在实验的时候应该修改为自己的名称。
第1步：以system登录到pdborcl，创建角色con_res_view和用户new_user，并授权和分配空间：
![第一步截图](./Imges/oracle_lab2)
第2步：新用户new_user连接到pdborcl，创建表mytable和视图myview，插入数据，最后将myview的SELECT对象权限授予hr用户。
![第二步截图](./Imges/oracle_lab2_2)
第3步：用户hr连接到pdborcl，查询new_user授予它的视图myview
![第三步截图](./Imges/oracle_lab2_3)
以下样例查看表空间的数据库文件，以及每个文件的磁盘占用情况。
![第四步截图](./Imges/oracle_lab2_4)
![第四步截图](./Imges/oracle_lab2_5)


