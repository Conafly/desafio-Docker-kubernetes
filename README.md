# Desafio Docker

**Questão 1:** `c8347edb41ad`

**Questão 2:** `docker run -d -p 8080:80`

    CONTAINER ID   IMAGE        COMMAND                  CREATED         STATUS         PORTS                                   NAMES
    f1ef31cdc71d   nginx:1.22   "/docker-entrypoint.…"   6 seconds ago   Up 5 seconds   0.0.0.0:8080->80/tcp, :::8080->80/tcp   reverent_rosalind

**Questão 3:** `docker run -d -v /home/$USER/questão3/default.conf:/etc/nginx/conf.d/default.conf:ro -p 8080:90 nginx:1.22`

    "[...]
     listen       90; #Modificação feita no arquivo de configuração padrão do nginx, mudando a porta
     listen  [::]:90;
     [...]"

**Questão 4:**

    Dockerfile:
    FROM python:3-alpine
    WORKDIR /app
    COPY   . .
    CMD ["python", "app/index.py"]

**Questão 5:** `docker run -d -m 128MB --cpus=".5" nginx`

**Questão 6:** `docker system prune`

**Questão 7:** `docker history --no-trunc <IMAGE-ID>`

# Desafio Kubernetes
======

**Questão 1:** `kubectl logs -n meusite -l app=ovo serverweb | grep -i erro`

**Questão 2:**

```
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: meu-spread
  namespace: kube-system
  labels:
    desafio: questao-2
spec:
  selector:
    matchLabels:
      name: meu-spread
  template:
    metadata:
      labels:
        name: meu-spread
    spec:
      containers:
      - name: meu-spread
        image: nginx:latest
```
**Questão 3:**
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: meu-webserver
  labels:
    app: deploy-q3
spec:
  replicas: 1
  selector:
    matchLabels:
      app: pod-q3
  template:
    metadata:
      labels:
        app: pod-q3
    spec:
      volumes:
      - name: volume-q3
        emptyDir: {}
      containers:
      - name: nginx
        image: nginx:latest
        volumeMounts:
        - mountPath: /usr/share/nginx/html
          name: volume-q3
      initContainers:
      - name: init-nginx
        image: alpine
        volumeMounts:
        - mountPath: /app
          name: volume-q3
        command: ["/bin/sh"]
        args: ["-c", "echo 'HelloGetup' > /app/index.html"]
```
**Questão 4:**
```
apiVersion: apps/v1
kind: Deployment
metadata:
    labels:
    app: meuweb
  name: meuweb
spec:
  replicas: 1
  selector:
    matchLabels:
      app: meuweb
  strategy: {}
  template:
    metadata:
      labels:
        app: meuweb
    spec:
      containers:
      - image: nginx:1.16
        name: nginx
      nodeSelector:
        dedicated: master
      tolerations:
      - key: dedicated
        operator: Equal
        value: master
        effect: NoSchedule
```
**Questão 5:** `kubectl set image deployment/meuweb nginx=nginx:1.19`

**Questão 6:** 
```
    helm repo add k8s-ingress-nginx https://kubernetes.github.io/ingress-nginx
    helm install ingress-nginx k8s-ingress-nginx/ingress-nginx \
    --set controller.hostPort.enable=true \
    --set controller.service.type=NodePort \
    --set controller.updateStrategy.type=Recreate
```
**Questão 7:** 
```
    kubectl create deployment pombo --image nginx:1.11.9-alpine --replicas 4
    kubectl set image deployment/pombo nginx=nginx:1.16 $$ kubectl annotate deploy pombo kubernetes.io/change-cause='change image to 1.16'
    kubectl set image deployment/pombo nginx=nginx:1.19 && kubectl annotate --overwrite deploy pombo kubernetes.io/change-cause='change image to 1.19'
    kubectl rollout history deployment pombo
    kubectl rollout undo deployment pombo --to-revision 1
    kubectl expose deployment pombo --type NodePort --port 80 --target-port 8080
    kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.1.1/deploy/static/provider/baremetal/deploy.yaml
    kubectl create ingress web --rule host/path=pombo:8080
```
**Questão 8:** 
```
    kubectl create deployment guardaroupa --image redis
    kubectl expose deployment guardaroupa --port 6379
```
**Questão 9:**
```
apiVersion: v1
kind: Service
metadata:
  name: meusiteset-svc
  labels:
    app: nginx
spec:
  ports:
  - port: 80
    name: meusiteset
  clusterIP: None
  selector:
    app: nginx
---
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: meusiteset
  namespace: backend
