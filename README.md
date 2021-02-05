# openMPI
## Wprowadzenie
Projekt ten został wykonany w ramach zaliczenia zajęć z przedmiotu PWiR. Zawarto w nim krótką instrukcję jak krok po kroku stworzyć kontener Docker, uruchomić środowisko openMPI oraz zainstalować potrzebne do tego oprogramowanie. Kontener pozwala na obsługę serwera OpenSSH, który umożliwia korzystanie z wielu kontenerów połączonych ze sobą na raz. Projekt został zrealizowany na gotowym obrazie systemu ubuntu pobranym ze strony osboxes.org

## Budowa kontenera
Pierwszym krokiem będzie sklonowanie tego repozytorium: https://github.com/Siwykrzywy/openMPI
Do budowy kontenera wykorzystano polecenie docker build. Na obrazie zainstalowany został git, docker, openssh, openMPI oraz wszystko co po drodze okazało się potrzebne do realizacji projektu.

Gotowy obraz został umieszczony w repozytorium: https://hub.docker.com/repository/docker/siwykrzywy/openmpi

## Przygotowanie klastra HPC
1. Pobieramy obraz dockera z repozytorium komendą:
```
sudo docker pull Siwykrzywy/openMPI:last
```
2. Następnie przechodzimy do katalogu "openMPI", który właśne został sklonowany.
Wykonujemy w nim komendę która utworzy klaster n = 5 klientów oraz jeden serewer mpi_head = 1
```
sudo docker-compose scale mpi_node=5 mpi_head=1
```
![image](https://user-images.githubusercontent.com/28909864/107068201-9b411100-67e0-11eb-91ae-bf5c182c2289.png)
![image](https://user-images.githubusercontent.com/28909864/107068486-fe32a800-67e0-11eb-9a6d-d335bf30216d.png)
3. Kolejnym krokiem będzie sprawdzenie czy kontenery poprawnie się uruchomiły, realizujemy to komendą:
```
sudo docker ps -a
```
![image]https://github.com/Siwykrzywy/openMPI/issues/3#issue-802351527
W wyniku czego otrzymujemy następne informacje, dane zawarte w kolumnie CONTAINER ID przedadzą się do uruchomienia
3. Przechodzimy do katalogu /usr

