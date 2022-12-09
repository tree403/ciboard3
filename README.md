# CIBoard3

## [MySQL/MariaDB] 설정

<pre>
CREATE USER 'ciboard'@'%' IDENTIFIED BY 'ciboard';
CREATE DATABASE ciboard CHARACTER SET utf8mb4;
GRANT ALL PRIVILEGES ON ciboard.* TO 'ciboard'@'%';
</pre>
