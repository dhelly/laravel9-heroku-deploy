# Fazendo Deploy de aplicações Laravel no Heroku

## Descrição

- O Heroku é uma plataforma de cloud computer de PaaS.  Heroku executa aplicativos por meio de contêineres virtuais conhecidos como Dynos.

- O Laravel é um framework PHP gratuito e de código aberto, utilizado no desenvolvimento de sistemas para web.


## Etapas

### Skeleton Laravel Local

```
composer create-project laravel/laravel laravel-heroku
```

Instalar o StarteKit Breeze

```
composer require laravel/breeze --dev
```

Configurar o Breeze

```
php artisan breeze:install
 
php artisan migrate
npm install
npm run dev
```

### heroku

1) Criar sua conta no Heroku
2) Instalar o Heroku CLI
3) No terminal, executar o comando
   > heroku login
4) Criar a aplicação no Heroku
   > heroku create <nome_da_aplicação>

5) Fazer uma limpeza na aplicação
   - Atualizar o gitignore
     - inserir o `/public/js` e `/public/css`

6) No Heroku, vamos definir o mode de deploy da aplicação. No nosso caso, Github. Só proceder com a conexão da conta ou escolher o repositório.

7) Selecionar o repositório do GITHUB e definir a ***branch*** que irá para o deploy. Selecione o checkbox para aguardar os teste(wait for CI...) e habilite o deploy automático.

8) Abrir a aplicação no terminal
   > heroku open

   > heroku open -a <nome_da_app_heroku>

9) Criação do arquivo `Procfile` que irá servir a nossa aplicação

Essa linha indica que a pasta que a aplicação dever ser servida

```web: vendor/bin/heroku-php-apache2 public/
```

10) Precisamos criar as variáveis de ambiente no Heroku
    - No painel da aplicação vamos para *Settings*.
    - Para auxiliar a inclusão das variáveis necessárias, inserirmos a KEY `APP_DEBUG` como *true* e `APP_KEY` com o valor local.

11) Servir o banco de dados. O Heroku limita o uso para aplicações free, no entanto atende as necessidades de teste de aplicação.
    
    - Aba *Resource* > *Add-ons*. Procuramos o `heroku postres`, pois é free.
    - Copias as Credenciais do Heroku para nossa aplicação via *HEROKU CLI*
    ```
    heroku config:add -a <nome_da_app_heroku> DB_CONNECTION=pgsql DB_HOST=<host> DB_DATABASE=<database> DB_PORT=5432 DB_USERNAME=<user> DB_PASSWORD=<password>
    ```
12) Visualizar os erros é muito importante, o comando `heroku logs --tail -a <nome_da_app_heroku>`

13) Rodar as migrations
    >heroku run php artisan migrate -a laravel-heroku-dhelly

14) Rodar os assets front-end
    - Habilitar em *Settings*>*buildpacks* e adicionamos o *nodejs*. Para que o heroku saiba o que fazer com nossos arquivos javascript
    - Definir os comandos do script que serão executados. Usaremos o `"postinstall": "npm run build"`

15) Rodar os comandos no composer.json

    ```,
        "post-install-cmd": [
            "@php artisan migrate --force",
            "@php artisan storage:link"
        ]
    ```
16) Força o HTTPS. Solução específica para o Heroku.
    - `AppServiceProvider.php`

    ```
        public function boot()
        {
            if(app()->environment('production')) {
                URL::forceScheme('https');
            }
        }
    ```

### Skeleton Laravel Sail

Laravel Sail
```
curl -s "https://laravel.build/laravel-heroku" | bash

./vendor/bin/sail up

php artisan sail:install
```


### Configurações para utilização do startpoint `vite`






## License

The Laravel framework is open-sourced software licensed under the [MIT license](https://opensource.org/licenses/MIT).
