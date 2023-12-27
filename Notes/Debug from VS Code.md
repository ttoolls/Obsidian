Идея дебажиться из VS Code, отображением значений переменных, точками останова и пр. удобствами.

Кроме того, там же можно билдиться, экспортить переменные стреды, запускать QEMU и т.п.

- [ ] Создать конфигурацию на сборку debug_endpoints
	- [x] Добавить export
- [ ] Создать конфигурацию на запуск отладки debug_endpoints
- [ ] Сделать конфигурацию на запуск QEMU и подключение к ней
	- [ ] QEMU уже запущена и к ней нужно только подключиться отладчиком
	- [ ] Загрузить в работающую ВМ сборку приложения и запустить отладку
	- [ ] Написать скрипт запуска QEMU
- [x] Автозапуск тестов в QEMU
- [x] Можно ли прописать скрипты при запуске workspace в VS Code? Нужно запускать SSH клиента и экспортить SYSROOTS


CMake может ставить Debug target и Launch target. Как этим пользоваться? Как обратиться к этим переменным, какая их область видимости?

Может быть полезна вот эта статья [Как запускать задачи в VS Code](https://sdivakarrajesh.medium.com/automating-task-to-run-on-startup-in-vscode-fe30d7f99454)
Почитал статью, сделал задачу. Стартует при запуске VS Code и экспортит переменные среды. Но не получилось с SSH агентом, видимо, его нужно стартовать раньше. Нужно писать скрипт.
##### Задача на запуск:
```
{
	"label": "Prepare project",
	"type": "shell",
	"command": "eval (ssh-agent -c) && ssh-add ~/.ssh/abus && export SYSROOTS=/opt/poky/active/sysroots",
	"group": "none",
	"presentation": {
		"reveal": "always",
		"panel": "new"
	},
	"runOptions": {
		"runOn": "folderOpen",
	}
},
```

Вот еще инструкция как в VS Code запускать дебаггер на основе CMake: [Target Debugging and Launching — CMake Tools 1.4.0 documentation (vector-of-bool.github.io)](https://vector-of-bool.github.io/docs/vscode-cmake-tools/debugging.html)






