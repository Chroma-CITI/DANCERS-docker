# DANCERS-docker
Docker containers to install and run the DANCERS co-simulator.

## Install
We provide the Dockerfile(s) to install the DANCERS co-simulator inside a container.
1. The first step is to build a docker image from the provided Dockerfile. Example :
```
docker build env_viragh_ns3 -t viragh_ns3_img --build-arg USERNAME=teatime
```
This step requires an internet connexion and can take several minutes.
2. Authorize docker to open windows on your host's X server:
```
xhost +local:docker
```
3. Once generated, you can create a docker container and connect to it with the following command (explained below):
```
docker run --rm -it -e "TERM=xterm-256color" -e "DISPLAY=$DISPLAY" -v "/tmp/.X11-unix:/tmp/.X11-unix" -v "$HOME/.Xauthority:/root/.Xauthority:ro" --network host viragh_ns3_img
```
`--rm` destroys the container at the end of utilization, kind of hazardous if you quit the container unexpetedly, you can remove it safely</br>
`-it` is for interactive, it opens a shell inside the container once it runs</br>
`-e "TERM=xterm-256color"` defines the terminal to be used in docker (colours, yey !)</br>
`-e "DISPLAY=$DISPLAY"` is necessary to let the container know to which display it should open windows</br>
`-v "/tmp/.X11-unix:/tmp/.X11-unix"` mounts a volume to the X server running in the host (allows the container to find your X server)</br>
`-v "$HOME/.Xauthority:/root/.Xauthority:ro"` Give the container access to create windows with the X server of the host</br>
`--network host` gives the container access to the host's networking

## Use
To launch your first simulation, run the following tmux script from the container:
```
./sim_ws/src/tmux_scripts/start_dancers_viragh.sh
```
I opens a tmux session with several terminals and the windows of the Viragh's simulator GUI. The tmux session:
**top-left** : Viragh's simulator<\br>
**top-right** : DANCERS's Coordinator module<\br>
**bottom-left** : ns-3<\br>
**bottom-right** : Connector module for Viragh's simulator<\br>

Note: The tmux prefix has been changed to Ctrl - a (instead of default Ctrl - b)

Press spacebar to launch the simulation.
