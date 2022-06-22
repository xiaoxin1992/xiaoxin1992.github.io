## POD使用

#### 基本的POD资源创建
```YAML
apiVersion: v1
kind: Pod
metadata:
  name: pod-example
spec:
  containers:
  - name: myapp
    image: ikubernetes/myapp:v1
```
然后使用`kubectl apply -f test.yaml`进行创建POD