spec:
  selector:
    matchLabels:
      app: nginx
  serviceName: "meusiteset-svc"
  replicas: 3
  minReadySeconds: 10
  template:
    metadata:
      labels:
        app: nginx
    spec:
      terminationGracePeriodSeconds: 10
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
          name: meusiteset
        volumeMounts:
        - name: data
          mountPath: /data
  volumeClaimTemplates:
  - metadata:
      name: data
    spec:
      accessModes: [ "ReadWriteOnce" ]
      storageClassName: "my-storage-class"
      resources:
        requests:
          storage: 1Gi
```
**Questão 10:**
```
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: balaclava
    backend: balaclava
    minhachave: semvalor
  name: balaclava
  namespace: backend
spec:
  replicas: 2
  selector:
    matchLabels:
      app: balaclava
  template:
    metadata:
      labels:
        app: balaclava
        backend: balaclava
        minhachave: semvalor
    spec:
      containers:
      - image: redis
        name: redis
```
**Questão 11:** `kubectl get svc -A -o wide| grep LoadBalancer`

**Questão 12:** 
```
kubectl create secret generic meusegredo --namespace segredosdesucesso --from-literal=segredo=azul --from-literal=chave_secreta=aW5ncmVzcy1uZ2lueCAgIGluZ3Jlc3MtbmdpbngtY29udHJvbGxlciAgICAgICAgICAgICAgICAgICAgICAgICAgICAgTG9hZEJhbGFuY2VyICAgMTAuMjMzLjE3Ljg0ICAgIDE5Mi4xNjguMS4zNSAgIDgwOjMxOTE2L1RDUCw0NDM6MzE3OTQvVENQICAgICAyM2ggICBhcHAua3ViZXJuZXRlcy5pby9jb21wb25lbnQ9Y29udHJvbGxlcixhcHAua3ViZXJuZXRlcy5pby9pbnN0YW5jZT1pbmdyZXNzLW5naW54LGFwcC5rdWJlcm5ldGVzLmlvL25hbWU9aW5ncmVzcy1uZ
```
**Questão 13:** `kubectl create configmap configsite -n site --from-file index.html`

**Questão 14:**
```
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: meudeploy
  name: meudeploy
  namespace: segredosdesucesso
spec:
  replicas: 1
  selector:
    matchLabels:
      app: meudeploy
  template:
    metadata:
      labels:
        app: meudeploy
    spec:
      containers:
      - image: nginx:latest
        name: nginx
        volumeMounts:
        - name: xiforinfola
          mountPath: "/app"
          readOnly: true
      volumes:
      - name: xiforinfola
        secret:
          secretName: meusegredo
          optional: true
```
**Questão 15:**
```
apiVersion: v1
kind: Pod
metadata:
  name: depconfigs
  labels:
    app: depconfigs
spec:
  containers:
  - name: depconfigs
    image: nginx:latest
    ports:
    - containerPort: 80
    volumeMounts:
    - name: foofighters
      mountPath: "/usr/share/nginx/html"
      readOnly: true
  volumes:
  - name: foofighters
    configMap:
      name: configsite
```
**Questão 16:**
```
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    chaves: secretas
  name: meudeploy-2
  namespace: segredosdesucesso
spec:
  replicas: 1
  selector:
    matchLabels:
      chaves: secretas
  template:
    metadata:
      labels:
        chaves: secretas
    spec:
      containers:
      - image: nginx:1.16
        name: nginx
        envFrom:
        - secretRef:
            name: meusegredo
```
**Questão 17:**
```
     kubectl create namespace cabeludo
     kubectl create deployment cabelo --image nginx:latest -n cabeludo
     kubectl create secret generic acesso -n cabeludo --from-literal username=pavao --from-literal password=asabranca
     kubectl edit deploy cabelo -n cabeludo
     [...] # parte adicionada ao escopo do containers do deploy
     env:
     - name: USUARIO
       valueFrom:
         secretKeyRef:
           name: acesso
           key: username
     - name: SENHA
       valueFrom:
         secretKeyRef:
           name: acesso
           key: password
     [...]
```
**Questão 18:**
```
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: redis
  name: redis
  namespace: cachehits
spec:
  replicas: 1
  selector:
    matchLabels:
      app: redis
  template:
    metadata:
      labels:
        app: redis
    spec:
      containers:
      - image: redis
        name: redis
        volumeMounts:
        - mountPath: /data/redis
          name: app-cache
      volumes:
      - name: app-cache
        emptyDir: {}
