## Laboratory work IX

Данная лабораторная работа посвещена изучению процесса создания артефактов на примере **Github Release**

```sh
$ open https://help.github.com/articles/creating-releases/
```

## Tasks

- [x] 1. Создать публичный репозиторий с названием **lab09** на сервисе **GitHub**
- [x] 2. Ознакомиться со ссылками учебного материала
- [x] 3. Получить токен для доступа к репозиториям сервиса **GitHub**
- [x] 4. Выполнить инструкцию учебного материала
- [x] 5. Составить отчет и отправить ссылку личным сообщением в **Slack**

## Tutorial
Создание переменных среды и установка их значений.
```sh
$ export GITHUB_USERNAME=kbrotskiy
$ export PACKAGE_MANAGER=brew
$ export GPG_PACKAGE_NAME=gpg

```
Установка утилиты **xclip**, которая представляет доступ к буферу обмена X из командной строки.
```sh
$ $PACKAGE_MANAGER install xclip
$ alias gsed=sed
$ alias pbcopy='xclip -selection clipboard'
$ alias pbpaste='xclip -selection clipboard -o'

```
Установка приложения командной строки **github-release**, для работы с релизами на **Github**.
```sh
$ cd ${GITHUB_USERNAME}/workspace
$ pushd . # Сохранение текущего каталога в стек
$ source scripts/activate
$ go get github.com/aktau/github-release

```
Настройка git-репозитория **lab09** для работы.
```sh
$ git clone https://github.com/${GITHUB_USERNAME}/lab08 projects/lab09
$ cd projects/lab09
$ git remote remove origin
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab09

```
Замена всех упоминаний `lab08` на `lab09`.
```sh
$ gsed -i "" 's/lab08/lab09/g' README.md
```
Установка и работа с программой **GPG** для шифрования информации и создания электронных цифровых подписей.
```sh
$ $PACKAGE_MANAGER install ${GPG_PACKAGE_NAME}
$ gpg --list-secret-keys --keyid-format
$ gpg --full-generate-key
$ gpg --list-secret-keys --keyid-format LONG
$ gpg -K ${GITHUB_USERNAME}
$ GPG_KEY_ID=$(gpg --list-secret-keys --keyid-format LONG | grep ssb | tail -1 | awk '{print $2}' | awk -F'/' '{print $2}')
$ GPG_SEC_KEY_ID=$(gpg --list-secret-keys --keyid-format LONG | grep sec | tail -1 | awk '{print $2}' | awk -F'/' '{print $2}')
$ gpg --armor --export ${GPG_KEY_ID} | pbcopy
$ pbpaste
-----BEGIN PGP PUBLIC KEY BLOCK-----
...
-----END PGP PUBLIC KEY BLOCK-----
$ open https://github.com/settings/keys
# Добавление секретного ключа к Git
$ git config user.signingkey ${GPG_SEC_KEY_ID}
$ git config gpg.program gpg
```
Создание скрипта для добавления сообщения к тегу
```sh
$ test -r ~/.zsh_profile && echo 'export GPG_TTY=$(tty)' >> ~/.zsh_profile
$ echo 'export GPG_TTY=$(tty)' >> ~/.profile
```
Генерация пакета с расширением `.tar.gz`
```sh
$ cmake -H. -B_build -DCPACK_GENERATOR="TGZ"
$ cmake --build _build --target package
CPack: Create package using TGZ
CPack: Install projects
CPack: - Run preinstall target for: print
CPack: - Install project: print []
CPack: Create package
CPack: - package: /Users/kbrotskiy/workspace/projects/lab09/_build/print-0.1.0.0-Darwin.tar.gz generated.
```
Инициализация проекта на **Travis CI**
```sh
$ travis login --auto --pro
Successfully logged in as kbrotskiy!
$ travis enable --pro
kbrotskiy/lab09: enabled :)
```
Создание тега для коммита
```sh
# Создание тега с сообщением информации
$ git tag -s v0.12.0.0
$ git tag -v v0.1.0.0
$ git show v0.1.0.0
$ git push origin master --tags

```
Создание релиза
```sh
# Проверка версии приложения
$ github-release --version
github-release v0.8.1
# Вывод информации об тегах и релизах в репозитории lab09
$ github-release info -u ${GITHUB_USERNAME} -r lab09

```
Добавление артефакта с указанием ОС и архитектуры, на которых происходила компиляция библиотеки
```sh
$ export PACKAGE_OS=`uname -s` PACKAGE_ARCH=`uname -m`
$ export PACKAGE_FILENAME=print-${PACKAGE_OS}-${PACKAGE_ARCH}.tar.gz
$ github-release upload \
    --user ${GITHUB_USERNAME} \
    --repo lab09 \
    --tag v0.1.0.0 \
    --name "${PACKAGE_FILENAME}" \
    --file _build/*.tar.gz
```

```sh
$ github-release info -u ${GITHUB_USERNAME} -r lab09tags:
# Скачивание артефакта из раздела релизов
$ wget https://github.com/${GITHUB_USERNAME}/lab09/releases/download/v0.1.0.0/${PACKAGE_FILENAME}
# Просмотр содержимого архива
$ tar -ztf ${PACKAGE_FILENAME}
print-0.1.0.0-Darwin/cmake/
print-0.1.0.0-Darwin/cmake/print-config-noconfig.cmake
print-0.1.0.0-Darwin/cmake/print-config.cmake
print-0.1.0.0-Darwin/bin/
print-0.1.0.0-Darwin/bin/demo
print-0.1.0.0-Darwin/include/
print-0.1.0.0-Darwin/include/print.hpp
print-0.1.0.0-Darwin/lib/
print-0.1.0.0-Darwin/lib/libprint.a
```

## Report
Создание отчета по ЛР № 9
```sh
$ popd
$ export LAB_NUMBER=09
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER} tasks/lab${LAB_NUMBER}
$ mkdir reports/lab${LAB_NUMBER}
$ cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md
$ cd reports/lab${LAB_NUMBER}
$ edit REPORT.md
$ gistup -m "lab${LAB_NUMBER}"
```

## Links

- [Create Release](https://help.github.com/articles/creating-releases/)
- [Get GitHub Token](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)
- [Signing Commits](https://help.github.com/articles/signing-commits-with-gpg/)
- [Go Setup](http://www.golangbootcamp.com/book/get_setup)
- [github-release](https://github.com/aktau/github-release)

```
Copyright (c) 2015-2020 The ISC Authors
```
