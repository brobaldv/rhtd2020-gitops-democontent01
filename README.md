# rhtd2020-gitops-democontent01

Create a deployment with following content to consume
```
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  labels:
    app: kube-applier
  name: kube-applier
spec:
  progressDeadlineSeconds: 600
  replicas: 1
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      app: kube-applier
  strategy:
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 1
    type: RollingUpdate
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: kube-applier
    spec:
      containers:
      - command:
        - /kube-applier
        env:
        - name: REPO_PATH
          value: /k8s/resources
        - name: LISTEN_PORT
          value: "2020"
        image: vincentbrobald/oc-applier:latest
        imagePullPolicy: Always
        name: kube-applier
        ports:
        - containerPort: 2020
          protocol: TCP
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
        volumeMounts:
        - mountPath: /k8s
          name: git-repo
      - command:
        - /git-sync
        env:
        - name: GIT_SYNC_REPO
          value: https://github.com/brobaldv/rhtd2020-gitops-democontent01
        - name: GIT_SYNC_DEST
          value: resources
        - name: GIT_SYNC_WAIT
          value: "300"
        image: gcr.io/google_containers/git-sync:v2.0.6
        imagePullPolicy: IfNotPresent
        name: git-sync
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
        volumeMounts:
        - mountPath: /git
          name: git-repo
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      serviceAccount: applier
      serviceAccountName: applier
      terminationGracePeriodSeconds: 30
      volumes:
      - emptyDir: {}
        name: git-repo
```
