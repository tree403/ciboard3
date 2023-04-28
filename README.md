# CIBoard3

## [MySQL/MariaDB] 설정

<pre>
CREATE USER 'ciboard'@'%' IDENTIFIED BY 'ciboard';
CREATE DATABASE ciboard CHARACTER SET utf8mb4;
GRANT ALL PRIVILEGES ON ciboard.* TO 'ciboard'@'%';
</pre>



## ciboard source

https://github.com/042x-7x/ciboard3-pro.git 

docker-compose 파일을 받으신 후

ciboard 는 

[docker-compose.yml 위치]/ciboard/public_html 아래에 clone 하시면 됩니다.

