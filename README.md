# openMPI
## Wprowadzenie
Projekt ten został wykonany w ramach zaliczenia zajęć z przedmiotu PWiR. Zawarto w nim krótką instrukcję jak krok po kroku stworzyć kontener Docker, uruchomić środowisko openMPI oraz zainstalować potrzebne do tego oprogramowanie. Kontener pozwala na obsługę serwera OpenSSH, który umożliwia korzystanie z wielu kontenerów połączonych ze sobą na raz. Projekt został zrealizowany na gotowym obrazie systemu ubuntu pobranym ze strony osboxes.org

## Budowa kontenera
Pierwszym krokiem będzie sklonowanie tego repozytorium: https://github.com/Siwykrzywy/openMPI<br/>
Do budowy kontenera wykorzystano polecenie docker build. Na obrazie zainstalowany został git, docker, openssh, openMPI oraz wszystko co po drodze okazało się potrzebne do realizacji projektu.

Gotowy obraz został umieszczony w repozytorium: https://hub.docker.com/r/siwykrzywy/openmpi

## Opis przygotowania obrazu docker
1. Instalacja dockera przy użyciu następujących poleceń:
```
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io
```
2. Test czy docker działa, przy użyciu obrazu hello world
```
sudo docker run hello-world
```
Bazowo nie mamy tego obrazu na naszej maszynie, zostanie on ściągnięty a następnie uruchomiony, potwierdza to że docker został pomyślnie zainstalowany.<br/>
![image](https://user-images.githubusercontent.com/28909864/107254123-4945f880-6a37-11eb-8ae2-f93b111c2d2d.png)<br/>
3. Możemy teraz sprawdzić listę obrazów dockera komendą:
```
sudo docker image ls
```
![image](https://user-images.githubusercontent.com/28909864/107254369-84e0c280-6a37-11eb-81ca-1fd0a81d8a99.png)<br/>
Obraz hello-world został zapisany w naszym kontenerze i widzimy go na liście obrazów.<br/>
4. Następnie musimy znaleźć repozytorium z obrazem bazowym, możemy znaleźć je używając komendy:
```
sudo docker search baseimage
```
![image](https://user-images.githubusercontent.com/28909864/107266301-9b8e1600-6a45-11eb-9bf9-90ce5a67bbd3.png)<br/>
Najwięcej gwiazdek ma phusion/baseimage, zaciągamy ten obraz przy pomocy komendy:
```
sudo docker pull phusion/baseimage:master
```
Pojawi się on na liście obrazów:<br/>
![image](https://user-images.githubusercontent.com/28909864/107267134-c331ae00-6a46-11eb-83e8-389c7e3dfb47.png)<br/>
5. Tworzymy dockerfile, pozwoli on na zbudowanie obrazu, na podstawie obrazu bazowego.
```
sudo vim Dockerfile
```
Wewnątrz wklejamy zawartość pliku dockerfile, który znajduje się w tym repoztyrium, polecenia w nim zawarte zainstalują potrzebne nam elementy.<br/>
6. Plik Dockerfile umieszczamy w dowolnym folderze który stworzymy na nasz projekt, muszą się tam również znaleźć wszystkie pliki z tego repozytorium poza docker-comopse.yml
7. Następnie w konsoli zmieniamy lokalizację na tenże folder i używamy komendy która zbuduje nasz obraz i wykona wszystkie instrukcje pliku dockerfile:
```
docker build -t mpi .
```
Jeśli operacja się powiedzie powinniśmy otrzymać informację zwrotną o powodzeniu, a obraz powinien się pojawić na liście po wpisaniu komendy:
```
sudo docker image ls
```
![image](https://user-images.githubusercontent.com/28909864/107272145-8f0dbb80-6a4d-11eb-8b67-56717f983685.png)<br/>
8. Następnie będziemy musieli zainstalować docker-compose, pozwala on uruchamiać wiele kontenerów, bez konieczności ręcznego konfigurowania każdego z nich. Zainstalujemy go używając pip, komenda do instalacji pip:
```
sudo apt install python3-pip
```
Komenda do instalacji docker-compose:
```
pip install docker-compose
```
9. Ostatnim krokiem będzie stworzenie pliku docker-compose.yml na wzór tego w repozytorium.
## Przygotowanie klastra HPC
1. Pobieramy obraz dockera z repozytorium komendą, jeśli wykonaliśmy rozdział "Opis przygotowania obrazu docker", to nie musimy go pobierać bo właśnie go stworzyliśmy:
```
sudo docker pull siwykrzywy/openmpi:latest
```
2. Następnie przechodzimy do katalogu "openMPI", który właśne został sklonowany lub stworzony w rozdziale "Opis przygotowania obrazu docker".
Wykonujemy w nim komendę która utworzy klaster n = 5 klientów oraz jeden serwer mpi_head = 1. mpi_head jest hostem i udostępnia serwer SSH dla mpi_node który jest klientem.
```
sudo docker-compose scale mpi_node=5 mpi_head=1
```
![image](https://user-images.githubusercontent.com/28909864/107273790-d39a5680-6a4f-11eb-915f-14539051e5e2.png)
![image](https://user-images.githubusercontent.com/28909864/107273918-05132200-6a50-11eb-9015-39fee31e387b.png)<br/>
3. Kolejnym krokiem będzie sprawdzenie czy kontenery poprawnie się uruchomiły, realizujemy to komendą:
```
sudo docker ps -a
```
W wyniku czego otrzymujemy następne informacje, dane zawarte w kolumnie CONTAINER ID przydadzą się do uruchomienia kontenera<br/>
![image](https://user-images.githubusercontent.com/28909864/107273574-861de980-6a4f-11eb-80a6-021c71073790.png)
## Uruchomienie programu na klastrze HPC
1. Pierwszym krokiem będzie uruchomienie kontenera mpi_head przy użyciu jego ID oraz komendy:
```
sudo docker exec -it 23a1d63b81f9 bin/bash
```
![image](https://user-images.githubusercontent.com/28909864/107069837-c88ebe80-67e2-11eb-92d5-f2682269618c.png)<br/>
Oraz przejść do katalogu:
 ```
cd /usr/bin
```
![image](https://user-images.githubusercontent.com/28909864/107274378-94203a00-6a50-11eb-998d-8470ab65d674.png)<br/>
2. Kolejnym krokiem będzie utworzenie pliku mpi.c przy użyciu komendy
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
5. Wpisać kod testowy, następnie otworzyć konsolę wciskajac ESC i wpisać ":x", aby zapisać zmiany i wyjść
![image](https://user-images.githubusercontent.com/28909864/107070447-a6497080-67e3-11eb-90c4-071a90aa8e4b.png)
6. Teraz należy skompliować plik komendą:
```
mpicc -o mpi mpi.c
```
![image](https://user-images.githubusercontent.com/28909864/107070628-e0b30d80-67e3-11eb-911e-66cd72a37b05.png)<br/>
7. Ostatnim krokiem będzie uruchomienie programu mpi, przy użyciu komendy:
```
mpirun --allow-run-as-root -n 5 mpi

```
![image](https://user-images.githubusercontent.com/28909864/107274846-42c47a80-6a51-11eb-8ed5-849541ec9786.png)<br/>
Operacja powiodła się program mpi.c został uruchomiony przez 5 klientów.

## Bibliografia
- https://docs.docker.com/engine/install/ubuntu/
-https://www.researchgate.net/publication/282310578_Building_a_Virtual_HPC_Cluster_with_Auto_Scaling_by_the_Docker
- https://github.com/oweidner/docker.openmpi
- https://mrpycharm.github.io/openmpi-docker.html
- https://www.tutorialspoint.com/docker/docker_file.html
- https://github.com/docker/compose
