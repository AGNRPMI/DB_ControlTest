## Задание
1. Используя команду cat в терминале операционной системы Linux, создать
два файла Домашние животные (заполнив файл собаками, кошками,
хомяками) и Вьючные животными заполнив файл Лошадьми, верблюдами и
ослы), а затем объединить их. Просмотреть содержимое созданного файла.
Переименовать файл, дав ему новое имя (Друзья человека).

```shell
sudo su
mkdir finaltest
cd final test
nano pets.txt
> вписать данные
nano burden.txt
> вписать данные

cat pets.txt burden.txt > animals.txt
cat animals.txt

mv animals.txt all_animals.txt

```
![картинка](/Linux/img/01.png)
![картинка](/Linux/img/02.png)

2. Создать директорию, переместить файл туда.
```shell
mkdir example
mv all_animals.txt example
cd example
ls
```
![картинка](/Linux/img/03.png)

3. Подключить дополнительный репозиторий MySQL. Установить любой пакет из этого репозитория.
```shell
apt install mysql-server
```
![картинка](/Linux/img/04.png)
![картинка](/Linux/img/05.png)

4. Установить и удалить deb-пакет с помощью dpkg.
```shell
apt download libfuse2
ls
dpkg -i libfuse
apt remove libfuse2
```
![картинка](/Linux/img/06.png)
![картинка](/Linux/img/07.png)
5. Выложить историю команд в терминале ubuntu
```shell
history
```
![картинка](/Linux/img/08.png)
![картинка](/Linux/img/09.png)





