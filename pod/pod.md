## POD使用

#### 基本的POD资源创建
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-example
spec:
  containers:
  - name: myapp
    image: ikubernetes/myapp:v1
```
POD创建使用命令`kubectl apply -f test.yaml` 或 `kubectl create -f test.yam;`, 更新同样可使用`kubectl apply`, 建议使用第一种

获取当前POD状态命令 `kubectl get -f test.yaml`或 `kubectl get pod`

 根据元数据过滤`kubectl get -f test.yaml -o custom-columns=NAME:metadata.name,STATUS:status.phase`, 过滤使用`custom-columns`参数

删除POD使用命令 `kubectl delete -f test.yaml` 或者直接删除 `kubectl delete pod [pod-name]`

参数描述
- apiVersion            表示当前使用的API版本
- kind                  表示要创建的资源类型
- metadata              表示资源的元数据，这里可以写标签，作者信息等元数据
- metadata.name         表示POD的名称
- spec.containers       容器相关配置
- spec.containers.name  容器名称
- spec.containers.image 容器使用的镜像

## POD资源对象管理
#### Pod镜像策略（imagePullPolicy）
1. Always: 镜像标签为 "latest"或景象不存在时总是从远程仓库获取景象
2. IfNotPresent: 仅本地仓库没有的时候才会去远程仓库下载景象, 它也是默认POD景象的默认策略
3. Never: 禁止从远程仓库下载景象，只使用本地，哪怕本地没有也不会去下载
   
例如下面配置策略为`Always`
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
spec:
  containerts:
  - name: nginx-test
    image: nginx:latest
    imagePullPolicy: Always
```
#### 端口暴露
参数：
- containerPort <integer>       必选字段， 指定在Pod对象的IP上暴露的容器端口范围(0-65535),使用时应该指定容器应用的正常监听端口
- name  <string>                当前端口的名称， 必须符合IANA_SVC_NAME规范且当前Pod内必须以为， 此名称可被Service资源调用
- protocol                      端口相关的协议， 其值可以是TCP或UDP， 默认为TCP
  
可以通过`kubectl explain pods.spec.containers.ports`  查看帮助

实例
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-test
spec:
  containers:
  - name: myapp
    image: ikubernetes/myapp:v1
    imagePullPolicy: IfNotPresent
    ports:
    - name: http
      containerPort: 80
      protocol: TCP
```

创建实例： `kubectl apply -f test.yaml`

#### 自定义运行应用
在docker中可以通过ENTRYPOINT指令进行定义， 传递参数则通过CMD指令， ENTRYPOINT不存在的时候，可以通过CMD同时指定程序和参数

docker查询运行参数

`docker inspect ikubernetes/myapp:v1 -f {{.Config.Cmd}}`

`docker inspect ikubernetes/myapp:v1 -f {{.Config.Entrypoint}}`

在k8s中通过command字短来指定默认运行的应用程序， 并且同时使用args字段进行参数传递， 他们将覆盖景象中的默认定义

如果仅仅定义了command字段， 那么它将覆盖景象定义的程序以参数， 并以无参数的方式运行应用

实例：
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-custom-command
spec:
  containers:
  - name: myapp
    image: alpine:latest
    command: ["/bin/sh"]
    args: ["-c ", "while true;do sleep 30;done"]
```



