FROM centos:7.2.1511

# 初始设置
RUN rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm &&\
    rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm &&\
    yum makecache &&\  
    yum install -y vim tar wget curl bzip2 net-tools lsof sysstat cronie python-setuptools &&\
    yum install php php-fpm nginx1w redis php-mcrypt php-mysqlnd php-gd php-mbstring php-pdo  -y &&\
    yum install perl perl-Data-Dumper libaio* libnuma* -y  &&\
    yum clean all && rm -rf /tmp/* && rm -rf /var/tmp/* &&\    
    easy_install supervisor &&\
    cp -f /usr/share/zoneinfo/Asia/Shanghai /etc/localtime


ENV fpm_conf /etc/php-fpm.d/www.conf
ENV php_ini /etc/php.ini

#copy nginx-site
ADD conf/nginx.conf /etc/nginx/nginx.conf
ADD conf/php_fcgi.conf /etc/nginx/php_fcgi.conf
ADD conf/pathinfo.conf /etc/nginx/pathinfo.conf
ADD conf/nginx-site.conf /etc/nginx/conf.d/nginx-site.conf

#copy zendGuardLoader
ADD deploy/ZendGuardLoader.so /usr/lib64/php/modules/ZendGuardLoader.so
ADD conf/Zend.ini /etc/php.d/Zend.ini


# tweak php-fpm and zend config
RUN adduser www &&\
    sed -i \        
    -e "s/pm = dynamic/pm = static/g" \    
    -e "s/pm.max_children = 50/pm.max_children = 5/g" \    
    -e "s/user = apache/user = www/g" \   
    -e "s/group = apache/group = www/g" \      
    ${fpm_conf}  &&\
    sed -i \
    -e "s/post_max_size = 8M/post_max_size = 50M/g" \    
    -e "s/upload_max_filesize = 2M/upload_max_filesize = 50M/g" \  
    -e "s/display_errors = Off/display_errors = On/g" \
    ${php_ini}



#copy supervisord
RUN rm -Rf /etc/supervisord.conf
ADD conf/supervisord.conf /etc/supervisord.conf



# Add Scripts
RUN rm -Rf /start.sh
ADD scripts/start.sh /start.sh
RUN chmod 755 /start.sh && chown -Rf www.www /var/lib/nginx



# EXPOSE 443 80 22
EXPOSE 80

CMD ["/start.sh"]

