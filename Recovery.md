https://www.youtube.com/watch?v=-ImtLXcEna8&t=1s

# scale to 0 replicas so this in the config otherwise argocd will overwrite it

to restore and not have issues with argocd, create a pv.yaml that contains something like the following:
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pvc-9fecbff1-f7b1-4237-90fd-bf2655ad80ef # Must match `volumeName` in PVC
spec:
  storageClassName: longhorn # Match your Longhorn StorageClass name
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain # Important for manual PV/PVC binding
  csi:
    driver: driver.longhorn.io
    fsType: ext4
    volumeHandle: pvc-9fecbff1-f7b1-4237-90fd-bf2655ad80ef # Longhorn volume UUID

then update the pvc to point to that pvc
---
# PostgreSQL Persistent Volume
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mealie-pgdata-pvc
  annotations:
    longhorn.io/recurring-job-group: '["daily-backup"]'
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
  volumeName: pvc-9fecbff1-f7b1-4237-90fd-bf2655ad80ef


then apply the pv and pvc and it should connect and work!