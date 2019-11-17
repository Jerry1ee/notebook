##  helm 部署 gitlab

### 拉取chart文件夹
```
helm fetch stable/gitlab-ce
```

### 解压到当前文件夹下
```
tar zxvf 要解压的文件 -C ./
```
### 修改valuse.yaml文件

```
## GitLab CE image
## ref: https://hub.docker.com/r/gitlab/gitlab-ce/tags/
##
image: gitlab/gitlab-ce:9.4.1-ce.0

## Specify a imagePullPolicy
## 'Always' if imageTag is 'latest', else set to 'IfNotPresent'
## ref: http://kubernetes.io/docs/user-guide/images/#pre-pulling-images
##
imagePullPolicy: IfNotPresent

## The URL (with protocol) that your users will use to reach the install.
## ref: https://docs.gitlab.com/omnibus/settings/configuration.html#configuring-the-external-url-for-gitlab
##
externalUrl: http://k8s.gitlab.com/

## Change the initial default admin password if set. If not set, you'll be
## able to set it when you first visit your install.
##
gitlabRootPassword: "gitlab123"

## For minikube, set this to NodePort, elsewhere use LoadBalancer
## ref: http://kubernetes.io/docs/user-guide/services/#publishing-services---service-types
##
serviceType: LoadBalancer

## Ingress configuration options
##
ingress:
  annotations:
      # kubernetes.io/ingress.class: nginx
      # kubernetes.io/tls-acme: "true"
  enabled: false
  tls:
      # - secretName: gitlab.cluster.local
      #   hosts:
      #     - gitlab.cluster.local
  url: gitlab.cluster.local

## Configure external service ports
## ref: http://kubernetes.io/docs/user-guide/services/
sshPort: 2222
httpPort: 8089
httpsPort: 4433
## livenessPort Port of liveness probe endpoint
livenessPort: http
## readinessPort Port of readiness probe endpoint
readinessPort: http

## Configure resource requests and limits
## ref: http://kubernetes.io/docs/user-guide/compute-resources/
##
resources:
  ## GitLab requires a good deal of resources. We have split out Postgres and
  ## redis, which helps some. Refer to the guidelines for larger installs.
  ## ref: https://docs.gitlab.com/ce/install/requirements.html#hardware-requirements
  requests:
    memory: 1Gi
    cpu: 500m
  limits:
    memory: 2Gi
    cpu: 1

## Enable persistence using Persistent Volume Claims
## ref: http://kubernetes.io/docs/user-guide/persistent-volumes/
## ref: https://docs.gitlab.com/ce/install/requirements.html#storage
##
persistence:
  ## This volume persists generated configuration files, keys, and certs.
  ##
  gitlabEtc:
    enabled: true
    size: 1Gi
    ## If defined, volume.beta.kubernetes.io/storage-class: <storageClass>
    ## Default: volume.alpha.kubernetes.io/storage-class: default
    ##
    storageClass: nfs
    accessMode: ReadWriteOnce
  ## This volume is used to store git data and other project files.
  ## ref: https://docs.gitlab.com/omnibus/settings/configuration.html#storing-git-data-in-an-alternative-directory
  ##
  gitlabData:
    enabled: true
    size: 10Gi
    ## If defined, volume.beta.kubernetes.io/storage-class: <storageClass>
    ## Default: volume.alpha.kubernetes.io/storage-class: default
    ##
    storageClass: nfs
    accessMode: ReadWriteOnce

## Configuration values for the postgresql dependency.
## ref: https://github.com/kubernetes/charts/blob/master/stable/postgresql/README.md
##
postgresql:
  # 9.6 is the newest supported version for the GitLab container
  imageTag: "9.6"
  cpu: 1000m
  memory: 1Gi

  postgresUser: gitlab
  postgresPassword: gitlab
  postgresDatabase: gitlab

  persistence:
    enabled: true
  ## If defined, volume.beta.kubernetes.io/storage-class: <storageClass>
  ## Default: volume.alpha.kubernetes.io/storage-class: default
  ##
    storageClass: nfs
    accessMode: ReadWriteOnce
    size: 10Gi

## Configuration values for the redis dependency.
## ref: https://github.com/kubernetes/charts/blob/master/stable/redis/README.md
##
redis:
  redisPassword: "gitlab"

  resources:
    requests:
      memory: 1Gi

  persistence:
    enabled: true
  ## If defined, volume.beta.kubernetes.io/storage-class: <storageClass>
  ## Default: volume.alpha.kubernetes.io/storage-class: default
  ##
    storageClass: nfs
    accessMode: ReadWriteOnce
    size: 10Gi
```


### 创建四个pv 以便自动生成的pvc挂载
```
kubectl apply -f gitlab-pv-1.yml,gitlab-pv-2.yml,gitlab-pv-3.yml,gitlab-pv-4.yml
```

### 踩坑
- helm fetch下来的 template中deployment.yaml 都用不了，要把版本 extensions/v1beta1 改成 apps/v1，此外，要根据报错信息依次向各个deployment.yaml文件的spec中加入 
```
  selector:
    matchLabels:
      app: "gitlab-gitlab-ce"
```
其中app: 后面的内容要根据报错信息填写。。。

### 采用helm进行安装 
 helm install --name gitlab --namespace gitlab -f values.yaml ./
