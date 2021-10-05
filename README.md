# devops-netology

### «2.4. Инструменты Git»

#####1. Найдите полный хеш и комментарий коммита, хеш которого начинается на aefea.
     git show aefea
    aefead2207ef7e2aa5dc81a34aedf0cad4c32545
    Update CHANGELOG.md
    
#####2. Какому тегу соответствует коммит 85024d3?
     git show 85024d3 --oneline
    85024d310 (tag: v0.12.23)
    
#####3. Сколько родителей у коммита b8d720? Напишите их хеши.
     git show -s --pretty=%P 85024d3
    либо
     git show 85024d3^@
    Один предок с хешем 4703cb6c1c7a00137142da867588a5752c54fa6a
    
#####4. Перечислите хеши и комментарии всех коммитов которые были сделаны между тегами v0.12.23 и v0.12.24.
     git log --oneline v0.12.23...v0.12.24
    - 33ff1c03b (tag: v0.12.24) v0.12.24
    - b14b74c49 [Website] vmc provider links
    - 3f235065b Update CHANGELOG.md
    - 6ae64e247 registry: Fix panic when server is unreachable
    - 5c619ca1b website: Remove links to the getting started guide's old location
    - 06275647e Update CHANGELOG.md
    - d5f9411f5 command: Fix bug when using terraform login on Windows
    - 4b6d06cc5 Update CHANGELOG.md
    - dd01a3507 Update CHANGELOG.md
    - 225466bc3 Cleanup after v0.12.23 release
    
#####5. Найдите коммит в котором была создана функция func providerSource, ее определение в коде выглядит так func providerSource(...) (вместо троеточего перечислены аргументы).
     git log -S'func providerSource('
    commit 8c928e83589d90a031f811fae52a81be7153e82f
    
#####6. Найдите все коммиты в которых была изменена функция globalPluginDirs.
     git grep --break --heading 'func globalPluginDirs'
    
    подставляем найденный файл в следующую команду
     git log --no-patch --oneline -L :globalPluginDirs:plugins.go
    - 78b122055 Remove config.go and update things using its aliases
    - 52dbf9483 keep .terraform.d/plugins for discovery
    - 41ab0aef7 Add missing OS_ARCH dir to global plugin paths
    - 66ebff90c move some more plugin search path logic to command
    - 8364383c3 Push plugin discovery down into command package
    
 #####7. Кто автор функции synchronizedWriters?
     git log --pretty='format:%C(auto)%h (%an, %ad)' -S'func synchronizedWriters'
    Получаем:
    - bdfea50cc (James Bardin, Mon Nov 30 18:02:04 2020 -0500)
    - 5ac311e2a (Martin Atkins, Wed May 3 16:25:41 2017 -0700)
    Таким образом автор функции Martin Atkins, т.к. он первый её написал.
