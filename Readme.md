# Boulegueur

A tool to stress Kubernetes cluster and solutions with fun

## Instructions to stress Kubernetes

Download a release for your desktop here : https://github.com/jpca/Boulegueur/releases

As a tank in hostile field, you have to kill opponents to delete pods in a As a tank in hostile field, you have to kill opponents to delete pods in a kubernetes cluster namespace.

Use WSAD keys (or ZQSD for french keyboards) to move, and the spacebar to shoot.

On standalone build:
- Escape key to Quit
- P key to make a screenshot 
    generated png file location depends on the platform 
    - windows: in "Boulegueur_Data" folder at this game binary root
    - macos: inside Boulegeur.app in Contents/Resources/Data folder

## Settings

At first launch you need to specify cluster connection parameters (click the gears button on the start screen)
- for Kubernetes Endpoint, see .kubectl/config file or use command: kubectl -n default get endpoints kubernetes
- for Kubernetes Authentification token, you can :
  - reuse an existing one having rights for get/list/delete verbs on Pods resources on your .kubectl/config file :
  > kubectl get serviceaccount default -o json
  or 
  - create one with the following commands (based on https://app.cnvrg.io/docs/guides/ssh.html#create-the-permissions):
    ```bash
    kubectl apply -f boulegueur-permissions.yaml
    kubectl -n kube-system describe secret $(kubectl -n kube-system  get secret | grep boulegueur- | awk '{print $1}')| grep token:
    ```

## Screenshots

![Start Screenshot](screenshot_start.png?raw=true "Start")

![Menu Screenshot](screenshot_menu.png?raw=true "Menu")

![Playing Screenshot](screenshot_playing.png?raw=true "Playing")

![Gameover Screenshot](screenshot_gameover.png?raw=true "Gameover")

See [Known issues](https://github.com/jpca/Boulegueur/issues)

## Stress test

For your stress test analysis, the cluster calls with date time are available in a daily csv file created in the folder of this game binary

Example file :
```csv
Date;Time;AppId;ResourceType;Metadata.Name;Action;Span
2022-10-01;00:57:15;BOUL;Pod;svclb-kafka-plaintext-0-external-q4b6p;ready;3.31
2022-10-01;00:57:18;BOUL;Pod;svclb-akhq-jtkd7;ready;3.01
2022-10-01;00:57:21;BOUL;Pod;kafka-plaintext-zookeeper-0;ready;3.00
2022-10-01;00:57:24;BOUL;Pod;elasticsearch-master-0;ready;3.00
2022-10-01;00:57:25;BOUL;Pod;svclb-akhq-jtkd7;delete;1.07
2022-10-01;00:57:26;BOUL;Pod;elasticsearch-master-0;delete;1.02
2022-10-01;00:57:47;BOUL;Pod;svclb-akhq-jtkd7;ready;21.93
```

The Action column possible values are :
- ready : the Boulegueur detected the resource as (re)started and ready
- delete : the Boulegueur deleted the ressource

### Replay the stress test

```bash
# define namespace
export K8S_NAMESPACE=default
kubectl config set-context --current --namespace=$K8S_NAMESPACE

# generate stress script from csv file
export BOULEGUEUR_STRESS_LOG=boulegueur-20221001.csv
awk -F ";" '{if (NR!=1) {print "sleep " $7; if ($6!="ready") {print "kubectl " $6 " " $4 " " $5;}}}' $BOULEGUEUR_STRESS_LOG > stress_$BOULEGUEUR_STRESS_LOG.tmp
echo "# Replay $BOULEGUEUR_STRESS_LOG stress test" > stress_$BOULEGUEUR_STRESS_LOG.sh
sed 's/,/\./' stress_$BOULEGUEUR_STRESS_LOG.tmp >> stress_$BOULEGUEUR_STRESS_LOG.sh
rm stress_$BOULEGUEUR_STRESS_LOG.tmp
```

### launch stress script
> sh stress_$BOULEGUEUR_STRESS_LOG.sh

## Disclaimer

DO NOT USE IN PRODUCTION

## Credits

Based on Kubernetes APIs

Made with [Unity 2021.3](https://unity3d.com/fr/get-unity/download),
and Unity assets :
- TopDownEngine
- BestHttp

## Licence

Apache License
Version 2.0, January 2004
http://www.apache.org/licenses/

