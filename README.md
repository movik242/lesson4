# lesson4
При выполнении задания использовал образ centos/7 - v. 2004.01

**1. Определить алгоритм с наилучшим сжатием.**

Развернул ВМ с помощью vagrantfile
Настроил пулы ZFS, установил разные алгоритмы сжатия

```
zpool create otus1 mirror /dev/sdb /dev/sdc
zpool create otus2 mirror /dev/sdd /dev/sde
zpool create otus3 mirror /dev/sdf /dev/sdg
zpool create otus4 mirror /dev/sdh /dev/sdi
zfs set compression=lzjb otus1
zfs set compression=lz4 otus2
zfs set compression=gzip-9 otus3
zfs set compression=zle otus4
zfs get all | grep compression
```
Вывод команды
```
[root@zfs ~]# zfs get all | grep compression
otus1  [01;31m[Kcompression[m[K           lzjb                   local
otus2  [01;31m[Kcompression[m[K           lz4                    local
otus3  [01;31m[Kcompression[m[K           gzip-9                 local
otus4  [01;31m[Kcompression[m[K           zle                    local
```
Скачал один и тот же текстовый файл во все пулы

```
for i in {1..4}; do wget -P /otus$i https://gutenberg.org/cache/epub/2600/pg2600.converter.log; done
ls -l /otus*
zfs list
```
Согласно выводу, наилучшим сжатием является gzip-9

![image](https://github.com/movik242/lesson4/assets/143793993/6de6cdbc-81a7-4ba3-957c-d828343e6807)


**2. Определить настройки pool’a.**

Скачал архив

```
wget -O archive.tar.gz --no-check-certificate 'https://drive.google.com/u/0/uc?id=1KRBNW33QWqbvbVHa3hLJivOAt60yukkg&export=download'
```

Разорхивировал и импортировал в пул

```
tar -xzvf archive.tar.gz zpoolexport/
zpool import -d zpoolexport/
zpool import -d zpoolexport/ otus
```

Проверил результат импорта
```
zpool status
```

![image](https://github.com/movik242/lesson4/assets/143793993/38b86eda-ac0c-4cfa-ba5b-304ff3311631)

Командами zfs определяю настройки

```
zfs get all otus
```

**3. Найти сообщение от преподавателей.**

Скопировал файл из удаленной директории
```

wget -O otus_task2.file --no-check-certificate "https://drive.google.com/file/d/1gH8gCL9y7Nd5Ti3IRmplZPF1XjzxeRAG/view?usp=sharing"
```

Восстановил файловую систему из снапшота
```
zfs receive otus/test@today < otus_task2.file
```

Нашел в каталоге /otus/test файл с именем “secret_message”
```
find /otus/test -name "secret_message"
cat /otus/test/task1/file_mess/secret_message
```

Получил в итоге сслыку https://github.com/sindresorhus/awesome

Также все команды отображены в файле typescript
