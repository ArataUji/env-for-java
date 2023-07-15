# tomcat:9.0.73-openjdk11のイメージを利用。任意のバージョンでOK。
FROM tomcat:9.0.73-jdk11

# ローカルのソースコードをマウントするためのディレクトリを作成。
# mavenとmysql-clientをインストールしておく。
RUN mkdir /opt/project &&\
    apt update &&\
    apt -y install maven &&\
    apt -y install mysql-client-8.0 &&\
    rmdir webapps &&\
    mv webapps.dist webapps

COPY ./ap_server/settings.xml /etc/maven/settings.xml
COPY ./ap_server/tomcat-users.xml /usr/local/tomcat/conf/tomcat-users.xml