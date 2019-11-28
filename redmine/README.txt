1. 先安装apache等必要common组件 common.sh

2. 安装rvm和ruby，安装rvm，详见rvm.sh 通过rvm管理来安装ruby，注意查看redmine版本对应的ruby版本, 以安装redmine3.4为例，ruby版本<1.9  ...  2.4>

4. 安装bundler， 也要查看redmine目录下的GemFile,安装对应的bundler版本。以remdine3.4 为例， bundler <1.5 .... 2.0>

5. 安装passenger，通过passenger来部署ruby的web application。详见passenger.sh

6. 配置apache

	1. 配置default.conf,也可以自定义conf，暂时以default为例
         sudo vim /etc/apache2/sites-available/000-default.conf
	<VirtualHost *:80>
	    ServerName 10.200.115.44 

	    # Tell Apache and Passenger where your app's 'public' directory is
	    # 注意这里的redmine目录其实link的是redmine/public
	    DocumentRoot /var/www/html/redmine
            #如何查看ruby的路径:passenger-config about ruby-command
	    PassengerRuby /path-to-ruby

	    # Relax Apache security settings
	    <Directory /var/www/html/redmine>
	      Allow from all
	      Options -MultiViews
	      # Uncomment this if you're on Apache > 2.4:
	      #Require all granted
	    </Directory>
	</VirtualHost>

	2. 配置passenger conf文件
	  sudo vim /etc/apache2/mods-available/passenger.conf
          sudo vim /etc/apache2/mods-available/passenger.load
	  这两个文件的内容通过命令：passenger-install-apache2-module查看。

	3. vim /etc/apache2/port.conf, 更改监听端口80->81, 一般80容易被占用，换成其他端口。
           vim /etc/apache2/apache2.conf, 增加一行 ServerName 10.200.115.44,否则容易报错。


7. redmine config
	rake generate_secret_token
	RAILS_ENV=production rake db:migrate 
	RAILS_ENV=production rake redmine:load_default_data 

8. 重启redmine 
   sudo a2enmod passenger
   sudo service apache2 restart

9. 查看redmine网址:10.200.115.44:81/redmine
