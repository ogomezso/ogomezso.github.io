# Create GKE Cluster
## 2022-04-21

### Status: #idea
### Tags:
[[cfk]] [[k8s]] [[GKE]]

### References:
- #repos [[CFK Playground]]
---

##### Create Cluster Command

~~~bash
gcloud beta container --project "solutionsarchitect-01" clusters create "ogomez-cfk" --region "europe-west2" --no-enable-basic-auth --cluster-version "1.21.9-gke.1002" --release-channel "regular" --machine-type "e2-standard-8" --image-type "COS_CONTAINERD" --disk-type "pd-standard" --disk-size "1000" --metadata disable-legacy-endpoints=true --scopes "https://www.googleapis.com/auth/devstorage.read_only","https://www.googleapis.com/auth/logging.write","https://www.googleapis.com/auth/monitoring","https://www.googleapis.com/auth/servicecontrol","https://www.googleapis.com/auth/service.management.readonly","https://www.googleapis.com/auth/trace.append" --max-pods-per-node "110" --num-nodes "3" --logging=SYSTEM,WORKLOAD --monitoring=SYSTEM --enable-ip-alias --network "projects/solutionsarchitect-01/global/networks/default" --subnetwork "projects/solutionsarchitect-01/regions/europe-west2/subnetworks/default" --no-enable-intra-node-visibility --default-max-pods-per-node "110" --no-enable-master-authorized-networks --addons HorizontalPodAutoscaling,HttpLoadBalancing,GcePersistentDiskCsiDriver --enable-autoupgrade --enable-autorepair --max-surge-upgrade 1 --max-unavailable-upgrade 0 --enable-shielded-nodes --node-locations "europe-west2-a","europe-west2-b","europe-west2-c"
~~~ 