```
**Questão 19:** `kubectl scale --replicas=10 deploy/basico -n azul`

**Questão 20:** `kubectl autoscale deployment site -n frontend --min=2 --max=5 --cpu-percent=90`

**Questão 21:** `kubectl get secret piadas -n meusegredos -o jsonpath="{.data.segredos}" | base64 --decode`

**Questão 22:** `kubectl cordon k8s-worker1`

**Questão 23:** `kubectl drain k8s-worker1 --ignore-daemonsets --force`

**Questão 24**: Através do uso de StaticPods pode-se garantir a criação e a manuteção de pods em um determinado node. Ele é gerenciado pelo Kubelet que lê os manifestos que se encontram na pasta especificada no arquivo de configuração do Kubelet.

**Questão 25**: A criação pode ser feita através do
`kubectl create serviceaccount userx -n developer` 
e será necessário ter uma role e uma rolebinding para garantir as devidas permissões, podendo ser realizadas através do
```
kubectl create role userx-role -n developer --verb='*' --resource=pods,deployments \
kubectl create rolebinding userx-rolebing -n developer --role userx-role --serviceaccount developer:userx
```
**Questão 26:**
```
     openssl genrsa -out jane.key 2048 \
     openssl req -new -key jane.key -out jane.csr \
     cat jane.csr | base64 | tr -d "\n" #comando pra gerar o código para request no formato de 1 linha
```
```
     cat <<EOF | kubectl apply -f -
     apiVersion: certificates.k8s.io/v1
     kind: CertificateSigningRequest
     metadata:
       name: jane
     spec:
       request: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0KTUlJQ2lqQ0NBWElDQVFBd1JURUxNQWtHQTFVRUJoTUNRVlV4RXpBUkJnTlZCQWdNQ2xOdmJXVXRVM1JoZEdVeApJVEFmQmdOVkJBb01HRWx1ZEdWeWJtVjBJRmRwWkdkcGRITWdVSFI1SUV4MFpEQ0NBU0l3RFFZSktvWklodmNOCkFRRUJCUUFEZ2dFUEFEQ0NBUW9DZ2dFQkFNQzVjYU51M1BNdlJ5ZE9Pc21vL3B6ZmhDYWxZajhwUlRMWGhoaDYKUmZuQ3FhQlI2L3RvNGlHTitBQ0JZc2g5cnlUZm4wZTgyVW1nTXZoZUxOZFJjVi8xVytseXNnclN3YnZGR2xvTwo2ZEJzelBUM2RkY1BtWGY5Y0RleW1mcmVIREVrZkQvbFk0N2phR1NzUEdUQ3c3MFlyZG93QkVvVnlxVXZ2MXYzClE1LzY3VUxlbUdkNXM1VmxENG5TU0t2Vm1OZEVzQjVFbUNzcERvY1duY0ZuRHBVOUVheEg1b281cCtrT3BRc1oKN0U4Um1UR3BRK1lsZVFRbnZzWUlNTmgzdzVXQ0FqSVRxMlRWOW5Xa0RPY04wQ0hKM09HQTdxdEhkOS9UVEpVVwp1OEpPdkIzeUFNY3gyaGxtelZVa2ZvR1pkc1Fod0cxZGs4VUEyY3ZIZ0lwUEhCMENBd0VBQWFBQU1BMEdDU3FHClNJYjNEUUVCQ3dVQUE0SUJBUUJkcC9lOUpiTFNYWUE1dlB1SkFuRktCaFlZdWI2c2V6T3h5U240eGljam9NNlcKaHBTRVNIUzlNeWtqbGIyVG8wSnY1cXNxZyt0cUFYdnYyUUo1ZmtiZXhsUHpWV0JzbEJ4M2JTVkUybHBaRjlhbgpTVGFMeGJyYzkyRlAwTGpBdEk0OFlrcnVGU0pWM1pRb2hoSHN5L2k4WCt3bnYwWGUzS2pEZ0N2dFRldTBiVGFKCm5UdDQ1aDlMY0h0Z2FhNDROYyt3R043bncwU1RKNmFXdGsvU1N6R0ViVTlnYXFLeS9neE8xeUFBdUVHem9IMmkKeFk0dEtWRlh0MW4yL281VEg4elF5anNTMWRzQklmNXFQekVybUV2SURia2ZyYlloMEZYdVdIYUFXQ1FaYlQwdQptVVhRZ2cyNlZDMWdiZk1tdnNzN1U0WmU0REVXRlVzbVRpV2xPWEFSCi0tLS0tRU5EIENFUlRJRklDQVRFIFJFUVVFU1QtLS0tLQo=
       signerName: kubernetes.io/kube-apiserver-client
       expirationSeconds: 86400
       usages:
       - client auth
```
```
     kubectl create role jane-role -n frontend  --verb=list  --resource=pods \
     kubectl create rolebinding frontend-biding-jane -n frontend --role=jane-role --user=jane \
     kubectl certificate approve jane
```
**Questão 27:** `Kubectl get pods -n kube-system | grep -E "scheduler|controller-manager|etcd"`
