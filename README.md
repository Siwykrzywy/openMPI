# openMPI
## Wprowadzenie
Projekt ten został wykonany w ramach zaliczenia zajęć z przedmiotu PWiR. Zawarto w nim krótką instrukcję jak krok po kroku stworzyć kontener Docker, uruchomić środowisko openMPI oraz zainstalować potrzebne do tego oprogramowanie. Kontener pozwala na obsługę serwera OpenSSH, który umożliwia korzystanie z wielu kontenerów połączonych ze sobą na raz. Projekt został zrealizowany na gotowym obrazie systemu ubuntu pobranym ze strony osboxes.org

## Budowa kontenera
Pierwszym krokiem będzie sklonowanie tego repozytorium: https://github.com/Siwykrzywy/openMPI<br/>
Do budowy kontenera wykorzystano polecenie docker build. Na obrazie zainstalowany został git, docker, openssh, openMPI oraz wszystko co po drodze okazało się potrzebne do realizacji projektu.

Gotowy obraz został umieszczony w repozytorium: https://hub.docker.com/repository/docker/siwykrzywy/openmpi

## Przygotowanie klastra HPC
1. Pobieramy obraz dockera z repozytorium komendą:
```
sudo docker pull Siwykrzywy/openMPI:last
```
2. Następnie przechodzimy do katalogu "openMPI", który właśne został sklonowany.
Wykonujemy w nim komendę która utworzy klaster n = 5 klientów oraz jeden serewer mpi_head = 1. mpi_head jest hostem i udostępnia serwer SSH dla mpi_node który jest klientem.
```
sudo docker-compose scale mpi_node=5 mpi_head=1
```
![image](https://user-images.githubusercontent.com/28909864/107068201-9b411100-67e0-11eb-91ae-bf5c182c2289.png)
![image](https://user-images.githubusercontent.com/28909864/107068486-fe32a800-67e0-11eb-9a6d-d335bf30216d.png)<br/>
3. Kolejnym krokiem będzie sprawdzenie czy kontenery poprawnie się uruchomiły, realizujemy to komendą:
```
sudo docker ps -a
```
W wyniku czego otrzymujemy następne informacje, dane zawarte w kolumnie CONTAINER ID przedadzą się do uruchomienia kontenera<br/>
![image](https://user-images.githubusercontent.com/28909864/107068983-93ce3780-67e1-11eb-9135-1ec2ce525350.png)<br/>
4. Gdy tylko jakaś komenda nie chce zadziałać wystarczy użyć:
```
sudo apt-get update
```
lub zainstalować ją jak na przykład:
```
sudo apt intall openmpi-bin
```
## Uruchomienie programu na klastrze HPC
1. Pierwszym krokiem będzie uruchomienie kontenera mpi_head przy użyciu jego ID oraz komendy:
```
sudo docker exec -it bc08d352274f bin/bash
```
![image](https://user-images.githubusercontent.com/28909864/107069837-c88ebe80-67e2-11eb-92d5-f2682269618c.png)<br/>
Oraz przejść do katalogu:
 ```
cd /usr/bin
```
2. Kolejnym krokiem będzie utwaorzenie pliku mpi.c przy użyciu komendy
```
touch mpi.c
```
3. Następnie należy zainstalować program vim przy użyciu komend:
```
sudo apt-get update
sudo apt-get install vim

```
4. Uruchomić edycję stworzonego pliku używając programu vim:
```
vi mpi.c

```
5. Wpisać kod, następnie otworzyć konsolę wciskajac ESC i wpisać ":x", aby zapisać zmiany i wyjść
![image](https://user-images.githubusercontent.com/28909864/107070447-a6497080-67e3-11eb-90c4-071a90aa8e4b.png)
6. Teraz należy skopliować plik komendą:
```
mpicc -o mpi mpi.c

```
![image](https://user-images.githubusercontent.com/28909864/107070628-e0b30d80-67e3-11eb-911e-66cd72a37b05.png)<br/>
7. Ostatnim krokiem będzie uruchomienie programu mpi, przy użyciu komendy:
```
mpirun --allow-run-as-root -n 5 mpi

```
![image](https://user-images.githubusercontent.com/28909864/107071004-5fa84600-67e4-11eb-89cb-570e13deb77b.png)<br/>
Operacja powiodła się program mpi.c został uruchomiony przez 5 klientów.

## Bibliografia
- https://docs.docker.com/engine/install/ubuntu/
- https://www.researchgate.net/publication/282310578_Building_a_Virtual_HPC_Cluster_with_Auto_Scaling_by_the_Docker
- https://github.com/oweidner/docker.openmpi
- https://mrpycharm.github.io/openmpi-docker.html
- oraz tutoriale z serwisu YT
