# started_with_celery

'''Configuração do celery em um servidor a partir de um projeto já existente que utiliza o Framework WEB Django'''

Breve descrição:
    O celery possibilita executar determinadas operações em um servidor com suas threads separadas da thread principal da aplicação, permitindo que operações ou requisições como as de querys complexas possam ser executadas de forma assíncronas, enquanto as páginas estão sendo carregadas.


Passo a Passo

1- Criar os dois os arquivos celery_work.conf e celery_beat.conf em /etc/supervisor/conf.d/

2- Criar um diretório para os logs, exemplo: /user/project/logs/celery/

3- Editar o conteúdos dos arquivos a seguir (celery_work.conf, celery_beat.conf) de acordo com o diretório do projeto, ambiente virtual e etc:

    

    3.1- Arquivo celery_work.conf:

        ; ==================================
        ;  celery worker supervisor
        ; ==================================

        [program:celery]
        directory=/user/project
        command=/user/project/venv/bin/celery -A app worker -l info

        user=username
        numprocs=1
        stdout_logfile=/user/project/logs/celery/worker-access.log
        stderr_logfile=/user/project/logs/celery/worker-error.log
        autostart=true
        autorestart=true
        redirect_stderr=True
        startsecs=10

        ; Need to wait for currently executing tasks to finish at shutdown.
        ; Increase this if you have very long running tasks.
        stopwaitsecs = 600

        ; Causes supervisor to send the termination signal (SIGTERM) to the whole process group.
        stopasgroup=true

        ; Set Celery priority higher than default (999)
        ; so, if redis is supervised, it will start first.
        priority=1000

    3.2 - Arquivo celery_beat.conf:

        ; ================================
        ;  celery beat supervisor
        ; ================================

        [program:celerybeat]
        directory=/user/project
        command=/user/project/venv/bin/celery -A app beat -l info

        user=user
        numprocs=1
        stdout_logfile=/user/project/logs/celery/beat-access.log
        stderr_logfile=/user/project/logs/celery/beat-error.log
        autostart=true
        autorestart=true
        redirect_stderr=True
        startsecs=10

        ; Causes supervisor to send the termination signal (SIGTERM) to the whole process group.
        stopasgroup=true

        ; if redis is supervised, set its priority higher
        ; so it starts first
        priority=999


4- Rodar os comandos: 
	supervisorctl reread
	supervisorctl update
	supervisorctl start all

5- Para verificar se tudo correu bem, basta executar o comando:
    supervisorctl status

        celery                           RUNNING   pid 18876, uptime 0:01:00
        celerybeat                       RUNNING   pid 18875, uptime 0:01:00
        project                          RUNNING   pid 17626, uptime 3:45:40
        project_demo                     RUNNING   pid 14205, uptime 20 days, 6:32:38

    Isso mostra que os programas celery, celerybeat já estão funcionando.

Esse passo a passo mostra apenas como configurar o celery em um servidor que utiliza Django, então para especificar quais operações devem ser realizadas em "background" consulte:

https://docs.celeryproject.org/en/stable/django/first-steps-with-django.html#using-celery-with-django


