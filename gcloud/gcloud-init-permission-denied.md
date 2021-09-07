## 문제 상황
```
Permission "artifactregistry.repositories.downloadArtifacts" denied 
on resource "projects/janeljs-docker-registry/locations/asia-northeast3/repositories/portfolio" (or it may not exist)
```
## 해결
- sudo를 붙여 재설정해주니 해결되었다.
```
sudo gcloud init
```
