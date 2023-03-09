# Assignment-4
Docker Training Assignments

cd /root/example-voting-app/k8s-specifications
[root@ip-172-31-3-120 k8s-specifications]# kubectl apply -f .
deployment.apps/db created
service/db created
deployment.apps/redis created
service/redis created
deployment.apps/result created
service/result created
deployment.apps/vote created
service/vote created
deployment.apps/worker created
[root@ip-172-31-3-120 k8s-specifications]# kubectl get all
NAME                          READY   STATUS              RESTARTS   AGE
pod/db-b54cd94f4-kp8cd        0/1     ContainerCreating   0          13s
pod/redis-868d64d78-hsx4c     0/1     ContainerCreating   0          13s
pod/result-5d57b59f4b-5qj55   0/1     ContainerCreating   0          13s
pod/vote-94849dc97-ms7m4      0/1     ContainerCreating   0          13s
pod/worker-dd46d7584-sxgnl    0/1     ContainerCreating   0          12s

NAME                 TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
service/db           ClusterIP   10.100.38.166    <none>        5432/TCP         13s
service/kubernetes   ClusterIP   10.96.0.1        <none>        443/TCP          24m
service/redis        ClusterIP   10.103.250.63    <none>        6379/TCP         13s
service/result       NodePort    10.99.210.132    <none>        5001:31001/TCP   13s
service/vote         NodePort    10.107.197.102   <none>        5000:31000/TCP   12s

NAME                     READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/db       0/1     1            0           13s
deployment.apps/redis    0/1     1            0           13s
deployment.apps/result   0/1     1            0           13s
deployment.apps/vote     0/1     1            0           13s
deployment.apps/worker   0/1     1            0           12s

NAME                                DESIRED   CURRENT   READY   AGE
replicaset.apps/db-b54cd94f4        1         1         0       13s
replicaset.apps/redis-868d64d78     1         1         0       13s
replicaset.apps/result-5d57b59f4b   1         1         0       13s
replicaset.apps/vote-94849dc97      1         1         0       13s
replicaset.apps/worker-dd46d7584    1         1         0       12s
[root@ip-172-31-3-120 k8s-specifications]# kubectl get po
NAME                      READY   STATUS    RESTARTS   AGE
db-b54cd94f4-kp8cd        1/1     Running   0          5m59s
redis-868d64d78-hsx4c     1/1     Running   0          5m59s
result-5d57b59f4b-5qj55   1/1     Running   0          5m59s
vote-94849dc97-ms7m4      1/1     Running   0          5m59s
worker-dd46d7584-sxgnl    1/1     Running   0          5m58s



 Observation-1:
 =============
  On terminating of vote app pod -
On the front end - vote app old pod vote-94849dc97-ms7m4  name has been removed from wave page updated by new respawned pod. Voting procedure is not affected. 
  Web page is still accepting vote and reflecting on result wave page. 
On the backend - Old vote app vote-94849dc97-ms7m4  pod is terminated New vote app pod vote-94849dc97-mt8ch has been respawned. No pod restart is seen.

[root@ip-172-31-3-120 k8s-specifications]# kubectl delete po vote-94849dc97-ms7m4
pod "vote-94849dc97-ms7m4" deleted

[root@ip-172-31-3-120 k8s-specifications]# kubectl get po
NAME                      READY   STATUS    RESTARTS   AGE
db-b54cd94f4-kp8cd        1/1     Running   0          8m20s
redis-868d64d78-hsx4c     1/1     Running   0          8m20s
result-5d57b59f4b-5qj55   1/1     Running   0          8m20s
vote-94849dc97-mt8ch      1/1     Running   0          110s
worker-dd46d7584-sxgnl    1/1     Running   0          8m19s

Observation-2:
=============
  On terminating of worker pod worker-dd46d7584-sxgnl -
On the front end - Voting procedure is not affected. Voter count remain same.
  Web page is still accepting vote and reflecting on result wave page. 
On the backend - Old worker pod worker-dd46d7584-sxgnl is terminated and New worker pod worker-dd46d7584-8h9qs has been respawned. No pod restart is seen.

[root@ip-172-31-3-120 k8s-specifications]# kubectl delete po worker-dd46d7584-sxgnl
pod "worker-dd46d7584-sxgnl" deleted
[root@ip-172-31-3-120 k8s-specifications]# kubectl get po
NAME                      READY   STATUS    RESTARTS   AGE
db-b54cd94f4-kp8cd        1/1     Running   0          8m55s
redis-868d64d78-hsx4c     1/1     Running   0          8m55s
result-5d57b59f4b-5qj55   1/1     Running   0          8m55s
vote-94849dc97-mt8ch      1/1     Running   0          2m25s
worker-dd46d7584-8h9qs    1/1     Running   0          23s
[root@ip-172-31-3-120 k8s-specifications]#

Observation-3:
=============
 On terminating of db pod db-b54cd94f4-6vt7t -
on front end - Voter app pod vote submission was not getting updated in db and same updated result was not visible at result app pod for some time until new db pod db-b54cd94f4-6vt7t was respawned  .   
on back end - Old db pod db-b54cd94f4-kp8cd was terminated and new db pod db-b54cd94f4-6vt7t was respawned. Also containers of worker pod app and result app has restarted and only after that results have been keeping updated/reflected on result pod web page. Old db data was lost.

[root@ip-172-31-3-120 k8s-specifications]# kubectl delete po db-b54cd94f4-kp8cd
pod "db-b54cd94f4-kp8cd" deleted
[root@ip-172-31-3-120 k8s-specifications]# kubectl get po
NAME                      READY   STATUS    RESTARTS   AGE
db-b54cd94f4-6vt7t        1/1     Running   0          35s
redis-868d64d78-hsx4c     1/1     Running   0          15m
result-5d57b59f4b-5qj55   1/1     Running   1          15m
vote-94849dc97-mt8ch      1/1     Running   0          9m23s
worker-dd46d7584-8h9qs    1/1     Running   1          7m21s
[root@ip-172-31-3-120 k8s-specifications]#

on front-end: vote is not updated on screen
on back-end: result and worker nodes have restarted 
