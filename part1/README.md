# Clusterを作成する

## Clusterで利用するService Accountの作成

Cluster内部で利用するデフォルトのService Accountを作成します。
Cloud DatastoreやCloud Storage, Stackdriverにアクセスする時に利用します。

```
gcloud iam service-accounts create gke-worker-default --display-name "gke-worker-default"
```

Service Accountが作られたかは、以下のコマンドで確認できる。

```
gcloud iam service-accounts list
```

権限として、Project Editorを割り当てておく。
実際のシステムではもう少し細かく権限を設定していくが、今回はハンズオン用にざっくり権限を渡している。

```
gcloud projects add-iam-policy-binding {your project id} \
  --member serviceAccount:{your service account} \
  --role roles/editor
```

## GKE Clusterの作成

作成したService Accountを指定して、GKE Clusterを作成する。
ハンズオン用なので、お値段を抑えた小さめの構成で、かつ `--preemptible` を指定しているので、時折シャットダウンされたりする。
GKEの場合は、シャットダウンされても、PodはReCreateしてくれるので、開発環境なら、 `--preemptible` で2台のインスタンスを作っておくと、安く運用できる。

```
gcloud container clusters create "handson-cluster-a2c" \
       --service-account "{your service account}
       --zone "us-central1-a" \
       --username "admin" \
       --machine-type "n1-standard-1" \
       --image-type "COS" \
       --disk-type "pd-standard" \
       --disk-size "10" --preemptible \
       --num-nodes "1" \
       --enable-cloud-logging \
       --enable-cloud-monitoring \
       --addons HorizontalPodAutoscaling,HttpLoadBalancing \
       --no-enable-ip-alias \
       --no-issue-client-certificate
```
