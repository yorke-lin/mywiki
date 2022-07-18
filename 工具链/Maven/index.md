
# 常用命令

## 将jar包导入本地maven库

`mvn install:install-file -Dfile=jar包的位置 -DgroupId=pom的groupId -DartifactId=pom的artifactId -Dversion=pom的version -Dpackaging=jar`
