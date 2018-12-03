---

title: ubuntu下svn服务器搭建教程

categories:

- 版本管理工具

tags:

- SVN

abbrlink: af8df786

date: 2018-11-26 16:55:31

---

ubuntu下svn服务器搭建步骤：

1. 下载安装svn:

    ```
    sudo apt update
    sudo apt install subversion
    ```

2. 创建svn版本库：
    
    ```
    mkdir -P /home/ubuntu/svn/repository
    sudo chmod -R 777 /home/ubuntu/svn/repository
    sudo svnadmin create /home/ubuntu/svn/repository
    cd /home/ubuntu/svn/repository
    sudo chmod -R 777 db
    ```

3. 配置svn访问权限：

    1. 配置文件路径：/home/ubuntu/svn/repository/conf/
    2. 配置svnserve.conf文件：
        
        将anon-access，auth-access，password-db，authz-db前面的\#去掉
    
        ```
        anon-access = none
        auth-access = write
        password-db = passwd
        authz-db = authz
        ```

        anon-access : 匿名用户访问权限(有三种类型read，write，none)，分别代表可读，可写，和不可读写
        
        auth-access : 权限用户访问权限(有三种类型read，write，none)，分别代表可读，可写，和不可读写
        
        password-db : 密码文件
        
        authz-db : 权限文件
        
    3. 配置passwd文件：
    
        ```
        ### This file is an example password file for svnserve.
        ### Its format is similar to that of svnserve.conf. As shown in the
        ### example below it contains one section labelled [users].
        ### The name and password for each user follow, one account per line.
        
        [users]
        # harry = harryssecret
        # sally = sallyssecret
        test = 123456
        
        ```
        
        设置用户名和密码：用户名 = 密码
        
    4. 配置authz文件：
    
        ```
        ### This file is an example authorization file for svnserve.
        ### Its format is identical to that of mod_authz_svn authorization
        ### files.
        ### As shown below each section defines authorizations for the path and
        ### (optional) repository specified by the section name.
        ### The authorizations follow. An authorization line can refer to:
        ###  - a single user,
        ###  - a group of users defined in a special [groups] section,
        ###  - an alias defined in a special [aliases] section,
        ###  - all authenticated users, using the '$authenticated' token,
        ###  - only anonymous users, using the '$anonymous' token,
        ###  - anyone, using the '*' wildcard.
        ###
        ### A match can be inverted by prefixing the rule with '~'. Rules can
        ### grant read ('r') access, read-write ('rw') access, or no access
        ### ('').
        
        [aliases]
        # joe = /C=XZ/ST=Dessert/L=Snake City/O=Snake Oil, Ltd./OU=Research Institute/CN=Joe Average
        
        [groups]
        # harry_and_sally = harry,sally
        # harry_sally_and_joe = harry,sally,&joe
        admin = test
        
        # [/foo/bar]
        # harry = rw
        # &joe = r
        # * =
        
        # [repository:/baz/fuz]
        # @harry_and_sally = rw
        # * = r
        
        [test:/]
        @admin=rw
        * = rw
        ```
        
        admin = test : 用户test属于admin权限组
        
        @admin = rw : admin权限组的权限是读和写
        
        \* = rw 所有的组都具有读权限和写权限

4. 启动svn服务器：
    
    ```
    svnserve -d -r /home/ubuntu/svn
    ```
    
5. 测试svn服务器：
    
    ```
    svn checkout svn://www.pcstar.top/repository --username test --password 123456
    ```
