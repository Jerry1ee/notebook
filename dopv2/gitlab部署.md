## gitlab 部署
- 需要的文件：https://github.com/dotbalo/k8s/tree/master/gitlab

### 修改对应的配置
- 主要修改每个rc的namespace，使用的持久化存储方式(当前yaml使用的GFS动态存储，pg使用的是NFS，按需修改)
- 修改gitlab-rc.yml里面的env，对应的LDAP信息和SMTP信息等
- 修改traefik的域名
- 创建gitlab
