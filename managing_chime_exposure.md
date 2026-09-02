

## Exposure system
Running on `frbadmin@frb-vsop`, to start the exposure service do  
```bash

# pull image 
docker pull chimefrb/frb-exposure:v1.1.0

docker stack deploy -c ~/docker_compose_exposure.yaml exposure # deploys yaml as docker stack

# to make more instances after it is running you can do 
docker service scale exposure_exposure=4
# or you can put it in the yaml
```